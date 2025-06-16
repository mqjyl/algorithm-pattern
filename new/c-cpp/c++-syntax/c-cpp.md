# c语言到c++

## 1、Class与Struct

 C++中的 struct 和 class 基本是通用的，唯有几个细节不同：

* 使用 class 时，类中的成员默认都是 private 属性的；而使用 struct 时，结构体中的成员默认都是 public 属性的。
* class 继承默认是 private 继承，而 struct 继承默认是 public 继承。
* class 可以使用模板，而 struct 不能。

### C与C++中的struct

* C 语言中： struct 是用户自定义数据类型（ UDT）； C++中 struct 是抽象 数据类型（ ADT），支持成员函数的定义，（ C++中的 struct 能继承，能实现多态）。
* C 中 struct 是没有权限的设置的，且 struct 中只能是一些变量的集合 体，可以封装数据却不可以隐藏数据，而且成员不可以是函数。
* struct 作为类的一种特例是用来自定义数据结构的。一个结构标记声明 后，在 C 中必须在结构标记前加上 struct，才能做结构类型名。

## 2、函数重载

函数重载就是指： 在同一作用域类，一组函数的函数名相同，参数列表不同（个数不同或类型不同），返回值可同可不同。C 语言不存在函数重载， C++支持函数重载，属于静多态。

比如一个函数声明如下： `void function(int x, int y);` 

在 C 语言中，编译器进行编译之后，在库中的名字为： `_function` ，在 C++中，编译器在进行编译之后，在库中的名字为： `_function_int_int` 。

编译器在链接的阶段，都是找到相应的函数名，进行链接。 在 C 语言中，两个函数的名字一样，就会在链接时报错。 在 C++中，两个函数饿名字不相同，就不会报错。

 函数的重载的规则：

* 函数名称必须相同。
* 参数列表必须不同（个数不同、类型不同、参数排列顺序不同等）。
* 函数的返回类型可以相同也可以不相同。
* 仅仅返回类型不同不足以成为函数的重载。

## 3、NULL & `nullptr`

### `3.1`**、C程序中的NULL**

在C语言中，NULL通常被定义为：`#define NULL ((void *)0)`

所以说NULL实际上是一个空指针，如果在C语言中写入以下代码，编译是没有问题的，因为在C语言中把空指针赋给int和char指针的时候，发生了隐式类型转换，把void指针转换成了相应类型的指针。

```c
int  *pi = NULL;
char *pc = NULL;
```

### **3.2、C++程序中的NULL**

以上代码如果使用C++编译器来编译则是会出错的，因为C++是强类型语言，`void*`是不能隐式转换成其他类型的指针的，所以实际上编译器提供的头文件做了相应的处理：

```c
#ifdef __cplusplus
#define NULL 0
#else
#define NULL ((void *)0)
#endif
```

可见，在C++中，NULL实际上是0。 因为C++中不能把`void*`类型的指针隐式转换成其他类型的指针，所以为了解决空指针的表示问题，C++引入了0来表示空指针，这样就有了上述代码中的NULL宏定义。

但是实际上，用NULL代替0表示空指针在函数重载时会出现问题，程序执行的结果会与我们的想法不同，举例如下：

```c
void func(void* t)
{
	cout << "func(void*)" << endl;
}
 
void func(int i)
{
	cout << "func(int)" << endl;
}
 
int main()
{
	func(NULL);      // 输出：func(int)
	func(nullptr);   // 输出：func(void*)
	();
	return 0;
}
```

在这段代码中，我们对函数`func`进行可重载，参数分别是`void*`类型和int类型，但是运行结果却与我们使用NULL的初衷是相违背的，因为我们本来是想用NULL来代替空指针，但是在将NULL输入到函数中时，它却选择了int形参这个函数版本，所以是有问题的，这就是用NULL代替空指针在C++程序中的二义性。这就是为什么C++的书都会推荐说C++中更习惯使用0来表示空指针而不是NULL，尽管NULL在C++编译器下就是0。因为使用0来表示空指针，那就会够“明显”，因为0是空指针，它更是一个整形常量。

### **3.3、C++中的`nullptr`**

为解决NULL代指空指针存在的**二义性问题**，在C++11版本中特意引入了`nullptr`这一新的关键字来代指空指针，`nullptr`可以明确区分整型和指针类型，能够根据环境自动转换成相应的指针类型，但不会被转换为任何整型，所以不会造成参数传递错误。从上面的例子中我们可以看到，使用`nullptr`作为实参，确实选择了正确的以`void*`作为形参的函数版本。在没有C++ 11的`nullptr`的时候，我们怎么解决避免这个问题呢？`nullptr`的一种实现方式如下：

```cpp
const class nullptr_t{
public:
    template<class T>  inline operator T*() const{ return 0; }
    template<class C, class T> inline operator T C::*() const { return 0; }
private:
    void operator&() const;
} nullptr = {};
```

以上通过模板类和运算符重载的方式来对不同类型的指针进行实例化从而解决了`(void*)`指针带来参数类型不明的问题，另外由于`nullptr`是明确的指针类型，所以不会与整形变量相混淆。但`nullptr`仍然存在一定问题，例如：

```cpp
void fun(char* p); 
void fun(int* p);
```

在这种情况下存在对不同指针类型的函数重载，此时如果传入`nullptr`指针则仍然存在无法区分应实际调用哪个函数，这种情况下必须显示的指明参数类型。

