.. _writer:

writer
=============
这个包实现记录log操作的底层写部分

.. contents:: 目录


重要数据结构
--------------

IWriter
^^^^^^^^^^^^

::

    import (
        "io"
    )

    type IWriter interface {
        io.Writer

        Flush() error
        Free()
    }

log可以被写到多处存储中，最常见的如本地磁盘、消息队列等，这个结构定义了这些写操作的接口。

空操作
-----------

::

    type Noop struct {
    }

什么都不做时使用，这是为了统一调用，减少if判断。

写操作到文件
--------------

固定文件
^^^^^^^^^^

Demo::

    import (
        "andals/gobox/log/writer"
    )

	path := "/tmp/test.log"

	w, _ := writer.NewFileWriter(path)

	w.Write([]byte("test file writer\n"))

固定文件的切割，通常使用额外工具来做，例如logrotate

写时自动切割
^^^^^^^^^^^^^^

Demo::

    import (
        "andals/gobox/log/writer"
    )

    const (
        SPLIT_BY_DAY  = 1   //按天写文件
        SPLIT_BY_HOUR = 2   //按小时写文件
    )

	path := "/tmp/test.log"

	w, _ := writer.NewFileWriterWithSplit(path, SPLIT_BY_DAY)     //实际文件名例如：test.log.20160719
	w.Write([]byte("test file writer with split by day\n"))

	w, _ = writer.NewFileWriterWithSplit(path, SPLIT_BY_HOUR)     //实际文件名例如：test.log.2016071909
	w.Write([]byte("test file writer with split by hour\n"))
