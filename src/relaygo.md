---
title: 私聊机器人搭建
description: 部分账号可能会有双向限制，无法直接发起私聊，这时候可能就需要一个私聊机器人。本文介绍了Telegram搭建私聊机器人的方法，无需服务器即可直接搭建。
head:
  - - meta
    - name: keywords
      content: Telegram私聊机器人,Telegram双向机器人,Telegram消息转发机器人,Telegram机器人,TG私聊机器人,TG双向机器人,TG消息转发机器人,TG机器人,电报私聊机器人,电报双向机器人,电报消息转发机器人,电报机器人
---

部分账号可能会有双向限制，无法直接发起私聊，这时候可能就需要一个私聊机器人。同时，私聊机器人还可以用作投稿，防止垃圾信息骚扰。

::: tip

提供双向机器人服务的机器人有很多（均不是Telegram官方），如 [@ModularBot](https://t.me/ModularBot) 、 [@LivegramBot](https://t.me/LivegramBot) 、 [@EasyPM_bot](https://t.me/EasyPM_bot) 等。本文以 [@RelayGoBot](https://t.me/RelayGoBot) 为例，其他机器人流程与此相似。

:::

## 项目介绍

RelayGo 是新一代 Telegram 私聊机器人，支持人机验证、联合封禁、自定义欢迎消息和自动回复等多个功能。

项目已在 GitHub 上开源：https://github.com/abcxyz-123456/RelayGo

### 功能特性

- 私聊转发
- 联合封禁
- 人机验证
- 自定义欢迎消息
- 设置自动回复
- 发布广播
- 手动封禁/解封
- Serverless 架构

### 工作原理

RelayGo 基于 Telegram Topics 机制实现「私聊 → 群组话题」的消息桥接：

```text
用户私聊
   ↓
群组 Topic（独立会话）
   ↓
管理员回复
   ↓
自动回传用户
```

每个用户对应一个独立话题，实现类似客服系统的体验。

## 使用方法

### 方法一：集中托管

1. 根据 [教程内容](./createrobot.html) 创建一个机器人，复制机器人 token 。在 `Bot Settings` 中关闭 `Group Privacy` 。
2. 私聊 [@RelayGoBot](https://t.me/RelayGoBot) 发送  `/add` {token} 等待机器人响应。
3. 创建一个群组，在 `群组设置` 中开启 `话题模式`（Topics） 。
4. 将你的机器人添加到群组，并设为管理员，必须授予 `管理话题` 权限。
5. 一般情况下，机器人会自动绑定群组。如果自动绑定没有生效，请在群组中发送 `/bind` 手动绑定
6. 添加完成。机器人会把其他用户的消息转发到群组中，并为每个用户创建独立的话题。同时，机器人会把您在话题中发送的消息转发给对应用户。

::: tip

更多使用教程请前往官方频道 [@RelayGo](https://t.me/RelayGo) 查看。

:::

### 方法二：自部署

RelayGo 采用 Serverless 架构，无需服务器即可部署，零成本且易于上手。

项目 GitHub 仓库给出了 [详细的部署教程](https://github.com/abcxyz-123456/RelayGo/blob/main/DEPLOY.md) ，此处不在赘述。
