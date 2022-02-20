---
title: 在Hexo博客中插入哔哩哔哩视频
date: 2022-02-17 12:32:06
tags:
- 哔哩哔哩
- Hexo
categories:
- 网站建设
- 	Hexo
---

博客的图片可以使用图床，例如SM.MS，CloudFlare等。

> SM.MS的付费图床速度还是杠杠的，使用了日本和 Premium Global CDN。

那么博客中的视频应该放哪儿呢，答案是哔哩哔哩和YouTube。它们都是理想的视频床。考虑到防火墙原因，推荐使用哔哩哔哩。<br />由于markdown中是可以直接插入html代码的，因此可以直接复制哔哩哔哩视频的html代码粘贴到博客中。<br />

![image.png](https://vip1.loli.io/2022/02/20/Y3RFeGBT6MZhjDu.png)

<br />但默认参数无法实现视频长宽自适应。<br />因此，需要修改参数。<br />以下是屏幕自适应所需要设置的参数，自己跟着改就行：

```html
<div style="position: relative; width: 100%; height: 0; padding-bottom: 75%;">
    <iframe src="//player.bilibili.com/player.html?aid=82937960&bvid=BV1KJ411p7WN&cid=141890422&page=1"  scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true" style="position: absolute; width: 100%; height: 100%; left: 0; top: 0;"></iframe>
</div>
```
**具体效果如下：**   

<div style="position: relative; width: 100%; height: 0; padding-bottom: 75%;">
    <iframe src="//player.bilibili.com/player.html?aid=82937960&bvid=BV1KJ411p7WN&cid=141890422&page=1"  scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true" style="position: absolute; width: 100%; height: 100%; left: 0; top: 0;"></iframe>
</div>


**参考文献：**

1. [Hexo中插入Bilibili视频。](https://liuzhihang.com/2019/09/14/hexo-inserts-bilibili-video.html)

