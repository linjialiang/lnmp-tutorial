```conf
location ~ ^.+\.(?:html?)$ {
    expires      30d;   # 缓存30天
    break;
}

location ~ ^.+\.(?:txt|js|css|gif|jpg|jpeg|png|ico|xlsx?)$ {
    access_log  off;    # 关闭访问日志
    log_not_found off;  # 关闭错误日志
    expires      30d;   # 缓存30天
    break;
}
```
