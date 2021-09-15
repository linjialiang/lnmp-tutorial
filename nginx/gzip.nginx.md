```conf
# 开启gzip压缩
gzip on;
# 压缩级别，1-9
gzip_comp_level 5;
# ie6之前的浏览器不开启gzip压缩
gzip_disable msie6;
# 文件小于100k，不开启gzip压缩
gzip_min_length 1k;
# 指定文件类型启用gzip压缩，强制开启text/html
gzip_types text/css text/xml application/javascript application/json

```
