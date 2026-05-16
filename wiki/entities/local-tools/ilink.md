---
title: iLink (WeixinBot)
type: entity
created: 2026-05-16
updated: 2026-05-16
sources:
  - https://github.com/zhuweiyou/weixinbot
tags:
  - local-tools
  - WeChat
  - bot
  - iLink
---

# iLink (WeixinBot)

基于微信 iLink Bot API 的最小 Node.js 示例。

## Features

- **Terminal QR Code Output**: Displays login QR code in terminal
- **Token Persistence**: Saves `bot_token` to local `.weixin-token.json` after scan
- **Long Polling**: Receives WeChat messages via long polling
- **Auto-reply**: Responds to `hello` with `world`
- **Image Response**: Responds to text `image` or image messages with a local image

## Tech Stack

- Node.js 22+

## Installation

```bash
npm install
```

## Usage

1. Prepare a reply image (`reply.jpg` or `reply.png`)
2. Run the bot
3. Scan the QR code in terminal
4. Send `hello` to receive `world`

## Note

这是一个最小示例项目，用于展示微信 iLink Bot API 的基本用法。
