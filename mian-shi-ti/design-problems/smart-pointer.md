# 实现智能指针

智能指针的一种通用实现方法是采用引用计数，将一个计数器与类指向的对象相关联，引用计数跟踪共有多少个类对象共享同一指针。

1. 每次创建类的新对象时，初始化指针并将引用计数置为1； 
2. 当对象作为另一对象的副本而创建时，拷贝构造函数拷贝指针并增加与之相应的引用计数； 
3. 对一个对象进行赋值时，赋值操作符减少左操作数所指对象的引用计数（如果引用计数减至0，则删除对象），并增加右操作数所指对象的引用计数，这是因为左侧的指针指向了右侧指针所指向的对象，因此右指针所指向的对象的引用计数+1； 
4. 调用析构函数时，构造函数减少引用计数（如果引用计数减至0，则删除基础对象）。 
5. 智能指针就是模拟指针动作的类，因此会重载 `->` 和 `*` 操作符。

```cpp
template<typename T>
class SharedPointer {
public:
    SharedPointer(T *ptr = nullptr) : _ptr(ptr){
        if(ptr != nullptr)
            _count = new int(1);
        else
            _count = new int(0);
    }
    // 拷贝构造函数
    SharedPointer(const SharedPointer& ptr){
        if(this != &ptr){
            this->_ptr = ptr._ptr;
            this->_count = ptr._count;
            (*this->_count) ++;
        }
    }
    // 拷贝赋值运算符
    SharedPointer& operator = (const SharedPointer &ptr){
        if(this->_ptr == ptr._ptr){
            return *this;
        }

        if(this->_ptr){
            (*this->_count) --;
            if((*this->_count) == 0){
                delete this->_count;
                delete this->_ptr;
            }
        }

        this->_ptr = ptr._ptr;
        this->_count = ptr._count;
        (*this->_count) ++;
        return *this;
    }

    ~SharedPointer(){
        (*this->_count) --;
        if((*this->_count) == 0){
            delete this->_count;
            delete this->_ptr;
        }
    }

public:
    T& operator*(){
        return *(this->_ptr);
    }

    T* operator->(){
        return this->_ptr;
    }

    int use_count(){
        return *(this->_count);
    }

    T* get(){
        return this->_ptr;
    }
    // 重载布尔值操作
    operator bool(){
        return this->_ptr == nullptr;
    }

private:
    T *_ptr;
    int *_count;
};

// 测试代码
void testSharedPointer(){
    SharedPointer<int> p1(new int(10));
    cout << "值：" << *(p1.get()) << " 引用计数：" << p1.use_count() << endl;
    *p1 = 100;
    cout << "值：" << *p1 << " 引用计数：" << p1.use_count() << endl;

    {
        SharedPointer<int> p2(p1);
        *p2 = 3;
        cout << "值：" << *p2 << " 引用计数：" << p1.use_count() << endl;

        SharedPointer<int> p3 = p2;
        *p3 = 55;
        cout << "值：" << *p3 << " 引用计数：" << p1.use_count() << endl;

        cout << "值：" << *p2 << " 引用计数：" << p1.use_count() << endl;
    }

    cout << "值：" << *p1 << " 引用计数：" << p1.use_count() << endl;

    if(p1){
        cout << "True" << endl;
    }

    SharedPointer<string> q1(new string("我是string1"));
    cout << "值：" << (*(q1)).c_str() << " size：" << q1->size() << endl;
}
```

实现智能指针还有两种经典的策略：一是引入辅助类，二是使用句柄类。后序会讨论。

