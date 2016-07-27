.. _crypto:

加解密
========

.. contents:: 目录

简介
------
这个包封装了一些我们实践项目中最长用到的加密方法，使用起来更加方便。

用法
------

import
^^^^^^^^^^
示例::

    import (
        "andals/gobox/crypto"
    )

Md5
^^^^^
func::

    func Md5(data []byte) []byte
       
输出32位的md5字符串[]byte

AES对称加解密（CBC模式）
^^^^^
func::

    func AesCBCEncrypt(key, data []byte) []byte
    func AesCBCDecrypt(key, crypted []byte) []byte

这两个方法主要让使用者无须再关注加解密的一些细节，golang原生包的使用是需要对这些细节有一定理解才能用好。
