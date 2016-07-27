.. _encoding:

编解码
========

.. contents:: 目录

简介
------
这个包封装了常用的编码方法。

用法
------

import
^^^^^^^^^^
示例::

    import (
        "andals/gobox/encoding"
    )

base64编解码
^^^^^^^^^^^^^^
func::

    func Base64Encode(data []byte) []byte
    func Base64Decode(coded []byte) []byte
