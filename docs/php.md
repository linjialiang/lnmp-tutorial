# PHP 篇

PHP 是处理 php 脚本的解释器

构建时，需要指定 MariaDB 的 socket 路径，所以在 MariaDB 后安装。

## 扩展库说明

PHP 扩展库按加载时间可分为：`动态库(共享扩展)` 和 `静态库`

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
-   更新频繁的扩展库，建议使用动态编译
-   非官方认可扩展库，建议使用动态编译

[官方认可扩展库列表](https://www.php.net/manual/zh/extensions.membership.php) 里的扩展，如有必要均可用静态库的方式构建，安全性和稳定性都由官方验证过

## 静态编译 PECL 扩展

本次对 3 个 PECL 扩展，进行静态编译

-   [redis-5.3.4](https://pecl.php.net/package/redis)
-   [swoole-4.7.1](https://pecl.php.net/package/swoole)
-   [yaml-2.2.1](https://pecl.php.net/package/yaml)

### 移动 PECL 扩展

将 PECL 扩展移动到 php 的 ext 目录下

```sh
$ cd /package/lnmp/ext_static/
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

### 创建构建目录

```sh
mkdir /package/lnmp/php-8.0.11/build_php
```

### pkg-config 相关

构建 php 时，自己编译的依赖包需要手动加入到 pkg-config 中

使用 export 是临时加入环境变量中，但会永久记录在编译后的可执行文件信息里

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
$ export PKG_CONFIG_PATH=/server/sqlite3/lib/pkgconfig:$PKG_CONFIG_PATH
```

### 安装必要依赖

```sh
$ apt install libxml2-dev
$ apt install libpng-dev libwebp-dev libjpeg-dev libxpm-dev libfreetype-dev
$ apt install libonig-dev
$ apt install libcurl4-openssl-dev
$ apt install libssl-dev
$ apt install libyaml-dev
```

-   libonig-dev

    对应提示：No package 'oniguruma' found

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
--with-openssl \
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
--enable-swoole \
--with-yaml
```

### 编译并安装

```sh
$ make
$ make test
$ maek install
```

至此， php 安装完成

## 配置 php.ini

php.ini 是 php 的配置文件，具体信息请查看 [官方手册](https://www.php.net/manual/zh/ini.php)

### 配置文件模板

php 编译完成后，在源码包根目录下会生成两个 php.ini 模版文件

-   开发环境推荐

    php.ini-development

-   部署环境推荐

    php.ini-production

### 配置文件路径

下面两个指令，可以快速获取到配置文件存放路径

-   使用 php-config 程序

    ```sh
    $ /server/php/bin/php --ini
    ```

-   使用 php 程序

    ```sh
    $ /server/php/bin/php-config --ini-path
    ```

推荐：使用 php 程序查询，会更加简洁

### 拷贝配置文件

当前环境为部署环境，所以拷贝 php.ini-production

```sh
$ cp -p -r /package/php-8.0.11/php.ini-production /server/php/lib/php.ini
```

### 检测配置文件

使用 php 程序，快速检测配置文件使用加载成功

```sh
$ /server/php/bin/php --ini
```

### 开启 OPcache

OPcache 只能编译为共享扩展

-   开启方式

    在 php.ini 第 960 行，将 `;` 去掉

    ```ini
    zend_extension=opcache
    ```

-   性能配置

    在 php.ini 第 1761 行，加入以下内容，可获得较好性能

    ```ini
    [opcache]
    opcache.memory_consumption=128
    opcache.interned_strings_buffer=8
    opcache.max_accelerated_files=4000
    opcache.revalidate_freq=60
    opcache.fast_shutdown=1
    opcache.enable_cli=1
    ```
