---
title: Aspera——利用SRR号批量高效下载FASTQ或SRA数据
tags:
  - Aspera
categories:
  - 生物信息学
  - 	RNA-seq转录组数据分析
date: 2022-07-12 09:32:06
---

> 本节简单介绍Aspera安装和使用，并给出**利用SRR号批量下载FASTQ或SRA数据的方法**，通过比较发现aspera的下载速度与prefetch相比有了质的飞跃。

**前言：**

我们下载测序数据一般使用sra-tools的prefetch功能，通过SRR号从NCBI的SRA数据库下载SRA文件，这种方式比较稳定，但下载速度有所限制且需要将SRA再转化为FASTQ文件，这过程中又会消耗不少时间和算力。 一种替代方法是使用Aspera软件，从EBI（European Bioinformatics Institute）的ENA(European Nucleotide Archive)数据库直接下载FASTQ格式文件，免去了转化格式的步骤，且下载速度有了质的提升，可以说是碾压prefetch。其缺点是有时候不太稳定，受网络影响速度波动大。


<!-- more -->


![img](https://vip2.loli.io/2022/07/12/H3KD2TlSWfvq7in.jpg)

官网：https://www.ibm.com/products/aspera

## 1. Aspera的下载和安装

### ① conda 安装

这是最为简单方便的方式，推荐使用：

```bash
conda install -c hcc aspera-cli
```

### ② 官网安装包下载安装

参照：[Aspera：基因组数据高速下载利器，以NCBI和EBI数据下载为例](https://mp.weixin.qq.com/s?__biz=MzIxMjQxMDYxNA==&mid=2247483983&idx=1&sn=d6478dcb0faa5ba03fcee176ee41b5be&chksm=9747cbd6a03042c0e8c473bf18c945050fbfecb6ca3e0529517b3e69e630b62944811967dc02&scene=178&cur_album_id=1434291934281170945#rd) 

官网下载地址：https://www.ibm.com/aspera/connect/

首先需要进入上面的aspera 下载地址，获取linux版本的下载链接，再运行以下代码：

```bash
# 下载
wget https://d3gcli72yxqn2z.cloudfront.net/downloads/connect/latest/bin/ibm-aspera-connect_4.2.0.42_linux.tar.gz
# 解压
tar xvf ibm-aspera-connect******.tar.gz   
# 解压后得到一个脚本文件，运行该脚本，即可完成自动安装
bash ibm-aspera-connect*******.sh
# 所有安装文件都在~/.aspera/connect目录下，在 ~/.bashrc添加环境变量
export PATH=~/.aspera/connect/bin/:$PATH
# 使环境变量生效
source  ~/.bashrc
```

------

## 2. 基本参数与使用

https://www.ibm.com/support/pages/downloading-data-ncbi-command-line#usage

### ① 使用示例：

下载SRR12207279_1.fastq.gz文件到当前文件夹：

```shell
ascp  -vQT -l 500m -P33001 -k 1 -i \
~/.aspera/connect/etc/asperaweb_id_dsa.openssh \
era-fasp@fasp.sra.ebi.ac.uk:/vol1/fastq/SRR122/079/SRR12207279/SRR12207279_1.fastq.gz 
```

> \######### 主要使用参数
> **-v** 详细模式
> **-Q** 用于自适应流量控制，磁盘限制所需
> **-T** 设置为无需加密传输
> **-l** 最大下载速度，一般设为500m
> **-P** TCP 端口，一般为33001
> **-k** 断点续传，通常设为 1
> **-i** 免密下载的密钥文件

### ② 密钥文件路径

如果是安装包安装的方式，私钥文件一般位于`~/.aspera/connect/etc/asperaweb_id_dsa.openssh` 如果是conda安装的方式，先使用`which ascp`命令查看ascp命令所在路径：

![img](https://vip2.loli.io/2022/07/12/xKhiILCwEgqTl5O.png)

再将bin及之后内容替换为 `etc/asperaweb_id_dsa.openssh` 如上图中替换后，密钥文件路径就为`/home/gu/miniconda3/envs/share/etc/asperaweb_id_dsa.openssh` 最后再`ll`查看确认一下密匙路径：

![img](https://vip2.loli.io/2022/07/12/npHkV2gu8sKhaNv.png)



### ③ 从EBI获取FASTQ文件下载地址

[https://www.ebi.ac.uk/ena/browser/home](https://link.zhihu.com/?target=https%3A//www.ebi.ac.uk/ena/browser/home)中搜索GSE号等信息（如：GSE154290），找到所需的项目，Column Select勾选fastq_aspera及其他想要的信息，直接点击复制Aspera下载地址，或者下载TSV文件，其中包含有FASTQ文件下载地址信息等

![img](https://vip2.loli.io/2022/07/12/Uo9TBIPkjmVdGvW.jpg)

![img](https://vip2.loli.io/2022/07/12/mkAWt3TMEoKzX5P.jpg)

![img](https://vip2.loli.io/2022/07/12/kByipoFWE73GPAK.jpg)



有了下载地址，其实就可以批量写循环下载了：参见生信技能树文章 [使用ebi数据库直接下载fastq测序数据的改进脚本](https://link.zhihu.com/?target=https%3A//mp.weixin.qq.com/s%3F__biz%3DMzAxMDkxODM1Ng%3D%3D%26mid%3D2247492889%26idx%3D2%26sn%3Dbc2ef17a3b96a257fb692f73338c6b0f%26scene%3D21%23wechat_redirect)

------

## 3. 利用SRR号批量下载FASTQ数据或SRA数据

在有了SRR号的情况下，还可以用下面的方法直接用脚本下载对应数据，而不用去EBI网站获取链接

### ① 批量下载FASTQ文件

观察FASTQ文件的Aspera下载地址:

```shell
era-fasp@fasp.sra.ebi.ac.uk:/vol1/fastq/SRR122/079/SRR12207279/SRR12207279_1.fastq.gz
```

可以发现其命名是很有规律的，很容易想到可以编写脚本批量循环下载数据，下面示范下载SRR12207279和SRR12207280这两个数据：

- 首先获取SRR号，导入到test路径下（test可替换成自己的项目路径）的idname文件，详细参见[RNA-seq入门实战（一）：上游数据下载、格式转化和质控清洗](https://link.zhihu.com/?target=https%3A//www.jianshu.com/p/313153805ac9)

![img](https://vip2.loli.io/2022/07/12/CzDXSdpk2mW6AE4.jpg)

- 这里要注意每行不能有空格，否则后续运行脚本可能会出错，用`cat -A`命令检查一下每行是否SRR开头、$结尾：

![img](https://vip2.loli.io/2022/07/12/zoKNiGW1Vw3a76O.jpg)

- 编辑、运行以下脚本，代码主要参考了 [（DownLoad）Aspera——ascp下载SRA数据 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/336794183) 并加入了样本编号为SRR+8位数的情况，且能自动根据SRR号的位数下载对应数据；运行前注意**修改密匙存放路径和自己的项目路径（替换test目录）**： （linux命令和R语言差别真是有点大，查了半天资料才搞明白linux中的逻辑判断语句的使用，艰难写出下面的代码.....）

```shell
vim ascp_fastq.sh  
```



```bash
### ascp_fastq.sh脚本内容
echo -e "\n\n\n 000# ascp Download FASTQ !!! \n\n\n"
date
## 目录设置
mkdir -p  ~/test/raw/fq/
cd  ~/test/raw/fq/
pwd

## 密匙路径
openssh=/home/gu/miniconda3/envs/share/etc/asperaweb_id_dsa.openssh

cat ~/test/idname | while read id
do
num=`echo $id | wc -m ` #这里注意，wc -m 会把每行结尾$也算为一个字符
echo "SRRnum+1 is $num" #因此SRRnum + 1才等于$num值
#如果样本编号为SRR+8位数 #
if [ $num -eq 12 ]
then
        echo "SRR + 8"
        x=$(echo $id | cut -b 1-6)
        y=$(echo $id | cut -b 10-11)
        echo "Downloading $id "
        ( ascp  -QT -l 500m -P33001  -k 1 -i $openssh \
                era-fasp@fasp.sra.ebi.ac.uk:vol1/fastq/$x/0$y/$id/   ./ & )
#如果样本编号为SRR+7位数 #
elif [ $num -eq 11 ]
then
        echo  "SRR + 7"
        x=$(echo $id | cut -b 1-6)
        y=$(echo $id | cut -b 10-10)
        echo "Downloading $id "
        ( ascp  -QT -l 500m -P33001  -k 1 -i $openssh \
                        era-fasp@fasp.sra.ebi.ac.uk:vol1/fastq/$x/00$y/$id/   ./ & )
#如果样本编号为SRR+6位数 #
elif [ $num -eq 10 ]
then
        echo  "SRR + 6"
        x=$(echo $id |cut -b 1-6)
        echo "Downloading $id "
        ( ascp  -QT -l 500m -P33001 -k 1 -i  $openssh \
                        era-fasp@fasp.sra.ebi.ac.uk:vol1/fastq/$x/$id/   ./ & )
fi
done
```



```bash
nohup bash ascp_fastq.sh >log_ascp_fastq 2>&1 &
```

- 运行结果如下，可以看到，在我的500M宽带下使用Aspera，**5.8G数据下载用时460s，平均速度约13mb/s**，下载过程中最高峰值速度可达到**近40mb/s**，这速度和prefetch相比可以说是飞快了！

![img](https://vip2.loli.io/2022/07/12/zLtnFwv1BfEkG45.jpg)



### ② 批量下载SRA文件（10x数据）

奇怪的是，如果是10x单细胞数据，ENA数据库中每个数据存放的却只有一个fastq.gz文件，如GSE117988项目，尝试使用aspera下载其中的 SRR7722941数据后也确实只有一个fastq.gz文件：

![img](https://vip2.loli.io/2022/07/12/3MSNAHYBzPUdjCb.jpg)

![img](https://vip2.loli.io/2022/07/12/94HfanRPMuiyZFo.jpg)



而**10x上游使用Cellranger软件需要3个fastq文件**...（参见生信技能树 [单细胞实战(二) cell ranger使用前注意事项 ](https://link.zhihu.com/?target=https%3A//mp.weixin.qq.com/s%3F__biz%3DMzI1Njk4ODE0MQ%3D%3D%26mid%3D2247484179%26idx%3D1%26sn%3Dfe84f5243a6021fe6afea128e3ac273a%26scene%3D21%23wechat_redirect)） 没办法，只能继续先下载SRA文件，再用**fasterq-dump（--split-files --include-technical 参数）或fastq-dump、 parallel-fastq-dump（--split-files参数）**转成三个fastq文件。（**如果有更好办法，欢迎评论区留言讨论**）

- 首先观察SRR7722941数据的SRA文件下载地址：

```bash
era-fasp@fasp.sra.ebi.ac.uk:/vol1/srr/SRR772/001/SRR7722941
```

- 可以发现其与FASTAQ文件下载地址相比，其实也就是其中的`fastq/`换成了`srr/`而已，套用上述①中脚本稍微改动一下即可：

```bash
echo -e "\n\n\n 000# ascp Download SRA !!! \n\n\n"
date
mkdir -p  ~/test/raw/sra/
cd  ~/test/raw/sra/
pwd

openssh=/home/gu/miniconda3/envs/share/etc/asperaweb_id_dsa.openssh

cat ~/test/idname | while read id
do
num=`echo $id | wc -m `
echo "SRRnum+1 is $num"
#如果样本编号为SRR+8位数 #
if [ $num -eq 12 ]
then
        echo "SRR + 8"
        x=$(echo $id | cut -b 1-6)
        y=$(echo $id | cut -b 10-11)
        echo "Downloading $id "
        ( ascp  -QT -l 500m -P33001  -k 1 -i $openssh \
                era-fasp@fasp.sra.ebi.ac.uk:vol1/srr/$x/0$y/$id/   ./ & )
#如果样本编号为SRR+7位数 #
elif [ $num -eq 11 ]
then
        echo  "SRR + 7"
        x=$(echo $id | cut -b 1-6)
        y=$(echo $id | cut -b 10-10)
        echo "Downloading $id "
        ( ascp  -QT -l 500m -P33001  -k 1 -i $openssh \
                        era-fasp@fasp.sra.ebi.ac.uk:vol1/srr/$x/00$y/$id/   ./ & )
#如果样本编号为SRR+6位数时 #
elif [ $num -eq 10 ]
then
        echo  "SRR + 6"
        x=$(echo $id |cut -b 1-6)
        echo "Downloading $id "
        ( ascp  -QT -l 500m -P33001 -k 1 -i  $openssh \
                        era-fasp@fasp.sra.ebi.ac.uk:vol1/srr/$x/$id/   ./ & )
fi
done
```

- 用上述脚本下载SRR7722941数据的SRA文件结果如下，**下载完成1016M数据，用时79s，平均速度13mb/s**：

![img](https://vip2.loli.io/2022/07/12/X2Ps4uq1SkW6lVd.jpg)



- 注意，这里下载的SRA文件没有.sra后缀，格式转换前需要先改名，再用parallel-fastq-dump转换为fastq.gz文件(否则parallel-fastq-dump会误认为要下载该SRR数据 )，最终生成3个FASTQ文件： （至于为什么选择parallel-fastq-dump呢，请参见[fastq-dump、fasterq-dump和parallel-fastq-dump处理SRA文件的速度比较 - 简书 (jianshu.com)](https://link.zhihu.com/?target=https%3A//www.jianshu.com/p/c0bc2e0b3beb)）

```bash
mv SRR7722941 SRR7722941.sra
parallel-fastq-dump -t 12  -O ./ --split-files --gzip -s SRR7722941.sra
```



![img](https://vip2.loli.io/2022/07/12/ce1JaAOhnrL7WIP.jpg)

- 最后再来比较一下相同网络条件下（500M宽带）使用prefetch命令的下载速度：同样**下载1016M数据，prefetch用时195min，平均速度只有90kb/s，花费的时间是Aspera的近150倍。。。。。。**（不过如果是后台同时下载多个文件，差距应该不至于那么大...）

```bash
time prefetch -p -O ./ SRR7722941
```



![img](https://vip2.loli.io/2022/07/12/wYdQsWe2nvX5Rko.jpg)



> 从以上可看出，相同网络状态下**aspera的下载速度与prefetch相比有了质的提升**，简直是碾压般的存在，因此在网络状况较好的情况下最好优先选择aspera下载吧

------

**参考资料**

[Downloading data from NCBI via the command line (ibm.com)](https://link.zhihu.com/?target=https%3A//www.ibm.com/support/pages/downloading-data-ncbi-command-line%23usage)

[使用ebi数据库直接下载fastq测序数据的改进脚本 (qq.com)](https://link.zhihu.com/?target=https%3A//mp.weixin.qq.com/s%3F__biz%3DMzAxMDkxODM1Ng%3D%3D%26mid%3D2247492889%26idx%3D2%26sn%3Dbc2ef17a3b96a257fb692f73338c6b0f%26scene%3D21%23wechat_redirect)

[Aspera下载安装使用 - 简书 (jianshu.com)](https://link.zhihu.com/?target=https%3A//www.jianshu.com/p/fed19a8821eb)

[从NCBI-SRA和EBI-ENA数据库下载数据 - 简书 (jianshu.com)](https://link.zhihu.com/?target=https%3A//www.jianshu.com/p/cf0a7b937413)

[Aspera：基因组数据高速下载利器，以NCBI和EBI数据下载为例 (qq.com)](https://link.zhihu.com/?target=https%3A//mp.weixin.qq.com/s%3F__biz%3DMzIxMjQxMDYxNA%3D%3D%26mid%3D2247483983%26idx%3D1%26sn%3Dd6478dcb0faa5ba03fcee176ee41b5be%26chksm%3D9747cbd6a03042c0e8c473bf18c945050fbfecb6ca3e0529517b3e69e630b62944811967dc02%26scene%3D178%26cur_album_id%3D1434291934281170945%23rd)

[（DownLoad）Aspera——ascp下载SRA数据 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/336794183)





本文转载自知乎用户嘿嘿嘿嘿哈：https://zhuanlan.zhihu.com/p/536866598

