# C标准库之stdarg

## ✏ 1、可变长参数

C语言支持可变长参数，正常情况下C的函数参数入栈规则为`__stdcall`，它是从右到左的，即函数中的最右边的参数最先入栈。例如，对于函数：

```cpp
void func(int a, char b, int c, double d, int e) {
    int f = 0;
    printf("&a = 0x%p\n", &a);
    printf("&b = 0x%p\n", &b);
    printf("&c = 0x%p\n", &c);
    printf("&d = 0x%p\n", &d);
    printf("&e = 0x%p\n", &e);
    printf("&f = 0x%p\n", &f);
}
// 输出
&a = 0x0058F968   
&b = 0x0058F96C  // 4  字对齐
&c = 0x0058F970  // 4
&d = 0x0058F974  // 4
&e = 0x0058F97C  // 8
&f = 0x0058F954  
```

用户栈是从上往下生长的，先占用高地址的空间，再占用低地址空间。对于在32位系统的多数编译器，每个栈单元的大小都是`sizeof(int)`，而函数的每个参数都至少要占一个栈单元大小。对于固定参数列表的函数，每个参数的名称、类型都是直接可见的，他们的地址也都是可以直接得到的。

按照C标准的说明，支持变长参数的函数在原型声明中，**必须有至少一个最左固定参数**\(这一点与传统C有区别，传统C允许不带任何固定参数的纯变长参数函数\)，这样我们可以得到其中固定参数的地址，根据上面的参数入栈顺序，我们可尝试写一个可变长参数的函数：

```cpp
void var_args_func(const char* fmt, ...)
{
    char* ap;
    ap = ((char*)&fmt) + sizeof(fmt);
    printf("%d\n", *(int*)ap);
    ap = ap + sizeof(int);
    printf("%d\n", *(int*)ap);
    ap = ap + sizeof(int);
    printf("%s\n", *((char**)ap));
}

int main()
{
    var_args_func("%d %d %s\n", 4, 5, "hello world");
    return 0;
}
```

解释：用`ap`获取第一个变参的地址，我们知道第一个变参是4，一个int 型，所以我们用`(int)ap`以告诉编译器，以`ap`为首地址的那块内存我们要将之视为一个整型来使用，`(int)ap`获得该参数的值；接下来的变参是5，又一个int型，其地址是`ap + sizeof(第一个变参)`，也就是`ap + sizeof(int)`，同样我们使用`(int)ap`获得该参数的值；最后的一个参数是一个字符串，也就是`char`，与前两个`int`型参数不同的是，经过`ap + sizeof(int)`后，`ap`指向栈上一个`char`类型的内存块\(我们暂且称之`tmp_ptr`\)的首地址，即`ap -> &tmp_ptr`，而我们要输出的不是`printf("%s\n", ap)`，而是`printf("%s\n", tmp_ptr)`；`printf("%s\n", ap)`是意图将`ap`所指的内存块作为字符串输出了，但是`ap -> &tmp_ptr`，`tmp_ptr`所占据的4个字节显然不是字符串，而是一个地址。如何让`&tmp_ptr`是`char`类型的，我们将`ap`进行强制转换`(char)ap <=> &tmp_ptr`，这样我们访问`tmp_ptr`只需要在`(char`**`)ap`**前面加上一个即可，即`printf("%s\n", (char)ap)`。

> 从理论上看，该程序没有问题，但是在GCC上不能输出预期的结果，在VC上可以按预期输出。

## ✏ 2、内存对齐

### 🖋 2.1、字节对齐

现代计算机中内存空间都是按照byte划分的，从理论上讲似乎对任何类型的变量的访问可以从任何地址开始，但实际情况是在访问特定类型变量的时候经常在特定的内存地址访问，这就需要各种类型数据按照一定的规则在空间上排列，而不是顺序的一个接一个的排放，这就是对齐。

各个硬件平台对存储空间的处理上有很大的不同。一些平台对某些特定类型的数据只能从某些特定地址开始存取。比如有些架构的CPU在访问一个没有进行对齐的变量的时候会发生错误，那么在这种架构下编程必须保证字节对齐。其他平台可能没有这种情况，但是最常见的是如果不按照适合其平台要求对数据存放进行对齐，会在存取效率上带来损失。比如有些平台每次读都是从偶地址开始，如果一个int型（假设为32位系统）如果存放在偶地址开始的地方，那 么一个读周期就可以读出这`32bit`，而如果存放在奇地址开始的地方，就需要2个读周期，并对两次读出的结果的高低字节进行拼凑才能得到该`32bit`数据，显然在读取效率上下降很多。

```cpp
struct Student
{
    double score;
    char sex;
    int ID;
};

sizeof(struct Student) = 16
```

其实，这是`VC`对变量存储的一个特殊处理。为了提高CPU的存储速度，`VC`对一些变量的起始地址做了“对齐”处理。在默认情况下，`VC`规定各成员变量存放的起始地址相对于结构的起始地址的偏移量必须为该变量的类型所占用的字节数的倍数。下面列出常用类型的对齐方式\(`vc6.0,32位系统`\)：

* char：偏移量必须为sizeof\(char\)即1的倍数
* int：偏移量必须为sizeof\(int\)即4的倍数
* float：偏移量必须为sizeof\(float\)即4的倍数
* double：偏移量必须为sizeof\(double\)即8的倍数
* Short：偏移量必须为sizeof\(short\)即2的倍数

各成员变量在存放的时候根据在结构中出现的顺序依次申请空间，同时按照上面的对齐方式调整位置，空缺的字节`VC`会自动填充。同时`VC`为了确保结构的大小为结构的字节边界数（即该结构中占用最大空间的类型所占用的字节数）的倍数，所以在为最后一个成员变量申请空间后，还会根据需要自动填充空缺的字节。

```cpp
struct Student
{
    char sex;     //偏移量为0，满足对齐方式，cda占用1个字节；
    double score; //下一个可用的地址的偏移量为1，不是sizeof(double)=8 
                  //的倍数，需要补足7个字节才能使偏移量变为8（满足对齐 
                  //方式），因此VC自动填充7个字节，dda存放在偏移量为8 
                  //的地址上，它占用8个字节。 
    int ID;  //下一个可用的地址的偏移量为16，是sizeof(int)=4的倍 
             //数，满足int的对齐方式，所以不需要VC自动填充，type存 
             //放在偏移量为16的地址上，它占用4个字节。
             
    //所有成员变量都分配了空间，空间总的大小为1+7+8+4=20，不是结构 
    //的节边界数（即结构中占用最大空间的类型所占用的字节数sizeof 
    //(double)=8）的倍数，所以需要填充4个字节，以满足结构的大小为 
    //sizeof(double)=8的倍数。
};

sizeof(struct Student) = 24
```

### 🖋 2.2、自定义对齐

`VC`对结构的存储的特殊处理确实提高CPU存储变量的速度，但是有时候也带来了一些麻烦，我们也屏蔽掉变量默认的对齐方式，自己可以设定变量的对齐方式。`VC` 中提供了`#pragma pack(n)`来设定变量以n字节对齐方式。n字节对齐就是说变量存放的起始地址的偏移量有两种情况：

1. 如果n大于等于该变量所占用的字节数，那么偏移量必须满足默认的对齐方式；
2. 如果n小于该变量的类型所占用的字节数，那么偏移量为n的倍数，不用满足默认的对齐方式。

结构的总大小也有个约束条件，分下面两种情况：如果n大于所有成员变量类型所占用的字节数，那么结构的总大小必须为占用空间最大的变量占用的空间数的倍数；否则必须为n的倍数。下面举例说明其用法：

```cpp
#pragma pack(push) //保存对齐状态 
#pragma pack(4)    //设定为4字节对齐 
struct Student
{
    char sex;
    double score;
    int ID;
};
#pragma pack(pop)  //恢复对齐状态 

sizeof(struct Student) = 16
```

首先为sex分配空间，其偏移量为0，满足我们自己设定的对齐方式（4字节对齐），sex占用1个字节。接着开始为 score 分配空间，这时其偏移量为1，需要补足3个字节，这样使偏移量满足为`n=4`的倍数（因为sizeof\(double\)大于n），score 占用8个字节。接着为ID分配空间，这时其偏移量为12，满足为4的倍数，ID占用4个字节。这时已经为所有成员变量分配了空间，共分配了4+8+4=16个字节，满足为n的倍数。如果把上面的`#pragma pack(4)`改为`#pragma pack(16)`，那么我们可以得到结构的大小为24。再看下面这个例子：

```cpp
#pragma pack(push) 
#pragma pack(16)   
struct Score
{
    int prj;
    double number;
};

struct Student
{
    char sex;            // 1 -> 8
    struct Score score;  // 8 * 2
    int ID;              // 4 -> 8
};
#pragma pack(pop)   

sizeof(struct Student) = 32
```

这里有三点很重要:

1. 每个成员分别按自己的方式对齐,并能最小化长度。
2. 复杂类型\(如结构\)的默认对齐方式是它最长的成员的对齐方式,这样在成员是复杂类型时,可以最小化长度。
3. 对齐后的长度必须是成员中最大的对齐参数的整数倍,这样在处理数组时可以保证每一项都边界对齐。

### 🖋 2.3、可移植性

C标准库在`stdarg.h`中定义了如下一个宏：

```cpp
/* Amount of space required in an argument list for an arg of type TYPE.
 * TYPE may alternatively be an expression whose type is used.
 */

#define __va_rounded_size(TYPE)  \
  (((sizeof (TYPE) + sizeof (int) - 1) / sizeof (int)) * sizeof (int))
```

n字节对齐就是说变量存放的起始地址的偏移量有两种情况：

1. 第一、如果n大于等于该变量所占用的字节数，那么偏移量必须满足默认的对齐方式（各成员变量存放的起始地址相对于结构的起始地址的偏移量必须为该变量的类型所占用的字节数的倍数）；
2. 第二、如果n小于该变量的类型所占用的字节数，那么偏移量为n的倍数，不用满足默认的对齐方式。

此时`n = 4`，对于`sizeof(TYPE)`一定为自然数，`sizeof(int) - 1 = 3`，`sizeof(TYPE)`只可能出现如下两种情况：

1. 当`sizeof(TYPE) >= 4`，`偏移量 = (sizeof(TYPE)/4)*4`
2. 当`sizeof(TYPE) < 4`，`偏移量 = 4`

此时`sizeof(TYPE) = 1 or 2 or 3`，而`(sizeof(TYPE) + 3) / 4  = 1`，为了将上述两种情况统一，`偏移量 = ((sizeof(TYPE) + 3) / 4) * 4`。在有的源代码中，将内存对齐宏`__va_rounded_size`通过位操作来实现，代码如下：

```cpp
#define __va_rounded_size(TYPE)  \
   ((sizeof(TYPE)+sizeof(int)-1)&~(sizeof(int)-1))
```

由于 `~(sizeof(int) – 1) ) = ~(4-1)=~（00000011B）=11111100B`

`(sizeof(TYPE) + sizeof(int) – 1)`就是将大于`4m`但小于等于`4(m+1)`的数提高到大于等于`4(m+1)`但小于`4(m+2)`，这样再`& ~(sizeof(int) – 1) )`后就正好将原长度补齐到4的倍数了。

## ✏ 3、stdarg

最前面的例子在GCC和VC的表现不一样，主要是编译器的优化导致的，这里重新提供一种写法：

```cpp
#define __va_rounded_size(TYPE)  \
  (((sizeof (TYPE) + sizeof (int) - 1) / sizeof (int)) * sizeof (int))

void var_args_func_round(const char* fmt, ...)
{
    char* ap;

    ap = ((char*)&fmt) + sizeof(fmt);
    printf("%d\n", *(int*)ap);

    ap = (char*)ap + sizeof(int) + __va_rounded_size(int);
    printf("%d\n", *(int*)ap);

    ap = ap + sizeof(int) + __va_rounded_size(int);
    printf("%s\n", *((char**)ap));
}

int main()
{
    var_args_func_round("%d %d %s\n", 4, 5, "hello world");
    return 0;
}
```

> 该程序在GCC上能输出预期的结果，但在VC上不能按预期输出。

C标准库在`stdarg.h`中提供了诸多便利以供实现变长长度参数时使用：宏`va_start`、`va_arg`和`va_end`；在ANSI C标准下，三个宏的原型如下：

```cpp
void va_start(va_list ap, last);// 取第一个可变参数（如上述printf中的i）的指针给ap，
				                        // last是函数声明中的最后一个固定参数（比如printf函数原型中的*fromat）；
type va_arg(va_list ap, type);	// 返回当前ap指向的可变参数的值，然后ap指向下一个可变参数；
				                        // type表示当前可变参数的类型（支持的类型位int和double）；
void va_end(va_list ap);	      // 将ap置为NULL
```

借助这三个宏重新实现上述函数的功能：

```cpp
void std_vararg_func(const char* fmt, ...) {
    va_list ap;
    va_start(ap, fmt);

    printf("%d\n", va_arg(ap, int));
    printf("%f\n", va_arg(ap, double));
    printf("%s\n", va_arg(ap, char*));

    va_end(ap);
}

int main()
{
    std_vararg_func("%d %d %s\n", 4, 5.0, "hello world");
    return 0;
}
```

> 该程序在GCC和VC下都是可以正确输出的。

对比一下 `std_vararg_func`和`var_args_func`的实现，`va_list`似乎就是`char*`， `va_start`似乎就是 `((char*)&fmt) + sizeof(fmt)`，`va_arg`似乎就是得到下一个参数的首地址。没错，多数平台下`stdarg.h`中`va_list`，`va_start`和`var_arg`的实现就是类似这样的。

下面我们来探讨如何写一个简单的可变参数的C 函数：

使用可变参数应该有以下步骤：

1. 首先在函数里定义一个`va_list`型的变量，这里是`arg_ptr`，这个变量是指向参数的指针。
2. 然后用`va_start`宏初始化变量`arg_ptr`，这个宏的第二个参数是第一个可变参数的前一个参数，是一个固定的参数。
3. 然后用`va_arg`返回可变的参数，并赋值给整数`j`。 `va_arg`的第二个参数是你要返回的参数的类型，这里是`int`型。
4. 最后用`va_end`宏结束可变参数的获取，然后你就可以在函数里使用第二个参数了，如果函数有多个可变参数的，依次调用`va_arg`获取各个参数。

在《C程序设计语言》中，`Ritchie`提供了一个简易版`printf`函数：

```cpp
#include<stdarg.h>

void minprintf(char *fmt, ...)
{
    va_list ap;
    char *p, *sval;
    int ival;
    double dval;

    va_start(ap, fmt);
    for (p = fmt; *p; p++) {
        if(*p != '%') {
            putchar(*p);
            continue;
        }
        switch(*++p) {
        case 'd':
            ival = va_arg(ap, int);
            printf("%d", ival);
            break;
        case 'f':
            dval = va_arg(ap, double);
            printf("%f", dval);
            break;
        case 's':
            for (sval = va_arg(ap, char *); *sval; sval++)
                putchar(*sval);
            break;
        default:
            putchar(*p);
            break;
        }
    }
    va_end(ap);
}
```

### 🖋 总结

1、标准C库的中的三个宏的作用只是用来确定可变参数列表中每个参数的内存地址，编译器是不知道参数的实际数目的。

2、在实际应用的代码中，程序员必须自己考虑确定参数数目的办法，如 ⑴在固定参数中设标志-- `printf`函数就是用这个办法。 ⑵在预先设定一个特殊的结束标记，就是说多输入一个可变参数，调用时要将最后一个可变参数的值设置成这个特殊的值，在函数体中根据这个值判断是否达到参数的结尾。本文前面的代码就是采用这个办法. 无论采用哪种办法，程序员都应该在文档中告诉调用者自己的约定。

3、实现可变参数的要点就是想办法取得每个参数的地址，取得地址的办法由以下几个因素决定：

* ①函数栈的生长方向 
* ②参数的入栈顺序 
* ③CPU的对齐方式 
* ④内存地址的表达方式 

结合源代码，我们可以看出`va_list`的实现是由④决定的，`_INTSIZEOF(n)`的引入则是由③决定的，他和①②又一起决定了`va_start`的实现，最后`va_end`的存在则是良好编程风格的体现，将不再使用的指针设为NULL，这样可以防止以后的误操作。

4、取得地址后，再结合参数的类型，就可以正确的处理参数，写出适合于自己机器的实现来。

