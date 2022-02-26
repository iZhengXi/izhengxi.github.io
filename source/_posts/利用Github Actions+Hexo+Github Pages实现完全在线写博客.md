---
title: 利用Github Actions+Hexo+Github Pages实现完全在线写博客
date: 2022-01-19 16:32:26
tags:
- Github Actions
- Hexo
- Github Pages
categories:
- 网站建设
---

<a name="JnUTE"></a>
# 1、本地安装Hexo

<br />Hexo是基于node.js，所以需要安装[node.js](https://nodejs.org/en/)和[Git](https://git-scm.com/)(用于提交本地文件到Github)<br />安装好环境后，打开Git Bash，全局安装`hexo-cli`

```bash
npm install -g hexo-cli
```
之后利用hexo命令建立本地站点：

<!-- more -->

```bash
hexo init howarzheng.github.io
cd howarzheng.github.io
npm install
```

<br />执行完毕之后，博客的框架就安装好了。<br />执行下面命令即可预览本地博客内容：<br />

```bash
hexo g
hexo s
```


> Hexo站点和主题相关配置网上有较多教程，这里略。



<a name="747ac54f"></a>
# 2、Github Actions实现Github Pages自动发布

<br />为了利用Github Actions实现Github Pages自动发布，我们首先需要将我们的Hexo站点文件推送到远程Github仓库分支。<br />**Git配置：**<br />

```bash
# 配置用户名和邮箱
 git config --global user.name "HowarZheng
 git config --global user.email "howar.zheng@gmail.com
```

<br />接着生成 ssh 密钥文件，输入如下命令后直接三次回车即可：<br />

```bash
# 生成 ssh 密钥
ssh-keygen -t rsa -C "howar.zheng@gmail.com"
```

<br />执行上述命令之后，会生成 id_rsa 和 id_rsa.pub 两个文件，前者是我们私有的，而后者则是对外开放的。<br />**配置公钥：**<br />接下来我们需要访问存放网页的仓库，也就是 Hexo 部署以后的仓库，比如：yourname.github.io 这种，访问 Settings -> Deploy keys：<br />![howarzhengimage](https://vip2.loli.io/2022/01/27/KjRfOeSGDqoJsVd.jpg)<br />点击Add deploy key添加公钥。Title: HEXO_DEPLOY_PUB；Key: id_rsa.pub内容。<br />**配置私钥：**<br />首先在 GitHub 上打开保存 Hexo 的仓库，访问 Settings -> Secrets，然后选择 New secret。Name: HEXO_DEPLOY_PRI。Value: id_rsa内容。<br />**推送本地文件到远程分支：**
```bash
git init // 初始化版本库
git add . // 添加文件到版本库（只是添加到缓存区），.代表添加文件夹下所有文件 
git commit -m "版本备注"
git remote add origin git@github.com:HowarZheng/howarzheng.github.io.git // 关联远程仓库
git push -u origin master // 第一次推送时
git push origin master // 第一次推送后，直接使用该命令即可推送修改
```
<a name="hOrjt"></a>
# 3、创建Github Actions Workflow


Hexo站点文件在master分支，hexo生成的HTML站点文件在hexo-web分支，图片放在Figure_Bed分支。
```yaml
name: Hexo Deploy

on:
  push:
    branches:
      - master

jobs:
  build:
    runs-on: ubuntu-18.04
    if: github.event.repository.owner.id == github.event.sender.id

    steps:
      - name: Checkout source
        uses: actions/checkout@v2
        with:
          ref: master

      - name: Setup Node.js
        uses: actions/setup-node@v1
        with:
          node-version: '12'

      - name: Setup Hexo
        env:
          ACTION_DEPLOY_KEY: ${{ secrets.HEXO_DEPLOY_PRI }}
        run: |
          mkdir -p ~/.ssh/
          echo "$ACTION_DEPLOY_KEY" > ~/.ssh/id_rsa
          chmod 700 ~/.ssh
          chmod 600 ~/.ssh/id_rsa
          ssh-keyscan github.com >> ~/.ssh/known_hosts
          git config --global user.email "howar.zheng@gmail.com"
          git config --global user.name "HowarZheng"
          npm install hexo-cli -g
          npm install
      - name: Deploy
        run: |
          hexo clean
          hexo deploy
```
> 简单解释一下，当我们推送内容到远程 master 分支的时候，就会触发这个 Workflow。使用 Ubuntu 18.04 作为 hexo deploy 的系统。首先 checkout 源代码，然后设置使用最新的 Node.js v12 LTS 作为 node 解释器。接下来就是创建 SSH 相关的配置文件，注意 secrets.HEXO_DEPLOY_PRI 就是对应我们之前设置的私钥，所以名字一定不要搞错。git config 相关的名字和邮件地址替换成大家自己使用的就好了。最后就是安装 Hexo CLI、各个依赖模块和部署了。

<a name="vvhh9"></a>
# 参考文献
[1、用 GitHub Actions 来自动部署 Hexo](https://tommy.net.cn/2020/08/06/deploy-hexo-with-github-actions/)
