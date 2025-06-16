# lambda表达式

匿名函数是很多高级语言都支持的概念，如lisp语言在1958年首先采用匿名函数。匿名函数有函数体，但没有函数名。C++11中引入了lambda表达式。利用`lambda`表达式可以编写内嵌的匿名函数，用以替换独立函数或者函数对象，并且使代码更可读。但是从本质上来讲，`lambda`表达式只是一种语法糖，因为所有其能完成的工作都可以用其它稍微复杂的代码来实现。但是它简便的语法却给`C++`带来了深远的影响。如果从广义上说，`lamdba`表达式产生的是函数对象。

> 相同类似功能我们也可以使用**函数对象**或者**函数指针**实现：函数对象能维护状态，但语法开销大，而函数指针语法开销小，却没法保存范围内的状态。lambda表达式正是结合了两者的优点。

## :pencil2: 1、Lambda表达式

### :pen\_fountain: 1.1、声明Lambda表达式

```cpp
// 完整语法
[capture list] (params list) mutable(optional) constexpr(optional)(c++17) exception attribute -> return type { function body };

// 可选的简化语法
[capture list] (params list) -> return type {function body};  //1
[capture list] (params list) {function body};		//2
[capture list] {function body};		//3
```

* capture list：捕获外部变量列表，不能省略；
* params list：形参列表，可以省略（但是后面必须紧跟函数体）；
* mutable指示符： 可选，将`lambda`表达式标记为`mutable`后，函数体就可以修改传值方式捕获的变量；
* constexpr：可选，C++17，可以指定`lambda`表达式是一个常量函数；
* exception：异常设定， 可选，指定`lambda`表达式可以抛出的异常；
* attribute：可选，指定`lambda`表达式的特性；
* return type：返回类型
* function body：函数体

标号1. 函数声明了一个const类型的表达式，此声明不可改变capture list中的捕获的值。

标号2. 函数省略了返回值，此时如果function body内含有return语句，则按return语句返回类型决定返回值类型，若无则返回值为void类型。

标号3. 函数无参数列表，意味无参函数。

```cpp
vector<int> vec{1,0,9,5,3,3,7,8,2};

sort(lbvec.begin(), lbvec.end(), [](int a, int b) -> bool { return a < b; });  
```

### :pen\_fountain: 1.2、捕获外部变量

`lambda`表达式最前面的方括号的意义何在？其实这是`lambda`表达式一个很Hong要的功能，就是闭包。这里我们先讲一下`lambda`表达式的大致原理：每当你定义一个`lambda`表达式后，编译器会自动生成一个匿名类（这个类当然重载了`()`运算符），我们称为闭包类型（closure type）。那么在运行时，这个`lambda`表达式就会返回一个**匿名的闭包实例**，其实一个右值。所以，我们上面的`lambda`表达式的结果就是一个个闭包。闭包的一个强大之处是其可以通过传值或者引用的方式捕捉**其封装作用域内的变量**，前面的方括号就是用来定义捕捉模式以及变量，我们又将其称为`lambda`捕捉块。

Lambda表达式可以捕获外面变量，但需要我们提供一个谓词函数（\[capture list]在声明表达式最前）。类似参数传递方式：值传递、引入传递、指针传递。在Lambda表达式中，外部变量捕获方式也类似：**值捕获、引用捕获、隐式捕获**。

#### 值捕获

```cpp
int a = 123;
auto f = [a] { cout << a << endl; }; 
f(); // 输出：123
a = 321;
f(); // 输出：123
```

值捕获和参数传递中的**值传递**类似，被捕获的值在Lambda表达式创建时通过**值拷贝**的方式传入，类中会相应添加对应类型的非静态数据成员。在运行时，会用复制的值初始化这些成员变量，从而生成闭包。因此**Lambda表达式函数体中不能修改该外部变量的值**； 因为函数调用运算符的重载方法是`const`属性的。同样，函数体外对于值的修改也不会改变被捕获的值。 想改动传值方式捕获的值，那么就要使用`mutable`。

```cpp
auto add_x = [x](int a) mutable { x *= 2; return a + x; };  // 复制捕捉x
    
cout << add_x(10) << endl; // 输出：30
```

&#x20;因为一旦将`lambda`表达式标记为`mutable`，那么实现的函数调用运算符是非const属性的。

#### 引用捕获

```cpp
int a = 123;
auto f = [&a] { cout << a << endl; }; 
a = 321;
f(); // 输出：321
```

引用捕获的变量使用的实际上就是该引用所绑定的对象，因此引用对象的改变会改变函数体内对该对象的引用的值。 对于引用捕获方式，无论是否标记`mutable`，都可以在`lambda`表达式中修改捕获的值。

#### 隐式捕获

隐式捕获有两种方式，分别是\
\[=]：以值补获的方式捕获外部**所有变量**\
\[&]：表示以引用捕获的方式捕获外部**所有变量**。

```cpp
int a = 123, b=321;
auto df = [=] { cout << a << b << endl; };    // 值捕获
auto rf = [&] { cout << a << b << endl; };    // 引用捕获
```

#### 其他捕获方式

| 捕获外部变量形式       |                          |
| -------------- | ------------------------ |
| \[ ]           | 不捕获任何变量（无参函数）            |
| \[变量1,&变量2, …] | 值(引用)形式捕获指定的多个外部变量       |
| \[this]        | 值捕获this指针                |
| \[=, \&x]      | 变量x以引用形式捕获，其余变量以传值形式捕获   |
| \[\*this]      | 通过传值方式捕获当前对象             |
| \[&, x]        | 默认以引用捕获所有变量，但是x是例外，通过值捕获 |

> 既然只使用一次，那直接写全代码不久醒了，为啥要函数呢？——因为lambda可以捕获局部变量

在上面的捕获方式中，注意最好不要使用`[=]`和`[&]`默认捕获所有变量。首先说默认引用捕获所有变量，你有很大可能会出现悬挂引用（Dangling references），因为引用捕获不会延长引用的变量的声明周期：

```cpp
std::function<int(int)> add_x(int x)
{
    return [&](int a) { return x + a; };
}
```

因为参数`x`仅是一个临时变量，函数调用后就被销毁，但是返回的`lambda`表达式却引用了该变量，但调用这个表达式时，引用的是一个垃圾值，所以会产生没有意义的结果。如果通过传值的方式来解决上面的问题：

```cpp
std::function<int(int)> add_x(int x)
{
    return [=](int a) { return x + a; };
}
```

使用默认传值方式可以避免悬挂引用问题。但是采用默认值捕获所有变量仍然有风险，看下面的例子：

```cpp
class Filter
{
public:
    Filter(int divisorVal) : divisor(divisorVal){}

    std::function<bool(int)> getFilter() 
    {
        return [=](int value) {return value % divisor == 0; };
    }

private:
    int divisor;
};
```

这个类中有一个成员方法，可以返回一个`lambda`表达式，这个表达式使用了类的数据成员`divisor`。而且采用默认值方式捕捉所有变量。你可能认为这个`lambda`表达式也捕捉了`divisor`的一份副本，但是实际上大错特错。问题出现在哪里呢？因为数据成员`divisor`对`lambda`表达式并不可见，你可以用下面的代码验证：

```cpp
// 类的方法，下面无法编译，因为divisor并不在lambda捕捉的范围
std::function<bool(int)> getFilter() 
{
    return [divisor](int value) {return value % divisor == 0; };
}
```

那么原来的代码为什么能够捕捉到呢？仔细想想，原来每个非静态方法都有一个`this`指针变量，利用`this`指针，你可以接近任何成员变量，所以`lambda`表达式实际上捕捉的是`this`指针的副本，所以原来的代码等价于：

```cpp
std::function<bool(int)> getFilter() 
{
    return [this](int value) {return value % this->divisor == 0; };
}
```

尽管还是以值方式捕获，但是捕获的是指针，其实相当于以引用的方式捕获了当前类对象，所以`lambda`表达式的闭包与一个类对象绑定在一起了，这也很危险，因为你仍然有可能在类对象析构后使用这个`lambda`表达式，那么类似“悬挂引用”的问题也会产生。所以，采用默认值捕捉所有变量仍然是不安全的，主要是由于指针变量的复制，实际上还是按引用传值。

### :pen\_fountain: 1.3、参数

* 参数列表中不能有默认参数
* 不支持可变参数
* 所有参数必须有参数名

### :pen\_fountain: 1.4、返回类型

单一的return语句可以推断返回类型；多语句则默认返回void，否则报错，应指定返回类型。

```cpp
// 正确，单一return语句
transform(vi.begin(),vi.end(),vi.begin(), 
    [] (int i) { 
    return i<0? -i; i;
    }
);
// 错误。不能推断返回类型
transform(vi.begin(),vi.end(),vi.begin(), 
    [] (int i) { 
        if (I<0) return -i;
        else return i;
    }
);
// 正确，尾置返回类型
transform(vi.begin(),vi.end(),vi.begin(), 
    [] (int i) ->int{ 
        if (I<0) 
            return -i;
        else 
            return i;
    }
);
```

### :pen\_fountain: 1.5、赋值

可以使用auto 和function 接受lambda表达式的返回：

```cpp
int x = 8, y = 9;
auto add = [](int a, int b) { return a + b; };
std::function<int(int, int)> Add = [=](int a, int b) { return a + b; };

cout << "add: " << add(x, y) << endl; // 17
cout << "Add: " << Add(x, y) << endl; // 17
```

lambda表达式产生的类不含有默认构造函数、赋值运算符、默认析构函数。至于闭包类中是否有对应成员，`C++`标准中给出的答案是：不清楚的，看来与具体实现有关。还有一点要注意：`lambda`表达式是不能被赋值的：

```cpp
auto a = [] { cout << "A" << endl; };
auto b = [] { cout << "B" << endl; };

a = b;   // 非法，lambda无法赋值
auto c = a;   // 合法，生成一个副本
```

因为禁用了赋值操作符：

```cpp
ClosureType& operator=(const ClosureType&) = delete;
```

但是没有禁用复制构造函数，所以可以用一个`lambda`表达式去初始化另外一个`lambda`表达式而产生副本。并且`lambda`表达式也可以赋值给相对应的函数指针，这也使得你完全可以把`lambda`表达式看成对应**函数类型的指针**。

### :pen\_fountain: 1.6、新特性

&#x20;在`C++14`中，`lambda`又得到了增强，一个是泛型`lambda`表达式，一个是`lambda`可以捕捉表达式。

#### lambda捕捉表达式

`lambda`表达式可以按复制或者引用捕获在其作用域范围内的变量。而有时候，我们希望捕捉不在其作用域范围内的变量，而且最重要的是我们希望捕捉右值。所以`C++14`中引入了表达式捕捉，其允许用任何类型的表达式初始化捕捉的变量：

```cpp
// 利用表达式捕获，可以更灵活地处理作用域内的变量
int x = 4;
auto y = [&r = x, x = x + 1] { r += 2; return x * x; }();
// 此时 x 更新为6，y 为25

// 直接用字面值初始化变量
auto z = [str = "string"]{ return str; }();
// 此时z是const char* 类型，存储字符串 string
```

可以看到捕捉表达式扩大了`lambda`表达式的捕捉能力，有时候你可以用`std::move`初始化变量。这对不能复制只能移动的对象很重要，比如`std::unique_ptr`，因为其不支持复制操作，你无法以值方式捕捉到它。但是利用`lambda`捕捉表达式，可以通过移动来捕捉它：

```cpp
auto myPi = std::make_unique<double>(3.1415);

auto circle_area = [pi = std::move(myPi)](double r) { return *pi * r * r; };
cout << circle_area(1.0) << endl; // 3.1415
```

&#x20;其实用表达式初始化捕捉变量，与使用`auto`声明一个变量的机理是类似的。

#### 泛型lambda表达式

从`C++14`开始，`lambda`表达式支持泛型：其参数可以使用自动推断类型的功能，而不需要显示地声明具体类型。这就如同函数模板一样，参数要使用类型自动推断功能，只需要将其类型指定为`auto`，类型推断规则与函数模板一样。这里给出一个简单例子：

```cpp
auto add = [](auto x, auto y) { return x + y; };

int x = add(2, 3);   // 5
double y = add(2.5, 3.5);  // 6.0
```

### :pen\_fountain: 1.7、Lambda与`STL`

`lambda`表达式可以赋值给对应类型的函数指针。但是使用函数指针貌似并不是那么方便。所以`STL`定义在`<functional>`头文件提供了一个多态的函数对象封装`std::function`，其类似于函数指针。它可以绑定任何类函数对象，只要参数与返回类型相同。如下面的返回一个bool且接收两个int的函数包装器：

```cpp
std::function<bool(int, int)> wrapper = [](int x, int y) { return x < y; };
```

而`lambda`表达式一个更重要的应用是其可以用于函数的参数，通过这种方式可以实现回调函数。其实，最常用的是在`STL`算法中，比如你要统计一个数组中满足特定条件的元素数量，通过`lambda`表达式给出条件，传递给`count_if`函数：

```cpp
int value = 3;
vector<int> v {1, 3, 5, 2, 6, 10};

int count = std::count_if(v.beigin(), v.end(), [value](int x) { return x > value; });
```

再比如你想生成斐波那契数列，然后保存在数组中，此时你可以使用`generate`函数，并辅助`lambda`表达式：

```cpp
vector<int> v(10);
int a = 0;
int b = 1;

std::generate(v.begin(), v.end(), [&a, &b] { int value = b; b = b + a; a = value; return value; });
// 此时v {1, 1, 2, 3, 5, 8, 13, 21, 34, 55}
```

此外，`lambda`表达式还用于对象的排序准则：

```cpp
class Person
{
public:
    Person(const string& first, const string& last):
        firstName(first), lastName(last) {}

    Person() = default;

    string first() const { return firstName; }
    string last() const { return lastName; }
private:
    string firstName;
    string lastName;
};

int main()
{
    vector<Person> vp;
    // 添加Person信息
    ...
    // 按照姓名排序
    std::sort(vp.begin(), vp.end(), [](const Person& p1, const Person& p2)
    { return p1.last() < p2.last() || (p1.last() == p2.last() && p1.first() < p2.first()); });

    return 0;
}
```

总之，对于大部分`STL`算法，可以非常灵活地搭配`lambda`表达式来实现想要的效果。

## :pencil2: 2、Lambda与递归

对于一个求自然数1-N和的问题，我们很容易写出以下递归公式：

```cpp
f(n) = n == 1 ? 1 : n + f(n - 1);
```

若想使用C++lambda表达式实现上述过程，我们很容易将其改写为以下代码：

```cpp
const auto& f = [](const int& n) {
	return n == 1 ? 1 : n + f(n - 1);
};
```

发现编译无法通过。

### :pen\_fountain: 2.1、使用std::function

std::function可以把lambda包装起来，相当于赋予了其一个函数名，在通过引用捕获并实现递归调用，实现如下：

```cpp
const auto& sum = [](const int& n) {
		std::function<int(const int&)> s;
		s = [&](const int& n) {
			  return n == 1 ? 1 : n + s(n - 1);
		};
		return s(n);
};

std::cout << sum(100);
```

### :pen\_fountain: 2.2、将lambda作为参数

```cpp
const auto& sum = [](const int& n) {
		const auto& s = [&](auto&& self, const int& x) -> int {
			  return x == 1 ? 1 : x + self(self, x - 1);
		};
		return s(s, n);
};

std::cout << sum(100);
```

### :pen\_fountain: 2.3、函数指针

{% tabs %}
{% tab title="C++14" %}
```cpp
auto sum = std::make_shared<std::unique_ptr< std::function<int(int)> >>();
*sum = std::make_unique<std::function<int(int)>>(
    [=](int n) {
        return n == 1 ? 1 : n + (**sum)(n - 1);
    }
);

std::cout << (**sum)(100);
```
{% endtab %}

{% tab title="C++11" %}
```cpp
auto sum = std::shared_ptr<std::unique_ptr< std::function<int(int)> >>
    (new std::unique_ptr< std::function<int(int)> >());
*sum = std::unique_ptr<std::function<int(int)>>(
    new std::function<int(int)>(
        [=](int n) {
            return n == 1 ? 1 : n + (**sum)(n - 1);
        }
    )
);

std::cout << (**sum)(100);
```
{% endtab %}
{% endtabs %}

### :pen\_fountain: 2.4、使用Y组合子

构造一个Y组合子如下：

```cpp
const auto& y = [](const auto& f) {
	  return [&](const auto& x) {
		    return x(x);
	  }([&](const auto& x) -> std::function<int(int)> {
		    return f([&](const int& n) {
			      return x(x)(n);
		    });
	  });
};
```

再实现一个求和函数的高阶函数，然后连接即可：

```cpp
const auto& sum = [](const auto& f) {
		return [=](const int& n) {
			  return n == 1 ? 1 : n + f(n - 1);
		};
};

std::cout << y(sum)(100);
```
