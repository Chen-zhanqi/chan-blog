---
title: macos升级后git相关命令错误
date: 2022-08-10
tags:
- 记录
- 问题
categories:
- 2022
- 技术
---

{{< admonition warning 场景>}}
macbook升级到macos big sur 11.7.6，git clone、push相关命令突然报错:`kex_exchange_identification: Connection closed by remote host`
{{< /admonition >}}

#### 解决：
1. 查看ssh服务是否启动
`sudo systemsetup -getremotelogin`

启动状态：输出应为“Remote Login: On”

未启用状态，则输出应为“Remote Login: Off”

2. 启动 `sudo systemsetup -setremotelogin on`

3. ~/.ssh/config 文件中加入：
```sh
Host monster
  HostName github.com
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/id_rsa
# 其中Host 后的名字可以随意方便自己记忆，但HostName必须为github.com。
```

#### 相关命令
```shell
#调试，看打印输出的什么，再来解决具体问题
ssh -v git@github.com

#测试，连接就正常了
ssh -T git@github.com

```
