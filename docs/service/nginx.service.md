# Nginx 系统单元文件

路径：/usr/lib/systemd/system/nginx.service

```sh
[Unit]
Description=nginx-1.20.1
Wants=mariadb.service
Wants=php-fpm.service
After=network.target

[Service]
Type=forking
PIDFile=/server/run/nginx/nginx.pid
ExecStart=/server/nginx/sbin/nginx
ExecReload=/server/nginx/sbin/nginx -s reload
ExecStop=/server/nginx/sbin/nginx -s quit
Restart=on-abort

[Install]
WantedBy=multi-user.target
```
