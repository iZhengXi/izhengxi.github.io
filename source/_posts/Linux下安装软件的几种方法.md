---
title: Linux下安装软件的几种方法
tags:
  - Linux
categories:
  - 编程笔记
  - Linux
abbrlink: 9c349f79
date: 2022-01-23 15:32:26
---
首先，我们来看Linux的各个目录的作用。

<!-- more -->

<br />**_Linux的目录像一颗树：_**<br />

![image.png](https://vip1.loli.io/2022/01/25/RCw9iTzF5JPpqQA.png)

<br />每个目录作用如下：<br />![image.png](https://vip2.loli.io/2022/01/25/GlwjsxHNb1UWOgv.png)<br />**_了解了个目录作用后，现在接受Linux下安装应用程序的几种方式。_**<br />
<a name="toc-heading-1"></a>

## 一、rpm包安装方式步骤：
1、找到相应的软件包，比如soft.version.rpm，下载到本机某个目录；<br />2、打开一个终端，su -成root用户；<br />3、cd soft.version.rpm所在的目录；<br />4、输入rpm -ivh soft.version.rpm<br />**详细介绍：**<br />**1. 安装：**<br />　　我只需简单的一句话，就可以说完。执行：<br />　　rpm –ivh rpm的软件包名<br />　 更高级的，请见下表：<br />　　rpm参数 参数说明<br />　　-i 安装软件<br />　　-t 测试安装，不是真的安装<br />　　-p 显示安装进度<br />　　-f 忽略任何错误<br />　　-U 升级安装<br />　　-v 检测套件是否正确安装<br />　　这些参数可以同时采用。更多的内容可以参考RPM的命令帮助。<br />**2. 卸载：**<br />　　我同样只需简单的一句话，就可以说完。执行：<br />　　rpm –e 软件名<br />　　不过要注意的是，后面使用的是软件名，而不是软件包名。例如，要安装software-1.2.3-1.i386.rpm这个包时，应执行：<br />　　rpm –ivh software-1.2.3-1.i386.rpm<br />　　而当卸载时，则应执行：<br />　　rpm –e software。<br />**另外，在Linux中还提供了象GnoRPM、kpackage等图形化的RPM工具，使得整个过程会更加简单。**
<a name="toc-heading-2"></a>
## 二、deb包安装方式步骤：
1、找到相应的软件包，比如soft.version.deb，下载到本机某个目录；<br />2、打开一个终端，su -成root用户；<br />3、cd soft.version.deb所在的目录；<br />4、输入dpkg -i soft.version.deb<br />**详细介绍：**<br />这是Debian Linux提供的一个包管理器，它与RPM十分类似。但由于RPM出现得更早，所以在各种版本的Linux都常见到。而debian的包管理器dpkg则只出现在Debina Linux中，其它Linux版本一般都没有。<br />**1. 安装**<br />　 dpkg –i deb的软件包名<br />　　如：dpkg –i software-1.2.3-1.deb<br />**2. 卸载**<br />　　 dpkg –e 软件名<br />　　如：dpkg –e software<br />**3.查询：**查询当前系统安装的软件包：<br />dpkg –l ‘_软件包名_’<br />如：dpkg –l ‘_software_‘
<a name="toc-heading-3"></a>
## 三、tar.gz源代码包安装方式：
1、找到相应的软件包，比如soft.tar.gz，下载到本机某个目录；<br />2、打开一个终端，su -成root用户；<br />3、cd soft.tar.gz所在的目录；<br />4、tar -xzvf soft.tar.gz //一般会生成一个soft目录<br />5、cd soft<br />6、./configure<br />7、make<br />8、make install<br />**详细介绍：**

1. **安装：**<br />　　整个安装过程可以分为以下几步：<br />　　1） 取得应用软件：通过下载、购买光盘的方法获得；<br />　　2）解压缩文件：一般tar包，都会再做一次压缩，如gzip、bz2等，所以你需要先解压。如果是最常见的gz格式，则可以执行：“tar –xvzf 软件包名”，就可以一步完成解压与解包工作。如果不是，则先用解压软件，再执行“tar –xvf 解压后的tar包”进行解包；<br />　　3） 阅读附带的INSTALL文件、README文件；<br />　　4） 执行“./configure”命令为编译做好准备；<br />　　5） 执行“make”命令进行软件编译；<br />　　6） 执行“make install”完成安装；<br />　　7） 执行“make clean”删除安装时产生的临时文件。<br />　　好了，到此大功告成。我们就可以运行应用程序了。但这时，有的读者就会问，我怎么执行呢？这也是一个Linux特色的问题。其实，一般来说， Linux的应用软件的可执行文件会存放在/usr/local/bin目录下！不过这并不是“放四海皆准”的真理，最可靠的还是看这个软件的 INSTALL和README文件，一般都会有说明。
1. **卸载：**<br />　　通常软件的开发者很少考虑到如何卸载自己的软件，而tar又仅是完成打包的工作，所以并没有提供良好的卸载方法。<br />　　那么是不是说就不能够卸载呢！其实也不是，有两个软件能够解决这个问题，那就是Kinstall和Kife，它们是tar包安装、卸载的黄金搭档。
<a name="toc-heading-4"></a>
## 四、tar.bz2源代码包安装方式：
1、找到相应的软件包，比如soft.tar.bz2，下载到本机某个目录；<br />2、打开一个终端，su -成root用户；<br />3、cd soft.tar.bz2所在的目录；<br />4、tar -xjvf soft.tar.bz2 //一般会生成一个soft目录<br />5、cd soft<br />6、./configure<br />7、make<br />8、make install
<a name="toc-heading-5"></a>
## 五、apt方式安装：（安装deb包）
1、打开一个终端，su -成root用户；<br />2、apt-cache search soft 注：soft是你要找的软件的名称或相关信息<br />3、如果2中找到了软件soft.version，则用apt-get install soft.version命令安装软件<br />注：只要你可以上网，只需要用apt-cache search查找软件，用apt-get install软件<br />详细介绍：<br />apt-get是debian，ubuntu发行版的包管理工具，与红帽中的yum工具非常类似。<br />apt-get命令一般需要[root权限](http://baike.baidu.com/view/3967294.htm)执行，所以一般跟着sudo命令例sudo apt-get xxxx
```bash
apt-get install packagename——安装一个新软件包（参见下文的aptitude）
apt-get remove packagename——卸载一个已安装的软件包（保留配置文件）
apt-get --purge remove packagename——卸载一个已安装的软件包（删除配置文件）
dpkg --force-all --purge packagename ——有些软件很难卸载，而且还阻止了别的软件的应用，就可以用这个，不过有点冒险。
apt-get autoremove——因为apt会把已装或已卸的软件都备份在硬盘上，所以如果需要空间的话，可以让这个命令来删除你已经删掉的软件。
apt-get autoclean——定期运行这个命令来清除那些已经卸载的软件包的.deb文件。通过这种方式，可以释放大量的磁盘空间。如果需求十分迫切，可以使用apt-get clean以释放更多空间。这个命令会将已安装软件包裹的.deb文件一并删除。
apt-get clean——这个命令会把安装的软件的备份也删除，不过这样不会影响软件的使用的。
apt-get upgrade——更新所有已安装的软件包
apt-get dist-upgrade——将系统升级到新版本
apt-cache search string——在软件包列表中搜索字符串
apt-cache showpkg pkgs——显示软件包信息。
apt-cache stats——查看库里有多少软件
apt-cache dumpavail——打印可用软件包列表。
apt-cache show pkgs——显示软件包记录，类似于dpkg –print-avail。
apt-cache pkgnames——打印软件包列表中所有软件包的名称
（需要定期运行这一命令以确保您的软件包列表是最新的）
```
> 简单的说： dpkg只能安装已经下载到本地机器上的deb包. apt-get能在线下载并安装deb包,能更新系统,
> 且还能自动处理包与包之间的依赖问题,这个是dpkg工具所不具备的。

<a name="toc-heading-6"></a>
## 六、yum方式安装：(安装rpm包)
rpm 是linux的一种软件包名称，以.rmp结尾，安装的时候语法为：rpm -ivh。<br />rpm包的安装有个很大的缺点就是文件的关联性太大，有时装一个软件要安装很多其他的软件包，很麻烦。<br />所以为此RedHat小红帽开发了yum安装方法，他可以彻底解决这个关联性的问题，很方便，只要配置两个文件即可安装，安装方法是：yum -y install 。<br />yum并不是一中包，而是安装包的软件。
> 简单的说： rpm 只能安装已经下载到本地机器上的rpm 包. yum能在线下载并安装rpm包,能更新系统,
> 且还能自动处理包与包之间的依赖问题,这个是rpm 工具所不具备的。

<a name="toc-heading-7"></a>
## 七、bin文件安装：
如果你下载到的软件名是soft.bin，一般情况下是个可执行文件，安装方法如下：<br />1、打开一个终端，su -成root用户；<br />2、chmod +x soft.bin<br />3、./soft.bin //运行这个命令就可以安装软件了
<a name="toc-heading-8"></a>
## 八、不需要安装的软件：
有了些软件，比如lumaqq，是不需要安装的，自带jre解压缩后可直接运行。假设<br />下载的是lumaqq.tar.gz，使用方法如下：<br />1、打开一个终端，su -成root用户；<br />2、tar -xzvf lumaqq.tar.gz //这一步会生成一个叫LumaQQ的目录<br />3、cd LumaQQ<br />4、chmod +x lumaqq //设置lumaqq这个程序文件为可运行<br />5、此时就可以运行lumaqq了，用命令./lumaqq即可，但每次运行要输入全路径或<br />切换到刚才生成的LumaQQ目录里<br />6、为了保证不设置路径就可以用，你可以在/bin目录下建立一个lumaqq的链接，<br />用命令ln -s lumaqq /bin/ 即可，以后任何时候打开一个终端输入lumaqq就可以<br />启动QQ聊天软件了<br />7、 如果你要想lumaqq有个菜单项，使用菜单编辑工具，比如Alacarte Menu<br />Editor，找到上面生成的LumaQQ目录里的lumaqq设置一个菜单项就可以了，当然你<br />也可以直接到 /usr/share/applications目录，按照里面其它*.desktop文件的格<br />式生成一个自己的desktop文件即可。
> 参考文献：

1. [https://blog.csdn.net/m0_37327416/article/details/78779532](https://blog.csdn.net/m0_37327416/article/details/78779532)
1. [https://www.jianshu.com/p/e5057c1ca1a1](https://www.jianshu.com/p/e5057c1ca1a1)
