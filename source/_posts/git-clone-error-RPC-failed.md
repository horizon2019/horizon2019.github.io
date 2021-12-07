---
title: "git clone error: RPC failed;"
date: 2018-12-17 21:30:43
tags: 
categories: git
---

### git clone 时发现项目一直反复克隆不下来，并且报错

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

depth 用于指定克隆深度，为 1 即表示只克隆最近一次 commit.

```
 cd large-repository
```

然后再使用 --unshallow 参数把之前的历史重新再 pull 下来

```
 git fetch --unshallow
```

方法 1. 推荐 proxychains 代理，或者挂米国 VPS

方法 2. 把 https 改成 http

方法 3. 在 release 中选择 download 而非 clone 完整仓库，缺点：没有.git 本地仓库，不能 commit，push

方法 4. git clone --depth=1 ,这样只 clone 当前最新的 commit 版本，缺点：没有.git 本地仓库，不能 commit，push

方法 5. 在国内同类代码托管网站查找是否有同样的源码，从国内镜像下载。

方法 6.网速不好，查看网络情况
