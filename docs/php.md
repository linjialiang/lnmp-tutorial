# PHP 篇

PHP 是处理 php 脚本的解释器

构建时，需要指定 MariaDB 的 socket 路径，所以在 MariaDB 后安装。

## 扩展库说明

PHP 按扩展库可分为：`动态库(共享扩展)` 和 `静态库`

### 动态库

动态库是在程序运行时链接的，动态库在编译时不会放到连接的目标程序中，即可执行文件无法单独运行

动态库扩展名一般为：

| 系统 | 扩展名  |
| ---- | ------- |
| win  | `*.dll` |
| unix | `*.so`  |

### 静态库

静态库是在程序编译时链接的，静态库在编译时会直接整合到目标程序中，编译成功的可执行文件可独立运行

动态库扩展名一般为：

| 系统 | 扩展名  |
| ---- | ------- |
| win  | `*.lib` |
| unix | `*.a`   |

### 动态库和静态库区别

静态库原理：静态库将需要的依赖在构建是已经从系统“拷贝”到程序中，执行时就不需要调用系统的库，这会让程序变得更大

动态库原理：动态库构建了 1 个连接文件(`*.so`) ，用于连接程序和系统对应库的文件，执行时需要调用系统的库以及该库对应的依赖项

-   性能：静态库优于动态库，服务器硬件配置较差的，应该倾向于静态库
-   体积：静态库体积大于动态库
-   升级：静态库升级需要重新构建 PHP，动态库升级只需要重新生成连接文件(`*.so`)即可
-   稳定：静态库更加稳定，对于动态库来讲，如果系统对应库升级，与动态库对应的构建版本不一致，可能会遇到兼容性问题

### 扩展库构建类型选择

-   使用频繁的扩展库，推荐使用静态库
-   变动频繁的扩展库，建议使用动态库
-   非官方认可扩展库，建议使用动态库

[官方认可扩展库列表](https://www.php.net/manual/zh/extensions.membership.php) 里的扩展，均可以使用静态构建，安全性和稳定性都由官方验证过