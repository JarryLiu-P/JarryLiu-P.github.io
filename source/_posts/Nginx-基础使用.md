---
title: Nginx-基础使用
date: 2019-04-30 11:19:54
tags: Nginx
---
### `Nginx` (engine x) 是一个高性能的HTTP和反向代理web服务器，同时也提供了IMAP/POP3/SMTP服务。
#### 常用场景
- [静态资源服务器](#1)
- [代理服务](#2)
- [跨站资源访问](#3)
- [适配PC与移动端环境](#4)
- [负载均衡调度](#5)
- [缓存服务](#6)
- [简单的访问控制](#7)
- [图片处理](#8)

#### 默认配置文件
> 全局块：`nginx` 全局配置。比如用户以及用户组 `user`，`nginx` 进程 `pid` 文件存放路径，日志存放路径 `error_log`，工作进程`worker process` 数等，也可以引入配置文件。

> `events` 块：服务器连接相关配置。比如进程的最大连接数，选取哪种事件驱动模型处理连接请求，是否允许同时接受多个网路连接，开启多个网络连接序列化等。

> `http` 块：可嵌套多个虚拟主机 `server`，配置代理，设置缓存，配置日志定义等。支持很多第三方模块的配置。比如文件引入，`mime-type` 定义，图片压缩，日志自定义，传输文件配置，设置连接超时时间，单个连接请求数等等。

> `server` 块：虚拟主机的相关配置。

> `location` 块：请求的路由相关配置，特殊页面的处理。

```
# {全局块}

# 设置用户组
#user  nobody;
# 配置用户或者组，默认为nobody nobody。
#user admin admin;  

# 设置工作进程的数量
worker_processes  1;

# 指定日志路径，日志级别包括 debug | info | notice | warn | error | alert 等
#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

# 指定进程文件
#pid        logs/nginx.pid;

events { # {event块}
    # 设置一个进程是否同时接受多个网络连接，默认为off
    #multi_accept on;
    # 事件驱动模型，select|poll|kqueue|epoll|resig|/dev/poll|eventport
    #use epoll;
    # 进程的最大连接数
    worker_connections  1024;
}

http { {#http块}
    # 文件拓展名查找集合
    include       mime.types;
    # 默认文件类型为文件流，匹配.*
    default_type  application/octet-stream;

    # 日志格式定义，main为日志别名
    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    # 取消服务日志
    #access_log off;
    # 指定日志输入文件
    #access_log  logs/access.log  main;

    # 调用 sendfile 系统传输文件，sendfile on 表示通过文件描述符直接拷贝，而不是用read()/write()
    sendfile        on;
    # tcp_nopush = on 会设置调用tcp_cork方法，这个也是默认的，结果就是数据包不会马上传送出去，等到数据包最大时，一次性的传输出去，这样有助于解决网络堵塞。
    #tcp_nopush     on;

    # 设置连接超时时间
    #keepalive_timeout  0;
    keepalive_timeout  65;

    # 是否启用 gzip 压缩，需要浏览器支持，需要解压缩
    #gzip  on;
    # 设置gzip所需的http协议最低版本 （HTTP/1.1, HTTP/1.0）
    #gzip_http_version 1.1;
    # 设置压缩级别，压缩级别越高压缩时间越长  （1-9）
    #gzip_comp_level 4;
    # 设置压缩的最小字节数， 页面Content-Length获取
    #gzip_min_length 1000;
    # 设置压缩文件的类型
    #gzip_types text/plain application/javascript text/css;

    server { #{server块}
        # 监听端口
        listen       80;
        # 虚拟主机名称
        server_name  localhost;

        # 设置编码字符集
        #charset koi8-r;

        # 指定日志输入文件
        #access_log  logs/host.access.log  main;

        # 监听请求路由
        location / { #{location块}
            # 查找根目录
            root   html;
            # 默认匹配
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        # 重定位服务器错误到静态页面 /50x.html
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


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

}
```
### `nginx` 内置变量
- $args ： #这个变量等于请求行中的参数，同 `$query_string`
- $content_length ： 请求头中的 `Content-length` 字段。
- $content_type ： 请求头中的 `Content-Type` 字段。
- $document_root ： 当前请求在 `root` 指令中指定的值。
- $host ： 请求主机头字段，否则为服务器名称。
- $http_user_agent ： 客户端 `agent` 信息
- $http_cookie ： 客户端 `cookie` 信息
- $limit_rate ： 这个变量可以限制连接速率。
- $request_method ： 客户端请求的动作，通常为 `GET` 或 `POST`。
- $remote_addr ： 客户端的 `IP` 地址。
- $remote_port ： 客户端的端口。
- $remote_user ： 已经经过 `Auth Basic Module` 验证的用户名。
- $request_filename ： 当前请求的文件路径，由 `root` 或 `alias` 指令与 `URI` 请求生成。
- $scheme ： `HTTP` 方法（如 `http` ，`https`）。
- $server_protocol ： 请求使用的协议，通常是 `HTTP/1.0` 或 `HTTP/1.1`。
- $server_addr ： 服务器地址，在完成一次系统调用后可以确定这个值。
- $server_name ： 服务器名称。
- $server_port ： 请求到达服务器的端口号。
- $request_uri ： 包含请求参数的原始 `URI`，不包含主机名，如：”/foo/bar.php?arg=baz”。
- $uri ： 不带请求参数的当前 `URI`，`$uri` 不包含主机名，如”/foo/bar.html”。
- $document_uri ： 与 `$uri` 相同

### `nginx` 基本命令
- nginx -t 检查配置文件是否有语法错误
- nginx -s reload 向主进程发送信号，重新加载配置文件
- nginx -s stop 快速关闭
- nginx -s quit 等待工作进程处理完成后关闭

### `location` 匹配规则（优先级递减）
|匹配符|含义|
|-:|:-|
|=|进行普通字符精确匹配。也就是完全匹配|
|^~|前缀匹配。如果匹配成功，则不再匹配其他location|
v~|表示执行一个正则匹配，区分大小写|
|~*|表示执行一个正则匹配，不区分大小写|
|/xxx/|常规字符串路径匹配|
|/|通用匹配，任何请求都会匹配到|

#### <span id="1">静态资源服务器<span>
常见的静态资源包括图片，文档，视频等文件，所以 `nginx` 常用来作为静态网站服务器，或者作为 `cdn` 资源服务器。
例如以下是一个前后端分离的工程的前端工程部署配置：
```
...
server {
    ...
    listen  8080;
    server_name localhost;
    root html/project;
    location / {
        // 按照优先级 '$uri' > '$uri/' > '/index.html' > '=404'依次匹配
        try_files $uri $uri/ /index.html =404;
        index  index.html index.htm;
    }
}
```
#### <span id="2">代理服务<span>
`nginx` 用作代理服务器可以作为正向代理或者反向代理，而反向代理正是跨站资源访问的解决方案之一。
如下是正向代理配置：
```
...
server {
    ...
    listen  8080;
    server_name localhost;
    # DNS 域名解析服务器
    resolver 8.8.8.8;
    # 解析超时时间
    resolver_timeout 5s;

    location / {
        proxy_pass   $scheme://$http_host/$request_uri;
        proxy_set_header Host $http_host;

        proxy_buffers 256 4k;
        proxy_max_temp_file_size 0;

        proxy_connect_timeout 30;

        proxy_cache_valid 200 302 10m;
        proxy_cache_valid 301 1h;
        proxy_cache_valid any 1m;
    }
}
```
一次代理，直接在shell执行：
```
export http_proxy=http://x.x.x.x:8080
```

永久使用：
```
vim ~/.bashrc
export http_proxy=http://x.x.x.x:8080
source .bashrc # 刷新以加载变量
```

#### <span id="3">跨站资源访问<span>
反向代理配置如下：
```
...
server {
    ...
    listen       80;
    server_name  localhost;

    location /api {
        # 代理转发
        proxy_pass http://localhost:8081;
        # 路径重写（如果需要）
        # rewrite  /api/(.*)  /$1  break;

        # 请求host传给后端
        proxy_set_header Host $http_host;
        # 设置用户ip地址
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $remote_addr;

        proxy_buffers 256 4k;
        proxy_max_temp_file_size 0;

        # 代理超时时间
        proxy_connect_timeout 30;

        # 当请求服务器出错去寻找其他服务器
        proxy_next_upstream error timeout invalid_header http_500 http_502 http_503; 
    }

}   
```
#### <span id="4">适配PC与移动端环境<span>
`nginx` 可以通过内置变量 `$http_user_agent`，获取到请求客户端的 `userAgent`，从而知道用户处于移动端还是PC，进而控制重定向到H5站还是PC站。
```
...
server {
    ...
    listen       80;
    server_name  localhost;
    location / {
        # 移动、pc设备适配
        if ($http_user_agent ~* '(Android|webOS|iPhone|iPod|BlackBerry)') {
            set $mobile_request '1';
        }
        if ($mobile_request = '1') {
            rewrite ^.+ http://mysite-base-H5.com;
        }
    }
}
```
#### <span id="5">负载均衡调度<span>
RR（round robin：轮询）
```
...
server {
    ...
    upstream web_servers {  
        server localhost:8081;  
        server localhost:8082;  
    }
    server {
        ...
        listen       80;
        server_name  localhost;
        #access_log  logs/host.access.log  main;


        location / {
            proxy_pass http://web_servers;
            # 必须指定Header Host
            proxy_set_header Host $host:$server_port;
        }
    }
}
```
按权重分配
```
upstream test {
    server localhost:8081 weight=1;
    server localhost:8082 weight=3;
    server localhost:8083 weight=4 backup;
}
```
按地址分配：ip_hash
```
upstream test {
    ip_hash;
    server localhost:8080;
    server localhost:8081;
}
```
后端服务器的响应时间来分配：fair(第三方)
```
upstream backend {
    fair;
    server localhost:8080;
    server localhost:8081;
}
```
按访问url的hash结果来分配：url_hash(第三方)
```
upstream backend {
    hash $request_uri;
    hash_method crc32;
    server localhost:8080;
    server localhost:8081;
}
```

#### <span id="6">缓存服务<span>
对扩展名为 `gif`，`jpg`，`jpeg`，`png`，`bmp`，`swf`，`js`，`css` 的图片，`flash`，`javascript`，`css` 文件开启 `Web` 缓存,其他文件不缓存。配置文件如下：
```
user  www;
worker_processes  8;
  
events {
    worker_connections  65535;
}
  
http {
    include       mime.types;
    default_type  application/octet-stream;
    charset utf-8;
 
    log_format  main  '$http_x_forwarded_for $remote_addr $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_cookie" $host $request_time';
    sendfile       on;
    tcp_nopush     on;
    tcp_nodelay    on;
    keepalive_timeout  65;
 
    #要想开启nginx的缓存功能，需要添加此处的两行内容！
    #设置Web缓存区名称为cache_one,内存缓存空间大小为500M,缓存的数据超过1天没有被访问就自动清除;访问的缓存数据,硬盘缓存空间大小为30G
    proxy_cache_path /usr/local/nginx/proxy_cache_path levels=1:2 keys_zone=cache_one:500m inactive=1d max_size=30g;
 
    #创建缓存的时候可能生成一些临时文件存放的位置
    proxy_temp_path /usr/local/nginx/proxy_temp_path;
 
    fastcgi_connect_timeout 3000;
    fastcgi_send_timeout 3000;
    fastcgi_read_timeout 3000;
    fastcgi_buffer_size 256k;
    fastcgi_buffers 8 256k;
    fastcgi_busy_buffers_size 256k;
    fastcgi_temp_file_write_size 256k;
    fastcgi_intercept_errors on;
  
     
    client_header_timeout 600s;
    client_body_timeout 600s;
  
    client_max_body_size 100m;             
    client_body_buffer_size 256k;           
  
    gzip  on;
    gzip_min_length  1k;
    gzip_buffers     4 16k;
    gzip_http_version 1.1;
    gzip_comp_level 9;
    gzip_types       text/plain application/x-javascript text/css application/xml text/javascript application/x-httpd-php;
    gzip_vary on;
  
 
    upstream LB-WWW {
      ip_hash;
      server 192.168.1.101:80 max_fails=3 fail_timeout=30s;     #max_fails = 3 为允许失败的次数，默认值为1
      server 192.168.1.102:80 max_fails=3 fail_timeout=30s;     #fail_timeout = 30s 当max_fails次失败后，暂停将请求分发到该后端服务器的时间
      server 192.168.1.118:80 max_fails=3 fail_timeout=30s;
    }
   
    server {
        listen       80;
        server_name  www.wangshibo.com;
        index index.html index.php index.htm;
        root /var/www/html;
    
        access_log  /usr/local/nginx/logs/www-access.log main;
        error_log  /usr/local/nginx/logs/www-error.log;
    
        location / {
            proxy_pass http://LB-WWW;
            proxy_redirect off ;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header REMOTE-HOST $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            #跟后端服务器连接超时时间，发起握手等候响应时间
            proxy_connect_timeout 300;
            #后端服务器回传时间，就是在规定时间内后端服务器必须传完所有数据
            proxy_send_timeout 300;
            #连接成功后等待后端服务器的响应时间，已经进入后端的排队之中等候处理
            proxy_read_timeout 600;
            #代理请求缓冲区,会保存用户的头信息以供nginx进行处理
            proxy_buffer_size 256k;
            #同上，告诉nginx保存单个用几个buffer最大用多少空间
            proxy_buffers 4 256k;
            #如果系统很忙时候可以申请最大的proxy_buffers
            proxy_busy_buffers_size 256k;
            #proxy缓存临时文件的大小
            proxy_temp_file_write_size 256k;
            proxy_next_upstream error timeout invalid_header http_500 http_503 http_404;
            proxy_max_temp_file_size 128m;
        }
    
        location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|js|css)$ {         
            #使用Web缓存区cache_one，已在nginx.conf的缓存配置中命名的。
            proxy_cache cache_one ;
            #对不同HTTP状态码缓存设置不同的缓存时间
            proxy_cache_valid 200 304 12h ;
            proxy_cache_valid 301 302 1m ;
            proxy_cache_valid any 1m ;
            #设置Web缓存的Key值,Nginx根据Key值md5哈希存储缓存,这里根据"域名,URI,
            #参数"组合成Key
            proxy_cache_key $host$uri$is_args$args;
        }
    
        #用于清除缓存的url设置
        #假设一个URL为http://www.wangshibo.com/test.gif,那么就可以通过访问http://www.wangshibo.com/purge/test.gif清除该URL的缓存。
        location ~ /purge(/.*) {
            #设置只允许指定的IP或IP段才可以清除URL缓存
            allow 127.0.0.1 ;
            allow 192.168.0.0/16 ;
            deny all ;
            proxy_cache_purge cache_one $host$1$is_args$args ;
        }
    }
}
```

#### <span id="7">简单的访问控制<span>
`deny` 和 `allow` 是 `ngx_http_access_module` 模块（已内置）中的语法。采用的是从上到下匹配方式，匹配到就跳出不再继续匹配。上述配置的意思就是，首先禁止 `192.168.1.100` 访问，然后允许 `192.168.1.10-200` ip段内的访问（排除 `192.168.1.100`），同时允许 `10.110.50.16` 这个单独ip的访问，剩下未匹配到的全部禁止访问。实际生产中，经常和 `ngx_http_geo_module` 模块（可以更好地管理ip地址表，已内置）配合使用。
```
...
server {
    ...
    location / {
        deny  192.168.1.100;
        allow 192.168.1.10/200;
        allow 10.110.50.16;
        deny  all;
    }
}
```
#### <span id="8">图片处理<span>
满足日常对图片的裁剪/缩放/旋转/图片品质等处理需求。要用到 `ngx_http_image_filter_module` 模块。这个模块是非基本模块，需要安装。 下面是图片缩放功能部分的 `nginx` 配置：
```
...
server {
    ...
    # 图片缩放处理
    # 这里约定的图片处理url格式：以 mysite-base.com/img/路径访问
    location ~* /img/(.+)$ {
        #图片服务端储存地址
        alias /Users/cc/Desktop/server/static/image/$1;
        #图片宽度默认值
        set $width -;
        #图片高度默认值
        set $height -;
        if ($arg_width != "") {
            set $width $arg_width;
        }
        if ($arg_height != "") {
            set $height $arg_height;
        }
        #设置图片宽高
        image_filter resize $width $height;
        #设置Nginx读取图片的最大buffer
        image_filter_buffer 10M;
        #是否开启图片图像隔行扫描
        image_filter_interlace on;
        #图片处理错误提示图，例如缩放参数不是数字
        error_page 415 = 415.png; 
    }
}
```

### 参考网站以及文章
[nginx官网](https://nginx.org/)

[Nginx与前端开发](https://juejin.im/post/5bacbd395188255c8d0fd4b2)

[前端想要了解的Nginx](https://juejin.im/post/5cae9de95188251ae2324ec3)

[Nginx常见使用场景-WEB服务(四)](https://www.jianshu.com/p/577d022f5693)

[nginx应用场景](https://blog.csdn.net/vbirdbest/article/details/80913319)

[Nginx 之六： Nginx服务器的正向及反向代理功能](https://www.cnblogs.com/zhang-shijie/p/5498623.html)

[nginx之正向代理](https://www.cnblogs.com/cuishuai/p/8073748.html)

[nginx的web缓存服务环境部署记录](https://www.cnblogs.com/kevingrace/p/6198287.html)