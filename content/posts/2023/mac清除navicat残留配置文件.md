---
title: mac清除navicat残留配置文件
date: 2023-02-18
slug: macDeletes-residual-navicat-configuration-file
tags:
- Docker
categories:
- 2023
- 技术
---


```shell
sudo rm -Rf /Applications/Navicat\ Premium.app
sudo rm -Rf /private/var/db/BootCaches/CB6F12B3-2C14-461E-B5A7-A8621B7FF130/app.com.prect.NavicatPremium.playlist
sudo rm -Rf ~/Library/Caches/com.apple.helpd/SDMHelpData/Other/English/HelpSDMIndexFile/com.prect.NavicatPremium.help
sudo rm -Rf ~/Library/Caches/com.apple.helpd/SDMHelpData/Other/zh_CN/HelpSDMIndexFile/com.prect.NavicatPremium.help
sudo rm -Rf ~/Library/Preferences/com.prect.NavicatPremium.plist 
sudo rm -Rf ~/Library/Application\ Support/CrashReporter/Navicat\ Premium_54EDA2E9-528D-5778-A528-BBF9A4CE8BDC.plist
sudo rm -Rf ~/Library/Application\ Support/PremiumSoft\ CyberTech
```