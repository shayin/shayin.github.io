---
layout: post
title: 用Nginx统计apk下载完成的次数
categories: Nginx
description: 关于静态方法、单例模式以及连接池。
keywords: php，静态方法，单例模式，连接池
---

发起下载次数可以通过中转来统计，但是统计下载完成的次数，就需要Nginx出马了

**ps： 目前还没应用在正式环境上**



### 配置Nginx的post_action

使用Nginx的post_action，在该请求完成后去到afterdownload

```

location ~* \.(apk)$ {
    limit_rate 128k;
    post_action @afterdownload;
}

```


### 配置代理转发

配置代理转发，将下载完成之后的请求转发到http://ws.xuan.com/count_down.php下

```

location @afterdownload {
    rewrite ^/.*\.apk /count_down.php?body_bytes_sent=$body_bytes_sent&status=$request_completion last;
    proxy_pass http://ws.xuan.com;
}

```


从上面的分析中可以看出：

- 当一个方法与他所在的类的实例无关的时候，那他就应该是静态的，比如一些工具类；
- 反之则是非静态的








