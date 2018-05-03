---
layout: post
title: "巧用 GitHub 当图床，一键上传资源，一键获取链接地址"
categories: AppleScript Automator
tags: GitHub AppleScript Automator
---

* content
{:toc}

运用 AppleScript 和 Automator ，编写脚本，做成系统服务，把 GitHub 当图床，实现右键一键上传资源，一键获取链接地址～







## 分析 GitHub 返回的地址

在之前码的字中选择了 GitHub 当图床，但使用起来总觉得不方便，不像某些工具那样，可以很方便的上传、获取链接。然后搜了一圈，好像也没有什么亮眼的解决方案。

对着已经上传到 GitHub 上的图片，和手动获取到的链接地址（上一篇有介绍 → →）

>https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-04/right-click.png

看起来好像有规律的样子， 这个 `raw.githubusercontent.com` 应该是固定的，`1ilI` 对应自己的账号，`1ilI.github.io` 是对应博客的仓库，`master` 就不用说了，最后 `resource/2018-04/right-click.png` 这一串对应我本地仓库的目录就是这样的。

那么是不是说我上传成功后的资源，它的链接地址基本上就是按照这样的格式进行命名的呢？试了之后，果然是这样，那就好办了，上传完图片后再按这样的格式拼这一串地址不就行了嘛。

## AppleScript 脚本编辑器

首先想到 AppleScript ，先试着用它一点点写吧

<img alt="applescript-logo" src="https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-04/applescript-logo.png" width="150px">

我想到的主要有以下步骤：

1. 获取要上传的文件及其路径
2. 将其拷贝到 GitHub 仓库 resuorce 目录
3. git 命令 add 、commit 、push 上传至线上
4. 根据本地目录位置拼接链接字符串
5. 把拼接好的字符串复制到剪切板，方便使用

上传的时候因为想对文件进行分类，不然太多的话，到时候找起来也不方便，故以时间进行分类。先自动获取当前月份，然后以 `年-月` 作为目录，把文件都放在这些目录下。例如 `2018-04` 这里全是4月份的上传的资源。（强行加需求= =）

<div align="center"> <img alt="show-me-code" src="https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-04/show-me-code.png"/> </div>

```applescript
--设置GitHub仓库对应的本地resource目录
set resourcePath to "/Users/yue/Desktop/GitHub/1ilI/resource"

--获取要上传文件的路径
choose file with prompt "请选择要上传的文件"
set uploadfile to result
set uploadPath to POSIX path of (uploadfile) as string

--当前时间
set theDate to current date
set yearString to year of theDate as string
set monthNum to month of theDate as integer
if monthNum < 10 then
	set monthString to ("0" & (monthNum as string))
else
	set monthString to (monthNum as string)
end if

--获取当前时间（年-月）
set currentDatePath to (yearString & "-" & monthString)
--最后文件复制到的路径
set copyedPath to (resourcePath & "/" & currentDatePath & "/")

--执行终端命令
tell application "Terminal"
	set windowA to do script "mkdir -p " & copyedPath & " && cp " & uploadPath & " $_ " & "
" & "cd " & resourcePath & "
" & "git add ." & "
" & "git commit -m 'add resource' " & "
" & "git push origin master"
end tell

tell application "Finder"
	--获取文件名（会有后缀名的）
	set uploadFileName to (name of uploadfile)
end tell

set sourceUrl to ("https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/" & currentDatePath & "/" & uploadFileName)
display dialog "上传成功后获取到的链接" default answer sourceUrl buttons {"复制", "关闭"} default button 1 with title "提示" with icon note

if button returned of result = "复制" then
    --复制到剪切板
	set the clipboard to (text returned of result) as string
end if

```

运行起来是这样子的

![demo](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-04/applescript-upload-demo.gif)

点击选择完图片后，就自动拷贝、上传，以对话框的形势展示地址，因为上传需要时间，我命令可能弄的也不怎么好，就直接在终端显示了，最后文件还没上传完呢，链接地址就好了 → →


## Automator 系统内置的神器

不知道 Automator <img alt="automator-logo" src="https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-04/automator-logo.png" width="100px" align="bottom"/> 在还没汉化名称前，你们叫它什么，看过有人叫它 扛炮 ，因为其图标就是一个机器人扛着一个炮筒嘛😂  来上一张高清写真大图

![高清写真图](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-04/Automator.png)

----
### 一键上传至GitHub
打开 `自动操作`，选取`服务`类型的，然后选择 `实用工具` -> `运行AppleScript` ，在 `服务收到选定的` 那一项，选择 `文件或文件夹` ，最后把之前写的脚本复制过来，稍稍修改点东西，这样就可以了。

![automator-demo](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-04/automator-upload-demo.png)

保存后在 `系统偏好设置` 里找到 `键盘` ，选取 `快捷键` ，选择 `服务` ，看看你刚刚保存的名字前面是不是打着 ☑️ ，这样就OK啦。

![automator-service-open](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-04/automator-service-open.png)

随便找一张图片，按右键，选择 `服务` ，找到刚刚命名的服务，一键上传至GitHub，这样就完成了～

![right-click-service](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-04/right-click-upload.png)

![result](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-04/get-link-url.png)

### 一键获取链接地址

对于已上传过的图片，再想引用的时候，又需要这个链接地址了，还是按照之前的规则，根据本地目录拼一下，做成服务，一键获取。

直接 show me the code

```applescript
on run {input, parameters}
	-----------------------在此设置对应信息-----------------------
	--GitHub仓库中图片对应的地址 前缀
	set baseUrl to "https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/"
	-----------------------------------------------------------------

	--获取选择文件的路径
	set filePath to (item 1 of input) as alias	
	tell application "Finder"
		--获取文件的名字（会有后缀名的）
		set uploadFileName to (name of filePath)
	end tell
	
	--获取当前时间
	set theDate to current date
	set yearString to year of theDate as string
	set monthNum to month of theDate as integer
	--若当前月份小于10月，则在月份前加个0，例 09
	if monthNum < 10 then
		set monthString to ("0" & (monthNum as string))
	else
		set monthString to (monthNum as string)
	end if
	--获取当前时间，例 2018-04
	set currentDatePath to (yearString & "-" & monthString)
	
	--最终获取文件对应GitHub上的链接
	set sourceUrl to (baseUrl & currentDatePath & "/" & uploadFileName)
	
	--复制到剪切板
	set the clipboard to sourceUrl as string
	
	return input
end run
```

同样方式，做成服务

![right-click-geturl](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-04/right-click-geturl.png)

另外，这种方式只能在 GitHub 线上仓库对应本地的目录下才有用，在其他位置也有这个服务，但肯定是不能用的啦😛

![get-uploaded-link](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-04/get-uploaded-link.png)

因为这个确定是要获取到链接的，所以就直接复制到剪切板了，不需 UI 展示，方便使用。


## 小结

### 文件操作命令

复制文件到某目录，若目标目录不存在，则先创建目录，然后再复制。

cp 和 mkdir 合用

```bash
# "$_" 为前面命令的返回值，也就是新建的目录
mkdir -p tagetFolderPath && cp filePath "$_"
```

### AppleScript 复制到剪切板

```applescript
set the clipboard to copyString as string
```

----

最后这些小功能写成服务，集成在系统右键中，真的是很方便有木有，而且有这个 workflow 文件，在其他设备上或者发给别人，点击安装就能用，这个真的很nice～

另附本文的两个 workflow 下载地址

* [一键上传文件](https://raw.githubusercontent.com/1ilI/AppleScript/master/一键上传至GitHub/一键上传GitHub.zip)
* [一键获取链接](https://github.com/1ilI/AppleScript/raw/master/获取GitHub仓库文件链接/获取文件对应GitHub上的链接.zip)


更多请看 [https://github.com/1ilI/AppleScript](https://github.com/1ilI/AppleScript)