---
title: 'git clone error: RPC failed;'
date: 2019-12-17 21:30:43
tags: git clone
categories: Git
---

### git clone时发现项目一直反复克隆不下来，并且报错
```
remote: Counting objects: 100% (1640/1640), done.
remote: Compressing objects: 100% (1167/1167), done.
error: RPC failed; curl 18 transfer closed with outstanding read data remaining
fatal: the remote end hung up unexpectedly
fatal: early EOF
fatal: index-pack failed

```
于是使用
```
 git clone http://github.com/large-repository --depth 1
```

depth用于指定克隆深度，为1即表示只克隆最近一次commit.
```
 cd large-repository
```
然后再使用 --unshallow 参数把之前的历史重新再 pull 下来
```
 git fetch --unshallow
```

方法1. 推荐proxychains代理，或者挂米国VPS  

方法2. 把https改成http

方法3. 在release中选择download 而非clone完整仓库，缺点：没有.git本地仓库，不能commit，push

方法4. git clone --depth=1 ,这样只clone当前最新的commit版本，缺点：没有.git本地仓库，不能commit，push

方法5. 在国内同类代码托管网站查找是否有同样的源码，从国内镜像下载。

方法6.网速不好，查看网络情况