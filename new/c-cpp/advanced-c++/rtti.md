# RTTI技术

`RTTI(Run-Time Type Information)`，运行时类型检查的英文缩写，它提供了运行时确定对象类型的方法。面向对象的编程语言，像`C++`，`Java`，`delphi`都提供了对`RTTI`的支持。

运行时类型判定有两种形式：（1）传统的`RTTI`;（2）反射reflection机制。`RTTI`的初期想法非常简单：当有一个指向基础型别（父类）的 `reference`（引用）时，`RTTI`机制让你找出其所指的确切型别，不过当拓展到`java.lang.reflection`的时候，展现了全新的功能。相比较而言，Java的反射机制功能比C++的`RTTI`更完整一些， 一般不要认为Java的反射就是指 `java.lang.reflect` 这个包提供的工具类和接口。其实Java的整个**对象类型系统**，包括所有类的始祖类**Object类**以及每个对象都附带的**Class对象**，都是反射机制的一部分。

## :pencil2: `RTTI`运算符

一些面向对象专家在传播自己的设计理念时，大多都主张在设计和开发中明智地使用虚拟成员函数，而不用 `RTTI` 机制。但是，在很多情况下，虚拟函数无法克服本身的局限。每每涉及到处理异类容器和根基类层次（如 `MFC`）时，不可避免要对对象类型进行动态判断，也就是动态类型的侦测。如何确定对象的动态类型呢？答案是使用内建的 `RTTI` 中的运算符：`typeid` 和 `dynamic_cast`。

### :pen\_fountain: `typeid`

`typeid`是C++的关键字之一，等同于`sizeof`这类的操作符。`typeid`操作符的返回结果是名为`type_info`的标准库类型的对象的引用（在头文件`<typeinfo>`中定义），它的表达式有两种形式：

* 类型ID：`typeid(type)`
* 运行时刻类型ID：`typeid(expr)`

**如果表达式的类型是类类型且至少包含有一个虚函数，则`typeid`操作符返回表达式的动态类型，需要在运行时计算；否则，`typeid`操作符返回表达式的静态类型，在编译时就可以计算。** ISO C++标准并没有确切定义type\_info，它的确切定义编译器相关的，但是标准却规定了其实现必需提供如下四种操作：

```cpp
t1 == t2      // 如果两个对象t1和t2类型相同，则返回true；否则返回false
t1 != t2      // 如果两个对象t1和t2类型不同，则返回true；否则返回false
t.name()      // 返回类型的C-style字符串，类型名字用系统相关的方法产生
t1.before(t2) // 返回指出t1是否出现在t2之前的bool值
```

type\_info类提供了public虚 析构函数，以使用户能够用其作为基类。它的默认构造函数和拷贝构造函数及赋值操作符都定义为private，所以不能定义或复制type\_info类型的对象。**程序中创建type\_info对象的唯一方法是使用`typeid`操作符**（由此可见，如果把`typeid`看作函数的话，其应该是`type_info`的 友元）。type\_info的name成员函数返回`C-style`的字符串，用来表示相应的类型名，但务必注意这个返回的类型名与程序中使用的相应类型名并不一定一致，这具体由编译器的实现所决定的，标准只要求实现为每个类型返回唯一的字符串。

```cpp
class A
{
public:
    void Print() { cout << "This is class A." << endl; }
    // virtual void Print() { cout << "This is class A." << endl; }
};

class B : public A
{
public:
    void Print() { cout << "This is class B." << endl; }
};

int main()
{
    A* pA = new B();
    cout << typeid(pA).name() << endl;  // class A *
    cout << typeid(*pA).name() << endl; // class A  
    // 如果将 print 函数变成虚函数， 则输出class B
    return 0;
}
```

使用type\_info类中重载的==和!=比较两个对象的类型是否相等：

```cpp
class C : public A
{
public:
    void Print() { cout << "This is class C." << endl; }
};

void Handle(A* a)
{
    if (typeid(*a) == typeid(A))
    {
        cout << "I am a A truly." << endl;
    }
    else if (typeid(*a) == typeid(B))
    {
        cout << "I am a B truly." << endl;
    }
    else if (typeid(*a) == typeid(C))
    {
        cout << "I am a C truly." << endl;
    }
    else
    {
        cout << "I am alone." << endl;
    }
}

int main()
{
    A* pA = new B();
    Handle(pA);   // I am a B truly.
    delete pA;
    pA = new C(); // I am a C truly.
    Handle(pA);
    return 0;
}
```

### :pen\_fountain:  [**dynamic\_cast**](../c++-syntax/type-conversion.md#4-dynamic_cast)**的内幕**

dynamic\_cast主要用于在多态的时候，它允许在运行时刻进行类型转换，从而使程序能够在一个类层次结构中安全地转换类型，把基类指针（引用）转换为派生类指针（引用）。当类中存在虚函数时，编译器就会在类的成员变量中添加一个指向虚函数表的`__vptr`指针，每一个class所关联的`type_info object`也经由`virtual table`被指出来，通常这个`type_info object`放在虚函数表的第一个`slot`。 当我们进行dynamic\_cast时，编译器会帮我们进行语法检查。如果指针的静态类型和目标类型相同，那么就什么事情都不做；否则，首先对指针进行调整，使得它指向`vftable`，并将其和调整之后的指针、调整的偏移量、静态类型以及目标类型传递给内部函数。其中最后一个参数指明转换的是指针还是引用。**两者唯一的区别是，如果转换失败，前者返回NULL，后者抛出`bad_cast`异常**。

```cpp
void Handle(A* a)
{
    if (dynamic_cast<B*>(a))
    {
        cout << "I am a B truly." << endl;
    }
    else if (dynamic_cast<C*>(a))
    {
        cout << "I am a C truly." << endl;
    }
    else
    {
        cout << "I am alone." << endl;
    }
}
```

### :pen\_fountain: 使用场景举例

首先创建某个处理文件的抽象基类。它声明下列纯虚拟函数：

```cpp
class File
{
public:
    virtual ~File() = 0; // 纯虚拟析构函数
    
    virtual int open(const std::string& filename, std::fstream& f_io) = 0;
    virtual int close(const std::string& filename, std::fstream& f_io) = 0;
    virtual int write(const std::string& data, std::fstream f_io) = 0;
    virtual int read(const std::string& buff, std::fstream f_io) = 0;
};
```

现在从 File 类派生的类要实现基类的纯虚拟函数，同时还要提供一些其他的操作。假设派生类为 `DiskFile`，除了实现基类的纯虚拟函数外，还要实现自己的`flush()`和`defragment()`操作：

```cpp
class DiskFile :public File
{
public:
    int open(const std::string& filename, std::fstream& f_io) {}
    int close(const std::string& filename, std::fstream& f_io) {}
    int write(const std::string& data, std::fstream f_io) {}
    int read(const std::string& buff, std::fstream f_io) {}
    
    virtual int flush() {}
    virtual int defragment() {}
};
```

接着，又从 `DiskFile` 类派生两个类，假设为 `TextFile` 和 `MediaFile`。前者针对文本文件，后者针对音频和视频文件：

```cpp
class TextFile : public DiskFile
{
public:
	int flush() {}
	int defragment() {}
	int sort_by_words() {}
};

class MediaFile : public DiskFile
{
public:
	int flush() {}
	int defragment() {}
	int split() {}
};
```

假设要开发一个基于图形用户界面（GUI）的文件管理器，每个文件都可以以图标方式显示。当鼠标移到图标上并单击右键时，文件管理器打开一个菜单，每个文件除了共同的菜单项，不同的文件类型还有不同的菜单项。如：共同的菜单项有“打开”“拷贝”、和“粘贴”，此外，还有一些针对特殊文件的专门操作。比如，文本文件会有“编辑”操作，而多媒体文件则会有“播放”菜单。

为了使用 `RTTI` 来动态定制菜单，文件管理器必须侦测每个文件的动态类型。利用 运算符 `typeid`可以获取与某个对象关联的运行时类型信息：

```cpp
void menu::build(const File* pfile)
{
	if (typeid(*pfile) == typeid(TextFile))
	{
		add_option("edit");
	}
	else if (typeid(*pfile) == typeid(MediaFile))
	{
		add_option("play");
	}
}
```

使用 `typeid` 要注意一个问题，那就是某些编译器（如 Visual C++）默认状态是禁用 `RTTI` 的，目的是消除性能上的开销。如果你的程序确实使用了 `RTTI`，一定要记住在编译前启用 `RTTI`。使用 `typeid` 可能产生一些将来的维护问题。假设你决定扩展上述的类层次，从`MediaFile` 派生另一个叫 `LocalizeMedia` 的类，用这个类表示带有不同语言说明文字的媒体文件。但 `LocalizeMedia` 本质上还是个 `MediaFile` 类型的文件。因此，当用户在该类文件图标上单击右键时，文件管理器必须提供一个“播放”菜单。可惜 `build()`成员函数会调用失败，原因是你没有检查这种特定的文件类型。为了解决这个问题，你必须象下面这样对 `build()` 打补丁：

```cpp
void menu::build(const File* pfile)
{
	...
	else if (typeid(*pfile) == typeid(LocalizedMedia))
	{
		add_option("play");
	}
}
```

以后每次添加新的类，毫无疑问都必须打类似的补丁。显然，这不是一个理想的解决方案。这个时候我们就要用到 [dynamic\_cast](../c++-syntax/type-conversion.md#4-dynamic_cast)，这个运算符用于多态编程中保证在运行时发生正确的转换（即编译器无法验证是否发生正确的转换）。dynamic\_cast 常用于从多态编程基类指针向派生类指针的向下类型转换。用它来确定某个对象是 `MediaFile` 对象还是它的派生类对象，也就是说，如果该函数成功地并且是动态的将 `*pfile` 强制转换为 `MediaFile`，那么 `pfile`的动态类型是 `MediaFile` 或者是它的派生类：

```cpp
void menu::build(const File* pfile)
{
	if (dynamic_cast <MediaFile*> (pfile))
	{
		// pfile 是 MediaFile 或者是MediaFile的派生类 LocalizedMedia
		add_option("play");
	}
	else if (dynamic_cast <TextFile*> (pfile))
	{
		// pfile 是 TextFile 或者是TextFile的派生类
		add_option("edit");
	}
}
```
