---
title: Get和Post请求的区别
date: 2022-01-25 17:52:26
tags:
- Python
categories:
- 编程笔记
- 	Python
---

_Get请求和Post请求是HPPT中最常用的请求，那么他们有什么区别呢？_<br />**我们来看看标准答案：**

> - GET在浏览器回退时是无害的，而POST会再次提交请求。
> - GET产生的URL地址可以被Bookmark，而POST不可以。
> - GET请求会被浏览器主动cache，而POST不会，除非手动设置。
> - GET请求只能进行url编码，而POST支持多种编码方式。
> - GET请求参数会被完整保留在浏览器历史记录里，而POST中的参数不会被保留。
> - GET请求在URL中传送的参数是有长度限制的，而POST么有。
> - 对参数的数据类型，GET只接受ASCII字符，而POST没有限制。
> - GET比POST更不安全，因为参数直接暴露在URL上，所以不能用来传递敏感信息。
> - GET参数通过URL传递，POST放在Request body中。

以上标准答案来自[w3schools](https://www.w3schools.com/)。<br />到这里前端的小伙伴们可能会说，他们的区别是：
> GET使用URL或Cookie传参，而POST将数据放在BODY中

**这其实是错的！**<br />GET和POST是由HTTP协议定义的。在HTTP协议中，Method和Data（URL， Body， Header）是正交的两个概念，也就是说，使用哪个Method与应用层的数据如何传输是没有相互关系的。<br />HTTP没有要求，如果Method是POST数据就要放在BODY中。也没有要求，如果Method是GET，数据（参数）就一定要放在URL中而不能放在BODY中。<br />因此网上的那些说法都是错的！<br />HTTP是什么？Hyper Text Transfer Protocol（超文本传输协议）的缩写，是基于TCP/IP的关于数据如何在万维网中如何通信的协议。<br />**具有以下特征：**

- 基于tcp/ip、一种网络应用层协议、超文本传输协议HyperText Transfer Protocol
- 工作方式：客户端请求服务端应答的模式
- 快速：无状态连接，灵活：可以传输任意对象，对象类型由Content-Type标记
- 客户端请求request消息包括以下格式：请求行（request line）、请求头部（header）、空行、请求数据。

我们惊讶的发现，HTTP、Get和Post他们的底层都是TCP/IP，也就是说，GET/POST都是TCP链接。GET能做的，POST也能做。你要给GET加上request body，给POST带上url参数，技术上是完全行的通的。<br />HTTP只是个行为准则，而TCP才是GET和POST怎么实现的基本。<br />另外，GET和POST还有一个重大区别：
> GET产生一个TCP数据包；POST产生两个TCP数据包。

- 对于GET方式的请求，浏览器会把http header和data一并发送出去，服务器响应200（返回数据）；
- 而对于POST，浏览器先发送header，服务器响应100 continue，浏览器再发送data，服务器响应200 ok（返回数据）。
> 参考文献：

1. [https://www.oschina.net/news/77354/http-get-post-different](https://www.oschina.net/news/77354/http-get-post-different)
1. [https://www.w3schools.com/](https://www.w3schools.com/)
1. [https://juejin.im/entry/597ca6caf265da3e301e64db](https://juejin.im/entry/597ca6caf265da3e301e64db)
