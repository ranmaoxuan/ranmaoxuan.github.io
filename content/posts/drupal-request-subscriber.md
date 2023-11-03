---
title: "Drupal中使用路由监听"
description: "介绍一下在Drupal中使用路由监听来实现访问特定的url，或者根据自定义规则进行路由重定向"
date: "2023-11-03T15:34:27+08:00"
thumbnail: ""
summary: "介绍一下在Drupal中使用路由监听来实现访问特定的url，或者根据自定义规则进行路由重定向"
draft: false
categories:
  - "Develop"
tags:
  - "Drupal"
---
## Introduction

介绍一下在Drupal中使用路由监听来实现访问特定的url，或者根据自定义规则进行路由重定向

### Step1: Define module

我们首先创建一个自定义模块，模块的名字叫做 **Test redirect**，同时在模块文件夹里面定义模块的基本信息，写在***test_redirect.info.yml***里面。
```
name: 'Test redirect'
type: module
description: 'This is a test module.'
core_version_requirement: '^8 || ^9'
package: 'Test'
```

### Step2: Define module service config file

第二部我们给模块注册一个服务，定义一个service文件，在模块文件夹里面定义一下服务信息，写在***test_redirect.services.yml***里面。

```
services:
  test.event_subscriber: #服务名称
    class: Drupal\test\EventSubscriber\RedirectSubscriber # 服务所在的命名空间
    arguments: ['@current_user','@file_url_generator'] #服务构造参数的依赖注入
    tags: #服务标识，event_subscriber代表我们这个服务是一个时间订阅服务
      - { name: event_subscriber }
```


### Step3: Create RedirectSubscriber file
在最后一步中，我们根据在模块服务配置文件中创建的内容，来自定义一个重定向的时间订阅类，在类里面去处理我们自己的业务逻辑，大概代码如下，关键部分我会给出解释。

```
<?php


namespace Drupal\test\EventSubscriber;

use Drupal\Core\File\FileUrlGeneratorInterface;
use Drupal\Core\Routing\TrustedRedirectResponse;
use Drupal\node\NodeInterface;
use Symfony\Component\HttpKernel\KernelEvents;
use Symfony\Component\HttpFoundation\RedirectResponse;
use Symfony\Component\EventDispatcher\EventSubscriberInterface;
use Drupal\Core\Session\AccountInterface;
use Symfony\Component\HttpKernel\Event\RequestEvent;

/**
 * News release redirect subscriber.
 */

class RedirectSubscriber implements EventSubscriberInterface {

  /**
   * The current user.
   *
   * @var \Drupal\Core\Session\AccountInterface
   */

  protected $currentUser;

  /**
   * The FileUrlGeneratorInterface service.
   *
   * @var \Drupal\Core\File\FileUrlGeneratorInterface
   */

  protected $fileUrlGenerator;

  /**
   * DeleteAssets constructor.
   *
   * @param \Drupal\Core\Session\AccountInterface $current_user
   *   The current user.
   * @param \Drupal\Core\File\FileUrlGeneratorInterface $file_url_generator
   *   The FileUrlGeneratorInterface service.
   */

  public function __construct(AccountInterface $current_user,FileUrlGeneratorInterface $file_url_generator) {
    $this->currentUser = $current_user;
    $this->fileUrlGenerator = $file_url_generator;
  }

  /**
   * {@inheritdoc}
   */

  public function checkForRedirection(RequestEvent $event) {
    #判断当前访问路由的用户如果不是匿名用户直接返回
    if (!$this->currentUser->isAnonymous()) {
      return;
    }
    $attr = $event->getRequest()->attributes;
    /**
     * @var \Drupal\node\NodeInterface
     */
    $node = $attr->get('node');
    #从当前路由里面获取node，如果有node，并且node的type是basic，并且这个node已经是发布状态才进行下一步的处理逻辑。
    if ($node instanceof NodeInterface && $node->bundle() == 'basic' && $node->isPublished()) {
      #如果这个node有field_pdf这个字段，并且他是一个media entity就会获取这个media对应的file文件地址，并从定向到这个file文件地址去
      if ($node->hasField('field_pdf') && $media = $node->get('field_pdf')->entity) {
        $file = $this->mediaTool->getMediaFile($media);
        $event->setResponse(new RedirectResponse($this->fileUrlGenerator->generateAbsoluteString($file->getFileUri())));
      }
      #如果这个node有field_url 字段，并且field_url字段有值，就会从定向到field_url字段的值上去，这个field_url字段有可能是一个外部的url，比如说是https://www.google.com 所以我们这里需要用Drupal的TrustedRedirectResponse去设置返回的url，如果这个路由不是Drupal内部的路由，用RedirectResponse会抛出一个错误。
      elseif ($node->hasField('field_url') && !$node->get('field_url')->isEmpty()) {
        $target_url = $node->get('field_url')->uri;
        if (!empty($target_url)) {
          $event->setResponse(new TrustedRedirectResponse($target_url));
        }
      }
    }
  }

  #主要是在这里定义我们的时间订阅类型，我们这里注册的时间订阅类型为Request，所以Drupal的请求都会走到我们这里的事件订阅进行处理。***checkForRedirection*** 这个方法代表了我们这个事件订阅具体要处理业务的方法
  /**
   * {@inheritdoc}
   */
  public static function getSubscribedEvents() {
    $events[KernelEvents::REQUEST][] = ['checkForRedirection'];
    return $events;
  }
}
```

### Done

最后通过drush命令清楚drupal缓存 drush cr
然后刷新页面，就能看到神奇的事情发生了！
