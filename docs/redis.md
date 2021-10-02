# redis 篇

[redis](https://redis.io/download) 是当下最热门的键值对(Key-Value)存储数据库，下面是 Debian11 下构建 Redis 的详细流程

## 安装依赖

对于 Redis，测试机当下缺失下面 2 个依赖库

```sh
$ apt install pkg-config tcl
```

## 构建安装

Redis 构建相对简单

### 创建必要目录

```sh
$ mkdir -p /server/redis /server/run/redis
```

### 下载并解压包

```sh
$ cd /package/lnmp/
$ wget https://download.redis.io/releases/redis-6.2.5.tar.gz
$ tar -xzvf redis-6.2.5.tar.gz
```

### 构建指令

1. 进入目录

    ```sh
    $ cd /package/lnmp/redis-6.2.5/
    ```

2. 清除 make 产生的临时文件

    ```sh
    $ make clean
    ```

3. 编译

    ```sh
    $ make
    ```

4. 测试

    ```sh
    $ make test
    ```

    当出现高亮信息 \o/ All tests passed without errors! 证明测试通过

5. 安装并指定安装目录

    ```sh
    $ make install PREFIX=/server/redis
    ```

### 可执行文件

Redis 安装后，很简洁，只有 3 个可执行文件

1. redis-benchmark

    用于 Redis 压力测试工具

2. redis-server

    启动 Reids 数据库

3. redis-cli

    Redis 命令工具

## 配置文件

redis 源码包中自带了 1 个配置文件，我们就直接拿来，按需修改即可

### 拷贝配置文件

```sh
$ cp -p -r /package/lnmp/redis-6.2.5/redis.conf /server/redis/conf/redis.conf
```

### 修改配置文件

测试环境一共对配置文件修改了 2 处

1. 允许 redis 后台启动

    默认情况下，redis 是前台启动的，实际运用中我们都会选择后台启动

    ```conf
    daemonize yes
    ```

2. 修改 pid 文件路径

    pid 文件统一放置 /server/run 下面，便于管理

    ```conf
    pidfile /server/run/redis/redis.pid
    ```

## 配置 redis 单元

推荐统一使用 systemd 管理各种服务

点击查看 [redis.service](./service/redis.service.md) 参考配置

下面是具体操作：

```sh
$ touch redis.service
$ vim redis.service
$ mv redis.service /usr/lib/systemd/system/
$ systemctl enable redis
$ systemctl daemon-reload
```

### Redis 单元管理

```sh
# 立即激活单元
$ systemctl start nginx.service

# 立即停止单元
$ systemctl stop nginx.service

# 重新启动
$ systemctl restart nginx.service
```

## 查看启动状态

```sh
$ ps -ef|grep -E "redis|PID" |grep -v grep
$ ps aux|grep -E "redis|PID" |grep -v grep
```