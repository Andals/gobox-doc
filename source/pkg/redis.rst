.. _redis:

Redis
==============

.. contents:: 目录

简介
------
这个包用于执行redis操作

driver使用了 `redigo <https://github.com/garyburd/redigo>`_

用法
-----

import
^^^^^^^^^^
示例::

    import (
        "andals/gobox/redis"
    )

Config
^^^^^^^^^^
定义::

    const (
        DEFAULT_CONNECT_TIMEOUT = 10 * time.Second
        DEFAULT_READ_TIMEOUT    = 10 * time.Second
        DEFAULT_WRITE_TIMEOUT   = 10 * time.Second
    )

    type Config struct {
        LogLevel int

        Host string
        Port string
        Pass string

        ConnectTimeout time.Duration
        ReadTimeout    time.Duration
        WriteTimeout   time.Duration
    }

    func NewConfig(host, port, pass string) *Config {
        return &Config{
            LogLevel: golog.LEVEL_INFO,

            Host: host,
            Port: port,
            Pass: pass,

            ConnectTimeout: DEFAULT_CONNECT_TIMEOUT,
            ReadTimeout:    DEFAULT_READ_TIMEOUT,
            WriteTimeout:   DEFAULT_WRITE_TIMEOUT,
        }
    }

使用NewConfig方法获得初始化结构::

    func NewConfig(host, port, pass string) *Config

Client
^^^^^^^^^
这个对象用于直接操作redis，里面有一些基础操作方法和常用方法的封装::

    func NewClient(config *Config, logger golog.ILogger) (*Client, error)

基础读写方法
.............

立即执行redis命令::

    func (this *Client) Do(cmd string, args ...interface{}) (*Reply, error)

暂存执行操作，这些操作之后可以使用pipelining方式或事务方式执行::

    func (this *Client) Send(cmd string, args ...interface{}) error

Pipelining执行暂存的操作::

    func (this *Client) ExecPipelining() ([]*Reply, error)

开始事务::

    func (this *Client) BeginTrans() error

放弃事务::

    func (this *Client) DiscardTrans() error

执行事务::

    func (this *Client) ExecTrans() ([]*Reply, error)

Reply
^^^^^^
操作返回结果，需要自己调用对应方法将结果转换为指定类型的值::

    func (this *Reply) Bool() (bool, error)
    func (this *Reply) ByteSlices() ([][]byte, error)
    func (this *Reply) Bytes() ([]byte, error)
    func (this *Reply) Float64() (float64, error)
    func (this *Reply) Int() (int, error)
    func (this *Reply) Int64() (int64, error)
    func (this *Reply) Int64Map() (map[string]int64, error)
    func (this *Reply) Ints() ([]int, error)
    func (this *Reply) Struct(s interface{}) error
    func (this *Reply) String() (string, error)
    func (this *Reply) StringMap() (map[string]string, error)
    func (this *Reply) Strings() ([]string, error)
    func (this *Reply) Uint64() (uint64, error)

转换为结构体时，结构体字段定义示例::

    Field int `redis:"myName"`

Demo::

    package main

    import (
        "andals/gobox/redis"

        "fmt"
    )

    type Demo struct {
        Id   int `redis:"id"`
        Name string `redis:"name"`
    }

    func main() {
        config := &redis.Config{
            Host: "127.0.0.1",
            Port: "6379",
            Pass: "123",
        }
        client, _ := redis.NewClient(config, nil)

        client.Do("set", "a", 1)
        reply, _ := client.Do("get", "a")
        fmt.Println(reply.Int())

        client.Do("hmset", "h1", "id", 1, "name", "a")
        reply, _ = client.Do("hgetall", "h1")
        demo := new(Demo)
        reply.Struct(demo)
        fmt.Println(demo)
    }

Output::

    1 <nil>
    &{1 a}
