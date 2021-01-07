---
title: windows搭建django框架本地环境
date: 2020-01-07 22:39:41
tags:
---

这是我最终搭建好的环境
python 3.8.0
django 3.0.2


1.在安装django框架之前，得确保本地环境有python和pip,pip是一个包依赖管理工具。
中间解决了pip更新问题，按照提示安装与python匹配的pip版本；

这是python安装参考教程：
https://baijiahao.baidu.com/s?id=1606573927720991570&wfr=spider&for=pc

中间出现过这个问题：
WARNING: You are using pip version 19.2.3, however version 19.3.1 is available.
当直接输入python -m pip install --upgrade pip更新还报错的时候，
输入命令：python -m pip install -U pip

2.pip install Django==3.0.2
在pip安装过程中(python pip安装第三方库超时问题（raise ReadTimeoutErrorself._pool, None, ‘Read timed out.’)
方案一：pip下载超时处理
pip --default-timeout=100 install 第三方库名
但是发现依然下载太慢解决不了问题
方案二：更换安装源
不用改pip的配置文件
安装python的库函数只要
python -m pip install -i https://pypi.tuna.tsinghua.edu.cn/simple pack_name

3.直到在命令行窗口输出可以看到版本信息，证明安装成功



4.创建django项目
django-admin startproject 项目名称
例：django-admin startproject mytest


5.创建django应用
项目创建完成后需要有具体的应用，一个项目下可以有多个应用。
进入命令行窗口：
python manage.py startapp 应用名称
例：python manage.py startapp myapp


6.在项目设置添加应用
打开mytest/settings.py,INSTALLED_APPS下添加：

7.在终端启动django自带的测试用服务器：
python manage.py runserver ip:port
例：python manage.py runserver

说明：ip和port可以分别设置服务器的ip和端口；省略不写默认ip为127.0.0.1；端口：8000；


8.打开浏览器输入"127.0.0.1:8000"，即可得到网页

