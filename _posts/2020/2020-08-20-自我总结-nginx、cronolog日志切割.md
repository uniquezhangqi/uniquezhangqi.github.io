---
layout:     post             				# 使用的布局（不需要改）
title:         nginx、cronolog日志切割  # 标题 
subtitle:    					  				#副标题
date:       2020-08-20  					# 时间
author:     Ian                  			# 作者
header-img: img/home-bg-o.jpg 	#这篇文章标题背景图片
catalog: true                        	# 是否归档
istop:  false                             # 是否置顶
iscopyright: false                      # 是否版权，默认有
music-id:                                        # 网易云音乐单曲嵌入
music-idfull:                               # 网易云音乐歌单嵌入
apserver:                           # 音乐平台netease/tencent/kugou/xiami/baidu
aptype:     	           		# 音乐类型song/playlist/album/search/artist
apsongid:                    # 音乐song/playlist/album id
tags:                              	           	#标签
    - 
---

&nbsp;
&nbsp;

> 原文：lemonHe - https://zhuanlan.zhihu.com/p/67754050
> 
> 原文：遗忘曾经谢谢 -  https://my.oschina.net/wForget/blog/3224595

## cronolog日志切割神器

许多日志文件是不分割的，这样既不易于管理，也不易于分析统计。

cronolog作为日志过滤程序，可用来切割linux日志文件，通过对输入的日志按文件名模板和当前日期重新编排，来按格式生成所需日志。cronolog 旨在和一个Web服务器一起使用，如Apache、Nginx，分割访问日志为yy-mm-dd-hh格式的日志。

记录一下使用cronolog来切割nginx日志的过程。

### 安装cronolog
centos系统直接使用yum来安装cronolog和httpd。

yum install -y cronolog httpd

### 查看cronolog
使用which cronolog来查看是否已经安装好cronolog，如下则表示已经安装好。

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi1o6kswrhj312a034gmm.jpg)

### 配置nginx的conf文件
配置error按天输出，access按小时保存，在nginx.conf文件中将error_log和access_log进行如下配置：

``` txt
error_log "pipe:/usr/sbin/cronolog /home/admin/logs/cronolog/%Y/%m/%Y-%m-%d-error.log" error;
access_log "pipe:/usr/sbin/cronolog /home/admin/logs/cronolog/%Y/%m/%Y-%m-%d-%H-access.log" main;

```

### 新建目录

新建目录用于保存日志。

``` txt
mkdir -p /home/admin/logs/cronolog/
```

### 查看日志
运行过程中，会产生按照日志格式配置的文件，大功告成！

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi1o8577ikj312i02cwfb.jpg)


## Nginx打印RequestBody 

## 背景
新的日志收集构架中直接去掉 SpringBoot 服务，而是通过 Nginx 作为服务器，收集日志，打印成日志文件，通过 Flume 消费到 Kafka 中。由于日志收集的请求都是 Post JSON 的格式，所以需要在 Nginx 中获取 Post 的 Body。

## 问题
在配置 log_format 后，发现无法打印 $request_body

``` txt
log_format user_log_format escape=json '{"time": "$msec", "ip": "$remote_addr", "ua": "$http_user_agent", "data": "$request_body"}';


```

查看官方文档，有如下注释。意思是说 $request_body 需要在带有 proxy_pass, fastcgi_pass, uwsgi_pass, scgi_pass 这些指令的 location 中，当 request_body 被读到内存缓冲区中使用。

``` txt
The variable’s value is made available in locations processed by the proxy_pass, fastcgi_pass, uwsgi_pass, and scgi_pass directives when the request body was read to a memory buffer.

```

### Nginx 安装
由于在解决问题的时候需要使用到 ngx_lua 模块，所以我是直接安装的 OpenResty。 官网：http://openresty.org/cn/ 安装文档：http://openresty.org/cn/installation.html

### 解决

>在使用 （proxy_pass, fastcgi_pass, uwsgi_pass, scgi_pass） 指令的 location 中打印日志。

>使用 HTTP Echo Module (OpenResty 中已经包括了这个模块)。

HTTP Echo Module 参考文档：https://www.nginx.com/resources/wiki/modules/echo/

``` xml
worker_processes  1;
error_log logs/error.log;
events {
    worker_connections 1024;
}
http {
    include       /usr/local/openresty/nginx/conf/mime.types;
    default_type  application/octet-stream;

    # 日志文件格式  access.log  nginx默认的日志文件
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent $host "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    # 日志文件格式 user_defined.log  数据会写到这个文件中
    log_format user_log_format escape=json '{"time": "$msec", "ip": "$remote_addr", "ua": "$http_user_agent", "data": "$request_body"}';

    server {
        listen 8080;
        set $p_body "";
        location / {
            access_log /data/nginx/logs/user_defined.log user_log_format;
            echo_read_request_body;
            echo '';
        }
    }
}

```

> 使用 ngx_lua 模块

Lua 参考文档：https://www.nginx.com/resources/wiki/modules/lua/

``` xml
worker_processes  1;
error_log logs/error.log;
events {
    worker_connections 1024;
}
http {
    include       /usr/local/openresty/nginx/conf/mime.types;
    default_type  application/octet-stream;

    # 日志文件格式  access.log  nginx默认的日志文件
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent $host "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    # 日志文件格式 user_defined.log  数据会写到这个文件中
    log_format user_log_format escape=json '{"time": "$msec", "ip": "$remote_addr", "ua": "$http_user_agent", "data": "$request_body"}';

    server {
        listen 8080;
        set $p_body "";
        location / {
            lua_need_request_body on;                                                                                            
            content_by_lua 'local s = ngx.req.get_body_data()';
            
            access_log /data/nginx/logs/user_defined.log user_log_format;
            # echo '';	# 注意这个不能使用 echo 命令和 return 命令
        }
    }
}
```

还可以通过自定义的变量存放 request_body，然后再 log_format 中使用自定义变量打印。

``` xml
worker_processes  1;
error_log logs/error.log;
events {
    worker_connections 1024;
}
http {
    include       /usr/local/openresty/nginx/conf/mime.types;
    default_type  application/octet-stream;

    # 日志文件格式  access.log  nginx默认的日志文件
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent $host "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    # 日志文件格式 user_defined.log  数据会写到这个文件中
    log_format user_log_format escape=json '{"time": "$msec", "ip": "$remote_addr", "ua": "$http_user_agent", "data": "$p_body"}';

    server {
        listen 8080;
        set $p_body "";
        location / {
            lua_need_request_body on;
            content_by_lua '
                local p_body = ngx.req.get_body_data() or ""
                ngx.var.p_body = p_body
            ';
			access_log /data/nginx/logs/user_defined.log user_log_format;
            # echo '';	# 注意这个不能使用 echo 命令和 return 命令
        }
    }
}

```

可用于做 app 等数据上报，打印成 日志文件，使用 filebeat 收集到 elk 中，做数据分析。
