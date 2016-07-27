.. _query:

query
=============
这个包用于从请求参数中解析出ActionFunc处理所需要的。

.. contents:: 目录

重要数据结构
--------------

Value
^^^^^^^^^^^^

::

    type Value interface {
        Set(str string) error               //调用这个方法设置解析出的值
        Check() bool                        //调用这个方法检查解析出的值
        Error() *exception.Exception        //出错时调用
    }

用于描述每个待解析的值

检查方法
^^^^^^^^^^

::

    type CheckInt func(v int) bool
    type CheckString func(v string) bool

这些方法都可以通过闭包生成


用法
------

import
^^^^^^^^^^
示例::

    import (
        "andals/gobox/http/query"
    )

设置解析一个int参数
^^^^^^^^^^^^^^^^^^^^^^^^

::

    func (this *QuerySet) IntVar(p *int, name string, errno int, msg string, cf CheckInt)

如果 ``cf == nil``，则默认判断解析后的值非0

设置解析一个string参数
^^^^^^^^^^^^^^^^^^^^^^^^

::

    func (this *QuerySet) StringVar(p *string, name string, errno int, msg string, cf CheckString)

如果 ``cf == nil``，则默认判断解析后的值非空

demo
^^^^^^

::

    import (
        "fmt"
        "net/url"

        "andals/gobox/http/query"
    )

	qv, _ := url.ParseQuery("a=1&b=hello")
	qs := query.NewQuerySet()

	var a int
	var b string

	qs.IntVar(&a, "a", 101, "invalid a", nil)
	qs.StringVar(&b, "b", 102, "invalid b", nil)

	e := qs.Parse(&qv)
	if e != nil {
		fmt.Println(e.Error())
	} else {
		fmt.Println(a, b)
	}
