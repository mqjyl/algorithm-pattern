# 强制类型转换

C语言中的强制类型转换（Type Cast）有显式和隐式两种，显式一般就是直接用小括号强制转换：Type\(Value\)或\(Type\)Value。隐式转换存在隐式截断的问题。

C++对C兼容，所以上述方式的类型转换是可以的，但是有时候会有问题，所以C++提供了四个强制类型转换的关键字：static\_cast，const\_cast，reinterpret\_cast 和 dynamic\_cast。

## ✏ 1、static\_cast

用法为 **static\_cast&lt;type-id&gt; \(expression\)**。该运算符把 expression 转换为 type-id 类型，但没有运行时类型检查来保证转换的安全性。 如果在 Visual Studio 中编写，且安装了**`Resharper C++`** 的话，如果按照 C 风格写的话，会提示你，并帮助你自动修改为 C++ 风格。

主要用法如下： 

1. 用于类层次结构中基类（父类）和派生类（子类）之间指针或引用的转换。 进行上行转换（把派生类的指针或引用转换成基类表示）是安全的； 进行下行转换（把基类指针或引用转换成派生类表示）时，由于没有动态类型检查，所以是不安全的。需要说明的继承必须为public。
2. 用于基本数据类型之间的转换，如把`int`转换成`char`，把`int`转换成`enum`。**这种转换的安全性也要开发人员来保证。** 
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

## ✏ 2、const\_cast

const\_cast 是C++中专用于处理与 const 相关的强制类型转换关键字，其功能为：为一个变量重新设定其const描述。即：const\_cast可以为一个变量强行增加或删除其const限定。

需要明确的是，即使用户通过const\_cast强行去除了const属性，也不代表当前变量从不可变变为了可变。const\_cast只是使得用户接管了编译器对于const限定的管理权，故用户必须遵守“不修改变量”的承诺。如果违反此承诺，编译器也不会因此而引发编译时错误，但可能引发运行时错误。

主要用法如下：

1. const\_cast可用于更改const成员函数内的非const类成员。
2. const\_cast可用于将const数据传递给不接收const的函数。
3. const\_cast&lt;&gt;里边的内容必须是引用或者指针。
4. const\_cast也可以用来抛弃`volatile`和`__unaligned`属性。

```cpp
class Student
{
private:
	int roll;
public:
	Student(int r) :roll(r) {}
	void fun() const
	{
		(const_cast<Student*> (this))->roll = 5;
	}
	int getRoll() { return roll; }
};

int main() {
    Student student(3);
    std::cout << "Old roll number: " << student.getRoll() << std::endl;
    student.fun();
    std::cout << "New roll number: " << student.getRoll() << std::endl;

    // const_cast只能调节类型限定符，不能更改基础类型
    int a1 = 40;
    //const int* b1 = &a1;
    //char* c1 = const_cast <char*> (b1); // 编译程序时出错

    const volatile int* d1 = &a1;
    std::cout << "typeid of d1 " << typeid(d1).name() << '\n'; // int const volatile *
    int* e1 = const_cast <int*> (d1);
    std::cout << "typeid of e1 " << typeid(e1).name() << '\n'; // int *
    
    return 0;
}
```

在const成员函数`fun()`中，编译器将`“this”`视为`“ const student const this”`，即`“this”`是指向常量对象的常量指针，因此编译器不允许通过以下方式更改数据成员“这个”指针。const\_cast将`“this”`指针的类型更改为`“student const this”`。

const\_cast比简单类型转换更安全。从某种意义上讲，如果强制类型与原始对象不相同，则强制转换不会发生，这是比较安全的。

## ✏ 3、**reinterpret\_cast**

reinterpret，即“重新解释”，顾名思义，这个强制类型转换的作用是提供某个变量在底层数据上的重新解释。当我们对一个变量使用`reinterpret_cast`后，编译器将无视任何不合理行为，强行将被转换变量的内存数据重解释为某个新的类型。它用于转换任何类型的另一个指针的一个指针，而不管该类是否相互关联。 它不检查指针类型和指针所指向的数据是否相同。需要注意的是，`reinterpret_cast`要求转换前后的类型所占用内存大小一致，否则将引发编译时错误。

```cpp
data_type *var_name = reinterpret_cast <data_type *>(pointer_variable);
```

使用reinterpret\_cast的目的：

1. reinterpret\_cast是一种非常特殊且危险的类型转换操作符。并且建议使用适当的数据类型使用它，即（指针数据类型应与原始数据类型相同）。
2. 它可以将任何指针类型转换为任何其他数据类型。
3. 当我们要使用位时使用它。
4. 它仅用于将任何指针转换为原始类型。
5. 布尔值将转换为整数值，即0表示false，1表示true。

```cpp
class A {
public:
    int a;
    A(int i) :a(i) {}
    void fun_a()
    {
        std::cout << "In class A\n";
    }
};

class B {
public:
    int b;
    B(int i) :b(i) {}
    void fun_b()
    {
        std::cout << "In class B\n";
    }
};

void testReinterpretCast() {
    B *x = new B(5);
    A* y = reinterpret_cast<A*>(x);
    y->fun_a();                      // In class A
    std::cout << y->a << std::endl;  // 5
}
```

## ✏ **4、dynamic\_cast**

\*\*\*\*

## ✏ **总结**

纵观C++的类型转换语法体系，其延续了C++一贯的包罗万象风格，不仅为用户提供了自定义类型转换的极大自由度，也在语法层面为类型转换可能会带来的各种错综复杂的情况作出了严谨的规定。

保守看来，如果对C++的类型转换没有深入的理解，或不希望大量使用隐式类型转换时，我们不应过度的依赖诸如非`explicit`转换构造函数，自定义的类型转换操作符，以及涉及隐式类型转换的各种重载确定等语法组分。但作为C++语法体系的一个重要部分，深入理解C++关于类型转换的各种话题，必定是十分重要的。

