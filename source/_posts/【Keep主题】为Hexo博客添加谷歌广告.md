---
title: 【Keep主题】为Hexo博客添加谷歌广告
date: 2022-01-25 11:32:06
tags:
- 谷歌广告
categories:
- 网站建设
---
本博客使用keep主题，在主题文件夹`themes/keep/layout/layout.ejs`中加入谷歌广告代码即可。

<!-- more -->

<br />

**例如：**

```javascript
<!DOCTYPE html>
<html lang="<%= config.language %>">
<%- partial('_partial/head') %>
<script data-ad-client="ca-pub-7835998003747351" async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<body>
<%- body %>
<%- partial('_partial/scripts') %>
</body>
</html>
```
待谷歌审核通过后，下载`ads.txt`文件，并上传到Hexo的文档源文件夹`source/`下（也就是网站根目录下）。
