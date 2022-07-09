---
title: 植物RNA-seq转录组数据分析实例【拟南芥】
tags:
  - fastqc
  - cutadapt
  - hisat2
  - featureCounts
categories:
  - 生物信息学
  - 	RNA-seq转录组数据分析
date: 2022-07-05 09:32:06
---
**目的：**分析拟南芥Col-0在22℃和37℃ 30 min的差异表达基因。

**RNA-seq分析流程：**FastQC+trim-galore+hisat2+samtools+featureCounts+DESeq2

> FastQC进行质控，trim-galore去除测序数据接头和低质量测序数据，hisat2将测序数据比对到参考基因组，samtools将比对后的SAM文件转为二进制BAM文件并排序，featureCounts计算raw counts，DESeq2将raw couts标准化并分析差异表达基因。

**环境：**Ubuntu 22.04 LTS

<!-- more -->

# 1、生信环境配置和软件安装

```bash
# 首先安装miniconda，配置conda环境
# Miniconda的下载地址：https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/
wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-py39_4.12.0-Linux-x86_64.sh
bash Miniconda3-py39_4.12.0-Linux-x86_64.sh # 安装过程中根据提示输入enter或yes。出现软件协议的时候，按q退出，之后输入yes同意。
# 之后重启终端
conda -V # 查看是否安装成功
# 在用户根目录创建文件.condarc，设置conda镜像
touch .condarc
vim .condarc # 按enter进入编辑模式。
# 在.condarc中写入以下文件
show_channel_urls: true
channels:
  - conda-forge
  - bioconda
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/ # Anocanda清华镜像
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/msys2/
  - defaults
  - r
    
# 执行以下命令清除索引缓存，保证用的是镜像站提供的索引
conda clean -i
# 配置完毕，执行以下命令查看是否已经换源，可以看到已经更换为清华源了
conda config --show

conda create -n RNA-seq python=3.9 # 创建RNA-seq专门的环境，并指定python版本为3.9。-n参数表示环境名称。
conda activate RNA-seq # 激活RNA-seq环境。
# conda decativate 退出去当前环境。




# 使用conda安装生信软件
conda install mamba # 后续使用mamba安装生信软件，速度会很快。
mamba install fastqc
fastqc -v # 查看fastqc版本
mamba install cutadapt 
cutadapt -v
mamba install trim-galore # 需要先安装fastqc和cutadapt
trim_galore -v 
mamba install hisat2
hisat2 -v
mamba insatall samtools
samtools 
mamba install subread # featureCounts是subread的一个子集。
featureCounts
```

# 2、FastQC和trim-galore对测序数据进行质控

```bash
# 下载测序数据
# https://sra-explorer.info/ SRA-Explorer可以直接下载fastq格式的测序数据。也可以下载SRA格式的测序数据，再转为fastq格式，但纯属多此一举。
# 本次分析的数据来自SRP187923和SRP187905这两个数据集中的部分数据。
wget -i download.txt & # 将下载链接保存在download.txt，批量下载。&，表示在后台下载。还可以加一个nohup，这样使用服务器下载时即使关闭终端也可以继续下载
# nohup wget -i download.txt & 

# download.txt
ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR869/008/SRR8698458/SRR8698458_1.fastq.gz
ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR869/008/SRR8698458/SRR8698458_2.fastq.gz
ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR869/004/SRR8698834/SRR8698834_1.fastq.gz
ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR869/004/SRR8698834/SRR8698834_2.fastq.gz
ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR869/000/SRR8698840/SRR8698840_1.fastq.gz
ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR869/000/SRR8698840/SRR8698840_2.fastq.gz
ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR869/000/SRR8698460/SRR8698460_1.fastq.gz
ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR869/000/SRR8698460/SRR8698460_2.fastq.gz
ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR869/007/SRR8698847/SRR8698847_1.fastq.gz
ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR869/007/SRR8698847/SRR8698847_2.fastq.gz
ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR869/003/SRR8698473/SRR8698473_1.fastq.gz
ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR869/003/SRR8698473/SRR8698473_2.fastq.gz
ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR869/005/SRR8698475/SRR8698475_1.fastq.gz
ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR869/005/SRR8698475/SRR8698475_2.fastq.gz



# fastQC 质控
fastqc -t 1 -o /mnt/d/zhengxidatascholar/RNAseq/fastqc.result /mnt/d/zhengxidatascholar/RNAseq/fastq.rawdata/*.fastq.gz &
# -t表示线程，-o表示的是fastqc输出文件夹，最后是格式为fastq的原始测序文件。

# 使用multiqc来把所有的质量评估放在一起观察
multqc -o <out.path> *.fastqc.zip 

# trim-galore 去除测序接头和低质量reads。




```







# 参考资料

1. [高级生信系列课程：转录组数据分析](https://ke.qq.com/course/2993553)
1. [SRA数据几种常用的下载方法](https://www.jianshu.com/p/160144b64c93)
1. [Trim Galore ——自动检测adapter的质控软件](https://www.jianshu.com/p/7a3de6b8e503)

