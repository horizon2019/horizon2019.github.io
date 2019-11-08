---
title: Hexo 搭建全记录
header_img: hexobuild.jpg
date: 2019-11-07 22:28:46
tags:
---

## Hexo 搭建全记录

> Markdown 本篇文章主要记录hexo博客的环境搭建以及到后期功能优化，以及问题修复。    

### Hexo 安装环境
Hexo 是一款基于node 的静态博客网站生成器，使用Markdown语法进行博文编辑，相比其他的静态网页生成器而言有着，生成静态网页最快，插件丰富（已经移植了大量Octopress插件）



作为一名phper的理解，我们可以将Nodejs理解为php,而npm好比composer,Node.js主要用于构建后端服务器端应用程序，但作为全栈和前端解决方案，它也非常受欢迎。Npm则是一个包管理工具。下面提供了`三种`安装方法：

一、安装nodejs和npm(从node source)

1.启用NodeSource存储库：`curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -`


2.安装nodejs和npm:`sudo apt install nodejs`

3.查看node版本：`node --version`

二、使用nvm来安装nodejs,可以管理nodejs的版本：

1.`curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash`

查看版本：`nvm --version`

2.nvm安装node：`nvm install node`

3.查看node版本：`node --version`

4.安装最近的nvm版本：`nvm install --lts`

三、从ubuntu仓库里安装node和npm
1.`sudo apt update`
`sudo apt install nodejs`


2.`sudo apt install build-essential`

3.如果要卸载和node和npm`sudo apt remove nodejs npm`


其中第一第三种方法更容易，但是使用nvm安装提供最大的灵活性，可以选择或者移除你的nodejs版本，可以根据实际情况选择。


