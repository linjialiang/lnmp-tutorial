# zlib 篇

构建 zlib

## 创建必要目录

```sh
$ mkdir /server/zlib
```

## 构建指令

### 清除 make

nginx 构建时，产生的 make 临时文件需要清理掉

```sh
$ cd /package/lnmp/zlib-1.2.11/
$ make clean
```

### 编译安装

```sh
$ cd /package/lnmp/zlib-1.2.11/
$ ./configure --prefix=/server/zlib
$ make
$ make test
$ make install
```

至此，zlib 安装完成
