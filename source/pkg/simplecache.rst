.. _simplecache:

简单本地cache
==============

.. contents:: 目录

简介
------
golang写的服务，执行时通常是单进程多线程的模式。

这样的话，我们就可以利用本地内存来做cache使用，很大程度上可以替代传统的memcache、redis类服务，这样的好处是无须额外的网络开销。

使用
------

import::

    import (
        "andals/gobox/simplecache"
    )

新建cache
***********

::

     func New(shardCnt uint8) *SimpleCache

这个cache的存储使用的shardmap，shardCnt即为shardmap的分片数量。

每个SimpleCache对象都会启动独自的goroutine，这个gotoutine没隔一段时间检查cache中的数据是否过期，释放空间。


获取值
*******

::

    func (this *SimpleCache) Get(key string) (interface{}, bool)

设置值
*********

::

    func (this *SimpleCache) Set(key string, value interface{}, expire time.Duration)
