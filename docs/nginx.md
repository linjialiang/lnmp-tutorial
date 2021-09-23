# Nginx 篇

Nginx 是现如今性能最强劲的 Web 服务器及反向代理服务器

## 涉及软件包

Nginx 编译安装，所涉及到的软件包具体如下：

| 源码及地址                                                            |
| --------------------------------------------------------------------- |
| [nginx-1.20.1.tar.gz](http://nginx.org/en/download.html)              |
| [openssl-1.1.1l.tar.gz](https://www.openssl.org/source/)              |
| [pcre-8.45.tar.gz](https://sourceforge.net/projects/pcre/files/pcre/) |
| [zlib-1.2.11.tar.gz](http://www.zlib.net/)                            |

> 提示：Nginx 官方说明里，pcre 外库支持的版本是 `4.4 — 8.43`

## 构建前

-   创建 nginx 用户及用户组

    ```sh
    $ useradd -c 'This is the nginx service user' -u 2002 -s /usr/sbin/nologin -d /server/www -M -U nginx
    ```

-   创建必要目录，并设置用户

    ```sh
    $ mkdir -p /server/nginx /package/lnmp /server/run/nginx /server/logs/nginx
    # root下启动nginx主程序，由root用户管理即可，这样更加安全
    # 如果终端是nginx用户启动，则目录需要设置为nginx用户
    $ chown nginx /server/run/nginx /server/logs/nginx
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

    本次没有构建该模块

-   构建 http_image_filter_module 模块需要 `GD` 开发库

    ```sh
    $ apt install libgd-dev
    ```

    本次没有构建该模块

-   构建 http_geoip_module 模块需要 `libgeoip` 开发库

    ```sh
    $ apt install libgeoip-dev
    ```

-   安装 g++

    gcc 一般自带，构建必备；而 g++ 需要自己安装

    ```sh
    $ apt install g++
    ```

    本次构建没有依赖 g++的

## 构建指令

```sh
$ mkdir /package/lnmp/nginx-1.20.1/build_nginx
$ mkdir -p /server/nginx
$ cd /package/lnmp/nginx-1.20.1
$ ./configure --prefix=/server/nginx \
... 内容见构建指令
```

-   允许构建的全部指令（参考）

    ```sh
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
    # 开启调试，生产环境下建议禁用
    --with-debug
    ```

-   普通服务器构建指令（当前构建指令）

    ```sh
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
    --with-http_geoip_module \
    --with-http_gunzip_module \
    --with-http_gzip_static_module \
    --with-http_secure_link_module \
    --with-http_degradation_module \
    --with-http_stub_status_module \
    # 禁用http功能模块
    --without-http_upstream_hash_module \
    --without-http_upstream_ip_hash_module \
    --without-http_upstream_least_conn_module \
    --without-http_upstream_random_module \
    --without-http_upstream_keepalive_module \
    --without-http_upstream_zone_module \
    # 外库路径
    --with-pcre=/package/lnmp/pcre-8.45 \
    --with-pcre-jit \
    --with-zlib=/package/lnmp/zlib-1.2.11 \
    --with-openssl=/package/lnmp/openssl-1.1.1l
    ```

在 Debian 11 下，亲测 nginx-1.20.1 能完成上面两套指令的构建

### 开始编译安装

```sh
# 4核以上可以使用 make -j4 编译
$ make
$ make install
```

### 测试

使用 curl 检测是否成功

```sh
$ /server/nginx/sbin/nginx
$ curl -I 127.0.0.1
```

-   成功信号：

    ```sh
    HTTP/1.1 200 OK
    Server: nginx/1.20.1
    Date: Wed, 15 Sep 2021 12:39:28 GMT
    Content-Type: text/html
    Content-Length: 612
    Last-Modified: Wed, 15 Sep 2021 12:38:19 GMT
    Connection: keep-alive
    ETag: "6141e93b-264"
    Accept-Ranges: bytes
    ```

-   失败信号：

    ```sh
    curl: (7) Failed to connect to 127.0.0.1 port 80: 拒绝连接
    ```

到此，nginx 构建结束！

## 记录一次升级

Nginx 平滑升级，具体操作如下：

### 构建指令：

```sh
$ mkdir /package/lnmp/nginx-1.20.1/build_nginx
$ mkdir -p /server/nginx
$ cd /package/lnmp/nginx-1.20.1
$ ./configure --prefix=/server/nginx \
... 内容见升级指令
```

### 升级指令

```sh
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
--with-http_geoip_module \
--with-http_gunzip_module \
--with-http_gzip_static_module \
--with-http_secure_link_module \
--with-http_degradation_module \
--with-http_stub_status_module \
# 禁用http功能模块
--without-http_upstream_hash_module \
--without-http_upstream_ip_hash_module \
--without-http_upstream_least_conn_module \
--without-http_upstream_random_module \
--without-http_upstream_keepalive_module \
--without-http_upstream_zone_module \
# 外库路径
--with-pcre=/package/lnmp/pcre-8.45 \
--with-pcre-jit \
--with-zlib=/package/lnmp/zlib-1.2.11 \
--with-openssl=/package/lnmp/openssl-1.1.1l
```

### 开始编译（只编译，不安装）

```sh
$ make
```

### 备份 nginx/sbin 目录下的 nginx 二进制

```sh
$ mv /server/nginx/sbin/nginx{,.-1.20.1-01}
```

### 拷贝新二进制文件到 nginx/sbin 目录下

```sh
$ cp -p -r /package/lnmp/nginx-1.20.1/bulid_nginx/nginx /server/nginx/sbin/
```

### 平滑升级

nginx 平滑升级步骤：

1. 查看旧版 nginx 的 pid

    通过 ps 指令查看

    ```sh
    $ ps -ef|grep nginx
    # 下面这个更加直观
    $ ps axw -o pid,ppid,user,%cpu,vsz,wchan,command | grep -E 'nginx|PID'
    ```

    通过 pid 文件查看

    ```sh
    $ cat /server/run/nginx/nginx.pid
    ```

2. 使用 kill -USR2 <pid> 启用新的 nginx 可执行文件

    ```sh
    $ kill -USR2 `cat /server/run/nginx/nginx.pid`
    ```

3. 使用 kill -WINCH <pid> 来关闭旧的进程

    指令实现：当进程没有访问者时，系统自动关闭当前进程

    ```sh
    $ kill -WINCH 旧版 nginx 的 pid 值
    ```

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
    操作：新增，通过 include 加载
    ```

3. [cache.conf](./nginx/cache.conf.md)

    ```text
    描述：静态文件缓存模板
    数量：按需新建，允许多个，命名需要区分
    路径：/server/nginx/conf/cache.conf
    操作：新增，通过 include 加载
    ```

4. [gzip.conf](./nginx/gzip.conf.md)

    ```text
    描述：静态文件启用压缩
    数量：按需新建，允许多个，命名需要区分
    路径：/server/nginx/conf/gzip.conf
    操作：新增，通过 include 加载
    ```

5. [limit_req_http.conf](./nginx/limit_req_http.conf.md)

    ```text
    描述：http 区块，设置限制请求
    数量：按需新建，允许多个，命名需要区分
    路径：/server/nginx/conf/limit_req_http.conf
    操作：新增，通过 include 加载
    ```

6. [limit_req_server.conf](./nginx/limit_req_server.conf.md)

    ```text
    描述：server 区块，设置限制请求
    数量：按需新建，允许多个，命名需要区分
    路径：/server/nginx/conf/limit_req_server.conf
    操作：新增，通过 include 加载
    ```

7. [sites-statics.nginx](./nginx/sites-statics.nginx.md)

    ```text
    描述：静态站点配置模版
    数量：按需新建，允许多个，命名需要区分
    路径：/server/sites/*.nginx
    操作：新增
    ```

8. [sites-tp.nginx](./nginx/sites-tp.nginx.md)

    ```text
    描述：tp6 站点配置
    数量：按需新建，允许多个
    路径：/server/sites/*.nginx
    操作：新增
    ```

## 管理 nginx

nginx 常用管理指令

| 操作         | 指令                               |
| ------------ | ---------------------------------- |
| 启动         | /server/nginx/sbin/nginx           |
| 正常关闭     | /server/nginx/sbin/nginx -s quit   |
| 快速关闭     | /server/nginx/sbin/nginx -s stop   |
| 重新载入     | /server/nginx/sbin/nginx -s reload |
| 重新打开日志 | /server/nginx/sbin/nginx -s reopen |
| 检测配置文件 | /server/nginx/sbin/nginx -t        |
| 显示帮助信息 | /server/nginx/sbin/nginx -h        |
| 列出配置信息 | /server/nginx/sbin/nginx -T        |

-   指定配置文件,启动 Nginx

    ```sh
    $ /server/nginx/sbin/nginx -c /server/nginx/conf/nginx.conf
    ```

-   检测指定的 Nginx 配置文件

    ```sh
    $ /server/nginx/sbin/nginx -t -c /server/nginx/conf/nginx.conf
    ```

-   强制停止 Nginx 进程

    ```sh
    $ pkill -9 nginx
    ```

## Systemd 单元(Unit)

用 Systemd 来管理守护进程更方便，建议为 Nginx 添加 Systemd 单元（Unit）

-   将 [nginx.service](./service/nginx.service.md) 拷贝/usr/lib/systemd/system 目录

    ```sh
    $ mv nginx.service /usr/lib/systemd/system/
    ```

-   使用类似如下指令加入开机启动

    ```sh
    $ systemctl enable nginx
    ```

-   重新加载 Systemd 配置文件

    ```sh
    $ systemctl daemon-reload
    ```

nginx 单元（Unit）管理

```sh
# 立即激活单元
$ systemctl start nginx.service

# 立即停止单元
$ systemctl stop nginx.service

# 重新加载配置
$ systemctl reload nginx.service
```
