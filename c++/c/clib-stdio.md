# C标准库之stdio

## ✏ 1、函数

### 🖋 1.1、二进制IO

```cpp
size_t fread( void *buffer, size_t size, size_t count, FILE *stream );
size_t fwrite ( void *buffer, size_t size, size_t count, FILE *stream );
```

buffer是用于保存数据的内存指针，size是缓冲区字节数，count是读入/写入的元素数，stream是目标流。

### 🖋 1.2、文件访问

```cpp
// 使用给定的模式 mode 打开 filename 所指向的文件。
FILE *fopen( const char *filename, const char *mode )
// 把一个新的文件名 filename 与给定的打开的流 stream 关联，同时关闭流中的旧文件。
FILE *freopen( const char *filename, const char *mode, FILE *stream )
// 刷新流 stream 的输出缓冲区。
int fflush(FILE *stream)
// 关闭流 stream。刷新所有的缓冲区。
int fclose(FILE *stream)
```

| **打开模式** | **含义** |
| :--- | :--- |
| r或rb | 以只读方式打开一个文本文件（**不创建文件**，若文件不存在则报错） |
| w或wb | 以写方式打开文件\(如果文件存在则**清空文件**，文件不存在则**创建**一个**文件**\) |
| a或ab | 以追加方式打开文件，在末尾添加内容，若文件不存在则**创建文件** |
| r+或rb+ | 以可读、可写的方式打开文件\(不创建新文件\) |
| w+或wb+ | 以可读、可写的方式打开文件\(如果文件存在则清空文件，文件不存在则创建一个文件\) |
| a+或ab+ | 以添加方式打开文件，打开文件并在末尾更改文件,若文件不存在则创建文件 |

注意：b表示二进制模式，二进制模式只在window下有效，在linux中是无效的。linux下所有的文本文件都以\n结尾。在window下二进制读取模式时可以获得\r\n。

### 🖋 1.3、非格式化IO

```cpp
fgetc： int fgetc(FILE *stream)
从指定的流 stream 获取下一个字符（一个无符号字符），并把位置标识符往前移动。
getc： int getc(FILE *stream)
从指定的流 stream 获取下一个字符（一个无符号字符），并把位置标识符往前移动。
fputc：int fputc(int char, FILE *stream)
把参数 char 指定的字符（一个无符号字符）写入到指定的流 stream 中，并把位置标识符往前移动。
putc：int putc(int char, FILE *stream)
把参数 char 指定的字符（一个无符号字符）写入到指定的流 stream 中，并把位置标识符往前移动。
ungetc：int ungetc(int char, FILE *stream)
把字符 char（一个无符号字符）推入到指定的流 stream 中，以便它是下一个被读取到的字符。
fgets：char *fgets(char *str, int n, FILE *stream)
从指定的流 stream 读取一行，并把它存储在 str 所指向的字符串内。当读取 (n-1) 个字符
fputs：int fputs(const char *str, FILE *stream)
把字符串写入到指定的流 stream 中，但不包括空字符。
```

### 🖋 1.4、格式化IO









## ✏ 2、格式字符

| 类型 | 转换字符 |
| :--- | :--- |
| int | %d（十进制） |
| int | %o（八进制无前缀） |
| int | %\#o（八进制带前缀0） |
| int | %x（十六进制无前缀） |
| int | %\#x（十六进制带前缀0x） |
| int | %\#X（十六进制带前缀0X） |
| unsigned int | %u |
| long | %ld（十进制） |
| long | %lo（八进制无前缀） |
| long | %lx（十六进制无前缀） |
| unsigned long | %lu |
| long long | %lld |
| unsigned long long | %llu |
| short | %hd（十进制） |
| short | %ho（八进制） |
| short | %hx（十六进制） |
| char | %c |
| float | %f（十进制计数法） |
| double | %f（十进制计数法） |
| float | %a（十六进制计数法） |
| double | %a（十六进制计数法） |
| float | %e（指数计数法） |
| double | %e（指数计数法） |
| 字符串 | %s |



## ✏ 3、标准文件指针

`stdin`、`stdout`、`stderr` 就是三个文件流指针，分别表示标准输入，输出，错误输出。Linux中0就是`stdin`，表示输入流，指从键盘输入，1代表`stdout`，2代表`stderr`，1、2默认是显示器。

三个变量都是一个打开的文件的句柄，系统会为每一个进程打开这三个文件，`stdout`，`stderr`一般关联到终端，`stdin`一般关联到键盘。

