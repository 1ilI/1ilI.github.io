---
layout: post
title: "Mac 终端的字体和配色"
categories: Terminal
tags: Terminal
---

* content
{:toc}

<div align="center"> <img alt="MyTerminal" src="https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-04/terminal-my.png"/> <p>一个好看的终端是键入命令行的开始</p> </div>






<br/>
<br/>
<br/>
<br/>
<br/>

--------
<h3>当你看到这样的终端时，是否觉得很乏味，还剩下多少敲命令行的欲望？</h3>
![Terminal-origin](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-04/terminal-origin.png)


## 终端主题配置

在终端界面 `右键`，选择 `显示检查器` ，在 `描述文件` 中，选择自己喜欢的主题，这个基本都会弄。

双击一个主题后会出现详细的信息，然后具体设置里面的内容，比如这样的，以下是我的设置
![MyTerminalSetting](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-04/terminal-setting.jpg)

最后设置完了效果是这样的
![MyTerminal1](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-04/terminal-homebrew.png)

最重要的一步来了，要把刚才的配置作为默认配置，不然你退出终端再次打开后就会发现又回到了初始的状态
<img alt="SaveSetting" src="https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-04/terminal-save-seeting.png" width="50%"/>

## 终端高亮显示

在用户家目录下创建一个名为 `.bash_profile` 的文件，里面的内容即为配置信息，具体如下

1. 打开终端输入：

```bash
vim ~/.bash_profile
```

2. 键入内容：

```bash
# for color
export CLICOLOR=1
# \h:\W \u\$
export PS1='\[\033[01;33m\]\u@\h\[\033[01;31m\] \W\$\[\033[00m\] '
```

退出编辑模式，wq 保存退出，重启终端，高亮显示就出来了。


## 配置命令说明

### CLICOLOR
`export CLICOLOR=1` 这个是对终端的输出信息，进行上色处理。

这样的，`ls -a`后，终端的输出，就有了颜色，不同的颜色代表它们不同的属性
![terminal-color](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-04/terminal-color.png)

另外，可以用 `LSCOLORS` 来自定义配置颜色。

```bash
export LSCOLORS=gxfxaxdxcxegedabagacad  
```

解释一下，LSCOLORS的值中每两个字母为一组，分别设置某个文件类型的文字颜色和背景颜色。比如 gx 对应的是 directory 的颜色， LSCOLORS中一共11组颜色设置，按照先后顺序，分别对以下的文件类型进行设置：

>
directory  
symbolic link  
socket  
pipe  
executable  
block special  
character special  
executable with setuid bit set  
executable with setgid bit set  
directory writable to others, with sticky bit  
directory writable to others, without sticky bit  

颜色表在这：
>
a 黑色  
b 红色  
c 绿色  
d 棕色  
e 蓝色  
f 洋红色  
g 青色  
h 浅灰色  
A 黑色粗体  
B 红色粗体  
C 绿色粗体  
D 棕色粗体  
E 蓝色粗体  
F 洋红色粗体  
G 青色粗体  
H 浅灰色粗体  
x 系统默认颜色

### PS1

`PS1='\[\033[01;33m\]\u@\h\[\033[01;31m\] \W\$\[\033[00m\] '`

这里面的值是使用了反斜杠转义的字符序列，设定后 bash 会用对应的值进行替换。例如下：

```
\u 用户名
\h 主机名第一部分
\H 主机名全称
\w 当前工作目录（如 “/home/username/mywork”）
\W 当前工作目录的“基名 (basename)”（如 “mywork”)
\t 24 小时制时间
\T 12 小时制时间
\@ 带有 am/pm 的 12 小时制时间
\d “Sat Dec 18″ 格式的日期
\s shell 的名称（如 “bash”)
\v bash 的版本（如 2.04）
\V Bash 版本（包括补丁级别）
\n 换行符
\r 回车符
\\ 反斜杠
\a ASCII 响铃字符（也可以键入 07）
\e ASCII 转义字符（也可以键入 33)
\[ 这个序列应该出现在不移动光标的字符序列（如颜色转义序列）之前。它使 bash 能够正确计算自动换行。
\] 这个序列应该出现在非打印字符序列之后。
```

然后 `\[\033[01;33m\]` 这个是配置显示效果及颜色的， 以 `\[` 开始，以 `\]` 结束， 其中 `\033` 可以换成 `\e`， `01`代表显示方式，`33`代表显示颜色。

下面是可选的显示方式：

|显示效果|代码值|
| :- | :-: | 
|关闭效果|00|
|高亮显示|01|
|下划线|04|
|闪烁|05|
|反白显示|07|
|不可见|08|

以及颜色值：

|前景色|背景色|颜色|
| :-: | :-: | :-: |
|30 | 40 |黑色|
|31 | 41 |红色|
|32 | 42 |绿色|
|33 | 43 |黄色|
|34 | 44 |蓝色|
|35 | 45 |紫红色|
|36 | 46 |青蓝色|
|37 | 47 |白色|

最后用这个 `\[\033[00m\]`结尾，关闭之后手动输入命令的显示效果及颜色。
<br/>
<br/>
其中这里面的显示顺序，文字以及时间等都可自己写入配置，也包括 emoji 哦

来上一个花里胡哨的

![sample](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-04/terminal-sample.png)

```bash
export PS1='\[\033[01;36m\]\u😎 \[\033[01;33m\]\h \[\033[01;35m\]\t \[\033[01;31m\]\W\$ \[\033[00m\]😛 '
```

## 附：查看/修改电脑名称

```bash
#查看
scutil --get ComputerName
scutil --get LocalHostName
```

修改 `--get` 改成 `--set` ，例：
```bash
#修改
scutil --set ComputerName "Yue的MacBook Pro"
scutil --set LocalHostName "YuedeMacBook-Pro"
```

>
- Computer Name：共享设置里的电脑名称，可以使用汉字等特殊字符；
- Local Host Name：Computer Name的别名，把特殊字符转换为ASCII字符，比如汉字转为拼音，空格转为-，同样在共享设置里可以看到；
- 在局域网里可以通过 Local Host Name 加上  .local  来访问 Mac 电脑，比如：
```bash
ping YuedeMacBook-Pro.local
```