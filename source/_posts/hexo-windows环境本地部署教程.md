---
title: hexo-windows本地环境部署教程
date: 2018-05-14 23:35:15
tags: hexo部署
categories: hexo
---

参考文章：https://www.cnblogs.com/xingyunblog/p/8675681.html
当在 cmd 模式下 npm --version 发现 nodejs 和 npm 安装失败时，需要配置 nodejs 和 npm 的环境变量

我是安装在 D 盘这个位置 D:\Program Files\nodejs

1.首先在 node.js 的安装目录新建两个文件夹 node_global 和 node_cache
![](https://www.starmoon.cloud/img/hexo_windows1.png)

2.cmd 窗口输入以下两行命令：

npm config set prefix "D:\Program Files\nodejs\node_global"

npm config set cache "D:\Program Files\nodejs\node_cache"

3.在环境变量中新建系统变量：
![](https://www.starmoon.cloud/img/hexo_windows2.png)

4.修改【用户变量】中的 path 变量，将 C:\Users\hua\AppData\Roaming\npm 修改为 D:\install\nodejs\node_global

![](https://www.starmoon.cloud/img/hexo_windows3.png)
5.npm install -g hexo-cli
![](https://www.starmoon.cloud/img/hexo_windows4.png)

发现运行很慢，解决办法就是修改 npm 的安装源，这里选择淘宝 NPM 镜像，这是一个完整 npmjs.org 镜像，你可以用此代替官方版本(只读)，同步频率目前为 10 分钟 一次以保证尽量与官方服务同步。执行 npm config set registry https://registry.npm.taobao.org

6.在 hexo 博客项目文件夹下面，hexo server,如果遇到
bash: hexo command not found 问题解决，那么请先配置环境变量，在 Path 里添加你的 node_modules 下的.bin 文件路径

7.在项目文件夹下，git bash 中执行 hexo-server,直接访问http://localhost:4000/

参考文章：
https://www.jianshu.com/p/957f5631faa9
