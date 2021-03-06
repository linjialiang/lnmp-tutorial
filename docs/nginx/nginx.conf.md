```conf
worker_processes auto;

worker_shutdown_timeout 30s;
worker_rlimit_core 50M;
working_directory /tmp/;

worker_rlimit_nofile 10240;

events
{
    worker_connections 10240;
    worker_aio_requests 1024;
}

http
{
    include mime.types;
    default_type application/octet-stream;

    charset utf-8;

    autoindex off;
    autoindex_exact_size on;
    autoindex_localtime on;

    # 启用 gzip 压缩，自定义配置文件
    include gzip.conf;
    sendfile on;

    # 在响应头中隐藏 Nginx 版本号
    server_tokens off;
    # 隐藏fastcgi的头信息，如：php-fpm
    fastcgi_hide_header X-Powered-By;
    # 隐藏反向代理服务器头信息，如：httpd
    proxy_hide_header X-Powered-By;
    # nginx 启用 TLSv1.3 加密传输协议
    ssl_protocols TLSv1.3;
    # 解决点击劫持漏洞，页面只能被本站页面嵌入到 iframe 或者 frame 中
    add_header X-Frame-Options sameorigin always;

    # 加载请求限制, http 区域
    include limit_req_http.conf;

    server
    {
        listen 80;
        server_name localhost;
        root /server/default;

        access_log /server/logs/nginx/localhost.log;
        index index.php;

        # 站点仅允许温州电信ip段访问
        allow 39.186.0.0/16;
        allow 60.181.0.0/16;
        deny all;

        # 设置站点仅至允许 GET、POST 请求
        if ($request_method !~* GET|POST)
        {
            return 403;
        }

        # 启用缓存设置，自定义配置文件
        include cache.conf;

        location / {
            # 加载请求限制，server区域，需要结合http区块
            include limit_req_server.conf;
        }

        location ~ \.php
        {
            fastcgi_pass unix:/server/run/php/default.sock;

            include fastcgi.conf;
        }

        location ~ /\.
        {
            deny all;
        }
    }

    include /server/sites/*.conf;
}
```
