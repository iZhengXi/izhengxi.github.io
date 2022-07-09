---
title: ChIP-seq数据分析【笔记】
tags:
  - ChIP-seq
categories:
  - 生物信息学
  - 	ChIP-seq数据分析
date: 2022-07-06 09:32:06
---
> 这是北大生信大佬孟浩巍生信课程[《高级生信系列课程：表观遗传学数据分析》](https://ke.qq.com/course/5409096)的学习笔记。

<!-- more -->

# 背景

ChIP-seq可以用来分析转录因子的结合位点和靶基因的全基因组鉴定。

# ChIP-seq数据分析流程

**ChIP-seq数据分析流程及用到的相关生信软件：**

- 原始测序文件的质控：FastQC
- 去除测序adapter序列：Cutadapt
- ChIP-seq的回帖：Bowtiew/BWA MEM
- BAM文件的排序，index建立：samtools
- BAM文件去重及MAPQ筛选：picard
- Call peak: MACS2
- peak区域的注释：Homer
- peak区域motif的发现：MEME/DREME/STREME



# ChIP-seq生信分析环境搭建

```bash
# Ubuntu 22.04 LTS
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

conda create -n ChIP-seq python=3.9 # 创建ChIP-seq专门的环境，并指定python版本为3.9。-n参数表示环境名称。
conda activate ChIP-seq # 激活ChIP-seq环境。
# conda decativate 退出去当前环境。
```

# ChIP-seq测序数据的回帖与质控

```bash
conda activate ChIP-seq
conda install mamba
mamba install fastqc
fastqc -v # 查看fastqc版本
mamba install cutadapt 
cutadapt -v 
# FastQC
fastq -o  FastQC_result -q -t 20 ./raw.fastq/chipseq_*.gz 
# -o 输出文件夹，需要提前建好；-q 安静模式运行；-t 多少个线程运行；最后就是原始的fastq测序文件。

# cutadapt
cutadapt -j 24 --times 1 -e 0.1 -o 3 --quality-cutoff 25 -m 55 \ # \是换行符；-j 24，用6个核心；--times 1，一条序列的adapter只 去一次；-e 0.1 是指允许10%的mismatch；-o 3，序列有3 bp和adapter匹配就去掉；--quality-cutoff 25，去除低质量序列；-m 55，去除完低质量的序列后，最后的序列大于等于55 bp，则保留下来。
-a AGATCGGAAGAGCACACGTCTGAACTCCAGTCA \ # adapter trimming reads1
-A AGATCGGAAGAGCGTCGTGTAGGGAAAGAGTGT \ # adapter trimming reads2
-o ./test_R1_cutadapt.fq.gz \ # trim完的R1。
-p ./test_R2_cutadapt.fq.gz \ # trim完的R2。
./test_R1.fq.gz\ # 需要trim的源文件。
./test_R2.fq.gz > fix.fastq/test_cutadapt.log 2>&1 & #>是输出，这一步是输出log文件。2输出到1。2是标准错误，1是标准输出。

# 怎么知道adapter triming reads1/2是否正确呢？
zcat test_R1.fq.gz | grep AGATCGGAAGAGCACACGTCTGAACTCCAGTCA # 查看adapter序列是否在fastq序列里。按Esc退出。

# bowtie2 index
bowtie2-build -t 6 ref_hg38.fa ref_hg38.fa > bt2_build.log 2>&1 & 

# bowtie2 alignment
bowtie2 -p 40 -x reference/hg38_chr1_chr2.fa -1 fix.fatq/test_ctcf_cutadapt_R1.fq.gz -2 fix.fatq/test_ctcf_cutadapt_R1.fq.gz -S bam.bt2/test_ctcf.sam --no-unal > bowtie2_ctcf.log 2>&1 &
# -p 40，40个线程；-x 参考基因组；-1 -2，cutadpt后的序列；-S 输出的SAM文件；--no-unal, 清除没有比对上的序列；

less bam.bt2/test_ctcf.sam # 查看SAM文件。

# samtools
samtools view -h -b -f 3 -F 12 -q 20 -F 256 test_ctcf.sam > test_ctcf.bam # 过滤。SAM
samtools sort -O BAM -m 2G -@20 -o test_ctcf.bam & # 排序。-m，每个线程2G内存；-@ 20 ，20个线程。-O 输出为BAM格式；-o 输出文件。
```

# BAM文件去重

使用软件：[picard](https://broadinstitute.github.io/picard/)

![image-20220708214310728](https://vip2.loli.io/2022/07/08/wQ2u1ctFHiIrPeJ.png)

点击`Latest Jar Release`，下载最新版本的`picard.jar`

> 使用picard需要提前安装好java环境。

```bash
sudo apt update
sudo apt install openjdk-11-jdk # 安装OpenJDK 11 JDK 软件包
# JRE 被包含在 JDK 软件包中。如果你仅仅需要 JRE，安装openjdk-11-jre软件包。最小 Java 运行环境，安装openjdk-11-jdk-headless软件包。
```

**BAM文件去重：**

```bash
java -jar picard.jar -h # 查看picard运行命令。帮助。
java -Xms20g -Xms20g -xx:ParallelGCThreads=4 \ # -Xms20g，用20G内存。4个线程(总共4个线程)。
-jar ./software/picard.jar \ # picard软件路径。
I={input} O={output_1} M={output_matrix} ASO=coordinate REMOVE_DUPLICATES=true 2>{log} # I就是输入文件；O就是输出文件。M相当于是日志。


java -Xms20g -Xms20g -xx:ParallelGCThreads=4 \
-jar ./software/picard.jar \ 
I=bam.bt2/test_ctcf.sort.bam O=bam.bt2/test_ctcf.sort.rmdup.bam M=bam.bt2/test_ctcf.sort.rmdup.matrix ASO=coordinate REMOVE_DUPLICATES=true 2>bam.bt2/test_ctcf.sort.rmdup.log &

samtools view test_ctcf.sort.rmdup.bam | less -S # 查看去重后的文件。文件会变大，因为会把一些去重后的信息标记在BAM文件里。
```

# 富集峰的鉴定（peak calling）

使用软件：MACS(Model-based Analysis of ChIP-Seq)

```bash
mamba install macs2
macs2 callpeak -h # 查看macs2 callpeak相关参数。
macs2 callpeak -t ctcf_chr1_chr2.bam -c input_chr1_chr2.bam -f BAMPE -g hs --outdir ../macs2_ctcf_chr1_chr2 -n ctcf_test -q 0.05 -m 3 50 &
```

peak结果包含三个文件：

- ctcf_test_peaks.narrowPeak
- ctcf_test_peaks.xml
- ctcf_test_summits.bed



# 使用homer软件对peak进行注释

软件：[homer](http://homer.ucsd.edu/homer/)

```bash
# homer的安装和使用
# 安装方式1
wget -c http://homer.ucsd.edu/homer/configureHomer.pl # weget -c是断点续传命令。
perl configureHomer.pl # 直接这样就可以运行了。

# 安装方式2 http://homer.ucsd.edu/homer/introduction/install.html
# 使用conda进行安装
conda install wget samtools r-essentials bioconductor-deseq2 bioconductor-edger
# 使用coonfigureHomerl.pl进行安装
perl /Users/chucknorris/homer/configureHomer.pl -install

# 查看homer已有的注释信息
perl configureHomer.pl -list

# 安装某一个注释
perl configureHomer.pl -install hg38

wc -l chipseq_ctcf_hg38_ENCFF151CRB.bed # 查看bed文件有多少行

annotatePeaks.pl chipseq_ctcf_hg38_ENCFF151CRB.bed hg38 -annStats test.log > test_ann.tsv # homer注释命令。

# homer注释结果
ENCFF151CRB.ann.tsv # 注释结果文件
ann_stats.log # peak分布统计情况
```



# 寻找富集的motif

软件：MEME

```bash
mamba install meme
```

> motif的搜索算法在1994年左右就比较成熟，主要是EM算法和Gibson sampling，可以去看刘小乐老师主讲的*[Harvard STAT115](https://canvas.harvard.edu/courses/39391)*课程。

**motif鉴定的具体步骤：**

选择区域：

- ChIP-seq peak
- 基因编辑位点附近
- 修饰位点附近

提取区域对应的序列：

- bedtools

根据序列运行软件。



**meme：**速度较慢，可以搜索长motif

**dreme/streme：**速度较快，可以通过设置搜索长motif

```bash
wc -l chipseq_ctcf_hg38_ENCFF151CRB.bed # 查看有多少行，也就是有多少个peak
head -n 3000 chipseq_ctcf_hg38_ENCFF151CRB.bed > head_3000_ENCFF151CRB.bed # 提取了前3000行。
less -S head_3000_ENCFF151CRB.bed # 查看head_3000_ENCFF151CRB.bed内容。

bedtools getfasta  -fi ./hg38_only_chromosome.fa -bed head_3000_ENCFF151CRB.bed > head_3000.fa # fasta序列提取。

meme -oc test_motif.meme/ -dna -maxw 30 -nsites 10 head_3000.fa # -oc，输出文件夹；

streme -oc test_out.streme -p head_3000.fa --dna --maxw 30 --nmotif 15 
# streme的结果中，E-value值的意思是，自然情况下出现该motif的概率。
```

# 差异peak的寻找

**思路：**

- 直接计算RPKM，计算fold change直接进行筛选；
- 使用类似DESeq2、edgeR的工具进行差异分析。
  - 可以提供一个所谓的统计检验值；
  - 潜在的基本假设可能并不相符。



# 参考资料

1. [如何在 Ubuntu 20.04 上安装 Java](https://zhuanlan.zhihu.com/p/137114682)
2. [MACS2 Call Peak 参数详细学习](https://www.jianshu.com/p/53d97099b739)
3. [ChIP-seq技术在植物研究领域中的应用](https://www.yuque.com/izhengxi/ngs/zz4h9g)

