---
layout: post
title: Laravel源码阅读——Cache
categories: Laravel源码
description: 简单分析Laravel的Cache。
keywords: 源码，Laravel 
---

# Cache分析

### 开始
Cache::get('cacheKey')使用的是facade，从config/app.php里面可以找到使用的facade别名，并从providers数组里面找到CacheServiceProvider

```php

'aliases' => [
    ...
    'Cache'     => Illuminate\Support\Facades\Cache::class,
    ...
],
'providers' => [
    ...
    Illuminate\Cache\CacheServiceProvider::class,
    ...
]

```

### CacheServiceProvider

从register方法中可以看到往容器单例化一个CacheManager，因此facade其实用的是CacheManager

```php

public function register()
{
    $this->app->singleton('cache', function ($app) {
        return new CacheManager($app);
    });

    $this->app->singleton('cache.store', function ($app) {
        return $app['cache']->driver();
    });

    $this->app->singleton('memcached.connector', function () {
        return new MemcachedConnector;
    });

    $this->registerCommands();
}

```

### CacheManager
首先研究get方法

```php

protected function get($name)
{
    return isset($this->stores[$name]) ? $this->stores[$name] : $this->resolve($name);
}

```

get方法是protected，调用的时候会跑到魔术方式__call去

```php

public function __call($method, $parameters)
{
    return call_user_func_array([$this->store(), $method], $parameters);
}

```

调用$this->store()，获取默认驱动名

```php

public function store($name = null)
{
    $name = $name ?: $this->getDefaultDriver();

    return $this->stores[$name] = $this->get($name);
}

```

调用$this->get()，调用$this->resolve("memcached")

```php

protected function resolve($name)
{
    // 获取memcached驱动配置
    $config = $this->getConfig($name);

    if (is_null($config)) {
        throw new InvalidArgumentException("Cache store [{$name}] is not defined.");
    }

    // 如果有自定义驱动（通过调用extend方法扩展），就实例化该驱动
    if (isset($this->customCreators[$config['driver']])) {
        return $this->callCustomCreator($config);
    } else {
        $driverMethod = 'create'.ucfirst($config['driver']).'Driver';
        // 不存在就调用createMemcachedDriver方法
        if (method_exists($this, $driverMethod)) {
            return $this->{$driverMethod}($config);
        } else {
            throw new InvalidArgumentException("Driver [{$config['driver']}] is not supported.");
        }
    }
}

```

调用$this->createMemcachedDriver($config)

```php

protected function createMemcachedDriver(array $config)
{
    $prefix = $this->getPrefix($config);

    // memcached实例
    $memcached = $this->app['memcached.connector']->connect($config['servers']);

    // 将封装过的memcached类MemcachedStore，返回一个Repository
    return $this->repository(new MemcachedStore($memcached, $prefix));
}

```

其实最后就是调用如下代码

```php

$object = new Illuminate\Cache\Repository(new MemcachedStore($memcached, $prefix));
$object->get('cacheKey');

```

### 整个过程


```

graph TB
Cache::get --> CacheFacade
CacheFacade --> CacheServerProvider
CacheServerProvider --> CacheManager::__call
CacheManager::__call --> Repository::get
Repository::get --> Memcached::get

```

### 怎么做到多种缓存驱动快速切换？

- cache.php配置驱动store
- 每种具体驱动store实现至store接口（get、put等方法）
- CacheManager获取配置，实例化Repository
- Repository中注入当前具体驱动store，调用Repository::get相当于调用具体store::get


### 参考
https://laravel-china.org/topics/2093#Xcache








