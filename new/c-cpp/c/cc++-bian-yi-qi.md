# C/C++编译器

## 一、GCC（GNU Compiler Collection） (\*)&#x20;

官方网站: https://gcc.gnu.org/&#x20;

GCC有Windows移植版本，比较出名的就是MinGW和TDM-GCC&#x20;

#### 1. MinGW ：Minimalistic GNU for Windows&#x20;

http://www.mingw.org/ (win)&#x20;

最初，MinGW的目标定位为：Windows下的开源的开发环境。它包括一系列软件：编译工具、链接工具、转储工具、调试工具、和其它开发工具等。另一方面，MinGW还提供一些开源的基础支持库，像GNU的C/C++ RT库（[libc](http://www.gnu.org/software/libc/)、[libstdc++](http://gcc.gnu.org/libstdc++/)），POSIX的基本调用界面（包括pthread），甚至还有OpenGL和Windows API的调用接口等。几乎所有MinGW中的软件都是从GNU项目或Linux下移植到Windows下的。后来MinGW有了一个子项目叫：[MSYS](http://www.mingw.org/wiki/msys)，其中包括了更多的Linux工具，其目标类似Cygwin：构建一套Windows下的Linux模拟环境。

#### 2. TDM-GCC

[http://tdm-gcc.tdragon.net/download](http://tdm-gcc.tdragon.net/download)  (win)

#### 3. Cygwin：[http://www.cygwin.com/](http://www.cygwin.com/)  (win)

这个项目的名字来源于：GNU、Cygnus、Windows，3者的缩写。Cygwin的目标是：构建一套Windows下的Linux模拟环境。因此，Cygwin是一个庞大的项目，不只包括Linux下的开发环境，也包括工作环境，和各种各样的Linux下的软件。在早期，Cygwin的核心是cygwin1.dll，可以认为它是一个POSIX界面的实现，依靠这个动态链接库，Unix/Linux下的软件 可以很容易的移植到Windows下，并且风格保持原有的不变。不过随着Cygwin的发展，越来越多的Unix/Linux程序的移植，建立基于 Cygwin的复杂程序依赖的库也越来越多，现在装完默认配置的Cygwin后，就会发现有很多cyg打头的动态链接库。目前Cygwin由[RedHat](http://www.redhat.com/services/custom/cygwin/)维护和支持。在版权上，由于Cygwin不是一个软件，而是由成百上千的软件堆砌起来的系统，里面有商业软件的成分/概念，所以它的许可证有开源性质的（GPL）和商业性质的（[从RedHat购买](http://cygwin.com/licensing.html)）两种。

#### 4. gnuwin32

[http://gnuwin32.sourceforge.net](http://gnuwin32.sourceforge.net/)  (win)

#### 5. DJGPP (win)&#x20;

#### 6. 还有正宗的GNU GCC 2.9.5.5——3.0.0.4版本&#x20;

| 特点       | CYGWIN                                                                     | MINGW/MSYS                       |
| -------- | -------------------------------------------------------------------------- | -------------------------------- |
| 是否GNU    | 否                                                                          | 是                                |
| 更多软件支持？  | 支持绝大多数的GNU软件                                                               | 支持常用软件，git、Vim等软件需要独立支持(详细介绍见下方） |
| 更类Linux？ | Cygwin在Windows中就好像Wine在Linux中                                              | 实现了Bash等主要的Linux程序               |
| GCC编译    | 内含MingGW32交叉编译功能，既支持依赖cygwin1.dll的程序编译，也支持独立的Windows程序编译；可以直接编译Linux下的应用程序 | 支持独立的Windows程序编译                 |
| 中文支持     | 直接支持中文显示和输入法                                                               | 需要配置才能支持中文显示和输入，删除一个中文字符需要删除2次   |
| 运行速度     | 慢                                                                          | 快                                |

\
GNU编译器套件（GNU Compiler Collection）包括C、C++、Objective-C、Fortran、[Java](http://lib.csdn.net/base/17)、Ada和Go语言的前端，也包括了这些语言的库（如libstdc++、libgcj等等）。GCC的初衷是为GNU操作系统专门编写的一款编译器。GNU系统是彻底的自由软件。此处，“自由”的含义是它尊重用户的自由。

## 二、llvm+Clang

LLVM官方网站：[http://llvm.org/](http://llvm.org/)

Clang官方网站：[http://clang.llvm.org/get\_started.html](http://clang.llvm.org/get_started.html)    &#x20;

LLVM是构架编译器(compiler)的框架系统，以C++编写而成，用于优化以任意程序语言编写的程序的编译时间(compile-time)、链接时间(link-time)、运行时间(run-time)以及空闲时间(idle-time)，对开发者保持开放，并兼容已有脚本。LLVM计划启动于2000年，最初由University of Illinois at Urbana-Champaign的Chris Lattner主持开展。2006年Chris Lattner加盟Apple Inc.并致力于LLVM在Apple开发体系中的应用。Apple也是LLVM计划的主要资助者。    &#x20;

Low Level Virtual Machine (LLVM) 是一个开源的编译器[架构](http://lib.csdn.net/base/16)，它已经被成功应用到多个应用领域。Clang ( 发音为 /klæŋ/) 是 LLVM 的一个编译器前端，它目前支持 C, C++, Objective-C 以及 Objective-C++ 等编程语言。Clang 对源程序进行词法分析和语义分析，并将分析结果转换为 Abstract Syntax Tree ( 抽象语法树 ) ，最后使用 LLVM 作为后端代码的生成器。   &#x20;

Clang 的开发目标是提供一个可以替代 GCC 的前端编译器。与 GCC 相比，Clang 是一个重新设计的编译器前端，具有一系列优点，例如模块化，代码简单易懂，占用内存小以及容易扩展和重用等。由于 Clang 在设计上的优异性，使得 Clang 非常适合用于设计源代码级别的分析和转化工具。Clang 也已经被应用到一些重要的开发领域，如 Static Analysis 是一个基于 Clang 的静态代码分析工具。

## 三、Watcom C/C++   (\*)

官方网站：[http://www.openwatcom.org/index.php/Download](http://www.openwatcom.org/index.php/Download)   &#x20;

在DOS开发环境中，Watcom C/C++ 编译器 以编译后的exe运行高速而著称，且首个支持Intel 80386 "保护模式"的编译器。于90年代中期，大批的雄心技术游戏(例如 Doom、Descent、Duke Nukem 3D 都以 Watcom C 写成）   &#x20;

Watcom C/C++ 编译器、Watch Fortran 编译器 经已在不其先前所属公司Sybase售卖, 而被 SciTech 软件公司 作为 Open Watcom 开源包 发行。类似于其他的开源编译器(例如 \[GCC])项目，Watcom C代码小而便携, 其编译器后端(代码生成器)的目标码可变。该编译器可在DOS、OS/2、Windows等操作系统上运行，并生成各种可运行的(不必是该操作系统的)代码。该编译器支持Novell NetWare的 NLM 目标码。目前正进行 为 Linux\[1] 、modern BSD (例如FreeBSD) 操作系统 重定目标码, 以便在 x86、PowerPC 及　其它处理器上运行。Open Watcom C/C++ 的1.4版于2005年12月发行，采用 Linux x86 为实验目标, 支持NT、OS/2等host平台. 曾有某被弃置的QNX版本，但其编译所须的库并未开源发行。当前最近的稳定版是1.9版，在2010年6月发行。

## 四、Digital Mars

官方网站：[http://www.digitalmars.com/](http://www.digitalmars.com/)    &#x20;

DigitalMars是一款高性能的编译器，功能包含，快速编译/链接时、强大的优化技术、Contract设计、完整的资源库、浏览HTML文档，反汇编、库、资源编译器等。命令行及GUI版本、教程、代码示例、在线更新、标准模板库等等。

## 五、MS系列  &#x20;

1.MSC 5.0、6.0、7.0  &#x20;

2.MSQC 1.0、2.5 &#x20;

3.MSVC 1.0、4.2、6.0、7.0 &#x20;

4.[Visual C++](http://baike.baidu.com/view/100377.htm)    &#x20;

VC++6.0对标准化C++的兼容仅达83.43%。它是Visual Studio、Visual Studio.net 2002、Visual Studio.net 2003、Visual Studio.net 2005的后台C++[编译器](http://baike.baidu.com/view/487018.htm)。随着Stanley Lippman等编译器设计大师的加盟，它变得非常成熟可靠了。Visual C++ 7.1对标准C++的兼容性达到98.22%。   &#x20;

与Visual Studio集成发布，微软自己的编译器，VS是一个基本完整的开发工具集，它包括了整个软件生命周期中所需要的大部分工具，如UML工具、代码管控工具、集成开发环境(IDE)等等。所写的目标代码适用于微软支持的所有平台，包括Microsoft Windows、Windows Mobile、Windows CE、.NET Framework、.NET Compact Framework和Microsoft Silverlight 及Windows Phone。

## 六、Borland系列（turbo c和Borland C++）   (\*)&#x20;

1.TC 1.0、2.0  &#x20;

2.TC++ 1.01、3.0 &#x20;

3.BC 3.0、3.1、4.0、4.5、5.0、5.02  &#x20;

4.BCB 3.0、5.0、6.0&#x20;

5.Borland C++   &#x20;

该编译以速度快、空间效率高而著称。它的5.5版本对标准化C++的支持达92.73%，而官方称100%符合ANSI/ISO的C++标准和C99标准。它是Borland公司开发的，是Borland C++ Builder和Borland C++ Builder X这两种IDE的后台编译器。

Borland C++ Builder Compiler 是一个 BC编译器。它是用来优化 BC 开发系统的工具。它包括最后版本的 ANSI/ISO C++ 语言的支持，包括 RTL，C++ 的 STL框架结构支持。Turbo C（TC）是其早期的命令行编译器作品。

## 七、Intel C++

Intel C++ Compiler （简称 icc 或 icl）是美国 Intel 公司开发的 C/C++编译器，适用于 Linux、Microsoft Windows 和 Mac OS X 操作系统。     Intel 编译支持 IA-32、Intel 64、Itanium 2、Intel Atom 处理器和某些非 Intel 的兼容处理器（例如某些 AMD 处理器）。开发人员应当检查系统需求。适用于 IA-32 和 Intel 64 的 Intel C++ 编译器的主要特点是自动向量化器，它能够生成 SSE、SSE2 和 SSE3 的 SIMD 指令及其适用于 Intel 无线 MMX 和 MMX 2 的嵌入式变种。    &#x20;

Intel C++ Compiler 进一步支持 OpenMP 3.0 和适用于对称多处理的自动并行化。借助于 Cluster OpenMP 的附加能力，编译器还可为分布存储多处理根据 OpenMP 指示自动生成消息传递接口调用。    &#x20;

Intel C++ Compiler 可通过四种方式获得，它分别是 Intel Parallel Studio、Intel C++ Compiler 专业版、Intel 编译器套装和 Intel Cluster Toolkit 编译器版的一部分。该编译器的最新发布是 Intel C++ Compiler 14.0 版本。

## 八、TCC(Tiny C Compiler)

官方网站：[http://bellard.org/tcc/](http://bellard.org/tcc/)   &#x20;

Tiny C Compiler（缩写为TCC, tCc或TinyCC）用于x86（16/32位）或是x86-64（64位）系统的C compiler，而开发者为Fabrice Bellard。软件是设计用于低级电脑环境，或是于磁盘容量有限的空间中（1.44磁片或是硬盘）。软件可以适用于Windows、Linux、Unix操作系统，而最新版本为0.9.26（Feb 15, 2013）。TCC是在GNU宽通用公共许可证（LGPL）协议规范下发布。作者是大神法布里斯·贝拉（FabriceBellard）。    TCC符合ANSI C（C89/C90）规范，Tiny C Compiler Reference Documentation accessed on 2008-08-07]亦符合新版的ISO C99标准规范，与GNU C扩展的内嵌汇编语言（即inline assembler，内联汇编大陆用语）功能汇编语言。而Google Andriod系统内亦曾经内置于其中，于Andriod 2.0版本中。\
其他一些没有详细解释的编译器VectorC1.3.3，IBM Visual Agefor C++，KAIC/C++4.03fforRedHat7.2，Lcc4.1，LCC-WIN32，SmallC，CC386，PacificC另外还有C的解释器Quincy，Eic，CINT



