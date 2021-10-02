# PHP 篇

PHP 是处理 php 脚本的解释器

构建时，需要指定 MariaDB 的 socket 路径，所以在 MariaDB 后安装。

## 扩展库说明

PHP 按扩展库可分为：`动态库(共享扩展)` 和 `静态库`

### 动态库

动态库是在程序运行时链接的，动态库在编译时不会放到连接的目标程序中，即可执行文件无法单独运行

动态库扩展名一般为：

| 系统 | 扩展名  |
| ---- | ------- |
| win  | `*.dll` |
| unix | `*.so`  |

### 静态库

静态库是在程序编译时链接的，静态库在编译时会直接整合到目标程序中，编译成功的可执行文件可独立运行

动态库扩展名一般为：

| 系统 | 扩展名  |
| ---- | ------- |
| win  | `*.lib` |
| unix | `*.a`   |

### 动态库和静态库区别

静态编译：静态库将需要的依赖在构建是已经从系统“拷贝”到程序中，执行时就不需要调用系统的库，这会让程序变得更大

动态编译：动态库构建了 1 个连接文件(`*.so`) ，用于连接程序和系统对应库的文件，执行时需要调用系统的库以及该库对应的依赖项

-   性能：静态库优于动态库，服务器硬件配置较差的，应该倾向于静态库
-   体积：静态库体积大于动态库
-   升级：静态库升级需要重新构建 PHP，动态库升级只需要重新生成连接文件(`*.so`)即可
-   稳定：静态库更加稳定，对于动态库来讲，如果系统对应库升级，与动态库对应的构建版本不一致，可能会遇到兼容性问题

### 扩展库构建类型选择

-   使用频繁的扩展库，推荐使用静态编译
-   变动频繁的扩展库，建议使用动态编译
-   非官方认可扩展库，建议使用动态编译

[官方认可扩展库列表](https://www.php.net/manual/zh/extensions.membership.php) 里的扩展，如有必要均可用静态库的方式构建，安全性和稳定性都由官方验证过

## 创建用户

创建 php-fpm 用户

```sh
$ useradd -c 'This is the php-fpm user' -u 2003 -s /usr/sbin/nologin -d /server/www -M -U phpfpm
```

## 静态编译 PECL 扩展

本次将额外增加 4 个 PECL 扩展，并静态编译它们

-   [imagick-3.5.1](https://pecl.php.net/package/imagick)
-   [redis-5.3.4](https://pecl.php.net/package/redis)
-   [swoole-4.7.1](https://pecl.php.net/package/swoole)
-   [yaml-2.2.1](https://pecl.php.net/package/yaml)

### 下载并解压

1. 下载并解压 php

```sh
$ cd /package/lnmp/
$ wget https://www.php.net/distributions/php-8.0.11.tar.gz
$ tar -xzvf php-8.0.11.tar.gz
```

2. 下载并解压 PECL 扩展

```sh
$ cd /package/lnmp/ext/
$ wget ...
$ ...
$ tar -xzvf imagick-3.5.1.tgz
$ tar -xzvf redis-5.3.4.tgz
$ tar -xzvf swoole-4.7.1.tgz
$ tar -xzvf yaml-2.2.1.tgz
```

### 移动 PECL 扩展

将 PECL 扩展移动到 php 的 ext 目录下

```sh
$ mv imagick-3.5.1 /package/lnmp/php-8.0.11/ext/imagick
$ mv redis-5.3.4 /package/lnmp/php-8.0.11/ext/redis
$ mv swoole-4.7.1 /package/lnmp/php-8.0.11/ext/swoole
$ mv yaml-2.2.1 /package/lnmp/php-8.0.11/ext/yaml
```

### 重新生成 configure

引入新的扩展后，需要强制 PHP 重新生成配置脚本 `configure`

-   生成配置脚本依赖 autoconf

    ```sh
    $ apt install autoconf
    ```

-   强制生成 configure

    ```sh
    $ cd /package/lnmp/php-8.0.11/
    $ mv configure{,.original}
    $ ./buildconf --force
    ```

## 构建 PHP

### 创建必要目录

```sh
mkdir /server/php
mkdir /package/lnmp/php-8.0.11/build_php
```

### 加入 pkg-config

构建 php 时，自己编译的依赖包需要手动加入到 pkg-config 中

使用 export 是临时加入，关闭终端后就会消失，这刚好符合编译要求

#### 原理

1. 检测路径是否已经加入到 PKG_CONFIG_PATH 环境变量中，避免多次加入，从而造成混沦

    ```sh
    $ echo $PKG_CONFIG_PATH
    ```

2. 将路径加入到 PKG_CONFIG_PATH 环境变量中

    加入单条：

    ```sh
    $ export PKG_CONFIG_PATH=/path/to/pkgconfig1:$PKG_CONFIG_PATH
    ```

    加入 2 条：

    ```sh
    $ export PKG_CONFIG_PATH=/path/to/pkgconfig1:/path/to/pkgconfig2:$PKG_CONFIG_PATH
    ```

3. 检测是否加入成功

    ```sh
    $ pkg-config --list-all
    ```

#### 指令

```sh
$ export PKG_CONFIG_PATH=/server/openssl/lib/pkgconfig:$PKG_CONFIG_PATH
$ export PKG_CONFIG_PATH=/server/sqlite3/lib/pkgconfig:$PKG_CONFIG_PATH
$ export PKG_CONFIG_PATH=/server/zlib/lib/pkgconfig:$PKG_CONFIG_PATH
$ export PKG_CONFIG_PATH=/server/curl/lib/pkgconfig:$PKG_CONFIG_PATH
```

### 安装必要依赖

```sh
$ apt install libxml2-dev
```

### 安装指令

redis、imagick、swoole、yaml 这里使用最简单的指令

更多指令请使用 `./congfigure -h | grep redis` 查看

```sh
$ cd /package/lnmp/php-8.0.11/build_php/
$ ../configure --prefix=/server/php \
--enable-fpm \
--with-fpm-user=phpfpm \
--with-fpm-group=phpfpm \
--disable-short-tags \
--with-openssl=/server/openssl \
--with-pcre-jit \
--with-zlib \
--with-curl \
--enable-exif \
--enable-ftp \
--enable-gd \
--with-webp \
--with-jpeg \
--with-xpm \
--with-freetype \
--enable-mbstring \
--enable-mysqlnd \
--with-mysql-sock=/run/mysqld/mysqld.sock \
--with-mysqli=mysqlnd \
--with-pdo-mysql=mysqlnd \
--enable-sockets \
--enable-redis \
--with-imagick=/server/ImageMagick \
--enable-swoole \
--with-yaml
```
