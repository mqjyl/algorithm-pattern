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

int* a = new int(5)
int* b = new int[5]{1,2,3,4,5}

struct Pos {
    double x; double y; double z;
};
Pos *one = new Pos{2.5 ，5.3, 7.2};
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
void* tmp = operator new[](sizeof(classname) * N + 4); // 调用 operator new[](size_t) 申请空间
classname* q = static_cast<classname*>(tmp + 4); // 进行强制类型转换
q->classname::classname(); // 调用 N次 构造函数
```

注意：申请的空间大小为：

```cpp
sizeof(classname) * N + 4
```

前4个字节写入数组大小，最后调用N次构造函数。

这里为什么要写入数组大小呢？释放内存之前会调用每个对象的析构函数。但是编译器并不知道 q 实际所指对象的大小。如果没有储存数组大小，编译器如何知道该把p所指的内存分为几次来调用析构函数呢？

new\[\] 调用的是operator new\[\]，计算出数组总大小之后调用operator new。值得一提的是，可以通过\(\)初始化数组为零值，实例：

```cpp
char* p = new char[32]();
```

等同于：

```cpp
char *p = new char[32];
memset(p, 32, 0);
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

### 🖋 2.3、delete的使用

```cpp
delete p;
delete[] q;
```

delete 的过程与 new 很相似，会调用 `operator delete(void*)` 或 `operator delete[] (void*)` 释放内存。

```cpp
delete p;
// 执行上面的代码实际过程是下面的语句
operator delete(p); // 调用 operator delete(void*); 释放空间

delete[] q;
// 执行上面的代码实际过程是下面的语句
operator delete[](q); // 调用 operator delete[](q); 释放空间
```

**针对简单类型，delete 和 delete\[\] 等同。**

delete 释放 object 空间

1. 调用类的析构函数
2. 调用 `operator delete(void*)` 或 `operator delete[] (void*)` 释放内存

```cpp
delete obj;
// 执行上面的语句实际过程是下面的语句
obj->~classname(); // 首先调用类的析构函数
operator delete(obj);   // 调用 operator delete(void*); 释放 object 内存空间

delete[] obj1;
// 执行上面的语句实际过程是下面的语句
obj->~classname(); // 调用 N次 类的析构函数
operator delete[](obj1); // 调用 operator delete[](void*); 释放 object 内存空间
```

new\[\]分配的内存只能由delete\[\]释放。如果由delete释放会崩溃，假设指针`obj1`指向new\[\]分配的内存，因为要4字节存储数组大小，实际分配的内存地址为`obj1-4`，系统记录的也是这个地址。`delete[]` 实际释放的就是`obj1-4`指向的内存。而`delete`会直接释放`obj1`指向的内存，这个内存根本没有被系统记录，所以会崩溃。

### 🖋 2.4、delete的底层

```cpp
/*
operator delete: 该函数最终是通过free来释放空间的
*/
void operator delete(void *pUserData)
{
	_CrtMemBlockHeader * pHead;
	
	RTCCALLBACK(_RTC_Free_hook, (pUserData, 0));
	
	if (pUserData == NULL)
		return;
	
	_mlock(_HEAP_LOCK); /* block other threads */
	__TRY
		/* get a pointer to memory block header */
		pHead = pHdr(pUserData);
		
		/* verify block type */
		_ASSERTE(_BLOCK_TYPE_IS_VALID(pHead->nBlockUse));
		
		 _free_dbg( pUserData, pHead->nBlockUse );
		
	__FINALLY
		_munlock(_HEAP_LOCK); /* release other threads */
	__END_TRY_FINALLY
	
	 return;
}
/*
free的实现
*/
#define free(p) _free_dbg(p, _NORMAL_BLOCK)
```

1. 在 new 一个数组时，与 `malloc` 相似OS会维护一张记录数组头指针和数组长度的表；
2. 释放基本数据类型的指针时，数组的头指针最终会被 free\(q\)释放，所以不论是 delete q或者 delete\[\] q最终的结果都是调用 free\(q\) 释放内存；
3. 释放 object 数组空间时，如果有空间需要在析构函数中释放，直接调用 delete obj 只会调用一次析构函数，然后就执行 free\(obj\) 没有调用其他数组元素的析构函数很容易导致内存泄漏，所以在释放 object 数组时，一定要用 delete\[\] obj 释放内存。

### 🖋 2.5、new和delete的 重写 和 重载

当我们在C++中使用new 和delete时，其实执行的是全局的`::operator new`和`::operator delete`。对于自定义类型来说，当我们使用new来进行构建对象时，首先会检查这个类是否重载了new运算符，如果这个类重载了new运算符那么就会调用类提供的new运算符来进行内存分配，而如果没有重载new运算符时就使用系统提供的全局new运算符来进行内存分配。内置类型则总是使用系统提供的全局new运算符来进行内存的分配。对象的内存销毁流程也是和分配一致的。new和delete运算符既支持全局的重载又支持类级别的函数重载。下面是这种运算符的定义的格式：

```cpp
//全局运算符定义格式
void* operator new(size_t size [, param1, param2,....]);
void operator delete(void *p [, param1, param2, ...]);

//类内运算符定义格式
class CA
{
    void* operator new(size_t size [, param1, param2,....]);
    void operator delete(void *p [, param1, param2, ...]);
};
```

标准库提供的全局的 operator new\( 函数 \) 有六种重载形式，operator delete也有六种重载形式：

```cpp
// =======================new=========================//
void *operator new(std::size_t count)
    throw(std::bad_alloc);              // 一般的版本
void *operator new[](std::size_t count)  
    throw(std::bad_alloc);
    
void *operator new(std::size_t count,   // 兼容早版本的 new
    const std::nothrow_t const&) throw();     // 内存分配失败不会抛出异常
void *operator new[](std::size_t count,  
    const std::nothrow_t const&) throw();
    
void *operator new(std::size_t count, void *ptr) throw();  // placement 版本
void *operator new[](std::size_t count, void *ptr) throw();

// ======================delete========================//
void * operator delete(void *) noexcept;
void * operator delete[](void *) noexcept;
 
void * operator delete(void *, std::nothrow_t const&) noexcept;
void * operator delete[](void *, std::nothrow_t const&) noexcept;

void * operator delete(void *, void *) noexcept;
void * operator delete[](void *, void *) noexcept;

// VS C++下还有两个，GCC下不确定
void * operator delete(void *, size_t) noexcept;
void * operator delete[](void *, size_t) noexcept;
```

重载的例子：

```cpp
class Foo
{
public:
    int _id;
    long _data;
    string _str;
public:
    Foo() :_id(0) { cout << "default ctor.this=" << this << " id=" << _id << endl; }
    Foo(int i) :_id(i) { cout << "ctor.this=" << this << " id=" << _id << endl; }
    ~Foo() { cout << "dtor.this=" << this << " id=" << _id << endl; }
    static void* operator new(size_t size);
    static void operator delete(void* pdead, size_t size);
    static void* operator new[](size_t size);
    static void operator delete[](void* pdead, size_t size);

    static void* operator new(size_t size, const std::nothrow_t& nothrow_value);
    static void operator delete(void* pdead, const std::nothrow_t& nothrow_value);
};

void* Foo::operator new(size_t size)
{
    Foo* p = (Foo*)malloc(size);
    cout << "调用了Foo::operator new" << endl;
    return p;
}

void Foo::operator delete(void* pdead, size_t size)
{
    cout << "调用了Foo::operator delete" << endl;
    free(pdead);
}

void* Foo::operator new[](size_t size)
{
    Foo* p = (Foo*)malloc(size);
    cout << "调用了Foo::operator new[]" << endl;
    return p;
}

void Foo::operator delete[](void* pdead, size_t size)
{
    cout << "调用了Foo::operator delete[]" << endl;
    free(pdead);
}

void* Foo::operator new(size_t size, const std::nothrow_t& nothrow_value)
{
    std::cout << "调用了Foo::operator new nothrow" << std::endl;
    return malloc(size);
}

void Foo::operator delete(void* pdead, const std::nothrow_t& nothrow_value)
{
    std::cout << "调用了Foo::operator delete nothrow" << std::endl;
    free(pdead);
}

int main()
{
    Foo* pf = new Foo(5);
    Foo* pf1 = new Foo[3];
    Foo* pf2 = new(std::nothrow) Foo;
    Foo* pf3 = new(std::nothrow) Foo;
    delete pf;
    delete[] pf1;
    delete pf2; 
    Foo::operator delete(pf3, std::nothrow);

    cout << "=======================" << endl;

    Foo* pf4 = ::new Foo(5);
    Foo* pf5 = ::new Foo[3];
    ::delete pf4;
    ::delete[] pf5;
}

// 输出
调用了Foo::operator new
ctor.this=00B00640 id=5
调用了Foo::operator new[]
default ctor.this=00AF5C3C id=0
default ctor.this=00AF5C60 id=0
default ctor.this=00AF5C84 id=0
调用了Foo::operator new nothrow
default ctor.this=00B00730 id=0
调用了Foo::operator new nothrow
default ctor.this=00B00550 id=0
dtor.this=00B00640 id=5
调用了Foo::operator delete
dtor.this=00AF5C84 id=0
dtor.this=00AF5C60 id=0
dtor.this=00AF5C3C id=0
调用了Foo::operator delete[]
dtor.this=00B00730 id=0
调用了Foo::operator delete
调用了Foo::operator delete nothrow
=======================
ctor.this=00B00050 id=5
default ctor.this=00AF5C3C id=0
default ctor.this=00AF5C60 id=0
default ctor.this=00AF5C84 id=0
dtor.this=00B00050 id=5
dtor.this=00AF5C84 id=0
dtor.this=00AF5C60 id=0
dtor.this=00AF5C3C id=0
```

> 有一点需要注意，operator delete的自定义参数重载并不能手动调用。即`delete(std::nothrow) pf3;`是错误的。并且第65行在释放`pf2`时调用的是`operator delete(void* pdead, size_t size);`而不是`operator delete(void* pdead, const std::nothrow_t& nothrow_value)`，并且如果删除了`operator delete(void* pdead, size_t size);`则后面的delete会报错，也就是说正常情况下delete不能调用`operator delete(void* pdead, const std::nothrow_t& nothrow_value)`，这个重载函数只在一种情况下被调用：当new关键字抛出异常时。这里并不是因为第二个参数是引用，必须赋值，改成按值传参也不行，如果将`operator delete(void* pdead, size_t size);`改成`operator delete(void* pdead, size_t& size);`后面的delete也会报错。
>
> （**具体原因不太清楚**，**这里留一个疑问** ❓ ）

对于`new/delete`运算符重载有如下规则：

1. new和delete运算符重载必须成对出现。
2. **new运算符的第一个参数必须是size\_t类型的，也就是指定分配内存的size尺寸；delete运算符的第一个参数必须是要销毁释放的内存对象。其他参数可以任意定义。**
3. 系统默认实现了new/delete、new\[\]/delete\[\]、 placement new 5个运算符（expression）。它们都有特定的意义。使用它们在底层调用对应的函数，可以是系统提供的，也可能是被重写或重载的。
4. 你可以重写默认实现的全局运算符，比如你想对内存的分配策略进行自定义管理或者你想监测堆内存的分配情况或者你想做堆内存的内存泄露监控等。**但是你重写的全局运算符一定要满足默认的规则定义。也可以重载全局运算符，但也必须符合默认的规则，即第一个参数不能变。**
5. 如果你想对某个类的堆内存分配的对象做特殊处理，那么你可以重载这个类的new/delete运算符。当重载这两个运算符时虽然没有带static属性，但是不管如何对类的new/delete运算符的重载总是被认为是静态成员函数。
6. **当delete运算符的参数&gt;=2个时，就需要自己负责对象析构函数的调用，并且以运算符函数的形式来调用delete运算符。**
7. 这里的重载遵循作用域覆盖原则，即在里向外寻找operator new的重载时，只要找到operator new\(\)函数就不再向外查找，如果参数符合则通过，如果参数不符合则报错，而不管全局是否还有相匹配的函数原型。比如如果这里只将Foo中`operator new(size_t, const std::nothrow_t&)`删除掉，就会在61行报错：

```cpp
error C2660: “Foo::operator new”: 函数不接受 2 个参数。
```

### 🖋 2.6、placement new & placement delete 运算符

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

* `placement new expression` 和 `placement operator new()` 通常被笼统的称作`placement new`。但两者概念不同： `placement operator new()`是标准`operator new()`的一个重载函数。 `placement new expression`，它属于c++语法，实际上底层是调用了`placement operator new()`，即`operator new(size_t, void*)`。`placement delete`表示`placement operator delete()`函数，不存在`placement delete expression`。即定位 new 运算符没有对应的 定位 delete ，因为 定位 new 运算符 没有申请内存空间。函数底层如下：

```cpp
#ifndef __PLACEMENT_NEW_INLINE
#define __PLACEMENT_NEW_INLINE
inline void *__cdecl operator new(size_t, void *_P)
        {return (_P); }
        
#if     _MSC_VER >= 1200
inline void __cdecl operator delete(void *, void *)
    {return; }
#endif
#endif
```

* 定位\(placement\) new 运算符允许我们将 object 或基本类型的数据创建在已申请的内存中，不管它是否已经被使用，而且可以看到新值直接覆盖在旧值上面。
* 全局的定位 new 不能更改，但可以重载，其实参数**&gt;=2**的`operator new`重载函数都可以叫做`placement new` 函数，要求是`placement operator new()`的第一个函数参数必须是`std::size_t`，表示要申请的内存的大小；`placement delete()`的第一个函数参数必须是`void*`， 表示要释放的对象指针。`placement operator new()`都可以用`placement delete expression`来调用。`nothrow`版本的`new` 和 `delete`，也是c++标准，它们就是通过`placement new`和`placement delete`实现的。

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
p->classname::classname(); // 调用构造函数，如果是申请object数组空间就调用多次
// ======================

classname* p = new(buf2) classname(type...); // 构造函数需要传参

// 自定义 placement new ，一般没啥意义
long s = 10;
classname* c = new(s) classname;
// ====== 底层调用 ======
void* tmp = operator new(sizeof(classname), s); // 调用 operator new(size_t size, long s); 
classname* c = static_cast<classname*>(tmp); // 进行强制类型转换
c->classname::classname(); // 调用构造函数，如果是申请object数组空间对调用多次
// ======================
```

* 关于是否使用 delete 来释放内存。上面的例子中 buffer 指定的内存是静态内存，而 delete 只能用于指向常规 new 操作符分配的内存，也就是说 buffer 数组位于 delete 管辖区之外。此时，使用 delete 将引发运行阶段错误。但是，如果 buffer 是使用常规 new 操作符创建的，便可以使用常规 delete 操作符来释放整个内存块：

```cpp
char* buf = new char[512];
// 定位new 在指定位置创建一个 object
classname *obj = new(buf) classname;
// 程序员需要显式调用类的析构函数
obj->~classname();
delete[] buf;
```

> 做一个总结，否则后面的内容看起来有点乱：重载运算符的本质的是重载底层的函数，而这些函数其实是可以显示调用的，而函数不管重写标准库提供的还是重载的函数，第一个参数必须是符合要求的。大于两个参数的new函数都可以叫做定位new，并且第二个参数为地址的定位new 是标准库提供的（可以叫做标准定位 new），标准库还提供了一组两参数的版本，即`nothrow`版。定位new函数都可以用定位new运算符调用，但是没有定位delete运算符。重载的大于等于两个参数的new和delete函数作用有限（标准定位 new 除外），也就是说大多数情况下是不需要重写和重载new和delete函数的。

### 🖋 2.7、对象的自动删除技术

一般来说系统对new/delete的默认实现就能满足我们的需求，我们不需要再去重载这两个运算符。那为什么C++还提供对这两个运算符的重载支持呢？答案还是在运算符本身具有的缺陷所致。我们知道用new关键字来创建堆内存对象是分为了2步：1.是堆内存分配，2.是对象构造函数的调用。而这两步中的任何一步都有可能会产生异常。如果说是在第一步出现了问题导致内存分配失败则不会调用构造函数，这是没有问题的。如果说是在第二步构造函数执行过程中出现了异常而导致无法正常构造完成，那么就应该要将第一步中所分配的堆内存进行销毁。**C++中规定如果一个对象无法完全构造那么这个对象将是一个无效对象，也不会调用析构函数。为了保证对象的完整性，当通过new分配的堆内存对象在构造函数执行过程中出现异常时就会停止构造函数的执行并且自动调用对应的delete运算符来对已经分配的堆内存执行销毁处理，这就是所谓的对象的自动删除技术**。正是因为有了对象的自动删除技术才能解决对象构造不完整时会造成内存泄露的问题。

**全局delete运算符函数所支持的对象的自动删除技术虽然能解决对象本身的内存泄露问题，但是却不能解决对象构造函数内部的数据成员的内存分配泄露问题，此时我们需要将析构函数中需要释放的数据成员的内存在重载的operator delete函数中进行释放。**

### 🖋 **2.8、**operator new运用技巧

#### \*\*\*\*💎 2.8.**1、operator new重载运用于调试：**

operator new的重载是可以有自定义参数的，那么我们如何利用自定义参数获取更多的信息呢？这里一个很有用的做法就是给operator new添加两个参数：`char* file, int line`，这两个参数记录new关键字的位置，然后再在new时将文件名和行号传入，这样我们就能在分配内存失败时给出提示：输出文件名和行号。  
  
那么如何获取当前语句所在文件名和行号呢，windows提供两个宏：`__FILE__`和`__LINE__`。利用它们可以直接获取到文件名和行号，也就是 `new(__FILE__, __LINE__)` 由于这些都是不变的，因此可以再定义一个宏：`#define new new(__FILE__, __LINE__)`。这样我们就只需要定义这个宏，然后重载operator new即可。源代码如下，这里只是简单输出new的文件名和行号：

```cpp
// A.h
class A
{
public:
    A(){
        std::cout << "call A constructor" << std::endl;
    }
    ~A(){
        std::cout << "call A destructor" << std::endl;
    }

    void* operator new(size_t size, const char* file, int line)
    {
        std::cout << "call A::operator new on file:" << file << "  line:" << line << std::endl;
        return malloc(size);
        return NULL;
    }
};

// main.cpp
#include "A.h"
#define new new(__FILE__, __LINE__)

int main()
{
    A* p1 = new A;
    delete p1;
    return 0;
}
```

注意：类定义应该放在宏定义之前。否则在类A中的operator new重载中的new会被宏替换，整个函数就变成了：`void* operator new(__FILE__, __LINE__)(size_t size, char* file, int line)`，编译器自然会报错。

#### \*\*\*\*💎 2.8.**2、内存池优化**

operator new的另一个大用处就是内存池优化，内存池的一个常见策略就是一次性分配一块大的内存作为内存池\(buffer或pool\)，然后重复利用该内存块，每次分配都从内存池中取出，释放则将内存块放回内存池。在我们客户端调用的是new关键字，我们可以改写operator new函数，让它从内存池中取出\(当内存池不够时，再从系统堆中一次性分配一块大的\)，至于构造和析构则在取出的内存上进行，然后再重载operator delete，它将内存块放回内存池。

#### \*\*\*\*💎 2.8.**3、`STL`中的new**

在`SGI STL`源码中，`defalloc.h`和`stl_construct.h`中提供了最简单的空间配置器\(`allocator`\)封装。它将对象的空间分配和构造分离开来，虽然在`defalloc.h`中仅仅是对`::operator new`和`::operator delete`的一层封装，但是它仍然给`STL`容器提供了更加灵活的接口。`SGI STL`真正使用的并不是`defalloc.h`中的分配器，而是`stl_alloc.h`中的`SGI`精心打造的"双层级配置器"，它将内存池技术演绎得淋漓尽致，值得细细琢磨。顺便提一下，在`stl_alloc.h`中并没有使用`::operator new/delete` 而直接使用`malloc`和`free`。具体缘由均可参见《`STL`源码剖析》。

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
// 这些版本可能抛出异常
void * operator new(size_t);
void * operator new[](size_t);
void * operator delete(void *) noexcept;
void * operator delete[](void *) noexcept;
 
// 这些版本承诺不抛出异常
void * operator new(size_t, nothrow_t const&) noexcept;
void * operator new[](size_t, nothrow_t const&) noexcept;
void * operator delete(void *, nothrow_t const&) noexcept;
void * operator delete[](void *, nothrow_t const&) noexcept

// VS C++下还有两个，GCC下不确定
void * operator delete(void *, size_t) noexcept;
void * operator delete[](void *, size_t) noexcept;
```

我们可以重载上面函数版本中的任意一个，前提是自定义版本必须位于全局作用域或者类作用域中。总之，我们有足够的自由去重载operator new /operator delete ，以决定我们的new与delete如何为对象分配内存，如何回收对象。

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

