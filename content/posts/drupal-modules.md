---
title: "Drupal 模块记录"
description: ""
summary: "针对工作中用到过的模块做如下总结，后续会根据使用过其他模块随时更新"
date: "2023-11-03T16:39:27+08:00"
thumbnail: ""
draft: false
categories:
  - "Develop"
tags:
  - "Drupal"
---

***前言***
> 针对工作中用到过的模块做如下总结，后续会根据使用过其他模块随时更新


----------


- **admin_toolbar**
> 增强管理员菜单功能，提供下拉快捷选项，以及快捷清除缓存等操作。
- **block_class**
> 提供在block layout里面针对block单独设置class，方便前端样式编写
- **block_content_permissions**
> 为每种block type设置权限，可以分别控制某个block type的创建，更新，删除等权限
- **ckeditor_templates**
> 提供ckeditor里面使用固定模板功能，ckeditor里面通用的小组建可以使用这个module
- **ckeditor_templates_ui**
> 提供UI界面，在线编辑ckeditor template的能力，方便创建模板
- **editor_file**
> 如果站点不需要启用media，同时想在ckeditor里面上传文件（PDF,XLS)等，可以安装这个模块，可以限制上传文件大小，以及文件类型，和文件目录等
- **entity_embed**
> 提供在ckeditor里面直接插入block的操作，通过也支持在ckeditor里面插入node，在需要node中间内容插入block | view block的时候很方便
- **image_link_formatter**
> 提供字段类型是Image的时候，可以设置Image的跳转链接，这样不用腹泻字段模板，方便直接设置链接
- **menu_breadcrumb**
> 提供通过menu层级直接设置面包屑，同时在特殊页面面包屑title不一致的时候可以创建多个menu来进行优先级覆盖
- **menu_item_extras**
> 增强Drupal自带的menu能力，可以在menu里面管理字段
- **menu_link_attributes**
> 增强Drupal自带menu的能力，可以单独设置menu item的属性，属性可以自定义，默认支持设置打开方式，class等
- **metatag**
> SEO模块，支持根据content type来设置默认的meta tag，同时可以针对每个node单独设置meta tag
- **page_specific_class**
> 这个模块支持根据页面的路径，单独在body上面增加class，不需要写自定义代码来实现，支持url 通配，以及正则规则
- **paragraph_view_mode**
> 支持在关联paragraph的时候，管理员使用不同的view model，主要是用于字段相同，但是页面效果不一样来使用
- **tb_megamenu**
> 使用这个模块可以在站点创建灵活的mega menu，支持多种配置方式，以及block插入等
- **path_redirect_import**
> 这个模块主要用重定向规则的导入导出等，方便批量导入规则
