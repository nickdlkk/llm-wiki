---
title: 都是AI Coding，为什么Java体验差了一个量级
type: summary
created: 2026-05-25
updated: 2026-05-25
sources:
  - https://mp.weixin.qq.com/s/3-hQ4vHYErfpIzYPC6wJwg
tags:
  - harness-engineering
  - java
  - microservice
  - local-harness
  - context-engineering
---

# 都是AI Coding，为什么Java体验差了一个量级

## 一句话总结

Java 微服务项目本地跑不起来导致 AI 每步都要等人。解决方案：**依赖倒置 + Spring Profile 零侵入隔离 + CLI 工具化**，把云端分布式依赖替换为本地替身，让 AI 能在本地自主跑"写代码→测试→修复"闭环。

## 问题的本质

```
轻量项目（前端/CLI/Python）：
  编辑 → 本地运行 → 测试验证 → AI读取 → 自动修复 → 再验证 → ...
  AI 可以自主迭代几十轮，人不需要盯着

Java 微服务：
  本地 mvn spring-boot:run → 失败
  推预发(5min) → 人工触发调用 → 发现NPE → 截图反馈AI
  → AI改两行 → 再推预发(5min) → 再等 ...
  每轮都要人，30分钟修一个参数注入顺序问题
```

**根因**：微服务架构把运行时依赖全部推到云端——HSF 服务调用、TDDL 数据库路由、Diamond/Switch 配置中心、MetaQ 消息队列、OSS 对象存储、远程沙箱。本地跑不起来，AI 就无法自主验证。

## 三条改造原则

### 1. 依赖倒置，接口先行

上层依赖抽象接口，不依赖具体实现。云端和本地只是同一接口的两套实现。

```
改造前：
  FilesystemService → OssStorageAdapter（直接调 OSS SDK）
  AgentWorkspace → SandboxCommandExecutor（调远程沙箱 API）
  上层直接绑定了云端实现，本地跑不了

改造后：
  StorageAdapter (接口)
  ├── OssStorageAdapter (线上)
  └── LocalStorageAdapter (本地，java.nio.file)
  
  CommandExecutor (接口)
  ├── SandboxCommandExecutor (线上)
  └── LocalCommandExecutor (本地，ProcessBuilder + bash -c)
```

切换运行环境只是换一个接口实现，上层代码完全不用改。

### 2. 零侵入，Profile 隔离

**严格零侵入**——本地改造不能让线上代码路径多走一行额外代码。

```java
// @Profile("!local") 守卫，编译期两套配置互不可见
@Configuration
@Profile("local")
public class LocalRepositoryConfig {
    @Bean
    CommandExecutor localCommandExecutor() { ... }
    
    @Bean("localFsBasePath")
    String localFsBasePath() { return "/tmp/agentfs"; }
    
    @Bean("sessionSequence")
    Sequence sessionSequence() {
        return new LocalSequence();  // AtomicLong 替代 TDDL GroupSequence
    }
}

// 可选依赖用 @Nullable，Spring 注入 null 而非报错
public AgentRunner(
    @Nullable @Qualifier("localFsBasePath") String localFsBasePath, ...) {
    // localFsBasePath != null → 走本地
    // ossClient != null → 走线上
}
```

**ComponentScan 正则过滤**：把线上专属的包（远程沙箱、OpenTelemetry 观测）整个排除，不用每个类都加条件注解。

侵入性分级：

| 侵入级别 | 改动类型 | 示例 |
|---------|---------|------|
| 零侵入 | 新建文件 | 新接口、新的本地实现类 |
| 极低 | 加一行声明 | 线上实现类加 `implements 接口` |
| 低 | 类型上提 | 字段类型从实现类改为接口 |
| 中等 | 新增可选参数 | 构造器加可选参数（原有构造器不动） |

**检验标准**：删掉所有本地相关代码后，线上行为完全不变。

### 3. 工具 AI 化：CLI 优先

**AI Agent 的能力边界 = 它能调用的工具的边界**。GUI 对 AI 不可见，CLI 才是 AI 能用的东西。

```bash
# 用 CLI 桥接企业内部系统
mw diamond get --unit online --data-id application.properties
mw hsf address --unit daily --app my-application

# 脚本化配置同步（替代人工登录管理台）
scripts/fetch-switch-config.sh
  → 调 mw diamond 从预发拉 JSON 配置
  → 解析写入 switch-config-local.properties
  → AI 可以自己运行脚本同步最新配置
```

工具 AI 化优先级：

| 优先级 | 形态 | AI 可用性 | 示例 |
|-------|------|---------|------|
| 1 | CLI | 直接可用 | mw-cli, mvn, git, arthas |
| 2 | MCP Server | 协议适配 | 数据库查询、监控数据 |
| 3 | Skill/Tool | 自定义封装 | 配置查询、服务诊断 |
| 4 | GUI | 不可用 | Web 管理台、IDE 插件 |

## 改造效果

| 对比项 | 改造前 | 改造后 |
|-------|-------|-------|
| 文件操作验证 | 推预发，OSS 控制台查看 | 本地 `ls` 直接看 |
| Bash 执行验证 | 推预发，登录沙箱查看 | 本地 Terminal 直接看 |
| AI 自主验证 | 做不到 | ReadFile → 验证 WriteFile 结果 |
| 单次迭代耗时 | 5-10 分钟（含部署等待） | 秒级 |
| AI 自主修复轮数 | 0（每轮都要人工介入） | 平均 3-5 轮后自行收敛 |

完整 bug fix 流程：改造前 30 分钟 + 3-4 轮人工推预发；改造后 AI 本地自主迭代，通常 2 分钟内收敛，人只需最后 review。

## 本地改造全景

| 线上依赖 | 本地替代 | 改动方式 |
|---------|---------|---------|
| OSS 对象存储 | 本地文件系统 (java.nio.file) | 新增接口 + 本地实现 |
| 远程沙箱 | 本机 bash (ProcessBuilder) | 新增本地实现 |
| TDDL + MySQL | H2 文件数据库 (MODE=MySQL) | application-local.properties |
| TDDL GroupSequence | AtomicLong 本地序号 | 新增本地实现 |
| Switch Center | switch-config-local.properties | 脚本从预发拉取 |
| Diamond 配置中心 | application-local.properties | Spring Profile |
| EagleEye/HSF/Sunfire | 排除自动配置 | spring.autoconfigure.exclude |
| Pandora 启动器 | 标准 java -cp 启动 | start-local.sh |
| OpenTelemetry | 排除观测包 | ComponentScan excludeFilters |

## CLAUDE.md 示例

```markdown
# 项目简介
AI Agent 运行时平台，支持 ReadFile/WriteFile/Bash 等 Tool。

# 本地开发
- 启动：`mvn spring-boot:run -Dspring.profiles.active=local`
- 测试：`mvn test`
- 本地文件系统根目录：`/tmp/agentfs/`

# 架构约束
- 上层模块只依赖接口，不依赖具体实现类
- 本地专属代码通过 @Profile("local") 隔离
- 新增本地适配不得修改线上代码路径
```

## 验证脚本：让 AI 自己检查

```bash
#!/bin/bash
# AI 可以直接跑这个来确认本地环境是否正常
set -e

echo "=== 1. 编译检查 ==="
mvn compile -q -Dspring.profiles.active=local

echo "=== 2. 单元测试 ==="
mvn test -q 2>&1 | tail -5

echo "=== 3. 本地启动检查 ==="
timeout 30 mvn spring-boot:run -Dspring.profiles.active=local &
PID=$!
sleep 15
if curl -s http://localhost:8080/actuator/health | grep -q "UP"; then
    echo "✓ 本地启动成功"
else
    echo "✗ 本地启动失败"
    exit 1
fi
kill $PID 2>/dev/null

echo "=== 4. 文件系统闭环检查 ==="
# 验证 ReadFile/WriteFile 和 Bash 看到的是同一套文件
...
echo "=== 全部通过 ==="
```

## 五条可复用方法论

1. **找到最小可运行子集**：不需要搬所有云端能力，只需核心链路（请求→LLM→Tool→返回）所需的 DB + 文件系统 + 命令执行三个基础设施
2. **替代而非模拟**：H2 是真实关系型数据库，LocalCommandExecutor 是真实的 bash 执行——替身要能真实运行，不只是返回 mock 数据
3. **脚本化一切人工操作**：凡是需要人登录管理台/复制配置/点击按钮的操作，都应有对应脚本
4. **分层隔离，逐层验证**：编译→启动→接口调通→E2E 测试，每层有对应验证手段
5. **让 AI 成为改造的参与者**：先手动搞定 H2 和接口抽象让项目能启动，之后 AI 在本地闭环里自己迭代 StorageAdapter、LocalCommandExecutor、冒烟测试脚本

## 后续方向

- JVM 诊断工具化：`jstack` 输出到 AI 可读的地方，或通过 Skill 封装 Arthas 的 `watch`/`trace`/`tt`
- 结构化异常输出：JSON 格式异常让 AI 直接解析定位到代码行
- 目标：让 AI 在本地完全自主跑完"发现问题→定位原因→修复代码→验证修复"闭环
