---
title: "Drupal override controller function"
description: ""
summary: "今天遇到一个需要覆盖Drupal中一个第三方模块自带的Controller中的function，这里简单介绍一下如何实现的。"
date: "2023-11-14T16:05:21+08:00"
thumbnail: ""
draft: false
categories:
  - "Develop"
tags:
  - "Drupal"
---

## Introduction
今天遇到一个需要覆盖Drupal中一个第三方模块自带的Controller中的function，这里简单介绍一下如何实现的。



## Solution

首先我们需要创建一个自定义模块，如果已经有存在的自定义模块，则我们需要在 example_module.services.yml 中定义一个service

**example_module.services.yml**
```
services:
  example_module.route_subscriber:
    class: Drupal\example_module\Routing\RouteSubscriber
    tags:
      - { name: event_subscriber }
```

service定义好之后，我们需要定义service对应的类文件

**RouteSubscriber.php**
```
<?php
/**
 * @file
 * Contains \Drupal\example_module\Routing\RouteSubscriber.
 */

// THIS FILE BELONGS AT /example_module/src/Routing/RouteSubscriber.php

namespace Drupal\example_module\Routing;

use Drupal\Core\Routing\RouteSubscriberBase;
use Symfony\Component\Routing\RouteCollection;

/**
 * Listens to the dynamic route events.
 */
class RouteSubscriber extends RouteSubscriberBase {

  /**
   * {@inheritdoc}
   */
  public function alterRoutes(RouteCollection $collection) {
    // Replace "some.route.name" below with the actual route you want to override.
    if ($route = $collection->get('some.route.name')) {
      $route->setDefaults(array(
        '_controller' => '\Drupal\example_module\Controller\ExampleModuleController::content',
      ));
    }
  }
}
```
最后在定义自己的Controller，实现对应的方法就好啦

**ExampleModuleController.php**

```
<?php
/**
 * @file
 * Contains \Drupal\example_module\Controller\ExampleModuleController.
 */

// THIS FILE BELONGS AT /example_module/src/Controller/ExampleModuleController.php

namespace Drupal\example_module\Controller;

use Drupal\Core\Controller\ControllerBase;

/**
 * An example controller.
 */
class ExampleModuleController extends ControllerBase {

  /**
   * {@inheritdoc}
   */
  public function content() {
    $build = array(
      '#type' => 'markup',
      '#markup' => t('Hello World!'),
    );
    return $build;
  }
}
```

**注意**
在上面的Controller继承中，我们可以继承需要覆盖的Controller，这样可以用到他的一些内部方法，同时也能够使用到继承Controller中的一些构造属性，以及通过继承覆盖的Controller可以更清晰的标识这个Controller是用来进行覆盖也有Controller中的方法的。
