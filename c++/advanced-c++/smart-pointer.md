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

## ✏ 2、unique\_ptr



