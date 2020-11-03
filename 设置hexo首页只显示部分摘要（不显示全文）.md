## 设置hexo首页只显示部分摘要（不显示全文）

本文针对Next主题，不确保对于其它主题有效（但从修改模式来看，是有效的）

Next默认是会显示全文的，这样显然很不方便，因此需要一些方法去只显示前面一部分。

## 修改配置

首先需要在Next主题的_config.yml中把设置打开：(默认安装时就打开了)

```
# Automatically excerpt description in homepage as preamble text.
excerpt_description: true
```

## 方法一：写概述

在文章的`front-matter`中添加`description`，其中description中的内容就会被显示在首页上，其余一律不显示。

```
---
title: 让首页显示部分内容
date: 2020-02-23 22:55:10
description: 这是显示在首页的概述，正文内容均会被隐藏。
---
```

## 方法二：文章截断

在需要截断的地方加入：

```
<!--more-->
```

