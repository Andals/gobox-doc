.. _client:

client
=============
这个包用于发起http请求，类似curl的功能

.. contents:: 目录

重要数据结构
--------------

Response
^^^^^^^^^^^^

::

    type Response struct {
        T        time.Duration  //本次请求总耗时
        Contents []byte         //响应body内容

        *http.Response
    }

响应如果没有错误，则Contents会自行读取填充，无须用户再调用 ``ioutil.ReadAll``

用法
------

import
^^^^^^^^^^
示例::

    import (
        "andals/gobox/http/client"
    )


get请求
^^^^^^^^^^^^^^^

示例::

    c := NewClient(logger).
        SetTimeout(time.Second * 3).     //设置请求超时
        SetMaxIdleConnsPerHost(10)       //每个host保持多少长连接

    extHeaders := map[string]string{
        "GO-CLIENT-1": "gobox-httpclient-1",
        "GO-CLIENT-2": "gobox-httpclient-2",
    }
    req, _ := client.NewRequestForGet("http://www.vdocker.com/test.php", "127.0.0.1", extHeaders)

    resp, err := c.Do(req, 1)
    fmt.Println(string(resp.Contents), resp.T.String(), err)
