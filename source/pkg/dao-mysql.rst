.. _dao-mysql:

mysql操作
==========

.. contents:: 目录

简介
------
这个包封装了操作mysql最常用到的一些方法。

driver部分使用了 `go-sql-driver <https://github.com/go-sql-driver/mysql>`_

重要数据结构
-------------

ColQueryItem::

    type ColQueryItem struct {
        Name      string         //列名称
        Condition string         //查询条件（=、>、< ...）
        Values    []interface{}  //列值
    }

这个结构体有两个用途：

1. 查询时用于设置需要的列的查询条件
#. 编辑时用于设置要更新的列的值

用法
-----

import
^^^^^^^^^^
示例::

    import (
        "andals/gobox/dao/mysql"
    )

Dao
^^^^^
这个对象用于直接操作mysql，里面有一些基础操作方法和常用方法的封装。

基础读写方法
.............

写操作基础方法
***************

func::

    func (this *Dao) Exec(query string, args ...interface{}) (sql.Result, error)

读操作基础方法
***************

读多个结果::

    func (this *Dao) Query(query string, args ...interface{}) (*sql.Rows, error)

读一个结果::

    func (this *Dao) QueryRow(query string, args ...interface{}) *sql.Row

事务操作
.............

func::

    func (this *Dao) Begin() error
    func (this *Dao) Commit() error
    func (this *Dao) Rollback() error

事务开启后的读写，都可以直接调用本包中封装的所有读写方法，无须额外判断。


常用读写方法
.............

插入多条数据
***************

func::

    func (this *Dao) Insert(tableName string, colNames []string, colsValues ...[]interface{}) (sql.Result, error)

参数说明使用插入sql::

    intert into tableName (colNames ...) values (colsValues[0]), (colsValues[1]) ...

通过id删除一条数据
********************

func::

    func (this *Dao) DeleteById(tableName string, id interface{}) (sql.Result, error)

参数说明使用删除sql::

    delete from tableName where id = id

通过id编辑一条数据
********************

func::

    func (this *Dao) UpdateById(tableName string, id interface{}, setItems ...*ColQueryItem) (sql.Result, error)

参数说明使用编辑sql::

    update tableName set setItems[0].Name = setItems[0].Values[0], setItems[1].Name = setItems[1].Values[0] ... where id = id

通过id查询一条数据
********************

func::

    func (this *Dao) SelectById(what, tableName string, id interface{}) *sql.Row

参数说明使用查询sql::

    select what from tableName where id = id

通过多个id查询多条数据
******************************

func::

    func (this *Dao) SelectByIds(what, tableName string, ids []interface{}) (*sql.Rows, error)

参数说明使用查询sql::

    select what from tableName where id in (ids[0], ids[1]...)

SimpleQueryBuilder
^^^^^^^^^^^^^^^^^^^
这个对象用于拼装最常用到的sql语句。

它的设计并没有想解决全部的sql语句拼装，我们总结了项目中最常用到的一些重点sql语句，通过它可以很方便的拼装出来。

里面的方法名和sql语句中的关键字保持一致，很容易理解。

示例::

    this.Sqb.
        Insert(tableName, colNames...).
        Values(colsValues...)

    this.Sqb.
        Delete(tableName).
        WhereConditionAnd(NewColQueryItem("id", COND_EQUAL, id))

    this.Sqb.
        Update(tableName).
        Set(setItems...).
        WhereConditionAnd(NewColQueryItem("id", COND_EQUAL, id))

    this.Sqb.
        Select(what, tableName).
        WhereConditionAnd(NewColQueryItem("id", COND_EQUAL, id))

常用where条件支持::

    const (
        COND_EQUAL         = "="       //值放在ColQueryItem.Values[0]
        COND_NOT_EQUAL     = "!="             .
        COND_LESS          = "<"              .
        COND_LESS_EQUAL    = "<="             .
        COND_GREATER       = ">"              .
        COND_GREATER_EQUAL = ">="             .
        COND_IN            = "in"      //值放在ColQueryItem.Values[0]、[1] ...
        COND_NOT_IN        = "not in"         .
        COND_LIKE          = "like"    //值放在ColQueryItem.Values[0]，需要自己添加"%"
        COND_BETWEEN       = "between" //ColQueryItem.Values[0]为min值，[1]为max值
    )
