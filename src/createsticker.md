---
title: 创建贴纸
description: 贴纸类似于微信中的表情包，Telegram支持多种多样的贴纸。本文介绍了Telegram如何创建贴纸（包括静态贴纸、动态贴纸），以及贴纸的大小限制和要求。
head:
  - - meta
    - name: keywords
      content: Telegram创建贴纸,Telegram贴纸,Telegram贴纸包,如何创建Telegram贴纸,TG创建贴纸,TG贴纸,TG贴纸包,如何创建TG贴纸,电报创建贴纸,电报贴纸,电报贴纸包,如何创建电报贴纸
---

贴纸类似于微信中的表情包，Telegram支持多种多样的贴纸。

**Telegram的贴纸管理机器人是 [@Stickers](https://t.me/Stickers) 。**

## 创建静态贴纸包

### 方法一

1. 发送 `/newpack` 命令，等待机器人回应后发送你想要的贴纸名称，创建一个贴纸集合的名字（支持中文）。

2. 上传图片

   图片格式要求：`512px`*`512px`（一边达到512px即可），支持png和webp格式，最大支持`512px`

   可使用Ps（Photoshop）修改格式，如果图片过多，可以使用ps的批处理工具，先录制动作，再进行批处理一次解决全部图片的适配问题，事半功倍。

   ::: details 附：批处理教程

   1. 将想要修改的图片全部放在一个本地文件夹里面。
   2. 使用Ps打开图片。
   3. 点击`窗口`-`动作`，创建新的动作，设置动作名称。
   4. `图选`->`图选大小`，设置 `512px` `512px`，点击确定。如果图片内存过大，可以在调整图像大小的时候降低分辨率，控制在`512kb`以下。
   5. 点击`文件`->`自动`->`批处理`，动作选择你刚刚录制的动作  源 选择文件夹 选择你存放图片的文件夹。点击确定，开始进行批量处理。等待全部处理过程，结束。
   
   :::
   
3. 发送图片给机器人，未压缩图片。 然后发送emoji表情，将表情和图片进行关联。

   重复进行操作，直至上传完成所有的。 发送 `/publish` 命令结束上传。

   ::: details 操作演示

   ![android-createsticker1.jpg](https://cdn.jsdelivr.net/gh/tgwiki/images/android/createsticker1.jpg)

   ![android-createsticker2.jpg](https://cdn.jsdelivr.net/gh/tgwiki/images/android/createsticker2.jpg)

   :::

4. 为贴图集设置图标。Telegram应用程序会将其显示在贴纸面板的贴纸列表中。

   要设置图标，请发送带有透明层的PNG或WEBP格式的`100`x`100`方形图像。

   您可以跳过此步骤，应用将使用包的第一个贴纸作为其图标。跳过发送 `/skip`

5. 为你的贴纸创建分享地址。设置成功后你可以通过此地址将贴纸分享给别人。

::: tip

此机器人还可以在后续对贴纸进行修改，可以添加，修改，删除，排序，还可以查看贴纸的每日使用人数，命令都有详细说明，可以认真探索以下。

:::

### 方法二

1. 点击输入框左侧的 `Open` 按钮打开小程序。
2. 点击 `Create Sticker Pack` 创建新的贴纸包。
3. 输入贴纸包名称并上传贴纸（贴纸要求同上）。
4. 为每一张贴纸选择对应的emoji表情（支持多个）。
5. 点 `Save` 保存，全部完成后，点击 `Publish` 设置分享地址并发布贴纸包。

::: details 操作演示

![android-createsticker3.jpg](https://cdn.jsdelivr.net/gh/tgwiki/images/android/createsticker3.jpg)

:::

## 创建动态贴纸包

动态贴纸的创作比较复杂，您可以通过命令 `/newvideo` ，或使用上面“方法二”中的小程序，创建动态视频贴纸

- 对于贴纸，一面的大小必须正好是 512 像素——另一面可以是 512 像素或更小。
- 对于表情符号，视频的大小**必须正好是`100`x`100`像素**
- 视频时长不得超过 `3`秒。
- 帧速率最高可达 `30`FPS。
- 视频应循环播放以获得最佳用户体验。
- 视频大小不应超过`256`KB。
- 视频必须为使用 **VP9 编解码器**编码的WEBM格式。
- 视频必须没有音频流。

目前通过在线网站可以将gif转成webm格式，如果gif是透明背景，那么转换城的webm格式的表情也是透明背景，在线网站地址：[GIF转WEBM](https://cdkm.com/cn/gif-to-webm)

在转换的时候设置转换项->`视频尺寸`->`自定义`，设置成`512px`*`512px`，进行转换完成后，下载下来。

上传过程同上描述。先创建动态贴纸名称，上传，对应emoji表情，上传完成设置贴纸地址即可分享使用。

## 即时创建贴纸

Telegram支持在聊天时即时创建贴纸，具体操作如下：

1. 随意打开一个对话，点击贴纸图标
2. 点击 `创建` 
3. 上传图片，支持裁剪、抠图、添加轮廓等操作
4. 完成后点击右下角按钮选择对应的emoji表情并保存，支持：直接发送、添加到收藏，或者添加到已有的贴纸包

::: details 操作演示

![android-createsticker4.jpg](https://cdn.jsdelivr.net/gh/tgwiki/images/android/createsticker4.jpg)

:::

## 将已有贴纸创建为贴纸包

以下第三方Telegram机器人支持将已有的贴纸添加到一个新的贴纸包，您可以通过此类机器人快速创建个人贴纸合集：

- [@fStikBot](https://t.me/fStikBot)
- [@KyStkrBot](https://t.me/KyStkrBot)

