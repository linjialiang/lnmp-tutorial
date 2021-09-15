```conf
location ~ ^.+\.(?:php|py|jsp|asp)$ {
	expires      off;
}


location ~ ^.+\.(?:js|css|gif|jpg|jpeg|png|ico|xlsx?|html?)$ {
    access_log  off;
    log_not_found off;  #
    expires      30d;   #缓存30天
}

location ~(favicon.ico) {
    log_not_found off;
    expires 99d;
    break;
}
location ~(robots.txt) {
    access_log  off;
    log_not_found off;
    expires 1d;
    break;
}

```
