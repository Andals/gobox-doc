

.. _pidfile:

创建和清理PID文件
==============
这个包封装了PID文件创建和清理的功能

.. contents:: 目录

重要数据结构
--------------

::

    type File struct {
        Path string
    }

    type Pid struct {
        Id int
    }

    type PidFile struct {
        *Pid
        *File

        IsTmp      bool
        OriginPath string
    }


使用
-------

引入包
********************

::

    import (
        "andals/gobox/pidfile"
    )


启动时创建PID文件
********************

::

    path := "./pidfiletest.pid"
    pf, err := pidfile.CreatePidFile(path)
    if err != nil {
        fmt.Println(err.Error())
    }


退出前清理PID文件
******************************

::

    err := pidfile.ClearPidFile(pf)
    if err != nil {
        fmt.Println(err.Error())
    }


