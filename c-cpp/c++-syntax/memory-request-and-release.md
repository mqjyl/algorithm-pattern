# 内存申请和释放

## ✏ 1、`malloc & free`

### 🖋 1.1、`malloc`使用方式

`malloc`函数可以从堆上获得指定字节的内存空间，其函数声明如下：

```cpp
void * malloc(size_t size);
```

其中，形参n为要求分配的字节数。如果函数执行成功，`malloc`返回获得内存空间的首地址；如果函数执行失败，那么返回值为NULL。由于`malloc`函数值的类型为void型指针，因此，可以将其值类型转换后赋给任意类型指针，这样就可以通过操作该类型指针来操作从堆上获得的内存空间。`malloc`申请的空间是连续的。size\_t数据类型经常用到，在 `32 bit` 编译器中是`unsigned int`；在`64 bit`系统中是`unsigned __int64`。`malloc(0)`返回一个有效的空间长度为零的内存首地址，但是没法用\(只进行申请和释放可以，如申请后执行了写操作，释放时会报错\)。

#### 💎 1.1.1、动态申请

**动态申请数组**

```cpp
int* p = (int*)malloc(sizeof(int) * 6);
```

申请一个有6个整形数组元素的一维数组，申请完不能初始化，只能通过`memset()`或循环的方式赋值。因该操作程序运行到这条语句时才在堆区申请的数组，所以被称为动态申请内存\(数组\)，栈区的数组在编译时就已经申请好内存了，所以不是动态申请的。

**动态申请数组指针**

```cpp
int (*p)[3] = (int(*)[3])malloc(sizeof(int) * 3);       // 一维数组指针
int (*q)[2][3] = (int(*)[2][3])malloc(sizeof(int) * 6); // 二维数组指针
```

#### 💎 1.1.2、初始化

需要注意的是，`malloc`函数分配得到的内存空间是未初始化的。因此，一般在使用该内存空间时，要调用另一个函数`memset`来将其初始化为全0。`memset`函数的声明如下：

```cpp
void* memset(void* dest, int c, size_t count);
```

该函数可以将指定的内存空间按字节单位置为指定的字符c。其中，`dest`为要清零的内存空间的首地址，c为要设定的值，count为被操作的内存空间的字节长度。

```cpp
void* memcpy(void* dest, void* src, size_t count);
```

此函数也是按照字节进行拷贝的，`dest`指向目标地址的指针，也就是要被赋值的空间首地址；`src`指向源地址的指针，也就是要被复制的空间的首地址；count跟`memset()`一样表示被拷贝的字节数；返回值也是被赋值的目标地址的指针。

### 🖋 1.2、其他申请方式

#### 💎 1.2.1、`calloc`

```cpp
void* calloc(size_t num, size_t size); 
```

申请连续的 `num` 块内存，每块内存的字节数为size；并将这些字节置为初始化为0，返回值为所申请空间的首地址，申请数组时比较方便，但是效率可能比`malloc()`会慢一点，因为多了一步初始化操作。

#### 💎 1.2.2、`realloc`

```cpp
void* realloc(void* memblock, size_t size);  // 为已分配的内存空间重新申请内存块
```

* `memblock`指向之前已分配内存块的指针；size新内存块大小\(字节数\)；返回值是重新分配内存块的首地址；
* 如果原来分配的内存块的地方无法再扩展到size要求的大小，那么会重新分配一块size大小的内存，原地址的内容会被拷贝过去，相应的返回值也会是新分配区域的首地址，如果可以扩展到指定大小，那返回值还会是重新分配前的返回值。

#### 💎 1.2.3、**`_msize`**

```cpp
size_t _msize(void* memblock);  // Windows平台下专用函数，非C语言标准函数
```

* 返回`malloc() & calloc() & realloc()`等申请内存块的大小，参数是分配内存块的首地址，也就是`malloc() & calloc() & realloc()`等的返回值。

### 🖋 1.3、`free`使用方式

用`malloc()`申请一块内存空间，OS会有一张表记录所申请空间的首地址和这块地址的长度，free\(空间首地址\)，free会从表中查找到这块首地址对应的内存大小，一并释放掉。

1. free\(\)不能去释放栈区的空间，栈区空间是由OS管理的，由OS进行申请和释放。
2. 释放空间后，指针需要置空，避免成为野指针。

```cpp
int* p = (int*)malloc(sizeof(int));
if (p == NULL) { // p 是空指针
    // 空间申请失败的错误处理
} else {
    // 申请成功，假设 p == 0X00000191D34DDAB0;
    free(p);  // p == 0X00000191D34DDAB0; p有值，但是指向的内存空间已经被释放掉了，p就成了一个野指针了
    p = NULL; // 释放空间后，指针需要置空，避免成为野指针
}
int *p;       //这种，定义完指针未初始化，也是野指针

int* q = (int*)malloc(3);
free(q);  // 会报错，int型指针一次操作 4Byte，这里只申请了 3Byte 相当去别人的地盘上拆东西，那肯定是不允许的
int* n = (int*)malloc(7);  // 允许多申请，但是 int型指针一次只能操作 4Byte 多余的空间浪费了
free(n);  // 释放时，从OS维护的表中查找到空间长度，会一并释放掉
```

## ✏ 2、`new & delete`

**`new/new[]`和`delete/delete[]`是运算符。**

### 🖋 2.1、new的使用

```cpp
int* p = new int;     // 申请单个空间
int* q = new int[10]; // 申请连续空间
```

new 在申请基本类型空间时，主要会经历两个过程：

1. 调用 `operator new(size_t)` 或 `operator new[] (size_t)` 申请空间
2. 进行强制类型转换

```cpp
// ====== 申请单个空间 ======
type* p = new type;
// 执行上面这条语句实际的过程是下面的语句
void* tmp = operator new(sizeof(type));       // 调用 operator new(size_t) 申请空间
type* p = static_cast<type*>(tmp); // 进行强制类型转换

// ====== 申请数组空间 ======
type* q = new type[N];
// 执行上面这条语句实际的过程是下面的语句
void* tmp = operator new[](sizeof(type) * N); // 调用 operator new[](size_t) 申请空间
type* p = static_cast<type*>(tmp); // 进行强制类型转换
```

new 在申请 object 空间时，主要会经历三个过程：

1. 调用 `operator new(size_t)` 或 `operator new[] (size_t)` 申请空间
2. 进行强制类型转换
3. 调用类的构造函数

```cpp
// ====== 申请单个object ======
classname* p = new classname;
// 执行上面的语句实际的过程是下面的条语句
void* tmp = operator new(sizeof(classname)); // 调用 operator new(size_t) 申请空间
classname* p = static_cast<classname*>(tmp);  // 进行强制类型转换
p->classname::classname(); // 调用类的构造函数，用户不允许这样调用构造函数，如果用户想调用可以通过 定位(placement)new 运算符 的方式调用

// ====== 申请object数组空间 ======
classname* q = new classname[N];
// 执行上面的语句实际的过程是下面的条语句
void* tmp = operator new[](sizeof(classname) * N); // 调用 operator new[](size_t) 申请空间
classname* q = static_cast<classname*>(tmp); // 进行强制类型转换
q->classname::classname(); // 调用 N次 构造函数
```

#### 💎 2.1.1、定位\(placement\) new 运算符

new 负责在堆（heap）中找到一个足够满足要求的内存块。new 操作符还有另一种变体，称为定位（placement）new 操作符，它能够让你指定要使用的位置。程序员可能使用这种特性来设置其内存管理规程或处理需要通过特定地址进行访问的硬件。

要使用定位 new 特性，首先需要包含头文件 new，它提供了这种版本的 new 操作符的原型；然后将 new 操作符用于提供了所需地址的参数。除了要指定参数外，句法与常规 new 操作符相同。具体地说，使用定位 new 操作符时，变量后面可以有方括号，也可以没有。

```cpp
#include <iostream>
#include <new>
using namespace std;

const int BUF = 512;
const int N = 5;
char buffer[BUF];

int main()
{
    double* pd1, * pd2;
    int i;
    cout << "Calling new and placement new:\n";
    pd1 = new double[N];
    //在 buffer 上给指针申请空间
    pd2 = new(buffer) double[N];   //use buffer array
    for (int i = 0; i < N; i++)
        pd2[i] = pd1[i] = 1000 + 20.0 * i;
    cout << "Memory addresses:\n" << " heap:" << pd1 << " static:" << (void*)buffer << endl;
    cout << "Memory contents:\n";
    for (i = 0; i < N; i++) {
        cout << pd1[i] << " at " << &pd1[i] << "; ";
        cout << pd2[i] << " at " << &pd2[i] << endl;
    }

    cout << "\nCalling new and placement new a second time:\n";
    double* pd3, * pd4;
    pd3 = new double[N];            //find new address
    pd4 = new(buffer) double[N];   //overwrite old data
    for (i = 0; i < N; i++)
        pd4[i] = pd3[i] = 1000 + 40.0 * i;
    cout << "Memory contents:\n";
    for (i = 0; i < N; i++) {
        cout << pd3[i] << " at " << &pd3[i] << "; ";
        cout << pd4[i] << " at " << &pd4[i] << endl;
    }
    cout << "\nCalling new and placement new a third time:\n";
    delete[]  pd1;
    pd1 = new double[N];
    pd2 = new(buffer + N * sizeof(double)) double[N];
    for (i = 0; i < N; i++)
        pd2[i] = pd1[i] = 1000 + 60.0 * i;
    cout << "Memory contents:\n";
    for (i = 0; i < N; i++) {
        cout << pd1[i] << " at " << &pd1[i] << "; ";
        cout << pd2[i] << " at " << &pd2[i] << endl;
    }
    delete[] pd1;
    delete[] pd3;

    return 0;
}
```

* 定位 new 运算符允许我们将 object 或基本类型的数据创建在已申请的内存中，不管它是否已经被使用，而且可以看到新值直接覆盖在旧值上面。
* 并且 定位new 运算符没有对应的 定位 delete ，因为 定位 new 运算符 没有申请内存空间。关于是否使用 delete 来释放内存。本例中 buffer 指定的内存是静态内存，而 delete 只能用于指向常规 new 操作符分配的内存，也就是说 buffer 数组位于 delete 管辖区之外。此时，使用 delete 将引发运行阶段错误。但是，如果 buffer 是使用常规 new 操作符创建的，便可以使用常规 new 操作符来释放整个内存块。
* 定位 new 实际上底层是调用了 `operator new(size_t, void*)`，我们也可以自定义 placement new 比如：`operator new(size_t, long)` 等。

```cpp
char* buf1 = new char[40];
char* buf2 = new char[sizeof(classname)];

int* q1 = new(buf1) int[5];
// ====== 底层调用 ======
void* tmp = operator new(sizeof(int) * 5, buf1); // 调用 operator new(size_t size, void* start); 在给定的空间创建对象
int* q1 = static_cast<int*>(tmp);
// ======================

int* q2 = new(buf1) int[5]; // 如果这样定义 buf1、q1、q2 起始地址是一样的，也是就是 q2 会覆盖掉 q1;  
int* q2 = new(buf1 + sizeof(int) * 5) int[5]; // 正确创建 q2 的方式

classname* p = new(buf2) classname; // 构造函数不需要传参
// ====== 底层调用 ======
void* tmp = operator new(sizeof(classname), buf); // 调用 operator new(size_t size, void* start); 在给定的地址上创建对象
classname* p = static_cast<classname*>(tmp); // 进行强制类型转换
p->classname::classname(); // 调用构造函数，如果是申请object数组空间对调用多次
// ======================

classname* p = new(buf2) classname(type...); // 构造函数需要传参

// 自定义 placement new
long s = 10;
classname* c = new(s) classname;
// ====== 底层调用 ======
void* tmp = operator new(sizeof(classname), s); // 调用 operator new(size_t size, long s); 
classname* c = static_cast<classname*>(tmp); // 进行强制类型转换
c->classname::classname(); // 调用构造函数，如果是申请object数组空间对调用多次
// ======================
```

### 🖋 2.2、new的底层

**`operator new(size_t)`** 是系统提供的全局函数，其 **底层是由 `malloc` 实现的：**

```cpp
/*
operator new：该函数实际通过malloc来申请空间，当malloc申请空间成功时直接返回；申请空间失败，尝试
执行空 间不足应对措施，如果改应对措施用户设置了，则继续申请，否则抛异常。
*/
void *__CRTDECL operator new(size_t size) _THROW1(_STD bad_alloc)
{
    // try to allocate size bytes
    void *p;
    while ((p = malloc(size)) == 0)
        if (_callnewh(size) == 0)
        {
            // report no memory
            // 如果申请内存失败了，这里会抛出bad_alloc 类型异常
            static const std::bad_alloc nomem;
            _RAISE(nomem);
        }
    return (p);
}
```

## ✏ 3、`new`和`malloc`的区别

### 🔨 1、申请的内存所在位置

new操作符从自由存储区（free store）上为对象动态分配内存空间，而`malloc`函数从堆上动态分配内存。自由存储区是C++基于new操作符的一个抽象概念，凡是通过new操作符进行内存申请，该内存即为自由存储区。而堆是操作系统中的术语，是操作系统所维护的一块特殊内存，用于程序的内存动态分配，C语言使用`malloc`从堆上分配内存，使用free释放已分配的对应内存。

那么自由存储区是否能够是堆（问题等价于new是否能在堆上动态分配内存），这取决于operator new 的实现细节。自由存储区不仅可以是堆，还可以是静态存储区，这都看operator new在哪里为对象分配内存。

特别的，new甚至可以不为对象分配内存！定位new的功能可以办到这一点：

```cpp
new(place_address) type
```

place\_address为一个指针，代表一块内存的地址。

### 🔨 2、返回类型安全性

new操作符内存分配成功时，返回的是对象类型的指针，类型严格与对象匹配，无须进行类型转换，故new是符合类型安全性的操作符。而`malloc`内存分配成功则是返回void \* ，需要通过强制类型转换将void\*指针转换成我们需要的类型。类型安全很大程度上可以等价于内存安全，类型安全的代码不会试图分配自己没被授权的内存区域。

### 🔨 3、内存分配失败时的返回值

new内存分配失败时，会抛出`bac_alloc`异常，它不会返回NULL；`malloc`分配内存失败时返回NULL。

### 🔨 4、是否需要指定内存大小

使用new操作符申请内存分配时无须指定内存块的大小，编译器会根据类型信息自行计算，而`malloc`则需要显式地指出所需内存的尺寸。

### 🔨 5、是否调用构造函数/析构函数

使用new操作符来分配对象内存时会经历三个步骤：

1. 第一步：调用operator new 函数（对于数组是operator new\[\]）分配一块足够大的，原始的，未命名的内存空间以便存储特定类型的对象。
2. 第二步：编译器运行相应的构造函数以构造对象，并为其传入初值。
3. 第三部：对象构造完成后，返回一个指向该对象的指针。

使用delete操作符来释放对象内存时会经历两个步骤：

1. 第一步：调用对象的析构函数。
2. 第二步：编译器调用operator delete\(或operator delete\[\]\)函数释放内存空间。

### 🔨 6、对数组的处理

C++提供了new\[\]与delete\[\]来专门处理数组类型，new\[\]分配的内存必须使用delete\[\]进行释放。

new对数组的支持体现在它会分别调用构造函数函数初始化每一个数组元素，释放对象时为每个对象调用析构函数。注意delete\[\]要与new\[\]配套使用，不然会找出数组对象部分释放的现象，造成内存泄漏。

至于`malloc`，它并知道你在这块内存上要放的数组还是啥别的东西，反正它就给你一块原始的内存，在给你个内存的地址就完事。所以如果要动态分配一个数组的内存，还需要我们手动自定数组的大小：

```cpp
int * ptr = (int *) malloc( sizeof(int)* 10 ); //分配一个10个int元素的数组
```

### 🔨 7、new与`malloc`是否可以相互调用

operator new 的实现可以基于`malloc`，而`malloc`的实现不可以去调用new。

### 🔨 8、是否可以被重载

`opeartor new /operator delete`可以被重载。标准库是定义了operator new函数和operator delete函数的8个重载版本：

```cpp
//这些版本可能抛出异常
void * operator new(size_t);
void * operator new[](size_t);
void * operator delete(void *) noexcept;
void * operator delete[](void *) noexcept;
 
//这些版本承诺不抛出异常
void * operator new(size_t , nothrow_t&) noexcept;
void * operator new[](size_t, nothrow_t&) noexcept;
void * operator delete(void *, nothrow_t&) noexcept;
void * operator delete[](void *0, nothrow_t&) noexcept
```

我们可以自定义上面函数版本中的任意一个，前提是自定义版本必须位于全局作用域或者类作用域中。总之，我们有足够的自由去重载operator new /operator delete ，以决定我们的new与delete如何为对象分配内存，如何回收对象。

而`malloc/free`并不允许重载。

### 🔨 9、能够直观地重新分配内存

使用`malloc`分配的内存后，如果在使用过程中发现内存不足，可以使用`realloc`函数进行内存重新分配实现内存的扩充。`realloc`先判断当前的指针所指内存是否有足够的连续空间，如果有，原地扩大可分配的内存地址，并且返回原来的地址指针；如果空间不够，先按照新指定的大小分配空间，将原有数据从头到尾拷贝到新分配的内存区域，而后释放原来的内存区域。

new没有这样直观的配套设施来扩充内存。

### 🔨 10、客户处理内存分配不足

在operator new抛出异常以反映一个未获得满足的需求之前，它会先调用一个用户指定的错误处理函数，这就是new\_handler。new\_handler是一个指针类型：

```cpp
namespace std
{
    typedef void (*new_handler)();
}
```

指向了一个没有参数没有返回值的函数，即为错误处理函数。为了指定错误处理函数，客户需要调用set\_new\_handler，这是一个声明于的一个标准库函数:

```cpp
namespace std
{
    new_handler set_new_handler(new_handler p ) throw();
}
```

set\_new\_handler的参数为new\_handler指针，指向了operator new 无法分配足够内存时该调用的函数。其返回值也是个指针，指向set\_new\_handler被调用前正在执行（但马上就要发生替换）的那个new\_handler函数。

对于`malloc`，客户并不能够去编程决定内存不足以分配时要干什么事，只能看着`malloc`返回NULL。

![](../../.gitbook/assets/200.png)

