---
title: Hexo 搭建全记录
header_img: /img/hexobuild.jpg
tags: hexo build
date: 2019-09-11 23:11:46
categories: blog
---

## Hexo 搭建全记录

> Markdown 本篇文章主要记录 hexo 博客的环境搭建以及到后期功能优化，以及问题修复。

### Hexo 安装环境

Hexo 是一款基于 node 的静态博客网站生成器，使用 Markdown 语法进行博文编辑，相比其他的静态网页生成器而言有着，生成静态网页最快，插件丰富（已经移植了大量 Octopress 插件）

作为一名 phper 的理解，我们可以将 Nodejs 理解为 php,而 npm 好比 composer,Node.js 主要用于构建后端服务器端应用程序，但作为全栈和前端解决方案，它也非常受欢迎。Npm 则是一个包管理工具。下面提供了`三种`安装方法：

一、安装 nodejs 和 npm(从 node source)

1.启用 NodeSource 存储库：`curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -`

2.安装 nodejs 和 npm:`sudo apt install nodejs`

3.查看 node 版本：`node --version`

二、使用 nvm 来安装 nodejs,可以管理 nodejs 的版本：

1.`curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash`

查看版本：`nvm --version`

2.nvm 安装 node：`nvm install node`

3.查看 node 版本：`node --version`

4.安装最近的 nvm 版本：`nvm install --lts`

三、从 ubuntu 仓库里安装 node 和 npm 1.`sudo apt update`
`sudo apt install nodejs`

2.`sudo apt install build-essential`

3.如果要卸载和 node 和 npm`sudo apt remove nodejs npm`

其中第一第三种方法更容易，但是使用 nvm 安装提供最大的灵活性，可以选择或者移除你的 nodejs 版本，可以根据实际情况选择。

### Hexo 主题下载安装

关于主题安装可以先去 hexo 的主题网站里面挑选比较喜欢的主题，在 github 找到源代码，然后这里我用了 git 子模块来管理主题。

参考文章：https://blog.csdn.net/guotianqing/article/details/82391665

简单解释下博文里面这条命令：
`git submodule add <url> <path>`
`url`指的是你的 githuburl 为子模块仓库的 url 路径，而`path`指的是你执行该条命令时文件夹的相对路径。例如，处在/var/www/blog/themes 路径下面执行该条命令，那么就是`git submodule add https://.... /theme_name`

然后在 hexo 的根目录下面的 theme 文件夹里就可以看到以及管理的你的主题代码。

### Hexo 主题配置及发布博文命令

关于 hexo 的全局配置在 hexo 根目录下面的站点配置文件`_config.yml`

而关于主题的相关配置就在 theme 里面的`_config.yml`

关于使本地代码各项配置项生效发布到网站的操作简要命令就是以下几个

```python
hexo clean
hexo generate ==hexo g
hexo deploy ==hexo d
hexo new blog_name(博文标题)

删除博文，在source/_post/下面直接删除就可以
```

### Hexo 使用问题小结（不断更新）

bug1.`YAMLException: end of the stream or a document separator is expected at line 2, column 6: title: about`

solution:网上看到的结论都是文件中所有冒号后面的空格，格式很严格，必须是只有一个，半角。不管是多了还是少了都会报错，这是 yml 解释器所定义的语法。如果不确定的话，将输入法调整到英文模式，删除所有冒号后面的空格重新输入，不要使用 Tab。
但是我反复改正空格之后错误依然存在，问题在于我创建 about 页面时，将

```title: about
date: 2017-05-31 10:05:56
layout: about
---
```

复制成了

```
--
title: about
date: 2017-05-31 10:05:56
layout: about
---
```

目前不清楚这个`--`为何会导致以上错误，这里需要注意不要多出其他字符。

bug2.`当发现配置的 hexo 博文下方作者头像不能正常显示，404 错误时.F12 使用谷歌浏览器查看图片完整的路径，在相应的文件夹添加图片。如果图片不多，也可以保存在 source/img 文件夹下面，直接使用绝对路径来表示图片路径。

`` 尝试本地source中建立img文件夹` <img src="img/图片名.jpg> 这里如果显示不出来，请使用<img src="/img/图片名.jpg>绝对路径 ``

bug3.`博文里面图片引入显示失败,hexo博文图片前缀有.com不能正常显示`

1.先安装插件
npm install https://github.com/CodeFalling/hexo-asset-image --save

中途出现这个错误，可以忽略

```
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@2.1.1 (node_modules/fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@2.1.1: wanted {"os":"darwin","arch":"any"} (current: {"os":"linux","arch":"x64"})
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@1.2.9 (node_modules/nunjucks/node_modules/fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.2.9: wanted {"os":"darwin","arch":"any"} (current: {"os":"linux","arch":"x64"})
```

2.打开\_config.yml 文件，修改下述内容
post_asset_folder: true

3.这里以防插件 bug，建议替换一下这个位置的文件/node_modules/hexo-asset-image/index.js

参考博文：https://blog.csdn.net/xjm850552586/article/details/84101345

bug4.`hexo博客文章里面插入图片后封面图片就不能正常显示`

![](http://localhost:4000/img/pic_bug.jpg)
尚未解决

此前使用的主题或许存在缺陷，后面选用了https://github.com/fi3ework/hexo-theme-archer主题

4.切换主题后出现了

```
 >> 63|                         <span><%- __('word_count') %>: <span class="post-count word-count"><%= wordcount(page.content) %></span><%= __('reading_time') %>: <span class="post-count reading-time"><%= min2read(page.content) %> min</span></span>
    64|                     </div>
    65|                 <% } %>
    66|                 <div class="post-intro-meta">
```

wordcount is not defined,于是执行：npm i --save hexo-wordcount

当时是在 hexo-theme-archer 主题文件夹下面执行的，后面尝试 cd ..在 theme 下面执行 npm i --save hexo-wordcount

虽然有警告但是安装成功

```
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@2.1.1 (node_modules/fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@2.1.1: wanted {"os":"darwin","arch":"any"} (current: {"os":"linux","arch":"x64"})
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@1.2.9 (node_modules/nunjucks/node_modules/fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.2.9: wanted {"os":"darwin","arch":"any"} (current: {"os":"linux","arch":"x64"})

+ hexo-wordcount@6.0.1
added 8 packages from 25 contributors, updated 1 package and audited 4872 packages in 13.544s
```

5.更新代码之后发现图片显示不出来，但是插件都已经成功安装，最后检查是 git 提交时自动忽略了 public 文件夹下面的东西，所有图片并没有更新在服务器，修改.gitignore 文件，重新 push 图片显示成功

`这个操作是错误的，因为hexo clean命令会删除数据库以及public文件夹下面的内容，所以还是建议图片存在其他文件夹下面`


6.hexo 下设置了language,中文标题链接出现乱码问题
1.将_config.yml文件修改language为zh-Hans;
2.之前的permalink是这样的permalink: :year/:month/:day/:title/，命名方式太多层了，爬虫不好爬，而且中文命名方式如果要引用自己的链接的话就很麻烦，（因为会重新编码成好长一串），并且中文链接在百度Google权重很低

于是这里我们使用hexo-abbrlink：
```
npm install hexo-abbrlink --save
```
然后在_config.yml配置即可使用
```
permalink: :abbrlink/
abbrlink:
  alg: crc32  # 算法：crc16(default) and crc32
  rep: hex    # 进制：dec(default) and hex
permalink_defaults:
```