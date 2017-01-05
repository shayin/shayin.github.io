---
layout: post
title: Linux防火墙配置
categories: Linux
description: Linux防火墙配置。
keywords: Linux，iptables， 
---

最近买了一台vps，安全性问题肯定不容忽视，于是简单配置了一下iptables


### 允许本地回环 127.0.0.1


```

iptables -A INPUT -i lo -p all -j ACCEPT

```

### 允许已经建立的所有连接

```

iptables -A INPUT -p all -m state --state ESTABLISHED,RELATED -j ACCEPT

```

### 允许所有向外发起的连接

```

iptables -A OUTPUT -j ACCEPT

```


### 拒绝 ping

```

iptables -A INPUT -p icmp -m icmp --icmp-type 8 -j REJECT

```


### 允许 SSH 服务端口

```

iptables -A INPUT -p tcp --dport 22 -j ACCEPT

```



### 允许 Web 服务端口

```

iptables -A INPUT -p tcp --dport 80 -j ACCEPT

```

### 拒绝其他所有未被允许的连接

```

iptables -A INPUT -j REJECT
iptables -A FORWARD -j REJECT

```

### 允许指定ip

```

-A RH-Firewall-1-INPUT -s 192.168.11.0/24 -m state --state NEW -j ACCEPT

```


