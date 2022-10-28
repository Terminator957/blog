---
title: Nginx配置
date: 2022-09-09 17:11:29
tags: 
- Linux
categories:
- Linux
cover: /img/linux.png

---

#### 1、Nginx指令

```bash
cd /usr/local/nginx/sbin/
./nginx // 启动nginx，没有提示消息启动成功
  
nginx -s stop // stop表示立即停止nginx,不保存相关信息
  
nginx -s quit // 优雅地停止Nginx服务（即处理完所有请求后再停止服务）
  
nginx -s reload // 修改配置后重新加载生效
  
killall nginx // 杀死所有nginx进程
```

#### 2、Nginx配置

个人服务器nginx配置

```yml
http {
    include       mime.types; #文件扩展名与文件类型映射表
    default_type  application/octet-stream; #访问到未定义的扩展名的时候，就默认为下载该文件

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        #SSL 默认访问端口号为 443
        listen 443 ssl;
        #请填写绑定证书的域名
        server_name xiuji957.cn; 
        #请填写证书文件的相对路径或绝对路径
        ssl_certificate  /usr/local/nginx/conf/XXXX; 
        #请填写私钥文件的相对路径或绝对路径
        ssl_certificate_key /usr/local/nginx/conf/XXXX; 
        ssl_session_timeout 5m;
        #请按照以下套件配置，配置加密套件，写法遵循 openssl 标准。
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
        #请按照以下协议配置
        ssl_protocols TLSv1.2 TLSv1.3;
        ssl_prefer_server_ciphers on;
        location / {
            #网站主页路径。此路径仅供参考，具体请您按照实际目录操作。 
            #例如，您的网站主页在 Nginx 服务器的 /etc/www 目录下，则请修改 root 后面的 html 为 /etc/www。
            root   项目路径;
            index  index.html index.htm;

        ##################### 跨域配置开始 ############################

            #nginx 配置文件 server，解决跨域问题
            set $cors_origin "";
            if ($http_origin ~* "^http://web.domain.com$") {
                    set $cors_origin $http_origin;
            }
            if ($http_origin ~* "^https://web.domain.com$") {
                    set $cors_origin $http_origin;
            }
	    add_header 'Access-Control-Allow-Origin' '*'; #允许来自所有的访问地址
            add_header 'Access-Control-Allow-Credentials' 'true'; #是否携带cookie
            add_header 'Access-Control-Allow-Headers' 'X-Requested-With,token,Content-Type,Access-Token,x-token'; #允许的header名称
            add_header 'Access-Control-Allow-Methods' 'GET, PUT, POST, DELETE, OPTIONS'; #支持请求方式
            add_header 'Access-Control-Max-Age' 86400; #预检测请求的缓存时间另外浏览器控制面板的Disable cache不勾选才可生效
            add_header 'Access-Control-Allow-Headers' 'Content-Type,*';

            #nginx 伪静态 location
            if ($request_method = 'OPTIONS') { #拦截预检测请求
                return 200; 
            }
        }

        ##################### 跨域配置结束 ############################
    }
    server {
        listen       80;  # 目前监听的端口号
        server_name  localhost; # 主机名称
        #请填写绑定证书的域名
       	server_name ####; 
        #把http的域名请求转成https
        return 301 https://$host$request_uri; 

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / { #表示默认首页
            root   /opt/blog/public/;
            index  index.html index.htm;

        ##################### 跨域配置开始 ############################

            #nginx 配置文件 server，解决跨域问题
            set $cors_origin "";
            if ($http_origin ~* "^http://web.domain.com$") {
                    set $cors_origin $http_origin;
            }
            if ($http_origin ~* "^https://web.domain.com$") {
                    set $cors_origin $http_origin;
            }
	    add_header 'Access-Control-Allow-Origin' '*'; #允许来自所有的访问地址
            add_header 'Access-Control-Allow-Credentials' 'true'; #是否携带cookie
            add_header 'Access-Control-Allow-Headers' 'X-Requested-With,token,Content-Type,Access-Token,x-token'; #允许的header名称
            add_header 'Access-Control-Allow-Methods' 'GET, PUT, POST, DELETE, OPTIONS'; #支持请求方式
            add_header 'Access-Control-Max-Age' 86400; #预检测请求的缓存时间另外浏览器控制面板的Disable cache不勾选才可生效
            add_header 'Access-Control-Allow-Headers' 'Content-Type,*';

            #nginx 伪静态 location
            if ($request_method = 'OPTIONS') { #拦截预检测请求
                return 200; 
            }
        }

        ##################### 跨域配置结束 ############################

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
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

#### 3、查看端口号占用

```bash
sudo lsof -i // 查看所有正在使用的端口
  
sudo lsof -i -P | grep -i "listen" // 查看端口监听情况
  
sudo lsof -i:8080 // 查看特定端口号
  
kill PID // 查询到之后通过PID杀掉进程
```

