---
title: 博客和keep主题更新方法
tags:
  - 博客
  - keep
categories:
  - Hexo
date: 2022-07-03 9:32:06
---
# 博客更新

```bash
git clone git@github.com:iZhengXi/zhengxi.io.git
cd zhengxi.io

# 本地更新完博客后，推送到远程仓库。
git add .
git commit -m "update"
git push origin master

# 使用git前需要进行配置
# 配置用户名和邮箱
 git config --global user.name "iZhengXi"
 git config --global user.email "z.x@outlook.jp"
 
 # 生成 ssh 密钥
ssh-keygen -t rsa -C "z.x@outlook.jp"
# 执行上述命令之后，会生成 id_rsa 和 id_rsa.pub 两个文件，前者是我们私有的，而后者则是对外开放的。

# hexo本地使用需要安装node.js
# 安装好环境后，打开Git Bash，全局安装hexo-cli
npm install -g hexo-cli
hexo init zhengxi.io
cd zhengxi.io
npm install
hexo g
hexo s

# 其他一些可能会用到的git命令。
# git强制覆盖：
git fetch --all
git reset --hard origin/master
git pull

# git强制覆盖本地命令（单条执行）：
git fetch --all && git reset --hard origin/master && git pull

```



<!-- more -->



# 主题更新

```bash
# nstall the latest version throuth npm:
cd hexo-site
npm update hexo-theme-keep

# Or update to latest master branch:
cd themes/keep
git pull
```