```sh
server
{
    listen 80;
    server_name example.com www.example.com;
    root /server/www/www_example_com;

    access_log /server/logs/nginx/www_example_com.log;
    index index.html index.php;

    # 设置站点仅至允许 GET、POST 请求
    if ($request_method !~* GET|POST)
    {
        return 403;
    }

    # 启用缓存设置，自定义配置文件
    include cache.conf;

    location /
    {
        # 加载请求限制，server区域，需要结合http区块
        include limit_req_server.conf;

        if (!-e $request_filename)
        {
            rewrite ^(.*)$ /index.php?s=/$1 last;
            break;
        }

        # try_files $uri $uri/ /index.php$uri?$query_string;
    }

    # types {
    #     application/php php py jsp asp;
    # }

    location ~ \.php
    {
        fastcgi_pass unix:/server/run/php/php-fpm.sock;
        fastcgi_index index.php;
        fastcgi_split_path_info ^(.+\.php)(.*)$;

        include fastcgi-tp.conf;
    }

    location ~ /\.ht
    {
        deny all;
    }
}
```
