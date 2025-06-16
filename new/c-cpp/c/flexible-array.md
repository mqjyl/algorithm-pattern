# 柔性数组

**在至少两个成员的结构体中，最后一个成员其类型若是不完整类型的数组类型，则该成员称为柔性数组。** 柔性数组既数组大小待定的数组，C语言中结构体的最后一个元素可以是大小未知的数组，也就是所谓的 0 长度，所以我们可以用结构体来创建柔性数组。\
典型例子：

```c
struct s { 
    int n; 
    char str[]; //str后面的中括号只能为空，数组类型不局限于char。
};
```

1. GCC编译器在C99发布之前就支持str\[0]作为“柔性数组”，而且str\[0]可以放在任何位置。这属于GCC对C语言的语法扩展。C89不支持柔性数组，C99把它作为一种特例加入了标准。但是，C99所支持的是 `incomplete type`，而不是 `zero array`，`int item[0]`的形式是非法的，C99支持的形式是 `int item[]；`只不过有些编译器把 `int item[0]`作为非标准扩展来支持，而且在C99发布之前已经有了这种非标准扩展了，C99发布之后，有些编译器把两者合而为一了。
2. 用法：在一个结构体的最后，申明一个长度为空的数组，就可以使得这个结构体是可变长的。对于编译器来说，此时长度为0的数组并不占用空间，因为数组名本身不占空间，它只是一个偏移量， 数组名这个符号本身代表了一个不可修改的地址常量 （注意：数组名永远都不会是指针！ ）对于柔性数组的这个特点，很容易构造出变长结构体，如缓冲区，数据包等等。

使用：

```c
#include<stdio.h>
#include<malloc.h>


typedef struct _SoftArray{
    int len;
    int array[];
}SoftArray;


int main()
{
    int len = 10,i = 0;
    SoftArray *p = (SoftArray*)malloc(sizeof(SoftArray)+sizeof(int)*len);
    p->len = len;
    for(i = 0;i < 11;i++)        # 11 
    {
        p->array[i] = i+1;
    }
    for(i = 0;i < 11;i++)
    {  
        printf("%d\n",p->array[i]);
    }
    free(p);
    return 0;
}
```

运行上面的代码会报错：

<img src="../../.gitbook/assets/image (68).png" alt="" data-size="original">

在动态分配的时候，会在数组界限外加一个用来标识数组范围的标志，例如a数组，就会在`array[-1]`和`array[11]`有两个标志，如果我们在这两个位置赋值，赋值和调用时并不会出错，而是在 free 掉 array 申请的内存时出错，错误的名称就是`“DAMAGE: before Normal block”`和`“DAMAGE: after Normal block”`。一般是后者居多。
