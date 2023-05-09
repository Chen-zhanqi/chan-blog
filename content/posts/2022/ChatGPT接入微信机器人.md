---
title: ChatGPT接入微信机器人
date: 2022-06-20
tags:
- chatgpt
- ai
categories:
- 2022
- 技术
---

#### 网页版体验
![](https://qn.chenzqi.cn//FhdDalbJaMcrkuwGGZmRAQgXAvUX)

#### 微信小程序体验
如果没有编程基础，可以直接WX扫码使用封装好ChatGPT的小程序体验

![AI猫](https://qn.chenzqi.cn/chenshen/aicat_4694.jpeg)

### 1. OpenAI账号注册

[OpenAI注册地址](https://beta.openai.com/signup)需要会翻墙，虚拟手机号接码。创建完账号则前往 [API管理页面](https://beta.openai.com/account/api-keys)创建一个 API Key 并保存下来，后面需要在项目中配置这个key

### 2.运动环境

支持Linux、MacOS、Windows系统（可在Linux服务器上长期运行），同时需要安装Python。

##### 1.克隆项目代码：

```bash
git clone https://github.com/Chen-zhanqi/chatgpt-on-wechat.git
cd chatgpt-on-wechat/
```

#### 2.安装所需核心依赖：

```bash
pip3 install itchat-uos==1.5.0.dev0
pip3 install --upgrade openai
```
> 注：`itchat-uos`使用指定版本1.5.0.dev0，`openai`使用最新版本，需高于0.25.0。
>
### 3. 配置

配置文件的模板在根目录的`config-template.json`中，需复制该模板创建最终生效的 `config.json` 文件：

```bash
cp config-template.json config.json
```

然后在`config.json`中填入配置，以下是对默认配置的说明，可根据需要进行自定义修改：

```bash
# config.json文件内容示例
{ 
  "open_ai_api_key": "YOUR API KEY",                          # 填入上面创建的 OpenAI API KEY
  "single_chat_prefix": ["bot", "@bot"],                      # 私聊时文本需要包含该前缀才能触发机器人回复
  "single_chat_reply_prefix": "[bot] ",                       # 私聊时自动回复的前缀，用于区分真人
  "group_chat_prefix": ["@bot"],                              # 群聊时包含该前缀则会触发机器人回复
  "group_name_white_list": ["ChatGPT测试群", "ChatGPT测试群2"], # 开启自动回复的群名称列表
  "image_create_prefix": ["画", "看", "找"],                   # 开启图片回复的前缀
  "conversation_max_tokens": 1000,                            # 支持上下文记忆的最多字符数
  "character_desc": "你是ChatGPT, 一个由OpenAI训练的大型语言模型, 你旨在回答并解决人们的任何问题，并且可以使用多种语言与人交流。"  # 人格描述
}
```
**配置说明：**

**1.个人聊天**

+ 个人聊天中，需要以 "bot"或"@bot" 为开头的内容触发机器人，对应配置项 `single_chat_prefix` (如果不需要以前缀触发可以填写  `"single_chat_prefix": [""]`)
+ 机器人回复的内容会以 "[bot] " 作为前缀， 以区分真人，对应的配置项为 `single_chat_reply_prefix` (如果不需要前缀可以填写 `"single_chat_reply_prefix": ""`)

**2.群组聊天**

+ 群组聊天中，群名称需配置在 `group_name_white_list ` 中才能开启群聊自动回复。如果想对所有群聊生效，可以直接填写 `"group_name_white_list": ["ALL_GROUP"]`
+ 默认只要被人 @ 就会触发机器人自动回复；另外群聊天中只要检测到以 "@bot" 开头的内容，同样会自动回复（方便自己触发），这对应配置项 `group_chat_prefix`
+ 可选配置: `group_name_keyword_white_list`配置项支持模糊匹配群名称，`group_chat_keyword`配置项则支持模糊匹配群消息内容，用法与上述两个配置项相同。

**3.其他配置**

+ 对于图像生成，在满足个人或群组触发条件外，还需要额外的关键词前缀来触发，对应配置 `image_create_prefix `
+ 关于OpenAI对话及图片接口的参数配置（内容自由度、回复字数限制、图片大小等），可以参考 [对话接口](https://beta.openai.com/docs/api-reference/completions) 和 [图像接口](https://beta.openai.com/docs/api-reference/completions)  文档直接在`bot/openai/open_ai_bot.py` 中进行调整。
+ `conversation_max_tokens`：表示能够记忆的上下文最大字数（一问一答为一组对话，如果累积的对话字数超出限制，就会优先移除最早的一组对话）
+ `character_desc` 配置中保存着你对机器人说的一段话，他会记住这段话并作为他的设定，你可以为他定制任何人格

### 4.运行

1.**本地运行**，直接在项目根目录下执行：

```bash
python3 app.py
```
终端输出二维码后，使用微信进行扫码，当输出 "Start auto replying" 时表示自动回复程序已经成功运行了（注意：**用于登录的微信需要在支付处已完成实名认证**）。扫码登录后你的账号就成为机器人了，可以在微信手机端通过配置的关键词触发自动回复 (任意好友发送消息给你，或是自己发消息给好友)，下图表示登录成功。
![](https://qn.chenzqi.cn//FrKNGMhIiur336AKH0KpnJyL-nxI)


### 更新
**2023.3.15**

本地正常运行，国内上个月开始无法访问openai域名了，所以需要开代理，不然会有 ~~我无法访问你的网络~~ 等错误提示。

微信图：
![](https://qn.chenzqi.cn//FhvywxSCTl4rw5MY6bIhYRDUyVeW)
后端运行：
![](https://qn.chenzqi.cn//FioXzfCktpGrMxzBYyZNCcfS9tUm)
