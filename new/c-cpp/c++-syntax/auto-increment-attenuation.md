# 自增(++) & 自减(--)

## :pencil2: 1、内置类型

```cpp
++a    a++   a=a+1    a+=1
```

对于内置类型，对于现代编译器而言，这四个的效率都是没有区别的，在`VS2019`下查看汇编代码如下：

![](<../../.gitbook/assets/image (69).png>)

注意：不要在一条语句中使用多个++，因为在不同系统中对这样的情况处理可能不一样，比如y = (4 + x++) + (6 + x++)。这条语句只能保证程序执行到下一条语句之前，x被递增两次，并不能保证4 + x++后立即自增。

## :pencil2: 2、自定义类型

### :pen\_fountain: 2.1、a++与++a区别

1. a++是先赋值再自增，++a是先自增再赋值。
2. a++是先用临时对象保存原来的对象，然后对原对象自增，再返回临时对象，不能作为左值；++a是直接对于原对象进行自增，然后返回原对象的**引用**，可以作为左值。
3. 由于要生成临时对象，a++需要调用两次拷贝构造函数与析构函数（将原对象赋给临时对象一次，临时对象以值传递方式返回一次）；++a由于不用生成临时变量，且以引用方式返回，故没有构造与析构的开销，效率更高。

左值一般是可以放在赋值符号左边的值，其在内存中有实体；右值一般只能放在赋值符号右边，不具有内存实体，无法通过取地址获得相应对象。如：

```cpp
class Point {
    int x_;
    int y_;
public:
    Point(int x = 0, int y = 0);
    Point(const Point&);
    ~Point();
    Point& operator++();         //前置
    const Point operator++(int); //后置，int仅仅为了重载
    Point operator+(const Point&);
    Point& operator+=(const Point&);
    void DisplayPoint();
};

Point::Point(int x, int y)
{
    x_ = x;
    y_ = y;
    cout << "this is constructor" << endl;
}

Point::Point(const Point& b)
{
    this->x_ = b.x_;
    this->y_ = b.y_;
    cout << "this is copy constructor" << endl;
}

Point::~Point()
{
    cout << "this is destructor" << endl;
}

Point& Point::operator+=(const Point& _right)
{
    this->x_ += _right.x_;
    this->y_ += _right.y_;
    return *this;
}

Point Point::operator+(const Point& _right)
{
    Point temp;
    temp.x_ = this->x_ + _right.x_;
    temp.y_ = this->y_ + _right.y_;
    return temp;
}


Point& Point::operator++()
{
    ++x_;
    ++y_;
    return *this;
}

const Point Point::operator++(int)
{
    Point temp(*this);
    this->x_++;
    this->y_++;
    return temp;
}

void Point::DisplayPoint()
{
    cout << "x: " << this->x_ << endl;
    cout << "y: " << this->y_ << endl;
}
```

#### :gem: 2.1.1、效率检测

```cpp
int main() {
    Point a(1, 1);
    cout << endl << "this is a++: " << endl;
    a++;
    cout << endl << "this is ++a: " << endl;
    ++a;
	return 0;
}

// 输出
this is constructor

this is a++:
this is copy constructor
this is copy constructor
this is destructor
this is destructor

this is ++a:
this is destructor
```

`a++`将会有两次的拷贝构造与析构的调用，效率非常低。

#### :gem: 2.1.2、左右值检测

```cpp
class Point {
    int x_;
    int y_;
public:
    Point(int x = 0, int y = 0);
    Point(const Point&);
    ~Point();
    Point& operator++();     //前置
    Point operator++(int);   //后置，去掉了const限定
    Point operator+(const Point&);
    Point& operator+=(const Point&);
    void DisplayPoint();
};

Point::Point(int x, int y)
{
    x_ = x;
    y_ = y;
    cout << "this is constructor" << endl;
}

Point::Point(const Point& b)
{
    this->x_ = b.x_;
    this->y_ = b.y_;
    cout << "this is copy constructor" << endl;
}

Point::~Point()
{
    cout << "this is destructor" << endl;
}

Point& Point::operator+=(const Point& _right)
{
    this->x_ += _right.x_;
    this->y_ += _right.y_;
    return *this;
}

Point Point::operator+(const Point& _right)
{
    Point temp;
    temp.x_ = this->x_ + _right.x_;
    temp.y_ = this->y_ + _right.y_;
    return temp;
}


Point& Point::operator++()
{
    ++x_;
    ++y_;
    return *this;
}

Point Point::operator++(int) // int仅仅为了重载
{
    Point temp(*this);
    this->x_++;
    this->y_++;
    return temp;
}

void Point::DisplayPoint()
{
    cout << "x: " << this->x_ << endl;
    cout << "y: " << this->y_ << endl;
}

int main() {
    Point b(2, 2);
    cout << endl << "this is &b: " << &b << endl;

    Point* c;
    c = &(++b);
    cout << endl << "this is c = &(++b): " << c << endl;
    c->DisplayPoint();

    c = &(b++);
    cout << endl << "this is c = &(b++): " << c << endl;
    c->DisplayPoint();

    ++b = *c;
    b.DisplayPoint();
    b++ = *c;
    b.DisplayPoint();
	return 0;
}

// 输出
this is constructor

this is &b: 008FFD38

this is c = &(++b): 008FFD38
x: 3
y: 3
this is copy constructor
this is copy constructor
this is destructor
this is destructor

this is c = &(b++): 008FFC5C
x: 3
y: 3
x: 3
y: 3
this is copy constructor
this is copy constructor
this is destructor
this is destructor
x: 4
y: 4
this is destructor
```

可以看到`++b`返回的对象指针跟`b`原来的地址是一样的，而`b++`返回的对象地址跟原来的`b`地址不一样（应该是临时对象的地址），虽然可以取到地址，但当成左值将有可能导致错误。比如`b++ = c`就不会将`c`给`b`，达不到原来的目的。**为此，我们应该将后置的`++`函数返回值定义为const类型：`const Point Point::operator++(int)`，就可以避免这种当成左值情况出现**。另外发现返回temp的引用可以减少一次拷贝和析构，但是不建议返回局部变量的引用！因为函数退出，局部变量将析构，引用就会指向不确定内存。

### :pen\_fountain: 2.2、a+=b与a=a+b的区别

`a+=b`返回的是a的引用，中间不涉及构造与析构，效率与`++a`一样。而`a=a+b`则会生成临时变量，而且以值传递方式返回，会有两次的构造与析构，与`a++`一样。

## :pencil2: 3、求值过程中的副作用与顺序点

```cpp
int i = 1;
i = (++i) + (++i); // i = ?
```

对于该代码，gcc和clang给出了不同的结果。clang给出了5，这很好理解；gcc却给出了6。为什么是6？

如果把`i`换成不同的变量，我们能写出这样的代码：`k = (++i) + (++j)`。尽管`i`和`j`自增先后不确定，但对结果是没有影响的。编译器可以这样拆分它：

```cpp
++i;
++j;
k = i + j;
```

那么，我们把三个`i`代入，就得到了：

```cpp
++i; // i = 2
++i; // i = 3
i = i + i; // i = 3 + 3
```

因此结果是6。

### 幕后的“顺序点” <a href="#section-5" id="section-5"></a>

之所以`(++i) + (++i)`未定义，这涉及到“顺序点”的概念。

“副作用”，即代码中对数据的改变，`++i`和`i = ...`都会产生副作用。C/C++为了让编译器能更好地优化代码，定义了一些位置，编译器能保证这些位置之后的副作用开始前，之前的副作用完成。这些位置，也就是顺序点。

顺序点包括：

* 两个语句之间，`for`循环的括号中包含了三个语句；
* 函数调用的参数与函数返回之间（但是参数自身没有顺序保证）；
* 短路求值操作符前后；
* 问号表达式的条件与结果之间；
* 变量初始化之间、之后。

