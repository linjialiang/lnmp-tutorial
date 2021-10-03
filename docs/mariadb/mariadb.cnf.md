# MariaDB 主配置文件

路径： /etc/mysql/mariadb.cnf

```conf
[client-server]
socket	= /server/run/mariadb/mariadb.sock

!includedir /etc/mysql/conf.d/
!includedir /etc/mysql/mariadb.conf.d/
```

> 提示：本次测试环境，未修改该配置文件
