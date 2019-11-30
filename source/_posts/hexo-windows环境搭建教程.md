---
title: hexo-windows环境搭建教程
date: 2019-11-30 14:35:15
tags:
---

参考文章：https://www.cnblogs.com/xingyunblog/p/8675681.html
当在cmd模式下npm --version发现nodejs和npm安装失败时，需要配置nodejs和npm的环境变量

我是安装在D盘这个位置D:\Program Files\nodejs

1.首先在node.js的安装目录新建两个文件夹node_global和node_cache
![](/img/hexo_windows1.png)

2.cmd窗口输入以下两行命令：

npm config set prefix "D:\Program Files\nodejs\node_global"

npm config set cache "D:\Program Files\nodejs\node_cache"


3.在环境变量中新建系统变量：
![](/img/hexo_windows2.png)


4.修改【用户变量】中的path变量，将C:\Users\hua\AppData\Roaming\npm修改为D:\install\nodejs\node_global

![](/img/hexo_windows3.png)
5.npm install -g hexo-cli
![](/img/hexo_windows4.png)

发现运行很慢，解决办法就是修改 npm 的安装源，这里选择淘宝 NPM 镜像，这是一个完整 npmjs.org 镜像，你可以用此代替官方版本(只读)，同步频率目前为 10分钟 一次以保证尽量与官方服务同步。执行npm config set registry https://registry.npm.taobao.org


6.在hexo 博客项目文件夹下面，hexo server,如果遇到
bash: hexo command not found 问题解决，那么请先配置环境变量，在Path里添加你的node_modules下的.bin文件路径





6.在项目文件夹下，git bash中执行hexo-server,直接访问http://localhost:4000/



参考文章：
https://www.jianshu.com/p/957f5631faa9