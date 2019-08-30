# C++ 基础

<https://antfaiz5z.github.io/2019/04/26/effective-cpp/>

RAII（资源获取即初始化）

RTTI （runtime type information）

RTTI（Run-Time Type Identification，运行时类型识别）？

## 内存布局

高栈映堆全代

## 内存对齐

## 指针与引用

``` c
const int* pt;  //常指针
int* const ps;  //指针常量
const int& pt;  //常引用
```

1、指针保存的是所指对象的地址，引用是所指对象的别名，指针需要通过解引用间接访问，而引用是直接访问；

2、指针可以改变地址，从而改变所指的对象，而引用必须从一而终；

3、引用在定义的时候必须初始化，而指针则不需要；

4、指针有指向常量的指针和指针常量，而引用没有引用常量；

## 静态成员变量、静态成员函数

类的静态成员变量是直接与类联系，属于类的成员而不是对象，供所有对象共享，存放于全局区，因而不计入类的内存计算。所以它不能由类的构造函数初始化，一般也不能在类内初始化。关键字static只出现类的内部。

一般来说无论怎样静态成员变量都需要在类外进行定义（定义可以初始化赋值，如果不显式初始化就是默认初始化）。

为什么static成员一定要在类外初始化?
    这是因为被static声明的类静态数据成员，其实体远在main()函数开始之前就已经在全局数据段中诞生了，其生命期和类对象是异步的。

（1）静态成员变量的初始化

1、在类外定义且初始化；

2、常量静态成员可以在类内初始化；

（2）静态成员变量的访问

1、使用类作用域运算符直接访问；

2、使用类的对象访问；

3、成员函数可以直接访问；

（3）静态成员函数

1、静态成员函数类似于静态成员变量都属于类而不是对象；

2、静态成员函数仅可以调用类的静态成员变量以及静态成员函数；

3、不具有this指针，因而自然不能声明为const；

## 多态、虚函数、虚表、虚表指针

静态多态通过模板与重载实现，编译时确定；动态多态通过虚函数实现，运行时确定。

虚函数通过继承方式来体现出多态作用，它必须是基类的非静态成员函数，其访问权限可以是protected或public；

虚表内的条目，即虚函数指针的赋值发生在编译器的编译阶段，也就是说在代码的编译阶段，虚表就可以构造出来了。

编译器为每一个类维护一个虚函数表，每个对象的首地址保存着该虚函数表的指针，同一个类的不同对象实际上指向同一张虚函数表。

虚函数表直接从基类继承到子类，如果覆盖了其中的某个虚函数，那么虚函数表的指针就会被替换，因此可以根据指针准确找到该调用哪个函数。

## 什么时候会执行函数的动态绑定？

这需要同时符合以下三个条件

1、通过指针来调用函数；

2、指针upcast向上转型；

3、调用的是虚函数；

## 为什么对于存在虚函数的类中析构函数要定义成虚函数？

为了实现多态进行动态绑定，将派生类对象指针绑定到基类指针上，对象销毁时，如果析构函数没有定义为虚函数，则只会调用基类的析构函数，显然只能销毁部分数据。如果要调用对象的析构函数，就需要将该对象的析构函数定义为虚函数，销毁时通过虚函数表找到对应的析构函数。

## 为什么构造函数不能是虚函数？

A virtual call is a mechanism to get work done given partial information. In particular, "virtual" allows us to call a function knowing only an interfaces and not the exact type of the object. To create an object you need complete information. Inparticular, you need to know the exact type of what you want tocreate. Consequently, a "call to a constructor" cannot be virtual.

翻译：虚函数只需要”部分“信息就可以自动调用，特别地，这种机制允许我们在只知道接口，不知道具体对象的类型的情况下调用函数。而实例化一个对象需要完整的信息，特别地，必须知道实例化对象的确切类型。总之，构造函数的调用不能是虚的。

因为有虚函数就存在继承，如果不覆盖的话，那派生类的构造函数实际上指向基类的构造函数，一团混乱。那定义成纯虚函数？如此派生类的确是必须重新实现了，但基类成为抽象类无法派生了。

## 为什么析构函数不能抛出异常？

析构函数不推荐抛出异常，如果析构函数可能抛出异常，那么必须要求在析构函数内消化所有异常或者结束程序。more effective c++提出两点理由：

1、如果析构函数抛出异常，则异常点之后的程序不会执行，如果析构函数在异常点之后执行了某些必要的动作比如释放某些资源，则这些动作不会执行，会造成诸如资源泄漏的问题。 （正常情况下调用析构函数抛出异常导致资源泄露）

2、通常异常发生时，c++的机制会调用已经构造对象的析构函数来释放资源，此时若析构函数本身也抛出异常，则前一个异常尚未处理，又有新的异常，会造成程序崩溃的问题。 （在发生异常的情况下调用析构函数抛出异常，会导致程序崩溃）

解决方案：

1、如果某个操作可能会抛出异常，class应提供一个普通函数（而非析构函数），来执行该操作。目的是给客户一个处理错误的机会。

2、如果析构函数中异常非抛不可，那就用try catch来将异常吞下，必须要把这种可能发生的异常完全封装在析构函数内部，决不能让它抛出函数之外。

来自 <https://www.cnblogs.com/hellogiser/p/constructor-destructor-exceptions.html> 

## 纯虚函数不能实现吗？

= 0 means derived classes must provide an implementation, not that the base class can not provide an implementation.

翻译：=0 表示子类必须实现而不是自己不可以实现。

含有纯虚函数的类是抽象类，抽象类不能实例化；如果一个类有5个虚方法，并且这五个方法都实现了（即非纯虚)，这时作者不想让用户实例化这个类，怎么办？可以使用纯虚析构函数，但析构函数为纯虚的话，编译不会有问题，而链接失败，怎么办？给纯虚析构函数加一个实现吧，空函数体也算。

## 智能指针实现

``` cpp
template <typename T>
class SharedPtr {
private:
    T *_ptr;
    int *_refCount;     //should be int*, rather than int

public:

    ~SharedPtr()
    {
        if (_ptr && --*_refCount == 0) {
            delete _ptr;
            delete _refCount;
        }
    }

    SharedPtr() : _ptr((T *)0), _refCount(0){}

    SharedPtr(T *obj) : _ptr(obj), _refCount(new int(1)){}
    //这里无法防止循环引用，若我们用同一个普通指针去初始化两个shared_ptr，此时两个ptr均指向同一片内存区域，但是引用计数器均为1，使用时需要注意。

    SharedPtr(SharedPtr &other) : _ptr(other._ptr), _refCount(&(++*other._refCount)) {}

    SharedPtr &operator=(SharedPtr &other)
    {
        if(this==&other)
            return *this;
        ++*other._refCount;
        if (--*_refCount == 0) {
            delete _ptr;
            delete _refCount;
        }
        _ptr = other._ptr;
        _refCount = other._refCount;
        return *this;
    }  
    T &operator*()
    {
        if (_refCount == 0)
            return (T*)0;
        return *_ptr;
    }
    T *operator->()
    {
        if(_refCount == 0)
            return 0;
        return _ptr;
    }
};
```

## 左值、右值

左值引用是具名变量值的别名，而右值引用则是不具名（匿名）变量的别名。

左值引用通常也不能绑定到右值，但常量左值引用是个“万能”的引用类型。它可以接受非常量左值、常量左值、右值对其进行初始化。

右值值引用通常不能绑定到任何的左值，要想绑定一个左值到右值引用，通常需要std::move()将左值强制转换为右值

来自 <https://blog.csdn.net/hyman_yx/article/details/52044632>

## 深拷贝、浅拷贝

这是由于编译系统在我们没有自己定义拷贝构造函数时，会在拷贝对象时调用默认拷贝构造函数，进行的是浅拷贝！即对指针name拷贝后会出现两个指针指向同一个内存空间。

所以，在对含有指针成员的对象进行拷贝时，必须要自己定义拷贝构造函数，使拷贝后的对象指针成员有自己的内存空间，即进行深拷贝，这样就避免了内存泄漏发生。

## new、delete、malloc、free

1、new分配内存按照数据类型进行分配，malloc分配内存按照大小分配；

2、new不仅分配一段内存，而且会调用构造函数，但是malloc则不会；

3、new返回的是指定对象的指针，而malloc返回的是void*，因此malloc的返回值一般都需要进行类型转化；

4、new是一个操作符可以重载，malloc是一个库函数；

5、new分配的内存要用delete销毁，malloc要用free来销毁；delete销毁的时候会调用对象的析构函数，而free则不会；

6、malloc分配的内存不够的时候，可以用realloc扩容。扩容的原理？new没用这样操作；

7、new如果分配失败了会抛出bad_malloc的异常，而malloc失败了会返回NULL；

8、new和new[]的区别，new[]一次分配所有内存，多次调用构造函数，分别搭配使用delete和delete[]，同理，delete[]多次调用析构函数，销毁数组中的每个对象。而malloc则只能sizeof(int) * n；

## static_cast、dynamic_cast、const_cast、reinterpret_cast

## typeid
