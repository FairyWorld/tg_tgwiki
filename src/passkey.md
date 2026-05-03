---
title: 通行密钥
lang: zh-CN
description: Telegram新增了通行密钥认证作为登录方式，目前正面向全球范围推出。本文介绍了Telegram创建通行密钥的步骤和如何使用通行密钥。
head:
  - - meta
    - name: keywords
      content: Telegram通行密钥,Telegram Passkey,Telegram登录,TG通行密钥,TG Passkey,TG登录,电报通行密钥,电报Passkey,电报登录
---

Telegram 新增了通行密钥认证作为登录方式，目前正面向全球范围推出。通行密钥认证的主要优势在于，用户只需点击一下即可登录账户，无需输入手机号码或查看验证码 (OTP)。

## 创建通行密钥

- 确保你使用的是新版本的应用：Android ≥ `12.2.10` 或 iOS ≥ `12.2.3`
- 前往`设置`->`隐私与安全`->`通行密钥`（Passkeys）。
- 点击“添加通行密钥”并确认创建。
- 你的设备可能会要求你输入锁屏密码或进行生物识别以解锁密钥存储。
- 创建的密钥将出现在列表中。

::: details 操作演示

![android-passkey.jpg](https://cdn.jsdelivr.net/gh/tgwiki/images/android/passkey.jpg)

:::

## 使用通行密钥登录

- 在最新版本的 Android 或 iOS 版 Telegram 应用中，应用会自动提示选择通行密钥进行登录。
- 如果未出现此提示，几秒钟后“你的手机号（Your phone number）”标题下方会出现“使用通行密钥登录（log in with passkey）”链接。点击此链接。
- 点击后会启动你的密码管理器，提示你选择密钥，并在通过面部识别、指纹或锁屏密码验证身份后将所选密钥传送至 Telegram。
- 通行密钥同时发挥手机号和验证码的作用。
- 如果你启用了两步验证，仍需输入在安全性设置中设置的密码。

## 注意事项

1. 通行密钥仅作为替代短信或邮箱登录的选项，无法用于注册。

2. 密钥默认存储在创建它的设备上，但可以通过 Google 密码管理工具、iCloud 密码或其他应用同步到其他设备。

  ::: tip

  你可以在设备上检查密码管理器设置，方法如下：

  Android：打开“设置”，搜索“Google自动填充”。确保在密码管理工具中选择了正确的 Google 账户。

  iOS：打开“设置”，点击顶部的你的姓名。选择`iCloud`->`密码`。确保启用了同步功能。

  :::

3. 支持第三方密码管理器。如果你使用支持通行密钥的密码管理器（例如 Bitwarden、KeePassDX），并在系统自动填充设置中启用它，你可以在那里保存你的 Telegram 通行密钥。

4. 最新版本的官方 Android、iOS 版 Telegram 和 Telegram Desktop（仅限 Windows — 需要Windows Hello）支持通行密钥。

  暂不支持 macOS 和 Linux 版 Telegram Desktop、macOS 版 Telegram (Swift)、Telegram WebK 和 WebA、Telegram X。由于通行密钥的安全要求，非官方应用将不会在此次更新中得到支持。

5. 每个 Telegram 账户最多可以创建 `5` 个通行密钥。




