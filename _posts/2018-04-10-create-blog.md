---
layout: post
title: "第一篇用 jekyll 写的博客"
categories: jekyll
tags: jekyll rake HTML
---

* content
{:toc}

安装 jekyll ，使用 rake 创建 md 文件，md 文件中图片的使用





## 安装 jekyll
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

结果

```bash
chmod: Unable to change file mode on /usr/bin: Operation not permitted
```

凭什么，我寄几的电脑，`/usr/bin`  这个目录没有权限，那还有谁有权限！！

一顿搜索后才发现，原来早在 Mac OS X 10.11 之后就一些目录就被视为系统位置且不可操作了(´･_･`)，那只能换个位置安装喽，后来找到了Jekyll的一些文档，里面写好了针对 10.11 之后应该如何操作的了，地址： [https://jekyllrb.com/docs/troubleshooting/#jekyll--mac-os-x-1011](https://jekyllrb.com/docs/troubleshooting/#jekyll--mac-os-x-1011)

```bash
#这两个竟然都可以
sudo gem install -n /usr/local/bin/ jekyll bundler
sudo gem install jekyll bundler -n /usr/local/bin/
```

这下可以了吧  囧

本地执行的话，我是按照大神的做法，直接 `jekyll s` 就可以，本地地址和怎么结束，终端输出上都有

这里还有一份中文翻译版 jekyll 官网[http://jekyllcn.com/](http://jekyllcn.com/) （中文文档翻译落后于英文官网，有兴趣有时间的小伙伴可以参与翻译，为开源世界贡献一份力哦~）


## rake 神器

当环境都搭好了，你要写文章的时候，可能有以下几步：

* 在 `_post` 文件夹中创建一个名为 `年-月-日-标题.md` 的文件，类似于这样 `2018-04-10-welcome-to-jekyll.md` ，Mac上右键只有个新建文件夹，这怎么办，我只好默默的掏出终端 `vim 2018-04-10-welcome-to-jekyll.md` ，好麻烦啊，创建个文件都这么繁琐，而且年月日这样的信息你就不能自己生成吗，非要我来写  -_-

* 在刚刚建好的 *.md 文件中加入yml语法开头的头信息，比如这样
```yml
---
layout: post
title:  "Welcome to Jekyll!"
categories: jekyll 
tags: [jekyll]
---
```

然后才开始写文章，这些东西就不能一下创建好吗，我就只是想码个几个字 (╯‵□′)╯︵┻━┻ 

好在有 Rake 这样的神器，Rake 的意思是 Ruby Make，一个用 ruby 开发的代码构建工具。用它做一个脚本，一行命令即可创建好 md 文件，并加上头信息。

#### 安装 rake 

```bash
sudo gem install rake
```

然鹅我电脑又装不上

```bash
Fetching: rake-12.3.1.gem (100%)
ERROR:  While executing gem ... (Gem::FilePermissionError)
    You don't have write permissions for the /usr/bin directory.
```

这个问题又出现了，直接 
```bash
sudo gem install rake -n /usr/local/bin
```
这样就可以了。

```bash
Successfully installed rake-12.3.1
Parsing documentation for rake-12.3.1
Installing ri documentation for rake-12.3.1
Done installing documentation for rake after 0 seconds
1 gem installed
```

另用 `gem list rake` 即可查看是否安装上。

#### 编写 Rakefile , 并放入博客项目的根目录下

先切换到对应目录下，`vim Rakefile` ，我的是这么写的

```ruby
require 'rake'
require 'yaml'

SOURCE = "."
CONFIG = {
  'posts' => File.join(SOURCE, "_posts"),
  'post_ext' => "md",
}

# Usage: rake post title="A Title"
desc "Begin a new post in #{CONFIG['posts']}"
task :post do
  abort("rake aborted: '#{CONFIG['posts']}' directory not found.") unless FileTest.directory?(CONFIG['posts'])
  title = ENV["title"] || "new-post"
  slug = title.downcase.strip.gsub(' ', '-').gsub(/[^\w-]/, '')
  filename = File.join(CONFIG['posts'], "#{Time.now.strftime('%Y-%m-%d')}-#{slug}.#{CONFIG['post_ext']}")
  if File.exist?(filename)
    abort("rake aborted!") if ask("#{filename} already exists. Do you want to overwrite?", ['y', 'n']) == 'n'
  end

  puts "Creating new post: #{filename}"
  open(filename, 'w') do |post|
    post.puts "---"
    post.puts "layout: post"
    post.puts "title: \"#{title.gsub(/-/,' ')}\""
    post.puts "categories: "
    post.puts "tags: "
    post.puts "---"
    post.puts ""
    post.puts "* content"
    post.puts "{:toc}"

  end
end # task :post
```

当然你也可以自行添加你的 description，categories，tag 等信息

#### 开始使用吧

终端命令

```bash
rake post title="hello world"
```

<!-- ![结果](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-04/rake-create-md-file.png) -->

<!-- <center >
<img alt="结果" src="https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-04/rake-create-md-file.png" width="400"/>
</center> -->

<div align="center"> <img alt="结果" src="https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-04/rake-create-md-file.png" width="400"/></div>

看一下，这样是不是就方便多了 ^_^


## MD文件引用图片

#### 图片上传至 GitHub ，并获得地址
我是直接把图片上传到 GitHub 中的，新建一个 `resource` 文件夹，把图片等资源和博客放在一起，一来方便管理，二来不用去找各种对象存储服务商（图床），懒得去注册，三来也不怕这个服务器跑路  /→ →

上传至 GitHub 后，找到对应图片，然后右键，选择 `在新标签页中打开图片` ，然后在新标签页中复制链接地址即可～

![右键](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-04/right-click.png)

![复制](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-04/copy-link.png)

#### 图片引用的方式
* Markdown 语法里的 `![alt](img_src)`

    这种方式图片居左，大小不受控制，由 img_src 决定。

* 使用 HTML 标签 `<img alt="" src=""/>`

    这种方式灵活多变，位置及大小由标签的属性决定。

#### 图片的缩放及位置

有时候我们引用了图片，为了更好的显示效果，需要一点点排版方式，或者缩放效果，这时候 `![alt](img_src)` 这个就不怎么好使了，我们可以这样

首先看一下原显示效果，和 `![alt](img_src)`显示效果一致

```html
<img alt="hua_q" src="https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/images/hua_q.png"/>
```
<img alt="hua_q" src="https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/images/hua_q.png"/>

* 按固定宽高来显示：

```html
<img alt="hua_q" src="https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/images/hua_q.png" width="200px" height="200px"/>
```

<img alt="hua_q" src="https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/images/hua_q.png" width="200px" height="200px"/>

这样就指定图片的宽高大小了，另外这里的 “width” 或 “height” 可以只填一个，未填的那个属性会按照比例进行缩放。

* 按比例来显示：

```html
<img alt="hua_q" src="https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/images/hua_q.png" width="15%" height="15%"/>
```

<img alt="hua_q" src="https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/images/hua_q.png" width="20%" height="20%"/>

这里的 “width” 和 “height” 后面的 15% 是根据窗口的大小以一定的比例显示的。并且这种按照百分比显示方法，是以 width 方向为准，并保持纵横比的，换句话说，把设置 width 的部分去掉，height的值改为任意百分比，显示的图像都是原图大小（个人觉得这根博客窗口有关，毕竟现实截面的宽度相对是固定的，而高度/长度却是变化着的）。因此用此方法的时候，可以把height设置缺省。

* 设置图片位置

```html
<img alt="hua_q" src="https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/images/hua_q.png" width="100px" align="right"/>
```

<img alt="hua_q" src="https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/images/hua_q.png" width="100px" align="right"/>
居右显示
<br/>
<br/>
<br/>
<br/>

 设置居中的话就稍微麻烦点 ╮(╯▽╰)╭

```html
<div align="center"> <img alt="hua_q" src="https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/images/hua_q.png" width="100px"/></div>
```

<div align="center"> <img alt="hua_q" src="https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/images/hua_q.png" width="100px"/> <br/>加个居中的 div </div>

