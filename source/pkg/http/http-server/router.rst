.. _router:

Router
=============
这里定义了实现传统MVC的路由器

.. contents:: 目录

先看下主要的数据结构：

::

    type Route struct {
        Cl          controller.Controller   //该请求使用哪个controller
        ActionValue *reflect.Value          //该请求使用哪个action（对应action方法的反射value）
        Args        []string                //从request_uri中解析出的该请求对应到action方法中的args（不包括ActionText参数）
    }

    type Router interface {
        MapRouteItems(cls ...controller.Controller)    //自动映射controller中的action方法和请求的request_uri做对应
        DefineRouteItem(pattern string, cl controller.Controller, actionName string)   //自定义request_uri对应到controller中的action方法

        FindRoute(path string) *Route     //路由查找实现，即实现通过request_uri查找到controller和action
    } 

SimpleRouter
---------------------

这是一个Router的实现，规则如下：

1. 请求会先查找自定义路由规则，找到后结束。
#. 自定义路由规则查找时会按照定义规则时的顺序进行查找，先定义的先找。
#. 自定义路由规则中找不到，会查找自动映射规则。

自动映射规则
###############

``GET /controllerName/actionName``

例如：请求 ``GET /demo/get`` 会对应到 ``DemoController`` 下的 ``GetAction``

手动添加规则
##############

正则中捕获的内容，会作为Action中的Args参数，按照捕获顺序依次赋值。

定义规则：

::

    dcl := new(DemoController)
    r := router.NewSimpleRouter()

    r.DefineRouteItem("^/g/([0-9]+)$", dcl, "get")

Action实现：

::

    func (this *DemoController) GetAction(context *DemoActionContext, id string) {
    }

上面这个自定义路由，会将如请求 ``GET /g/123456`` 对应到 ``DemoController`` 下的 ``GetAction``，并将捕获到的 ``123456`` 赋值给参数 ``id string``
