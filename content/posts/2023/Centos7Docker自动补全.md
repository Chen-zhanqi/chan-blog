---
title: Centos7Docker自动补全
date: 2023-04-11
slug: Centos7DockerAutocomplete
tags:
- Linux
categories:
- 2023
- 技术
---



### TAB键自动补全

```shell
#安装bash-completion
yum install -y bash-completion

#刷新文件
source /usr/share/bash-completion/completions/docker
source /usr/share/bash-completion/bash_completion
```





