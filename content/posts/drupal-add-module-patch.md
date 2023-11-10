---
title: "Drupal给模块生成补丁"
description: "在这篇文章中，我将向您展示如何在Drupal模块中修复问题，并提交到Drupal社区。"
summary: "今天给大家介绍一下如何给Drupal模块生成补丁，并提交到Drupal社区中。"
date: "2023-11-10T09:40:42+08:00"
thumbnail: ""
draft: false
categories:
  - "Develop"
tags:
  - "Drupal"
---
## Introduction
今天给大家介绍一下如何给Drupal模块生成补丁，并提交到Drupal社区中。

### Solution
首先我们需要在Drupal社区模块中[创建一个issue](https://www.drupal.org/node/add/project-issue)，

![Create issue](/images/20231110/2023-11-10_09-44.png)

issue创建完成后，会生成一个纯数字，并且唯一的issue number，这个时候我们要找到对应模块的git仓库，然后去clone这个仓库，模块对应的git仓库可以在模块介绍首页找到

![Module source code](/images/20231110/2023-11-10_09-52.png)

从这里进入后，找到对应模块仓库的clone地址，然后在本地电脑上拉下来这个模块对应的代码，并且根据你当前Drupal项目中使用到对应模块的版本，去创建一个本地分支，分支命名规则如下
```
git checkout -b [issue-number]-[short-description] # e.g. 123456-some-bug
```
在本地分支创建完成后，就可以在这个分支上面进行你要对这个模块的修改，代码修改完成后需要执行对应的git命令，大概步骤如下
```
git add new.filename #Add change file.
git commit -m "my new feature" #Commit my change
git diff origin/BASE_VERSION #对比你的修改和当前分支版本的变动
git diff origin/BASE_VERSION > PATCH_FILE_NAME.patch #对比你的修改和当前分支版本的变动，并生成补丁文件
```
到这里我们的补丁文件就生成成功了，这个时候回到你的issue链接里面去，然后上传这个补丁文件，社区里面就能够看到你做的贡献啦！也欢迎大家多多给社区做贡献哟，这样Drupal才能变得更好


***注意***
上面代码中的*BASE_VERSION*代表的是该模块在你项目中对应的版本，如果BASE_VERSION不正确的话会导致补丁无法打上，因为BASE_VERSION不正确的话会导致代码差异较多，和你实际修改的代码不一致

---
**Reference**
- [Making a patch
](https://www.drupal.org/docs/develop/git/using-git-to-contribute-to-drupal/working-with-patches/making-a-patch )

