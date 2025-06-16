# C语言

## :pencil2: 标准

&#x20;        起初，C语言没有官方标准。1978年由美国电话电报公司(AT\&T）贝尔实验室正式发表了C语言。布莱恩·柯林汉（Brian Kernighan） 和 丹尼斯·里奇（Dennis Ritchie） 出版了一本书，名叫《The C Programming Language》。这本书被 C语言开发者们称为K\&R，很多年来被当作 C语言的非正式的标准说明。人们称这个版本的 C语言为K\&R C。\
&#x20;        1970到80年代，C语言被广泛应用，从大型主机到小型微机，也衍生了C语言的很多不同版本。\
&#x20;        1983年，美国国家标准协会（ANSI）成立了一个委员会X3J11，来制定 C语言标准。\
&#x20;       **1989年，美国国家标准协会（ANSI）通过了C语言标准，被称为ANSI X3.159-1989 "Programming Language C"。因为这个标准是1989年通过的，所以一般简称C89标准。有些人也简称ANSI C，因为这个标准是美国国家标准协会（ANSI）发布的。**\
&#x20;        1990年，国际标准化组织（ISO）和国际电工委员会（IEC）把C89标准定为C语言的国际标准，命名为ISO/IEC 9899:1990 - Programming languages -- C 。因为此标准是在1990年发布的，所以有些人把简称作C90标准。不过大多数人依然称之为C89标准，因为此标准与ANSI C89标准完全等同。\
&#x20;        1994年，国际标准化组织（ISO）和国际电工委员会（IEC）发布了C89标准修订版，名叫ISO/IEC 9899:1990/Cor 1:1994，有些人简称为C94标准。\
&#x20;        1995年，国际标准化组织（ISO）和国际电工委员会（IEC）再次发布了C89标准修订版，名叫ISO/IEC 9899:1990/Amd 1:1995 - C Integrity，有些人简称为C95标准。\
&#x20;        **1999年1月，国际标准化组织（ISO）和国际电工委员会（IEC）发布了C语言的新标准，名叫ISO/IEC 9899:1999 - Programming languages -- C，简称C99标准。这是C语言的第二个官方标准。**\
&#x20;        **2011年12月8日，国际标准化组织（ISO）和国际电工委员会（IEC）再次发布了C语言的新标准，名叫ISO/IEC 9899:2011 - Information technology -- Programming languages -- C ，简称C11标准，原名C1X。这是C语言的第三个官方标准，也是C语言的最新标准。**\
不同的机器平台的编译器可能不同，但是他**必须按照ISO C的标准**来实现，即他必须支持对于C标准的语法规则的编译。

## :pencil2: GNU

**GNU**：它是GNU's Not Unix的缩写，实际上GNU就是一个自由软件操作系统，GNU操作系统包括GNU软件包（专门由GNU工程发布的程序）和由第三方发布的自由软件。GNU 是由自由软件基金会 （Free SoftwareFoundation，FSF） 于1984年发起的，称为GNU工程。

**GNU/Linux**：GNU是一个类Unix操作系统。它是各种软件包的集合，包括：应用程序、系统库、开发工具以及游戏等等。GNU的许多程序在GNU工程下发布；我们称之为GNU软件包。类Unix操作系统中用于资源分配和硬件管理的程序称为“内核”。GNU 的内核尚未完成，所以 GNU所用的典型内核是Linux，该组合叫做GNU/Linux操作系统。GNU软件包列表：该系统的基本组成包括GNU编译器套装（GCC）、GNU的C库（Glibc）、以及GNU核心工具组（coreutils）、（GDB）。

**GNU C：**&#x47;NU C 实际上指的是GNU C库（GNU C Library，又称为Glibc），即C运行库。Glibc是linux系统中最底层的api，几乎其它任何运行库都会依赖于Glibc。它定义了 ISO C 标准指定的所有的库函数，以及由 POSIX 或其他 UNIX 操作系统变种指定的附加特色，还包括有与 GNU 系统相关的扩展。关于GNU C library的具体介绍参见[http://www.gnu.org/software/libc/manual/html\_mono/libc.html#Introduction](http://www.gnu.org/software/libc/manual/html_mono/libc.html#Introduction)

Glibc 基于如下标准：

* ISO C: C 编程语言的国际标准。
* POSIX：GNU C 函数库实现了 ISO/IEC 9945-1:1996 （POSIX 系统应用程序编程接口， 即 POSIX.1）指定的所有函数。该标准是对 ISO C 的扩展，包括文件系统接口原语、设备相关的终端控制函数以及进程控制函数。同时，GUN C 函数库还支持部分由 ISO/IEC 9945-2:1993（POSIX Shell 和 工具标准，即 POSIX.2）指定的函数， 其中包括用于处理正则表达式和模式匹配的函数。
* Berkeley Unix：BSD 和 SunOS。GNU C 函数库定义了某些 UNIX 版本中尚未标准化的函数， 尤其是 4.2 BSD, 4.3 BSD, 4.4 BSD Unix 系统（即“Berkeley Unix”)以及“SunOS” （流行的 4.2 BSD 变种，其中包含有某些 Unix System V 的功能）。BSD 函数包括 符号链接、select 函数、BSD 信号处理函数以及套接字等等。
* SVID：System V 的接口描述。GNU C 函数库定义了大多数由 SVID 指定而未被 ISO C 和 POSIX 标准指定的函数。来自 System V 的支持函数包括进程间通信和共享内存、 hsearch 和 drand48 函数族、fmtmsg 以及一些数学函数。
* XPG：X/Open 可移植性指南（由 X/Open Company, Ltd.出版）。GNU C 函数库遵循 X/Open 可移植性指南（Issue 4.2） 以及所有的 XSI（X/Open 系统接口）兼容系统的扩展，同时也遵循所有的 X/Open Unix 扩展。X/Open 拥有 Unix 的版权，而 XPG 则指定成为 Unix 操作系统必须满足的要求。

## :pencil2: `POSIX`

`POSIX`表示可移植操作系统接口（Portable Operating System Interface of UNIX，缩写为 `POSIX` ），`POSIX`标准定义了操作系统应该为应用程序提供的接口标准，是IEEE为要在各种UNIX操作系统上运行的软件而定义的一系列API标准的总称。其正式称呼为IEEE Std 1003，而国际标准名称为ISO/IEC 9945。

`POSIX`标准意在期望获得源代码级别的软件可移植性。换句话说，为一个`POSIX`兼容的操作系统编写的程序，应该可以在任何其它的`POSIX`操作系统（即使是来自另一个厂商）上编译执行。

`POSIX`只定义接口，不定义具体实现,即定义了头文件 `*.h`，源文件`*.c`或库文件由各个提供商提供。

`POSIX`是标准C的超集，意味着标准C的函数都属于`POSIX`，可以直接使用这些函数，比如`stdio.h`中的`printf`、`scanf`，`pthread.h`中的`pthread_create`等。

`POSIX`主要由四部分组成：

1. `XBD(Base Definitions volume)`：包含一些通用的术语、概念、接口以及工具函数(`cd`，`mkdir`，`cp`，`mv`等)和头文件定义(`stdio.h`，`stdlib.h`，`pthread.h`等)。
2. `XSH(System Interface volume)`：包含系统服务函数的定义,例如线程、套接字、标准IO、信号处理、错误处理等。
3. `XCU(Shell and Utilities volume)`：包含shell脚本书写的语法、关键字以及工具函数(`break`，`cd`，`cp`，`continue`，`pwd`，`return`)的定义。
4. `XRAT(Rationale volume)`：包含与本标准有关的历史信息以及采用或舍弃某功能的扩展基本原理。

## :pencil2: 标准库

C标准库是严格按照C标准规范实现的一个C库，在Linux下有个叫libc的库就是标准库了。有些Linux下可能已经和glibc打包到一起了。起初的C标准库存在15个头文件，目前已经扩展到28项，每一个部分都有一个头描述。标准头主要由函数原型、类型定义以及宏定义组成。

| 标准头名         | 功能     | 描述                                                        |
| ------------ | ------ | --------------------------------------------------------- |
| `<assert.h>` | 诊断     | 仅包含`assert`宏。可以在程序中使用该宏来诊断程序状态（例如某个变量是否为0等），若检查失败，程序终止。   |
| `<ctype.h>`  | 字符处理   | 包含判断字符类型及大小写转换的函数。                                        |
| `<errno.h>`  | 错误监测   | 提供了`errno`。可以在调用特定库函数后检测`errno`的值以判断调用过程中是否有错误发生。         |
| `<float.h>`  | 浮点数特性  | 提供了描述浮点数特性的宏。                                             |
| `<limits.h>` | 整型特性   | 提供了描述整数类型和字符类型特性的宏。                                       |
| `<locale.h>` | 本地化    | 提供了一些支持程序国际化的函数。                                          |
| `<math.h>`   | 数学计算   | 提供了大量用以数学计算的函数。                                           |
| `<setjmp.h>` | 非本地跳转  | 提供了用于绕过正常的函数返回机制，从一个函数跳转到另一个正在活动的函数的`setjmp`和`longjmp`函数。 |
| `<signal.h>` | 信号处理   | 提供了包括中断和运行时错误在内的异常情况处理函数。                                 |
| `<stdarg.h>` | 不定参数   | 提供了支持函数处理不变个数的参数的工具。                                      |
| `<stddef.h>` | 常用定义   | 提供了常用的类型和宏。                                               |
| `<stdio.h>`  | 输入输出   | 提供了大量输入输出函数。                                              |
| `<stdlib.h>` | 常用实用函数 | 提供了大量实用的函数。                                               |
| `<string.h>` | 字符串处理  | 提供了大量字符串处理函数。                                             |
| `<time.h>`   | 日期和时间  | 提供了获取、操纵和处理日期的函数。                                         |

