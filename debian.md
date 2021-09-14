# Debian 篇

Debian 是为比较喜欢的 Linux 发行版，本次测试环境如下：

| 配置参数   | 参数值        |
| ---------- | ------------- |
| 服务器商   | 腾讯云        |
| 服务器产品 | 云服务器/CVM  |
| 发行版本号 | Debian 10.2.x |
| CPU        | 1 核          |
| 内存       | 2GB           |
| 带宽       | 1Mbps         |

## 升级

测试环境需要先升级到 Debian 11，具体操作如下：

1. 快照备份
2. 小版本更新 Debian 10
3. 快照备份
4. 跨大版本升级到 Debian 11
5. Debian 11 各种配置
6. 快照备份

以上步骤操作完成，LNMP 服务器环境搞定，开始构建 LNMP 相关服务

提示：快照备份在下面内容中不会再次出现，请自行做好快照备份

### 更新 Debian 10

更换镜像源地址

```sh
$ cp /etc/apt/sources.list{,.bak}
$ vim /etc/apt/sources.list

# 镜像源内容
deb http://mirrors.tencentyun.com/debian/ buster main contrib non-free
deb http://mirrors.tencentyun.com/debian/ buster-updates main contrib non-free
deb http://mirrors.tencentyun.com/debian/ buster-backports main contrib non-free
deb http://mirrors.tencentyun.com/debian-security buster/updates main contrib non-free
deb-src http://mirrors.tencentyun.com/debian/ buster main contrib non-free
deb-src http://mirrors.tencentyun.com/debian/ buster-updates main contrib non-free
deb-src http://mirrors.tencentyun.com/debian/ buster-backports main contrib non-free
deb-src http://mirrors.tencentyun.com/debian-security buster/updates main contrib non-free

# 更新软件包
$ apt update -y
$ apt upgrade -y
```

移除多余的软件包

```sh
# 获取已经安装的全部包
$ apt list --installed > list.txt
$ sz list.txt
```
