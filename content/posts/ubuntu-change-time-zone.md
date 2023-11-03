---
title: "如何在Ubuntu上面修改时区"
description: ""
summary: "今天给大家介绍一下如果修改Ubuntu的时区"
date: "2023-11-03T16:36:29+08:00"
thumbnail: ""
draft: false
categories:
  - "System"
tags:
  - "Linux"
---

1、首先在shell终端输入命令 timedatectl 查看当前系统的时区

```
ubuntu@127.0.0.1:~$ timedatectl
     Local time: Mon 2022-08-08 06:08:01 UTC
           Universal time: Mon 2022-08-08 06:08:01 UTC
                 RTC time: Mon 2022-08-08 06:08:01
                Time zone: Etc/UTC (UTC, +0000)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

可以看到我们当前系统的时区是 ETC/UTC,然后我们现在需要把时区修改为 Asia/Shanghai，那应该怎么做，继续往下看吧！


2、执行修改时区命令：
```
 sudo timedatectl set-timezone "Asia/Shanghai"
```
修改完成后再次查看系统时区：

```
ubuntu@127.0.0.1:~$ timedatectl
               Local time: Mon 2022-08-08 14:10:04 CST
           Universal time: Mon 2022-08-08 06:10:04 UTC
                 RTC time: Mon 2022-08-08 06:10:04
                Time zone: Asia/Shanghai (CST, +0800)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```
发现我们的系统时间已经变成了 Asia/Shanghai,至此修改时区成功！

