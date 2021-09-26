# MariaDB 主配置文件

路径： /etc/mysql/mariadb.cnf

```conf
[client-server]
port	= 3306
socket	= /server/run/mariadb/mysqld.sock

!includedir /etc/mysql/conf.d/
!includedir /etc/mysql/mariadb.conf.d/
```