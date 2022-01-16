---
title: "Firebase 集成 Slack 并实现自动化监控"
date: 2022-01-16T18:45:37+08:00
draft: false
tags: [自动化,Firebase,Slack]
categories: 开发平台
---

### 为什么要集成 Slack？

Firebase 可将一些崩溃信息同步到 Slack 频道，而 Slack Api 又支持对频道的信息进行监控，在这前提下可以搞点事情了，实现企业微信沟通群里实时留意应用的崩溃信息，同时也可将崩溃信息存入本地数据库，第二天利用爬虫脚本对昨天崩溃信息进行二次追踪，留意崩溃数量是否上升。

![image](/images/2022/01/firebase_slack_01.png)

以下就开始一步一步实现。

### 创建 App

https://api.slack.com/apps

![image](/images/2022/01/firebase_slack_02.png)

![image](/images/2022/01/firebase_slack_03.png)

### Incoming Webhooks

![image](/images/2022/01/firebase_slack_04.png)

#### Add New Webhook to Worksapce

创建 Webhook 添加到已创建的频道

![image](/images/2022/01/firebase_slack_05.png)

添加完之后回到刚页面你会看到 Webhook URL，将其复制。

![image](/images/2022/01/firebase_slack_06.png)

### 关联 Firebase

安装 Slack

![image](/images/2022/01/firebase_slack_07.png)

将上面复制的 Webhook URL 填写进去及对应频道

![image](/images/2022/01/firebase_slack_08.png)

再对应用进行配置

![image](/images/2022/01/firebase_slack_09.png)

发送测试消息，到频道查看是否收到消息。

**至此已完成了 Slack 频道创建及关联到 Firebase，下面开启脚本监控之路**

### 使用脚本前配置

下图中左边栏红色框住的几个菜单下面一步一步带大家进行相关配置。

![image](/images/2022/01/firebase_slack_10.png)

#### OAuth & Permissions

1、滚动到 **Bot Token Scopes**，点击 **Add an OAuth Scope**，添加 **chat:write**；

![image](/images/2022/01/firebase_slack_11.png)

2、滚动回到顶部 OAuth & Permissions，看到 **OAuth Tokens for Your Workspace**，点击 **Install App to Workspace**，允许授权之后会生成 **Bot User OAuth Access Token**，复制记录等下脚本需要用到；

![image](/images/2022/01/firebase_slack_12.png)

#### Socket Mode

1、开启 Socket Mode，Token 名称 自己随便起一个，生成之后将获得 **App Level Token**，复制记录等下脚本需要用到；

![image](/images/2022/01/firebase_slack_13.png)

![image](/images/2022/01/firebase_slack_14.png)

#### Event Subscriptions

1、开启 Events；

![image](/images/2022/01/firebase_slack_15.png)

2、滚动底部展开 **Subscribe to bot events**，点击 **Add Bot User Event**，添加下图几个事件；

![image](/images/2022/01/firebase_slack_16.png)

3、然后点击保存。

### Token 信息保存到环境变量中

为了安全将脚本需要用到的 Token 信息保存到环境变量当中，若不考虑安全性可直接脚本使用，以下示例适用于 Mac 系统；

1、编写环境变量文件；
```shell
sudo vi ~/.bash_profile
```

2、添加需要的 Token 信息；
```shell
export SLACK_SIGNING_SECRET=9666xxxxxxxx
# Bot User OAuth Token
export SLACK_BOT_TOKEN=xoxb-xxxxx-xxxx
# App Level Token
export SLACK_APP_TOKEN=xapp-1-xxxx-xxxxxx
```
3、保存退出，并执行生效。

```
source ~/.bash_profile
```

### 编写脚本

上面一切到准备好了之后，可以开始编写脚本啦！

```javascript
const { App } = require('@slack/bolt');

const app = new App({
    token: process.env.SLACK_BOT_TOKEN,
    signingSecret: process.env.SLACK_SIGNING_SECRET,
    socketMode: true, // add this
    appToken: process.env.SLACK_APP_TOKEN, // add this
    port: process.env.PORT || 3000
});

// 监听接收到的信息为 hello 时，并自动回复 Hey there $userName
app.message('hello', async ({ message, say }) => {
    // say() sends a message to the channel where the event was triggered
    await say(`Hey there <@${message.user}>!`);
});

// 监听渠道接收到的信息
app.event('message', async ({ event }) => {
    // fired when `app_home_opened` event is received
    // do work
    console.log(`channel: ${event.channel} and message: ${event.text}`);
});

(async () => {
    // Start the app
    await app.start();

    console.log('⚡️ Bolt app is running!');
})();

```

### 测试

1、运行脚本；

2、将我们上面创建的 App 添加到测试频道上；

![image](/images/2022/01/firebase_slack_17.png)

3、发送 hello 信息

![image](/images/2022/01/firebase_slack_18.png)

![image](/images/2022/01/firebase_slack_19.png)

### 结束

至此完成了 Firebase 平台集成 Slack，应用相关的崩溃信息会同步到 Slack 频道上，同时利用 Slack Api，实现了对频道接收到的信息监控，该操作可将崩溃信息存入数据库又或者同步到企业微信群，后续也可进行崩溃追踪。

### 文档

1. [将 Slack 集成添加到您的项目中](https://support.google.com/firebase/answer/9005934?hl=zh-Hans)
2. [连接工具和自动执行任务](https://slack.com/intl/zh-cn/help/articles/115005265063-%E9%80%82%E7%94%A8%E4%BA%8E-Slack-%E7%9A%84-Incoming-Webhook)
3. [打开激活 Incoming Webhook](https://www.slack.com/services/new/incoming-webhook)
4. [Sending messages using Incoming Webhooks](https://api.slack.com/messaging/webhooks)
5. [Node Bolt 脚本使用指南](https://slack.dev/bolt-js/tutorial/getting-started)