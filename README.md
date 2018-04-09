## 我的博客：[http://1ilI.github.io](http://1ilI.github.io)

基于 [Jekyll](https://jekyllrb.com/) 所创建的静态博客，主题来源于 [Gaohaoyang](https://github.com/Gaohaoyang/gaohaoyang.github.io) ，感谢大神～  

稍稍修改了点东西，果然是有大神带路，改起来都方便 (｡ì _ í｡)

## 趟坑经历
### 安装jekyll
安装 jekyll 环境主要是为了让博客系统在本地跑起来，如果不想在本地运行，也是可以的，但还是强烈建议先试着在本地跑起来，没有什么问题后再推到的 GitHub 上。

我这个系统是 macOS Heigh Sierra 的，安装时就只看了 [jekyll 官网](https://jekyllrb.com/) ，一共就四行代码，大喜，然后就入坑了。。。

```bash
gem install jekyll bundler

jekyll new my-awesome-site

cd my-awesome-site

bundle exec jekyll serve
```

第一步 `gem install jekyll bundler` 就出错了

```bash
ERROR:  While executing gem ... (Errno::EACCES)
Permission denied @ rb_sysopen - /Library/Ruby/Gems/2.3.0/gems/jekyll-3.7.3/.rubocop.yml
```

看起来应该是没有权限，那么试一下 `sudo`

```bash
sudo gem install jekyll bundler
```

又出错了= =

```bash
ERROR:  While executing gem ... (Gem::FilePermissionError)
You don't have write permissions for the /usr/bin directory.
```

还是权限，说你没有写的权限，那好，我给你加 `chmod 777` 总可以了吧

```bash
chmod 777 /usr/bin
```

结果

```bash
chmod: Unable to change file mode on /usr/bin: Operation not permitted
```

凭什么，我寄几的电脑，`/usr/bin`  这个目录没有权限，那还有谁有权限！！

一顿搜索后才发现，原来早在 Mac OS X 10.11 之后就一些目录就被视为系统位置且不可操作了(´･_･`)，那只能换个位置安装喽，后来找到了Jekyll的一些文档，里面写好了针对 10.11 之后应该如何操作的了，地址： [https://jekyllrb.com/docs/troubleshooting/#jekyll--mac-os-x-1011](https://jekyllrb.com/docs/troubleshooting/#jekyll--mac-os-x-1011)

```bash
#这两个竟然都可以
sudo gem install -n /usr/local/bin/ jekyll bundler
sudo gem install jekyll bundler -n /usr/local/bin/
```

这下可以了吧  囧

本地执行的话，我是按照大神的做法，直接 `jekyll s` 就可以，本地地址和怎么结束，终端输出上都有

这里还有一份中文翻译版 jekyll 官网[http://jekyllcn.com/](http://jekyllcn.com/) （中文文档翻译落后于英文官网，有兴趣有时间的小伙伴可以参与翻译，为开源世界贡献一份力哦~）


### 标签页小图标
我是在localhost环境下试运行的，发现我自己换的小图标 favicon.ico 在浏览器标签页上怎么都不显示，很纳闷，然后想到我这个 Mac 下面的 Photoshop 没有导出 ico 的选项，只能先导出 bmp ，再手改 ico 。是不是这个 ico 文件有问题啊，然后又找了个在线转 ico 的网站，导出 ico ，再试一次，还是不行，于是找到了原作者的代码

```html
<link rel="shortcut icon" href="/favicon.ico?" type="image/x-icon">
<link rel="icon" href="/favicon.ico?" type="image/x-icon">
```

嗯？ ` href="/favicon.ico?" ` 最后这个问号哪来的，黑人问号脸？不对啊，原作者就是那么写的，而且他博客上也没毛病啊，但是这个问号到底是干嘛的，删了试试？试了竟然可以显示，不是前端工程师的我感到很奇怪。

好吧，那就这样吧，` href="/favicon.ico" `，提交推送，线上环境一看，小图标又没有了
(╯°□°）╯︵ ┻━┻ 

我又改回来了，本地环境不看也罢，线上可以就行，知道为什么的大神可以给我科普一下 (ｰ ｰ;)

