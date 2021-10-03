```conf
server
{
    # == 编码3选1，通常建议全部使用 utf-8
    # 支持gbk编码
    # charset gbk;
    # 支持utf8编码，http区块已经设置
    # charset utf-8;
    # 同时支持 utf8 和 gbk 编码
    # charset ISO-88509-1;

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

    # 启用缓存设置，自定义配置文件
    include cache.conf;

    location /
    {

        # 加载请求限制，server区域，需要结合http区块
        include limit_req_server.conf;

        # 开启跨域访问资源 == start
        add_header Access-Control-Allow-Origin *;
        add_header Access-Control-Allow-Methods 'GET, POST, OPTIONS';
        add_header Access-Control-Allow-Headers 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Authorization';

        if ($request_method = 'OPTIONS')
        {
            return 204;
        }
        # 开启跨域访问资源 == end
    }

    location ~ /\. {
        deny all;
    }
}
```
