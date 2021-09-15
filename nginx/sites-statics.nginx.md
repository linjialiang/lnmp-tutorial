```conf
server
{
    # 支持gbk编码
    charset gbk;
    # 支持utf8编码，默认值
    charset utf-8;
    # 同时支持 utf8 和 gbk 编码
    charset ISO-88509-1;

    listen 80;
    server_name example.com www.example.com;
    root /server/www/www_example_com;

    access_log /server/logs/nginx/www_example_com.log;
    index index.html;

    # 支持的请求方式
    if ($request_method !~* GET|POST|OPTIONS)
    {
        return 403;
    }

    # 开启跨域访问资源
    location /
    {
        add_header Access-Control-Allow-Origin *;
        add_header Access-Control-Allow-Methods 'GET, POST, OPTIONS';
        add_header Access-Control-Allow-Headers 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Authorization';

        if ($request_method = 'OPTIONS')
        {
            return 204;
        }
    }

    location ~ /\.ht
    {
        deny all;
    }
}
```
