---
title: 在CloudFlare Pages上部署Hexo博客
date: 2022-01-28 12:32:06
tags:
- CloudFlare
- CloudFlare Pages
categories:
- 网站建设
---

我的初衷是完全使用Github写博客，目前可以的方案有（**免备案**）：

- Github Actions+Github Pages
- CloudFlare Pages

目前这两种方案都可以实现自动部署，且完全使用Github写博客。考虑到速度和稳定性，我决定将博客部署到CloudFlare Pages。

<!-- more -->

<a name="nuxQC"></a>

# 1、本地Hexo站点搭建
以Keep主题为例，在本地搭建Hexo站点。首先安装[Node.js](https://nodejs.org/en/)和[Git](https://git-scm.com/)。<br />之后在本地新建站点文件夹，并安装`Hexo`。
```bash
npm install -g hexo-cli # 全局安装hexo
hexo init howarzheng # howarzheng是站点文件夹
cd howarzheng
npm install
hexo g
hexo s # 之后根据提示在浏览器中打开localhost:4000即可预览本地站点。

# 将Hexo站点的默认主题landscape更换为keep
# 如果你在使用 Hexo 5.0 或更高版本，最简单的安装方式是通过 npm。本示例通过npm进行安装keep主题。
cd howarzheng # 在Hexo站点根文件夹通过npm安装主题
npm install hexo-theme-keep # 之后可在站点根文件夹~/node_modules中看到安装的keep主题。

# 也可以通过 git clone 克隆整个仓库来安装keep主题：
cd howarzheng
git clone https://github.com/XPoet/hexo-theme-keep themes/keep
```
安装好`keep`主题后，在站点根文件夹新建`_config.keep.yml`主题配置文件，并按照[keep主题](https://keep-docs.xpoet.cn/)配置说明配置即可。
> 只要存在于 `_config.keep.yml `的配置都是高优先级，修改keep主题文件夹里的` _config.yml` 是无效的。

<a name="vzSrN"></a>
# 2、将本地Hexo站点推送至Github
**生成SSH key：**<br />SSH key是一种身份凭证，用于将本地Hexo推送至Github。SSH key包含公钥和私钥，公钥存储在Github，私钥存储在本地。
```bash
# 配置用户名和邮箱
 git config --global user.name "HowarZheng
 git config --global user.email "howar.zheng@gmail.com
 
  生成 ssh 密钥
ssh-keygen -t rsa -C "howar.zheng@gmail.com" # 输入如下命令后直接三次回车即可。
# 执行上述命令之后，会生成 id_rsa 和 id_rsa.pub 两个文件，id_rsa是私钥，id_rsa.pub是公钥。
```
**添加SSH key到Github：**<br />登录Github账号，点击个人头像。接着点击设置-SSH and keys。<br />![image.png](https://vip1.loli.io/2022/01/28/jNMdx7sG2KkOg41.png)<br />之后点击 New SSH key。<br />![image.png](https://vip2.loli.io/2022/01/28/wUuYET86rlCgBOM.png)<br />Title随意，key输入本地~/.ssh/id_rsa.pub(公钥)内容。<br />![image.png](https://vip1.loli.io/2022/01/28/TtzeOHIG8DBQWCP.png)<br />之后在本地检查SSH key是否生效。

```bash
$ ssh -T git@github.com
```
<br /> 直接输入 yes 回车，如果提示以下内容说明已配置成功。
```bash
Hi xxx! You've successfully authenticated, but GitHub does not provide shell access.
```
**推送本地Hexo站点到Github远程仓库：**
```bash
cd howarzheng
git init
git add .
git commit -m "版本备注"
git remote add origin git@github.com:HowarZheng/howarzheng.git # 关联远程仓库。 origin是远程仓库的简写，可以自定义。
git push origin master --force # 确保本地版本是最新的时候，直接使本地仓库强制覆盖远程仓库。

# 获取远程库与本地同步合并（如果远程库不为空必须做这一步，否则后面的提交会失败）
git pull --rebase origin master

git push origin master # 之后推送时使用此命令即可。
```
<a name="EuRnB"></a>
# 3、部署Hexo站点到CloudFlare Pages
首先登陆[CloudFlare](https://www.cloudflare.com/zh-cn/)，点击左侧栏Pages，接着点击创建项目。<br />![image.png](https://vip2.loli.io/2022/01/28/PTUFBevqs5IxAg1.png)<br />之后会让你关联Git仓库，可以是Github，也可以是GitLab。这里我们选Github。登录Github并授权后，选择需要部署的仓库和分支。<br />![image.png](https://vip1.loli.io/2022/01/28/GP45vpLxHUzWyEM.png)<br />最后输入部署时的构建命令和其他参数，自动部署网站即可。<br />框架预设如何找不到`Hexo`的化，就选`none`。<br />![image.png](https://vip1.loli.io/2022/01/28/GlwtIqnzLBPX6b7.png)<br />之后想要更新文章，只需要在Github仓库/source/_posts文件夹内修改文章或上传文章即可，最终将实现完全使用Github写博客。<br />CloudFlare Pages和Github Pages一样，可以自定义域名，并且CloudFlare Pages可以绑定10个域名。<br /><br />

---

后期如果想要对博客进行大改，例如修改主题和安装插件，只需要将Github 仓库克隆到本地，修改完再push 到远程Github仓库即可。<br />​

**CloudFlare使用限制（说明书）：**[https://developers.cloudflare.com/pages/](https://developers.cloudflare.com/pages/)<br />![](https://vip2.loli.io/2022/01/28/cSk2M85ENlZhTAv.png)

- 专业版（Pro）每月5000次构建发布，5个并发构建，20美元/月；
- 商业版（Business）每月20000次构建发布，20个并发构建，200美元/月。
- 其它限制为：单个文件大小 ≤25MB ，单个站点文件总数 ≤20000个。

**​**

**​**<br />
