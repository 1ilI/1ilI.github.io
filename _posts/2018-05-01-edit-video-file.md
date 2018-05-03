---
layout: post
title: "上百个视频片段一键合而为一"
categories: AppleScript Automator 
tags: AppleScript  Automator
---

* content
{:toc}

<div align="center">
<img alt="merge-result" src="https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-05/video-merge-result.gif"/>
<p>上百个视频片段快速合而为一</p>
</div>






## 起因
放假在家用腾讯视频看电影，想给小伙伴分享一下，结果是 VIP 才能观看的片源，心想那只能把视频缓存下来，然后再把文件发给他不就可以了，而且我也可以收藏片源到硬盘里。等我找到缓存文件后就呆了，每部影片都是由上百个视频片段组成，而且分布在不同的文件夹下，怎么把这些片段提取出来合成一个文件呢，就是这一简单的初始需求从而产生了这篇文章 @(￣-￣)@ 。。。。

## 分析

首先腾讯视频缓存的路径是
```
/Users/用户名/Library/Containers/com.tencent.tenvideo/Data/Library/Application Support/Download/
```
这里面除了 `ad` 文件夹外，每一个文件夹都对应着一部影片，同时片段的命名也挺有规则，像这样
<img alt="video-clips" src="https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-05/video-clips.png" width="60%"/>

那么现在的需求就变成
1. 把所有的 `*.ts` 文件找出来放在一个文件夹下
2. 把这个新文件夹下里的内容按顺序进行合并

针对1，想到用 `find` 命令，遍历查找文件夹及子文件夹下所有的 ts 文件，然后拷贝到新目录下
```bash
find 文件夹路径 -name *.ts |xargs -I {} cp {} 新目录路径
```

针对2，想到用 `cat` 命令，对文件进行追加合并，file1 后面追加 file2 ，file2 后面追加 file3 ，...
```bash
cat file1 file2 file3 ... >> 目标文件
```

## 实现
主要问题已能解决，然后就实现吧，再加一个在桌面下新建一个 `ts文件合并`，里面有 `原文件` 和 `合并文件` 两个文件夹，分别用来放初始的 ts 文件片段和最终的合并文件。<br/>
最后实现代码：
```bash
mkdir -p ~/Desktop/ts文件合并/原文件
mkdir -p ~/Desktop/ts文件合并/合并文件
find 文件夹路径 -name *.ts |xargs -I {} cp {} ~/Desktop/ts文件合并/原文件
cd ~/Desktop/ts文件合并/原文件
for file in `ls | sort -n`; do cat $file >> ~/Desktop/ts文件合并/合并文件/合并后的文件名.ts;done
```

这样只使一次或几次的，直接用这些命令就好了<br/>不过身为程序猿，当然要给它做成一次编译处处运行的形式啦～
![merge-1](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-05/merge-version1.png)
在文件夹上右键选择扩展服务，一键执行脚本，合并完成后再自动打开工作目录 ♪（ｖ＾＿＾）ｖ

## 优化
之前只考虑了基本流情况，其他异常情况也得加以考虑，比如在合并前检查 ts 片段的个数是否大于2个，大于才执行，另外打开工作文件夹，合并后才展示等等

最后上优化后的代码吧：
![merge-result](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-05/merge-result.jpg)

## 下载
已制成扩展服务，点击下载 [workflow 文件](https://raw.githubusercontent.com/1ilI/AppleScript/master/视频片段ts文件合并/视频文件 *.ts 合并.zip)，下载解压后安装服务即可。


