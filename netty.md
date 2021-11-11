### 异步 同步 阻塞 非阻塞
（https://segmentfault.com/a/1190000022478666；https://www.zhihu.com/question/19732473）

### 阻塞和非阻塞：
阻塞和非阻塞关注的是当前程序在等待调用结果时的状态；
关于系统调用的场景： 阻塞和非阻塞的区别在于，阻塞会一直等待，知道系统调用完成，而非阻塞，会直接返回一个失败，EWouldBlock。
区别点在于如果为完成，是直接返回错误码，还是让用户进程一直处于waiting状态；

### 异步与同步：
异步和同步指的是信息通信机制，同步是没有得到结果前一致等待，异步会直接返回，然后通过回调或者异步通知的形式通知发起方处理。

linux中的aio就是，从内核到用户进程的读写由操作系统完成，完成后再异步通知用户进程处理该事件回调。

### Reactor 和Preactor
![image](https://user-images.githubusercontent.com/31442134/141248214-5fcd228f-3a09-46b6-9ebe-f845ff40df28.png)

Reactor和Preactor是两种事件处理模型，Reactor是主线程（I/O处理单元）监听文件描述符，然后通过工作线程（逻辑单元）处理请求。Reactor存在单Reactor 单进程/线程， 单Reactor 多进程/线程，多Reactor 多进程/线程。这三种情况（https://www.zhihu.com/question/26943938），其中redis是单Reactor 单进程/线程，由于redis运行在内存中，其瓶颈点往往不是cpu计算的运行速度，而且多个工作线程之间的同步加锁往往会比较复杂。多Reactor 多进程/线程，会存在一个主Reactor仅处理各个请求的accept，后续二级Reactor处理socket的读写分发（nginx属于该工作方式）；

以epoll为例，工作流程如下；
	1. 主线程往epoll中注册socket的读就绪事件，随后主线程调用epoll_wait等待socket中等待数据；
	2. 当内核存在可读的fd时，epoll_wait通知主线程，主线程将socket的可读事件放入请求队列；
	3. 请求队列中的工作线程被唤醒，并将数据从内核读到用户进程，并处理客户请求，若存在输出的话，往epoll内核中注册该socket的写就绪事件；
	4. 主线程调用epoll_wait等待socket可写事件；
	5. epoll_wait通知主线程的可写事件；
	6. 同理，主线程唤醒一个工作线程，将数据写入内核。

Preactor和Reactor的主要区别点在于，文件的读写操作不是工作线程执行的，
而是操作系统内核或者主线程执行的，执行完文件读取或者写入之后，再通知工作线程。
因此注册的不是读就绪或者写就绪事件，而是读完成或写完成事件；
这种属于属于异步通知操作，关于同步，异步，阻塞，非阻塞，见上。
![image](https://user-images.githubusercontent.com/31442134/141248204-3f215c0c-c2b7-4042-9863-8fcd70dcede4.png)


