---
title: Hexo keep主题更新Twikoo为最新版本
tags:
  - Hexo
  - keep
  - Twikoo
categories:
  - 网站建设
  - Hexo
abbrlink: c451f068
date: 2022-07-03 18:32:13
---

编辑文件`\themes\keep\layout\_partial\comment\twikoo.ejs` 

```ejs
<% if(theme.comment.use === 'twikoo' && theme.comment.twikoo.env_id) { %>
    <div class="twikoo-container">
        <script <%= theme.pjax.enable === true ? 'data-pjax' : '' %>
                src="//cdn.jsdelivr.net/npm/twikoo@1.5.11/dist/twikoo.all.min.js"
        ></script>
        <div id="twikoo-comment"></div>
        <script <%= theme.pjax.enable === true ? 'data-pjax' : '' %>>
            function loadTwikoo() {
                twikoo.init({
                    el: '#twikoo-comment',
                    envId: '<%= theme.comment.twikoo.env_id %>',
                    region: '<%= theme.comment.twikoo.region %>',
                });
            }

            if ('<%= theme.pjax.enable %>') {
                const loadTwikooTimeout = setTimeout(() => {
                    loadTwikoo();
                    clearTimeout(loadTwikooTimeout);
                }, 1000);
            } else {
                window.addEventListener('DOMContentLoaded', loadTwikoo);
            }
        </script>
    </div>
<% } %>
```

在这里编辑twikoo版本即可。



<!-- more -->



之后还需要更新云函数版本为相应的twikoo版本。

> 登录[环境-云函数](https://console.cloud.tencent.com/tcb/scf/index)，点击 twikoo，点击函数代码，打开 `package.json` 文件，将 `"twikoo-func": "x.x.x"` 其中的版本号修改为最新版本号，点击“保存并安装依赖”即可。

