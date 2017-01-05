---
layout: post
title: a标签jsonp请求在IE下跑error回调
categories: Javascript
description: a标签jsonp请求跑error回调。
keywords: a标签，jsonp，IE，error 
---

a标签在低版本IE下请求成功，却一直跑到error回调里面去，最后在http://yu123.me/2013/03/ie6-ajax-bug/ 这篇博文中找到解决方法


### 调试


```

error: function (a,b,c) {
    alert(a.readyState);	//输出：4
    alert(a.responseText);	//输出：undefined
    alert(a.status);	//输出：200
    alert(a.statusText);	//输出：success
    alert(b);	//输出：parseeror
    alert(c);	//输出：[object error]
    alert("Connection error");
}

```

### 解决方法
1. 最简单的，换个标签吧，别用a了
2. 使用 <a href=javascript:aa();”> 这种写法调用javascript方法
3. 在绑定事件里阻止A标签触发默认事件，jQuery就提供了个很简单的调用 event.preventDefault()



