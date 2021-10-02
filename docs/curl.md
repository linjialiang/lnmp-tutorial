# curl 篇

[curl-7.79.1](https://curl.se/download/curl-7.79.1.tar.gz) 下载地址

## 创建必要目录

```sh
$ mkdir /server/curl
```

## 构建指令

```
$ cd /package/lnmp/curl-7.79.1
$ make clean
$ ./configure --prefix=/server/curl --with-openssl=/server/openssl
$ make
$ make test
$ make install
```

至此， curl 安装完成
