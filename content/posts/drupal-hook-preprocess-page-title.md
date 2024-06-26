---
title: 在Drupal中使用Hook修改page title
description: 在Drupal中使用Hook修改page title
summary: "介绍一下如何通过Drupal hook_preprocess_page_title去获取Entity字段模板进行渲染输出"
date: 2024-06-26T14:13:40+08:00
thumbnail: ""
draft: false
categories:
  - Develop
tags:
  - Drupal
---

## 介绍
使用Drupal的同学都知道，页面上的page title是通过Drupal core提供的一个page title block来进行展示的，但是在项目中有些情况需要使用Drupal提供的hook_preprocess_page_title来进行替换

## 背景
在我做的项目中会有一些操作是通过自定义一个Controller,然后根据地址栏的参数解析出来Node ID，再通过\Drupal::entityTypeManager()->getViewBuilder('node')
        ->view($node);进行渲染的，这样一开始是没问题的，直到有一天需要在node
title里面增加一些额外的东西，比如根据一些条件输出一个icon在node title附近，如果用户是直接访问Node detail页面，比如/node/xxx 这种我们可以直接通过覆盖Node title的template来进行修改，但是因为我们上面是已经通过ViewBuilder加载了这个Node，所以在Drupal中没有走完完整的渲染流程，page title模板也只会使用Drupal core默认提供的模板，那这个时候我们应该怎么做呢？
### 解决方式

```

/**
 * Implements hook_preprocess_page_title().
 */
function custom_model_preprocess_page_title(&$variables) {
  $route_name = \Drupal::routeMatch()->getRouteName();
  // Replace the title of the depplink page.
  if ($route_name == 'custom_mode.deeplink') {
    // Get the 'token' parameter from the current route.
    $token = \Drupal::routeMatch()->getParameter('token');
    $nid = \Drupal::service('custom_mode.common_service')->encrypt($token, custom_mode_key, 'D');
    $node = \Drupal::entityTypeManager()->getStorage('node')->load($nid);
    if ($node instanceof NodeInterface) {
      $view_builder = \Drupal::entityTypeManager()->getViewBuilder('node');
      $rendered_title = $view_builder->viewField($node->get('title'));
      $variables['title'] = \Drupal::service('renderer')->renderRoot($rendered_title);
    }
  }
}

```
可以从上面的代码看出来，我们在这个hook中判断了当前的路由是不是自定义Controller生成出来的，并且拿到当前的路由参数Token，对Token进行解密后拿到当前实际需要加载的Node ID，然后通过Node ID加载了整个Node page的View Builder对象，在这个对象里面我们公国viewField这个方法，去加载了当前这个Node的Title 模板，在这个方法里面就会根据我们定义的Node title模板进行加载，最后我们只需要用Drupal提供的Render方法，把加载出来的Node title模板进行渲染，然后替换掉$variables里面的title属性就大功告成了。

```
Tips:
截至到写这个文章的时候，提供另一个解决该问题的思路，通过代码自定义一个Block，在Block中输出一个Template，页面的内容根据自己需要的逻辑进行渲染，在Block layout中放置这个自定义Block的时候Path里面只用包含自己Controller定义出来的路由即可，同时在Drupal core提供的page title block中设置隐藏自定义Controller里面的路由
 ```
