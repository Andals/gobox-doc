.. _formater:

formater
=============
通常在写log时，会对要写的信息增加一些额外内容，例如：时间、消息级别等，本包即实现这些功能。

.. contents:: 目录

使用
------

统一操作接口
****************

IFormater::

    type IFormater interface {
        Format(level int, msg []byte) []byte
    }

import
********

::

    import (
        "andals/gobox/log"
    )

无操作
********

NoopFormater::

    type NoopFormater struct {
    }

此结构统一接口调用，减少if判断。

简单格式化
***********

SimpleFormater::

    type SimpleFormater struct {
    }

此结构添加level信息，并添加换行。

web格式化
**********

::

    func NewWebFormater(logId []byte) *WebFormater

此结构格式化web请求时记录的log，添加logId、请求时间及换行符。
