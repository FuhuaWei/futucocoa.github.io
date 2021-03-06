---
layout: post
title:  "团队Jekyll技术博客搭建"
date:   2016-07-20 17:25:50 +0800
tags: [macOS, shell]
author: deger

---

## 下载安装Jekyll

具体详情可查看[Jekyll官方文档](https://jekyllrb.com/docs/home/)


{% highlight shell %}
~ $ gem install jekyll #安装
~ $ jekyll new myblog #创建一个站点到文件夹`myblog`
~ $ cd myblog #进到文件夹
~/myblog $ jekyll serve #启动服务
 => Now browse to http://localhost:4000 #在浏览器可以打开站点
{% endhighlight %}

## 发布到Github Page
1. 到Github创建一个仓库`FutuCocoa`.github.io, `FutuCocoa`替换成自己的用户名或已创建的组织名
2. 将Jekyll生成的文件夹push到这个仓库即可

## Jekyll目录介绍

{% highlight shell %}
.
├── _config.yml #一些站点配置
├── _drafts #草稿存放处 需要显示草稿：jekyll server --drafts
|   ├── begin-with-the-crazy-ideas.textile #草稿无需日期前缀
|   └── on-simplicity-in-technology.markdown
├── _includes #通用的网页块
|   ├── footer.html #网页顶部
|   └── header.html #网页顶部
├── _layouts #网页模版，每一篇文章都会指定一个模版
|   ├── default.html
|   └── post.html 
├── _posts #文章存放处，文章需要日期前缀
|   ├── 2007-10-29-why-every-programmer-should-play-nethack.textile
|   └── 2009-04-26-barcamp-boston-4-roundup.textile
├── _data #自定义数据存放处
|   └── members.yml
├── _site #jekyll生成的的静态网页
├── .jekyll-metadata
└── index.html #主页
{% endhighlight %}


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
- `tags` 标签
- `author` 文章作者

新增一篇文章，刷新网站就可以看到文章了。

## 增加Google统计

到[Google Analytics](https://analytics.google.com/analytics/)注册一个账号，获取跟踪ID，类似UA-867248**-*。在_config.yml增加行即可

```
google_analytics: UA-867248**-*
```

## 使用新皮肤

使用[Beautiful Jekyll](https://github.com/daattali/beautiful-jekyll)，将代码clone下来，除_posts, _drafts外，全部覆盖。

## TODO
- 增加作者介绍页面
- 按分类展示文章列表
- 按标签展示文章列表
- 按作者展示文章列表


- [Adding authors to your Jekyll site](https://blog.sorryapp.com/blogging-with-jekyll/2014/02/06/adding-authors-to-your-jekyll-site.html)
- [Author information in jekyll blog](http://blog.bigbinary.com/2015/01/09/author-information-in-jekyll-blog.html)
- [Use Tags and Categories in your Jekyll based Github Pages without plugins](https://codinfox.github.io/dev/2015/03/06/use-tags-and-categories-in-your-jekyll-based-github-pages/
)