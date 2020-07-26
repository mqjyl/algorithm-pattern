# 强制类型转换

C语言中的强制类型转换（Type Cast）有显式和隐式两种，显式一般就是直接用小括号强制转换：Type\(Value\)或\(Type\)Value。隐式转换存在隐式截断的问题。

C++对C兼容，所以上述方式的类型转换是可以的，但是有时候会有问题，所以C++提供了四个强制类型转换的关键字：static\_cast，const\_cast，reinterpret\_cast 和 dynamic\_cast。

## ✏ static\_cast

用法为 **static\_cast&lt;type-id&gt; \(expression\)**。该运算符把 expression 转换为 type-id 类型，但没有运行时类型检查来保证转换的安全性。 如果在 Visual Studio 中编写，且安装了**`Resharper C++`** 的话，如果按照 C 风格写的话，会提示你，并帮助你自动修改为 C++ 风格。

主要用法如下： 

1. 用于类层次结构中基类（父类）和派生类（子类）之间指针或引用的转换。 进行上行转换（把派生类的指针或引用转换成基类表示）是安全的； 进行下行转换（把基类指针或引用转换成派生类表示）时，由于没有动态类型检查，所以是不安全的。需要说明的继承必须为public。
2. 用于基本数据类型之间的转换，如把int转换成char，把int转换成`enum`。**这种转换的安全性也要开发人员来保证。** 
3. 把空指针转换成目标类型的空指针。
4. 把任何类型的表达式转换成void类型。
5. 用于非多态类型的转换。

需要注意的是，static\_cast 不能转换掉 `expression` 的 `const`、`volitale` 或者 `__unaligned` 属性。

```cpp
char c = 'a';
int* q = (int*) &c;
int *p = static_cast<int *>(&c); //编译错误

```

在c++ primer 中说道：c++ 的任何的隐式转换都是使用 static\_cast 来实现。

## ✏ const\_cast

const\_cast 是C++中专用于处理与 const 相关的强制类型转换关键字，其功能为：为一个变量重新设定其const描述。即：const\_cast可以为一个变量强行增加或删除其const限定。

需要明确的是，即使用户通过const\_cast强行去除了const属性，也不代表当前变量从不可变变为了可变。const\_cast只是使得用户接管了编译器对于const限定的管理权，故用户必须遵守“不修改变量”的承诺。如果违反此承诺，编译器也不会因此而引发编译时错误，但可能引发运行时错误。

主要用法如下：

* const\_cast可用于更改const成员函数内的非const类成员。

