.. _inotify:

Inotify（文件系统通知）
==============

.. contents:: 目录

简介
------
这个包用于系统inotify调用

Demo
------

demo::

    package main

    import (
        "andals/gobox/inotify"

        "path/filepath"
        "os"
        "fmt"
    )

    func main() {
        path:="/tmp/a.log"
        
        watcher, _ := inotify.NewWatcher()
        watcher.AddWatch(path, inotify.IN_ALL_EVENTS)
        watcher.AddWatch(filepath.Dir(path), inotify.IN_ALL_EVENTS)

        events, _ := watcher.ReadEvents()
        for _, event := range events {
            showEvent(event)
        }

        os.OpenFile(path, os.O_RDONLY, 0)
        watcher.RmWatch(path)
        watcher.AddWatch(path, inotify.IN_ALL_EVENTS)

        for i := 0; i < 50; i++ {
            events, _ := watcher.ReadEvents()
            for _, event := range events {
                if watcher.IsUnreadEvent(event) {
                    fmt.Println("it is a last remaining event")
                }
                showEvent(event)
            }
        }

        watcher.Free()
        fmt.Println("bye")
    }

    func showEvent(event *inotify.Event) {
        fmt.Println(event)

        if event.InIgnored() {
            fmt.Println("IN_IGNORED")
        }

        if event.InAttrib() {
            fmt.Println("IN_ATTRIB")
        }

        if event.InModify() {
            fmt.Println("IN_MODIFY")
        }

        if event.InMoveSelf() {
            fmt.Println("IN_MOVE_SELF")
        }

        if event.InMovedFrom() {
            fmt.Println("IN_MOVED_FROM")
        }

        if event.InMovedTo() {
            fmt.Println("IN_MOVED_TO")
        }

        if event.InDeleteSelf() {
            fmt.Println("IN_DELETE_SELF")
        }

        if event.InDelete() {
            fmt.Println("IN_DELETE")
        }

        if event.InCreate() {
            fmt.Println("IN_CREATE")
        }
    }
