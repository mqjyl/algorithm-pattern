# RAII技术

`RAII`是C++的发明者`Bjarne Stroustrup`提出的概念，`RAII`全称是“Resource Acquisition is Initialization”，直译过来是“资源获取即初始化”，也就是说在构造函数中申请分配资源，在析构函数中释放资源。因为C++的语言机制保证了，当一个对象创建的时候，自动调用构造函数，当对象超出作用域的时候会自动调用析构函数。所以，在`RAII`的指导下，我们应该使用类来管理资源，将资源和对象的生命周期绑定。

## :pencil2: 1、资源管理

`RAII`是用来管理资源、避免资源泄漏的方法。在计算机系统中，资源是数量有限且对系统正常运行具有一定作用的元素。**比如：网络套接字、互斥锁、文件句柄和内存等等，它们属于系统资源。**&#x7531;于系统的资源是有限的，所以，我们在编程使用系统资源时，都必须遵循一个步骤：

1. 申请资源；
2. 使用资源；
3. 释放资源。

第一步和第二步缺一不可，因为资源必须要申请才能使用的，使用完成以后，必须要释放，如果不释放的话，就会造成资源泄漏。

智能指针（`std::shared_ptr`和`std::unique_ptr`）即`RAII`最具代表的实现，使用智能指针，可以实现自动的内存管理，再也不需要担心忘记delete造成的内存泄漏。（[博客](http://mindhacks.cn/2012/08/27/modern-cpp-practices/)）

```cpp
#define SCOPEGUARD_LINENAME_CAT(name, line) name##line
#define SCOPEGUARD_LINENAME(name, line) SCOPEGUARD_LINENAME_CAT(name, line)
#define ON_SCOPE_EXIT(callback) ScopeGuard SCOPEGUARD_LINENAME(EXIT, __LINE__)(callback)

class ScopeGuard
{
public:
    explicit ScopeGuard(std::function<void()> f) : 
        handle_exit_scope_(f){};

    ~ScopeGuard(){ handle_exit_scope_(); }
private:
    std::function<void()> handle_exit_scope_;
};

int main()
{
    {
        A *a = new A();
        ON_SCOPE_EXIT([&] {delete a; });
        ......
    }

    {
        std::ofstream f("test.txt");
        ON_SCOPE_EXIT([&] {f.close(); });
        ......
    }

    system("pause");
    return 0;
}
```

使用C++11标准中的lambda表达式和`std::function`相结合的方法，定义了根据行号来对`ScopeGuard`类型对象命名的宏定义。当`ScopeGuard`对象超出作用域，`ScopeGuard`的析构函数中会调用`handle_exit_scope`函数，也就是lambda表达式中的内容，所以在lambda表达式中填上资源释放的代码即可。既不需要为每种资源管理单独写对应的管理类，也不需要考虑手动释放出现各种异常情况下的处理，同时资源的申请和释放放在一起去写。

### :pencil2: 2、状态管理

`RAII`另一个引申的应用是可以实现安全的状态管理。一个典型的应用就是在线程同步中，使用std::unique\_lock或者std::lock\_guard对互斥量`std:: mutex`进行状态管理。通常我们不会写如下的代码：

```cpp
std::mutex mutex_;
void function()
{
    mutex_.lock();
    ......
    ......
    mutex_.unlock();
}
```

因为，在互斥量lock和unlock之间的代码很可能会出现异常，或者有return语句，这样的话，互斥量就不会正确的unlock，会导致线程的死锁。所以正确的方式是使用`std::unique_lock`或者`std::lock_guard`对互斥量进行状态管理：

```cpp
std::mutex mutex_;
void function()
{
    std::lock_guard<std::mutex> lock(mutex_);
    ......
    ......
}
```

在创建`std::lock_guard`对象的时候，会对`std::mutex`对象进行lock，当std::lock\_guard对象在超出作用域时，会自动`std::mutex`对象进行解锁，这样的话，就不用担心代码异常造成的线程死锁。
