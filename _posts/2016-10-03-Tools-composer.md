---
layout: post
title: 初识Composer
categories: Tools
description: composer。
keywords: composer
---

PHP必不可少的包管理工具---Composer

### composer.json

声明包之间的相互关系和其他的一些元素标签；

### require关键字

```
{
    "require": {
        "monolog/monolog": "1.2.*"
    }
}
```

### 包的命名

主名/包名称，主名必须唯一，包名可以重复；

### 包的版本

- 1.2.*代表1.2分支都可以，比如1.2.0，1.2.3；
- 可以使用< ，<=， 等符号定义版本范围；
- ~1.2相当于>1.2；

### 安装依赖包

配置好commoser.json之后，运行命令：

```
$ php composer.phar install
```

会自动下载monolog/monolog文件到vendor目录下，安装依赖包，生成composer.lock

### 更新依赖包

```bash
$ php composer.phar update                 #更新全部依赖包（更新依赖包版本的时候使用，会更新composer.lock）；
$ php composer.phar update monolog/monolog #更新指定的某个依赖包；
```

### composer.lock - 锁定文件

锁定所有包的版本

### composer.json + composer.lock
- 使用install命令的时候，首先判断composer.lock文件是否存在，如果存在则根据该文件下载相应的依赖包（不读取composer.json文件的配置）；

- 如果不存在composer.lock文件，则根据composer.json读取相应的包和版本信息，创建composer.lock；

- 有了composer.lock后，依赖包不会自动更新新版本，除非使用update；

### 自动加载

composer自动生成了一个文件vendor/autoload.php；

```php
require 'vendor/autoload.php'
```

### 可以在composer.json中加载自己的代码

composer将会按照psr-4的规范来注册Acme的命名空间，可以定义一个映射通过命名空间到文件目录，src目录就是根目录，与vendor是同一级的目录，例如src/Foo.php就包含了Acme\Foo类

```
{
    "autoload": {
        "psr-4": {"ACME\\" : "src/"}
    }
}
```









