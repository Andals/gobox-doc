.. _pool:

Pool（连接池）
==============

.. contents:: 目录

简介
------
这个包用于实现外部资源连接池，如mysql、redis

重要数据结构
---------------

连接池中的连接实例::

    type IConn interface {
        Free()
    }

没有可用连接时，新建连接实例的方法::

    type NewConnFunc func() (IConn, error)

使用说明
----------

创建连接池::

    func NewPool(connTimeout time.Duration, size int, ncf NewConnFunc) *Pool

从连接池中获取实例::

    func (this *Pool) Get() (IConn, error)

将不用的实例放回连接池::

    func (this *Pool) Put(conn IConn) error

特别说明
***********

#. 从连接池中获取client和将不用的client放回连接池中的行为都是非阻塞方式
#. 获取连接实例时，如果连接池中没有可用实例，则会创建一个连接实例，创建方法使用新建连接池时传入的newConnFunc
#. 获取连接实例时，如果得到的实例已经过期，则会创建一个新实例，创建方法使用新建连接池时传入的newConnFunc，过期的实例会被释放，执行conn.Free()
#. 将连接实例放回连接池时，如果连接池已满，则会抛弃掉这个实例，会执行conn.Free()
