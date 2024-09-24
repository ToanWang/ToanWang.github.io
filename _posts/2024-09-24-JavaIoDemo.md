---
layout:     post
title:      Java Io Demo
subtitle:   JavaBase
date:       2024-09-19
author:     toan
catalog:	true
tags:
    - IO
    - NIO
---

示例代码：https://github.com/toanwang/SpringComponent/tree/master/IO-demo/src/main/java/org/io/base/NIO

## IO模型

### BIO

* 阻塞`IO`，每一个线程只能处理一个链接
  * 服务端： `ServerSocket(PORT)`
    * `serverSocket.accept()`：阻塞，等待链接，获取`Socket`对象
  * 客户端：`Socket(HOST, PORT)`
* 面向流：使用`OutputStream`和`InputStream`

### NIO

* 面向缓冲区(块)

* 三个核心模块

  * `Buffer`：双向的
    * `allocate()`：分配大小
    * 主要的三个参数
      * `position()`：下一次读取位置
      * `limit()`：缓冲区终点，不能读取超过限制的位置
      * `capacity()`：容量，不可变
    * `clear()`：写模式，清空缓冲
    * `compact()`：写模式，不清空缓冲
    * `flip()`：读模式
  * `Channel`：`channel`必须通过`buffer`进行读写操作，绑定`IP+PORT`
    * `FileChannel`：文件读写
    * `DatagramChannel`：`UDP`数据读写
    * `ServerSocketChannel`：服务端
      * 使用`bind(new InetSocketAddress(PORT))`绑定端口号
      * 使用`ServerSocketChannel.accept()`获取链接的`SocketChannel`
      * 使用`SelectionKey`获取有数据的`SocketChannel`
      * 使用`SocketChannel.read(buffer)`，将数据读到`buffer`中
      * 注册`sChannel.register(selector, SelectionKey.OP_READ);`
    * `SocketChannel`：客户端
      * `SocketChannel.open(new InetSocketAddress(IP, PORT))` 通道绑定服务器
  * `Selector`：绑定`channel`
    * `Selector.open()`：获取监视器对象
    * `Selector.select()`：阻塞，获取所有通道中事件
    * `Selector.selectedKeys()`：获取所有的`selectedKey`
      * 每个`selectdKey`有一个`SelectableChannel`和`Selector`
      * 事件分为四种
        * `OP_READ`：`1<<0`
        * `OP_WRITE`：`1<<2`
        * `OP_CONNECT`：`1<<3`
        * `OP_ACCEPT`：`1<<4`


## 零拷贝

* `mmap`：适合小数据读写
  * 通过内存映射，将文件映射到内核缓冲区，减少用户空间到内核空间的拷贝
* `sendfile`：适合大文件传输
  * 不经过用户态，直接在内核空间拷贝，减少一次上下文切换