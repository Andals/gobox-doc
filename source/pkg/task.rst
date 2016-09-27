.. _task:

task
==============

.. contents:: 目录

简介
------
这个包简单封装了执行task的方法。

数据结构
------------
::

    type TaskFunc func(params url.Values)
          
    type Task struct {
        taskList map[string]TaskFunc     
    }     


使用
------

import::

    import (
        "andals/gobox/task"
    )

初始化
***********************

::

    func NewTask() *Task


添加task
************

::

    func (this *Task) Add(key string, tf TaskFunc)
    

执行task
************

::

    func (this *Task) Run(taskName, paramStr string) error

 taskName为用户自己获取并传入，task参数paramStr以urlEncode形式传入如a=testA&b=testB&c=testC
