---
title: "在Drupal中如何清除Node重定向缓存"
description: ""
summary: "首先我们来看一段代码，这这段代码里面返回了一个Response，但是这里并没有做任何缓存相关处理的东西"
date: "2023-11-03T16:32:47+08:00"
thumbnail: ""
draft: false
categories:
  - "Develop"
tags:
  - "Drupal"
---
## Introduction
今天遇到了一个很神奇的事情，在模块中处理Node重定向的时候，一直使用的是原始数据，如果Node更新后，重定向还是老的数据，记录一下避免下次踩坑。

## Before
首先我们来看一段代码，这这段代码里面返回了一个Response，但是这里并没有做任何缓存相关处理的东西
```
$event->setResponse(new RedirectResponse($this->fileUrlGenerator->generateAbsoluteString($file->getFileUri())));
```
现在会发生一个在Node中改了字段的值，但是你会发现访问这个node url的时候还是上一次重定向的url，并不是最新的Url，原因就是我们在这段代码里面并没有处理任何Response缓存相关的逻辑，所以我们修改一下这段代码再来试试看！改造如下：

### Solution

```
    $attr = $event->getRequest()->attributes;
    /**
     * @var \Drupal\node\NodeInterface
     */
    $node = $attr->get('node');
    if ($node instanceof NodeInterface && $node->bundle() == 'news_release' && $node->isPublished()) {
      if ($node->hasField('field_pdf') && $media = $node->get('field_pdf')->entity) {
        $file = $this->mediaTool->getMediaFile($media);
        $response = new TrustedRedirectResponse($this->fileUrlGenerator->generateAbsoluteString($file->getFileUri()));
        $response->getCacheableMetadata()->addCacheTags(["node:{$node->id()}"]);
        $event->setResponse($response);
      }
      elseif ($node->hasField('field_url') && !$node->get('field_url')->isEmpty()) {
        $target_url = $node->get('field_url')->uri;
        if (!empty($target_url)) {
          $response = new TrustedRedirectResponse($target_url);
          $response->getCacheableMetadata()->addCacheTags(["node:{$node->id()}"]);
          $event->setResponse($response);
        }
      }
    }
```

在这段代码里面我们给Response对象增加了一个缓存标签，这样在重新保存Node的时候，drupal就会默认刷新Node的缓存，我们这里用的缓存标签和Node默认的缓存标签使用一致的话，drupal也就会默认清楚这个缓存，所以下次修改Node的字段值后，我们再次访问Node url，就会发现跳转的url是最新修改的值啦！

```
 $response->getCacheableMetadata()->addCacheTags(["node:{$node->id()}"]);
 ```
