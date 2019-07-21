### 传输层TCP，UDP

两个主机之间相反两个方向上路径MTU可以不一致。因为因特网中的路由选择往往是不对称的。

##### UDP输出

UDP不存在真正的发送缓冲区，但是有发送缓冲区大小限制，不过它仅仅是可以写到该套接字的UDP数据报的大小上限。通常UDP数据报被复制到某种格式的一个内核缓冲区中，然后当该数据被发送之后，这个副本就被数据链路层丢弃了。

write UDP套接字返回成功表示数据报加入链路层输出队列成功。

### 套接字编程简介

##### IPV4套接字地址结构

```c
struct in_addr{
  in_addr_t s_addr;/*32-bit IPV4 address*/
}
struct sockaddr_in{
  uint8_t sin_len; /*length of structure(16)*/
  sa_family_t sin_family; 
  in_port_t sin_port;
  struct in_addr sin_addr;/*network byte ordered*/
  
  char sin_zero[8]; /*unused*/
};
```

##### 通用套接字地址结构

```c
struct sockaddr{
  uint8_t sa_len;
  sa_family_t sa_family;
  char sa_data[14];   /*protocol-specific address*/
};
```

从内核的角度看，使用指向通用套接字地址结构的指针另有原因：内核必须取调用者的指针，把它类型强制转换为struct sockaddr *类型，然后检查其中sa_family字段的值来确定这个结构的真实类型。

### 基本TCP套接字编程

客户端关闭连接从而给服务器发送一个EOF(文件结束)通知为止。

##### connect函数

```c
#include <sys/socket.h>
int connect(int sockfd,const struct sockaddr *servaddr, socklen_t addrlen);
```

如果是TCP套接字，调用connect函数讲激发TCP的三路握手过程。如果出错，出错返回的情况可能有以下几种情况。

1. 若TCP客户没有收到SYN分节的响应，则返回ETIMEOUT错误。举例来说，内核发送一个SYN，若无响应则等待6s后再发送一个，若仍无响应则等待24秒后再发送一个。若总共等了75s后仍未收到响应则返回本错误。
2. 若对客户的SYN响应是RST。返回ECONNREFUSED错误。

###### 产生RST的三个条件

1. 端口上没有正在监听的服务
2. 异常终止连接
3. TCP接收到一个根本不存在的连接上的分节

##### bind函数

如果TCP服务器没有把IP地址捆绑到它的套接字上，内核就把客户发送的SYN的目的地址作为服务器的源IP地址。

##### listen函数

```c
#include <sys/socket.h>
int listen(int sockfd,int backlog);
```

backlog参数规定了内核应该为相应套接字排队的最大连接个数。

##### accept函数

accept函数由TCP服务器调用，用于从已完成连接队列队头返回下一个已完成连接。如果已完成连接队列为空，那么进程将被投入睡眠。

```c
#include <sys/socket.h>
int accept(int sockfd,struct sockaddr* cliaddr, socklen_t *addrlen);
```

### TCP客户/服务器程序示例

##### 处理僵死进程

处理僵死进程的可移植方法就是捕获SIGCHILD，并调用wait或者waitpid。

##### 处理被中断的系统调用

当阻塞于某个慢系统调用的一个进程捕获某个信号且从相应的信号处理函数返回时，该系统调用可能返回EINTR错误。我们需要重新调用被打断的系统调用。

**有一个函数我们不能重启：connect，如果该函数返回EINTR,我们就不能再次调用它，否则将立即返回一个错误。当connect被一个信号中断且不能自动重启时，我们必须调用select来等待连接完成。**

##### accept返回前连接中止

可能会返回ECONNABORTED错误。

##### SIGPIPE信号

当一个进程向某个已经收到RST的套接字执行写操作时，内核向该进程发送一个SIGPIPE信号。该信号的默认行为是终止进程，因此进程必须捕获她以免不情愿的被终止。

如果向一个关闭写半端的套接字继续执行write,也会收到SIGPIPE信号。

##### 服务器进程终止&&主机关机

一般会发送FIN给对端。

##### 服务器主机崩溃&&网络挂掉

客户端TCP持续重传数据分节。

##### 服务器主机崩溃后重启

服务器丢失了客户端的信息，如果客户端又发送了报文服务器，服务器返回一个RST

### I/O复用：select和poll函数

#### I/O模型

1. 阻塞式I/O
2. 非阻塞式I/O
3. 复用
4. 信号驱动式I/O
5. 异步I/O

##### select

```c
#include <sys/select.h>
#include <sys/time.h>
int select(int maxfdp1, fd_set *readset,fd_set *writeset,fd_set *exceptset, const struct timeval *timeout);
```

maxfdp1参数指定待测试的描述符个数，它的值是待测试的最大描述符加1。

**内核正是通过在进程与内核之间不复制描述符中不必要的部分，从而不测试总为0的那些位来提高效率的**

##### poll

```c
#include <poll.h>
int poll(struct pollfd* fdarray, unsigned long nfds, int timeout);
```

**如果我们不再关心某个特定描述符，那么可以把与它对应的pollfd结构的fd成员设置成一个负值。poll函数将忽略这样的pollfd结构的events成员，返回时将它的revents成员的值置为0.**

##### shutdown函数

终止网络连接的通常方法是调用close函数。

1. close把描述符的引用计数减1，仅在该计数变为0时才关闭套接字。使用shutdown可以不管引用计数就激发TCP的正常连接终止序列。
2. close终止读和写两个方向的数据传送。

```c
#include <sys/socket.h>
int shutdown(int sockfd,int howto); 
```

###### SHUT_RD

 **关闭连接的读这一半-套接字中不再有数据可接收。而且套接字接受缓冲区中现有数据将被丢弃。进程不能再对这样的套接字调用任何读函数。对一个TCP套接字这样调用shutdown函数后，由该套接字接收的来自对端的如何数据都被确认，然后悄然丢弃。**

###### SHUT_WR

关闭连接的写这一半-对于TCP套接字，这称为半关闭。当前留在套接字发送缓冲区中的数据将被发送掉，后跟TCP的正常连接终止序列。这样进程就不能在再对这样的套接字调用任何写函数。

###### SHUT_RDWR

 这样的读半端和写半端都被关闭，这与调用shutdown两次等效，**第一次调用指定SHUT_RD,第二次调用指定SHUT_WR。**

### 套接字选项

##### SO_KEEPALIVE套接字选项

###### 如果2小时内在该套接字的任一方向都没有数据交换，TCP就自动给对端发送一个保持存活探测分节。这是一个对端必须响应的TCP分节。

1. 对端以期望的ACK相应。
2. 对端以RST响应，它告知本端TCP，对端崩溃已经重启。
3. 对端对保持存活探测分节没有任何响应。源自伯克利的TCP将另外发送8个探测分节，两两相隔75秒，试图得到一个响应。11分15秒内若没有得到任何响应则放弃。


##### SO_LINGER

```c
struct linger {
  int l_onoff;  /*0=off,nonzero=on*/
  int l_linger; /*linger time,POSIX specifies units as seconds*/
}
```

1. 如果l_onoff为0，那么关闭本选项。l_linger设置的值被忽略。
2. 如果l_onoff为非0值且l_linger为0，那么当close某个连接时TCP将中止该连接。这就是说TCP将丢弃保留在发送缓冲区中的任何数据，并发送一个RST给对端，而没有正常的四分组连接终止序列。**这样一来避免了TIME_WAIT状态，但是存在以下可能性，在2MSL秒内创建该连接的另一个化身。导致来自刚被终止的连接上的旧的重复分节被不正确地递送到新的化身上。**
3. 如果l_onoff为非0值且l_linger也为非0值，那么当套接字关闭时内核将拖延一段时间。这就是说如果在套接字发送缓冲区中仍残留有数据，那么进程将被投入睡眠，直到（a）所有数据都已发送完且均被对方确认或（b）延滞时间到。**如果套接字被设置为非阻塞型，那么它将不会等待close完成，即使滞留时间为非0也是如此。** 

###### 如果使用了套接字的SO_LINGER选项这个特性时，应用进程检查close的返回值是非常重要的。因为如果在数据发送完并被确认前延滞时间到的话，close将返回EWOULDBLOCK错误，且套接字发送缓冲区中的任何残留数据都被丢弃。

##### SO_RCVBUF

###### 对于客户端，SO_RCVBUF选项必须在调用connect之前设置：对于服务器这意味着该选项必须在调用listen之前给监听套接字设置。给已连接套接字设置该选项时对于可能存在的窗口规模选项没有任何影响。

##### SO_REUSEADDR和SO_REUSEPORT

补充一点，UDP支持设置SO_REUSEADDR选项时，同样的IP地址和端口可以绑定到另外一个套接字上。

### 基本UDP套接字编程

##### recvfrom和sendto函数

```c
#include <sys/socket.h>
ssize_t recvfrom(int sockfd, void *buff, size_t nbytes, int flags, struct sockaddr* from,socklen_t *addrlen);
ssize_t sendto(int sockfd, const void* buff, size_t nbytes, int flag, const struct sockaddr* to, socklen_t addrlen);
```

**写一个长度为0的数据报是可行的。在UDP情况下，这会形成一个只包含IP首部和一个8字节UDP首部而没有数据的IP数据报，这也意味着对于数据报协议，recvfrom返回0值是可接受的：它不像TCP套接字上read返回0表示对端已关闭连接。**

**如果recvfrom的from参数是一个空指针，那么相应长度参数也必须是一个空指针，表示我们并不关心数据发送者的协议地址。**

##### UDP异步错误

ICMP错误由sendto引起，但是sendto本身却成功返回。UDP输出操作成功返回仅仅表示在接口输出队列中具有存放所形成IP数据报的空间。该ICMP错误直到后来才返回，这就是称其为异步的原因，并未返回给应用层。

###### 一个基本的规则是，对于一个UDP套接字。由它引发的异步错误并不返回给它，除非它已连接。

##### UDP connect函数

###### 已连接的UDP套接字

1. 无需指定sendto的地址
2. 只能recv到来自connect的对端的用户数据报
3. 由已连接UDP套接字引发的异步错误会返回给它们所在的进程，而未连接UDP套接字不接收任何异步错误。

###### 给一个UDP套接字多次调用connect

拥有一个已连接UDP套接字的进程可出于下列两个目的之一再次调用connect

1. 指定新的IP地址和端口号
2. 断开套接字（为了断开一个已经连接的UDP套接字，地址结构地址族指定AF_UNSPEC。）

###### 性能

两次调用sendto与connect后调用两次send。第二种情况内核只复制一次含有目的IP地址和端口号的套接字地址结构，调用两次sendto,需复制2次。临时连接未连接的UDP套接字大约会消耗每个UDP传输三分之一的开销。

### Unix域套接字

Unix域套接字提供两类套接字：字节流套接字和数据报套接字。

1. Unix域套接字往往比通信两端位于同一个主机的TCP套接字快出1倍。
2. Unix域套接字可用于在同一个主机上的不同进程之间传递描述符。（通过sendmsg和recvmsg配合辅助数据）
3. Unix域套接字较新的实现把客户的用户凭证提供给服务器，从而能够提供额外的安全检查措施。

**Unix域中用于标识客户和服务器的协议地址是普通文件系统中的路径名。**

#### Unix域套接字地址结构

```c
struct sockaddr_un{
  	sa_family_t sun_family; /*AF_LOCAL*/
  	char sun_path[104]; /*null terminated pathname*/
};
```

存放在sun_path数组中的路径必须以空字符结尾。未指定地址通过以空字符串作为路径名指示，也就是一个sun_path[0]值为0的地址结构。它等价于IPV4的INADDR_ANY常值以及IPV6的IN6ADDR_ANY_INIT常值。

##### socketpair函数

socketpair函数创建两个随后连接起来套接字。本函数仅适用于Unix域套接字。

```c
#include <sys/socket.h>
int socketpair(int family, int type, int protocol, int sockfd[2]);
```

family参数必须为AF_LOCAL，protocol参数必须为0，type参数既可以是SOCK_STREAM,也可以是SOCK_DGRAM。

##### 当用于Unix域套接字，套接字函数中存在一些差异和限制

1. 与Unix域套接字关联的路径名应该是一个绝对路径名，而不是一个相对路径名。**避免使用后者的原因是它的解析依赖于调用者的当前工作目录**
2. 在connect调用中指定的路径名必须是一个当前绑定在某个打开的Unix域套接字上的路径名，而且它们的套接字类型（字节流或数据报）也必须一致。出错条件包括（a）该路径名已存在却不是一个套接字（b）该路径名已存在且是一个套接字，不过没有与之关联的打开的描述符（c）该路径名已存在且是一个打开的套接字，不过类型不符。
3. Unix域字节流套接字类似TCP套接字：它们都为进程提供一个无边界的字节流接口。
4. 如果对于某个Unix域字节流套接字的connect调用发现这个监听套接字的队列已满，调用就立即返回一个ECONNREFUSED错误。这一点不同于TCP：如果TCP监听套接字的队列已满，TCP监听端就忽略新到达的SYN,而TCP连接发起端将数次发送SYN进行重试。
5. Unix域数据报套接字类似于UDP套接字，都提供一个保留记录边界的不可靠的数据报服务。
6. 在一个未绑定的Unix域数据报(SOCK_DGRAM)套接字上发送数据报不会自动给这个套接字捆绑一个路径名，这一点不同于UDP套接字：在一个未绑定的UDP套接字上发送UDP数据报导致给这个套接字捆绑一个临时端口。**这一点意味着除非数据报发送端已经捆绑了一个路径名到它的套接字，否则数据接收端无法发回应答数据报。**


### 高级UDP套接字编程

UDP是不可靠的协议，不过有些应用程序确实有理由使用UDP而不使用TCP。我们必须包含一些特性以弥补UDP的不可靠性：超时和重传(用于处理丢失的数据报),序列号（用于匹配应答与请求）。

##### 数据报截断

当到达的一个UDP数据报超过应用进程提供的缓冲区容量时，recvmsg在其msg结构的msg_flags成员上设置MSG_TRUNC标志。

MSG_TRUNC是必须从内核返回到进程的标志之一。函数recv和recvfrom存在的一个设计问题是它们的flags参数是一个整数，因而只允许进程到内核传递标志，而不能反方向返回标志。

并非所有实现都以这种方式处理超过预期长度的UDP数据报。

1. 丢弃超出部分的字节并向应用进程返回MSG_TRUNC标志。
2. 丢弃超出部分的字节但不告诉应用进程这个事实。
3. 保留超出部分的字节并在同一个套接字上后续的读操作中返回它们。

##### 何时用UDP代替TCP

###### UDP的优势

1. UDP支持广播和多播。
2. UDP没有连接建立和拆除。

###### 什么时候选择UDP

1. 对于广播或多播应用程序必须使用UDP。
2. 对于简单的请求-应答应用程序可以使用UDP。
3. 对于海量数据传输（例如文件传输）不应该使用UDP。

##### 给UDP应用增加可靠性

1. 超时和重传：用于处理丢失的数据报
2. 序列号：供客户验证下一个应答是否匹配相应的请求

##### 并发UDP服务器

1. 一问一答的UDP服务器，简单的fork一个子进程，并让子进程去处理该应用
2. 第二种UDP服务器与客户交换多个数据报。让服务器为每一个客户创建一个新的套接字，在其上bind一个临时端口然后使用该套接字发送对该客户的所有应答。这个办法要求客户查看服务器第一个应答中的源端口号，并把本请求的后续数据报发送到该端口。

### 客户/服务器程序设计范式

##### TCP客户程序设计范式

1. 阻塞式I/O
2. 阻塞式+I/O复用
3. 非阻塞I/O
4. 多进程，用父进程处理客户到服务器的数据，子进程处理服务器到客户的数据
5. 多线程，用2个线程取代2个进程

##### TCP服务器程序设计范式

1. TCP迭代服务器程序
2. TCP并发服务器程序：每个客户一个子进程
3. TCP预先派生子进程服务器程序，accept无上锁保护（**惊群问题**）
4. TCP预先派生子进程服务器程序，accept使用文件上锁保护
5. TCP预先派生子进程服务器程序，accept使用线程上锁保护（PTHREAD_PROCESS_SHARED）(线程互斥锁上锁快于文件上锁)
6. TCP预先派生子进程服务器程序，传递描述符（UNIX域套接字（sockpair））
7. TCP并发服务器，每个客户一个线程
8. TCP预先创建线程服务器程序，每个线程各自accept+线程互斥锁
9. TCP预先创建线程服务器程序，主线程同一accept

###### select冲突

当多个进程在引用同一个套接字的描述符上调用select时就会发生冲突，因为在socket结构中为存放本套接字就绪之时应该唤醒哪些进程而分配的仅仅是一个进程ID的空间。如果有多个进程在等待同一个套接字，那么内核必须唤醒的是阻塞在select调用中的所有进程，因为它不知道哪些进程受刚变得就绪的这个套接字的影响。

##### 总结性意见

1. 当系统负载较轻时，每来一个客户请求现场派生一个子进程为之服务的传统并发服务器程序模型就足够了。
2. 相比传统的每个客户fork一次设计范式，预先创建一个子进程池或一个线程池的设计范式能够把进程控制CPU时间降低10倍或以上。
3. 某些实现允许多额子进程或者线程阻塞在同一个accept调用中，另一些实现却要求包绕accept调用时使用某种类型的锁加以保护。
4. 让所有子进程或线程自行调用accept通常比让父进程或主线程独自调用accept并把描述符传递给子进程或线程来的简单而快速。
5. 由于潜在的select冲突的原因，让所有子进程或线程阻塞在同一个aacept调用中比让它们阻塞在同一个select调用中更可取。
6. 使用线程通常远快于使用进程。





