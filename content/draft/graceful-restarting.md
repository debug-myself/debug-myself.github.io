---
title: "如何优雅重启网络服务"
subtitle: ""
date: 2024-11-01T23:49:06+08:00
lastmod: 2024-11-01T23:49:06+08:00
keywords: []
description: ""
author: "debugmyself"
type: ""
image: ""

tags: ["Go", "Linux"]
toc: true
disable_comments: true

draft: true
---

## TL;DR
- [ ] 交给GPT总结

本文介绍了`Cloudflare`用`Go`语言实现的能保证网络程序`graceful restarting`的库--[tableflip](https://github.com/cloudflare/tableflip)的实现原理.

## 背景
对于一个持续迭代的网络服务, 变更是家常便饭. 常规的变更流程如下:
```mermaid
graph LR
    first["更新程序/配置"]
    second["停止当前服务"]
    third["启动服务"]

    first --> second;
    second --> third;
```

1. **更新程序/配置**: 当修复了已知bug或者是新增功能时, 需要更新程序. 或者是更改了配置;
2. **停止当前服务**: 为避免和新程序冲突, 需要暂停当前服务;
3. **启动新的程序**: 再次启动程序让新程序/配置生效;

这个变更过程存在很大的问题: 变更过程会影响正在使用

很多网络服务都被要求持续无间断运行, 这就意味着在 *更新服务* 或者 *重新加载配* 置期间不能影响用户使用, 即优雅的重启服务(`graceful restarting`). 许多成熟的项目都支持`graceful restarting`, 如`Nginx`, `ssh server`. 


很多场景下需要服务能保证7*24h提供服务, 但是对于一个活跃的项目而言, 更新是家常便饭. 更新一般意味: `停止-启动`服务, 这个`停止-启动`服务的时间窗口内就无法提供服务, 对于用户体验非常不好. 所以像`Nginx`这样的成熟项目都是支持`gracefully restart`.
普通项目也有`gracefully restart`的需求;

## 要求
1. 升级过程不影响正在使用的用户;
2. 升级后新接入的用户的流量被新启动的进程处理;
3. ...

## tableflip

### tableflip使用示例

### 前置知识
1. 文件: 子进程可以继承父进程的文件描述符, 文件描述符的标志位CLOEXEC;
2. IPC -> pipe & 信号
3. 


### tableflip实现分析

## 应用实例


## 参考
* [Graceful upgrades in Go](https://blog.cloudflare.com/graceful-upgrades-in-go/)
* [GLB part 2: HAProxy zero-downtime, zero-delay reloads with multibinder](https://github.blog/news-insights/the-library/glb-part-2-haproxy-zero-downtime-zero-delay-reloads-with-multibinder/)


Q: 连接器server父子进程共享了日志文件的文件表项吗? 为什么日志给是正常?
A: 因为打开日志文件时设置了O_APPEND, O_APPEND 在多个进程或线程对同一文件描述符写入时，能避免写入冲突。操作系统会确保每次写入都是原子的，即便多个进程或线程同时写入，也不会发生写入位置交错的问题（比如写到相同的位置）。