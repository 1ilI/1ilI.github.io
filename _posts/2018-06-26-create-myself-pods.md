---
layout: post
title: "利用 CocoaPods 创建一个属于自己的 Pod"
categories: iOS CocoaPods
tags: iOS CocoaPods
---

* content
{:toc}

![search-pods](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-06/search-pods.gif)
<p align="center">在 cocoapods 中搜索 AFNetworking </p>






在开发中我们经常会使用到 CocoaPods 来导入第三方库，相比手动集成，一句 pod install 命令，即可方便快速的把第三方框架集成到我们自己的项目中，这也是 CocoaPods 如此火的原因。

当有一天，我们也想创建自己的 Pod，把自己写的代码开源出去，让别人也能用 pod install 来安装我们自己的库时，又该怎么办呢？

下面让我们一起跟着步骤来创建一个属于自己的 Pod 吧！ 😎

## 目录结构

`Y_ShowAlert` 文件夹里是将要做成 pod 的源文件，`Y_ShowAlertExample` 是样例工程。
<div align="center"> <img alt="show-me-code" src="https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-06/directory-structure.jpg"/> </div>

## 生成 podspec 文件

```bash
#生成 podspec 文件，create 后面填写要创建的 pod 名字
pod spec create Y_ShowAlert
```

终端命令切换到当前工程目录，然后输入以上命令，即可在工程目录中生成一个 `Y_ShowAlert.podspec` 文件。

使用 Sublime Text 打开该文件，可以看到里面的注释已经很详细了。
![podspec-file](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-06/podspec-file.png)

现在这个我就用了这些，其他的根据不同场景，填写对应字段
```rb
Pod::Spec.new do |s|
  #名称
  s.name         = "Y_ShowAlert"
  #版本
  s.version      = "0.0.1"
  #简介
  s.summary      = "对 UIAlertController 的扩展，快速创建与展示 AlertController"
  #详介
  s.description  = <<-DESC
                   快速创建与展示 AlertController，包括含有 TextField 的 Alert
                   DESC
  #首页
  s.homepage     = "https://github.com/1ilI/Y_ShowAlert"
  #截图
  # s.screenshots  = "www.example.com/screenshots_1.gif", "www.example.com/screenshots_2.gif"
  #开源协议
  s.license      = { :type => "MIT", :file => "LICENSE" }
  #作者信息
  s.author             = { "1ilI" => "1ilI" }
  #iOS的开发版本
  s.ios.deployment_target = "9.0"
  #源码地址
  s.source       = { :git => "https://github.com/1ilI/Y_ShowAlert.git", :tag => "#{s.version}" }
  #源文件所在文件夹，会匹配到该文件夹下所有的 .h、.m文件
  s.source_files  = "Y_ShowAlert", "Y_ShowAlert/**/*.{h,m}"
  #依赖的framework
  s.framework  = "UIKit"
end
```

## 上传至 GitHub，打上标签
把所有的文件上传到 GitHub ，然后打上 tag，要求和 `podspec` 文件里的 `s.version` 一致。也就是说每次更新版本，上传至 GitHub 时，对应的 `s.version` 也要改，和 tag 一样。

命令操作或是 Sourcetree 图形界面操作都一样的，当然这一步也可于生成 `podspec` 文件之前操作。

## 验证 podspec 文件

```bash
#验证 podspec 文件
pod spec lint 生成时填写的名字.podspec
```

像这样有 `***.podspec passed validation` 字段就通过了

![podspec-passed](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-06/podspec-passed.jpg)

## 发布自己的 Pod

### trunk register
在第一次发布前需要注册 Trunk ，以后就不需要了，终端输入下面命令即可

```bash
#-verbose 啰嗦模式，输出详细信息，可略 
pod trunk register 邮箱地址 '用户名' -verbose
```

当然，你也可以通过网页注册，如果你打的开的话，地址：[https://trunk.cocoapods.org/claims/new](https://trunk.cocoapods.org/claims/new)

随后你就会在你的邮箱里收到一封由 cocoapods 发给你的验证注册邮件。
![verify-trunk](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-06/verify-trunk.png)

邮件里的链接还不能点😕 ，直接复制链接粘贴到浏览器打开就可以完成注册了。

要是想要验证，可以通过 `pod trunk me` 来验证。

### trunk push

```bash
#上传 spec 文件至 trunk
pod trunk push
```

这会再次检验你的 podspec 文件，然后上传至 trunk，出现 🎉  Congrats 像以下这样的就成功了。 
![push-success](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-06/push-success.jpg)

这时候你就可以 ~~👍Tell your friends!~~  `pod search` 了

如果是 search 时遇到搜索不到，像这样的 
```bash
[!] Unable to find a pod with name, author, summary, or description matching `Y_ShowAlert`
```

可以先把位于 `~/Library/Caches/CocoaPods/search_index.json` 的搜索缓存删除，然后再重新 search 一下就好了。
![push-success](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-06/pod-search.jpg)
