---
title: sleuth基于TPM值行差异表达基因分析
tags:
  - sleuth
categories:
  - 生物信息学
  - 	RNA-seq转录组数据分析
date: 2022-07-11 09:32:06
---

kallisto等alignment-free转录本定量软件，会给出`TPM`值的定量结果。基于这种类型的结果进行差异分析时，有两种策略可以选择。

第一种是采用`tximport`R包，将结果导入到`DESeq2`种进行分析；第二种是直接采用`sleuth`R包进行差异分析。本章主要介绍`sleuth`的使用。

这个包的源代码存放在github上，链接如下

> https://github.com/pachterlab/sleuth

github上的R包其安装方式比较特殊, 具体过程如下

```R
source("http://bioconductor.org/biocLite.R")
biocLite("rhdf5")
library(devtools)
install_github("pachterlab/sleuth")
```

首先从Bioconductor上安装依赖的`rhdf5`包，因为kallisto的定量结果为HDF5格式，这个R包用来读取数据，然后采用`devtools`这个R包，自动从github的源代码进行安装。

所有差异分析需要的都是定量结果和样本分组这两个基本元素，只不过不同的R包要求的格式不同。在`sleuth`中，将这两种信息存储在一个三列的数据框中，示例如下

```R
> s2c
    samples   group              paths
1 control-1 control kallisto/control-1
2 control-2 control kallisto/control-2
3 control-3 control kallisto/control-3
4    case-1    case    kallisto/case-1
5    case-2    case    kallisto/case-2
6    case-3    case    kallisto/case-3
```

第一列为样本名称，第二列为样本对应的分组信息，第三列为每个样本kallisto定量结果的文件夹。通过这样的一个数据框，就包含了差异分析所需的所有信息。

假定有6个样本，分成control，case 两组， 每组3个生物学重复，可以通过以下代码构建上述的数据框

```R
samples = c(
"control-1",
"control-2",
"control-3",
"case-1",
"case-2",
"case-3")

s2c <- data.frame(
samples = samples,
group   = rep(c("control", "case"), each = 3),
paths   = paste("kallisto", samples, sep = "/")
)
```

上述代码要求将所有样本的定量结果放在同一个文件夹下，目录结构如下

```R
kallisto/
├── control-1
├── control-2
├── control-3
├── case-1
├── case-2
└── case-3
```

上述数据框准备好之后，就可以读取数据进行差异分析了，完整的代码如下：

```R
library(sleuth)
so <- sleuth_prep(s2c, extra_bootstrap_summary = TRUE)
so <- sleuth_fit(so, ~condition, 'full')
so <- sleuth_fit(so, ~1, 'reduced')
so <- sleuth_lrt(so, 'reduced', 'full')
sleuth_table <- sleuth_results(so, 'reduced:full', 'lrt', show_all = FALSE)
```



本文转载自：https://www.yisu.com/zixun/560403.html

原链接：https://my.oschina.net/u/4580290/blog/4620668