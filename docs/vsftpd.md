# vsftpd 篇

vsftpd 是 Linux 中最安全、最稳定的 FTP 服务器

## 安装 vsftpd

vsftpd 推荐使用 apt 安装，这样更加稳定

```sh
$ apt install vsftpd
```

## 创建映射用户

使用 pam 认证需要创建一个本地用户，用于虚拟用户授权

### 创建 www 用户

```sh
$ useradd -c 'This Linux user is used to map VSFTPD virtual users' -u 2003 -s /usr/sbin/nologin -d /server/default -M -U www
```

### 修改家目录权限

```sh
$ chmod 550 /server/www
```

### 创建虚拟用户单独配置文件

在 /server/vsftpd 目录下，创建与虚拟用户同名的配置文件，并自定义根目录地址，具体操作如下：

-   创建虚拟用户(www)的单独配置文件

    ```sh
    $ vim /server/vsftpd/www
    ```

    具体内容：

    ```conf
    local_root=/server/www
    ```

-   控制目录权限不可写

    设置了 virtual_use_local_privs=yes 以后，虚拟用户的权限与本地用户完全相同，所以家目录不能有写的权限：

    ```sh
    $ chown www:www /server/www
    $ chmod a-w /server/www
    ```

### 限制到家目录

设置了 virtual_use_local_privs=yes 以后，虚拟用户的权限与本地用户完全相同，所以同样需要使用 chroot_local_user 来限制家目录：

```conf
chroot_local_user=YES
chroot_list_enable=YES
chroot_list_file=/etc/vsftpd/chroot_list
```

> 提示：虚拟用户登陆，最大的好处就是我们之后可以通过 web 后台来控制！

## PAM 模块

libpam-mysql 让 PAM 支持 MariaDB 认证

### 安装 libpam-mysql

```sh
$ apt install libpam-mysql
```

> 提示：libpam-mysql 配置文件位于 /etc/pam-mysql.conf

### 创建 vsftpd 的 PAM 配置文件

路径 ：/etc/pam.d/vsftpd-guest

```conf
auth required pam_mysql.so user=pam_vsftpd passwd=123456 host=127.0.0.1 db=db_pam table=pam_vsftpd usercolumn=ftp_user passwdcolumn=ftp_passwd crypt=2
account required pam_mysql.so user=pam_vsftpd passwd=123456 host=127.0.0.1 db=db_pam table=pam_vsftpd usercolumn=ftp_user passwdcolumn=ftp_passwd crypt=2
```

### 数据库管理

配置数据库信息

```sh
MariaDB [(none)]> CREATE DATABASE db_pam;
MariaDB [(none)]> CREATE TABLE db_pam.pam_vsftpd (
   -> id    int AUTO_INCREMENT  NOT NULL    PRIMARY KEY,
   -> ftp_user  varchar(255)    BINARY  NOT NULL,
   -> ftp_passwd    char(41)    BINARY  NOT NULL,
   -> ftp_dir   varchar(255)    BINARY
   -> );
MariaDB [(none)]> CREATE USER 'pam_vsftpd'@'localhost' IDENTIFIED BY '数据库用户密码';
MariaDB [(none)]> GRANT SELECT ON db_pam.pam_vsftpd TO 'pam_vsftpd'@'localhost';
MariaDB [(none)]> FLUSH PRIVILEGES;
```

创建 vsftpd 用户 www

```sh
MariaDB [(none)]> INSERT INTO db_pam.pam_vsftpd
   -> ( ftp_user, ftp_passwd )
   -> VALUES
   -> ( 'www', password('ftp登录用户密码') );
```

创建 vsftpd 用户 qyadmin

```sh
MariaDB [(none)]> INSERT INTO db_pam.pam_vsftpd
   -> ( ftp_user, ftp_passwd )
   -> VALUES
   -> ( 'qyadmin', password('ftp登录用户密码') );
```

表 db_pam.pam_vsftpd 的结构：

```sh
MariaDB [(none)]> DESCRIBE db_pam.pam_vsftpd;
+------------+--------------+------+-----+---------+----------------+
| Field      | Type         | Null | Key | Default | Extra          |
+------------+--------------+------+-----+---------+----------------+
| id         | int(11)      | NO   | PRI | NULL    | auto_increment |
| ftp_user   | varchar(255) | NO   |     | NULL    |                |
| ftp_passwd | char(41)     | NO   |     | NULL    |                |
| ftp_dir    | varchar(255) | YES  |     | NULL    |                |
+------------+--------------+------+-----+---------+----------------+
4 rows in set (0.001 sec)
```

表 db_pam.pam_vsftpd 的数据：

```sh
MariaDB [(none)]> select * from db_pam.pam_vsftpd;
+----+----------+-------------------------------------------+---------+
| id | ftp_user | ftp_passwd                                | ftp_dir |
+----+----------+-------------------------------------------+---------+
|  1 | www      | *6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9 | NULL    |
|  2 | qyadmin  | *6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9 | NULL    |
+----+----------+-------------------------------------------+---------+
2 rows in set (0.001 sec)
```
