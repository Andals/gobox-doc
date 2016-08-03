.. _exception:

错误
========

.. contents:: 目录

简介
------
我们通常在处理错误时，会使用errno和errmsg来标示一个错误，这个包就是对这类错误的封装。

因为golang自身并没有异常机制，模拟改机制需要用到panic，我们认为异常和panic在设计上不应当混用，所以这里的exception对象也只是封装了errno和errmsg。

用法
------

import
^^^^^^^^^^
示例::

    import (
        "andals/gobox/exception"
    )

exception结构
^^^^^^^^^^^^^^
struct::

    type Exception struct {
        errno int
        msg   string
    }

    func New(errno int, msg string) *Exception
    func (this *Exception) Errno() int
    func (this *Exception) Msg() string
    func (this *Exception) Error() string
