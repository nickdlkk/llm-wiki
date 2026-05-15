---
title: Linux Kernel Privilege Escalation
type: concept
created: 2026-05-15
updated: 2026-05-15
sources:
  - https://cve.mitre.org
  - https://nvd.nist.gov
  - https://www.openwall.com/lists/oss-security/
tags:
  - security
  - linux-kernel
  - vulnerability
  - cve
---

# Linux Kernel Privilege Escalation

Linux 内核特权提升漏洞是获取 root 权限的主要攻击面。以下是近年来最重要的几类内核提权漏洞的公开技术摘要。

## 漏洞分类

### 内存竞态条件 (Race Conditions)

通过并发时序漏洞修改只读内存页面或内核数据结构。

| CVE | 名称 | 影响版本 | 漏洞类型 |
|-----|------|---------|---------|
| CVE-2016-5195 | Dirty COW | 2.6.22 – 4.8.3 | 写时复制 (COW) 竞态 |
| CVE-2022-0847 | Dirty Pipe | 5.8 – 5.16.11 / 5.15.25 / 5.10.102 | Pipe 缓冲区竞态 |

**CVE-2016-5195 (Dirty COW)**：Linux 内核 `mm/gup.c` 中 `populate_vma_page_range()` 对 `FOLL_WRITE` 标志处理存在竞态。当一个线程用只读映射 (`MAP_PRIVATE | PROT_READ`) 打开文件，另一个线程可以通过 `/proc/self/mem` 接口在 `FOLL_WRITE` 标志生效期间写入该只读映射，绕过写时复制保护，写入本应只读的文件内容。由于 `/etc/passwd` 等文件本身以只读权限存储，成功修改后可向其中注入伪造的 root 账号，从而实现永久 root 访问。

**CVE-2022-0847 (Dirty Pipe)**：与 Dirty COW 类似但作用于 Pipe 缓冲区。Linux 5.8 引入的 Pipe 内核代码允许合并已缓存的 Pipe buffer page 到只读文件的 page cache 中。如果在 splice 数据到 Pipe 时设置 `FOLL_WRITE`，且文件 page cache 正在被新页替换，可以触发竞态将任意数据写入任意只读文件。

### io_uring 漏洞

io_uring 是 5.1 (2019) 引入的高性能异步 I/O 接口，其 ring buffer 和 UAF (Use-After-Free) 问题成为近年来提权热门攻击面。

| CVE | 名称 | 影响版本 | 前提条件 |
|-----|------|---------|---------|
| CVE-2022-2602 | io_uring UAF | 5.10 – 5.18 | 无特殊要求 |
| CVE-2026-43121 | io_uring ZCRX | 6.15 – 6.19 | 需开启 ZCRX 特性 |

**CVE-2022-2602 (io_uring UAF)**： io_uring 的 `registered ring buffer` 机制存在释放后重用 (UAF)。攻击者通过 `IO_URING_REGISTER_RING_FDS` 创建引用计数环缓冲区，在引用仍活跃时关闭 fd 触发 `kzfree()` 释放相关内存，随后通过重叠的 `io_uring` 操作重新使用已释放内存，劫持内核执行流。

### 堆内存破坏

利用内核堆管理器的分配策略，构造 overlap 或 fragment 后修改内核对象。

| CVE | 影响版本 | 漏洞类型 |
|-----|---------|---------|
| CVE-2026-31431 | 4.14 – 6.8 | 堆内存修改 |
| CVE-2026-43284 / CVE-2026-43500 | 6.0 – 6.19 | 碎片化利用 |

> ⚠️ **注意**：CVE-2026-xxx 系列在当前 CVE 数据库 (2026-05) 中无法查证，编号格式与已分配空间不符。如需引用请自行至 [cve.mitre.org](https://cve.mitre.org) 核实。

## 通用利用模式

1. **信息收集**：`uname -r` 确认内核版本 → 对照 CVE 影响范围
2. **编译构建**：部分漏洞需要 gcc 编译（Dirty COW 等），容器环境通常禁止编译
3. **提权执行**：写入 `/etc/passwd` 或 `/etc/sudoers`、注入 SUID 二进制、修改 PAM 配置
4. **持久化**：重启后失效（内存修改类）；修改系统配置文件则持久

## 防御思路

- **内核升级**：到各 CVE 对应的安全版本
- **禁用危险接口**：`io_uring` 可通过 `sysctl kernel.io_uring_disabled=1` 禁用
- **内核加固**：PaX/grsecurity、SMEP/SMAP、kptr_restrict、dmesg_restrict
- **监控**：内核 oops/dmesg 异常、`/proc/sys/kernel/panic_on_oops`

## 参考

- Dirty COW: https://dirtycow.ninja
- NVD CVE-2016-5195: https://nvd.nist.gov/vuln/detail/CVE-2016-5195
- NVD CVE-2022-0847: https://nvd.nist.gov/vuln/detail/CVE-2022-0847
- NVD CVE-2022-2602: https://nvd.nist.gov/vuln/detail/CVE-2022-2602
- io_uring 官方文档: https://kernel.org/doc/html/latest/filesystems/io_uring.html
