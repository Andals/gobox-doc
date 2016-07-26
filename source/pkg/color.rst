.. _color:

为终端输出增加颜色
===================

.. contents:: 目录

简介
-------
这个包能为在终端中输出的信息添加颜色。

用法
------

import
^^^^^^^^^^
示例::

    import (
        "andals/gobox/color"
    )

内置颜色
^^^^^^^^^^
内置颜色支持：Black、Red、Green、Yellow、Blue、Maganta、Cyan、White，调用对应颜色名称的方法即可。

示例::

    rc := color.Red([]byte("Red"))

自定义颜色
^^^^^^^^^^^
也支持自定义颜色，用户需要传入标准的颜色参数::

    func Color(color []byte, msg []byte) []byte

参考
-------
`技巧：提示行魔术 <https://www.ibm.com/developerworks/cn/linux/l-tip-prompt/tip01/>`_
