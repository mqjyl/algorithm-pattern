# 面向对象之封装

## :pencil2: 1、面向对象编程

C++ 在 C 语言的基础上增加了面向对象编程（`OOP`），C++ 支持面向对象程序设计。类是 C++ 的核心特性，通常被称为用户定义的类型。

### :pen\_fountain: 1.1、类和对象

类用于**指定对象的形式**，它包含了**数据表示法**和**用于处理数据的方法**。**类中的数据和方法称为类的成员**。**函数在一个类被称为类的成员**。 **对象是根据类来创建的，声明类的对象，就像声明基本类型的变量一样**。

### :pen\_fountain: 1.2、三大特性

*   封装性：数据和代码捆绑在一起，避免外界干扰和不确定性访问，封装可以使得代码模块化。

    优点：

    * 确保用户代码不会无意间破坏封装对象的状态
    * 被封装的类的具体实现细节可以随时改变，而无须调整用户级别的代码
* 继承性：让某种类型对象获得另一个类型对象的属性和方法，继承可以扩展已存在的代码。
* 多态性：同一事物表现出不同事物的能力，即向不同对象发送同一消息，不同的对象在接收时会产生不同的行为（重载实现编译时多态，虚函数实现运行时多态），多态的目的则是为了接口重用。

### :pen\_fountain: 1.3、`OOP`的五大设计原则

1. **依赖倒置原则：**&#x4E0A;层模块不应该依赖于下层模块，他们应该共同依赖于一个抽象。细节依赖于抽象，抽象不依赖于细节。常用实现方式：在代码中使用抽象类，而将具体类放在配置文件中。
2. **开放-封闭原则：**&#x4E00;个软件或软件模块应该对功能的扩展是开放的，是支持的，但是对于已经写好的部分的修改应该是封闭的，是尽可能少的。比如一个软件，原本只能解析`xml`格式文件，现在需要添加解析`json`格式文件的功能，这时不应该对原有的解析`xml`文件模块进行大规模修改，而可以添加解析`json`格式文件的功能，这就需要在设计之初将解析流程中公共的部分抽象出来，对抽象编程而不是对具体编程。
3. **单一职责原则：**&#x5355;一职责原则有两层含义，一是一个类不应该承担太多职责，二是相同的职责不应该分给多个不同的类，总的来说就是需要职责明细。
4. **接口隔离原则：**&#x5BA2;户端不应该依赖于那些它不需要的接口，即一个类的肥胖接口需要用多个接口替代之，这样客户端对该类的依赖性就建立在了最少的接口上。
5. **里氏替换原则：**&#x5B50;类应该可以在任何场景下替换父类。一般就是在子类中实现或者重写所有父类的方法。

### :pen\_fountain: 1.4、基本概念

| 概念          | 描述                                                        |
| ----------- | --------------------------------------------------------- |
| 类成员函数       | 类的成员函数是指那些把定义和原型写在类定义内部的函数，就像类定义中的其他变量一样。                 |
| 类访问修饰符      | 类成员可以被定义为 public、private 或 protected。默认情况下是定义为 private。   |
| 构造函数 & 析构函数 | 类的构造函数是一种特殊的函数，在创建一个新的对象时调用。类的析构函数也是一种特殊的函数，在删除所创建的对象时调用。 |
| 拷贝构造函数      | 拷贝构造函数，是一种特殊的构造函数，它在创建对象时，是使用同一类中之前创建的对象来初始化新创建的对象。       |
| 友元函数        | 友元函数可以访问类的 private 和 protected 成员。                        |
| 内联函数        | 通过内联函数，编译器试图在调用函数的地方扩展函数体中的代码。                            |
| this指针      | 每个对象都有一个特殊的指针 this，它指向对象本身。                               |
| 指向类对象的指针    | 指向类的指针方式如同指向结构的指针。实际上，类可以看成是一个带有函数的结构。                    |
| 类的静态成员      | 类的数据成员和函数成员都可以被声明为静态的。                                    |
| 类型转换函数      | 转换构造函数可以将普通的基础类型转换为当前的类类型，也可以将其它类类型的对象转换为当前的类类型。          |
| 运算符重载       |                                                           |

## :pencil2: 2、[构造函数 & 析构函数](constructor-destructor.md)

> 这部分内容太多了，所以另起一页。

## :pencil2: 3、类访问修饰符

数据封装是面向对象编程的一个重要特点，它防止函数直接访问类类型的内部成员。类成员的访问限制是通过在类主体内部对各个区域标记 **public、private、protected** 来指定的。关键字 **public、private、protected** 称为访问修饰符。

一个类可以有多个 public、protected 或 private 标记区域。每个标记区域在下一个标记区域开始之前或者在遇到类主体结束右括号之前都是有效的。成员和类的默认访问修饰符是 private。

1. &#x20;**公有**成员在程序中类的外部是可访问的。
2. &#x20;**私有**成员变量或函数在类的外部是不可访问的，甚至是不可查看的。只有类和友元函数可以访问私有成员。
3. &#x20;**保护**成员变量或函数与私有成员十分相似，但有一点不同，保护成员在派生类（即子类）中是可访问的。

## :pencil2: 4、类型转换函数

转换构造函数能够将其它类型转换为当前类类型（例如将double类型转换为complex类型），但是不能反过来将当前类类型转换为其它类型（例如将complex类型转换为double 类型），C++提供了类型转换函数（Type conversion function）来解决这个问题。**类型转换函数的作用就是将当前类类型转换为其它类型，它只能以成员函数的形式出现，也就是只能出现在类中**。类型转换函数和运算符的重载非常相似，都使用 operator 关键字，因此也把类型转换函数称为类型转换运算符。\
\
&#x20;类型转换函数的语法格式为：

```cpp
operator type(){
    //TODO:
    return data;
}
```

&#x20;operator 是 C++ 关键字，type 是要转换的目标类型，data 是要返回的 type 类型的数据。\
\
因为要转换的目标类型是 type，所以返回值 data 也必须是 type 类型。类型转换函数看起来没有返回值类型，其实是隐式地指明了返回值类型。类型转换函数也没有参数，因为要将当前类的对象转换为其它类型，所以参数不言而喻。实际上编译器会把当前对象的地址赋值给 this 指针，这样在函数体内就可以操作当前对象了。

```cpp
//复数类
class Complex {
public:
    Complex() : m_real(0.0), m_imag(0.0) { }
    Complex(double real, double imag) : m_real(real), m_imag(imag) { }
public:
    friend ostream& operator<<(ostream& out, Complex& c);
    friend Complex operator+(const Complex& c1, const Complex& c2);
    operator double() const { return m_real; }  //类型转换函数
private:
    double m_real;  //实部
    double m_imag;  //虚部
};

//重载>>运算符
ostream& operator<<(ostream& out, Complex& c) {
    out << c.m_real << " + " << c.m_imag << "i";;
    return out;
}
//重载+运算符
Complex operator+(const Complex& c1, const Complex& c2) {
    Complex c;
    c.m_real = c1.m_real + c2.m_real;
    c.m_imag = c1.m_imag + c2.m_imag;
    return c;
}

int main() {
    Complex c1(24.6, 100);
    double f = c1;  //相当于 double f = Complex::operator double(&c1);
    cout << "f = " << f << endl;

    f = 12.5 + c1 + 6;  //相当于 f = 12.5 + Complex::operator double(&c1) + 6;
    cout << "f = " << f << endl;

    int n = Complex(43.2, 9.3);  //先转换为 double，再转换为 int
    cout << "n = " << n << endl;

    return 0;
}

// 输出
f = 24.6
f = 43.1
n = 43
```

### :pen\_fountain: 4.1、说明

1. type 可以是内置类型、类类型以及由 `typedef` 定义的类型别名，任何可作为函数返回类型的类型（void 除外）都能够被支持。一般而言，不允许转换为数组或函数类型，转换为指针类型或引用类型是可以的。
2. 类型转换函数一般不会更改被转换的对象，所以通常被定义为 const 成员。
3. 类型转换函数可以被继承，可以是虚函数。
4. 一个类虽然可以有多个类型转换函数（类似于函数重载），但是如果多个类型转换函数要转换的目标类型本身又可以相互转换（类型相近），那么有时候就会产生二义性。以 Complex 类为例，假设它有两个类型转换函数：

```cpp
operator double() const { return m_real; }    //转换为double类型
operator int() const { return (int)m_real; }  //转换为int类型
```

那么下面的写法就会引发二义性：

```cpp
Complex c1(24.6, 100);
float f = 12.5 + c1;
```

编译器可以调用 operator double() 将 c1 转换为 double 类型，也可以调用 operator int() 将 c1 转换为 int 类型，这两种类型都可以跟 12.5 进行加法运算，并且从 Complex 转换为 double 与从 Complex 转化为 int 是平级的，没有谁的优先级更高，所以这个时候编译器就不知道该调用哪个函数了，干脆抛出一个二义性错误，让用户解决。

## :pencil2: 5、类的指针和this指针

### :pen\_fountain: 5.1、类对象和类指针

**类的指针：**&#x4ED6;是一个内存地址值，他指向内存中存放的类对象(包括一些成员变量所赋的值)。 \
**对象：**&#x4ED6;是利用类的构造函数在内存中分配一块内存(包括一些成员变量所赋的值)。&#x20;

指针变量是间接访问，但可实现多态（**通过父类指针可调用子类对象**），并且没有调用构造函数。 **类指针调用成员函数，根据的不是指针所指向对象而是根据指针所属的类型，调用相关的函数。** \
类对象直接声明可直接访问，但不能实现多态，声明即调用了构造函数（已分配了内存）。&#x20;

类的对象：用的是**内存栈，**&#x662F;个局部的临时变量。  \
类的指针：用的是**内存堆，**&#x662F;个永久变量，需要手动释放。&#x20;

### :pen\_fountain: 5.2、`this`指针

this 是 C++ 中的一个关键字，也是一个 const 指针，它指向当前对象，通过它可以访问当前对象的所有成员。如：

```cpp
class Complex {
public:
    Complex() : real(0.0), imag(0.0) { }
    Complex(double real, double imag)
    { 
        this->setVal(real, imag);
    }
public:
    void setVal(double real, double imag)
    {
        this->real = real;
        this->imag = imag;
    }
private:
    double real;  //实部
    double imag;  //虚部
};
```

&#x20;this 是一个指针，要用`->`来访问成员变量或成员函数。this 虽然用在类的内部，但是只有在对象被创建以后才会给 this 赋值，并且这个赋值的过程是编译器自动完成的，不需要用户干预，用户也不能显式地给 this 赋值。\
\
给 Student 类添加一个成员函数`printThis()`，专门用来输出 this 的值，如下所示：

```cpp
void printThis()
{
    cout << this << endl;
}

int main()
{
    Complex *complex = new Complex();
    complex->printThis();
    cout << complex << endl;
    return 0;
}

// 输出
00955BC0
00955BC0
```

&#x20;几点注意：

* this 是 const 指针，它的值是不能被修改的，一切企图修改该指针的操作，如赋值、递增、递减等都是不允许的。
* this 只能在成员函数内部使用，用在其他地方没有意义，也是非法的。
* 只有当对象被创建后 this 才有意义，因此不能在 static 成员函数中使用（后续会讲到 static 成员）。

#### this的本质：

&#x20;this 实际上是成员函数的一个形参（第一个形参），在调用成员函数时将对象的地址作为实参传递给 this。不过 this 这个形参是隐式的，它并不出现在代码中，而是在编译阶段由编译器默默地将它添加到参数列表中。\
\
this 作为隐式形参，本质上是成员函数的局部变量，所以只能用在成员函数的内部，并且只有在通过对象调用成员函数时才给 this 赋值。\
\
对象的内存模型中只保留了成员变量，除此之外没有任何其他信息，成员函数最终被编译成与对象无关的普通函数，会丢失所有信息（包括成员变量），所以编译时要在成员函数中添加一个额外的参数，把当前对象的首地址传入，以此来关联成员函数和成员变量。这个额外的参数，实际上就是 this，它是成员函数和成员变量关联的桥梁。

## :pencil2: 6、友元函数和友元类



## :pencil2: 7、类的静态成员



## :pencil2: 8、封装的例子

在这里先给出一个封装好的学生类：

```cpp
```
