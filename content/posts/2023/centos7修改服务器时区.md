---
title: centos7修改服务器时区
date: 2023-03-01
slug: centos7_Changes_the_server_time_zone
tags:
- linux
categories:
- 2023
- 技术
---


如果服务器上没有 `/etc/timezone` 文件，您可以尝试以下方法来更改时区为上海：

1. 使用 root 或具有管理员权限的用户登录到服务器上。

2. 运行以下命令以查看当前时区设置：

   ```shell
   timedatectl
   ```

   在输出中，找到 "Time zone" 行，该行显示了当前的时区设置。

3. 运行以下命令以列出所有可用的时区：

   ```shell
   timedatectl list-timezones
   ```

   这将显示一个包含所有可用时区的列表。

4. 找到上海时区的名称，通常为 "Asia/Shanghai"。

5. 运行以下命令以将服务器的时区更改为上海：

   ```shell
   timedatectl set-timezone Asia/Shanghai
   ```

   这将更改服务器的时区设置为上海。

6. 最后，您可以再次运行以下命令来验证时区是否已成功更改：

   ```shell
   timedatectl
   ```

   在输出中，确保 "Time zone" 行显示了新的上海时区设置。

这些命令可能需要管理员权限才能执行。