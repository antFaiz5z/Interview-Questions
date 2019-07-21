##### 构造函数初始化顺序
    1. 先进行初始化列表初始化，然后进行默认初始化(初始化列表中已初始化的变量不再进行默认初始化，忽略其类内初始值)，最后执行构造函数体内进行的操作。
    2. 没有出现在构造函数初始值列表中的成员将通过相应的类内初始值(如果存在的话)初始化，或者执行默认初始化。

##### 类的前向声明
    1. class A;这种声明有时被称作前向声明，在声明之后，定义之前，是一个不完全类型，不清楚它到底包含哪些成员。
    2. 不完全类型只能在非常有限的情景下使用：可以定义指向这种类型的指针或引用，也可以声明(但是不能定义)以不完全类型作为参数或者返回类型的函数。

##### c++哪些运算符不可以重载

1.**sizeof** 2.**::** 3. **.*** 4. **.** 5. **?:**

##### 把内联函数放在头文件内

和其他函数不一样，内联函数可以在程序中多次定义。毕竟，编译器想要展开函数仅有的函数声明是不够的，还需要函数的定义。不过对于某个给定的内联函数来说，它的多个定义必须完全一致。基于这个原因，内联函数通常定义在头文件中。

##### 警告：如果程序崩溃，输出缓冲区不会被刷新
    1. 如果程序异常终止，输出缓冲区是不会被刷新的。当一个程序崩溃后，他所输出的数据很可能留在输出缓冲区中等待打印。
    2. 当调试一个已经崩溃的程序时，需要确认那些你认为已经输出的数据确实已经刷新了。否则，可能将大量时间浪费在追踪代码为什么没有执行上，而实际上代码已经执行了，只是程序崩溃后缓冲区没有被刷新，输出数据被挂起没有打印而已。


##### 文件模式
默认情况下，即使我们没有指定trunc,以out模式打开的文件也会被截断，为了保留以out模式打开的文件的内容，我们必须同时指定app模式，这样只会将数据追加到文件末尾;或者同时指定in模式，即打开文件同时指定读写操作



##### string
sstream头文件定义了三个类型来支持内存IO，这些类型可以向string写入数据，从string读取数据，就像string是一个IO流一样。

###### stringstream特有的操作
    1. sstream strm  strm是一个未绑定的stringstream流对象。  
    2. sstream strm(s),strm是一个sstream对象，保存string s的一个拷贝。此构造函数是explicit的。
    3. strm.str(), 返回strm所保存的string的拷贝。
    4. strm.str(s), 将string s拷贝到strm中，返回void

```
struct PeopleInfo {
    string name;
    vector<string> phones;
};
string line,word;
vector<PersonInfo> people;
while(getline(cin,line)){
    PersonInfo info;
    istringstream record(line);
    record >> info.name;
    while(record>>word)
        info.phones.push_back(word);
    peoplle.push_back(info);    
}
```

##### 拷贝、赋值与销毁
如果一个构造函数的第一个参数是自身类类型的引用，且任何额外参数都有默认值，则此构造函数是拷贝构造函数。

```
class Foo {
public:
    Foo(); //默认构造函数
    Foo(const Foo&); //拷贝构造函数
}
```
拷贝构造函数在几种情况下都会被隐式地使用。因此，拷贝构造函数通常不应该是explicit的。

###### 拷贝初始化

```
string dots(10, '.'); //直接初始化
string s(dots);  //直接初始化
string s2 = dots; //拷贝初始化
string null_book = "9-999-9999-9"; //拷贝初始化
string nines = string(100, '9'); //拷贝初始化
```
当我们直接初始化时，我们实际上是要求编译器使用普通的函数匹配来选择与我们提供的参数最匹配的构造函数。当我们使用拷贝初始化时，我们要求编译器将右侧运算对象拷贝到正在创建的对象中，拷贝初始化通常使用拷贝构造函数来完成。

###### 拷贝初始化不仅在我们用=定义变量时会发生，在下列情况下也会发生
    1. 将一个对象作为实参传递给一个非引用类型的形参
    2. 从一个返回类型为非引用类型的函数返回一个对象
    3. 用花括号列表初始化一个数组中的元素或者一个聚合类中的成员
    4.某些类类型还会对它们所分配的对象使用拷贝初始化。例如，当我们初始化标准库容器或是调用其insert或push成员时，容器会对其元素进行拷贝初始化。

##### 拷贝赋值运算符
赋值运算符通常应该返回一个指向其左侧运算对象的引用。

##### 析构函数
如同构造函数有一个初始化部分和一个函数体，析构函数也有一个函数体和一个析构部分。在一个构造函数中，成员的初始化是在函数体执行之前完成的，且按照他们在类中出现的顺序进行初始化。在一个构造函数中，首先执行函数体，然后销毁成员。成员按照初始化顺序的逆序销毁。

    1. 隐式销毁一个内置指针类型的成员不会delete它所指向的对象。
    2. 与普通指针不同，智能指针类型是类类型，所以具有析构函数，因此，与普通指针不同，智能指针成员在析构阶段会被自动销毁。
    3.当指向一个对象的引用或指针离开作用域时，析构函数不会执行。


##### 阻止拷贝
- 定义删除的函数
    * 在新标准下，我们可以通过将拷贝构造函数和拷贝赋值运算符定义为删除的函数来阻止拷贝。删除的函数是这样一种函数:我们虽然声明了它们，但不能以任何方式使用他们。在函数的参数列表后面加上=delete来指出我们希望将它定义为删除的。
    * 与=default不同，=delete必须出现在函数第一次声明的时候,这个差异与这些声明的含义在逻辑上是吻合的。与=default的另外一个不同之处是，我们可以对任何函数指定=default(我们只能对编译器可以合成的默认构造函数或拷贝构造成员使用=default)。虽然删除函数的主要用途是禁止拷贝控制成员，但当我们希望引导函数匹配过程时，删除函数有时也是有用的。
    * 对于析构函数已删除的类型，不能定义该类型的变量或释放指向该类型动态分配对象的指针。
    * 一个成员有删除的或不可访问的析构函数会导致合成的默认和拷贝构造函数被定义为删除的，这看起来可能有些奇怪。其原因是，我们可能会创建出无法销毁的对象。

```
struct NoDtor {
    NoDtor() = default; //use合成默认构造函数
    ~NoDtor() = delete;  //我们不能销毁NoDtor类型的对象
};
NoDtor nd; //错误：NoDtor的析构函数是删除的
NoDtor *p = new NoDtor(); //正确：但我们不能delete p
delete p; //错误： NoDtor的析构函数是删除的
```

- 希望阻止拷贝的类应该使用=delete来定义他们自己的拷贝构造函数和拷贝赋值运算符，而不应该将他们声明为private的。
    - 通过声明(但不定义)private的拷贝构造函数，我们可以预先阻止任何拷贝该类型对象的企图；
    - 试图拷贝对象的用户代码将在编译阶段被标记为错误;
    - 成员函数或友元函数中拷贝操作将会导致链接时错误。

##### 拷贝控制和资源管理

###### 行为像值的类
- 为了提供类值的行为，对于类管理的资源，每个对象都应该拥有一份自己的拷贝。为了实现类值行为，HasPtr需要
    1. 定义一个拷贝构造函数，完成string的拷贝，而不是拷贝指针
    2. 定义一个析构函数来释放string
    3. 定义一个拷贝赋值运算符来释放当前的string,并从右侧运算对象拷贝string

```
class HasPtr {
public：
    HasPtr(const string& s= string())
        :ps(new string(s)),
         i(0)
    {}
    Hasptr(const HasPtr &p)
        :ps(new string(*p.ps)),
         i(p.i)
    {}
    ~HasPtr()
    { delete ps; }
    
    HasPtr& operator(const HasPtr& rhs);

private:
    string *ps;
    int i;
};

HasPtr operator=(const HasPtr &rhs)
{
    //先拷贝右值，再删除，防止自赋值
    string* newp = new string(*rhs.ps);  
    delete ps;
    ps = newp;
    i = rhs.i;
    return *this;
}
```
###### 关键概念:拷贝赋值运算符
    1. 当你编写一个赋值运算符时，一个好的模式是先将右侧运算对象拷贝到一个局部临时对象中，当拷贝完成后，销毁左侧运算对象的现有成员就是安全的了。
    2. 对于一个赋值运算符来说，正确工作是非常重要的，即使是将一个对象赋予它自身，也要正确工作。一个好的方法是在销毁左侧运算对象资源之前拷贝右侧运算对象。


##### 对象移动
新标准中一个最主要的特性是可以移动而非拷贝对象的能力。很多情况下都会发生对象拷贝，在其中某些情况下，对象拷贝后就立即被销毁了。在这些情况下，移动而非拷贝对象会大幅度提升性能。

###### 标准库容器、string和shared_ptr类既支持移动也支持拷贝。IO类和unique_ptr类可以移动但不能拷贝。

- 右值引用
    * 为了支持移动操作，新标准引用了一种新的引用类型--右值引用，所谓右值引用就是必须绑定到右值的引用。我们通过&&而不是&来获得右值引用。如我们将要看到的，右值引用有一个重要的性质，只能绑定到一个将要销毁的对象。因此，我们可以自由的将一个右值引用的资源“移动”到另一个对象中。
    * 左值持久;值短暂。考察左值和右值表达式的列表，两者相互区别之处就很明显了:左值有持久的状态，而右值要么是字面常量，要么是在表达式求值过程中创建的临时对象。
    * 由于右值引用只能绑定到临时对象，我们得知
        1. 所引用的对象将要被销毁
        2. 该对象没有其他用户
    * 这两个特性意味着：使用右值引用的代码可以自由的接管所引用的对象的资源。
    * 变量是左值，因此我们不能将一个右值引用直接绑定到一个变量上，即使这个变量是右值引用类型也不行。

```c++
int i = 42;
int &r = i; //正确,r引用i
int &&rr = i;//错误,不能将一个右值引用绑定到一个左值上
int &r = i * 42;//错误，i*42是一个右值
const int &r3 = i * 42;//正确,我们可以将一个const的引用绑定到一个右值上
int &rr2 = i * 42;//正确,将rr2绑定到乘法结果上

int &&rr1 = 42; //正确,字面常量是右值
int &&rr2 = rr1; //错误，表达式rr1是左值！

const int& rr3 = 5; //正确

int &&rr4 = 42;
int &&rr5 = rr4;//错误，表达式rr4是左值！
```

返回左值引用的函数，连同赋值，下标，解引用和前置递增/递减运算符，都是返回左值的表达式的例子。

返回非引用类型的函数，连同算术，关系，位以及后置递增/递减运算符，都生成右值。我们不能将一个左值引用绑定到这类表达式上，但我们可以将一个const的左值引用或者一个右值引用绑定到这类表示式上。

###### 左值持久，右值短暂

由于右值引用只能绑定到临时对象，我们得知

1. 所引用的对象将要被销毁
2. 该对象没有其他用户

###### 右值指向将要被销毁的对象。因此，我们可以从绑定到右值引用的对象“窃取”状态。

###### 变量是左值，因此我们不能将一个右值引用直接绑定到一个变量上，即使这个变量是右值引用类型也不行。

##### 标准库move函数

    1. 虽然我们不能讲一个右值引用直接绑定到一个左值上，但我们可以显式地将一个左值转换为对应的右值引用类型。我们还可以通过调用一个名为move的新新标准库函数来获取绑定到左值上的右值引用，此函数定义在头文件utility中。
    2. move调用告诉编译器：我们有一个左值，但我们希望像一个右值一样处理它。我们必须认识到，调用move就意味着承诺:除了对rr1赋值或销毁它以外，我们将不再使用它。在调用move之后，我们不能对移后源对象的值做任何假设。
    3.使用move的代码应该使用std::move而不是move。这样做可以避免潜在的名字冲突。

```c++
    int &&rr3 = std::move(rr1); //ok
```
##### 移动构造函数和移动赋值运算符

```c++
// 移动操作不应该抛出任何异常
StrVec::StrVec(StrVec &&s) noexcept
    :elements(s.element),first_free(s.first_free),cap(s.cap)
{
    s.element = s.first_free = s.cap = nullptr;
}
```
    1. 不抛出异常的移动构造函数和移动赋值运算符必须标记为noexcept
    2. 移动赋值运算符执行与析构函数和移动构造函数相同的工作。与移动构造函数一样，如果我们的移动赋值运算符不抛出任何异常，我们就应该将它标记为noexcept。类似拷贝赋值运算符，移动赋值运算符必须正确处理自赋值。

```c++
StrVec &StrVec::operator=(StrVec &&rhs) noexcept
{
    if(this != &rhs)
    {
        free();
        elements = rhs.elements;
        first_free = rhs.first_free;
        cap = rhs.cap;
        rhs.elements = rhs.first_free = rhs.cap =  nullptr;
    }
    return *this;
}
```

###### 移后源对象必须可析构

###### 只有当一个类没有定义任何自己版本的拷贝控制成员，且他的所有数据成员都能移动或者移动赋值时，编译器才会为它合成移动构造函数或移动赋值运算符。

### 顺序容器概述

    1. vector 可变大小数组，支持快速随机访问。在尾部之外的位置插入或删除元素可能很慢
    2. deque 双端队列。 支持快速随机访问。在头尾位置插入/删除速度很快
    3. list 双向链表。只支持双向顺序访问。在list中任何位置进行插入/删除操作速度都很快
    4. forward_list 单向链表。只支持单向顺序访问。在链表任何位置进行插入/删除操作速度都很快?
    5. array 固定大小数组。支持快速随机访问。不能添加或删除元素
    6. string 与vector相似的容器，但专门用于保存字符。随机访问快。在尾部插入/删除速度快。

##### 容器定义和初始化
为了创建一个容器为另一个容器的拷贝，两个容器的类型及其元素类型必须匹配。不过，当传递迭代器参数来拷贝一个范围时，就不要求容器类型是相同的了。而且，新容器和原容器中的元素类型也可以不同，只要能将要拷贝的元素转换为要初始化的容器的元素类型即可。

```
list<string> authors={"Milton","Shakesperare","Austen"};
vector<const char*> articles={"a", "an", "the"};
list<string> list2(authors); //正确：类型匹配
deque<string> authorList(authors); //错误:容器类型不匹配
vector<string> words(articles); //错误：容器类型必须匹配
forward_list<string> words(articles.begin(), articles.end());
//正确：可以将const char*元素转换为string
```
###### 当将一个容器初始化为另一个容器的拷贝时，两个容器的容器类型和元素类型都必须相同。

##### 标准库array
与内置数组一样，标准库array的大小也是类型的一部分。当定义一个array时，除了指定元素类型，还要指定容器大小:

```
array<int,42> //类型为：保存42个int的数组

```
为了使用array类型，我们必须同时指定元素类型和大小：

```
array<int,10>::size_type i; //数组类型包括元素类型和大小
array<int>::size_type j; //错误：array<int>不是一个类型
```
值得注意的是，虽然我们不能对内置数组类型进行拷贝或对象赋值操作，但array并无此限制：

```
int digs[10] = {0,1,2,3,4,5,6,7,8,9};
int cpy[10] = digs; //错误：内置数组不支持拷贝或赋值
array<int, 10> digits = {0,1,2,3,4,5,6,7,8,9};
array<int, 10> copy = digits; //正确：只要数组类型匹配即合法
```
与其他容器一样，array也要求初始值的类型必须与要创建的容器类型相同。此外，array还要求元素类型和大小也都一样，因为大小是array类型的一部分。

##### 赋值和swap
    1. c1=c2 将c1中元素替换为c2中元素的拷贝。c1和c2必须具有相同的类型
    2. c={a,b,c...} 将c中元素替换为初始化列表中的元素的拷贝(array并不适用)
    3. swap(c1,c2) && c1.swap(c2) 交换c1和c2中的元素。c1和c2必须具有相同的类型。swap通常比从c2向c1拷贝元素快得多.
    4. seq.assign(b,e) 将seq中的元素替换为迭代器b和e所表示的范围中的元素。迭代器b和e不能指向seq中的元素
    5. seq.assign(il) 将seq中的元素替换为初始化列表il中的元素
    6. seq.assign(n,t) 将seq中的元素替换为n个值为t的元素

###### assign操作不适用于关联容器和array,赋值相关运算会导致指向左边容器内部的迭代器、引用和指针失效。而swap操作将容器内容交换不会导致指向容器的迭代器、引用和指针失效（容器类型为array和string的情况除外）

###### 由于其旧元素被替换,因此传递给assign的迭代器不能指向调用assign的容器

###### 除array外，swap操作不对任何元素进行拷贝、删除或插入操作，因此可以保证在常数时间内完成。

##### 使用emplace操作
- 新标准引入三个新成员---emplace_front、emplace和emplace_back，这些操作构造而不是拷贝元素。这些操作分别对应push_front、insert和push_back，允许我们将元素放置在容器头部、一个指定位置之前或容器尾部。
- 当调用push或insert成员函数时，我们将元素类型的对象传递给它们，这些对象被拷贝到容器中。而当我们调用一个emplace成员函数时，则是将参数传递给元素类型的构造函数。emplace成员使用这些参数在容器管理的内存空间中直接构造元素。
- emplace函数的参数根据元素类型而变化，参数必须与元素类型的构造函数相匹配

```
//iter指向c中的一个元素，其中保存了Sales_data元素
c.emplace_back(); //使用Sales_data的默认构造函数
c.emplace(iter,"999-999999999"); //使用Sales_data(string)
//使用Sales_data接受一个ISBN,一个count和一个price的构造函数
c.emplace_front("978-0590353403", 25, 15.99);
```
###### emplace函数在容器中直接构造元素。传递给emplace函数的参数必须与元素类型的构造函数相匹配。

##### 在顺序容器中访问元素的操作
    1. c.back() 返回c中尾元素的引用。若c为空，函数行为未定义
    2. c.front() 返回c中首元素的引用。若c为空，函数行为未定义
    3.c[n] 返回c中下标为n的元素的引用，n是一个无符号整数。若n>=c.size()，则函数行为未定义
    4.c.at(n) 返回下标为n的元素的引用。如果下标越界，则抛出out_of_range异常

###### at和下标操作只适用于string、vector、deque和array，对于一个空容器调用front和back，就像使用一个越界的下标一样，是一种严重的程序设计错误


```
if(!c.empty()) {
    c.front() = 42; //将42赋予c中的第一个元素
    auto &v = c.back(); //获得指向最后一个元素的引用
    v = 1024; //改变c中的元素
    auto v2 = c.back(); //v2不是一个引用，它是c.back()的一个拷贝
    v2 = 0; //未改变c中的元素
}
```
###### 与往常一样，如果我们使用auto变量来保存这些函数的返回值，并且希望使用此变量来改变元素的值，必须记得将变量定义为引用类型。

##### 从容器内部删除一个元素
成员函数erase从容器中指定位置删除元素。我们可以删除由一个迭代器指定的单个元素，也可以删除由一对迭代器指定的范围内的所有元素。两种形式的erase都返回指向删除的(最后一个)元素之后位置的迭代器。

##### 如果在一个循环中插入/删除deque、string或vector中的元素，不要缓存end返回的迭代器。

##### 容器大小管理操作
###### shrink_to_fit只适用于vector、string和deque，capacity和reserve只适用于vector和string
    1. c.shrink_to_fit() 请将capacity()减少为与size()相同大小，但是，具体的实现可以选择忽略此请求。也就是说，调用shrink_to_fit也不保证一定退出内存空间
    2. c.capacity() 不重新分配内存空间的话，c可以保存保存多少元素
    3. c.reverse(n)，分配至少能容纳n个元素的内存空间
###### reserve并不改变容器中元素的数量，它仅影响vector预先分配多大的内存空间

##### 额外的string操作
##### 构造string的其他方法
###### n、len2和pos2都是无符号值
    1. string s(cp,n) s是cp指向的数组中前n个字符的拷贝。此数组至少应该包含n个字符
    2. string s(s2,pos2) s是string s2从下标pos2开始的字符的拷贝，若pos2>s2.size()，构造函数的行为未定义
    3. string s(s2,pos2,len2) s是string s2从下标pos2开始len2个字符的拷贝。若pos2>s2.size(),构造函数的行为未定义。不管len2的值是多少，构造函数至多拷贝s2.size()-len2个字符

###### 通常当我们从一个const char*创建string时，指针指向的数组必须以空字符结尾，拷贝操作遇到空字符停止。如果我们还传递给构造函数一个计数值，数组就不必以空字符结尾。但是，如果给定计数值大于数组大小，则构造函数的行为是未定义的。

##### 字符串操作
    1. s.substr(pos,n) 返回一个string，包含s中从pos开始的n个字符的拷贝。pos的默认值为0,n的默认值为s.size()-pos,即拷贝从pos开始的所有字符
    2. s.replace(range,args) 删除s中范围range内的字符，替换为args指定的字符。range或者是一个下标和一个长度，或者是一对指向s的迭代器。返回一个指向s的引用 

#### string搜索操作
string搜索函数返回string:: size_type值，该类型是一个unsigned类型，因此，用一个int或其他带符号类型来保存这些函数的返回值不是一个好主意。如果搜索失败，则返回一个名为string:: npos的static成员，标准库将npos定义为一个const string:: size_type类型，并初始化为值-1。由于npos是一个unsigned类型，此初始值意味着npos等于任何string最大的可能大小

##### 搜索操作函数
    1. s.find(args) 查找s中args第一次出现的位置
    2. s.rfind(args) 查找s中args最后一次出现的位置
    3. s.find_first_of(args) 在s中查找args中任何一个字符第一次出现的位置
    4. s.find_last_of(args) 在s中查找args中任何一个字符最后一次出现的位置
    5. s.find_first_not_of(args) 在s中查找第一个不在args中的字符
    6. s.find_last_not_of(args) 在s中查找最后一个不在args中的字符
###### 每个操作都接受一个可选的第二个参数，可用来指出从什么位置开始搜索

##### 数值转换

```
int i = 42;
string s = to_string(i); //将整数i转换为字符表示形式
double d = stod(s); //将字符串s转换为浮点数
```

##### 容器适配器
除了顺序容器外，标准库还定义了三个顺序容器适配器：stack,queue,和priority_queue。

### 关联容器
类型map和multimap定义在头文件map中;set和multiset定义在头文件set中;无序容器则定义在头文件unordered_map和unordered_set中。

##### 关键字类型的要求
关联容器对其关键字类型有一些限制。默认情况下，标准库使用关键字类型的<运算符来比较两个关键字。在集合类型中，关键字类型就是元素类型：在映射类型中,关键字类型是元素的第一部分的类型。

##### pair类型
在介绍关联容器操作之前，我们需要了解名为pair的标准库类型，它定义在头文件utility中.

    1. make_pair(v1, v2) 返回一个用v1和v2初始化的pair。pair的类型从v1和 v2类型推断出来。

##### 创建pair对象的函数
想象有一个函数需要返回一个pair。在新标准下，我们可以对返回值进行列表初始化

```
pair<string, int>
process(vector<string> &v)
{
    //处理v
    if(!v.empty())
        return (v.back(), v.back().size()); //列表初始化
    else 
        reurn pair<string,int>(); //隐式构造返回值
}
```

##### 关联容器额外的类型别名
    1. key_value 此容器类型的关键字类型
    2. mapped_value 每个关键字关联的类型;只适用于map
    3. value_type 对于set, 与key_type相同,对于map,为pair<const key_type, mapped_type>

###### 必须记住，一个map的value_type是一个pair，我们可以改变pair的值,但不能改变关键字成员的值。

##### set的迭代器是const的
虽然set类型同时定义了iterator和const_iterator类型，但两种类型都只允许只读访问set中的元素。与不能改变一个map元素的关键字一样，一个set中关键字也是const的。

##### 向map中添加元素

```
//向word_count插入word的4种方法
word_count.insert({word,1});
word_count.insert(make_pair(word,1));
word_count.insert(pair<string,size_t>(word,1));
word_count.insert(map<string,size_t>::value_type(word,1));
```

##### 检测insert的返回值
insert返回的值依赖于容器类型和参数。对于不包含重复关键字的容器，添加单一元素的insert和emplace版本返回一个pair,告诉我们插入操作是否成功。pair的first成员是一个迭代器，指向具有给定关键字的元素；second成员是一个bool值，指出元素是插入成功还是已经存在与容器中。

##### 从关联容器中删除元素
    1. c.erase(k) 从c中删除每个关键字为k的元素，返回一个size_type值,指出删除的元素的数量
    2. c.erase(p) 从c中删除迭代器p指定的元素。p必须指向c中一个真实元素，不能等于c.end()。返回一个指向p之后元素的迭代器，若p指向c中的尾元素，则返回c.end()
    3. c.erase(b,e) 删除迭代器对b和e所表示的范围中元素，返回e

###### 对一个map使用下标操作,其行为与数组或vector上的下标操作很不相同：使用一个不在容器中的关键字作为下标，会添加一个具有此关键字的元素到map中

##### 在一个关联容器中查找元素的操作
    1. c.find(k) 返回一个迭代器,指向第一个关键字为k的元素，若k不在容器中，则返回尾后迭代器
    2. c.count(k) 返回关键字等于k的元素的数量
    3. c.lower_bound(k) 返回一个迭代器，指向第一个关键字不小于k的元素
    4. c.upper_bound(k) 返回一个迭代器，指向第一个关键字大于k的元素
    5. c.equal_range(k) 返回一个迭代器pair,表示关键字等于k的元素的范围。若k不存在，pair的两个成员均等于c.end()

###### lower_bound返回的迭代器可能指向一个具有给定关键字的元素，但也可能不指向。如果关键字不在容器中，则lower_bound会返回关键字的第一个安全插入点---不影响容器中元素顺序的插位置。

```
//beg和end表示对应此作者的元素的范围
for(auto beg = authors.lower_bound(search_item),end = authors.upper_bound(search_item); beg != end; ++beg)
    cout << beg->second << endl;
```
###### 如果lower_bound和upper_bound返回相同的迭代器，则给定关键字不在容器中。

##### 无序容器
如果关键字类型固有就是无序的，或者性能测试发现问题可以用哈希技术解决，就可以使用无序容器。

#### OOP
OOP基于三个基本概念：数据抽象，继承，动态绑定。
##### 在C++语言中，当我们使用基类的引用时(或指针)调用一个虚函数时将发生动态绑定。

##### 虚函数
    1. virtual只能出现在类内部的声明语句之外而不能用于类外部的函数定义。
    2. 如果基类把一个函数声明成虚函数，则该函数在派生类中隐式地也是虚函数。
    3. C++ 11新标准允许派生类显式地注明它使用某个成员函数覆盖了它继承的虚函数。具体做法是在形参列表后面、或者在const成员函数的const关键字后面、或者在引用成员函数的引用限定符后面添加一个关键字override。

##### 每个类控制自己的成员初始化过程
尽管在派生类对象中含有从基类继承而来的成员，但是派生类并不能直接初始化这些成员。和其他创建了基类对象的代码一样，派生类也必须使用基类的构造函数来初始化它的基类部分。

##### 关键概念：遵循基类的接口
必须明确一点：每个类负责定义各自的接口。因此，派生类对象不能直接初始化基类的成员。尽管从语法上来说我们可以在派生类构造函数体内给它的公有或受保护的基类成员赋值，但是最好不要这样做。和使用基类的其他场合一样，派生类应该遵循基类的接口，并且通过调用基类的构造函数来初始化那些从基类中继承而来的成员。

##### 被用作基类的类
如果我们想将某个类用作基类，则该类必须已经定义而非仅仅声明：

```
class  Quote; //声明但未定义
//错误：Quote必须被定义
class Bulk_Quote : public Quote { ... };

```

##### 防止继承的发生
C++ 11 新标准提供了一种防止继承发生的方法，即在类名后面跟一个关键字final.

```
class NoDerived final { /* */ };
class Last final: Base { /* */};
```

##### 静态类型与动态类型
表达式的静态类型在编译时总是已知的，它是变量声明时的类型或表达式生成的类型；动态类型则是变量或表达式表示的内存中的对象的类型。动态类型直到运行时才可知。

##### 基类的指针或引用的静态类型可能与其动态类型不一致，读者一定要理解其中的原因。

##### ...在对象之间不存在类型转换
派生类向基类的自动类型转换只对指针或引用类型有效，在派生类类型和基类类型之间不存在这样的转换。

##### 基类中的虚函数在派生类中隐含地也是一个虚函数。当派生类覆盖了某个虚函数时，该函数在基类中的形参必须与派生类中的形参严格匹配。
派生类如果定义了一个函数与基类中虚函数的名字相同但是形参列表不同，这仍然是合法的行为。编译器将认为新定义的这个函数与基类中原有的函数是相互独立的。这是派生类并没有覆盖掉基类中版本。

##### C++11新标准中我们可以使用override关键字来说明派生类中的虚函数。
我们还能把某个函数指定为final,如果我们已经把函数定义成final了，则之后任何尝试覆盖该函数的操作都将引发错误。
###### final和override说明符出现在形参列表(包括任何const或引用修饰符)以及位置返回类型之后。

##### 虚函数与默认实参
和其他函数一样，虚函数也可以拥有默认实参。如果某次函数使用了默认实参，则该实参值由本次调用的静态类型决定。
###### 如果虚函数使用默认实参，则基类和派生类中定义的默认实参最好一致。

##### 抽象基类
含有纯虚函数的类是抽象基类，我们不能创建抽象基类的对象。

##### 纯虚函数
一个纯虚函数无需定义。我们通过在函数体的位置(即在声明语句的分号之前)书写=0就可以将一个虚函数说明为纯虚函数。其中，=0只能出现在类内部虚函数声明语句处。
###### 值得注意的是，我们也可以为纯虚函数提供定义，不过函数体必须定义在类的外部。

##### 派生类的成员或友元只能通过派生类对象来访问基类的受保护成员。派生类对于一个基类对象中的受保护成员没有任何访问特权

```
class Base {
protected:
    int prot_mem; //protected成员
};

class Sneaky : public Base {
    friend void clobber(Sneaky&);  //能访问Sneaky::prot_mem
    friend void clobber(Base&); //不能访问Base::prot_mem
    int j; //j默认是private
};

//正确： clobber能访问Sneaky对象的private和protected成员
void clobber(Sneaky &s) 
{
    s.j = s.prot_mem = 0;
}
//错误： clobber不能访问Base的protected成员
void clobber(Base &b) 
{
    b.prot_mem = 0;
}
```

##### 公有，私有和受保护继承
派生访问说明符对于派生类的成员（及友元）能否访问其直接基类的成员没有什么影响。对基类成员的访问权限只于基类中的访问说明符有关。
派生访问说明符的目的是控制派生类用户(包括派生类的派生类在内)对于基类成员的访问权限。

```
class Base {
public: 
    void pub_mem();
protected:
    int prot_mem;
private:
    char priv_mem;
};

struct Pub_Derv: public Base {

};

struct Priv_Derv: private Base {

};

Pub_Derv d1; //继承自Base的成员是public的
Priv_Derv d2; //继承自Base的成员是private的
d1.pub_mem(); //正确
d2.pub_mem(); //错误
```
###### 派生访问说明符还可以控制继承自派生类的新类的访问权限：

```
struct Derived_from_Public : public Pub_Derv {
    //正确： Base::prot_mem在Pub_Derv中仍然是protected的
    int use_base() { return prot_mem; }
};

struct Derived_from_Private : public Priv_Derv {
    //错误： Base::prot_mem在Pub_Derv中是private的
    int use_base() { return prot_mem; }
};
```
##### 友元与继承
就像友元关系不能传递一样，友元关系同样也不能继承。基类的友元在访问派生类成员时不具有特殊性，类似的，派生类的友元也不能随意访问基类的成员。

```
class Base {
    friend class Pal; //Pal在访问Base的派生类时不具有特殊性
};

class Pal {
public:
    int f(Base b) //正确，Pal是Base的友元
    {
        return b.prot_mem;
    }
    
    int f2(Sneaky s) //错误： Pal不是Sneaky的友元
    {
        return s.j;
    }
    
    int f3(Sneaky s) //正确： Pal是Base的友元
    {
        return s.prot_mem;
    }
};
```
##### 改变个别成员的可访问性
有时我们需要改变派生类继承的某个名字的访问级别，通过使用using声明可以达到这一目的。
```
class Base {
public:
    size_t size() const { return n; }
protected:
    size_t n;
};

class Derived : private Base {
public:
    using Base::size;
protected:
    using Base::n;
};

```
###### 通过在类的内部使用using声明语句，我们可以将该类的直接或间接基类中的任何可访问成员(例如：非私有成员)标记出来。using声明语句中的名字的访问权限由该using声明语句之前的访问说明符来决定。          

##### 名字冲突与继承
派生类的成员将隐藏同名的基类成员


##### 虚析构函数
当我们delete一个动态分配的对象的指针时将执行析构函数。如果该指针指向继承体系中的某个类型，则有可能出现指针的静态类型与被删除对象的动态类型不符的情况。例如，如果我们delete一个Quote*类型的指针，则该指针有可能实际指向了一个Bulk_quote类型的对象。
###### 如果基类的析构函数不是虚函数，则delete一个指向派生类对象的基类指针将产生未定义的行为。

##### 定义派生类的拷贝或移动构造函数
在默认情况下，基类默认构造函数初始化派生类对象的基类部分。如果我们想拷贝(或移动)基类部分，则必须在派生类的构造函数初始值列表中显式地使用基类的拷贝(或移动)构造函数。

##### 如果构造函数或析构函数调用了某个虚函数，则我们应该执行与构造函数或析构函数所属类型相对应的虚函数版本。

##### 继承的构造函数
在C++11新标准中,派生类能够重用其直接基类定义的构造函数。
派生类继承基类构造函数的方式是提供一条注明了(直接基类名)的using声明语句。

```
class Bulk_quote ： public Disc_quote {
public:
    using Disc_quote::Disc_quote; //继承Disc_quote的构造函数
};
```
通常情况下，using声明语句只是令某个名字在当前作用域内可见。而当作用于构造函数时，using声明语句将令编译器产生代码。对于基类每个构造函数，编译器都生成一个与之对应的派生构造函数。
这些编译器生成的构造函数形如：

```
derived(parms) ： base(args) { }
```
如果派生类含有自己的数据成员，则这些成员将被默认初始化。

##### 继承的构造函数的特点
    1. 和普通成员的using声明不一样，一个构造函数的using声明不会改变该构造函数的访问级别。例如，不管using声明出现在哪儿，基类的私有构造函数在派生类中还是一个私有构造函数，受保护的构造函数和公有的构造函数也是同样的规则。
    2. 而且，一个using声明的语句不能指定explicit或constexpr。继承基类的explicit或者constexpr属性。 

### 泛型算法
    1.find();接收一对迭代器，以及需要find的值，返回一个迭代器指向查找的结果，若没有查找到，则返回尾后迭代器
    2.count();接收一对迭代器，以及需要find的值，返回find的值出现的次数。
    3.accumulate();接受一对迭代器，以及和的初值，返回累加和。
    4.equal();用于确定两个序列是否保存相同的值。此算法接收三个迭代器：前两个表示第一个序列中的元素范围。
    5.fill()接收一对迭代器表示一个范围，还接收一个值作为第三个参数。fill将给定的这个值赋予输入序列中的每个元素。
    6.fill_n();接受一个单迭代器,一个计数值和一个值。他将给定值赋予迭代器指向的元素的开始的指定个元素。

```
//roster2中的元素数目应该至少与roster1一样多
equal(roster1.cbegin(),roster1.cend(),roster2.cbegin());

//将每个元素重置为0
fill(vec.begin(),vec.end(),0);
```

​    

##### accumulate的第三个参数的类型决定了函数中使用哪个加法运算符，以及返回值的类型。
```
//错误：const char*上没有定义+运算符
string sum = accumulate(v.cbegin(),v.cend(), "");
```

##### 那些只接受一个单一迭代器来表示第二序列的算法，都假定第二个序列至少与第一个序列一样长。

##### 算法不检查写操作
向目的位置迭代器写入数据的算法假定目的位置足够大，能容纳要写入的元素。

```
vector<int> vec; //空向量
//灾难：修改vec中的10个不存在的元素
fill_n(vec.begin(),10,0);

```
#### 介绍back_inserter
他是定义在头文件iterator中的一个函数，back_inserter接收一个指向容器的引用，返回一个与该容器绑定的插入
迭代器，当我们通过此迭代器赋值时，赋值运算符会调用push_back将一个具有给定值的元素添加到容器中;

```
vector<int> vec;
auto it = back_inserter(vec); //通过它赋值会将元素添加到vec中
*it = 42;//vec中现在有一个元素，值为42
```

我们常常使用back_inserter来创建一个迭代器，作为算法的目的位置来使用。

```
vector<int> vec; //空向量
//正确：back_inserter创建一个插入迭代器，可用来向vec添加元素。
fill_n(back_inserter(vec), 10, 0); //添加10个元素到vec
```
#### 拷贝算法
    1.copy();是另一个向目的位置迭代器指向的输出序列中的元素写入数据的算法,此算法接收三个迭代器，前两个
    表示一个输入范围，第三个表示目的序列的起始位置。此算法将输入范围中的元素拷贝至目的序列中。传递给copy
    的目的序列至少要包含与输入序列一样多的元素,copy返回的是其目的迭代器(递增后)的值。
    2.replace();算法读入一个序列，并将其中所有等于给定值的元素都改为另一个值。此算法接收4个参数，前两个
    为范围迭代器，表示输入序列，后两个是一个要搜索的值，另外一个是新值。
    3.replace_copy();如果我们希望保留原序列不变，可以调用replace_copy。此算法接收额外第三个迭代器参数，
    指出调整后序列的保存位置。
    
    ​```
    copy(begin(a1),end(a1),a2);//完成从a1到a2的拷贝
    
    replace(ilst.begin(),ilst.end(), 0 , 42); //将序列中所有0替换成42
    
    //lst并未改变，ivec包含ilst的一份拷贝，不过原来的0变成42
    replace_copy(ilst.begin();ilst.end(); back_inserter(ivec),0,42);
    
    ​```

#### 重排容器元素的算法
    1.sort();重排输入序列中的元素，使之有序。
    2.unique();unique算法重排输入序列，将相邻的重复项“消除”，并返回一个指向不重复范围末尾的迭代器。
    3.stable_sort();稳定的排序算法。
    4.find_if();前两个参数为一对范围迭代器，第三个参数为bool函数对象
    5.for_each();前两个参数为一对范围迭代器，第三个参数为可调用对象。



##### 标准库算法对迭代器而不是容器进行操作。因此，算法不能(直接)添加或删除元素。

### 向算法传递函数
#### 谓词
谓词是一个可调用的表达式，其返回结果是一个能用作条件的值。标准库算法所用的谓词分为2类：一元谓词和二
元谓词。接收谓词参数的算法对输入序列中的元素调用谓词。因此，元素类型必须能转换为谓词的参数类型。

#### 标准库bind函数
它定义在头文件functional中，可以将bind函数看作一个通用的函数适配器，它接受一个可调用对象，生成一个
新的可调用对象来"适应"原对象的参数列表。

```
// 调用bind的一般形式为：
auto newCallable = bind(callable,arg_list);
```
##### 其中，newCallable本身是一个可调用对象，arg_list是一个逗号分隔的参数列表，对应给定的callable的参数，即，当我们调用newCallable时会调用callable,并传递给他arg_list中的参数。arg_list中的参数可能包含形如_n的名字，其中n是一个整数。这些参数是“占位符”，表示newCallable的参数，他们占据了传递给newCallable的参数的“位置”。数值n表示生成的可调用对象中参数的位置:_1为newCallable的第一个参数，_2为第二个参数，依次类推。

##### 名字_n都定义在一个名为placeholders的命名空间中，而这个命名空间本身定义在std命名空间中。

##### bind的参数
我们可以用bind修正参数的值，更一般的，我们用bind绑定给定可以调用对象中的参数或重新安排其顺序。例如，假定f是一个可调用对象，它有5个参数。则下面对bind的调用。

```
//g是一个有两个参数的可调用对象
auto g=bind(f, a, b, _2, c, _1)；
```
g(X,Y) == f(a,b,Y,c,X);

##### 绑定引用参数
默认情况下,bind的那些不是占位符的参数被拷贝到bind返回的可调用对象中。

```
ostream &print(ostream &os, const string &s, char c)
{
	return os << s << c;
}

//但是，不能直接用bind来代替对os的捕获。

//错误，不能拷贝os
for_each(word.begin(), words.end()，bind(print, os, _1, ' '))；

//原因在于bind拷贝其参数，而我们不能拷贝一个ostream。如果我们希望传递给bind一个对象而又不拷贝它，就必须使用标准库ref函数：
for_each(word.begin(),words.end(), bind(print,ref(os), _1, ' '));

函数ref返回一个对象，包含给定的引用，此对象是可以拷贝的。标准库中还有一个cref函数，生成一个保存const引用的类。与bind一样，函数ref和cref也定义在头文件functional中。

```

#### 插入迭代器
插入迭代器是一种迭代器适配器，它接受一个容器，生成一个迭代器，能实现向给定容器添加元素。

##### 插入迭代器的操作
	1.it=t; 在it指定的位置插入值t，假定c是it绑定的容器，依赖于插入迭代器的不同种类，此赋值会分别调用c.push_back(t),c.push_front(t)或c.insert(t,p)，其中p为传递给inserter的迭代器的位置。
	2.*it,++it,it++.这些操作虽然存在，但不会对it自拍任何事情，每个操作都返回it

##### 插入迭代器有三种类型，差异在于元素插入的位置
	1.back_inserter创建一个使用push_back的迭代器
	2.front_inserter创建一个使用push_front的迭代器
	3.inserter创建一个使用insert的迭代器。此函数接受第二个参数，这个参数必须是一个指向给定容器的迭代器，元素将被插入到给定迭代器所表示的元素之前。

#### iostream迭代器
虽然iostream类型不是容器，但标准库定义了可以用于这些IO类型对象的迭代器。istream_iterator读取输入流，ostream_iterator向一个输出流写数据。通过使用流迭代器，我们可以用泛型算法从流对象读取数据以及向其写入数据。

##### istream_iterator操作
当创建一个流迭代器时，必须指定迭代器将要读写的对象类型。一个istream_iterator使用>>来读取流。因此istream_iterator要读取的类型必须定义了输入运算符。当然，我们还可以默认初始化迭代器，这样就创建了一个可以当作尾后值使用的迭代器。

```
istream_iterator<int> in_iter(cin); //从cin读取int
istream_iterator<int> eof; //istream尾后迭代器
while(in_iter != eof){
	//后置递增运算符读取流，返回迭代器的旧值
	//解引用迭代器，获取从流读取的前一个值
	vec.push_back(*in_iter++)；
}

//对于一个绑定到流的迭代器，一旦其关联的流遇到文件尾或者遇到I/O错误，迭代器的值就与尾后迭代器的值相等。

```
###### 我们可以将程序改写成如下形式，这体现了istream_iterator更为有用的地方。
```
istream_iterator<int> int_iter(cin), istream_iterator<int> eof; 从cin读取int
vector<int> vec(int_iter, eof); //从迭代器范围构造vec；
```
###### 使用算法操作流迭代器
```
istream_iterator<int> int_iter(cin), istream_iterator<int> eof; 从cin读取int
cout<< accumulate(int_iter, eof, 0) << endl;

```

##### ostream_iterator操作
我们可以对任何具有输出运算符（<<运算符）的类型定义ostream_iterator。当创建了一个ostream_iterator时，我们可以提供(可选的)第二参数，它是一个字符串，在输出的每个元素后都会打印此字符串。此字符串必须是一个C风格字符串。必须将ostream_iterator绑定到一个指定的流，不允许空的或表示尾后位置的ostream_iterator。

	1.ostream_iterator<T> out(os)；  //out将类型为T的值写到流os中
	2.ostream_iterator<T> out(os,d); //out将类型为T的值写到输出流os中，每个值后面都将输出一个d,d指向一个空字符结尾的字符数组
	3.用<<运算符将val写入到out所绑定的ostream中。val的类型必须与out可写的类型兼容。
	4.*out,++out,out++; 这些运算符是存在的，但不对out做任何事情，每个运算符都返回out

```
//我们可以用ostream_iterator来输出值的序列
ostream_iterator out_iter(cout," ");
for(auto val:vec)
{
	*out_iter++ = val；
}
cout << endl;

//值得注意的是，当我们向out_iter赋值时，可以忽略解引用和递增运算。即可以重写成如下：
for(auto val:vec)
	out_iter = val;
cout << endl;

//可以调用copy来打印vec中的元素。
copy(vec.begin(), vec.end(), out_iter);
cout << endl;

```

#### 反向迭代器
反向迭代器就是在容器中从尾元素向首元素反向移动的迭代器。rbegin(),crbegin(),rend(),crend();这些成员函数返回指向容器尾元素和首元素之前一个位置的迭代器。
```
vector<int> vec = {0,1,2,3,4,5,6,7,8,9}；
sort(vec.begin(), vec.end());//按"正常序"排序vec
sort（vec.rbegin(), vec.rend())； //按逆序排序
```

##### 反向迭代器需要递减运算符
流迭代器不支持递减运算符，因为不可能在一个流中反向移动。forward_list也不支持递减运算符。因此不能在一个流迭代器或forward_list创建反向迭代器。

```
//在一个逗号分隔的列表中查找最后一个元素
auto rcomma = find(line.crbegin(), line.crend()， ',');
cout<< string(line.crbegin(), rcomma)<<endl；
//如果输入为FIRST, MIDDLE, LAST
//则输出结果后打印TSAL 

//我们需要做的是将rcomma转换成一个普通迭代器，能在line中正向移动,我们通过调用reverse_iterator的base成员函数来完成这一转换。

//正确，得到一个正向迭代器，从逗号开始读取字符直到line末尾
cout<< string(rcomma.base(), line.cend()) << endl;
```

##### 其他算法
	1.find
	2.find_if
	3.reverse(beg, end); //反转输入范围中元素的顺序
	4.reversr(beg, end, dest)； 将元素以逆序拷贝到dest
	5.remove_if(beg, end, bool ())； 删除输入范围内符合条件的元素
	6.remove_if(beg, end, dest, bool ())；

#### 特定容器算法
##### 通用版本的sort要求随机访问迭代器，因此不能用于list和forward_list。因为这两个类型分别提供双向迭代器和前向迭代器。

##### 对于list和forward_list，应该优先使用成员函数版本的算法而不是通用算法

##### list和forward_list成员函数版本的算法
	1. lst.merge(lst2),lst.merge(lst2,cmp)；将来自lst2的元素合并入lst。lst和lst2都必须是有序的。元素将从lst2中删除。在合并之后，lst2变为空。第一个版本使用<运算符；第二个版本使用给定的比较操作
	2. lst.remove(val),lst.remove_if(pred)；调用erase删除掉与给定值相等（==）或令一元谓词为真的每个元素。
	3. lst.reverse();反转lst中的元素的顺序
	4. lst.sort(),lst.sort(cmp);使用<或给定比较操作排序元素
	5. lst.unique(),lst.unique(pred)；调用erase删除同一个值的连续拷贝。第一个版本使用==;第二个版本使用给定的二元谓词。

### 动态指针与智能指针
#### 静态内存与栈内存
静态内存用来保存局部static对象,类static数据成员以及定义在任何函数之外的变量。栈内存用来保存定义在函数内的非static对象。分配在静态内存和栈内存中的对象由编译器自动创建和销毁。对于栈对象，仅在其定义的程序块运行时才存在；static对象在使用之前分配,在程序结束时销毁。
#### 动态内存
堆。存储在程序运行时分配的对象，动态对象的生存期由程序来控制。当动态对象不再使用时，我们的代码必须显式的销毁他们。

#### 智能指针 
shared_ptr,unique_ptr,weak_ptr。这三种类型都定义在memory头文件中。

##### shared_ptr类
默认初始化的智能指针中保存着一个空指针。

```
shared_ptr<string> p1；
shared_ptr<list<int>> p2;
```

###### 智能指针的使用方式与普通指针类似。解引用一个智能指针返回它指向的对象。
```
if(p && p->empty())
	*p = "hi";
```

###### shared_ptr和unique_ptr都支持的操作
	1. shared_ptr<T> sp ; 空智能指针，可以指向类型为T的对象
	2. unique_ptr<T> up;
	3. p; 将p用作一个条件判断，若p指向一个对象，则为true
	4. *p; 解引用p,获取它所指向的对象。
	5. p->mem；等价于(*p).mem
	6. p.get()； 返回p中保存的指针。要小心使用，若智能指针释放了其对象。返回的指针所指向的对象也就消失了
	7. swap(p,q) 交换p和q中的指针
	8. p.swap(q)

###### shared_ptr独有的操作
	1. make_shared<T>(args)； 返回一个shared_ptr，指向一个动态分配的类型为T的对象。使用args初始化此对象
	2. shared_ptr<T> p(q)； p是shared_ptr q的拷贝；此操作会递增q中的计数。q中的指针必须能转换为T*
	3. p = q; p和q都是shared_ptr，所保存的指针必须能相互转换。此操作会递减p的引用计数，递增q的引用计数；若p的引用计数变为0,则将其管理的原内存释放。
	4. p.unique(); 若p.use_count()为1，返回true，否则返回false
	5. p.use_count()； 返回与p共享对象的智能指针数量，可能很慢，主要用于调试

###### make_shared函数
最安全的分配和使用动态内存的方法是调用make_shared。

```
//指向一个值为42的int的shared_ptr
shared_ptr<int> p = make_shared<int>(42)；

//指向一个值初始化的int，即，值为0
shared_ptr<int> p2 = make_shared<int>()；
	
```
###### shared_ptr自动销毁所管理的对象，shared_ptr还会自动释放相关联的内存

###### 如果你将shared_ptr存放于一个容器中，而后不需要全部元素，而只使用其中一部分，要记得用erase删除不再需要的那些元素。 

###### 使用new动态分配和初始化对象
```
//默认情况下，动态分配的对象是默认初始化的，这意味着内置类型或组合类型的对象的值将是未定义的，而类类型对象将用默认构造函数进行初始化。
int *pi = new int; //pi指向一个动态分配的，未初始化的无名对象

//也可以对动态分配的对象进行值初始化，只需要在类型名之后跟一对空括号即可。
int *pi2 = new int()； //值初始化为0，*pi2=0；

```
###### 对于定义了自己的构造函数的类类型来说，要求值初始化是没有意义的；不管采用什么形式，对象都会通过默认构造函数来初始化。对于内置类型，两种形式的差别就很大了。。。

###### 动态分配的const对象
类似其他任何const对象，一个动态分配的const对象必须进行初始化
```
//用new分配const对象
const int *pci = new const int(1024);
```

##### shared_ptr跟new结合使用
我们还可以用new返回的指针来初始化智能指针，接收指针参数的智能指针构造函数是explicit的。因此我们不能将一个内置指针隐式转换为一个智能指针，必须使用直接初始化形式来初始化一个智能指针。出于相同原因，一个返回shared_ptr的函数不能在其返回语句中隐式转换一个普通指针。

```
shared_ptr<int> p1 = new int(1024); //错误，必须使用直接初始化
shared_ptr<int> p1(new int(1024)); //正确，使用了直接初始化 

shared_ptr<int> clone(int p){
	return new int(p); //错误
}

shared_ptr<int> clone(int p){
	return shared_ptr<int>(new int(p)); //正确
}
```
##### 定义和改变shared_ptr的其他方法
	1. shared_ptr<T> p(q)； 管理内置指针q所指向的对象；q必须指向new分配的内存，且能够转换为T*类型
	2. shared_ptr<T> p(u); p从unique_ptr u那里接管了对象的所有权；将u置为空
	3. shared_ptr<T> p(q,d) p接管了内置指针q所指向的对象的所有权。q必须能转换为T*类型。p将使用可调用对象d来代替delete
	4. shared_ptr<T> p(p2,d) p是shared_ptr p2的一个拷贝，唯一的区别是p将调用可调用对象d来代替delete
	5. p.reset(),p.reset(q),p.reset(q,d)；若p是唯一指向其对象的shared_ptr，reset会释放此对象，若传递了可选的参数内置指针q，会令p指向q，否则会将p置为空。若还传递了参数d，将会调用d而不是delete来释放q

##### 使用一个内置指针来访问一个智能指针所负责的对象是很危险的，因为我们无法知道对象何时会被销毁

##### get用来将指针的访问权限传递给代码，你只有在确定代码不会delete指针的情况下，才能使用get.特别是，永远不要用get初始化另一个智能指针或者为另一个智能指针赋值。

##### reset成员经常与unique一起使用
```
if(!p.unique())
	p.reset(new string(*p)); //我们不是唯一用户，分配新的拷贝
*p += newVal;  //现在我们知道自己是唯一的用户，可以改变对象的值
```

##### 智能指针陷进
智能指针可以提供对动态分配的内存安全而又方便的管理，但这建立在正确使用的前提下，为了正确使用智能指针，我们必须坚持一些基本规范：
	1. 不适用相同的内置指针初始化(或reset)多个智能指针。
	2. 不delete get()返回的指针。
	3. 不使用get()初始化或reset另一个智能指针。
	4. 如果你使用get()返回的指针，记住当最后一个对应的智能指针销毁后，你的指针就变成无效了。
	5. 如果你使用智能指针管理的资源不是new分配的内存，记住传递给它一个删除器

##### unique_ptr
一个unique_ptr"拥有"它指向的对象。与shared_ptr不同，某个时刻只能有一个unique_ptr指向一个给定对象。当unique_ptr被销毁时，它所指向的对象也被销毁。
##### 由于一个unique_ptr拥有它指向的对象，因此unique_ptr不支持普通的拷贝或复制操作。
```
unique_ptr<string> p1(new string("Stegosaurus"))；
unique_ptr<stirng> p2(p1)； //错误，不支持拷贝
unique_ptr<string> p3;
p3 = p2; //错误：unique_ptr不支持赋值
```

##### unique_ptr的操作
```c++
1. unique_ptr<T> u1; 空unique_ptr,可以指向类型为T的对象。u1会调用delete来释放它的指针
2. unique_ptr<T, D> u2； u2会使用一个类型为D的可调用对象来释放它的指针
3. unique_ptr<T, D> u(d);  空unique_ptr,会使用一个类型为D的可调用对象d来代替delete
4. u = nullptr; 释放u指向的对象，将u置为空
5. u.release()； u放弃对指针的控制权，返回指针，并将u置为空
6. u.reset()； 释放u指向的对象
7. u.reset(q),u.reset(nullptr)； 如果提供了内置指针q,令u指向这个对象，否则将u置为空。
```

###### 虽然我们不能拷贝或赋值unique_ptr，但我们可以通过调用release或reset将指针的所有权从一个（非const）unique_ptr转移到另一个unique
```c++
unique_ptr<string> p2(p1.release()); //p1置为空，转移给p2
unique_ptr<string> p3(new string("Trex"))；
p2.reset(p3.release())； //p2释放原来指向的内存，p3转移到p2
```
###### 如果我们不用另一个智能指针来保存release返回的指针，我们的程序就要负责资源的释放
```c++
p2.release(); //错误，p2不会释放内存，而且我们丢失了指针
auto p = p2.release(); //正确，但我们必须记得delete p
```

###### 传递unique_ptr参数和返回unique_ptr
不能拷贝unique_ptr的规则有一个例外:我们可以拷贝或赋值一个将要被销毁的unique_ptr。(移动赋值函数)

```c++
unique_ptr<int> clone(int p){

	return unique_ptr<int>(new int(p));
}

//还可以返回一个局部对象的拷贝
unique_ptr<int> clone(int p){
	unique_ptr<int> ret(new int(p));
	return ret;
}
```

##### weak_ptr
weak_ptr是一个不控制所指向对象生存期的智能指针，它指向一个shared_ptr

```c++
1. weak_ptr<T> w；空weak_ptr可以指向类型为T的对象
2. weak_ptr<T> w(sp)； 与shared_ptr sp指向相同对象的weak_ptr。T必须能转换为sp指向的类型
3. w = p; p可以是一个shared_ptr或一个weak_ptr。赋值后w与p共享对象
4. w.reset()；将w置空
5. w.use_count()； 与w共享对象的shared_ptr的数量
6. w.expired()； 若w.use_count()为0，返回true,否则返回false
7. w.lock()；如果expire为true,返回一个空shared_ptr；否则返回一个指向w的对象的shared_ptr
```

```c++
if（shared_ptr<int> np =wp.lock()） //若np不为空，则条件成立
{
	//在if中，np与p共享对象
}
```

##### 动态数组
###### new和数组
```c++
	//也可以用一个表示数组的类型别名来分配一个数组
	typedef int arrT[42]；
	int *p = new int; //10个未初始化的int
	delete []p;

	//在新标准中，我们可以提供一个元素初始化器的花括号列表
	int *pia3 = new intp[10]{0,1,2,3,4,5,6,7,8,9};
```

###### 动态分配一个空数组是合法的
```c++
char arr[0]; //错误。不能定义长度为0的数组
char * parr = new char[0]; //正确，但parr不能解引用，对于零长度的数组来说，此指针就像尾后指针一样
```

###### 智能指针和动态数组
标准库提供了一个可以管理new分配的数组的unique_ptr版本
```c++
	unique_ptr<int[]> up(new int[10]); 
	up.release(); //自动调用delete[]销毁其指针
```
###### 当一个unique_ptr指向一个数组时，我们不能使用点和箭头成员运算符。毕竟其指向的是一个数组，我们可以用下标运算符来访问数组中的元素
```
	up[i] = i; 
```

##### allocator类

allocator类定义在头文件memory中，它帮助我们将内存分配和对象构造分离出来。它提供一种类型感知的内存分配方法，它分配的内存是原始的，未构造的。

###### 标准库allocator类及其算法

```c++
allocator<string> alloc;
auto const p = alloc.allocate(n); 
//这个allocate调用为n个string分配了内存

auto q = p;
alloc.construct(q++); //*q为空字符串
alloc.construct(q++,10,'c'); //*q为10个c
alloc.construct(q++,"hi"); //*q为hi!

cout<<*p<<endl; //正确，使用string的输出运算符
cout<<*q<<endl; //灾难，q指向未构造的内存

while(q!=p){
  alloc.destroy(--q);  //释放我们真正构造的string
}
//释放内存通过deallocate来完成
alloc.deallocate(p,n);
//我们传递给deallocate的指针不能为空，它必须指向由allocate分配的内存。而且传递给deallocate的大小参数必须与调用allocated分配内存时提供的大小参数具有一样的值。
```

###### 为了使用allocator返回的内存，我们必须使用construct构造对象。使用未构造的内存，其行为是未定义的。

###### 我们只能对真正构造了的元素进行destroy操作。

###### 拷贝和填充未初始化内存的算法

```c++
//这些函数在给定目的位置创建元素，而不是由系统分配内存给它们。
uninitialized_copy(b,e,b2); //从迭代器b和e指出的输入范围中拷贝元素到迭代器b2指定的未构造的原始内存中。b2指定的内存必须足够大，能容纳输入序列中元素的拷贝。
uninitialized_copy_n(b,n,b2);//从迭代器b指向的元素b开始，拷贝n个元素到b2开始的内存中
uninitialized_fill(b,e,t);//在迭代器b和指定的原始内存范围中创建对象，对象的值均为t的拷贝。
uninitialized_fill(b,n,t);  //从迭代器b指向的内存地址开始创建n个对象。b必须指向足够大的未构造的原始内存，能够容纳给定数目的对象

```