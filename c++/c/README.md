# C语言

## ✏ `POSIX`

`POSIX`表示可移植操作系统接口（Portable Operating System Interface of UNIX，缩写为 `POSIX` ），`POSIX`标准定义了操作系统应该为应用程序提供的接口标准，是IEEE为要在各种UNIX操作系统上运行的软件而定义的一系列API标准的总称。其正式称呼为IEEE Std 1003，而国际标准名称为ISO/IEC 9945。

`POSIX`标准意在期望获得源代码级别的软件可移植性。换句话说，为一个`POSIX`兼容的操作系统编写的程序，应该可以在任何其它的`POSIX`操作系统（即使是来自另一个厂商）上编译执行。

`POSIX`只定义接口，不定义具体实现,即定义了头文件 `*.h`，源文件`*.c`或库文件由各个提供商提供。

`POSIX`是标准C的超集，意味着标准C的函数都属于`POSIX`，可以直接使用这些函数，比如`stdio.h`中的`printf`、`scanf`，`pthread.h`中的`pthread_create`等。

`POSIX`主要由四部分组成：

1. `XBD(Base Definitions volume)`：包含一些通用的术语、概念、接口以及工具函数\(`cd`，`mkdir`，`cp`，`mv`等\)和头文件定义\(`stdio.h`，`stdlib.h`，`pthread.h`等\)。
2. `XSH(System Interface volume)`：包含系统服务函数的定义,例如线程、套接字、标准IO、信号处理、错误处理等。
3. `XCU(Shell and Utilities volume)`：包含shell脚本书写的语法、关键字以及工具函数\(`break`，`cd`，`cp`，`continue`，`pwd`，`return`\)的定义。
4. `XRAT(Rationale volume)`：包含与本标准有关的历史信息以及采用或舍弃某功能的扩展基本原理。



