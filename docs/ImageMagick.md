# ImageMagick 篇

ImageMagick 是一个图象处理软件

Imagick 是用 ImageMagic API 来创建和修改图像的 PHP 官方扩展

Imagick 依赖于 ImageMagick 运行库

## 构建

日常工作用 gd 库足够用了，很多时候该库并不是必要的，大家按自己需求安装

### 创建必要目录

```sh
$ mkdir -p /server/ImageMagick
```

### 下载并解压包

```sh
$ cd /package/lnmp/
$ wget https://download.imagemagick.org/ImageMagick/download/ImageMagick-7.1.0-8.tar.gz
$ tar -xzvf ImageMagick-7.1.0-8.tar.gz
```

### 构建指令

1. 进入目录

    ```sh
    $ cd /package/lnmp/ImageMagick-7.1.0-8/
    ```

2. 编译

    ```sh
    $ ./configure --prefix=/server/ImageMagick
    $ make
    ```

3. 检测

    ```sh
    $ make check
    ```

4. 安装并指定安装目录

    ```sh
    $ make install
    ```

至此，ImageMagick 安装完成
