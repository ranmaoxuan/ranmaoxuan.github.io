---
title: 如何在Drupal view中使用Global token
date: 2023-10-20 14:04:07
tags:
---
记录一下Drupal view中，在Field中插入全局token的方式。

## 开始操作

``` bash
{{ drupal_token('site:name') }}
```

在Customer text中输入上面的代码，即可获取当前站点名称。
