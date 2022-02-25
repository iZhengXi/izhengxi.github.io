---
title: 在Hexo博客中插入YouTube视频和哔哩哔哩视频
date: 2022-02-25 21:32:06
tags:
- Hexo
- 博客
- YouTube
- 哔哩哔哩
categories:
- 网站建设
-   Hexo
---


在Hexo博客中可以插入哔哩哔哩视频和YouTube视频。并且相比哔哩哔哩视频，插入的YouTube视频可以调节清晰度。
在这里我们以插入YouTube博主[Bozeman Science](https://www.youtube.com/channel/UCEik-U3T6u6JA0XiHLbNbOw)的一个科普视频为例：

**首先复制视频的分享代码：**

![image-20220225214429976](https://vip2.loli.io/2022/02/25/GgidB6Se4zRLTx7.png)

**这是复制的视频分享代码：**

```html
<iframe width="560" height="315" src="https://www.youtube.com/embed/MnYppmstxIs" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
```

<iframe width="560" height="315" src="https://www.youtube.com/embed/MnYppmstxIs" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

**为了使视频的长宽自适应，可将代码修改为如下这样：**  
```html
<div style="position: relative; width: 100%; height: 0; padding-bottom: 75%;">
    <iframe src="https://www.youtube.com/embed/MnYppmstxIs"  scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true" style="position: absolute; width: 100%; height: 100%; left: 0; top: 0;"></iframe>
</div>
```
<div style="position: relative; width: 100%; height: 0; padding-bottom: 75%;">
    <iframe src="https://www.youtube.com/embed/MnYppmstxIs"  scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true" style="position: absolute; width: 100%; height: 100%; left: 0; top: 0;"></iframe>
</div>


**参考资料：**

1. [hexo博客插入视频bilibili 和 youtube的mv视频《take me hand 》。](https://xmuli.tech/posts/c0c8973/)

