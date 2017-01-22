.. _system:

System
=============
服务端简单MVC的实现，实现了Handler接口

.. contents:: 目录

system实现了http.Handler接口，提供ServeHTTP方法，这里面就是http-server处理流程的实现。 `http-server的处理流程 </pkg/http/http-server/index.html>`_

Dispatch流程说明
---------------------

BeforeAction
###############

这里用于处理每个Action中的公共逻辑部分，在Action前执行，如生成logId、登录验证等。

示例：

::

    func (this *BaseController) BeforeAction(context gcontroller.ActionContext) {
        acontext := context.(*ApiContext)
        realIp := acontext.RemoteRealAddr.Ip

        sl, e := glog.NewSimpleLogger(log.UserLogWriter, glog.LEVEL_INFO, glog.NewWebFormater(acontext.Rid, []byte(realIp)))
        if e != nil {
            acontext.UserLogger = new(glog.NoopLogger)
        } else {
            acontext.UserLogger = glog.NewAsyncLogger(sl, log.UserLogRoutineCh)    //创建本次请求的userLogger对象
        }

        acontext.RespWriter.Header().Add("X-Powered-By", "gohttp")
    }

Action
########

这里处理请求对应的Action逻辑。


AfterAction
#############

示例：

::

    func (this *BaseController) AfterAction(context gcontroller.ActionContext) {
        acontext := context.(*ApiContext)

        acontext.RespBody = misc.ApiJson(acontext.ApiData.Data, acontext.ApiData.Err)      //统一Api输出
    }

处理对应请求的Action逻辑中的公共逻辑部分，在Action后执行，如Api接口通常会按照固定接口格式统一发送数据等。

Destruct阶段说明
---------------------

这里销毁请求中逻辑中创建的一些临时变量，防止内存溢出，如每个请求的logger等。

示例：

::

    func (this *BaseController) Destruct(context gcontroller.ActionContext) {
        acontext := context.(*ApiContext)

        acontext.UserLogger.Free()   //销毁本次请求的userLogger对象
    }


JumpOutAction使用
----------------------

这个方法用于跳出Dispatch流程，直接进入Destruct阶段，如Redirect请求等。 ``func Redirect302(url string)`` 就是一个典型的应用实现。

::
    
    func Redirect302(url string) {
        JumpOutAction(redirect302, url)
    }
