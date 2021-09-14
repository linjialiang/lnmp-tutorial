# Nginx 篇

Nginx 是现如今性能最强劲的 Web 服务器及反向代理服务器

## 涉及软件包

Nginx 编译安装，所涉及到的软件包具体如下：

| 源码及地址                                                                     |
| ------------------------------------------------------------------------------ |
| [nginx-1.20.1.tar.gz](http://nginx.org/en/download.html)                       |
| [openssl-1.1.1l.tar.gz](https://www.openssl.org/source/)                       |
| [pcre-8.45.tar.gz](https://sourceforge.net/projects/pcre/files/pcre/)          |
| ~~[perl-5.34.0.tar.gz](https://www.activestate.com/products/perl/downloads/)~~ |
| [zlib-1.2.11.tar.gz](http://www.zlib.net/)                                     |

> 提示：Nginx 官方说明里，pcre 外库支持的版本是 `4.4 — 8.43`

## 构建前

-   创建 nginx 用户及用户组

    ```sh
    $ useradd -c 'This is the nginx service user' -u 2002 -s /usr/sbin/nologin -d /server/www -M -U nginx
    ```

-   查看当前版本全部构建参数

    ```sh
    $ cd /package/lnmp/nginx-1.20.1
    $ ./configure --help
    ```

### 模块开发环境依赖

-   构建 http_xslt_module 模块需要 `libxml2/libxslt` 开发库

    ```sh
    $ apt install libxml2-dev libxslt1-dev
    ```

-   构建 http_image_filter_module 模块需要 `GD` 开发库

    ```sh
    $ apt install libgd-dev
    ```

-   构建 http_geoip_module 模块需要 `libgeoip` 开发库

    ```sh
    $ apt install libgeoip-dev
    ```

-   安装 g++

    gcc 一般自带，g++ 需要自己安装

    ```sh
    $ apt install g++
    ```

## 构建指令

```sh
$ mkdir /package/lnmp/nginx-1.20.1/build_nginx
$ mkdir -p /server/nginx
$ cd /package/lnmp/nginx-1.20.1
$ ./configure --prefix=/server/nginx \
--builddir=/package/lnmp/nginx-1.20.1/build_nginx \
--user=nginx \
--group=nginx \
--error-log-path=/server/logs/nginx/error.log \
--http-log-path=/server/logs/nginx/access.log \
--pid-path=/server/run/nginx/nginx.pid \
# 核心功能模块
--with-threads \
--with-file-aio \
# 启用http功能模块
--with-http_ssl_module \
--with-http_v2_module \
--with-http_realip_module \
--with-http_addition_module \
--with-http_xslt_module \
--with-http_image_filter_module \
--with-http_geoip_module \
--with-http_sub_module \
--with-http_dav_module \
--with-http_flv_module \
--with-http_mp4_module \
--with-http_gunzip_module \
--with-http_gzip_static_module \
--with-http_auth_request_module \
--with-http_random_index_module \
--with-http_secure_link_module \
--with-http_degradation_module \
--with-http_slice_module \
--with-http_stub_status_module \
# 启用邮箱服务
--with-mail \
--with-mail_ssl_module \
# 启用负载均衡服务
--with-stream \
--with-stream_ssl_module \
--with-stream_realip_module \
--with-stream_geoip_module \
--with-stream_ssl_preread_module \
# 外库路径
--with-pcre=/package/lnmp/pcre-8.45 \
--with-pcre-jit \
--with-zlib=/package/lnmp/zlib-1.2.11 \
--with-openssl=/package/lnmp/openssl-1.1.1l \
# 开启调试
--with-debug
```

### 开始构建

```sh
$ make -j4
$ make install
```

到此，nginx 安装完毕！

## 配置 nginx

nginx 的配置原理，在这里不做过多讲解，直接给参考文件：

1. [nginx.conf](./nginx/nginx.conf.md)

    ```text
    描述：nginx 主配置文件
    数量：1个
    路径：/server/nginx/conf/nginx.conf
    操作：替换
    ```

2. [fastcgi-tp.conf](./nginx/fastcgi-tp.conf.md)

    ```text
    描述：tp6 站点配置模版
    数量：按需新建，允许多个，命名需要区分
    路径：/server/nginx/conf/fastcgi-tp.conf
    操作：替换
    ```

3. [tp-sites.nginx](./nginx/tp-sites.nginx.md)

    ```text
    描述：静态站点配置模版
    数量：按需新建，允许多个，命名需要区分
    路径：/server/sites/*.nginx
    操作：替换
    ```

4. [public-sites.nginx](./nginx/public-sites.nginx.md)

    ```text
    描述：静态站点配置模版（涉及跨域）
    数量：按需新建，允许多个，命名需要区分
    路径：/server/sites/*.nginx
    操作：替换
    ```

5. [gbk-sites.nginx](./nginx/gbk-sites.nginx.md)

    ```text
    描述：修改默认编码格式为 gbk
    数量：按需新建，允许多个
    路径：/server/sites/*.nginx
    操作：替换
    ```

6. [utf8-gbk-sites.nginx](./nginx/gbk-sites.nginx.md)

    ```text
    描述：同时支持utf8和gbk编码
    数量：按需新建，允许多个
    路径：/server/sites/*.nginx
    操作：替换
    ```
