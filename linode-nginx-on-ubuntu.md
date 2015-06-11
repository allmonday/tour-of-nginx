## install 

```shell
add-apt-repository ppa:nginx/stable
apt-get update
apt-get upgrade --show-upgraded
```

```shell
apt-get install nginx
```

```shell
/etc/init.d/nginx start
```

## create init script to manage nginx

```shell
wget -O init-deb.sh http://www.linode.com/docs/assets/1139-init-deb.sh
mv init-deb.sh /etc/init.d/nginx
chmod +x /etc/init.d/nginx
/usr/sbin/update-rc.d -f nginx defaults 
```


## Basic Nginx Configuration
[link](https://www.linode.com/docs/websites/nginx/basic-nginx-configuration)

config: `/etc/nginx/nginx.conf`

备份日志: `cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.$(date "+%b_%d_%Y_%H.%M.%S")`

`service nginx reload` 
`service nginx start` 
`service nginx stop` 


## syntax

```
user www-data;
worker_processes 4;
pid /run/nginx.pid;

events {
        worker_connections 768;
        # multi_accept on;
}
```

## understand how nginx works

### defining the directives
- `user`: 启动nginx 的用户
- `worker_process` 线程数, 或者说实例数
- `pid` 运行nginx 的进程号

### http
`/etc/nginx/nginx.conf`

```
http {
    ##
    # Basic Settings
    ##

    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;
    # server_tokens off;

    # server_names_hash_bucket_size 64;
    # server_name_in_redirect off;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    ##
    # Logging Settings
    ##

    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    ##
    # Gzip Settings
    ##

    gzip on;
    gzip_disable "msie6";
```

- `include` 导入其他文件, 或配置
- `gzip` 实时压缩  [full definitions](http://nginx.org/en/docs/http/ngx_http_gzip_module.html)

### server (virtual domains configuration)

`/etc/nginx/sites-available/default`

```
server {
        listen 80 default_server;
        listen [::]:80 default_server ipv6only=on;

        root /usr/share/nginx/html;
        index index.html index.htm;

        # Make site accessible from http://localhost/
        server_name localhost;

        location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ /index.html;
                # Uncomment to enable naxsi on this location
                # include /etc/nginx/naxsi.rules
        }
}
```

`http` 通过 `include /etc/nginx/sites-enabled/*;` 载入所有配置的 server
通常会有个符号链接到 `/etc/nginx/sites-available/`, 请在这里修改
