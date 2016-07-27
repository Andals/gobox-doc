.. _misc:

零碎的东西
==============

.. contents:: 目录

简介
------
这个包收集一些零碎的方法。

使用
------

import::

    import (
        "andals/gobox/encoding"
    )

int型slice元素unique
***********************

::

    func IntSliceUnique(s []int) []int

string型slice元素unique
***************************

::

    func StringSliceUnique(s []string) []string

检查文件是否存在
**********************

::

    func FileExist(path string) bool

检查目录是否存在
**********************

::

    func DirExist(path string) bool

go test时清晰打印执行的测试方法
************************************

::

    func PrintCallerFuncNameForTest()

合并多个[]byte
******************

::

    func AppendBytes(b []byte, elems ...[]byte) []byte

通用时间layout
********************

::

    const (
        TIME_FMT_STR_YEAR   = "2006"
        TIME_FMT_STR_MONTH  = "01"
        TIME_FMT_STR_DAY    = "02"
        TIME_FMT_STR_HOUR   = "15"
        TIME_FMT_STR_MINUTE = "04"
        TIME_FMT_STR_SECOND = "05"
    )

    func TimeGeneralLayout() string //layout如：2016-01-02 15:04:05

通过时间生成随机数
********************

::

    import (
        "time"
    )

    func RandByTime(t *time.Time) int64

``t == nil`` 时会自动取当前时间 ``time.Now()``
