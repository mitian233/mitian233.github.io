---
title: Hexo博客配置Google Analytics过程及心得
date: 2021-05-11 23:02:11
tags:
    - 日志
---
实际上，不论Hexo博客使用的主题是否支持，都可以通过向主题的`head.ejs`内添加代码的方式使生成的网站支持Google Analytics（以下简称GA）。

Hexo作为一个无服务器的博客系统（实际上算不上是系统，只是一个静态网站生成器），生成网站页面时是按照指定主题的相应文件内容拼接生成网站，
而这些布局元素（`.ejs`）一般存放在主题文件夹下的layout文件夹内。所以，只要向相应的文件添加指定的内容即可向页面添加相应功能。

这里记录了博主使用Hexo主题Diaspora时对博客使用GA时遇到的问题和解决方案。

## 初次尝试GA

刚刚换新博客主题时，Diaspora在它的`_config.yml`里提供了GA衡量ID（有时称为跟踪ID，取决于你目前使用的GA版本是GA4还是GA3）的填写位， 我将自己的衡量ID填写进去后发现，GA管理后台并不能正常收到访问数据。

博客使用错误配置运行了三个月，直到上手试用了GA发现其强大之处于是决定着手解决这个问题。

进入主题目录的`layout/_partial`内，我发现了主题所自带的`google-analytics.ejs`文件，显然就是GA的页面代码了，打开发现如下内容：

```JavaScript
<!-- Google Analytics-->
<script type="text/javascript">
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','https://www.google-analytics.com/analytics.js','ga');

  ga('create', '<%= theme.google_analytics %>', 'auto');
  ga('send', 'pageview');
</script>
```

运行`hexo g`后生成的页面按下F12可找到如下代码

```JavaScript
<!-- Google Analytics-->
<script type="text/javascript">
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','https://www.google-analytics.com/analytics.js','ga');

  ga('create', '这儿是你在配置文件中填写的衡量ID', 'auto');
  ga('send', 'pageview');
</script>
```

因为谷歌升级了GA4，后期创建的统计媒体资源将统一启用新的`gtag.js`来统计数据，虽然旧版本`analytics.js`从官方文档描述来看貌似依旧可用（[参看文档](https://developers.google.com/analytics/devguides/collection/gtagjs/migration?hl=zh-cn)） ，但未知原因GA后台无法正常统计到数据，那就乘此机会升级到新的页面代码吧。

## 使用gtag.js

将`google-analytics.ejs`内部分内容替换成以下内容：

```JavaScript
<!-- Global site tag (gtag.js) - Google Analytics -->
  <script async src="https://www.googletagmanager.com/gtag/js?id=<%= theme.google_analytics %>"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());

  gtag('config', '<%= theme.google_analytics %>');
</script>
<!-- End Google Analytics -->
```

_注：`<%= theme.google_analytics %>`是Hexo主题的全局变量，当然你也可以将其直接替换成自己的GA衡量ID（不过主题配置文件内的变量将不再可用）。_

生成并部署到GitHub Pages后再次访问静态博客，可以看到GA后台已经能够显示数据了。

## 如果你使用的主题不是Diaspora

1. 如果主题不支持GA，则直接添加`gtag.js`页面代码到`head.ejs`内
2. 如果主题支持GA但GA后台无法收到统计信息，尝试升级旧版页面代码或许有助于解决问题，访问该页面了解[如何从analytics.js迁移到gtag.js](https://developers.google.com/analytics/devguides/collection/gtagjs/migration?hl=zh-cn)

文章未完……