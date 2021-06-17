# Nginx概述

## Nginx简介

​	Nginx是一个轻量级的、高性能的、基于 Http 的、反向代理和web服务器，同时也提供了IMAP/POP3/SMTP服务。

​	Nginx是由伊戈尔·赛索耶夫使用C语言为俄罗斯访问量第二的Rambler.ru站点开发的，第一个公开版本0.1.0发布于2004年10月4日，其将源代码以类BSD许可证的形式发布，因它的稳定性、丰富的功能集、示例配置文件和低系统资源的消耗而闻名。2019年3月11日，Nginx公司被F5 Networks以6.7亿美元收购。

​	Nginx 的官网： http://nginx.org

## 代理服务器

###  正向代理

#### 隐藏服务器

![1615950225835](D:\repositories\others\research\java\nginx\images\1615950225835.png)

#### 翻墙

![1615950313689](D:\repositories\others\research\java\nginx\images\1615950313689.png)

#### 加速

![1615950345680](D:\repositories\others\research\java\nginx\images\1615950345680.png)

#### 缓存

![1615950366132](D:\repositories\others\research\java\nginx\images\1615950366132.png)

#### 授权

![1615950525881](D:\repositories\others\research\java\nginx\images\1615950525881.png)

### 反向代理

#### 保护隐藏服务器

![1615951559073](D:\repositories\others\research\java\nginx\images\1615951559073.png)

#### 分布式路由

![1615951576657](D:\repositories\others\research\java\nginx\images\1615951576657.png)

#### 负载均衡

![1615951621533](D:\repositories\others\research\java\nginx\images\1615951621533.png)

#### 动静分离

![1615951640566](D:\repositories\others\research\java\nginx\images\1615951640566.png)

#### 数据缓存

![1615951653781](D:\repositories\others\research\java\nginx\images\1615951653781.png)



### 正向代理和反向代理的区别

- 正向代理是对客户端的代理；反向代理是对服务端的代理。
- 正向代理架设在客户端；反向代理架设在服务端。
- 正向代理客户端知道真正要访问的地址；反向代理客户端只知道代理服务器的地址。



## Nginx特点

### 高并发

​	一个 Nginx 服务器在不做任何配置的情况下并发量可达 1000 左右。在硬件条件允许的前提下，Nginx 可以支持高达 5万左右的并发量（除了 Nginx 的设置外，Linux 主机需要做大量的设置来配合 Nginx）。
​	对比一下 Tomcat。Tomcat 服务器默认的并发量为 150（不做任何配置）。

### 低消耗

​	官方给出的测试结果，10000 个非活跃连接，在 Nginx 中仅消耗 2.5M 内存。

### 热部署

​	可以在 7*24 小时不间断服务的前提下，进行 Nginx 版本的平滑升级，Nginx 配置文件的平滑修改。即在不停机的情况下升级 Nginx，修改替换 Nginx 配置文件。

### 高可用

​	Nginx之所以可以实现高并发，是因为其具有很多工作进程 worker。当这些工作进程中的某些出现问题停止工作时，并不会影响整个系统的整体运行。因为其它 worker 会接替那些出问题的线程。

### 高扩展

​	Nginx很多功能都已经开发好并模块化。若需要哪些功能，只需要安装相应功能的扩展模块即可。根据编写扩展模块所使用的语言的不同，可以划分为两类：C 语言扩展模块与 LUA 脚本扩展模块。 http://openresty.org/cn/



## Nginx的下载和安装

### 下载

http://nginx.org/en/download.html

![1615962395321](D:\repositories\others\research\java\nginx\images\1615962395321.png)



### windows安装

解压缩下载的压缩包即可

![1615962755644](D:\repositories\others\research\java\nginx\images\1615962755644.png)

### Nginx相关命令

#### 查看命令

```shell
nginx -h
```

![1615963002646](D:\repositories\others\research\java\nginx\images\1615963002646.png)



#### 查看版本

显示版本

```shell
nginx -v
```

![1615963164339](D:\repositories\others\research\java\nginx\images\1615963164339.png)

显示详细版本信息和配置信息

```shell
nginx -V
```

![1615963229294](D:\repositories\others\research\java\nginx\images\1615963229294.png)



#### 测试配置文件

```shell
#测试配置文件是否正确，默认只测试默认的配置文件 conf/nginx.conf
nginx -t
```

```shell
#测试配置文件是否正确，并显示配置文件内容
nginx -T
```

```shell
#在配置文件测试过程中，禁止显示非错误信息，即只显示错误信息
nginx -tq
```

#### 启动Nginx

```shell
#windows下可直接双击nginx.exe，或者执行start nginx
start nginx 
```

```shell
#启动指定配置文件路径，直接执行nginx则默认加载安装目录下conf/nginx.conf
nginx -c file
```

启动成功后无任何提示。windows下启动时命令窗口会一闪而过。

#### 关闭Nginx

```shell
#强制停止 Nginx，无论当前工作进程是否正在处理工作
nginx -s stop
```

```shell
#优雅停止 Nginx，使当前的工作进程完成当前工作后停止。
nginx -s quit
```

#### 热重启Nginx

```shell
#在不重启 Nginx 的前提下重新加载 Nginx 配置文件
nginx –s reload
```

#### 重新打开日志文件

```shell
nginx –s reopen
```

#### 指定 Nginx 配置文件的存放路径

```shell
nginx -p file
```

#### 查看nginx进程（windows）

```shell
tasklist /fi "imagename eq nginx.exe"
```





# Nginx性能调优

## 零拷贝

### 零拷贝介绍

​	零拷贝指的是，从一个存储区域到另一个存储区域的 copy 任务没有 CPU （主要指ALU，arithmetic and logic unit，算数逻辑单元）参与。零拷贝通常用于网络文件传输，以减少 CPU 消耗和内存带宽占用，减少用户空间与 CPU 内核空间的拷贝过程，减少用户上下文与 CPU 内核上下文间的切换，提高系统效率。用户空间指的是用户可操作的内存缓存区域，CPU 内核空间是指仅 CPU 可以操作的寄存器缓存及内存缓存区域。
​	用户上下文指的是用户状态环境，CPU 内核上下文指的是 CPU 内核状态环境。
​	零拷贝需要 DMA 控制器的协助。DMA，Direct Memory Access，直接内存存取，是 CPU的组成部分，其可以在 CPU 内核（算术逻辑运算器 ALU 等）不参与运算的情况下将数据从一个地址空间拷贝到另一个地址空间。



### 传统拷贝方式

![1615968799122](D:\repositories\others\research\java\nginx\images\1615968799122.png)

​	该拷贝方式共进行了 4 次用户空间与内核空间的上下文切换，以及 4 次数据拷贝，其中两次拷贝存在 CPU 参与。
​	我们发现一个很明显的问题：应用程序的作用仅仅就是一个数据传输的中介，最后将kernel buffer 中的数据传递到了 socket buffer。显然这是没有必要的。所以就引入了零拷贝。



### 零拷贝方式

![1615968874982](D:\repositories\others\research\java\nginx\images\1615968874982.png)

​	Linux 系统（CentOS6 及其以上版本）对于零拷贝是通过 sendfile 系统调用实现的。

​	该拷贝方式共进行了 2 次用户空间与内核空间的上下文切换，以及 3 次数据拷贝，但整个拷贝过程均没有 CPU 的参与，这就是零拷贝。
​	我们发现这里还存在一个问题：kernel buffer 到 socket buffer 的拷贝需要吗？kernelbuffer 与 socket buffer 有什么区别呢？DMA 控制器所控制的拷贝过程有一个要求，数据在源头的存放地址空间必须是连续的。kernel buffer 中的数据无法保证其连续性，所以需要将数据再拷贝到 socket buffer，socket buffer 可以保证了数据的连续性。
​	这个拷贝过程能否避免呢？可以，只要主机的 DMA 支持 Gather Copy 功能，就可以避免由 kernel buffer 到 socket buffer 的拷贝。



###  Gather Copy DMA  零拷贝方式

![1615968937548](D:\repositories\others\research\java\nginx\images\1615968937548.png)

​	由于该拷贝方式是由 DMA 完成，与系统无关，所以只要保证系统支持 sendfile 系统调用功能即可。

​	该方式中没有数据拷贝到 socket buffer。取而代之的是只是将 kernel buffer 中的数据描述信息写到了 socket buffer 中。数据描述信息包含了两方面的信息：kernel buffer 中数据的地址及偏移量。

​	该拷贝方式共进行了 2 次用户空间与内核空间的上下文切换，以及 2 次数据拷贝，并且整个拷贝过程均没有 CPU 的参与。
​	该拷贝方式的系统效率是高了，但与传统相比，也存在有不足。传统拷贝中 user buffer中存有数据，因此应用程序能够对数据进行修改等操作；零拷贝中的 user buffer 中没有了数据，所以应用程序无法对数据进行操作了。Linux 的 mmap 零拷贝解决了这个问题。



### mmap  零拷贝

![1615969002604](D:\repositories\others\research\java\nginx\images\1615969002604.png)

​	mmap 零拷贝是对零拷贝的改进。当然，若当前主机的 DMA 支持 Gather Copy，mmap同样可以实现 Gather Copy DMA 的零拷贝。

​	该方式与零拷贝的唯一区别是，应用程序与内核共享了 Kernel buffer。由于是共享，所以应用程序也就可以操作该 buffer 了。当然，应用程序对于 Kernel buffer 的操作，就会引发用户空间与内核空间的相互切换。

​	该拷贝方式共进行了 4 次用户空间与内核空间的上下文切换，以及 2 次数据拷贝，并且整个拷贝过程均没有 CPU 的参与。虽然较之前面的零拷贝增加了两次上下文切换，但应用程序可以对数据进行修改了。



## 多路复用器 select|poll|epoll

### 连接处理模型

​	若要理解 select、poll 与 epoll 多路复用器的工作原理，就需要首先了解什么是多路复用器。而要了解什么是多路复用器，就需要先了解什么是“多进程/多线程连接处理模型”。



- 多进程/ 多线程连接处理模型

![1615970108901](D:\repositories\others\research\java\nginx\images\1615970108901.png)

​	在该模型下，一个用户连接请求会由一个内核进程处理，而一个内核进程会创建一个应用程序进程，即 app 进程来处理该连接请求。应用程序进程在调用 IO 时，采用的是 BIO 通讯方式，即应用程序进程在未获取到 IO 响应之前是处于阻塞态的。
​	优点：内核进程不存在对app进程的竞争，一个内核进程对应一个app进程。

​	缺点：（1）若请求很多，则需要创建很多app进程。（2）一个系统的进程数量是有上限的，所以该模型不能处理高并发的情况。（3）app进程中使用的用户连接请求数据是复制于内核进程的，没有使用零拷贝，效率低，消耗系统资源。



- 多路复用连接处理模型

![1615970419133](D:\repositories\others\research\java\nginx\images\1615970419133.png)

​	在该模型下，只有一个 app 进程来处理内核进程事务，且 app 进程一次只能处理一个内核进程事务。故这种模型对于内核进程来说，存在对 app 进程的竞争。

​	在前面的“多进程/多线程连接处理模型”中我们说过，app 进程只要被创建了就会执行内核进程事务。那么在该模型下，应用程序进程应该执行哪个内核进程事务呢？谁的准备就绪了，app 进程就执行哪个。但 app 进程怎么知道哪个内核进程就绪了呢？需要通过“多路复用器”来获取各个内核进程的状态信息。那么多路复用器又是怎么获取到内核进程的状态信息的呢？不同的多路复用器，其算法不同。常见的有三种：select、poll 与 epoll。

​	app 进程在进行 IO 时，其采用的是 NIO 通讯方式，即该 app 进程不会阻塞。当一个 IO结果返回时，app 进程会暂停当前事务，将 IO 结果返回给对应的内核进程。然后再继续执行暂停的线程。

​	该模型的优点很明显，无需再创建很多的应用程序进程去处理内核进程事务了，仅需一个即可。





### 多路复用器工作原理

#### select

​	select 多路复用器是采用轮询的方式，一直在轮询所有的相关内核进程，查看它们的进程状态。若已经就绪，则马上将该内核进程放入到就绪队列。否则，继续查看下一个内核进程状态。在处理内核进程事务之前，app 进程首先会从内核空间中将用户连接请求相关数据复制到用户空间。
​	该多路复用器的缺陷有以下几点：

- 对所有内核进程采用轮询方式效率会很低。因为对于大多数情况下，内核进程都不属于
  就绪状态，只有少部分才会是就绪态。所以这种轮询结果大多数都是无意义的。
- 由于就绪队列底层由数组实现，所以其所能处理的内核进程数量是有限制的，即其能够
  处理的最大并发连接数量是有限制的。
- 从内核空间到用户空间的复制，系统开销大。

#### poll

​	poll 多路复用器的工作原理与 select 几乎相同，不同的是，由于其就绪队列由链表实现，所以，其对于要处理的内核进程数量理论上是没有限制的，即其能够处理的最大并发连接数量是没有限制的（当然，要受限于当前系统中进程可以打开的最大文件描述符数 ulimit，后面会讲到）。

#### epoll

​	epoll 多路复用是对 select 与 poll 的增强与改进。其不再采用轮询方式了，而是采用回调方式实现对内核进程状态的获取：一旦内核进程就绪，其就会回调 epoll 多路复用器，进入到多路复用器的就绪队列（由链表实现）。所以 epoll 多路复用模型也称为 epoll 事件驱动模型。
​	另外，应用程序所使用的数据，也不再从内核空间复制到用户空间了，而是使用 mmap零拷贝机制，大大降低了系统开销。

​	当内核进程就绪信息通知了 epoll 多路复用器后，多路复用器就会马上对其进行处理，将其马上存放到就绪队列吗？不是的。根据处理方式的不同，可以分为两种处理模式：LT模式与 ET 模式。

- LT模式

  LT，Level Triggered，水平触发模式。即只要内核进程的就绪通知由于某种原因暂时没有被 epoll 处理，则该内核进程就会定时将其就绪信息通知 epoll。直到 epoll 将其写入到就绪队列，或由于某种原因该内核进程又不再就绪而不再通知。其支持两种通讯方式：BIO 与NIO。

- ET模式

  ET，Edge Triggered，边缘触发模式。其仅支持 NIO 的通讯方式。当内核进程的就绪信息仅会通知一次 epoll，无论 epoll 是否处理该通知。明显该方式的
  效率要高于 LT 模式，但其有可能会出现就绪通知被忽视的情况，即连接请求丢失的情况。





## Nginx的并发处理机制

​	一般情况下并发处理机制有三种：多进程、多线程，与异步机制。Nginx 对于并发的处理同时采用了三种机制。当然，其异步机制使用的是异步非阻塞方式。
​	我们知道 Nginx 的进程分为两类：master 进程与 worker 进程。每个 master 进程可以生成多个 worker 进程，所以其是多进程的。每个 worker 进程可以同时处理多个用户请求，每个用户请求会由一个线程来处理，所以其是多线程的。

![1615972783333](D:\repositories\others\research\java\nginx\images\1615972783333.png)

​	那么，如何解释其“异步非阻塞”并发处理机制呢？
​	worker 进程采用的就是 epoll 多路复用机制来对后端服务器进行处理的。当后端服务器返回结果后，后端服务器就会回调 epoll 多路复用器，由多路复用器对相应的 worker 进程进行通知。此时，worker 进程就会挂起当前正在处理的事务，拿 IO 返回结果去响应。



## 全局模块下的调优

### worker_processes

​	用于指定 Nginx 的工作进程数量。其数值一般设置为 CPU内核数量，或内核数量的整数倍。
​	不过需要注意，该值不仅仅取决于 CPU 内核数量，还与硬盘数量及负载均衡模式相关。在不确定时可以指定其值为 auto。

### worker_cpu_affinity

​	将 worker 进程与具体的内核进行绑定。不过，若指定 worker_processes 的值为 auto，则无法设置 worker_cpu_affinity。

​	该设置是通过二进制进行的。每个内核使用一个二进制位表示，0 代表内核关闭，1 代表内核开启。也就是说，有几个内核，就需要使用几个二进制位。

![1615980605896](D:\repositories\others\research\java\nginx\images\1615980605896.png)

### worker_rlimit_nofile

​	用于设置一个 worker 进程所能打开的最多文件数量（能处理的请求数量）。其默认值与当前 Linux 系统可以打开的最大文件描述符数量相同。



## events模块下的调优

### worker_connections 1024

​	设置每一个 worker 进程可以并发处理的最大连接数。该值不能超过 worker_rlimit_nofile的值。



### accept_mutex on

当在所有worker进程中存在空闲worker时，这些空闲worker会被放置在一个阻塞队列，等待新连接的到来。这些新来的连接如何分配给这些空闲worker呢？该属性的值不同会产生两种不同的分配方式。

- on：默认值，表示当一个新连接到达时，那些没有处于工作状态的 worker 将以串行方式来处理；
- off：表示当一个新连接到达时，所有的 worker 都会被唤醒，不过只有一个 worker 能获取新连接，其它的 worker 会重新进入阻塞状态，这就是“惊群”现象。当新连接数量很多时，这种方式效率会比较高。



### accept_mutex_delay 500ms

​	该属性前提是accept_mutex=on。设置队首 worker 会尝试获取互斥锁的时间间隔。默认值为 500 毫秒。



### multi_accept on

- off：系统会逐个拿出新连接按照负载均衡策略，将其分配给当前处理连接个数最少的worker。
- on：系统会实时的统计出各个 worker 当前正在处理的连接个数，然后会按照“缺编”最多的 worker 的“缺编”数量，一次性将这么多的新连接分配给该 worker。



### use epoll

​	设置worker与客户端连接的处理方式。Nginx会自动选择适合当前系统的最高效的方式。当然，也可以使用 use 指令明确指定所要使用的连接处理方式。user 的取值有以下几种：

- select | poll | epoll

  这是三种多路复用机制。select 与 poll 工作原理几乎相同，而 epoll 的效率最高，是现在使用最多的一种多路复用机制。

- rtsig

  realtime signal，实时信号，Linux 2.2.19+的高效连接处理方式。但在 在 Linux 2.6 版本后，不再支持该方式。

- kqueue

  应用在 BSD 系统上的 epoll

-  /dev/poll

  UNIX 系统上使用的 poll



## http模块下的调优



### 非调优属性

```perl
#将当前目录(conf 目录)中的 mime.types 文件包含进来
include mime.types;

#对于无扩展名的文件，默认其为 application/octet-stream 类型，即 Nginx 会将其作为一个八进制流文件来处理
default_type application/octet-stream;

#设置请求与响应的字符编码
charset utf-8;
```



### sendfile on

​	设置为 on 则开启 Linux 系统的零拷贝机制，否则不启用零拷贝。当然，开启后是否起作用，要看所使用的系统版本。CentOS6 及其以上版本支持 sendfile 零拷贝。



###   tcp_nopush on

- on：以单独的数据包形式发送 Nginx 的响应头信息，而真正的响应体数据会再以数据包的形式发送，这个数据包中就不再包含响应头信息了。当向客户端响应的数据量比较大时，可以开启，可以降低数据冗余，减少数据发送量。
- off：默认值，响应头信息包含在每一个响应体数据包中



### tcp_nodelay on

- on：不设置数据发送缓存，即不推迟发送，适合于传输小数据，无需缓存。
- off：开启发送缓存。若传输的数据是图片等大数据量文件，则建议设置为 off



### keepalive_timeout 60

​	设置客户端与Nginx间所建立的长连接的生命超时时间，时间到达，则连接将自动关闭。单位秒。

​	0表示不支持长连接。

​	连接时间也要看浏览器支持的最大时间。



### keepalive_requests 10000

​	设置一个长连接最多可以发送的请求数。该值需要在真实环境下测试。



### client_body_timeout 10

​	设置客户端获取 Nginx 响应的超时时限，即一个请求从客户端发出到接收到 Nginx 的响应的最长时间间隔。若超时，则认为本次请求失败。

​	该参数在建立连接后会传递给客户端，由客户端来判断。



# 请求定位

## 路径匹配优先级

路径匹配优先级如下：

​	普通匹配 < 长路径匹配 < 正则匹配 < 短路匹配 < 精确匹配

- 普通匹配

```perl
#只要请求以/aaa开头就能命中
location /aaa {
    ......
}
```

- 长路径匹配

```perl
#当一个请求同时匹配一个长路径和一个短路径时，长路径优先级高,下面两个匹配请求localhost:8080/aaa/bbb/ccc时命中的是第二个
location /aaa{
    ......
}
location /aaa/bbb {
    ......
}
```

- 正则匹配

```perl
#在正则匹配与普通匹配（长路径匹配也属于普通匹配）均可匹配上时，正则匹配的优先级高。
#正则匹配分为区分大小（~）的和不区分大小写（~*）的

#区分大小写匹配/aaa开头
location ~/aaa {
    ......
}
#不区分大小写匹配/aaa开头
location ~*/aaa {
    ......
}

```

- 短路匹配

```perl
#以A~开头匹配路径称为短路匹配，优先级高于正则匹配
location A~/aaa {
    ......
}
```

- 精确匹配

```perl
#以=开头的匹配路径为精确匹配，它的优先级最高
location =/aaa {
    ......
}
```



## 缓存配置

​	Nginx具有很强大的缓存功能，可以对请求的response进行缓存，起到类似CDN的作用，甚至有比 CDN 更强大的功能。同时，Nginx 缓存还可以用来“数据托底”，即当后台 web 服务器挂掉的时候，Nginx 可以直接将缓存中的托底数据返回给用户。此功能就是 Nginx 实现“服务降级”的体现。
​	Nginx 缓存功能的配置由两部分构成：全局定义与局部定义。在 http{}模块的全局部分中进行缓存全局定义，在 server{}模块的各个 location{}模块中根据业务需求进行缓存局部定义。



### http{} 模块的缓存全局定义



#### proxy_cache_path

```perl
proxy_cache_path d:/cache levels=1:2 keys_zone=mycache:10m 
					max_size=5g inactive=2h use_temp_path=off;
```

用于指定nginx缓存的存放路径和相关配置

- d:cache

  nginx缓存存放路径，可以是任意路径

- levels=1:2

  在指定的缓存存放路径下采用两级目录进行管理，第一级目录使用1个字符命名，第二级使用2个字符命名

- keys_zone=mycache:10m

  在内存中指定一块区域存放缓存的key，这样nginx就可以快速判断一个请求是否命中缓存。mycache是这块内存区域的名称，可以随意命名。10m表示这块内存区域的大小。

- max_size=5g

  用于设置缓存所占硬盘空间的最大大小。若不指定，最终会将硬盘占满

- inactive=2h

  表示未被使用的缓存最长的存活时间为2小时，超过2小时会自动被删除。

- use_temp_path=off

  是否启用临时路径。为off时，nginx会将缓存文件直接写入缓存空间。否则，会先将缓存文件写入proxy_temp_path设置的临时目录中，达到某容量或某时间点（需配置）后再一次性将临时目录中的缓存写入到指定的缓存空间。

  需要注意，所有向缓存空间写入的缓存数据都需要根据其key进行运算，以确定其在缓存空间的具体存放目录。这种运算是相对较耗时的。而先放在临时目录，再批量写入缓存空间可以提高效率。

  一般情况下设为off，当需要缓存的数据量较大时才考虑启用临时路径。

#### proxy_temp_path

​	指定Nginx缓存的临时存放目录。若proxy_cache_path中的use_temp_path设置为了off，则该属性可以不指定。



### location{} 模块的缓存局部定义

#### proxy_cache mycache

​	指定用于存放缓存 key 内存区域名称。其值为 http{}模块中 proxy_cache_path 中的keys_zone 的值

### proxy_cache_key $host$request_uri

​	指定 Nginx 生成的缓存的 key 的组成。 $host  $request_uri 都是系统定义好的变量

### proxy_cache_bypass $arg_age

​	指定当前请求是否从缓存中获取数据。只要请求中包含age参数，就不走缓存

### proxy_cache_methods GET HEAD

​	指定客户端请求的哪些提交方法将被缓存，默认为 GET 与 HEAD，但不缓存 POST

### proxy_no_cache $aaa $bbb $ccc

​	指定对本次请求是否不做缓存。只要$aaa $bbb $ccc中有一个不为 0，就不对该请求结果缓存。

### proxy_cache_purge $ddd $eee $fff

​	指定是否清除缓存 key。

### proxy_cache_lock on

​	指定是否采用互斥方式回源

### proxy_cache_lock_timeout 5s

​	指定再次生成回源互斥锁的时限

### proxy_cache_valid 200 301 302 5s

​	对指定的 HTTP 状态码的响应数据进行缓存，并指定缓存时间。默认指定的状态码为200，301，302.

​	该配置表示对返回200、301、302的接口进行缓存，缓存时间为5s

### proxy_cache_use_stale error timeout http_500

​	设置启用托底缓存的条件。而一旦这里指定了相应的状态码，则前面 proxy_cache_calid中指定的相应状态码所生成的缓存就变为了“托底缓存”。

###  expires 3m

​	为请求的静态资源开启浏览器端的缓存



## nginx变量

内置变量：

```
$args 请求中的参数;
$binary_remote_addr 远程地址的二进制表示
$body_bytes_sent 已发送的消息体字节数
$content_length HTTP 请求信息里的"Content-Length"
$content_type 请求信息里的"Content-Type"
$document_root 针对当前请求的根路径设置值
$document_uri 与$uri 相同
$host 请求信息中的"Host"，如果请求中没有 Host 行，则等于设置的服务器名;
$http_cookie cookie 信息
$http_referer 来源地址
$http_user_agent 客户端代理信息
$http_via 最后一个访问服务器的 Ip 地址
$http_x_forwarded_for 相当于网络访问路径。
$limit_rate 对连接速率的限制
$remote_addr 客户端地址
$remote_port 客户端端口号
$remote_user 客户端用户名，认证用
$request 用户请求信息
$request_body 用户请求主体
$request_body_file 发往后端的本地文件名称
$request_filename 当前请求的文件路径名
$request_method 请求的方法，比如"GET"、"POST"等
$request_uri 请求的 URI，带参数
$server_addr 服务器地址，如果没有用 listen 指明服务器地址，使用这个变量将发起一次系统调用以取得地址(造成资源浪费)
$server_name 请求到达的服务器名
$server_port 请求到达的服务器端口号
$server_protocol 请求的协议版本，"HTTP/1.0"或"HTTP/1.1"
$uri 请求的 URI，可能和最初的值有不同，比如经过重定向之类的
```



自定义变量

```perl
set $aaa "hello";
set $bbb 0;
```



# nginx日志管理

## 日志管理范围

下面要讲的这些日志相关属性可以配置在任意模块。在不同的模块，记录的是不同请求的日志信息。即，日志记录的请求范围是不同的。Nginx 日志一般可以指定三个范围：http{}模块范围、server{}模块范围，与 location{}模块范围。

###  http{} 模块范围

只要有请求通过 http 协议访问该 Nginx，就会有日志信息写入到这里的日志文件。

```perl
http {
    include       mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    access_log  logs/access.log  main;
    error_log  logs/error.log;

	......
}
```
###  server{} 模块范围

只要有请求访问当前 Server，就会有日志信息写入到这里的日志文件。

```perl
server {
    listen       20001;
    server_name  localhost;

    #charset koi8-r;

    access_log  logs/host.access.log  main;
    error_log  logs/host.error.log;
	......
}
```



###  location{} 模拟范围

只要有请求访问当前 location，就会有日志信息写入到这里的日志文件。

```perl
location / {
    root html;
    index index.html;
    
    access_log  logs/location.access.log  main;
    error_log  logs/location.error.log;
}
```



## 日志管理指令

### log_format

```perl
log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
```

​	用于设置访问日志的格式，其后的 main 是为该格式所起的名称，可以任意，而其后面
的内容则为具体格式，通过 Nginx [内置变量](#nginx变量)定义。



### access_log

```perl
access_log logs/access.log main buffer=64k;
```

该指令用于设置访问日志。上面的格式包含三个参数：

- 第一个参数是日志的存放路径与日志文件名；

- 第二个参数是日志格式名；

- 第三个参数是日志文件所使用的缓存。不过，即使不指定 buffer，其也会存在默认
  日志缓存的。

- access_log 还可以跟一个参数 off，用于关闭访问日志，即直接写 access_log off 即
  可关闭访问日志。



### error_log

```perl
error_log logs/error.log debug;
```

该指令用于指定错误日志的路径与文件名。需要注意以下几点：

- 其不能指定格式，因为其有默认格式。
- 可以使用自己指定的错误日志文件，不过，将来的访问异常日志就不会再写入到默认的logs/error.log 文件中了。所以关于错误日志，一般使用默认的即可。
- 错误日志级别由低到高有：[debug | info | notice | warn | error | crit | alert | emerg]，默认为 error，级别越高记录的信息越少。
- 错误日志默认是开启的。关闭错误日志的写法为 error_log  /dev/null;



### open_log_file_cache

```perl
open_log_file_cache max=1000 inactive=10s min_uses=2 valid=60s
```

​	该指令用于打开日志文件读缓存，将日志信息读取到缓存中，以加快日志解析系统对日志的访问。该功能默认为 off，即 open_log_file_cache off;

- max 最大可以打开的日志文件个数
- inactive和min_uses要联用：在日志进入缓存inactive时间内，应至少有min_uses次被访问。否则该日志将被移除缓存。
- valid 缓存日志刷新时间



## 默认的/favicon.ico请求

​	浏览器tabl页上的图标。客户端对于服务端页面会自动提交一个/favicon.ico 请求，若没有 favicon.ico 文件则会在日志文件中报出 404。
​	从网上任意下载一个ico图标，将其重命名为favicon.ico，然后放到Linux中的任意目录。然后再修改 nginx.conf 文件，在其中添加如下的 location{}模块。注意，若将其添加到的位置与页面在同一个目录，下面的 location{}模块不用设置。

```perl
location /favicon.ico {
    root html;
}
```



## 日志自动切割

​	日志切割的方式有很多，需要时上网查即可。这里只以Linux为例简单介绍一下大概步骤。

- 创建切割 shell  脚本文件
- 为该文件添加可执行权限
- 向 crontab中添加一个定时任务



# 静态代理

​	Nginx 静态代理是指，将所有的静态资源，例如，css、js、html、jpg 等资源存放到 Nginx服务器，而不存放在应用服务器 Tomcat 中。当客户端发出的请求是对这些静态资源的请求时，Nginx 直接将这些静态资源响应给客户端，而无需提交给应用服务器处理。这样就减轻了应用服务器的压力。



## 拦截方式

```perl
#拦截以.css/.js/.html/.jpg/.png结尾的静态资源
location ~.*\.(css|js|html|jpg|png) {
    root d:static
}

#拦截css/js/images/html文件夹下的静态资源
location ~.*(css|js|images|html).+ {
    root d:static
}
```



## 页面压缩

### 浏览器常见的压缩算法

- deflate ： 是一种过时的压缩算法，是 huffman 编码的一种加强。
- gzip：是目前大多数浏览器都支持的一种压缩算法，是对 deflate 的改进。
- sdch：Shard Dictionary Compression over HTTP（HTTP共享字典压缩算法）。谷歌开发的一种压缩算法，一种全新的压缩思路。deflate 与 gzip 的的压缩思想是，修改传输数据的编码格式以达到减少体量的目的，其最终传输的数据并没有减少。而sdch 压缩算法的思想是，让冗余的数据仅出现一次，其最终传输的数据减少了。2010年。
- Zopfli：谷歌开发的一种压缩算法，Deflate 压缩算法的改进。比标准的gzip -9要小 3%-8%，但压缩用时是 gzip -9 的 80 多倍。2013年
- br：即 Brotli，谷歌开发的一种压缩算法，是一种全新的数据格式。与 Zopfli 相比，压缩率能够降低 20%-26%。Brotli -1 有着与 Gzip -9 相近的压缩比和更快的压缩解压速度。2015年



### 常用设置

```
#开启gzip压缩，默认off
gzip on;

#指定最小启用压缩的文件大小
gzip_min_length 5K;

#指定压缩级别。1-9。数字越大，压缩比越高，但压缩时间越长。默认为1
gzip_comp_level 4;

#4表示缓存颗粒数量，16K表示缓存颗粒大小
gzip_buffers 4 16K

#开启动态压缩，默认关闭
gzip_vary on;

#通过 MIME 类型来指定要压缩的文件类型。默认值 text/html。支持的类型可查看config/mime.types
gzip_types mimeType;
gzip_types text/plain application/javascript;

```



# 反向代理

通过在location{}中添加通行代理proxy_pass可以指定当前Nginx所要代理的真正服务器。

```perl
location /test/ {
    proxy_pass https://www.baidu.com/;
    
    #Nginx允许客户端请求的单文件最大大小，单位字节。
    client_max_body_size 100k;
    #Nginx 为客户端请求设置的缓存大小。
    client_body_buffer_size 80k;
    #开启从后端被代理服务器的响应内容缓冲区。默认值 on。
    proxy_buffering on;
    #该指令用于设置缓冲区的数量与大小。从被代理的后端服务器取得的响应内容，会缓存到这里。
    proxy_buffers 4 8k;
    #高负荷下缓存大小，其默认值为一般为单个 proxy_buffers 的 2 倍。
    proxy_busy_buffers_size 16k;
    #Nginx 跟后端服务器连接超时时间。默认 60 秒。
    proxy_connect_timeout 60s;
    #Nginx 发出请求后等待后端服务器响应的最长时限。默认 60 秒。
    proxy_read_timeout 60s;
}
```



# 负载均衡

## 负载均衡分类

### 软硬件分类

#### 硬件负载均衡

​	硬件负载均衡器的性能稳定，且有生产厂商作为专业的服务团队。但其成本很高，一台硬件负载均衡器的价格一般都在十几万到几十万，甚至上百万。知名的负载均衡器有 F5、Array、深信服、梭子鱼等。

#### 软件负载均衡

​	软件负载均衡成本几乎为零，基本都是开源软件。例如，LVS、HAProxy、Nginx 等。



### 负载均衡工作层分类

​	负载均衡就其所工作的 OSI 层次，在生产应用层面分为两类：七层负载均衡与四层负载均衡。当然，为四层负载均衡提供更为底层实现的，还有三层负载均衡与二层负载均衡。

- 七层负载均衡L7：应用层，基于 HTTP 协议，通过虚拟 URL 将请求分配到真实服务器。一般应用于基于HTTP协议的 B/S 架构系统。Nginx 就是七层负载均衡。
- 四层负载均衡L4：传输层，基于 TCP 协议，通过“虚拟 IP + 端口号” 将请求分配到真实服务器。一般应用于 C /S 架构系统。例如，LVS、F5、Nginx Plus 都属于四层负载均衡。
- 三层负载均衡L3：网络层，基于 IP 协议，通过虚拟 IP 将请求分配到真实服务器。部分DNS提供的是L3负载均衡
-  二层负载均衡L2：数据链路层，基于虚拟 MAC 地址将请求分配到真实服务器。



## 负载均衡的实现

```perl
upstream www.xxx.com {
    server localhost:1001 weight=1;
    server localhost:1002 weight=1;
    server localhost:1003 weight=1;
}

server {
    listen 80;
    server_name localhost;
    
    location /test/ {
        proxy_pass http://www.xxx.com;
    }
    
    ......
}
```



## Nginx 负载均衡策略

​	Nginx 内置了三种负载均衡策略，另外，其还支持第三方的负载均衡。而每种负载均衡主机根据负载均衡策略的不同，又可设置很多性能相关的属性。



### 轮询

​	默认的负载均衡策略，其是按照各个主机的权重比例依次进行请求分配的。

```perl
upstream www.xxx.com {
    server localhost:1001 weight=1 fail_timeout=20 max_fail=3;
    server localhost:1002 weight=1 fail_timeout=20 max_fail=3;
    server localhost:1003 backup;
    server localhost:1004 down;
}
```

- fail_ timeout：表示当前主机被 Nginx 认定为停机的最长失联时间，默认为 10 秒。常与max_fails 联合使用。
- max_fails：表示在 fail_timeout 时间内最多允许的失败次数。
- backup：表示当前服务器为备用服务器。
- down：表示当前服务器永久停机。



### ip_hash

​	指定负载均衡器按照基于客户端 IP 的分配方式。

```perl
upstream www.xxx.com {
	ip_hash；
    server localhost:1001 weight=1 fail_timeout=20 max_fail=3;；
    server localhost:1002 weight=1 fail_timeout=20 max_fail=3;
    server localhost:1003 down;
}
```

对于该策略需要注意以下几点：

-  在 nginx1.3.1 版本之前，该策略中不能指定 weight 属性。
- 该策略不能与 backup 同时使用。
- 此策略适合有状态服务，比如 session。
- 当有服务器宕机，必须手动指定 down 属性，否则请求仍是会落到该服务器。



### least_conn

​	把请求转发给连接数最少的服务器

```perl
upstream www.xxx.com {
	least_conn；
    server localhost:1001 weight=1 fail_timeout=20 max_fail=3;；
    server localhost:1002 weight=1 fail_timeout=20 max_fail=3;
    server localhost:1003 backup;
    server localhost:1004 down;
}
```



## Nginx Plux  的四层负载均衡实现

​	同样是修改 nginx.conf 文件，添加一个 stream 模块，其与 events、http 等模块同级。在其中配置 upstream{}与 server{}模块。此时需要注意，通行代理配置在 server{}中（前面的是配置在 server 模块的 location 模块中），且不能再是 http://开头的了，因为其负载均衡协议不再是 HTTP 协议了。

```perl
event {
    //......
}

stream{
    upstream www.xxx.com {
        server localhost:1001;
        server localhost:1002;
        server localhost:1003;
	}
	server {
        listen 8888;
        proxy_pass www.xxx.com;
	}
}

http {
    //......
}
```



# 动静分离

```perl
upstream www.xxx.com {
    server tomcatOS1:8080;
    server tomcatOS2:8080;
}

upstream static.xxx.com {
    server nginxOS1:80;
    server nginxOS2:80;
}
server {
    listen 80;
    server_name localhost;
    
    location / {
        proxy_pass http://www.xxx.com;
    }
    
    location ~.*/(css|js|images) {
        proxy_pass http://static.xxx.com;
    }
}
```



# 虚拟主机

​	虚拟主机，就是将一台物理服务器虚拟为多个服务器来使用，从而实现在一台服务器上配置多个站点，即可以在一台物理机上配置多个域名。Nginx中，一个server标签就是一台虚拟主机，配置多个server标签就虚拟除了多台主机。

​	Nginx虚拟主机的实现方式有两种：域名虚拟方式和端口虚拟方式。域名虚拟方式是指不同的虚拟机使用不同的域名，通过不同的域名虚拟出不同的主机；端口虚拟方式是指不同的虚拟机使用相同的域名不同的端口号，通过不同的端口号虚拟出不同的主机。基于端口的虚拟方式不常用。

​	现在很多生活服务类网络平台都具有这样的功能：不同城市的用户可以打开不同城市专属的站点。用户首先打开的是平台总的站点，然后允许用户切换到不同的城市。其实，不同的城市都是一个不同的站点。

​	以下我们要实现的功能是为平台总站点，北京、上海两个城市站点分别创建一个虚拟主机。每一个虚拟主机都具有两台 Tomcat 的负载均衡主机。

​	

```perl
upstream www.xxx.com {
    server tomcatOS:8080;
    server tomcatOS:8081;
}
upstream bj.xxx.com {
    server tomcatOS:8082;
    server tomcatOS:8083;
}
upstream sh.xxx.com {
    server tomcatOS:8084;
    server tomcatOS:8085;
}

server {
	listen 80;
	server_name www.xxx.com;
	
	location / {
        proxy_pass http://www.xxx.com;
	}
}

server {
	listen 80;
	server_name bj.xxx.com;
	
	location / {
        proxy_pass http://bj.xxx.com;
	}
}
server {
	listen 80;
	server_name sh.xxx.com;
	
	location / {
        proxy_pass http://sh.xxx.com;
	}
}
```





















