.. _shardmap:

碎片化map
==============

.. contents:: 目录

简介
------
golang中的map不是并发安全的，并发处理时需要加读写锁。

但这样做，锁的粒度是整个map，高并发尤其写操作场景多时效率会很差。

shardmap结构把一个大map拆分成多个小map，通过降低锁粒度提高效率。

使用
-------

import::

    import (
        "andals/gobox/shardmap"
    )

新建map
*********

::

    func New(shardCnt uint8) *ShardMap

这里需要注意的是，shardCnt需要是2的整次幂。

获取值
*******

::
    
    func (this *ShardMap) Get(key string) (interface{}, bool)

设置值
*********

::

    func (this *ShardMap) Set(key string, value interface{})

遍历值
*********

::

    func (this *ShardMap) Walk(wf func(k string, v interface{}))

每遍历到一个值，会执行对应的wf方法，参数为该值的key、value。

参考
---------
`碎片map <https://github.com/DeanThompson/syncmap/blob/master/syncmap.go>`_

`并发安全的map <http://liyangliang.me/posts/2015/01/concurrent-safe-map-in-golang/>`_
