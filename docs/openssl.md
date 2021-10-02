# openssl 篇

构建 openssl

## 创建必要目录

```sh
$ mkdir /server/openssl
```

## 构建指令

### 清除 make

nginx 构建时，产生的 make 临时文件需要清理掉

```sh
$ cd /package/lnmp/openssl-1.1.1l/
$ make clean
```

## 编译安装

```sh
$ cd /package/lnmp/openssl-1.1.1l/
$ ./config --openssldir=/server/openssl --prefix=/server/openssl
$ make
$ make test
$ make install
```
