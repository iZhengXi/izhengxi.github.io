---
title: hisa2安装报错，提示：`GLIBCXX_3.4.26' not found
tags:
  - HISAT2
  - RNA-seq
categories:
  - 生物信息学
  - RNA-seq转录组数据分析
date: 2022-06-05 10:32:06
---

更新libstdc++.so版本即可。

<!-- more -->

```bash
# 报错提示
(NGS) ubuntu@VM-20-10-ubuntu:~$ hisat2 -h
/home/ubuntu/anaconda3/envs/NGS/bin/hisat2-align-s: /home/ubuntu/anaconda3/envs/NGS/bin/../lib/libstdc++.so.6: version `GLIBCXX_3.4.26' not found (required by /home/ubuntu/anaconda3/envs/NGS/bin/hisat2-align-s)
(ERR): Description of arguments failed!
Exiting now ...

cd /home/ubuntu/anaconda3/envs/NGS/lib/
strings libstdc++.so.6 | grep GLIBCXX # 查看GLIBCXX版本，结果发现确实没有GLIBCXX_3.4.26

因此，只需要更新libstdc++.so，下载一个含有该库的版本即可。到时候删除原文件，增加新的软链接即可。

sudo wget http://www.vuln.cn/wp-content/uploads/2019/08/libstdc.so_.6.0.26.zip
unzip libstdc.so_.6.0.26.zip # 解压。
sudo rm libstdc++.so.6 # 建议删除前备份。
sudo ln -s libstdc++.so.6.0.26 libstdc++.so.6 # 链接新版本。


strings libstdc++.so.6 | grep GLIBCXX # 查看是否包含GLIBCXX_3.4.26

GLIBCXX_3.4
GLIBCXX_3.4.1
GLIBCXX_3.4.2
GLIBCXX_3.4.3
GLIBCXX_3.4.4
GLIBCXX_3.4.5
GLIBCXX_3.4.6
GLIBCXX_3.4.7
GLIBCXX_3.4.8
GLIBCXX_3.4.9
GLIBCXX_3.4.10
GLIBCXX_3.4.11
GLIBCXX_3.4.12
GLIBCXX_3.4.13
GLIBCXX_3.4.14
GLIBCXX_3.4.15
GLIBCXX_3.4.16
GLIBCXX_3.4.17
GLIBCXX_3.4.18
GLIBCXX_3.4.19
GLIBCXX_3.4.20


```

<a name="CA3dC"></a>

# 参考资料

1. [version `GLIBCXX_3.4.20' not found 解决方法](https://www.jianshu.com/p/050b2b777b9d)
1. [libstdc.so_.6.0.26.zip](https://www.yuque.com/attachments/yuque/0/2022/zip/641947/1654431867662-cf156f39-b614-4f60-bc92-1ad4098946f9.zip?_lake_card=%7B%22src%22%3A%22https%3A%2F%2Fwww.yuque.com%2Fattachments%2Fyuque%2F0%2F2022%2Fzip%2F641947%2F1654431867662-cf156f39-b614-4f60-bc92-1ad4098946f9.zip%22%2C%22name%22%3A%22libstdc.so_.6.0.26.zip%22%2C%22size%22%3A4165669%2C%22type%22%3A%22application%2Fx-zip-compressed%22%2C%22ext%22%3A%22zip%22%2C%22source%22%3A%22%22%2C%22status%22%3A%22done%22%2C%22download%22%3Atrue%2C%22taskId%22%3A%22u08f7d87f-c2e1-4074-ac01-892dace24a9%22%2C%22taskType%22%3A%22upload%22%2C%22__spacing%22%3A%22both%22%2C%22id%22%3A%22u0509d202%22%2C%22margin%22%3A%7B%22top%22%3Atrue%2C%22bottom%22%3Atrue%7D%2C%22card%22%3A%22file%22%7D)

