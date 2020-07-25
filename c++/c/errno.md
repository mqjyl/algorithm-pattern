# C标准库之errno

 `<errno.h>`定义了整数变量`extern int errn o`。由系统调用和某些库函数根据事件来设置它，用以表明哪里有问题。这个值只有当调用的返回表明错误的时候有用（比如，对于大多数的系统调用是-1，对于大多数的库函数来说是-1或NULL），正确的函数也可以修改`errno`。

Linux中系统调用的错误都存储于 `errno` 中，`errno` 由操作系统维护，存储就近发生的错误，即下一次的错误码会覆盖掉上一次的错误。

*  在程序启动时，**`errno`** 设置为零，系统调用或 C 标准库中的特定函数修改它的值为一些非零值以表示某些类型的错误。有效的错误number都是非零的，系统调用和库函数不会把`errno`设为0。 对于某些系统调用和库函数（比如，`getpriority(2)`），没有错误的时候也会返回-1。在这种情况下，可以在调用之前先将`errno`设为0，当不确定有没有错误的时候，可以通过查看`errno`是不是一个非零值来确定是否发生错误。 
* `errno`的值不会被任何程序清除，因此在使用`errno`的值之前，先要通过函数（系统调用/库函数）的返回值来确定有错误发生。
*  ISO C标准将`errno`定义为一个可以修改的int型左值，**并且不允许准确声明**，`errno`可能是一个宏，`errno`是`thread-local`，在一个线程中设置它的值不会影响它在另一个thread中的值。
* 

