# C标准库之stdio

## :pencil2: 1、函数

### :pen\_fountain: 1.1、二进制IO

```cpp
size_t fread( void *buffer, size_t size, size_t count, FILE *stream );
size_t fwrite( void *buffer, size_t size, size_t count, FILE *stream );
```

buffer是用于保存数据的内存指针，size是缓冲区字节数，count是读入/写入的元素数，stream是目标流。

### :pen\_fountain: 1.2、文件访问

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

| **打开模式** | **含义**                                         |
| -------- | ---------------------------------------------- |
| r或rb     | 以只读方式打开一个文本文件（**不创建文件**，若文件不存在则报错）             |
| w或wb     | 以写方式打开文件(如果文件存在则**清空文件**，文件不存在则**创建**一个**文件**) |
| a或ab     | 以追加方式打开文件，在末尾添加内容，若文件不存在则**创建文件**              |
| r+或rb+   | 以可读、可写的方式打开文件(不创建新文件)                          |
| w+或wb+   | 以可读、可写的方式打开文件(如果文件存在则清空文件，文件不存在则创建一个文件)        |
| a+或ab+   | 以添加方式打开文件，打开文件并在末尾更改文件,若文件不存在则创建文件             |

注意：b表示二进制模式，二进制模式只在window下有效，在linux中是无效的。linux下所有的文本文件都以\n结尾。在window下二进制读取模式时可以获得\r\n。

### :pen\_fountain: 1.3、非格式化IO

```cpp
// 从指定的流获取下一个字符(无符号字符)，并推进流的位置指示符。
int fgetc(FILE *stream)	
// 将参数char指定的字符(无符号字符)写入指定的流，并推进流的位置指示符。
int fputc(int char, FILE *stream)	

// 从指定的流中读取一行，并将其存储到str所指向的字符串中。当读取(n-1)个字符、读取换行字符或到达文件结束符时(以先读取的字符数为准)，它将停止。
char *fgets(char *str, int n, FILE *stream)	
// 将字符串写入指定的流，但不包括空字符。
int fputs(const char *str, FILE *stream)	

// 从stdin中读取一行，并将其存储到由，str指向的字符串中。当读取换行符或到达文件结束符时(无论哪个先到达)，它都会停止。
char *gets(char *str)	
// 将一个字符串写入stdout直到但不包括空字符。一个换行字符被追加到输出。
int puts(const char *str)	

// 从指定的流获取下一个字符(无符号字符)，并推进流的位置指示符。
int getc(FILE *stream)	
// 将参数char指定的字符(无符号字符)写入指定的流，并推进流的位置指示符。
int putc(int char, FILE *stream)	


// 从stdin获取一个字符(无符号字符)。
int getchar(void)	
// 将参数char指定的字符(无符号字符)写入stdout。
int putchar(int char)	

// 将字符字符(无符号字符)推入指定的流，以便读取下一个字符。
int ungetc(int char, FILE *stream)	
```

### :pen\_fountain: 1.4、格式化IO

```cpp
// 从流中读取格式化的输入。
int fscanf(FILE *stream, const char *format, ...)	
// 将格式化输出发送到流。
int fprintf(FILE *stream, const char *format, ...)	

// 使用参数列表将格式化输出发送到流。
int vfprintf(FILE *stream, const char *format, va_list arg)	
// 使用参数列表将格式化输出发送到标准输出。
int vprintf(const char *format, va_list arg)	
// 使用参数列表将格式化输出发送到字符串。
int vsprintf(char *str, const char *format, va_list arg)	

// 从标准输入读取格式化输入。
int scanf(const char *format, ...)	
// 将格式化输出发送到标准输出。
int printf(const char *format, ...)	

// 从字符串读取格式化输入。
int sscanf(const char *str, const char *format, ...)
// 将格式化输出发送到字符串。
int sprintf(char *str, const char *format, ...)	
```

`scanf`：解包

1. 遇到空格、回车、\0、\t、\v、\f。均结束提取(简单地说就是空白字符与\0都会结束`sscanf`的提取)。
2. `sscanf(buf, "%c3%c", &ch1, &ch2);` `buf`与format字符串中有匹配项可以跳过，比如3。
3. 跳过数据：`%*d %*s`
4. 指定提取宽度：`%3s  %*2s`
5. 指定提取范围：`[a~zA~Z0~9] [aBc]`
6. 取反`%[^a]`&#x20;

`printf`：组包

1. 格式化字符串
2. 字符串连接
3. 把数字转成字符串

### :pen\_fountain: 1.5、文件定位

```cpp
int fseek( FILE *stream,long offset,int origin )
```

`fseek`函数设置流 stream 的文件位置，后续的读写操作将从新位置开始。对于二进制文件，此位置被设置为从origin开始的第offset个字符处。Origin的值可以为SEEK\_SET（文件开始处）、SEEK\_CUR（当前位置）或 SEEK\_END（文件结束处） 。对于文本流，offset必须设置为 0，或者是由函数 `ftell` 返回的值（此时 origin 的值必须是 SEEK\_SET）。 `fseek`函数在出错时返回一个非 0 值。

注：`fseek()`函数出错时返回的是非零值，不一定是-1。

```cpp
long ftell( File *stream )
```

`ftell`函数返回stream流的当前文件位置，出错时该函数返回`-1L`。

注：一般情况下，这个`-1L`与-1没有区别，但还是注意为好。

```cpp
void rewind( FILE *stream )
```

`rewind(fp)`函数等价于语句`fseek(fp, 0L, SEEK_SET);clearerr(fp);`的执行结果。(`clearerr`函数见下文)

```cpp
int fgetpos(FILE *stream,fpos_t *ptr)
```

`fgetpos` 函数把 stream 流的当前位置记录在`*ptr` 中，供随后的 `fsetpos` 函数调用使用。若出错则返回一个**非 0 值**。

```cpp
int fsetpos(FILE *stream,const fpos_t *ptr)
```

`fsetpos` 函数将流 stream 的当前位置设置为 `fgetpos` 记录在`*pt`r 中的位置。若出错则返回一个**非 0 值**。

### :pen\_fountain: 1.6、错误处理函数

当发生错误或到达文件末尾时，标准库中的许多函数都会设置状态指示符。这些状态指示符可被显式地设置和测试。另外，整型表达式 `errno`（在中声明）可以包含一个错误编号，据此可以进一步了解最近一次出错的信息。

```cpp
// clearerr 函数清除与流 stream 相关的文件结束符和错误指示符。
void clearerr(FILE *stream) 
// 如果设置了与 stream 流相关的文件结束指示符，feof 函数将返回一个非 0 值。 
int feof(FILE *stream)  
// 如果设置了与 stream 流相关的错误指示符，ferror 函数将返回一个非 0 值。
int ferror(FILE *stream)
```

```cpp
void perror(cinst char *s)
```

`perror(s)`函数打印字符串 s 以及与 `errno` 中整型值相应的错误信息，错误信息的具体内容与具体的实现有关。该函数的功能类似于执行下列语句：

```cpp
fprintf(stderr, "%s: %s\n", s, "error message"); 
```

### :pen\_fountain: 1.7、文件管理

```cpp
// 删除给定的文件名，使其不再可访问。
int remove(const char *filename)	
// 将old_filename所引用的文件名更改为new_filename。
int rename(const char *old_filename, const char *new_filename)	
// 以二进制更新模式(wb+)创建一个临时文件。
FILE *tmpfile(void)	
```

## :pencil2: 2、变量和宏

### :pen\_fountain: 2.1、变量

| 变量类型        | 描述                       |
| ----------- | ------------------------ |
| **size\_t** | 这是无符号整数类型，是sizeof关键字的结果。 |
| **FILE**    | 这是一种适合于存储文件流信息的对象类型。     |

### :pen\_fountain: 2.2、常用的宏

| 宏                               | 描述                                |
| ------------------------------- | --------------------------------- |
| **`NULL`**                      | 这个宏是一个空指针常量的值。                    |
| **`EOF`**                       | 该宏是一个负整数，表示已经到达文件结束。              |
| **`stderr, stdin, and stdout`** | 这些宏是指向与标准错误、标准输入和标准输出流对应的文件类型的指针。 |

## :pencil2: 3、格式字符

| 类型                 | 转换字符           |
| ------------------ | -------------- |
| int                | %d（十进制）        |
| int                | %o（八进制无前缀）     |
| int                | %#o（八进制带前缀0）   |
| int                | %x（十六进制无前缀）    |
| int                | %#x（十六进制带前缀0x） |
| int                | %#X（十六进制带前缀0X） |
| unsigned int       | %u             |
| long               | %ld（十进制）       |
| long               | %lo（八进制无前缀）    |
| long               | %lx（十六进制无前缀）   |
| unsigned long      | %lu            |
| long long          | %lld           |
| unsigned long long | %llu           |
| short              | %hd（十进制）       |
| short              | %ho（八进制）       |
| short              | %hx（十六进制）      |
| char               | %c             |
| float              | %f（十进制计数法）     |
| double             | %f（十进制计数法）     |
| float              | %a（十六进制计数法）    |
| double             | %a（十六进制计数法）    |
| float              | %e（指数计数法）      |
| double             | %e（指数计数法）      |
| 字符串                | %s             |

## :pencil2: 4、标准文件指针

`stdin`、`stdout`、`stderr` 就是三个文件流指针，分别表示标准输入，输出，错误输出。Linux中0就是`stdin`，表示输入流，指从键盘输入，1代表`stdout`，2代表`stderr`，1、2默认是显示器。

三个变量都是一个打开的文件的句柄，系统会为每一个进程打开这三个文件，`stdout`，`stderr`一般关联到终端，`stdin`一般关联到键盘。
