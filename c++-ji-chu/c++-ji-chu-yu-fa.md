---
description: c++基础语法复习，主要包括常考点、难点、易错点和重要特性。
---

# C++基础语法

### 常见考点



### 其他知识点

1、cout、cerr 和 clog

> C++标准库IO库中提供的输出工具有：
>
> * cout：写到标准输出的ostream对象；
> * cerr：输出到标准错误的ostream对象，没有缓冲；
> * clog：输出到标准错误的ostream对象，有缓冲。
>
> 重定向操作只影响cout，而不影响cerr；因此，如果将程序输出重定向到文件，并且发生了错误，则屏幕上仍然会出现错误消息。
>
> 在UNIX系统中，可以分别对cout和cerr进行重定向，命令行操作符 ‘’1&gt;” 用于对cout进行重定向，操作符 “2&gt;” 对cerr进行重定向。因为，系统的SHELL里一般约定1为正确流，2为错误流。而1是作为缺省值使用可以省略不写。

```cpp
# test_cerr.cpp
#include <iostream>
 
using namespace std;
 
int main() {
    cout << "hello world---cout" << endl ;
    cerr << "hello world---cerr" << endl ;
    return 0;
} 

/*
$ ./test_cerr 2> test_cerr.txt
hello world---cout
$ ./test_cerr > test_cout.txt
hello world---cerr
$ cat ./test_cerr.txt
hello world---cerr
$ cat ./test_cout.txt 
hello world---cout
*/
```



