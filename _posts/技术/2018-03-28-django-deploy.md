---
layout: post
title: Nginx + Supervisor + uWSGI 部署 Django 服务
category: 技术
tags: Django
---
这一篇介绍使用 Nginx + Supervisor + uWSGI 部署 Django 服务。 

为了保证服务器的纯净，不用virtualenv。

下面演示一下，我们的平台的部署

# 下载工程

centos的/var目录下新建www目录

    cd /var
    sudo mkdir www
    sudo chmod a+w www # 所有用户具有写权限
    
一般我们的web工程都放在 `/var/www`下面，放在`/var/www/`目录下。 

	cd /var/www/jed/
	git clone https://github.com/xxx/jed.git

在工程目录下，有以下文件：

    ├── jed/
            ├── nginx.conf
            ├── supervisor.conf
            ├── uwsgi.ini
            └── requirements.txt

包含了下面所有的用到的配置文件.

    pip install -r requirments.tx # 安装工程相关依赖

# 配置uWsgi

uWsgi

通过pip来安装
    
    sudo yum install python-devel # python开发相关
    sudo pip install uwsgi
    sudo yum -y install mysql-devel # python操作mysql 

执行下面的命令,测试uWsgi+Django是否能正常启动Web服务

    # 启动web服务
    uwsgi --http-socket 0.0.0.0:59152 --chdir /var/www/jed/ --wsgi-file jed/wsgi.py --master --processes 4 --threads 2 --stats 0.0.0.0:9191

然后打开浏览器访问: http://你的服务器ip:3031, 看能否访问网站。 测试通过后,可以结束上面的进程。
或者直接本地使用curl命令来访问
    
    curl 192.168.1.100:59152 # 可以访问到该网页

uWsgi的具体配置如下:

    [uwsgi]
    chdir=/var/www/jed/
    # py-autoreload=3  #实现和django自带server一样更新文件自动重启功能
    module=jed.wsgi:application
    master=True
    pidfile=/tmp/jed.pid
    vacuum=True   # clear environment on exit
    socket=127.0.0.1:59152
    processes=16    # 启动16个进程
    harakiri=20 # respawn processes taking more than 20 seconds
    max-requests=5000  # 请求5000次后重启
    # daemonize=/var/log/yz/jed.log # 不使用daemon模式，防止supervisor自动重启

需要在/var/log目录下创建yz目录
    
    sudo mkdir /var/log/yz
    sudo a+w /var/log/yz


# 配置Supervisor

`Supervisor`是用来将`uWsgi`做成一个守护进程的，通过`supervisor`命令或者网页服务也能启动和停止`uUwsgi`.

安装和运行`Supervisor`, 也可参考官方文档: [http://supervisord.org/](http://supervisord.org/) 。
    
    sudo pip install supervisor # 安装
    su - root # 切换到root用户
    echo_supervisord_conf > /etc/supervisord.conf # 在root用户下，写配置文件

修改supervisord.conf 配置文件，在文件末尾加上如下语句：

    [include]
    files = /etc/supervisord.conf.d/*.conf

启动supervisor

    sudo supervisord -c /etc/supervisord.conf # 启动
    ps aux|grep supervisor # 查看进程是否启动


下面开始配置`Supervisor`。

    # 使用软链接, 载入 `supervisor` 的配置
    sudo mkdir /etc/supervisord.conf.d # 创建配置目录
    cd /etc/supervisord.conf.d/
    sudo ln -s /var/www/jed/supervisord.conf jed.localhost.conf 
    
    # supervisor 更新配置,并启动进程
    sudo supervisorctl -c /etc/supervisord.conf
    > reread
    > update
    > status

这个时候可以看到服务已经启动起来了。

`Supervisor`的配置如下:
    
    [program:uwsgi-jed.localhost]
    # 确保uwsgi命令的路径是对的
    command=/usr/bin/uwsgi --ini /var/www/jed/uwsgi.ini
    directory=/var/www/jed
    umask=022
    # 以ripple用户运行
    user=jed
    startsecs=0
    stopwaitsecs=0
    autostart=true
    autorestart=true
    # 注意确保路径存在
    stdout_logfile=/var/log/yz/jed.stdout.log
    stderr_logfile=/var/log/yz/jed.stderr.log
    stopsignal=QUIT
    killasgroup=true
    
    [supervisord]

如果不想使用`Supervisor`也可以，只要把上面`uwsgi.ini`文件中关于最后一行关于`daemonize`的注释去掉, 直接用以下命令启动`uWsgi`。

    /usr/bin/uwsgi --ini /var/www/jed/uwsgi.ini


# 配置Nginx

使用软链接, 将 `nginx.conf` 写入到 `nginx` 的配置中:

    cd /etc/nginx/conf.d/
    sudo ln -s /var/www/jed/nginx.conf jed.localhost.conf

然后重启 `nginx`

    sudo /usr/sbin/nginx # 开启nginx
    sudo /usr/sbin/nginx -s reload # 重新加载nginx
    

`nginx.conf` 的配置内容如下:

    upstream jed_uwsgi_backend {
    	# django工程在59152端口启动
        server 127.0.0.1:59152;
    }
    
    server {
        listen   9001;
        # 通过该域名访问
        server_name jed.localhost;
    
    	# 禁止访问git文件
        location ^~ /.git {
            deny all;
        }
    	
    	# django的静态文件，直接通过nginx访问，不走uWsgi。 
    	# 静态文件的目录是：/var/www/jed/jed/static/
        location ^~ /static {
            root   /var/www/jed/jed; 
            index  index.html;
            expires 1M;
            access_log off;
            add_header Cache-Control "public";
        }
    	# 非静态文件请求都走uWsgi，具体端口在upstream配置
        location / {
            proxy_next_upstream error timeout http_500 http_503;
            proxy_connect_timeout 4000ms;
            proxy_read_timeout    30s;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
            proxy_set_header X-Rewrite-URL $request_uri;
            client_max_body_size 10m;
    
    		# 通过 upstream 
            uwsgi_pass  jed_uwsgi_backend;
            include uwsgi_params;
        }
    
    }
 
# 配置域名

 在你的域名提供服务商处,增加一条域名解析A记录,将域名 `jed.localhost` 解析到 你的服务器ip。
 成功后。
 
 这个时候打开 [http://jed.localhost:9001](http://jed.localhost:9001) ,就能成功访问网站了。
 
 