---
title: 在Drupal项目开发阶段中需要初始化的一些东西
description: ""
date: 2023-12-11T07:28:59.139Z
summary: "今天记录一下在Drupal项目开发阶段中需要初始化的一些东西，以免忘记以及帮助其他人知道这个事情"
preview: ""
draft: false
tags:
  - Drupal
categories:
  - Develop
keywords:
  - Drupal
---
***禁用缓存***
>在Drupal项目中大家最头疼的应该就是各种缓存了，改个代码需要清理缓存，改个模板内容需要清理缓存，总之默认就是改啥都要清理缓存

那我们再开发阶段中怎么避免这个很频繁的清理缓存操作，让我们尽量少花一点时间在这个上面呢，其实官方已经给出了答案，那就是在开发阶段的时候我们可以通过配置来禁用掉缓存，

[Disable caching](https://www.drupal.org/docs/develop/development-tools/disable-caching)

目前Drupal10 已经可以很方便的在CMS后台进行设置禁用掉缓存，对于Drupal10以下的项目我们可以通过在配置文件中进行配置的方式来禁用缓存

development.services.yml
```
# Local development services.
#
# To activate this feature, follow the instructions at the top of the
# 'example.settings.local.php' file, which sits next to this file.
parameters:
  http.response.debug_cacheability_headers: true
  twig.config:
    debug: true
    auto_reload: true
    cache: false
services:
  cache.backend.null:
    class: Drupal\Core\Cache\NullBackendFactory
```

Open settings.local.php and make sure development.services.yml is enabled.

settings.local.php
```
$settings['container_yamls'][] = DRUPAL_ROOT . '/sites/development.services.yml';
```

Change the following to be TRUE if you want to work with enabled css- and js-aggregation:

settings.local.php
```
$config['system.performance']['css']['preprocess'] = FALSE;
$config['system.performance']['js']['preprocess'] = FALSE;
```

Uncomment these lines to disable the Render Cache, disable the Internal Page Cache and disable Dynamic Page Cache:

settings.local.php
```
$settings['cache']['bins']['render'] = 'cache.backend.null';
$settings['cache']['bins']['page'] = 'cache.backend.null';
$settings['cache']['bins']['dynamic_page_cache'] = 'cache.backend.null';
```

进行以上的设置后，在Drush中再清理一下缓存，然后你就会惊喜的发现后面的修改代码以及模板都不用再去执行清理缓存的操作，当然如果你要是改了一些定义的文件发现不生效的话还是再去清理一遍，比如改了*module_name.info.yml* 还有 *theme_name.info.yml* 类似于这种基础的配置文件还是需要去清理缓存
