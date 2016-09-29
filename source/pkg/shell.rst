.. _shell:

执行shell命令
==============
这个包封装了一些常用的shell命令执行方法

.. contents:: 目录

重要数据结构
--------------

ShellResult::

    type ShellResult struct {
        Ok     bool       //执行结果
        Output []byte     //命令输出
    }


使用
-------

import::

    import (
        "andals/gobox/shell"
    )

执行指定shell命令
********************

::

    func RunCmd(cmdStr string) *ShellResult

执行指定命令，内容输出到终端
******************************

::

    func RunCmdBindTerminal(cmdStr string)

以指定用户执行命令
***********************

::

    func RunAsUser(cmdStr string, username string) *ShellResult

执行rsync
****************

::

    func Rsync(host string, sou string, dst string, excludeFrom string, sshUser string, timeout int) *ShellResult

从shell中获取参数列表
****************

::

    func GetParamsFromShell(shell string, keyMap map[string]string) map[string]string

