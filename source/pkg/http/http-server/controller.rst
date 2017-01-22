.. _controller:

Controller
=============
定义Controller接口和ActionContext

.. contents:: 目录

要理解如何写一个controller，就需要先了解Controller和ActionContext这两个接口

Controller接口
-------------------

::

    type Controller interface {
        NewActionContext(req *http.Request, respWriter http.ResponseWriter) ActionContext   //每个Controller都可以实现自己的ActionText

        BeforeAction(context ActionContext)     //流程中的BeforeAction
        AfterAction(context ActionContext)      //流程中的AfterAction
        Destruct(context ActionContext)         //流程中的Destruct，此阶段用于释放每个请求中创建的临时资源，防止内存泄漏
    }

ActionContext接口
-----------------------

::

    type ActionContext interface {
        Request() *http.Request                //获得请求的http.Request对象
        ResponseWriter() http.ResponseWriter   //获得请求的http.ResponseWriter

        ResponseBody() []byte                  //获得响应Body，内容在Action处理中填充
    }


一个示例
----------

定义使用的ActionContext
###########################

::

    type DemoActionContext struct {
        Req        *http.Request
        RespWriter http.ResponseWriter
        RespBody   []byte
    }

    func (this *DemoActionContext) Request() *http.Request {
        return this.Req
    }

    func (this *DemoActionContext) ResponseWriter() http.ResponseWriter {
        return this.RespWriter
    }

    func (this *DemoActionContext) ResponseBody() []byte {
        return this.RespBody
    }

定义Controller
###################

::

    type IndexController struct {
    }

    func (this *IndexController) NewActionContext(req *http.Request, respWriter http.ResponseWriter) controller.ActionContext {
        return &DemoActionContext{
            Req:        req,
            RespWriter: respWriter,
        }
    }

    func (this *IndexController) BeforeAction(context controller.ActionContext) {
        acontext := context.(*DemoActionContext)

        acontext.RespBody = append(acontext.RespBody, []byte(" index before ")...)
    }

    例如将 "GET /index/index" 的请求在这个Action中处理
    func (this *IndexController) IndexAction(context *DemoActionContext) {
        context.RespBody = append(context.RespBody, []byte(" index action ")...)
    }

    例如将 "GET /index/redirect" 的请求在这个Action中处理
    func (this *IndexController) RedirectAction(context *DemoActionContext) {
        system.Redirect302("https://github.com/Andals/gobox")
    }

    func (this *IndexController) AfterAction(context controller.ActionContext) {
        acontext := context.(*DemoActionContext)

        acontext.RespBody = append(acontext.RespBody, []byte(" index after ")...)
    }

    func (this *IndexController) Destruct(context controller.ActionContext) {
        println(" index destruct ")
    }

