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



