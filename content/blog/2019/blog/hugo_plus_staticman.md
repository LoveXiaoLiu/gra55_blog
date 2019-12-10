+++
title = "Hugo 配置 staticman 静态评论平台"
description = "staticman 使用的坎坷之路"
author = "gra55"
categories = ["Blog"]
tags = ["blog", "2019"]
date = "2019-12-09"
featured = "pic01.jpg"
featuredalt = "Mt. Fuji"
featuredpath = "date"
linktitle = ""
type = "post"

+++

## 0x00 起因

我使用的 [hugo 主题](https://themes.gohugo.io/) [hugo-future-imperfect](https://github.com/jpescador/hugo-future-imperfect)，默认支持 [disqus](http://www.disqus.com/) 和 [staticman](https://staticman.net/) 的评论方式。

奈何有一些不可描述的原因，[disqus](http://www.disqus.com/) 无法访问，遂转投 [staticman](https://staticman.net/) 怀抱，这才开启了踩坑之路。

## 0x01 太年轻

准备按照 [staticman](https://staticman.net/) 的 Getting started 一步一步执行。

可谓是出师不利，第一步就过不去：无法把 staticmanapp 账户添加到 Collaborators 中。
{{< img-post path="date" file="collaborator_add_error.jpg" alt="collaborator_add_error" type="center" >}}

随后访问 https://api.staticman.net/v2/connect/LoveXiaoLiu/blog-comment 也是各种报错。
{{< img-post path="date" file="access_staticman_error.jpg" alt="access_staticman_error" type="center" >}}

于是去 [staticman](https://staticman.net/) 的 issue 区找找，果然很多人遇到了这个问题。

出现这个问题的原因是由于使用 [staticman](https://staticman.net/) 的人太多了，导致部署的公共 staticman app 达到了 Github 的限制（Github 限制每个用户每小时只能调用 API 5K 次）。

+ 为什么会达到这个限制呢？
  + [staticman](https://staticman.net/) 的工作方式是这样的，你将 [staticman](https://staticman.net/) 添加到你的博客系统以后，需要将 staticmanapp 账户添加到你的 repo Collaborators 中。当有人在你的博客中提交评论，[staticman](https://staticman.net/) 会将这些评论内容通过公开的 staticman app，以提交代码的方式提交到你的 repo 中。问题就出在这里，提交代码的账户就是 staticmanapp，随着 [staticman](https://staticman.net/) 的用户越来越多，staticmanapp 肯定会超过这个限制的。
  + 目前这个账户已经被注销。
  + 针对这个问题，维护人员咨询了 GitHub 官方，官方回复让他创建一个 [GitHub App](https://developer.github.com/apps/)
  + 参考 [issues 243](https://github.com/eduardoboucas/staticman/issues/243)

## 0x02 自己动手

本来想自己动手搭建一个 staticman app（[参考这个教程](https://github.com/eduardoboucas/staticman/issues/243#issuecomment-447296229)）

奈何已经有轮子了，何必自己造一个呢？

是的，已经有人写好了 [GitHub App for staticman](https://github.com/eduardoboucas/staticman/issues/243#issuecomment-453754860)，所以可以直接拿来用：

+ 去这里  https://github.com/apps/staticman-net 给自己的 GitHub 账号安装此 APP
+ 将评论的 post 请求地址换成 https://dev.staticman.net/v3/entry/github/[USERNAME]/[REPOSITORY]/[BRANCH] 即可
+ 需要增加评论审核功能的话，在 staticman.yml 文件将 moderation 设置为 true 
