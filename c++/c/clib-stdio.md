# C标准库之stdio

stdin、stdout、stderr 就是三个文件流指针。分别表示标准输入，输出，错误输出。系统会为每一个进程打开这三个文件

上面三个变量都是一个打开的文件的句柄，stdout，stderr一般关联到终端，stdin一般关联到键盘

