

.. _ipquery:

IP定位查询
==============
这个包封装了IP定位查询的功能

.. contents:: 目录

重要数据结构
--------------

IpRange::

    type IpRange struct {
        Begin uint32
        End   uint32
        Data  []byte
    }

IpData::

    type IpData []*IpRange


使用
-------



导入包
********************

::

    import (
        "andals/gobox/ipquery"
    )


加载IP数据
********************

::

    df := "testdata/test_10000.data"
    err := ipquery.Load(df)
    if err != nil {
        fmt.Println(err)
    }

查询IP信息
******************************

::

    ip := "61.149.208.1"
    dt, err := ipquery.Find(ip)
  
    if err != nil {
        fmt.Println(err)
    } else {
        fmt.Println(ip, string(dt))
    }


