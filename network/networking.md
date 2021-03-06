# 
### TCP基础
> N个应用程序共享一条连接互联网的线路. TCP把每个通信拆分为很小的信息包, 因此用户感觉是几个程序同时在通信.

#### 寻址
> IP为目的地, 每台机器都有唯一的IP. 端号是这台机器上每个程序的唯一标识.

#### 保证数据的完整性
> 每个信息包都有验证码, TCP要求接收方收到每个包都反馈一下, 以此确认对方是否收到完整的信息.
> 包里面还有序列号, 接收方收到很多打散的包后, 会按序列号组织包顺序.

#### 路由
> 信息从一台机器到另一台机器要经过Internet上的很多地方, 路由器是传输这些包的机器.

#### 安全
> SSL和TLS

### Server/Client模式
> 服务器一直监听, 客户端总是开始申请连接的.

#### 端口
> web server默认80. 实际上在 /etc/services 里可以看到官方分配的列表.
> 一般而言程序端口开在 1024-65535 间.
> 客户端的端口一般由操作系统随机分发, server按照client请求时的端口返回信息即可.

### UDP
> 它只保证收到的数据是完整的: 不保证数据能否被收到, 是不是只接收一次,
> 顺序是否一致. 但它非常快.

### 物理和ethen
> TCP/IP协议可以在不同物理网络硬件之间传输信息.
> 以太网的一个最主要特点: 可以向本地网络所有工作站广播信息包.
> 我一般提到的内网通信, 就是以太网通信: 通过比较IP的头几位, 确定通信方是否在同一以太网内, 否则, 就需要先发到本地路由器上再传送到Internet上.

### socket
> 网络上的两个程序通过一个双向的通信连接实现数据的交换，这个连接的一端称为一个socket。它是跨平台的。
> Python Lib/socket.py 实现了 socket 的 Python 接口。
- socket.socket()
- socket.recv(buff)
- socket.connect((host, port))
- socket.send(bytes)
- socket.sendall(bytes)
- socket.bind((host, port))
- socket.listen(int)
- socket.accept() -> (conn, addr) -> addr=host,port

### socket

### urllib
- urllib.request.urlopen()



# UDP

IP 协议只负责寻址，把若干 packet 组织成对话的是 IP
协议之上的协议要做的事情，它们需要2种特性：
1. 多路复用 multiplexing：给每个包打上标记，以区分不同对话间的包。
2. 可靠性 reliable transport：
    - 修复受损的包
    - 再次发送丢失的包
    - 保证包的顺序
    - 丢弃重复的包

UDP 协议只实现了上述第一点，第二点则需要程序本身来考虑。TCP
协议则满足了这两种特性。

## Port

port：借鉴无线电广播频道的模式，同一个机器（一个IP地址）内有不同端口用来处理不同对话。port
的范围在 16-bit 内（0-2^16)。

来往通信的两个程序需要带上
source(IP:port)-destination(IP:port)，至于如何分配端口，有3种方式：
1. 约定：官方机构设计的端口，例如 UDP 默认为53。
2. 自动分配：
3. 手动分配：

范围：
1. 0-1023：需要管理员权限
2. 1024-49151：
3. 49152-65535：

## Sockets

沿用了C的 socket。

## Binding to Interfaces

socket.bind() 不仅仅是端口，而已一对 (ip, port) 作为 socket name。
试想一下自己的IP地址是192.168.1.5：
可以同时开两个 server 监听 192.168.1.5:8080 和 127.0.0.1:8080 而不会冲突。
不过 0.0.0.0 是一个特殊值，它包括了本地 127.0.0.1 和对外的ip。

## UDP Fragmentation 帧

有最大限长的。

## Socket Options

Linux man 的 socket, udp, tcp 部分有 socket options 的完整解释。
在 Python 中可以通过 setsockopt() / getsockopt()。
```python
value = s.getsockopt(socket.SOL_SOCKET, socket.SO_BROADCAST)
s.setsockopt(socket.SOL_SOCKET, socket.SO_BROADCAST, value)
```

## Broadcast

`setsockopt(socket.SOL_SOCKET, socket.SO_BROADCAST, -1)`

比如设在 client 中，当向广播地址 192.168.1.255 发送 udp 请求，所有本地监听的 server 都会收到。

## When to Use UDP

当丢包和包的顺序等都不重要，而是希望请求响应小而快的时候。


# TCP

## TCP 是如何工作的

1. 在每个数据包中加上序列号

# 服务器构造

## 多线程
一个进程中的多个线程，监听同一个端口。
```python
from threading import Thread

def start_threads(listener, workers=4):
    for i in range(workers):
        # run_server 接收一个 socket 实例并启动它
        t = Thread(target=run_server, args=(listener,))
        t.start()

if __name__ == '__main__':
    # 构造一个 socket 实例
    listener = create_socket(addr)
    start_threads(listener)
```

## SocketServer


