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

# Debian 10 全部镜像源
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

### 大版本升级

使用 Debian 11 官方全部的镜像源内容，确保升级时更新全部包

```sh
deb http://mirrors.tencentyun.com/debian/ bullseye main contrib non-free
deb-src http://mirrors.tencentyun.com/debian/ bullseye main contrib non-free

deb http://mirrors.tencentyun.com/debian/ bullseye-updates main contrib non-free
deb-src http://mirrors.tencentyun.com/debian/ bullseye-updates main contrib non-free

deb http://mirrors.tencentyun.com/debian/ bullseye-backports main contrib non-free
deb-src http://mirrors.tencentyun.com/debian/ bullseye-backports main contrib non-free

deb http://mirrors.tencentyun.com/debian-security/ bullseye-security main contrib non-free
deb-src http://mirrors.tencentyun.com/debian-security/ bullseye-security main contrib non-free
```

更新软件包

```sh
$ apt update
$ apt full-upgrade -y
$ apt autoremove

# 出现输入，一路输入 Y
# 出现弹窗，一路选择第一个 install
```

### 设置中文环境

汉语比英文好的同胞，可以设置成中文环境 `zh_CN.UTF-8`

```sh
# 检查是否安装 locales
$ apt list --installed locales

# 未安装，则需要先安装
$ apt install locales
$ dpkg-reconfigure locales
```

### 升级内核

跨大版本升级，内核只会小版本升级，无法打版本升级，需要我们自行升级

```sh
# 镜像源中，不写版本号，为当前发行版的默认内核
$ apt install linux-image-amd64
# 卸载本地版本，旧版本
$ apt autoremove linux-image-4.19.0-11-amd64
```

## 安装软件包

安装几个实用扩展

```sh
$ apt install lrzsz tar bzip2 gzip curl wget neofetch
```

安装 vim 中文文档

```sh
$ tar -xzvf vimcdoc-2.3.0.tar.gz
$ cd vimcdoc-2.3.0/
$ ./vimcdoc.sh -i
```

## 美化 bash 终端

修改用户根目录下的 .bashrc 可以美化 bash 控制台，具体如下：

```sh
$ cp ~/.bashrc{,.bak}
$ vi ~/.bashrc
```

示例：

```sh
PS1='[${debian_chroot:+($debian_chroot)}\u@Debian10 \W]\$ '
export LS_OPTIONS='--color=auto'
eval "`dircolors`"
alias ls='ls $LS_OPTIONS -F'
alias ll='ls $LS_OPTIONS -lF'
alias lla='ls $LS_OPTIONS -laF'
```

使用 source 更新终端界面：

```sh
$ source ~/.bashrc
```

## 安装 Git

### git 安装指令

```sh
$ apt install git
```

### 设置 git 配置文件

```sh
$ touch /etc/gitconfig
$ vim /etc/gitconfig
```

配置文件内容

```conf
[user]
	name  = linjialiang
	email = linjialiang@163.com
[color]
    ui          = true
    status      = auto
    branch      = auto
    interactive = auto
    diff        = auto
[core]
	autocrlf  = input
	quotepath = false
	editor    = vim
```
