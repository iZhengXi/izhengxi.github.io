---
title: 搭建基于telegraph的无限网盘
tags:
  - telegraph
categories:
  - 转载
date: 2022-03-13 14:35:26
---
 > 本文转载自：https://blog.paddi.cc/index.php/archives/8/

telegram里面用户是可以无限上传文件的，但是我们在国内无法直接访问telegram，所以有大佬开发了个项目，可以通过机器人获取telegram里的文件直链，只需要一台vps即可

原项目地址:[点我直达](https://github.com/EverythingSuckz/TG-FileStreamBot)

如果嫌搭建麻烦可以直接用项目作者的机器人:**@TG_FileStreamBot**

测试文件:**https://00s23-webgx4.wrench.gq/35822/test.mp4**

<!-- more -->

如果本文图片或者教程失效，请在本文评论区回复或者发送邮件到**talk@live.cn**提醒我
本文原创地址:https://blog.paddi.cc/index.php/archives/8/

环境要求:

- python3

首先我们通过git拉取项目:

```bash
git clone https://github.com/EverythingSuckz/TG-FileStreamBot
```

进入项目地址

```bash
cd TG-FileStreamBot
virtualenv -p /usr/bin/python3 venv
```

如果这一步报错“Command 'virtualenv' not found, but can be installed with”，说明没有安装virtualenv,执行:

```bash
pip3 install virtualenv
```

安装好virtualenv后，再执行：

```bash
virtualenv -p /usr/bin/python3 venv
. ./venv/bin/activate
pip3 install -r requirements.txt
```

**然后我们telegram里面搜索botfather并添加机器人：**

![botfather](https://vip1.loli.io/2022/03/13/vzWAi6ZU4NgeQMb.png)](http





向bot发送**/newbot**指令来新建一个bot,以下带#号为bot回复的语句

```bash
#Alright, a new bot. How are we going to call it? Please choose a name for your bot.
输入你的bot名称
#Good. Now let's choose a username for your bot. It must end in `bot`. Like this, for example: TetrisBot or tetris_bot.
然后输入你的bot用户名，之后你会用这个用户名搜索你的bot,bot名必须以_bot结尾,比如zhangsan_bot
```

**输入用户名后，botfather会回复给你一串机器人密钥，我们要保存好，图中圈出来的部分就是密钥**

![图中红框圈的部分即为密钥](https://vip2.loli.io/2022/03/13/ivG3ICLbljDJR1y.png)

**图中红框圈的部分即为密钥**



接下来我们新建一个频道，公开频道或者私人频道都可以，再将我们新建的bot添加到频道中，并且给管理员权限，然后我们在频道中发一条消息，再将消息转发给一个叫**@missrose_bot** 的机器人，在ross机器人中回复我们发给它的信息**/id**，它会回复一个负数给你，那个就是你频道的id，id格式为**-100xxxxxxxxxx**如图：

![图中红框为频道Id](https://vip1.loli.io/2022/03/13/nvOpClHtJ8bZ9UE.png)

**图中红框为频道Id**



我们再到浏览器打开https://my.telegram.org/，登录我们的telegram账号，注册获取一个app id和app hash，这一步就不详细写了，按流程走就行了

现在我们拥有一个**机器人密钥**，一个**app id**和一个**app hash**和一个**频道id**

然后在项目目录下新建一个名为**.env**的文件，注意不要忘记前面那个**.**

按照以下格式编辑.env文件，注意#号后面的内容不要输入到文件中:

```bash
API_ID=     #这里写你的app id
API_HASH=   #这里写你的app hash
BOT_TOKEN=  #这里写你的机器人密钥
BIN_CHANNEL=  #这里写你的频道id
PORT=         #这里写你想要使用的端口号
FQDN=         #这里填你的域名
HAS_SSL=False  #这里是ssl选项，最好填默认的False
```

编辑好后，保存，我们再在ssh内执行

```bash
python3 -m WebStreamer
```

现在我们只要把tg内的文件转发给我们新建的机器人，或者上传文件给机器人，机器人就会返回一条文件直链供我们下载，下载文件会经过我们的vps中转，如图
![发送文件会返回文件直链](https://vip1.loli.io/2022/03/13/PelwE7LIWMkNvGK.png)

**发送文件会返回文件直链**
