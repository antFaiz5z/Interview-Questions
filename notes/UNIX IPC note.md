### Ubuntu 64位 IPC内核参数

```shell
>: ipcs -l
```

------ Shared Memory Limits --------
max number of segments = 4096
max seg size (kbytes) = 18014398509465599
max total shared memory (kbytes) = 18446744073642442748
min seg size (bytes) = 1

------ Semaphore Limits --------
max number of arrays = 32000
max semaphores per array = 32000
max semaphores system wide = 1024000000
max ops per semop call = 500
semaphore max value = 32767

------ Messages Limits --------
max queues system wide = 32000
max size of message (bytes) = 8192
default max size of queue (bytes) = 16384

##### System V IPC

以下三种类型的IPC合称为System V IPC

1. System V消息队列
2. System V信号量
3. System V共享内存区

###### key_t键和ftok函数

三种类型的System V IPC使用key_t值作为它们的名字。 key_t通常是一个至少32位的整数。这些整数值通常是由ftok函数赋予的。

```c
#include <sys/ipc.h>
int ftok(const char* pathname, int id);
																//若成功则为IPC键，若出错则为-1
```

该函数把从pathname导出的信息与id的低序8位组合成一个整数IPC键。

pathname可以是服务器守护程序的程序名，服务器使用的某个公共数据文件的路径名或者系统上的某个其他的路径。如果pathname不存在，或者对于调用进程不可访问，ftok就返回-1。

ftok的典型实现调用stat函数，然后组合以下三个值

1. pathname所在的文件系统的信息（stat结构的st_dev成员）
2. 该文件在本文件系统内的索引节点号(stat结构的st_ino成员)
3. id的低序8位(不能为0)

**不能保证两个不同的路径名与同一个id的组合产生不同的键，因为上面所列三个条目中的信息位数可能大于一个整数的信息位数。**

**索引结点绝不会是0，因为大多数实现把IPC_PRIVATE定义为0**

### 管道和FIFO

#### 管道

由pipe函数创建，提供一个单路(单向数据流)。

全双工管道可能的实现隐含的意思是：整个管道只存在一个缓冲区，(在任意一个描述符上)，写入管道的任何数据都添加到该缓冲区的末尾，(在任意一个缓冲区上从管道读出的都是取自该缓冲区开头的数据)

这种实现存在的问题在于，当一个进程往该全双工管道写入数据，过后再对该管道调用read时，有可能读回刚写入的数据。

#### FIFO

又叫做有名管道，由mkfifo函数创建，是一个单向数据流。

```c++
#include <sys/types.h>
#include <sys/stat.h>
int mkfifo(const char* pathname, mode_t mode);
```

mode参数指定文件权限位，类似于open的第二个参数。mkfifo函数已隐含指定O_CREAT|O_EXCL。也就是说，它要么创建一个新的FIFO，要么返回一个EEIXST错误。

**要打开一个已存在的FIFO或创建一个新的FIFO，应先调用mkfifo,再检查它是否返回EEXIST错误，若返回该错误则改为调用open。**

在创建出一个FIFO后，它必须或者打开来读，或者打开来写，所用的可以是open函数，也可以是某个标准I/O打开函数，如fopen。

**如果对管道或者FIFO调用lseek,那就返回ESPIPE错误**

没有正确使用FIFO的程序会发生微妙的问题。如果我们对换父进程中两个open调用的顺序，该程序就不工作。其原因在于，**如果当前尚没有任何进程打开某个FIFO来写，那么打开该FIFO来读的进程将阻塞。**顺序对调后将会发生死锁。

```c++
//子进程
int readfd=open(FIFO1,O_RDONLY);
int writefd=open(FIFO2,O_WRONLY);

//父进程
int writefd=open(FIFO1,O_WRONLY);
int readfd=open(FIFO2,O_RDONLY);
```

#### 管道和FIFO对比

1. 创建并打开一个管道只需要调用pipe。创建并打开一个FIFO则需在调用mkfifo后再调用open。
2. 管道在所有进程最终都关闭它之后自动消失。FIFO的名字则只有通过调用unlink才从文件系统中删除。

#### 管道的大小限制

Since  Linux  2.6.11, 64KB。

       the pipe capacity is 65536 bytes. 
### Posix消息队列

消息队列可认为是一个消息链表。每个消息都是一个记录，它由发送者赋予一个优先级。在某个进程往一个队列写入消息之前，并不需要另外某个进程在该队列上等待消息的到达，这跟管道和FIFO是相反的。

一个进程可以往某个队列写入一些消息，然后终止，再让另外一个进程在以后的某个时刻再读出这些消息。我们说过消息队列具有随内核的持续性，这跟管道和FIFO不一样。当一个管道或FIFO的最后一次关闭发生时，仍在该管道或FIFO上的数据将被丢弃。

###### POSIX消息队列与SYSTEM V消息队列的区别

1. 对POSIX消息队列的读总是总是返回最高优先级的最早消息，对SYSTEM V消息队列的读则可以返回任意指定优先级的消息。
2. 当往一个空队列放置一个消息时，POSIX消息队列允许产生一个信号或启动一个线程，System V消息队列则不提供类似机制。

###### 队列中的每个消息具有如下属性

1. 一个无符号整数优先级或一个长整数类型
2. 消息的数据部分长度
3. 数据本身

##### mq_open,mq_close和mq_unlink

###### mq_open

mq_open函数创建一个新的消息队列或打开一个已存在的消息队列。

```c
#include <mqueue.h>
mqd_t mq_open(const char *name,int oflag,.../*mode_t mode,struct mq_attr *attr */);
															//成功，返回消息队列描述符，失败，-1
```

当实际操作是创建一个新队列时，mode和attr参数是需要的。attr参数用于给新队列指定某些属性。如果它为空指针，那就使用默认属性。

###### mq_close

已打开的消息队列是由mq_close关闭的。

```c
#include <mqueue.h>
int mq_close(mqd_t mqdes);
```

###### mq_unlink

要从系统中删除用作mq_open第一个参数的某个name,必须调用mq_unlink。

```c
#include <mqueue.h>
int mq_unlink(const char* name);
```

##### mq_getattr,mq_setattr

```c
#include <mqueue.h>
int mq_getattr(mqd_t mqdes,struct mq_attr *attr);
int mq_setattr(mqd_t mqdes,const struct mq_attr *attr, struct mq_attr *oattr);

struct mq_attr{
  	long mq_flags;  /*message queue flag: 0, O_NONBLOCK*/
  	long mq_maxmsg; /*max number of messages allowed on queue*/
  	long mq_msgsize; /*max size of a message(in bytes)*/
  	long mq_curmsgs; /*number of messages currrently on queue*/
};
```

mq_open时可以指定mq_maxmsg和mq_msgsize属性。mq_open忽略该结构的另外两个成员。

mq_setattr给指定队列设置属性，但是只使用由attr指向的mq_attr结构的mq_flag成员，以设置或清楚非阻塞标志。

另外，如果oattr指针非空，那么所指定队列的先前属性（mq_flags,mq_maxmsg和mq_msgsize）和当前状态（mq_curmsgs）将返回到由该指针指向的结构中。

##### mq_send和mq_receive

mq_receive总是返回由指定队列中最高优先级的最早消息，而且该优先级能随该消息的内容及其长度一同返回。

```c
#include <mqueue.h>
int mq_send(mqd_t mqdes,const char* ptr,size_t len,unsigned int prior);
ssize_t mq_receive(mqd_t mqdes,char *ptr,size_t len,unsigned int *prior);
```

mq_recieve的len参数的值不能小于能加到所指定队列中的消息的最大大小（该队列mq_attr结构的mq_msgsize成员）。要是len小于该值，mq_receive就立即返回EMSGSIZE错误。

**这意味着使用Posix消息队列的大多数应用程序必须在打开某个队列后调用mq_getattr确定最大消息大小，然后分配一个或多个那样大小的读缓冲区**

### System V消息队列

...

### 互斥锁和条件变量

如果互斥锁变量是静态分配的，那么我们可以把它初始化成常值PTHREAD_MUTEX_INITIALIZER,例如：

static pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;

如果互斥锁是动态分配的或者分配在共享内存区中，那么我们必须在运行之前通过调用pthread_mutex_init函数来初始化。

#### pthread_mutex

```c
#include <pthread.h>
int pthread_mutex_lock(pthread_mutex_t *mptr);
int pthread_mutex_trylock(pthread_mutex_t *mptr);
int pthread_mutex_unlock(pthread_mutex_t *mptr);
														//均返回：若成功则为0，若出错则为正的Exxx值
```

**pthread_mutex_trylock是对应的非阻塞函数，如果该互斥锁已锁住，它就返回一个EBUSY错误。**

#### 持有锁期间进程终止

当在进程间共享一个互斥锁时，持有该互斥锁的进程在持有期间终止（也许是非自愿地）的可能总是有的。没有办法让系统进程终止时自动释放所持有的锁。读写锁和Posix信号量也具备这种属性。进程终止时内核总是自动清理的唯一同步锁类型是fcntl记录锁。

#### 静态初始化和动态初始化

##### 互斥锁和条件变量可以静态分配并初始化。他们也可以动态分配，动态初始化允许我们指定进程间共享属性，从而允许在不同的进程间共享某个互斥锁或条件变量，其前提是该互斥锁或条件变量必须存放在由这些进程共享的内存中。

### 读写锁

#### 读写锁的分配策略

1. 只要没有线程持有某个给定的读写锁用于写，那么任意数目的线程可以持有该读写锁用于读。
2. 仅当没有线程持有某个给定的读写锁用于读或者用于写时，才能分配该读写锁用于写。

##### 某些应用中读数据比修改数据频繁，这些应用可从改用读写锁代替互斥锁中获益。

读写锁用于读称为共享锁，获取一个读写锁用于写称为独占锁。

##### pthread_rwlock_rdlock,pthread_rwlock_wrlock,pthread_rwlock_unlock

读写锁的数据类型是pthread_rwlock_t,可以静态初始化和动态初始化，动态初始化可以设置进程间共享属性（PTHREAD_PROCESS_SHARED）

```c
#include <pthread.h>
int pthread_rwlock_rdlock(pthread_rwlock_t *rwptr);
int pthread_rwlock_wrlock(pthread_rwlock_t *rwptr);
int pthread_rwlock_unlock(pthread_rwlock_t *rwptr);
														//success:return 0, fail: return Exxx值 
```

##### pthread_rwlock_tryrdlock,pthread_rwlock_trywrlock

```c
#include <pthread.h>
int pthread_rwlock_tryrdlock(pthread_rwlock_t *rwptr);
int pthread_rwlock_trywrlock(pthread_rwlock_t *rwptr);
														//success:return 0, fail: return EBUSY 
```

pthread_rwlock_init,pthread_rwlock_destroy

```c
#include <pthread.h>
int pthread_rwlock_init(pthread_rwlock_t *rwptr);
int pthread_rwlock_destroy(pthread_rwlock_t *rwptr);
														//success:return 0, fail: return Exxx值
```

#### 使用互斥锁和条件变量实现读写锁

...

#### 线程取消

如果pthread_rwlock_rdlock的调用线程阻塞在其中的pthread_cond_wait调用上并随后被取消，它就在仍持有互斥锁的情况下终止。通过由对方调用pthread_cancel,一个线程可以被同一进程内的任何其他线程所取消，pthread_cancel的唯一参数就是待取消线程的线程ID。

###### pthread_cancel

```c
#include <pthread.h>
int pthread_cancel(pthread_t tid);
														//success:return 0, fail: return Exxx值
```

未处理被取消的情况，任何线程都可以压入和弹出清理处理程序。

###### pthread_cleanup_push,pthread_cleanup_pop

```c
void pthread_cleanup_push(void (*function)(void*)，void *arg);
void pthread_cleanup_pop(int execute);
```

这些处理程序就是发生以下情况时被调用的函数

1. 调用线程被取消（某个线程调用pthread_cancel）
2. 调用线程自愿终止（调用pthread_exit，或者从自己的线程起始函数返回）

**清理处理程序可以恢复任何需要恢复的状态，例如给调用线程当前持有的任何互斥锁或信号量解锁**

pthread_cleanup_pop()总是弹出调用线程的取消清理栈中位于栈顶的函数，而且如果execute不为0，那就调用该函数。

### 记录上锁

**fork后的子进程不继承fcntl记录锁。且进程异常终止时，内核自动清理的唯一同步锁为记录锁。fork+exec后只要描述符继续打开着，记录锁则不变。**

#### 劝告性上锁

POSIX记录上锁称为劝告性上锁，含义是内核维护着已由各个进程上锁的所有文件的正确信息，但是它不能防止一个进程写已由另一个进程读锁定的某个文件。类似地它也不能防止一个进程读已由另外一个进程写锁定的某个文件。

#### 强制性上锁

有些系统提供另一种类型的记录上锁，称为强制性上锁。使用强制性上锁后，内核检查每个read和write的请求，以验证其操作不会干扰由某个进程持有的某个锁。

#### 记录上锁的一个常见用途

记录上锁的一个常见用途是确保某个程序（例如守护程序）在任何时刻只有一个副本在运行。

##### 打开一个文件并为其上锁

守护进程维护一个只有一行文本的文件，其中含有它的进程ID，他打开这个文件，必要的话创建之，然后请求整个文件的一个写入锁。如果没有取得该锁，我们就知道该程序有另一个副本在运行，于是输出一个出错消息并终止。

### Posix信号量

信号量是一种用于提供不同进程间或一个给定进程的不同线程间同步手段的原语。

1. Posix有名信号量：使用Posix IPC名字标识，可用于进程或线程间的同步。
2. Posix基于内存的信号量：存放在共享内存区中，可用于进程和线程间的同步。
3. System V信号量：在内核中维护，可用于进程或线程间的同步。

#### 二值信号量和计数信号量

###### 信号量还有一个互斥锁没有提供的特性，互斥锁必须总是由锁住它的线程解锁，信号量的挂出却不必由执行过它的等待操作的同一线程执行。

#### 信号量，互斥锁和条件变量之间的差异

1. 互斥锁必须总是由给它上锁的线程解锁，信号量的挂出却不必由执行过它的等待操作的同一线程执行。
2. 互斥锁要么被锁住，要么被解开。（二值状态）
3. 既然信号量有一个与之关联的状态（它的计数值），那么信号量挂出操作总是被记住。然而当向一个条件变量发送信号时，如果没有线程等待在该条件变量上，那么该信号将丢失。

#### 有名信号量

##### sem_open,sem_close,sem_unlink

sem_ulink类似于文件I/O的unlink函数，当引用计数还是大于0时，name就能从文件系统中删除，然而其信号量的析构（不同于将它的名字从文件系统中删除）却要等到最后一个sem_close发生时为止。

#### 基于内存的信号量

##### sem_init,sem_destroy

```c
#include <semaphore.h>
int sem_init(sem_t *sem,int shared,  unsigned int value);
int sem_destroy(sem_t *sem);
```

如果shared为0，那么待初始化的信号量是在同一个进程的各个线程间共享的，否则该信号量实在进程间共享的。当shared为非0时，该信号量必须存放在某种类型的共享内存区中，而即将使用它的所有进程都要能访问该共享内存区。

###### 注意，基于内存的信号量不使用类似于O_CREAT标志的东西，也就是说，sem_init总是初始化信号量的值，因为，对于一个给定的信号量，我们必须小心保证只调用sem_init一次。对于一个已初始化的信号量调用sem_init，其结果是未定义的。

### 共享内存区

共享内存是可用IPC形式中最快的。一旦这样的内存区映射到共享它的进程的地址空间，这些进程间数据的传递就不再涉及内核。

###### 附接和断接

**这里说的“不在涉及内核”的含义是：进程不再通过执行任何进入内核的系统调用来彼此传递数据。显然，内核必须简历允许各个进程共享该内存区的内存映射关系，然后一直管理该内存区（处理页故障等）。**

#### 内存映射

##### mmap,munmap和msync函数

###### mmap

mmap函数把一个文件或一个Posix共享内存区对象映射到调用进程的地址空间。使用该函数有三个目的：

1. 使用普通文件以提供内存映射I/O;
2. 使用特殊文件以提供匿名内存映射;
3. 使用shm_open以提供无亲缘关系进程间的Posix共享内存区

```c
#incldue <sys/mman.h>
void *mmap(void *addr,size_t len,int prot,int flags,int fd, off_t offset)
```

prot:

| prot       | 说明     |
| ---------- | ------ |
| PROT_READ  | 数据可读   |
| PROT_WRITE | 数据可写   |
| PROT_EXEC  | 数据可执行  |
| PROT_NONE  | 数据不可访问 |

flags: MAP_SHARED或MAP_PRIVATE这两个标志必须指定一个，如果指定了MAP_PRIVATE,那么调用进对被映射数据所作的修改只对该进程可见，而不改变其底层支撑对象（或者是一个文件对象，或者是一个共享内存区对象）。如果指定了MAP_SHARED,那么调用进程对被映射对象所作的修改对于共享该对象的所有进程可见，而且确实改变了其底层支撑对象。

**mmap成功返回后，fd参数可以关闭。该操作对于由mmap建立的映射关系没有影响**

###### munmap

为从某个进程的地址空间删除一个映射关系，我们调用munmap。

```c
#include <sys/mman.h>
int munmap(void* addr, size_t n);
```

munmap成功返回后，再次访问这些地址将导致向调用进程产生一个SIGSEGV信号。

内核的虚拟内存算法保持内存映射文件与内存映射区的同步，前提它是一个MAP_SHARED内存区。这就是说，如果我们修改了处于内存映射到某个文件的内存区中某个位置的内容，那么内核将在稍后某个时刻相应地更新文件。

###### msync

**有时候我们希望确信硬盘上的文件内容与内存映射区中的内容一致，于是调用mysnc来执行这种同步**

```c
#include<sys/mman.h>
int msync(void* addr,size_t len,int flags);
```

| 常值            | 说明         |
| ------------- | ---------- |
| MS_ASYNC      | 执行异步写      |
| MS_SYNC       | 执行同步写      |
| MS_INVALIDATE | 使高速缓存的数据失效 |

MS_ASYNC和MS_SYNC这两个常值中必须指定一个但不能都指定。他们的差别是，一旦写操作已由内核排入队列，MS_ASYNC即返回，而MS_SYNC则要等到写操作完成后才返回。指定了MS_INVALIDATE,那么与其最终副本不一致的文件数据的所有内存中的副本都失效。后续的引用将从文件中取得数据。

不是所有文件都能进行内存映射。试图把一个访问终端或套接字的描述符映射到内存将导致mmap返回一个错误。这些类型的描述符必须使用read和write（或者他们的变体）来访问。

**内核的内存保护是以页面为单位的，内核允许我们读最后一页中映射区以远部分，内核跟踪着被内存映射的底层支撑对象的大小，而且我们总是能访问在当前文件大小以内又在内存映射区以内的那些字节。**

#### POSIX 共享内存区

##### shm_open，shm_unlink函数

###### shm_open

```c
#include <sys/mman.h>
int shm_open(const char* name, int oflag, mode_t mode);
														//返回，若成功则为非负描述符，若出错则为-1
```

oflag参数必须或者含有O_RDONLY或者O_RDWR标志，还可以指定如下标志：O_CREAT,O_EXCL或O_TRUNC。如果随O_RDWR指定O_TRUNC标志，而且所需的共享内存对象已经存在，那么它将被截断成0长度。

###### shm_unlink

```c
#include <sys/mman.h>
int shm_unlink(const char* name);
															 //若成功则为0，若出错则为-1
```

shm_unlink函数删除一个共享内存区对象的名字。删除一个名字不会影响对于其底层支撑对象的现有引用，直到对于该对象全部关闭为止。

###### ftrunctate

处理mmap的时候，普通文件或共享内存区对象的大小都可以通过调用ftruncate修改。

```c
#include <unistd.h>
int ftruncate(int fd, off_t length);
                                                                    //若成功则为0，若出错则为-1
```

1. 对于一个普通文件：如果该文件的大小大于length参数，额外的数据就被丢弃掉。如果该文件的大小小于length,那么该文件是否修改以及大小是否增长是未加说明的。事实上，把文件的大小扩展为length字节的可移植方法是使用lseek+write。
2. 对于一个共享内存区对象，ftruncate把该对象的大小设置成length字节。

###### fstate

```c
#include <sys/types.h>
#include <sys/stat.h>
int fstat(int fd,struct stat* buf);

struct stat {
  //...
  mode_t st_mode; /*mode: S_I{RW}{USR,GRP,OTH}*/
  uid_t st_uid;   /*user ID of owner*/
  gid_t st_gid;   /*group ID of owner*/
  off_t st_size;  /*size in bytes*/
};
```

可以通过fstat获取共享内存的大小 st_size.

#### System V共享内存区

##### shmget,shmat,shmdt,shmctl函数

###### shmget

shmget函数创建一个新的共享内存区，或者访问一个已存在的共享内存区。

```c
#include <sys/shm.h>
int shmget(key_t key,size_t size, int oflag);
```

返回值是一个称为共享内存区标识符的整数，其他三个shmXXX函数就是用它来指代找个内存区。

key既可以是ftok的返回值，也可以是IPC_PRIVATE.

size：当实际操作为创建一个新的共享内存区时，必须指定一个不为0的size值。如果实际操作作为访问一个已存在的共享内存区，那么size应为0.

oflag是读写权限值的组合，它还可以与IPC_CREAT或IPC_CREAT|IPC_EXEL按位或。(IPC_W,IPC_R)

**当实际操作为创建一个新的共享内存区时，该内存区被初始化为size字节的0**

###### shmat

由shmget创建或打开一个共享内存区后，通过调用shmat把它附接到调用进程的地址空间。

```c
#include <sys/shm.h>
void *shmat(int shmid,const void *shmaddr,int flag);
													//返回：若成功则为映射区的起始地址，若出错则为-1
```

1. 如果shmaddr是一个空指针，那么系统替调用者选择地址。
2. 如果shmaddr是一个非空指针，那么返回地址取决于调用者是否给flag参数指定了SHM_RND值。
   1. 如果指定了SHM_RND，那么相应的共享内存区附接到由shmaddr参数指定的地址向下舍入一个SHMLBA常值。LBA代表“低端边界地址”
   2. 如果没有指定SHM_RND，那么相应的共享内存区附接到由shmaddr参数指定的地址。

默认情况下，只要调用进程具有某个共享内存区的读写权限，它附接该内存区后就能够同时读写该内存区。flag参数中也可以指定SHM_RDONLY值，它限定只读访问。

###### shmdt

当一个进程完成某个共享内存区的使用时，它可调用shmdt断接这个内存区。

```c
#include <sys/shm.h>
int shmdt(const void* shmaddr);
																		//success:0,fail:-1
```

当一个进程中止时，它当前附接着的所有共享内存区都自动断接掉。

注意本函数调用并不删除所指定的共享内存区。这个删除工作通过以IPC_RMID调用shmctl完成。

###### shmctl

```c
#include <sys/shm.h>
int shmctl(int shmid, int cmd, strucy shmid_ds * buff);
																	    //success:0,fail:-1
```

该函数提供了三个命令。

1. IPC_RMID  从系统中删除由shmid标识的共享内存区并拆除掉。
2. IPC_SET    给所指定的共享内存区设置其shmid_ds结构的几个成员，shm_perm.uid,shm_perm.gid，shm_perm.mode.
3. IPC_STAT   (通过buff参数)的调用者返回所指定共享内存区当前的shmid_ds结构

**删除一个共享内存区指的是使其标识符失效，这样以后针对该标识符的shmdt,shmat,shmclt函数调用必定失败，拆除一个共享内存区指的是释放或回收与它对应的数据结构，包括删除存放在其上的数据。拆除操作要到该共享内存区的引用计数变为0时才进行。另外当某个shmdt调用发现所指定的共享内存区的引用计数变为0时也顺便拆除它，这就是shmctl的IPC_RMID命令先于最后一个shmdt调用发出时会发生的情形。**

**打开并附接所指定的共享内存区。其大小通过以一个IPC_STAT命令调用shmclt获取。通过buff.shm_segsz**

##### 共享内存的限制

| 名字     | 说明              | DUNIX 4.0B | SOLARIS 2.6 | Ubuntu 14.04 64位 |
| ------ | --------------- | ---------- | ----------- | ---------------- |
| shmmax | 一个共享内存区的最大字节数   | 4194304    | 1048576     | 非常大，实际应该跟硬件有关    |
| shmmnb | 一个共享内存区的最小字节数   | 1          | 1           |                  |
| shmmni | 系统范围最大共享内存区标识符数 | 128        | 100         | 4096             |
| shmseg | 每个进程附接的最大共享内存区数 | 32         | 6           |                  |

##### POSIX共享内存区对象的大小可在任何时刻通过调用ftruncate修改，而System V共享内存区对象的大小是在调用shmget创建时固定下来的。

