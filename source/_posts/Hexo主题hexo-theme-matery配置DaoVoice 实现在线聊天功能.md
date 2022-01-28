---
title: Hexo主题hexo-theme-matery配置DaoVoice 实现在线聊天功能
date: 2022-01-25 18:01:26
tags:
- Hexo
- matery
categories:
- 网站建设
- 	Hexo
---

**DaoVoice 可以提供在线联系的功能，可以让静态博客Hexo更加多样和完整。**<br />设置完成效果如下：<br />![](https://vip1.loli.io/2022/01/25/bUaPyOcKD5orTJs.png)
<a name="toc-heading-1"></a>

## 实现方法
<a name="toc-heading-2"></a>
### 注册
首先需要注册一个 DaoVoice，[点击注册](http://dashboard.daovoice.io/get-started?invite_code=7f3d6e70)。<br />注册成功后，进入后台控制台，进入到 `应用设置-->安装到网站` 页面，**可以得到一个 ** **`app_id`**：**<br />![](https://vip1.loli.io/2022/01/25/mywG9pZqtken3RX.png)<br />**之后复制下的`app_id`

![](https://vip2.loli.io/2022/01/25/ySOsQ39NpXlIcxm.png)<br />接下来，打开`\themes\hexo-theme-matery\layout\_partial`文件夹下的`footer.ejs`文件<br />在文件末尾写入：
```javascript
<script>
  (function(i,s,o,g,r,a,m){i["DaoVoiceObject"]=r;i[r]=i[r]||function(){(i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;a.charset="utf-8";m.parentNode.insertBefore(a,m)})(window,document,"script",('https:' == document.location.protocol ? 'https:' : 'http:') + "//widget.daovoice.io/widget/0f81ff2f.js","daovoice")
  daovoice('init', {
      app_id: "刚刚复制的app_id"
    });
  daovoice('update');
  </script>
```
在主题配置文件 `_config.yml`，添加如下代码：
```yaml
# Online contact 
daovoice: true
daovoice_app_id: 这里输入前面复制的app_id
```
[hexo-theme-matery](https://github.com/blinkfox/hexo-theme-matery)主题下聊天的按钮会和其他按钮重叠到一起，可以到聊天设置，修改下按钮的位置：<br />![image.png](https://vip1.loli.io/2022/01/25/8QbHw5uLxNmreIF.png)<br />位置可以在 hexo s 调试模式下进行调试，效果满意后部署就可以看到最终效果啦！
