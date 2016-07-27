.. _buffer:

buffer
=============
这个包实现对writer添加buffer，提升效率。

buffer是writer的装饰，所以依旧是IWriter。

.. contents:: 目录

使用
------

import::

    import (
        "andals/gobox/log/buffer"
    )

demo::

    import (
        "andals/gobox/log/buffer"
        "andals/gobox/log/writer"
    )

	path := "/tmp/test_buffer.log"
	bufsize := 4096

	fw, _ := writer.NewFileWriter(path)
	bw := buffer.NewBuffer(fw, bufsize)

	bw.Write([]byte("test file writer with buffer and time interval\n"))
	time.Sleep(time.Second * 5)
	bw.Free()

本buffer会自动启动一个goroutine，在后台自动定时将buffer中的信息刷到对应的write中调用Write方法。
