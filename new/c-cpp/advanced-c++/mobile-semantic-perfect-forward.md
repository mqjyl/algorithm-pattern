# 移动语义 & 完美转发

&#x20;c++中引入了`右值引用`和`移动语义`，可以避免无谓的复制，提高程序性能。

## :pencil2: 1、左值 & 右值

`C++`中所有的值都必然属于左值、右值二者之一。左值是指表达式结束后依然存在&#x7684;_**持久化对象**_，右值是指表达式结束时就不再存在&#x7684;_**临时对象**_。所有的具名变量或者对象都是左值，而右值不具名。很难得到左值和右值的真正定义，但是有一个可以区分左值和右值的便捷方法：**看能不能对表达式取地址，如果能，则为左值，否则为右值**。

书上又将右值分为将亡值和纯右值。纯右值就是`c++98`标准中右值的概念，如非引用返回的函数返回的临时变量值；一些运算表达式，如`1+2`产生的临时变量；不跟对象关联的字面量值，如2，'c'，true，"hello"；这些值都不能够被取地址。

而将亡值则是`c++11`新增的和右值引用相关的表达式，这样的表达式通常时将要移动的对象、`T&&`函数返回值、`std::move()`函数的返回值等。

categories来说，c++11前，只有左值(`lvalue`)和右值(`rvalue`)；c++11后，任何`value categories`(值类型)都是下面的三种之一：`lvalue`， `xvalue`， `prvalue`。`gvalue`为广义左值，包括`lvalue`和`xvalue`，`rvalue`为右值，包括`xvalue`和`prvalue`，可见`xvalue`可以是左值也可以是右值。

**纯右值(`prvalue`)：纯右值是传统右值的一部分，纯右值是表达式产生的中间值，不能取地址。 纯右值一定会在表达式结束后被销毁。**

一般满足下列条件：

1. 本身就是赤裸裸的、纯粹的字面值，如3、false；
2. 求值结果相当于字面值或是一个不具名的临时对象。

**消亡值(`xvalue`)是c++11的不具名的右值引用引入的**。 以下情况的表达式求值结果为`xvalue`，准确的说叫不具名右值引用，这种属于新的”右值”，由右值引用带来，通常用来完成**移动构造**或**移动赋值**的特殊任务。事实上，将亡值不过是C++11提出的一块晦涩的语法糖。它与纯右值在功能上及其相似，如都不能做操作符的左操作数，都可以使用移动构造函数和移动赋值运算符。当一个纯右值来完成移动构造或移动赋值任务时，其实它也具有“将亡”的特点。一般，我们不必刻意区分一个右值到底是纯右值还是将亡值。

## :pencil2: 2、左值引用 & 右值引用

`c++98`中的引用很常见了，就是给变量取了个别名，在`c++11`中，因为增加了**右值引用(`rvalue reference`)**&#x7684;概念，所以`c++98`中的引用都称为了**左值引用(`lvalue reference`)**。 `c++11`中的右值引用使用的符号是`&&`，如：

```cpp
class A {
public:
    int a;
};
A getTemp()
{
    return A();
}

int main()
{
    int a = 10;
    int& refA = a;     // 左值引用
    // int& ref2 = 2;  // 编译错误

    int&& ref1 = 1;    // 右值引用

    int b = 5;
    // int&& refB = b; // 编译错误，不能将一个左值复制给一个右值引用

    A&& refIns = getTemp(); // 函数返回值是 右值
    
    return 0;
}
```

`getTemp()`返回的右值本来在表达式语句结束后，其生命也就该终结了（因为是临时变量），而通过右值引用，该右值又重获新生，其生命期将与右值引用类型变量`refIns`的生命期一样，只要`refIns`还活着，该右值临时变量将会一直存活下去。实际上就是给那个临时变量取了个名字。

**注意**：这里`refIns`的**类型**是右值引用类型(`int &&`)，但是如果从左值和右值的角度区分它，它实际上是个**左值**。因为可以对它取地址，而且它还有名字，是一个已经命名的右值。

### :pen\_fountain: 2.1、常量左值引用

左值引用只能绑定左值，右值引用只能绑定右值，如果绑定的不对，编译就会失败。但是，**常量左值引用**却是个奇葩，它可以算是一个“万能”的引用类型，它可以绑定非常量左值、常量左值、右值，而且在绑定右值的时候，常量左值引用还可以像右值引用一样将右值的生命期延长，缺点是，只能读不能改。

```cpp
const A & a = getTemp();   //不会报错
```

事实上，很多情况下我们用了常量左值引用的这个功能却没有意识到，如下面的例子：

```cpp
class Copyable {
public:
    Copyable() {}
    Copyable(const Copyable& o) {
        cout << "Copied" << endl;
    }
};
Copyable ReturnRvalue() {
    return Copyable(); //返回一个临时对象
}
void AcceptVal(Copyable a) {

}
void AcceptRef(const Copyable& a) {

}

int main() {
    cout << "pass by value: " << endl;
    AcceptVal(ReturnRvalue()); // 应该调用两次拷贝构造函数
    cout << "pass by reference: " << endl;
    AcceptRef(ReturnRvalue()); // 应该只调用一次拷贝构造函数
}
```

> **期望**中`AcceptVal(ReturnRvalue())`需要调用两次拷贝构造函数，一次在`ReturnRvalue()`函数中，构造好了`Copyable`对象，返回的时候会调用拷贝构造函数生成一个临时对象，在调用`AcceptVal()`时，又会将这个对象拷贝给函数的局部变量`a`，一共调用了两次拷贝构造函数。而`AcceptRef()`的不同在于形参是常量左值引用，它能够接收一个右值，而且不需要拷贝。
>
> 而实际的结果是，不管哪种方式，一次拷贝构造函数都没有调用！

这是由于编译器默认开启了返回值优化(`RVO/NRVO`, `RVO`, `Return Value Optimization` 返回值优化，或者`NRVO`， Named Return Value Optimization)。编译器很聪明，发现在`ReturnRvalue`内部生成了一个对象，返回之后还需要生成一个临时对象调用拷贝构造函数，很麻烦，所以直接优化成了1个对象对象，避免拷贝，而这个临时变量又被赋值给了函数的形参，还是没必要，所以最后这三个变量都用一个变量替代了，不需要调用拷贝构造函数。

虽然各大厂家的编译器都已经都有了这个优化，但是这并不是`c++`标准规定的，而且不是所有的返回值都能够被优化，而这篇文章的主要讲的**右值引用**，**移动语义**可以解决编译器无法解决的问题。

为了更好的观察结果，可以在编译的时候加上`-fno-elide-constructors`选项(关闭返回值优化)。此时输出为：

```cpp
// g++ test.cpp -o test -fno-elide-constructors
pass by value: 
Copied
Copied     //可以看到确实调用了两次拷贝构造函数
pass by reference: 
Copied
```

### :pen\_fountain: 2.2、总结

`T`是一个具体类型：

1. 左值引用， 使用 `T&`, 只能绑定**左值**
2. 右值引用， 使用 `T&&`， 只能绑定**右值**
3. 常量左值， 使用 `const T&`, 既可以绑定**左值**又可以绑定**右值**
4. 已命名的**右值引用**，编译器会认为是个**左值**
5. 编译器有返回值优化，但不要过于依赖

## :pencil2: 3、移动语义

### :pen\_fountain: 3.1、移动构造函数 & 移动赋值运算符

用c++实现一个字符串类`MiniString`，`MiniString`内部管理一个C语言的`char *`数组，这个时候一般都需要实现拷贝构造函数和拷贝赋值函数，因为默认的拷贝是浅拷贝，而指针这种资源不能共享。代码如下：

```cpp
class MiniString
{
public:
    static size_t CCtor; //统计调用拷贝构造函数的次数
public:
    // 构造函数
    MiniString(const char* cstr = 0) {
        if (cstr) {
            m_data = new char[strlen(cstr) + 1];
            strcpy(m_data, cstr);
        }
        else {
            m_data = new char[1];
            *m_data = '\0';
        }
    }

    // 拷贝构造函数
    MiniString(const MiniString& str) {
        CCtor++;
        m_data = new char[strlen(str.m_data) + 1];
        strcpy(m_data, str.m_data);
    }
    // 拷贝赋值函数 =号重载
    MiniString& operator=(const MiniString& str) {
        if (this == &str) // 避免自我赋值!!
            return *this;

        delete[] m_data;
        m_data = new char[strlen(str.m_data) + 1];
        strcpy(m_data, str.m_data);
        return *this;
    }

    ~MiniString() {
        delete[] m_data;
    }

    char* get_c_str() const { return m_data; }
private:
    char* m_data;
};

size_t MiniString::CCtor = 0;

int main()
{
    vector<MiniString> vecStr;
    vecStr.reserve(1000);   //先分配好1000个空间，调用的次数可能远大于1000
    for (int i = 0; i < 1000; i++) {
        vecStr.push_back(MiniString("hello"));
    }
    cout << MiniString::CCtor << endl;
}
```

> 改代码执行了`1000`次拷贝构造函数，如果`MiniString("hello")`构造出来的字符串本来就很长，构造一遍就很耗时了，最后却还要拷贝一遍，而`MiniString("hello")`只是临时对象，拷贝完就没什么用了，这就造成了没有意义的资源申请和释放操作，如果能够直接使用临时对象已经申请的资源，既能节省资源，又能节省资源申请和释放的时间。而`C++11`新增加的**移动语义**就能够做到这一点。

移动语义避免了移动原始数据，而只是修改了记录。要实现移动语义就必须增加两个函数：移动构造函数和移动赋值构造函数。必须让编译器知道什么时候需要复制，什么时候不需要复制。这就是右值引用发挥最大作用的地方。

```cpp
class MiniString
{
public:
    static size_t CCtor; //统计调用拷贝构造函数的次数
    static size_t MCtor; //统计调用移动构造函数的次数
    static size_t CAsgn; //统计调用拷贝赋值函数的次数
    static size_t MAsgn; //统计调用移动赋值函数的次数
public:
    // 构造函数
    MiniString(const char* cstr = 0) {
        if (cstr) {
            m_data = new char[strlen(cstr) + 1];
            strcpy(m_data, cstr);
        }
        else {
            m_data = new char[1];
            *m_data = '\0';
        }
    }
    // 拷贝构造函数
    MiniString(const MiniString& str) {
        CCtor++;
        m_data = new char[strlen(str.m_data) + 1];
        strcpy(m_data, str.m_data);
    }
    // 移动构造函数
    MiniString(MiniString&& str) noexcept
        :m_data(str.m_data) {
        MCtor++;
        str.m_data = nullptr; //不再指向之前的资源了
    }
    // 拷贝赋值函数 =号重载
    MiniString& operator=(const MiniString& str) {
        CAsgn++;
        if (this == &str) // 避免自我赋值!!
            return *this;

        delete[] m_data;
        m_data = new char[strlen(str.m_data) + 1];
        strcpy(m_data, str.m_data);
        return *this;
    }
    // 移动赋值函数 =号重载
    MiniString& operator=(MiniString&& str) noexcept {
        MAsgn++;
        if (this == &str) // 避免自我赋值!!
            return *this;

        delete[] m_data;
        m_data = str.m_data;
        str.m_data = nullptr; //不再指向之前的资源了
        return *this;
    }

    ~MiniString() {
        delete[] m_data;
    }

    char* get_c_str() const { return m_data; }
private:
    char* m_data;
};

size_t MiniString::CCtor = 0;
size_t MiniString::MCtor = 0;
size_t MiniString::CAsgn = 0;
size_t MiniString::MAsgn = 0;

int main()
{
    vector<MiniString> vecStr;
    vecStr.reserve(1000); //先分配好1000个空间
    for (int i = 0; i < 1000; i++) {
        vecStr.push_back(MiniString("hello"));
    }
    cout << "CCtor = " << MiniString::CCtor << endl;
    cout << "MCtor = " << MiniString::MCtor << endl;
    cout << "CAsgn = " << MiniString::CAsgn << endl;
    cout << "MAsgn = " << MiniString::MAsgn << endl;
}
// 输出
CCtor = 0
MCtor = 1000
CAsgn = 0
MAsgn = 0
```

移动构造函数与拷贝构造函数的区别是，拷贝构造的参数是`const MiniString& str`，&#x662F;_**常量左值引用**_，而移动构造的参数是`MiniString&& str`，&#x662F;_**右值引用**_，而`MiniString("hello")`是个临时对象，是个右值，优先进入**移动构造函数**而不是拷贝构造函数。而移动构造函数与拷贝构造不同，它并不是重新分配一块新的空间，将要拷贝的对象复制过来，而是"偷"了过来，将自己的指针指向别人的资源，然后将别人的指针修改为`nullptr`，这一步很重要，如果不将别人的指针修改为空，那么临时对象析构的时候就会释放掉这个资源，"偷"也白偷了。

不用奇怪为什么可以抢别人的资源，临时对象的资源不好好利用也是浪费，因为生命周期本来就是很短，在你执行完这个表达式之后，它就毁灭了，充分利用资源，才能很高效。

当类中同时包含拷贝构造函数和移动构造函数时，如果使用临时对象初始化当前类的对象，编译器会优先调用移动构造函数来完成此操作。只有当类中没有合适的移动构造函数时，编译器才会退而求其次，调用拷贝构造函数。

### :pen\_fountain: 3.2、std::move()

对于一个左值，肯定是调用拷贝构造函数了，但是有些左值是局部变量，生命周期也很短，能不能也移动而不是拷贝呢？`C++11`为了解决这个问题，提供了`std::move()`方法来**将左值转换为右值**，从而方便应用移动语义。其实就是告诉编译器，虽然我是一个左值，但是不要对我用拷贝构造函数，而是用移动构造函数。`std::move` 的源码实现：

```cpp
// FUNCTION TEMPLATE move
template <class _Ty>
_NODISCARD constexpr remove_reference_t<_Ty>&& move(_Ty&& _Arg) noexcept { // forward _Arg as movable
    return static_cast<remove_reference_t<_Ty>&&>(_Arg);
}
```

可以看到 `std::move` 是一个模板函数，通过`remove_reference_t`获得模板参数的原本类型，然后把值转换为该类型的右值。用C++大师 Scott Meyers 的在《Effective Modern C++》中的话说， std::move 是个cast ，not a move.

注意： 使用 `move` 意味着，把一个左值转换为右值，原先的值不应该继续再使用（承诺即将废弃）。

```cpp
int main()
{
    vector<MiniString> vecStr;
    vecStr.reserve(1000); //先分配好1000个空间
    for (int i = 0; i < 1000; i++) {
        MiniString tmp("hello");
        vecStr.push_back(tmp); //调用的是拷贝构造函数
    }
    cout << "CCtor = " << MiniString::CCtor << endl;
    cout << "MCtor = " << MiniString::MCtor << endl;
    cout << "CAsgn = " << MiniString::CAsgn << endl;
    cout << "MAsgn = " << MiniString::MAsgn << endl;

    cout << endl;
    MiniString::CCtor = 0;
    MiniString::MCtor = 0;
    MiniString::CAsgn = 0;
    MiniString::MAsgn = 0;
    vector<MiniString> vecStr2;
    vecStr2.reserve(1000); //先分配好1000个空间
    for (int i = 0; i < 1000; i++) {
        MiniString tmp("hello");
        vecStr2.push_back(std::move(tmp)); //调用的是移动构造函数
    }
    cout << "CCtor = " << MiniString::CCtor << endl;
    cout << "MCtor = " << MiniString::MCtor << endl;
    cout << "CAsgn = " << MiniString::CAsgn << endl;
    cout << "MAsgn = " << MiniString::MAsgn << endl;
}

// 输出
CCtor = 1000
MCtor = 0
CAsgn = 0
MAsgn = 0

CCtor = 0
MCtor = 1000
CAsgn = 0
MAsgn = 0
```

看下面的例子：

```cpp
MiniString str1("hello"); // 调用构造函数
MiniString str2("world"); // 调用构造函数
MiniString str3(str1);    // 调用拷贝构造函数
MiniString str4(std::move(str1));    // 调用移动构造函数
// cout << str1.get_c_str() << endl; // 此时str1的内部指针已经失效了！不要再使用
// 虽然str1中的m_dat已经称为了空，但是str1这个对象还活着，知道出了它的作用域才会析构，
// 而不是move完了立刻析构
MiniString str5;
str5 = str2;            // 调用拷贝赋值函数
MiniString str6;
str6 = std::move(str2); // 调用移动赋值函数，str2的内容也失效了，不要再使用
```

需要注意一下几点：

1. &#x20;`str6 = std::move(str2)`，虽然将`str2`的资源给了`str6`，但是`str2`并没有立刻析构，只有在`str2`离开了自己的作用域的时候才会析构，所以，如果继续使用`str2`的`m_data`变量，可能会发生意想不到的错误。
2. 如果我们没有提供移动构造函数，只提供了拷贝构造函数，`std::move()`会失效但是不会发生错误，因为编译器找不到移动构造函数就去寻找拷贝构造函数，也这是拷贝构造函数的参数是`const T&`常量左值引用的原因！
3. &#x20;`c++11中`的所有容器都实现了`move`语义，`move`只是转移了资源的控制权，本质上是将左值强制转化为右值使用，以用于移动拷贝或赋值，避免对**含有资源的对象**发生无谓的拷贝。`move`对于拥有如内存、文件句柄等资源的成员的对象有效，如果是一些基本类型，如`int`和`char[10]`数组等，如果使用`move`，仍会发生拷贝（因为没有对应的移动构造函数），所以说`move`对含有资源的对象说更有意义。

## :pencil2: 4、通用引用（universal references）

当右值引用和模板结合的时候，就复杂了。`T&&`并不一定表示右值引用，它可能是个左值引用又可能是个右值引用。例如：

```cpp
template<typename T>
void f( T&& param ){
    
}
f(10);  // 10 是右值
int x = 10; 
f(x);   // x 是左值
```

如果上面的函数模板表示的是右值引用的话，肯定是不能传递左值的，但是事实却是可以。这里的`&&`是一个未定义的引用类型，称为`universal references`，它必须被初始化，它是左值引用还是右值引用却决于它的初始化，如果它被一个左值初始化，它就是一个左值引用；如果被一个右值初始化，它就是一个右值引用。

**注意**：只有当**发生自动类型推断**时（如函数模板的类型自动推导，或auto关键字），`&&`才是一个`universal references`。

例如：

```cpp
template<typename T>
void f( T&& param );    // 这里T的类型需要推导，所以&&是一个 universal references

template<typename T>
class Test {
  Test( Test&& rhs );   // Test是一个特定的类型，不需要类型推导，所以&&表示 右值引用  
};

void f( Test&& param ); // 右值引用

//复杂一点
template<typename T>
void f( std::vector<T>&& param ); // 在调用这个函数之前，这个vector<T>中的推断类型
                                  // 已经确定了，所以调用f函数的时候没有类型推断了，所以是 右值引用

template<typename T>
void f( const T&& param );  // 右值引用

// universal references仅仅发生在 T&& 下，任何一点附加条件都会使之失效
```

所以最终还是要看`T`被推导成什么类型，如果`T`被推导成了`string`，那么`T&&`就是`string&&`，是个右值引用，如果`T`被推导为`string&`，就会发生类似`string& &&`的情况，对于这种情况，`c++11`增加了引用折叠的规则，总结如下：

1. 所有的右值引用叠加到右值引用上仍然使一个右值引用。
2. 所有的其他引用类型之间的叠加都将变成左值引用。

如上面的`T& &&`其实就被折叠成了个`string &`，是一个左值引用。

```cpp
template<typename T>
void f( T&& param ){
    if (std::is_same<string, T>::value)
        std::cout << "string" << std::endl;
    else if (std::is_same<string&, T>::value)
        std::cout << "string&" << std::endl;
    else if (std::is_same<string&&, T>::value)
        std::cout << "string&&" << std::endl;
    else if (std::is_same<int, T>::value)
        std::cout << "int" << std::endl;
    else if (std::is_same<int&, T>::value)
        std::cout << "int&" << std::endl;
    else if (std::is_same<int&&, T>::value)
        std::cout << "int&&" << std::endl;
    else
        std::cout << "unkown" << std::endl;
}

int main()
{
    int x = 1;
    f(1);   // 参数是右值 T推导成了int, 所以是int&& param, 右值引用
    f(x);   // 参数是左值 T推导成了int&, 所以是int&&& param, 折叠成 int&,左值引用
    int && a = 2;
    f(a);   //虽然a是右值引用，但它还是一个左值， T推导成了int&
    string str = "hello";
    f(str); //参数是左值 T推导成了string&
    f(string("hello")); //参数是右值， T推导成了string
    f(std::move(str));  //参数是右值， T推导成了string
}

```

所以，归纳一下， 传递左值进去，就是左值引用，传递右值进去，就是右值引用。如它的名字，这种类型确实很"通用"，下面要讲的完美转发，就利用了这个特性。

## :pencil2: 5、完美转发

所谓转发，就是通过一个函数将参数继续转交给另一个函数进行处理，原参数可能是右值，可能是左值，如果还能继续保持参数的原有特征，那么它就是完美的。

```cpp
void process(int& i){
    cout << "process(int&):" << i << endl;
}
void process(int&& i){
    cout << "process(int&&):" << i << endl;
}

void myforward(int&& i){
    cout << "myforward(int&&):" << i << endl;
    process(i);
}

int main()
{
    int a = 0;
    process(a);    // a被视为左值 process(int&):0
    process(1);    // 1被视为右值 process(int&&):1
    process(move(a)); //强制将a由左值改为右值 process(int&&):0
    myforward(2);     //右值经过forward函数转交给process函数，却称为了一个左值，
    //原因是该右值有了名字  所以是 process(int&):2
    myforward(move(a));  // 同上，在转发的时候右值变成了左值  process(int&):0
    // forward(a) // 错误用法，右值引用不接受左值
}
```

上面的例子就是不完美转发，而c++中提供了一个`std::forward()`模板函数解决这个问题。将上面的`myforward()`函数简单改写一下：

```cpp
void myforward(int&& i){
    cout << "myforward(int&&):" << i << endl;
    process(std::forward<int>(i));
}

myforward(2); // process(int&&):2
```

上面修改过后还是不完美转发，`myforward()`函数能够将右值转发过去，但是并不能够转发左值，解决办法就是借助`universal references`通用引用类型和`std::forward()`模板函数共同实现完美转发。例子如下：

```cpp
void RunCode(int &&m) {
    cout << "rvalue ref" << endl;
}
void RunCode(int &m) {
    cout << "lvalue ref" << endl;
}
void RunCode(const int &&m) {
    cout << "const rvalue ref" << endl;
}
void RunCode(const int &m) {
    cout << "const lvalue ref" << endl;
}

// 这里利用了universal references，如果写T&，就不支持传入右值，而写T&&，既能支持左值，又能支持右值
template<typename T>
void perfectForward(T && t) {
    RunCode(forward<T> (t));
}

template<typename T>
void notPerfectForward(T && t) {
    RunCode(t);
}

int main()
{
    int a = 0;
    int b = 0;
    const int c = 0;
    const int d = 0;

    notPerfectForward(a);       // lvalue ref
    notPerfectForward(move(b)); // lvalue ref
    notPerfectForward(c);       // const lvalue ref
    notPerfectForward(move(d)); // const lvalue ref

    cout << endl;
    perfectForward(a);       // lvalue ref
    perfectForward(move(b)); // rvalue ref
    perfectForward(c);       // const lvalue ref
    perfectForward(move(d)); // const rvalue ref
}
```

上面的代码测试结果表明，在`universal references`和`std::forward`的合作下，能够完美的转发这4种类型。

## :pencil2: 6、`emplace_back`减少内存拷贝和移动

我们之前使用`vector`一般都喜欢用`push_back()`，由上文可知容易发生无谓的拷贝，解决办法是为自己的类增加移动拷贝和赋值函数，但其实还有更简单的办法！就是使用`emplace_back()`替换`push_back()`，如下面的例子：

```cpp
class A {
public:
    A(int i){
        str = to_string(i);
    }
    ~A(){}
    A(const A& other): str(other.str){
        cout << "A&" << endl;
    }

public:
    string str;
};

int main()
{
    vector<A> vec;
    vec.reserve(10);
    for(int i=0;i<10;i++){
        // vec.push_back(A(i));     //调用了10次拷贝构造函数
        vec.emplace_back(i);     //一次拷贝构造函数都没有调用过
    }
    for(int i=0;i<10;i++)
        cout << vec[i].str << endl;
}
```

可以看到效果是明显的，虽然没有测试时间，但是确实可以减少拷贝。`emplace_back()`可以直接通过构造函数的参数构造对象，但前提是**要有对应的构造函数**。

对于`map`和`set`，可以使用`emplace()`。基本上`emplace_back()`对应`push_back()`, `emplce()`对应`insert()`。

移动语义对`swap()`函数的影响也很大，之前实现swap可能需要三次内存拷贝，而有了移动语义后，就可以实现高性能的交换函数了。

```cpp
template <typename T>
void swap( T& a, T& b )
{
    T tmp(std::move(a));
    a = std::move(b);
    b = std::move(tmp);
}
```

如果`T`是可移动的，那么整个操作会很高效，如果不可移动，那么就和普通的交换函数是一样的，不会发生什么错误，很安全。

## :pencil2: 7、总结

* 由两种值类型，左值和右值。
* 有三种引用类型，左值引用、右值引用和通用引用。左值引用只能绑定左值，右值引用只能绑定右值，通用引用由初始化时绑定的值的类型确定。
* 左值和右值是独立于他们的类型的，右值引用可能是左值可能是右值，如果这个右值引用已经被命名了，他就是左值。
* 引用折叠规则：所有的右值引用叠加到右值引用上仍然是一个右值引用，其他引用折叠都为左值引用。当`T&&`为模板参数时，输入左值，它将变成左值引用，输入右值则变成具名的右值应用。
* 移动语义可以减少无谓的内存拷贝，要想实现移动语义，需要实现移动构造函数和移动赋值函数。
* &#x20;`std::move()`将一个左值转换成一个右值，强制使用移动拷贝和赋值函数，这个函数本身并没有对这个左值什么特殊操作。
* &#x20;`std::forward()`和`universal references`通用引用共同实现完美转发。
* 用`empalce_back()`替换`push_back()`增加性能。
