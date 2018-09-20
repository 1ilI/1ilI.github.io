---
layout: post
title: "Xcode10 使用 SVN 做版本控制"
categories: Git SVN Xcode
tags: Git SVN Xcode
---

* content
{:toc}

![Xcode10](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-09/Xcode10-summary.png)
<p align="center">2018-09-17 更新的 Xcode 10 </p>






## 前言

这几天更新完 Xcode 后，发现的一些问题，像 `libstdc++` 没了的问题、 `info.plist` 重复的问题、`CocoaPods` 的问题，搜一搜都能解决，但是以前 Xcode 自带的 SVN 管理，现在全没了！没了！但凡使用 SVN 的，都不能直接使用 Xcode 进行 update、commit 等操作了，最最主要的文件比较的功能也没了，这样开发的时候都不方便看自己改动了啥，隐患很大啊。

另外在官网上看到 [Xcode 10 Release Notes](https://developer.apple.com/documentation/xcode_release_notes/xcode_10_release_notes?language=objc) 中，确定已经移除了对 SVN 的支持后，更感觉凉凉了 = =
![SVN-Removed](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-09/subversion-been-removed.png)


## 解决方案

事已至此，除非不更新 Xcode10，否则在 Xcode 上已经没法直接使用 SVN 进行版本管理了。


但，Xcode 支持 `Git` 啊，而且支持的非常好，所以我们能不能换个思路，用曲线救国的方式，把 SVN 转成 Git，然后在 Xcode 中直接把它当成 Git 用，不就好了嘛。

### git svn

Git 最为重要的特性之一是名为 `git svn` 的 Subversion 双向桥接工具。该工具把 Git 变成了 Subversion 服务的客户端，从而让你在本地享受到 Git 所有的功能，而后直接向 Subversion 服务器推送内容，仿佛在本地使用了 Subversion 客户端。也就是说，在其他人忍受古董的同时，你可以在本地享受分支合并，使暂存区域，衍合以及 单项挑拣等等。巴拉巴拉~~小魔~~…… 此段来源于 [Git 与 Subversion](https://git-scm.com/book/zh/v1/Git-%E4%B8%8E%E5%85%B6%E4%BB%96%E7%B3%BB%E7%BB%9F-Git-%E4%B8%8E-Subversion)



话不多说，直接上命令吧。

#### git svn clone

```bash
#git检出svn内容 （等效于svn checkout）
# -r 指定版本号，防止日志过多检出较慢 （HEAD是最新一次的版本）
git svn clone --username=用户名 SVN地址 -r HEAD

#例如在桌面检出SVN
cd ~/Desktop
git svn clone --username=1ilI https://1.2.3.4/svn/ -r 1234
```

#### git svn info

```bash
#svn 信息（等效于 svn info）
git svn info
```

#### git svn log
```bash
#查看日志（等效于svn log）
git svn log 
```

#### git svn rebase
```bash
#更新update (等效于 svn update)
git svn rebase
```

#### git svn dcommit
提交这步和 SVN 稍微有些不一样，分为两步，毕竟一个是集中式的一个是分布式的嘛。

首先提交代码 commit ，其实这个是本地操作；然后再 dcommit，这个才推到服务器，跟 git push 类似。

```bash
#代码提交分两步
#1、先 commit 提交（本地操作）
git commit -m "日志信息"
#2、提交至SVN
git svn dcommit
```

## Xcode 中的使用

当 clone 成功后，打开工程，修改过未提交的文件右边就会出来我们熟悉的 `M` ，然后左右两边版本对比也可以用了，`Source Control`里的 `commit` 也可以了，当然这个`commit`是本地的，提交到 SVN 还需要我们手动`dcommit`一下。

另外这次更新的 Xcode10 又有了新功能，本地修改的内容可以直接实时显示出来了，在行数左边会有蓝条，并且还可以高亮显示、Discard操作，我就想问这么好的功能为什么 SVN 没有！！

![modify-point](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-09/Xcode10-modify-point.png)

## 小知识

每次提交`dcommit`时，最好要`rebase`一下，和使用 SVN 类似，`commit`前先`update`一下。

另外，若有一次`git commit`后还未`dcommit`提交至SVN，然后修改了某些东西，这时候再`dcommit`就会失败，提示`XXX文件 needs update update-index --refresh: command returned error: 1`，解决方式如下：

1. 你可以把当前修改的 `git commit` 后，再 `dcommit` ，这样 SVN 会有两次你提交的记录。

2. 若当前修改的不想提交，只是想把上一次`commit`的给提交给SVN，可以用 `git stash` 暂存一下当前的修改，然后`dcommit`成功后再回到刚刚暂存的位置就可以了。

### git stash 
```bash
#暂时贮藏(使得working tree clean)
git stash

#查看贮藏的内容
git stash list
#例如
$ git stash list
> stash@{0}: WIP on master: 90d537f 日志2
> stash@{1}: WIP on master: 087e275 日志1

#启用贮藏的内容
#默认第一条
git stash apply
#可以指定
git stash apply stash@{1}

# 重新贮藏（取消当前的）
git stash pop

#移除贮藏（最新一条）
git stash drop

#移除指定贮藏
git stash drop stash@{1}
```
