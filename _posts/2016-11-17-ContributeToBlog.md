---
layout: post
title:  "如何在GitPage团队博客发布文章"
date:   2016-11-17 15:13:11 +0800
tags: [team, jekyll]
author: deger

---

团队博客搭建基本完成，deger也试用了一段时间。之前的团队分享都是放在内网gitlab，现在可以开始往外发，简单整理了一个文章发表指引供大家参考。

## 拉取Github代码库

1. 加入Github组织，将账户邮箱发给管理员申请加入
2. 管理在Github组织设置页面邀请后，被邀请人需要到邮箱点击确认邮件
3. 拉取Github库：`git clone https://github.com/FutuCocoa/futucocoa.github.io.git Blog`
4. 在Blog文件夹中运行`bundle exec jekyll serve`

## 撰写文章

`_posts`文件夹已经有样例文章，文件名称格式为`年-月-日-文章标题`，后缀名支持md、markdown、texttile等等。

文章顶部需要对文章做一些说明，格式如下

{% highlight shell %}

---

layout: post 
title:  "开篇序言" 
date:   2016-07-15 17:25:50 +0800
categories: 团队建设
tags: [team]
author: deger

---

{% endhighlight %}

- `layout` 指定使用的模版，`post`即使用_layouts/post.html
- `title` 显示的标题
- `date` 文章撰写的时间，这个时间必须小于当前时间文章才会显示
- `categories` 文章分类, 多个则使用[]，比如[swift, mac]
- `tags` 标签，格式同categories一样
- `author` 文章作者

新增一篇文章，刷新网站就可以看到文章了。

有兴趣可查看 [团队Jekyll技术博客搭建](/2016-07-20-CreateSimpleJekyllSite)

## 提交图片等附件资源

1. 将图片等附件资源放到`assets`文件夹，如图片多的可以创建文件夹，按规则命名`yyyy-mm-dd-**`，方便识别
2. 假设图片名称为`2016-11-17-test.png`, 在文章中引用地址为`/assets/2016-11-17-test.png`


