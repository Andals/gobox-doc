.. _crypto:

加解密
========

.. contents:: 目录

简介
------
这个包封装了一些我们实践项目中最长用到的加密方法，使用起来更加方便。

用法
------

import::

    import (
        "andals/gobox/crypto"
    )

Md5
^^^^^
func::

    func Md5(data []byte) []byte
       
输出32位的md5字符串[]byte

PaddingInterface
^^^^^^^^^^^^^^^^^^
使用对称加密如AES的CBC模式加密时，需要对要加密数据做位数填充，这个接口统一数据填充调用方法。

PKCS5Padding
***************
实现最常用的PKCS5填充方式

::

    type PKCS5Padding struct {
        BlockSize int
    }

    func (this *PKCS5Padding) Padding(data []byte) []byte
    func (this *PKCS5Padding) UnPadding(data []byte) []byte

AES对称加解密（CBC模式）
^^^^^^^^^^^^^^^^^^^^^^^^^
AesCBCCrypter::

    func NewAesCBCCrypter(key []byte, iv []byte) (*AesCBCCrypter, error)  //key和iv的规则下面的Demo中有说明。
    func (this *AesCBCCrypter) SetPadding(padding PaddingInterface)       //默认的padding方法使用PKCS5Padding，如果需要修改时可以调用这个方法。
    func (this *AesCBCCrypter) Encrypt(data []byte) []byte
    func (this *AesCBCCrypter) Decrypt(crypted []byte) []byte

Demo:: 

    import (
        "andals/gobox/crypto"
    )

	key := crypto.Md5([]byte("gobox"))                           //The key argument should be the AES key, either 16, 24, or 32 bytes to select AES-128, AES-192, or AES-256.
	iv := crypto.Md5([]byte("andals"))[:crypto.AES_BLOCK_SIZE]   //The length of iv must be the same as the Block's block size
	data := []byte("abc")

	acc, _ := NewAesCBCCrypter(key, iv)
	crypted := acc.Encrypt(data)
	d := acc.Decrypt(crypted)

	if string(d) != string(data) {
        println("crypto error")
	}
