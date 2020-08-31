# C++面试题总结

1、形参和实参的区别：

1. 形参变量只有在被调用时才分配内存单元，在调用结束时， 即刻释放所分配的内存单元。因此， 形参只有在函数内部有效。 函数调用结束返回 主调函数后则不能再使用该形参变量。
2. 实参可以是常量、变量、表达式、函数等， 无论实参是何种类型的量， 在进行函数调用时，它们都必须具有确定的值， 以便把这些值传送给形参。 因此应预先用赋值，输入等办法使实参获得确定值，会产生一个临时变量。
3. 实参和形参在数量上，类型上，顺序上应严格一致， 否则会发生“ 类型 不匹配” 的错误。
4. 函数调用中发生的数据传送是单向的。 即只能把实参的值传送给形参， 而不能把形参的值反向地传送给实参。 因此在函数调用过程中，形参的 值发生改变，而实参中的值不会变化。 
5. 当形参和实参不是指针类型时，在该函数运行时，形参和实参是不同的变 量，他们在内存中位于不同的位置，形参将实参的内容复制一份，在该函 数运行结束的时候形参被释放，而实参内容不会改变。

2、指针和引用的区别。【[链接](../c-cpp/c++-syntax/pointers-and-references.md#2-1-yin-yong-he-zhi-zhen-de-qu-bie)】

3、为什么 C 语言不支持重载， C++支持重载呢？【[链接](../c-cpp/c++-syntax/c-cpp.md#2-han-shu-zhong-zai)】

4、`struct{ char a; int b; }p; sizeof(p)` 是多大？【[链接](../c-cpp/c/clib-stdarg.md#2-nei-cun-dui-qi)】

5、public 、private、 和保护。【链接】

6、delete 和 delete\[\] 什么区别。【[链接](../c-cpp/c++-syntax/memory-request-and-release.md#2-new-and-delete)】

7、malloc 和 realloc。【链接】

8、static 修饰有什么作用？【链接】

* static 修饰一个函数，函数有什么变化？

9、C++ 如何调用C 里面的库？

10、如何封装一个库？

11、包含警告：遇到过头文件`include`多次的问题吗，怎么解决？（`#ifndef`）

12、多态和继承的关系。

13、为什么会有虚函数？

14、inline与define有什么区别？

15、拷贝构造函数形参能否值传递？【[链接](../c-cpp/c++-syntax/constructor-destructor.md#2-kao-bei-gou-zao-han-shu-kao-bei-fu-zhi-yun-suan-fu)】

## STL

1、vector 和 list 有什么区别？

2、vector 的size\(\) 和 cap 的区别。

3、hash\_map 和 map 有什么区别？

4、deque的扩容机制。

5、vector的2倍扩容机制相对于1.5倍扩容机制来说有什么缺陷？

> 答：以 2 倍的方式扩容，下一次申请的内存会大于之前分配内存的总和（ $$2^k > \sum_{i = 0}^{k - 1} 2^i$$ ），导致之前释放的内存不能再次被使用。



