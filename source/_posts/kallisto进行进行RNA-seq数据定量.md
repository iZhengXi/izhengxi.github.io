---
title: kallisto进行进行RNA-seq数据定量
tags:
  - kallisto
categories:
  - 生物信息学
  - RNA-seq转录组数据分析
abbrlink: 9eb2d377
date: 2022-07-10 09:32:06
---

kallisto是2016年发表于Nature biotechnology《Near-optimal probabilistic RNA-seq quantification》的软件。该算法认为，将fastq文件的reads比对到参考基因组的具体位置，然后根据位置信息进行计数没有实质性意义的，而该算法采用的是伪比对的方式：即直接将fq文件的reads比对参考转录组上并且直接计数。

kallisto的使用手册：https://pachterlab.github.io/kallisto/manual



<!-- more -->



```bash
mamba install kallisto
# kallisto index
kallisto index -i hg38.idx Homo_sapiens.GRCh38.cdna.all.fa

# kallisto quant
# 双端测序
kallisto quant -i hg38.idx -o output test.R1.fq.gz test.R2.fq.gz
# 单端测序
kallisto quant -i hg38.idx -o output --single -l 200 -s 20 test1.fq.gz
# --single 参数指定为单端测序数据
# -l 估计的reads平均长度
# -s 估计的reads片段长度标准差
# -l 和 -s 参数值可以通过类似Agilent Bioanalyzer软件估算
```
