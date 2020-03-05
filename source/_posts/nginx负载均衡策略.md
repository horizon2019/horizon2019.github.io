---
title: nginx负载均衡策略
date: 2019-04-23 18:27:40
tags:
---


## nginx可以做什么？

### 1.正向代理
正向代理是一个位于客户端和目标服务器之间的代理服务器(中间服务器)。为了从原始服务器取得内容，客户端向代理服务器发送一个请求，并且指定目标服务器，之后代理向目标服务器转交并且将获得的内容返回给客户端。正向代理的情况下客户端必须要进行一些特别的设置才能使用。

  反向代理正好相反。对于客户端来说，反向代理就好像目标服务器。并且客户端不需要进行任何设置。客户端向反向代理发送请求，接着反向代理判断请求走向何处，并将请求转交给客户端，使得这些内容就好似他自己一样，一次客户端并不会感知到反向代理后面的服务，也因此不需要客户端做任何设置，只需要把反向代理服务器当成真正的服务器就好了。

区别：正向代理需要你主动设置代理服务器ip或者域名进行访问，由设置的服务器ip或者域名去获取访问内容并返回；而反向代理不需要你做任何设置，直接访问服务器真实ip或者域名，但是服务器内部会自动根据访问内容进行跳转及内容返回，你不知道它最终访问的是哪些机器。

  正向代理是代理客户端，为客户端收发请求，使真实客户端对服务器不可见；而反向代理是代理服务器端，为服务器收发请求，使真实服务器对客户端不可见。

### 2.解决跨域问题

同源：URL由协议、域名、端口和路径组成，如果两个URL的协议、域名和端口相同，则表示他们同源。  
浏览器的同源策略：浏览器的同源策略，限制了来自不同源的"document"或脚本，对当前"document"读取或设置某些属性。从一个域上加载的脚本不允许访问另外一个域的文档属性。

### 3.配置文件解析
1.main(全区设置)  
2.server(主机配置)  
3.http(控制着nginx http处理的所有核心特性)  
4.location(URL匹配特定位置设置)。  
5.upstream(负载均衡服务器设置)  

```
#Nginx的worker进程运行用户以及用户组
#user  nobody;

#Nginx开启的进程数
worker_processes  1;

#定义全局错误日志定义类型，[debug|info|notice|warn|crit]
#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#指定进程ID存储文件位置
#pid        logs/nginx.pid;


#事件配置
events {
    
    
    #use [ kqueue | rtsig | epoll | /dev/poll | select | poll ];
    #epoll模型是Linux内核中的高性能网络I/O模型，如果在mac上面，就用kqueue模型。
    use kqueue;
    
    #每个进程可以处理的最大连接数，理论上每台nginx服务器的最大连接数为worker_processes*worker_connections。理论值：worker_rlimit_nofile/worker_processes
    worker_connections  1024;
}

#http参数
http {
    #文件扩展名与文件类型映射表
    include       mime.types;
    #默认文件类型
    default_type  application/octet-stream;
    
    #日志相关定义
    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';
    
    #连接日志的路径，指定的日志格式放在最后。
    #access_log  logs/access.log  main;

    #开启高效传输模式
    sendfile        on;
    
    #防止网络阻塞
    #tcp_nopush     on;

    #客户端连接超时时间，单位是秒
    #keepalive_timeout  0;
    keepalive_timeout  65;

    #开启gzip压缩输出
    #gzip  on;

    #虚拟主机基本设置
    server {
        #监听的端口号
        listen       80;
        #访问域名
        server_name  localhost;
        
        #编码格式，如果网页格式与当前配置的不同的话将会被自动转码
        #charset koi8-r;

        #虚拟主机访问日志定义
        #access_log  logs/host.access.log  main;
        
        #对URL进行匹配
        location / {
            #访问路径，可相对也可绝对路径
            root   html;
            #首页文件，匹配顺序按照配置顺序匹配
            index  index.html index.htm;
        }
        
        #错误信息返回页面
        #error_page  404              /404.html;
        
        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
        
        #访问URL以.php结尾则自动转交给127.0.0.1
        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}
        
        #php脚本请求全部转发给FastCGI处理
        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        #禁止访问.ht页面
        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }

    #第二个虚拟主机配置
    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    #HTTPS虚拟主机定义
    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
    include servers/*;
}

```

### 4.负载均衡实例
轮询策略:
```
server {
    #监听80端口
    listen 80;
    server_name localhost;
    
    # individual nginx logs for this web vhost
    access_log /tmp/access.log;
    error_log  /tmp/error.log ;

    location / {
        #负载均衡
        #轮询 
        #proxy_pass http://polling_strategy;
        #weight权重
        #proxy_pass http://weight_strategy;
        #ip_hash
        # proxy_pass http://ip_hash_strategy;
        #fair
        # proxy_pass http://fair_strategy;
        #url_hash
        # proxy_pass http://url_hash_strategy;
        #重定向
        #rewrite ^ http://localhost:8080;
    }

```
权重策略:

```
#2、指定权重
#指定轮询几率，weight和访问比率成正比，用于后端服务器性能不均的情况。 
upstream  weight_strategy { 
    server glmapper.net:8080 weight=1; # 应用服务器1
    server glmapper.net:8081 weight=9; # 应用服务器2
}

```
ip hash策略：
```
#每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，
#可以解决session的问题;在不考虑引入分布式session的情况下，
#原生HttpSession只对当前servlet容器的上下文环境有效
upstream ip_hash_strategy { 
    ip_hash; 
    server glmapper.net:8080; # 应用服务器1
    server glmapper.net:8081; # 应用服务器2
} 
```

iphash 算法:  
ip是基本的点分十进制，将ip的前三个端作为参数加入hash函数。这样做的目的是保证ip地址前三位相同的用户经过hash计算将分配到相同的后端server。作者的这个考虑是极为可取的，因此ip地址前三位相同通常意味着来着同一个局域网或者相邻区域，使用相同的后端服务让nginx在一定程度上更具有一致性。

其他负载均衡策略:
```
4、fair（第三方）
#按后端服务器的响应时间来分配请求，响应时间短的优先分配。 
upstream fair_strategy { 
    server glmapper.net:8080; # 应用服务器1
    server glmapper.net:8081; # 应用服务器2
    fair; 
} 

5、url_hash（第三方）
#按访问url的hash结果来分配请求，使每个url定向到同一个后端服务器，
#后端服务器为缓存时比较有效。 
upstream url_hash_strategy { 
    server glmapper.net:8080; # 应用服务器1
    server glmapper.net:8081; # 应用服务器2 
    hash $request_uri; 
    hash_method crc32; 
} 
```

重定向rewrite:
```
location / {
    #重定向
    #rewrite ^ http://localhost:8080;
}
```

验证思路：本地使用localhost:80端口进行访问，根据nginx的配置，如果重定向没有生效，则最后会停留在当前localhost:80这个路径，浏览器中的地址栏地址不会发生改变；如果生效了则地址栏地址变为localhost:8080；
