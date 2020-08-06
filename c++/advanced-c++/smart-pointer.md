# 智能指针

智能指针主要用于管理在堆上分配的内存，它将普通的指针封装为一个栈对象。当栈对象的生存周期结束后，会在析构函数中释放掉申请的内存，从而防止内存泄漏。C++里面的四个智能指针: `auto_ptr`，`unique_ptr`，`shared_ptr`，`weak_ptr` 其中后三个是C++11支持，并且第一个已经被C++11弃用。

智能指针的作用是管理一个指针，因为存在以下这种情况：申请的空间在函数结束时忘记释放，造成内存泄漏。使用智能指针可以很大程度上的避免这个问题，因为智能指针就是一个类，当超出了类的作用域是，类会自动调用析构函数，析构函数会自动释放资源。所以智能指针的作用原理就是在函数结束时自动释放内存空间，不需要手动释放内存空间。

## ✏ 1、`auto_ptr`

`auto_ptr` 是C++标准库（C++98的方案，C++11已经抛弃）提供的类模板，`auto_ptr`对象通过初始化指向由`new`创建的动态内存，它是这块内存的拥有者，一块内存不能同时被分给两个拥有者。当`auto_ptr`对象生命周期结束时，其析构函数会将`auto_ptr`对象拥有的动态内存自动释放。即使发生异常，通过异常的栈展开过程也能将动态内存释放。`auto_ptr`不支持 new 数组。

需要包含的头文件：`#include <memory>`。

### 🖋 1.1、初始化`auto_ptr`对象

```cpp
int* p = new int(33);
auto_ptr<int> api(p);  //1.将已存在的指向动态内存的普通指针作为参数来构造

auto_ptr< int > api( new int( 33 ) ); //2.直接构造智能指针

auto_ptr< string > pstr_auto( new string( "Brontosaurus" ) );
auto_ptr< string > pstr_auto2( pstr_auto );  //3.拷贝构造：利用pstr_auto来构造pstr_auto2
```

### 🖋 1.2、赋值

利用已经存在的智能指针来构造新的智能指针

```cpp
auto_ptr< int > p1( new int( 1024 ) );
auto_ptr< int > p2( new int( 2048 ) );
p1 = p2;
```

在赋值之前，由p1 指向的对象被删除。赋值之后，p1 拥有 int 型对象的所有权， p2 不再被用来指向该对象，当程序运行时访问p2将会报错。所以`auto_ptr`的缺点是：存在潜在的内存崩溃问题！

> 空的 `auto_ptr`需要初始化吗？
>
> 通常的指针在定义的时候若不指向任何对象，我们用`NULL`给其赋值。对于智能指针，因为构造函数有默认值0，我们可以直接定义空的`auto_ptr`如下：`auto_ptr<int> p_auto_int; //不指向任何对象`

### 🖋 1.3、特性

#### 🐹 1.3.1、防止两个`auto_ptr`对象拥有同一个对象（一块内存）

因为`auto_ptr`的所有权独有，所以下面的代码会造成混乱：

```cpp
int* p = new int(0);
auto_ptr<int> p1(p);
auto_ptr<int> p2(p);
```

因为p1与p2都认为指针p是归它管的，在析构时都试图删除p, 两次删除同一个对象的行为在C++标准中是未定义的。所以我们必须防止这样使用`auto_ptr`。

#### 🐹 1.3.2、警惕智能指针作为参数

1、按值传递时，函数调用过程中在函数的作用域中会产生一个局部对象来接收传入的`auto_ptr`\(拷贝构造\)，这样，传入的实参`auto_ptr`就失去了其对原对象的所有权，而该对象会在函数退出时被局部`auto_ptr`删除。如下例：

```cpp
void f(auto_ptr ap){cout<<*ap;}
auto_ptr ap1(new int(0));
f(ap1);
cout<<*ap1; //错误，经过f(ap1)函数调用，ap1已经不再拥有任何对象了。
```

2、当参数为`auto_ptr`类型的引用或指针时，不会存在上面的拷贝过程。但我们并不知道在函数中对传入的`auto_ptr`做了什么，如果当中某些操作使其失去了对对象的所有权，那么这还是可能会导致致命的执行期错误。

**结论：const reference是智能指针作为参数传递的底线。**

#### \*\*\*\*🐹 **1.3.3、**auto\_ptr不能初始化为指向非动态内存

原因很简单，delete 表达式会被应用在不是动态分配的指针上这将导致未定义的程序行为。

### 🖋 1.4、常用的成员函数

* **get\(\)：**返回`auto_ptr`指向的那个对象的内存地址；
* **reset\(\)：**重新设置`auto_ptr`指向的对象。类似于赋值操作，但赋值操作不允许将一个普通指针指直接赋给`auto_ptr`，而`reset()`允许。

```cpp
auto_ptr< string > pstr_auto( new string( “Brontosaurus” ) );
pstr_auto.reset( new string( “Long -neck” ) );
```

重置前`pstr_auto`拥有"Brontosaurus"字符内存的所有权，这块内存首先会被释放。之后`pstr_auto`再拥有"Long -neck"字符内存的所有权。

**注：reset\(0\)可以释放对象，销毁内存。**

* **release\(\)：**返回`auto_ptr`指向的那个对象的内存地址，并释放对这个对象的所有权。用此函数初始化`auto_ptr`时可以避免两个`auto_ptr`对象拥有同一个对象的情况（与get函数相比）。

```cpp
auto_ptr< string > pstr_auto( new string( “Brontosaurus” ) );
auto_ptr< string > pstr_auto2( pstr_auto.get() ); //这是两个auto_ptr拥有同一个对象
auto_ptr< string > pstr_auto2( pstr_auto.release() ); //release可以首先释放所有权
```

## ✏ 2、`unique_ptr`

`unique_ptr` 是 `auto_ptr` 的升级版，`unique_ptr`实现独占式拥有或严格拥有概念，保证同一时间内只有一个智能指针可以指向该对象。它对于避免资源泄露\(例如“以new创建对象后因为发生异常而忘记调用delete”\)特别有用。`unique_ptr`定义在`<memory>`头文件中：

`std::unique_ptr` 有两个版本：

1. 管理个对象（例如以 new 分配）
2. 管理动态分配的对象数组（例如以 new\[\] 分配）

```cpp
template <class T, class D = default_delete<T>> class unique_ptr;
template <class T, class D> class unique_ptr<T[], D>;
```

**1） 任意时刻`unique_ptr`只能指向某一个对象，指针销毁时，指向的对象也会被删除（通过内置删除器，通过调用析构函数实现删除对象）**

**2）禁止拷贝和赋值（底层实现拷贝构造函数和复制构造函数 = delete），可以使用`std::move()`、`unique_ptr.reset(...)` 转移对象指针控制权。**

### 🖋 **2.1、构造函数表**

类满足可移动构造 \(Move ****Constructible\) 和可移动赋值 \(Move Assignable\) 的要求，但不满足可复制构造 \(Copy Constructible\) 或可复制赋值 \(Copy Assignable\) 的要求。

```cpp
// default (1)	
constexpr unique_ptr() noexcept;
// from null pointer (2)	
constexpr unique_ptr (nullptr_t) noexcept : unique_ptr() {}
// from pointer (3)	
explicit unique_ptr (pointer p) noexcept;
// from pointer + lvalue deleter (4)	
unique_ptr (pointer p,
    typename conditional<is_reference<D>::value,D,const D&> del) noexcept;
// from pointer + rvalue deleter (5)	
unique_ptr (pointer p,
    typename remove_reference<D>::type&& del) noexcept;
// move (6)	
unique_ptr (unique_ptr&& x) noexcept;
// move-cast (7)	
template <class U, class E>
  unique_ptr (unique_ptr<U,E>&& x) noexcept;
// move from auto_ptr (8)	
template <class U>
  unique_ptr (auto_ptr<U>&& x) noexcept;
// 拷贝构造 copy (deleted!) (9)
unique_ptr (const unique_ptr&)= delete;
// 复制作业 (deleted!) (10)
unique_ptr＆operator = (const unique_ptr&)= delete;
```

在下列两者之一发生时用关联的删除器释放对象：

* 销毁了管理的 `unique_ptr` 对象
* 通过 operator= 或 reset\(\) 赋值另一指针给管理的 `unique_ptr` 对象。

通过调用 `get_deleter()(ptr)` ，用潜在为用户提供的删除器释放对象。默认删除器用 delete 运算符，它销毁对象并解分配内存。`unique_ptr` 亦可以不占有对象，该情况下称它为_空_ \(empty\)。

### 🖋 2.2、`auto_ptr`与`unique_ptr`

1、`auto_ptr`有拷贝语义，拷贝后源对象变得无效，这可能引发很严重的问题；而`unique_ptr`则无拷贝语义，但提供了移动语义，这样的错误不再可能发生，因为很明显必须使用std::move\(\)进行转移。

```cpp
class A
{
public:
    string id;
    A(string id) :id(id)
    {
        cout << id << "：构造函数" << endl;
    }
    ~A()
    {
        cout << id << "：析构函数" << endl;
    }
};

int main()
{
    auto_ptr<A> auto_ap(new A("auto_ptr")), auto_bp;
    cout << auto_ap.get() << endl;
    auto_bp = auto_ap;
    cout << auto_bp.get() << endl;

    cout << auto_ap.get() << endl;

    unique_ptr<A> unique_ap(new A("unique_ptr")), unique_bp;
    cout << unique_ap.get() << endl;
    // unique_bp = unique_ap;  // 报错
    unique_bp = move(unique_ap);
    cout << auto_bp.get() << endl;
    
    cout << unique_ap.get() << endl;
    
    unique_ap = unique_ptr<A>(new A("unique_ptr_new"));
    cout << unique_ap.get() << endl;
    return 0;
}
// 输出
auto_ptr：构造函数
00CEFD48
00CEFD48
00000000
unique_ptr：构造函数
00CEFE20
00CEFD48
00000000
unique_ptr_new：构造函数
00CEFF40
unique_ptr：析构函数
unique_ptr_new：析构函数
auto_ptr：析构函数
```

`unique_ptr`聪明的地方在于：当程序试图将一个 `unique_ptr` 赋值给另一个时，如果源 `unique_ptr` 是个临时右值，编译器允许这么做；如果源 `unique_ptr` 将存在一段时间，编译器将禁止这么做（如32行）。

2、`auto_ptr`不可作为容器元素，`unique_ptr`可以作为容器元素。因为`auto_ptr`的拷贝和赋值具有破坏性，不满足容器要求：拷贝或赋值后，两个对象必须具有相同值。

3、`auto_ptr`不可指向动态数组，`unique_ptr`可以指向动态数组。因为`unique_ptr`有`unique_ptr<T[]>`重载版本，销毁动态对象时调用delete\[\]。

```cpp
class A
{
public:
    string id;
    A(string id) :id(id)
    {
        cout << id << "：构造函数" << endl;
    }
    ~A()
    {
        cout << id << "：析构函数" << endl;
    }
    void print() {
        cout << "Hello, " << id << endl;
    }
};

int main()
{
    unique_ptr<A[]> unique_ap_array(new A[2]{ A("unique_ptr1"), A("unique_ptr2") });
    cout << unique_ap_array.get() << endl;
    cout << unique_ap_array.get() + 1 << endl;
    unique_ap_array[0].print();
    unique_ap_array.get()->print();
    (unique_ap_array.get() + 1)->print();
    return 0;
}
// 输出
unique_ptr1：构造函数
unique_ptr2：构造函数
0165EA04
0165EA20
Hello, unique_ptr1
Hello, unique_ptr1
Hello, unique_ptr2
unique_ptr2：析构函数
unique_ptr1：析构函数
```

4、`auto_ptr`不可以自定义删除器`deleter`，而`unique_ptr`可以。

```cpp
int main()
{
    unique_ptr<A, void(*)(A*)> unique_ap_1(
        new A[2]
        {
            A("unique_ptr1"),A("unique_ptr2")
        },
        [](A* a)
        {
            delete[]a;
        });

    unique_ptr<A[]> unique_ap_2(new A[2]{ A("unique_ptr1"), A("unique_ptr2") });
    return 0;
}
// 输出
unique_ptr1：构造函数
unique_ptr2：构造函数
unique_ptr1：构造函数
unique_ptr2：构造函数
unique_ptr2：析构函数
unique_ptr1：析构函数
unique_ptr2：析构函数
unique_ptr1：析构函数
```

## ✏ 3、`shared_ptr`

## ✏ 4、 **`week_ptr`**

`share_ptr`虽然已经很好用了，但是有一点`share_ptr`智能指针还是有内存泄露的情况，当两个对象相互使用一个`shared_ptr`成员变量指向对方，会造成循环引用，使引用计数失效，从而导致内存泄漏。

`weak_ptr` 是一种不控制对象生命周期的智能指针，它指向一个 `shared_ptr` 管理的对象。进行该对象的内存管理的是那个强引用的`shared_ptr`，`weak_ptr`只是提供了对管理对象的一个访问手段。weak\_ptr 设计的目的是为配合 shared\_ptr 而引入的一种智能指针来协助 shared\_ptr 工作，它只可以从一个 shared\_ptr 或另一个 weak\_ptr 对象构造，它的构造和析构不会引起引用记数的增加或减少。weak\_ptr是用来解决shared\_ptr相互引用时的死锁问题，如果说两个shared\_ptr相互引用，那么这两个指针的引用计数永远不可能下降为0，资源永远不会释放。它是对对象的一种弱引用，不会增加对象的引用计数，和shared\_ptr之间可以相互转化，shared\_ptr可以直接赋值给它，它可以通过调用lock函数来获得shared\_ptr。

## ✏ **5、**make\_unique与make\_shared

### 🖋 5.1、make\_unique

std::make\_unique 和 std::make\_unique\_for\_overwrite  定义于头文件 `<memory>`， 构造 `T` 类型对象并将其包装进 `std::unique_ptr` 。

```cpp
// (1)(C++14 起)(仅对非数组类型)
template< class T, class... Args >
    unique_ptr<T> make_unique( Args&&... args );
// (2)(C++14 起)(仅对未知边界数组)
template< class T >
    unique_ptr<T> make_unique( std::size_t size );
// (3)(C++14 起)(仅对已知边界数组)
template< class T, class... Args >
/* unspecified */ make_unique( Args&&... args ) = delete;

// (4)(C++20 起)(仅对非数组类型)
template< class T  >
    unique_ptr<T> make_unique_for_overwrite( );
// (5)(C++20 起)(仅对未知边界数组)
template< class T >
    unique_ptr<T> make_unique_for_overwrite( std::size_t size );
// (6)(C++20 起)(仅对已知边界数组)
template< class T, class... Args >
/* unspecified */ make_unique_for_overwrite( Args&&... args ) = delete;
```

1\) 构造非数组类型 `T` 对象。传递参数 `args` 给 `T` 的构造函数。此重载仅若 `T` 不是数组类型才参与重载决议。函数等价于：

```cpp
unique_ptr<T>(new T(std::forward<Args>(args)...))
```

2\) 构造未知边界的 `T` 数组。此重载仅若 `T` 是未知边界数组才参与重载决议。函数等价于：

```cpp
unique_ptr<T>(new typename std::remove_extent<T>::type[size]())
```

3,6\) 不允许构造已知边界的数组。4\) 同 \(1\) ，除了默认初始化对象。此重载仅若 `T` 不是数组类型才参与重载决议。函数等价于：

```cpp
unique_ptr<T>(new T)
```

5\) 同 \(2\) ，除了默认初始化数组。此重载仅若 `T` 是未知边界数组才参与重载决议。函数等价于：

```cpp
unique_ptr<T>(new typename std::remove_extent<T>::type[size])
```

