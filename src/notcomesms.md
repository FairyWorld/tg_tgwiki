---
title: 未收到验证码
description: Telegram注册/登录时没有收到验证码？本文给出了解决Telegram没有收到验证码的解决方法，希望对您有所帮助。
head:
  - - meta
    - name: keywords
      content: Telegram未收到验证码,Telegram短信验证码,Telegram验证码,Telegram注册,TG未收到验证码,TG短信验证码,TG验证码,TG注册,电报未收到验证码,电报短信验证码,电报验证码,电报注册
---

### 如果你是注册

1. 请检查您使用的是否是官方客户端，Telegram现在不支持使用第三方客户端注册（第三方客户端不给发短信验证码）。
2. `+86`短信验证码的署名可能并非Telegram（不建议使用`+86`注册），而是Telegram使用的短信平台的名称。
3. 请检查验证短信是否被手机识别为骚扰短信。

::: tip

您可以尝试使用 [Telegram X](https://play.google.com/store/apps/details?id=org.thunderdog.challegram) 获取验证码。经测试，Telegram X的短息验证码获取率更高，速度更快！

如果仍无法收到验证码，则可以考虑购买境外号码或使用 [接码平台](https://sms-activate.org/?ref=3073106) 注册。

:::

### 如果你是登录

1. Telegram会将验证码优先发送至已登录的客户端，请检查您的消息。
2. 如果你的账号开启了邮箱登录，你可以使用邮箱获取验证码登录。
3. 您也可以点击`Get Code via SMS`请求使用短信验证码的方式登录（仅限官方客户端）。

### 注册/登录时显示需要支付短信费用？

Telegram为短信价格较贵的地区推出了SMS Fee，即登录时需要支付费用开通一周会员才可以发送验证码。然而，经测试，此机制还与设备环境等多重因素有关。

**解决办法**：

- 您可以通过切换ip，美国、日本、新加坡ip，关闭定位加全局尝试。
- 如果你想要支付SMS Fee，请注意：Google Play绑定大陆银行卡，调用Google Play付款，必须是香港ip。如果有visa、万事达或者虚拟卡，不必强制要求香港ip。

### 已有旧设备在线，但新设备登录时仍然发送短信？

该情况的具体触发原因尚不清楚，目前推测与Telegram的风控机制有关。~~（也可能是杜叔叔缺钱了）~~

**解决方法：**

- 使用第三方客户端绕过（通常第三方客户端默认发送验证码到已登录设备）；

- 尝试将Telegram回退到之前的版本，您可以在登录后重新把应用更新到最新版本

  ::: warning

  回退版本需要卸载新版本并重新安装Telegram，该操作会导致现有的应用数据被清空！请谨慎操作！

  :::

  ::: details 附：几个Telegram之前的版本号及下载地址

  | 版本号  | 下载地址                                                     |
  | ------- | ------------------------------------------------------------ |
  | 11.13.1 | [apkmirror](https://www.apkmirror.com/apk/telegram-fz-llc/telegram/telegram-11-13-1-release/) |
  | 11.3.1  | [apkmirror](https://www.apkmirror.com/apk/telegram-fz-llc/telegram/telegram-11-3-1-release/) |
  | 10.12.0 | [apkmirror](https://www.apkmirror.com/apk/telegram-fz-llc/telegram/telegram-10-12-0-release/) |

  :::

