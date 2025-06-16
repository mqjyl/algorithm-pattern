# 函数对象

函数对象：重载了调用操作符`()`的类对象。当用该类对象调用此操作符时，其表现形式如同普通函数调用一般，因此取名叫函数对象。如：

```cpp
class A
{
public:
    int operator() (int val)
    {
        return val > 0 ? val : -val;
    }
};

int main()
{
    A a;
    cout << a(-10);  // 10
    return 0;
}
```

## :pencil2: **1、**&#x4E0D;同函数复用相同处理代码

### :pen\_fountain: 1.1、**C语言的处理方式**

使用函数指针和回调函数来实现代码复用。例如`qsort()`

```cpp
#include <stdio.h>  
#include <stdlib.h>  
int arr_sort( const void *a, const void *b) {
    return *(int*)a - *(int*)b;  
}
  
int main() {  
   int arr[5] = { 4, 1, 2, 5, 6 };  
   qsort(arr, 5, sizeof(arr[0]), arr_sort);
   // qsort()函数的第四个参数为 函数指针：
   // typedef int (__cdecl* _CoreCrtNonSecureSearchSortCompareFunction)(void const*, void const*);
   for (int i = 0; i < 5; i++){  
      printf("%i\n", arr[i]);  
   }
   return 0;  
}
```

### :pen\_fountain: 1.2、**C++语言的处理方式**

#### :hamster: **1.2.1、**&#x51FD;数指针方式

```cpp
#include <iostream>  
#include <algorithm>  
using namespace std;  
inline bool Sort(int a, int b){
    return a > b;
}
inline void Display(int a){
    cout << a << endl;
}

int main() {  
    int arr[5] = { 4, 1, 2, 5, 6 }; 
    sort(arr, arr + 5, Sort);  
    for_each(arr, arr + 5, Display);
    return 0;   
}
```

#### :hamster: 1.2.2、函数模板方式

```cpp
#include <iostream>  
#include <algorithm>  
using namespace std;  
template<class T>
inline bool Sort(T const& a, T const& b){
    return a > b;
}

template<class T>
inline void Display(T a){
    cout << a << endl;
}

int main() {  
    int arr[5] = { 4, 1, 2, 5, 6 }; 
    sort(arr, arr + 5, Sort<int>);  
    for_each(arr, arr + 5, Display<int>);
    return 0;   
}
```

#### :hamster: 1.2.3、仿函数方式

```cpp
class Sort{  
public:  
    bool operator()(int a, int b) const {
        return a > b;
    }   
};

class Display{
public:
    void operator()(int a) const{
        cout << a << endl;
    } 
};

int main()  
{  
    int arr[5] = { 4, 1, 2, 5, 6 }; 
    sort(arr, arr + 5, Sort());  
    for_each(arr, arr + 5, Display());
    return 0;   
}
```

#### :hamster: 1.2.4、仿函数模板方式

```cpp
template<class T>
class Sort{  
public:  
    inline bool operator()(T const& a, T const& b) const {
        return a > b;
    }   
};

template<class T>
class Display{
public:
    inline void operator()(T const& a) const{
        cout << a << endl;
    } 
};

int main()  
{  
    int arr[5] = { 4, 1, 2, 5, 6 }; 
    sort(arr, arr + 5, Sort<int>());  
    for_each(arr, arr + 5, Display<int>());
    return 0;   
}
```

## :pencil2: 2、优势

### :pen\_fountain: 2.1、函数对象和普通函数

与普通函数相比，函数对象比函数更加灵活，函数对象有以下的优势：

1. **函数对象可以有自己的状态**。我们可以在类中定义状态变量，这样一个函数对象在多次的调用中可以共享这个状态。但是函数调用没这种优势，除非它使用全局变量来保存状态。
2. 函数对象有自己特有的类型，而普通函数无类型可言。这种特性对于使用C++标准库来说是至关重要的。这样我们在使用`STL`中的函数时，可以传递相应的类型作为参数来实例化相应的模板，从而实现我们自己定义的规则。比如自定义容器的排序规则。
3. **函数对象一般快于普通函数**：因为函数对象一般用于模板参数，模板一般会在编译时会做一些优化。

### :pen\_fountain: 2.2、函数对象与函数指针

尽管**函数指针**被广泛用于实现函数回调，但C++还提供了一个重要的实现回调函数的方法，那就是函数对象。函数对象（也称“函数符”）是重载了“()”操作符的普通类的对象。

用函数对象代替函数指针有几个优点：

* 首先，因为对象可以在内部修改而不用改动外部接口，因此**设计更灵活，更富有弹性**。函数对象也具备有存储先前调用结果的数据成员。在使用普通函数时需要将先前调用的结果存储在全程或者本地静态变量中，但是全程或者本地静态变量有某些我们不愿意看到的缺陷。
* 其次，在**函数对象中编译器能实现内联调用**，从而更进一步增强了性能。这在函数指针中几乎是不可能实现的。
* C++11还提供了**lambda表达式**来实现函数的灵活调用。

### :pen\_fountain: 2.3、函数对象与Lambda表达式

定义一个可以拥有状态的函数对象，其用于生成连续序列：

```cpp
class IntSequence
{
public:
    IntSequence(int initVal) : value{ initVal } {}

    int operator()() { return ++value; }
private:
    int value;
};

// T需要支持输出流运算符
template <typename T>
class Print
{
public:
    void operator()(T elem) const
    {
        cout << elem << ' ' ;
    }
};


int main()
{
    vector<int> v(10);
    std::generate(v.begin(), v.end(), IntSequence{ 0 });
    /*  lambda实现同样效果
        int init = 0;
        std::generate(v.begin(), v.end(), [&init] { return ++init; });
    */
    std::for_each(v.begin(), v.end(), [](int x) { cout << x << ' '; });
    // std::for_each(v.begin(), v.end(), Print<int>{});
    //输出：1, 2, 3, 4, 5, 6, 7, 8, 9, 10
    return 0;
}
```

可以看到，函数对象可以拥有一个私有数据成员，每次调用时递增，从而产生连续序列。同样地，你可以用lambda表达式实现类似的效果，但是必须采用引用捕捉方式。但是，函数对象可以实现更复杂的功能，而用lambda表达式则需要复杂的引用捕捉。当需要捕捉的外部变量很多时，函数对象就比较有优势。

可以看到`Print<int>`的实例可以传入`std::for_each`，其表现可以像函数一样，因此我们称这个实例为函数对象。大家可能会想，`for_each`为什么可以既接收`lambda`表达式，也可以接收函数对象，其实`STL`算法是泛型实现的，其不关心接收的对象到底是什么类型，但是必须要支持函数调用运算：

```cpp
// for_each的类似实现
namespace std
{
    template <typename Iterator, typename Operation>
    Operation for_each(Iterator act, Iterator end, Operation op)
    {
        while (act != end)
        {
            op(*act);
            ++act;
        }
        return op;
    }
}
```

泛型提供了高级抽象，不论是`lambda`表达式、函数对象，还是函数指针，都可以传入`for_each`算法中。

## :pencil2: 3、使用场景

### :pen\_fountain: 3.1、自定义排序规则

std::sort()函数的排序规则和[关联式容器的排序规则](../stl-basics/set-map.md#zi-ding-yi-guan-lian-shi-rong-qi-de-pai-xu-gui-ze)都是可以自定义的，一种方式就是使用函数对象。如：

```cpp
//以普通函数的方式实现自定义排序规则
bool greater_comp(int i, int j) {
    return (i < j);
}
//以函数对象的方式实现自定义排序规则
class Less {
public:
    bool operator() (int i, int j) {
        return (i > j);
    }
};

int main() {
    std::vector<int> data{ 32, 71, 12, 45, 26, 80, 53, 33 };
    //对 32、71、12、45 进行排序
    std::sort(data.begin(), data.begin() + 4); //(12 32 45 71) 26 80 53 33
    for (std::vector<int>::iterator it = data.begin(); it != data.end(); ++it) {
        std::cout << *it << ' ';
    }
    std::cout << std::endl;

    //利用STL标准库提供的其它比较规则（比如 greater<T>）进行排序
    std::sort(data.begin(), data.begin() + 4, std::greater<int>()); //(71 45 32 12) 26 80 53 33
    for (std::vector<int>::iterator it = data.begin(); it != data.end(); ++it) {
        std::cout << *it << ' ';
    }
    std::cout << std::endl;

    //通过自定义比较规则进行排序
    std::sort(data.begin(), data.end(), greater_comp);//12 26 32 33 45 53 71 80
    for (std::vector<int>::iterator it = data.begin(); it != data.end(); ++it) {
        std::cout << *it << ' ';
    }
    std::cout << std::endl;

    //通过自定义比较规则进行排序
    Less less;
    std::sort(data.begin(), data.end(), less);
    for (std::vector<int>::iterator it = data.begin(); it != data.end(); ++it) {
        std::cout << *it << ' ';
    }

    return 0;
}
// 输出
12 32 45 71 26 80 53 33
71 45 32 12 26 80 53 33
12 26 32 33 45 53 71 80
80 71 53 45 33 32 26 12
```

### :pen\_fountain: 3.2、**谓词函数**&#x20;

谓词函数是一个返回布尔值的函数。

**一元谓词函数**就是只釆用一个实参的函数。使用一元谓词函数可以确定一个给定对象是否具有某些特征。

```cpp
bool isEven(int i) {
    return ((i % 2) == 1);
}

class IsEven
{
public:
    bool operator()(int x)
    {
        return x % 2 == 0;
    }
}
```

**二元谓词函数**就是釆用两个形参的谓词函数。使用二元谓词函数可以确定两个对象是否以某种方式相关联。

```cpp
bool lessThan(int a, int b)
{
    return a < b;
}

class LessThan
{
public:
    bool operator()(int a, int b)
    {
        return a < b;
    }
}
```

在调用用到函数对象的标准库算法时，除非显式地指定模板类型为传引用，否则默认情况下函数对象是按值传递的，因此，如果传递一个具有内部状态的函数对象，则被改变状态的是函数内部被复制的临时对象，函数结束后随之消失。真正传进来的函数对象状态并为改变。

```cpp
class B
{
public:
    B(int n = 0) : th(n), count(1) {}
    bool operator() (int)
    {
        return count++ == th;
    }

    int getCount() const
    {
        return count;
    }
private:
    int th;
    int count;
};

int main() {
    vector<int> vec({2, 4, 5, 7, 8, 6, 9});
    B b(3);
    vector<int>::iterator iter = find_if(vec.begin(), vec.end(), b); //调用函数对象，查找第三个数字  
    cout << "3rd:" << *iter << endl;
    cout << "State:" << b.getCount() << endl;  //指向函数对象内容，但内部值却为改变
    return 0;
}
```

**原则：**

不是所有的返回布尔值的函数对象都适合作为谓词函数，因此用作谓词函数的函数对象，最好不要依赖其内部状态的改变。
