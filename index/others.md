# 其他

## 交换机与路由器

交换机发生在数据链路层，交换机根据MAC地址寻址；而路由发生在网络层，根据IP地址寻址，路由器可以处理TCP/IP协议。

路由器的主要功能是路由功能，它的路由功能更多的体现在不同类型网络之间的互联上，如局域网与广域网之间的连接、不同协议的网络之间的连接等，所以路由器主要是用于不同类型的网络之间，解决好各种复杂路由路径网络的连接就是它的最终目的。

网络交换机，是一个扩大网络的器材，能为子网络中提供更多的连接端口，以便连接更多的计算机。

## 零拷贝

mmap、sendfile、splice等

<https://www.jianshu.com/p/fad3339e3448>

## 互斥锁实现读写锁

<https://blog.csdn.net/tt_love9527/article/details/82107549>

<https://blog.csdn.net/TT_love9527/article/details/81987215>

## vector 插入时间复杂度

<https://blog.csdn.net/qq_40416052/article/details/81906286>

0(1)

为避免复杂度震荡，可以扩容时两倍，而当size=capacity/4时再进行缩容一半

## 内存栅栏

简单来说内存屏障（Memory Barrier，或内存栅栏，Memory Fence）就是从本地或工作内存（CPU寄存器、高速缓存-cache）到主存之间的拷贝动作。

在多线程并发过程中，仅当写操作线程先跨越内存栅栏而读线程后跨越内存栅栏的情况下，写操作线程所做的变更才对其他线程可见。

volatile不具备“互斥性”

volatile不能保证变量的“原子性”

## 哈希表扩张、插入、删除，冲突解决

冲突解决：

1、开放地址法：线性探测法；二次探测法；双哈希函数探测法；

2、链地址法

## gdb调试命令

``` sh
r=run
l=list 显示当前代码10行
b=breakpoint 行号 打断点
n=next 步进？
s=step 步入？
c=continue 继续执行至下一个断点
finish
q=quit 退出
whatis 变量名 查看变量类型
print 变量名 查看变量值
bt 查看函数堆栈
info breakpoints 查看所有断点信息
disable/enable 断点序号 禁用/使能某断点
clear 断点序号/函数名 清除断点
delete 可多个断点序号（逗号分开）清除断点（无参则默认删除所有断点 ）
```

## 定位内存泄漏

一、重载new/delete操作符

重载new/delete操作符，用list或者map记录对内存的使用情况。new一次，保存一个节点，delete一次，就删除节点。最后检测容器里是否还有节点，如果有节点就是有泄漏。也可以记录下哪一行代码分配的内存被泄漏。类似的方法：在每次调用new时加个打印，每次调用delete时也加个打印。

二、用mtrace定位内存泄漏

三、查看进程maps表

在实际调试过程中，怀疑某处发生了内存泄漏，可以查看该进程的maps表，看进程的堆或mmap段的虚拟地址空间是否持续增加。如果是，说明可能发生了内存泄漏。如果mmap段虚拟地址空间持续增加，还可以看到各个段的虚拟地址空间的大小，从而可以确定是申请了多大的内存。

四、valgrind工具

五、在VS中加入如下代码：

``` cpp
    #include <stdlib.h>  
    #include <crtdbg.h>  
    #ifdef _DEBUG  
    #define new new(_NORMAL_BLOCK, __FILE__, __LINE__)  
    #endif  

    void EnableMemLeakCheck()  
    {  
        int tmpFlag = _CrtSetDbgFlag(_CRTDBG_REPORT_FLAG);  
        tmpFlag |= _CRTDBG_LEAK_CHECK_DF;  
        _CrtSetDbgFlag(tmpFlag);  
    }  

    using namespace std;  
    int main()  
    {  
        EnableMemLeakCheck();  
        //_CrtSetBreakAlloc(在debug模式下第一遍注释执行获取内存泄漏的行号, 在第二遍执行时填入);  
        //自己的代码  
    }  
```

## 静态链接与动态链接区别

1、静态链接库被包含在宿主程序中，而动态链接库则是在需要时动态地装载和卸载DLL文件；

2、静态链接库中不能再包含其它动态或者静态链接库，而动态链接库中则可再包含。

动态（共享）链接库：

优点：

1.可执行文件要小得多，因为它只含有你通过object文件显式链接的代码。 外部库是引用，它们的代码不会进入二进制文件中。

2.多个可执行文件可以共享(名称就是这么来的)一个库。

3.你可以， 如果你对二进制兼容比较小心的话，可以在程序运行之间更新库中的代码，程序将在不需要更改的情况下选择新库。

缺点：

1.将程序链接在一起需要时间。 对于共享库，有时这些时间会延迟到每次运行可执行文件时。

With shared libraries some of this time is deferred to every time the executable runs.

2.这个过程比较复杂。 共享库中的所有其他符号都是在运行时使库链接所需的基础结构的一部分。

3.你可能会遇到库的不同版本之间存在细微不兼容的风险。 在Windows上，这称为“DLL地狱”。
