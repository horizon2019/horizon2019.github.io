---
title: windows下scrapy框架搭建爬取数据
date: 2019-01-22 20:08:30
tags:
---

**一、安装scrapy的时候，使用pip install scrapy一般会失败，报超时的错误**
解决方法：pip install scrapy框架报错，就分开安装库
1.将scrapy安装过程中所用到的依赖的库安装完成之后，再安装scrapy
2.需要安装的依赖库有 lxml、 pyOpenSSL 、 Twisted 、pywin32
3.所有安装都可以使用pip install **来执行，一切进展都顺利，尝试多试几次
ps:我在安装过程中只有pip install Twisted报错，于是去这个网站https://www.lfd.uci.edu/~gohlke/pythonlibs/#twisted 寻找合适的安装whl文件
我这里下载的是Twisted-19.10.0-cp38-cp38-win32.whl，将它放在执行命令的根目录下
pip install Twisted-19.10.0-cp38-cp38-win32.whl
pip 安装Twisted成功
最后执行pip install scrapy全部成功


**二、IDE中出现IndentationError:unindent does not match any outer indentation level：**
在setting.json中配置：
```
    "python.linting.pylintArgs": [
        "--generate-members"
    ]
```


参考文章：https://blog.csdn.net/lwx356481/article/details/81224667
https://scrapy-chs.readthedocs.io/zh_CN/latest/intro/tutorial.html
https://blog.csdn.net/ngy321/article/details/88972255