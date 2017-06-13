.. _redis:

redis
==============

.. contents:: 目录

简介
------
这个包用于执行redis操作

重要数据结构
---------------

字符串数据结果::

    type StringResult struct {
        Value string
        Err   error
    }

hash数据结果::

    type HashResult struct {
        Value map[string]string
        Err   error
    }

操作client接口，用户可以自行实现自己的client::

    type IClient interface {
        SetLogger(logger log.ILogger)
        Free()

        Expire(key, seconds string) error
        Del(key string) error

        //args：[EX seconds] [PX milliseconds] [NX|XX]
        Set(key, value string, args ...string) error
        Setex(key, seconds, value string) error
        Get(key string) *StringResult

        Hset(key, field, value string) error
        Hmset(key string, fieldValuePairs ...string) error
        Hget(key, field string) *StringResult
        Hgetall(key string) *HashResult
        Hdel(key string, fields ...string) error
    }

由于redis协议基于字符串传输，这里并没有使用interface这种兼容数据结构，而是直接使用了string作为操作值和返回结果中的相关数据类型。

简单client实现
------------------

这里是一个预制的client实现，可以满足基本操作需求。

获得client::

    func NewSimpleClient(network, addr, pass string, timeout time.Duration) (*simpleClient, error)

注意
**********

简单client获取获取数据时，如果数据为空（对应key已过期或不存在），则返回的xxxResult为nil。

例如::

    sr := client.Get(key)
    if sr == nil {
        fmt.Println("key not exists")
    }

redis连接池
----------------

实测使用连接池能极大提升性能。

使用说明
**************

创建连接池::

    func NewPool(clientTimeout time.Duration, size int, newClientFunc func() (IClient, error)) *Pool

从连接池中获取client::

    func (this *Pool) Get() (IClient, error) {

将不用的client放回连接池::

    func (this *Pool) Put(client IClient) error {

特别说明
***********

#. 从连接池中获取client和将不用的client放回连接池中的行为都是非阻塞方式
#. 获取client时，如果连接池中没有可用client，则会创建一个client，创建方法使用新建连接池时传入的newClientFunc
#. 获取client时，如果得到的client已经过期，则会创建一个client，创建方法使用新建连接池时传入的newClientFunc，过期的client会被释放，执行client.Free()
#. 将client放回连接池时，如果连接池已满，则会抛弃掉这个client，会执行client.Free()
