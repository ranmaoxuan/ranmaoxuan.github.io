---
title: 使用轻量级 flatpickr 来进行时间格式化选择
date: 2023-10-24 14:34:47
tags:
---
今天在Drupal项目中使用了flatpickr来进行时间格式化选择，进行一下简单的记录。

#### 首先放一下flatpickr官方链接 [Flatpickr](https://flatpickr.js.org/)

按照官方指引，我们需要在Drupal theme的libraries中定义好flatpickr所需要对应的js & css文件，然后在对应的js文件中使用以下代码

``` javascript
$("#edit-prescription-date").flatpickr({
  enableTime: false, //禁用时分秒选择
  dateFormat: "Y-m-d", //提交到服务器的日期格式
  altFormat: "d/m/Y", //显示在输入框的日期格式
  altInput: true, //是否启用不同的日期格式
});
```

#### 更多参数请查看官方文档 [Config Options](https://flatpickr.js.org/options/)
