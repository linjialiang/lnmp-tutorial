# MariaDB 篇

LNMP 第二个要安装的是 MariaDB ，事实上 MariaDB 官方文档非常糟糕，幸好 MairaDB 对常见 Linux 发行版都能及时的做了预编译

MariaDB 的预编译完全足够 phper 使用，具体安装方法如下：

## 添加 MariaDB 镜像

预编译版本使用使用 apt 来安装 MariaDB

### 生成镜像信息

点击 [获取 MariaDB 镜像](https://downloads.mariadb.org/mariadb/repositories) ，通过 4 步选择，生成最符合你的 MariaDB 镜像信息

1. 选择 Linux 发行版

    | 英文 | Choose a Distro |
    | ---- | --------------- |
    | 选择 | Debian          |

2. 选择发行版版本号

    | 英文 | Choose a Release     |
    | ---- | -------------------- |
    | 选择 | Debian 11 "bullseye" |

3. 选择 MariaDB 版本号

    | 英文 | Choose a Version |
    | ---- | ---------------- |
    | 选择 | 10.6 [Stable]    |

4. 选择 MariaDB 镜像

    清华大学通常是最快的，大家按具体情况选择

    | 英文 | Choose a Mirror |
    | ---- | --------------- |
    | 选择 | 清华大学        |

    > 提示：本次测试环境使用的是腾讯云服务器，所以 MariaDB 镜像选择的是腾讯软件源

### 具体操作

1. 外网导入 MariaDB 密钥

    ```sh
    $ apt install software-properties-common dirmngr
    $ curl -LsSO https://mariadb.org/mariadb_release_signing_key.asc
    $ chmod -c 644 mariadb_release_signing_key.asc
    $ mv -vi mariadb_release_signing_key.asc /etc/apt/trusted.gpg.d/
    ```

2. 内网导入 MariaDB 密钥

    MariaDB 镜像的公共密钥是唯一的，简单说就是全球服务器用的都是一样的，因此将下载好的 [MariaDB 密钥](./mariadb/mariadb_release_signing_key.asc.md) 文件拷贝到 /etc/apt/trusted.gpg.d/ 目录下即可

    具体操作如下：

    ```sh
    $ apt install software-properties-common dirmngr
    $ vim /etc/apt/trusted.gpg.d/mariadb_release_signing_key.asc
    # 插入 MariaDB 密钥内容 ...
    $ chmod -c 644 /etc/apt/trusted.gpg.d/mariadb_release_signing_key.asc
    ```

3. 为 MariaDB 创建自定义源文件

    ```sh
    $ touch /etc/apt/sources.list.d/mariadb.list
    $ vim /etc/apt/sources.list.d/mariadb.list
    ```

    清华外网 mariadb.list 源码：

    ```conf
    # MariaDB 10.6 repository list - created 2021-09-25 14:11 UTC
    # http://downloads.mariadb.org/mariadb/repositories/
    deb [arch=amd64,arm64,ppc64el] https://mirrors.tuna.tsinghua.edu.cn/mariadb/repo/10.6/debian bullseye main
    deb-src https://mirrors.tuna.tsinghua.edu.cn/mariadb/repo/10.6/debian bullseye main
    ```

    腾讯云内网 mariadb.list 源码：

    ```conf
    # MariaDB 10.6 repository list - created 2021-09-25 14:11 UTC
    # http://downloads.mariadb.org/mariadb/repositories/
    deb [arch=amd64,arm64,ppc64el] http://mirrors.tencentyun.com/mariadb/repo/10.6/debian bullseye main
    deb-src http://mirrors.tencentyun.com/mariadb/repo/10.6/debian bullseye main
    ```

4. 更新 Debian 源

    新加入的 MariaDB 镜像，必须更新后才能生效

    ```sh
    $ apt update
    ```

## 安装 MariaDB

上一步完成以后，接下来安装 MariaDB 只需 1 条指令

```sh
$ apt install mariadb-server
```

## 系统单元管理

系统单元管理 就是 Systemd Unit 管理，是一个相对复杂的指令集，这里不做过多的讲解，下面是针对 MariaDB 的内容：

### 安装信息解读

安装 MariaDB 区间出现了 3 段有意思的内容

1. 提示 MariaDB 配置文件路径

    ```text
    正在设置 mariadb-common (1:10.6.4+maria~bullseye) ...
    update-alternatives: 错误: 无 my.cnf 的候选项
    update-alternatives: 使用 /etc/mysql/mariadb.cnf 来在自动模式中提供 /etc/mysql/my.cnf (my.cnf)
    ```

2. rsync 系统单元管理

    ```text
    正在设置 rsync (3.2.3-4) ...
    Created symlink /etc/systemd/system/multi-user.target.wants/rsync.service → /lib/systemd/system/rsync.service.
    ```

    - 将 rsync 加入到系统单元的 multi-user.target 组
    - 创建硬链接到 system 目录，以支持开机启动

    > 提示：rsync 是 linux 系统下的数据镜像备份工具，是 MariaDB 的依赖项，会自动安装， 通常 phper 不需要了解

3. MariaDB 系统单元管理

    ```text
    正在设置 mariadb-server-10.6 (1:10.6.4+maria~bullseye) ...
    Created symlink /etc/systemd/system/multi-user.target.wants/mariadb.service → /lib/systemd/system/mariadb.service.
    mariadb-extra.socket is a disabled or a static unit, not starting it.
    mariadb-extra.socket is a disabled or a static unit, not starting it.
    ```

    - 将 mariadb 加入到系统单元的 multi-user.target 组
    - 创建硬链接到 system 目录，以支持开机启动

    > 提示：使用 systemctl daemon-reload 重载 Systemd 配置，即可使用 Systemd 来控制 MariaDB

### MariaDB 系统单元文件

安装好 MariaDB 以后，自动创建的 [系统单元文件](./service/mariadb.service.md)

## 配置 MariaDB

修改配置文件，让 MariaDB 更加容易管理和操作，具体如下：

| 　序号 | 需要修改的配置项                |
| ------ | ------------------------------- |
| 01     | 修改 MariaDB 的 PID 文件路径    |
| 02     | 修改 MariaDB 的 socket 文件路径 |
| 03     | 修改 MariaDB 的 socket 文件路径 |
| 04     | 修改 MariaDB 索引日志存放路径   |
| 05     | 允许远程链接                    |
| 06     | 创建 MariaDB 远程用户           |

### 配置文件路径

1. MariaDB 主配置文件

    MariaDB 服务器端的主配置文件只有 1 个，路径为：/etc/mysql/my.cnf

    /etc/mysql/my.cnf 是 /etc/alternatives/my.cnf 的软链接

    /etc/alternatives/my.cnf 又是 /etc/mysql/mariadb.cnf 的软链接

    > 提示：所以修改和备份主配置文件，都应该在 /etc/mysql/mariadb.cnf 文件上下工夫

2. MariaDB 子配置文件

    以下两个目录中的所有 `.cnf` 格式文件，为 MariaDB 的子配置文件

    ```text
    !includedir /etc/mysql/conf.d/
    !includedir /etc/mysql/mariadb.conf.d/
    ```

3. MariaDB 修改配置文件

    通常我们只需要修改 [50-server.cnf](./mariadb/50-server.cnf) 这个子配置文件

### 首先，停止 MariaDB 服务

```sh
$ service mariadb stop
# 或者
$ systemctl stop mariadb
```

### 修改配置文件

```sh
$ cp -p -r /etc/mysql/my.cnf{,.bak}
$ vim /etc/mysql/my.cnf
```

> my.cnf 文件修改的参数如下：

| 属性                   | my.cnf 对应参数及参数值                             |
| ---------------------- | --------------------------------------------------- |
| 套接字                 | socket = /server/run/mariadb/mariadb.sock           |
| pid 文件               | pid-file = /server/run/mariadb/mariadb.pid          |
| 数据目录               | datadir = /server/data                              |
| 二进制日志记录格式     | binlog_format = mixed                               |
| 二进制日志文件过期时间 | expire_logs_days = 30                               |
| 二进制日志每个文件容量 | max_binlog_size = 100M                              |
| 二进制日志路径         | log_bin = /server/logs/mariadb/bin_log              |
| 二进制日志索引文件路径 | log_bin_index = /server/logs/mariadb/bin_log.index  |
| 关闭慢查询日志         | slow_query_log = 0                                  |
| 慢查询日志路径         | slow_query_log_file = /server/logs/mariadb/slow.log |
| 关闭通用日志           | general_log = 0                                     |
| 通用日志文件           | general_log_file = /server/logs/mariadb/general.log |
| 错误日志记录级别       | log_warnings = 0                                    |
| 错误日志文件           | log_error = /server/logs/mariadb/err.log            |

> 修改后的配置文件请参考 [my.cnf](./source/mariadb/my.cnf)

### 创建必要目录

创建必要目录，并设置用户权限为 MariaDB 用户

```sh
$ mkdir /server/data
$ chown mysql /server/data/
$ mkdir /server/logs/mariadb
$ chown mysql /server/logs/mariadb/
$ mkdir /server/run/mariadb
$ chown mysql /server/run/mariadb/
```

### 初始化数据目录

使用 `mysql_install_db` 这个工具初始化数据目录：

```sh
$ /usr/bin/mysql_install_db --user=mysql \
--datadir=/server/data \
--skip-test-db
```

## 六、启动 MariaDB

MariaDB 自带了守护进程启动方式，但是使用 Systemd 控制更加优秀

1. 使用 MariaDB 自带的程序操作方法：

    | 操作类型     | 指令                    |
    | ------------ | ----------------------- |
    | 启用 MariaDB | `$ mysqld_safe &`       |
    | 停止 MariaDB | `$ mysqladmin shutdwon` |

2. 使用 Systemd 单元(Unit)文件操作 MariaDB

    经过修改的 MariaDB 已经无法通过之前 Unit 文件来管理，需要如下修改：

    | Unit 文件        | 路径                                                          |
    | ---------------- | ------------------------------------------------------------- |
    | 自带的 Unit 文件 | /usr/lib/systemd/system/mariadb.service                       |
    | 可用的 Unit 文件 | 源码请查看 [mariadb.server](./source/mariadb/mariadb.service) |

    修改 MariaDB 自带的 Unit 文件，然后使用重新加载 systemd 配置：

    ```sh
    $ systemctl daemon-reload
    ```

    Systemd 常用的操作 MariaDB 指令：

    | 操作类型     | 指令                    |
    | ------------ | ----------------------- |
    | 启动 MariaDB | systemctl start mysqld  |
    | 关闭 MariaDB | systemctl stop mariadb  |
    | 重启 MariaDB | systemctl restart mysql |

### 允许客户端远程连接

默认情况下 MariaDB 只能通过 shell 终端或本地客户端管理数据，为了方便管理我们需要开启远程管理（开启远程是有风险的）

1. 修改 MariaDB 配置文件里的 `bind-address` 参数即可：

    bind-address 只有 2 个参数值：

    | 参数值    | 描述                               |
    | --------- | ---------------------------------- |
    | 0.0.0.0   | MariaDB 服务对所有 IP 的客户端开放 |
    | 127.0.0.1 | MariaDB 服务对只对本地客户端开放   |

    > 注意：bind-address 无论怎么设置，shell 终端都可以登陆 MariaDB，两者无关！

2. 创建允许本地客户端登陆的超级管理员用户

    ```sh
    $ service mariadb start
    $ mysql
    MariaDB [(none)]> create user 'admin'@'localhost' identified by '123456';
    MariaDB [(none)]> grant all privileges on *.* to 'admin'@'localhost' WITH GRANT OPTION;
    MariaDB [(none)]> flush privileges;
    ```

    > 说明：默认 root@localhost 只允许 linux 的 root 用户登陆

3. 创建允许远程客户端登陆的超级管理员用户

    ```sh
    $ service mariadb start
    $ mysql
    MariaDB [(none)]> create user 'root'@'192.168.10.9' identified by '123456';
    MariaDB [(none)]> grant all privileges on *.* to 'root'@'192.168.10.9' WITH GRANT OPTION;
    MariaDB [(none)]> flush privileges;
    ```

    > 关于 sql 指令的具体含义，请参阅 [MariaDB 下的 sql 指令](./../../MariaDB/03-mariadb下的sql指令.md)

    | 设备           | ip 地址        |
    | -------------- | -------------- |
    | MariaDB 服务器 | 192.168.10.251 |
    | 客户机路由器   | 192.168.10.9   |
    | 客户机         | 192.168.66.103 |

    > 上表告诉我们多网段的局域网中，服务器只能识别与其处于同一网段的主设备，下级设备只能通过主设备来操作（这是网络的知识点了）！
