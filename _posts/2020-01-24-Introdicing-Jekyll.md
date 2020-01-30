---
layout: post
title: "Jekyll 入手: 基本操作"
date: 2020-01-24 11:12:06 +0800
authorize: Daren
categories: jekyll
nutshell: 简要介绍如何使用 Jekyll
---

## 安装

**安装之前需要先准备好 ruby**

在 shell 里执行

```shell
gem install bundler jekyll
```

安装 jekyll 和 bundler， bundler 是一个 gem 的管理器， 可以通过 gemfile 来指定需要的版本

## 第一个页面

### 书写

建一个文件夹作为网站的根目录

创建一个 `index.html`, 写一个简单的 helloworld

```html
<!DOCTYPE html>
<html>
<head>
	<title>first site</title>    
</head>
<body>
hello,world!!!    
</body>
</html>
```

### 构建

实际上, jekyll 是一个静态网页构建工具, 这里介绍两个常用的 jekyll 构建命令

```shell
jekyll build
# 构建网页并把网页输出到网页根目录下面的 _site 文件夹
jekyll serve
# 跟上面功能一样但是会随着你的每次更改进行更新, 并且把网站运行在本地端口4000

## 注意, 如果 jekyll serve 找不到 _site 文件夹, 会报 not found 错误, 这时候运行 jekyll build 可以自动生成一个 _site
```

现在你就能看到本地 4000 端口上已经把你的 helloworld 放上去了

**我们刚刚来到山脚, 前路漫漫, 可惜不是所有的人都能一览群山**

## 这些目录都是干什么的

目录里 会看到很多目录

```shell
daren@daren-laptop-slim:~/Documents/learnJekyll$ tree
.
├── 404.html
├── about.markdown 
├── _config.yml # 比较重要的配置文件, 有很多属性帮助你进行个性化, 包括网站的标题等等
├── Gemfile # ruby 用的文件 存放所有依赖包
├── Gemfile.lock
├── index.markdown # 主页?
├── _posts # 存放 你写完的 markdown 文件 
│   └── 2020-01-21-welcome-to-jekyll.markdown # 这个是系统自动生成的实例文章
└── _site # 最终的网页目录 这也是 需要托管的目录 
    ├── 404.html
    ├── about
    │   └── index.html
    ├── assets
    │   ├── main.css
    │   ├── main.css.map
    │   └── minima-social-icons.svg
    ├── feed.xml
    ├── index.html
    └── jekyll
        └── update
            └── 2020
                └── 01
                    └── 21
                        └── welcome-to-jekyll.html

9 directories, 15 files
daren@daren-laptop-slim:~/Documents/learnJekyll$ 


```

如果你看了后面有关 Front  Matter 的叙述, 你会发现 layout 为 page 的都放在 jekyll 的根目录下,  _post 下的文章都是 layout 为 post 的文章.

## 前页 (Front  Matter) && 怎么写文章

Front  Matter 写在每一个 markdown 文件的前面用来写明这个文件的相关信息. 所有 jekyll 的文章都要具备 Front  Matter

### 举个例子

让我们打开 jekyll 自带的 md 文件, 是这个样子的

```
---
layout: post
title:  "Welcome to Jekyll!"
date:   2020-01-21 17:18:34 +0800
categories: jekyll update
---
## 下面是正文, 上面就是 Front Matter, 这些信息都是可以修改, 甚至可以自己定义
You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

Jekyll requires blog post files to be named according to the following format:

`YEAR-MONTH-DAY-title.MARKUP`

Where `YEAR` is a four-digit number, `MONTH` and `DAY` are both two-digit numbers, and `MARKUP` is the file extension representing the format used in the file. After that, include the necessary front matter. Take a look at the source for this post to get an idea about how it works.

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/

```

- layout : 不同的 layout 对应不同的版块, 比如 post 和 page, 空白显示的是纯文本
- title : 标题, 如果不写的话或从文件名提取
- date : 详细日期时间, 如果这里不写的话, 会从 _post 里的相应文件名获取, 注意两者一定要匹配, 而且至少要写在文件名上
- categories : 分类
- permalink : 指定链接地址 

需要注意的是`_post`下的文件名一定要符合规范(前面包含日期, 并且日期与 Front  Matter 相匹配, 空格用连字符代替), 不然 jekyll 不会将其作为文章处理, 导致的结果就是文件不会出现在列表了.

这个文件可以用在后面, 你甚至可以自己定义自己想要的 Front Matter 和 自己想要的 Layout

如果你想要更好的对你的文章进行分类的话, 你完全可以在 _post 下面建立子文件夹, 对最终结果完全不影响

最后我们总结一下 _post 文件夹下面的要点

- 要有 Front Matter (所有的都需要)
- 要有日期

## 写草稿

jekyll 其实内置了`draft`功能, 如果你想要打草稿, 可以在项目的根目录下面建立`_drafts`文件夹, 其中所有的文件都会被忽略

但是`draft`提供预览功能, 你只须在启动 jekyll 运行时添加参数 `--draft`即可

```shell
jekyll serve --draft
```

而且 draft 中的文件不必写日期, 如果不写默认会用上次修改的日期作为缺省日期.

草稿对应的是`_post`文件夹中的文章, 而 layout 为 page 的不能使用 draft 预览

## 写 page

另一个 layout 就是 page

这种 layout 直接就放在项目的根目录下, 文件名没有特殊的要求, 

- 要有 Front Matter
- permalink 如果不写的话直接从文件在项目中的相对目录作为网址

## URL结构 以及 permalink

jekyll 会根据你的 post 的 Front Matter 类别, 时间自动生成目录, 并存入`_site`中, 你可以用 tree 来查看

```shell
daren@daren-laptop-slim:~/Documents/learnJekyll/_site$ tree
.
├── 404.html
├── about
│   └── index.html
├── assets
│   ├── main.css
│   ├── main.css.map
│   └── minima-social-icons.svg
├── feed.xml
├── fu
│   └── index.html
├── index.html
├── intro
│   └── index.html
└── jekyll
    └── update
        └── 2020
            └── 01
                ├── 01
                │   └── welcome-to-jekyll111.html
                └── 11
                    └── welcome-to-jekyll.html

# 类别-日期-名字 的结构
```

但是这样的坏处就是如果你的文件 Front Matter 产生了变动, 相应的 URL 也会产生变动. 如果需要一个恒定不变的网址, 可以在 Front  Matter 中加入 `permalink` 进行设定

例子:

```
---
layout: page
title: About
permalink: /about/ # 这里也可以用引号来括起来
---
```

同时我们也可以用变量来设置 permalink 来设置永久 URL

例子:

```
layout: post
title:  "Welcome to Jekyll!"
date:   2020-01-11 17:18:34 +0800
categories: jekyll update
permalink: /:categories
permalink: /:categories/:year/:month/:day/:title # 其实这个生成的就是缺省链接
# 你甚至可以在后面加上,html或者.php...
```

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/img/permalink-var.png)

## 默认 Front Matter

每个文章前面都要写 Front  Matter 实在是太不爽了, 你可以通过编辑 `_config.yml`来对符合条件的文章制定相应的缺省 Front Matter

来到`_config.yml`的最后, 添加

```yaml
defaults:
	-
	scope:
		path: "" # 相应的目录, 空值的意思是所有目录
	  values:
	  		# 这里写 values
	  		layout; "post"
	  		title:
	  		...
	  		
		
```

最后, 记得重新启动 jekyll 来看到效果.

## 安装主题 (Themes)

现在你可能觉得自己的网站看起来十分的简约 - 事实上, jekyll 默认的主题名字就脚 minima , 那么如何换成自己中意的主题呢?

### 查找目标

由于所有的 jekyll 都是基于 ruby 开发的软件包, 你可以直接从 gem 软件源的网站搜索, 在中国, 速度最快的 gem 软件源是 `ruby-china`
直接搜索关键字 `jekyll theme`就能找到许多 jekyll 的主题

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/img/%E6%90%9C%E7%B4%A2jekyll%E4%B8%BB%E9%A2%98.png)

如果你想看预览图的话, 可以到这个主题的 "HomePage" - 也就是这个主题的 github 主页查看效果图和 demo 页面

前面我们在安装 jekyll 的时候一并安装了 bundler , bundler 可以通过`Gemgfile`来对 ruby 软件包进行管理, 所以当你找到了自己中意的主题, 可以直接到`Gemfile`中将这个软件包添加进去

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/img/%E7%BC%96%E8%BE%91Gemfile%E6%B7%BB%E5%8A%A0%E4%B8%BB%E9%A2%98.png)

同时在 `_config.yml`中将主题改成想要的主题

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/img/%E4%BF%AE%E6%94%B9config.png)

最后在 jekyll 的项目目录中执行 `bundle install` 来安装刚才的 ruby 包, 完成启动之后, 博客的主题就变成你想要的样子了

