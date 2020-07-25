# C标准库之errno

 `<errno.h>`定义了整数变量`extern int errno`。`errno`是一个由`POSIX`和`ISO C`标准定义的符号，由系统调用和某些库函数根据事件来设置它，用以表明哪里有问题。这个值只有当调用的返回表明错误的时候有用（比如，对于大多数的系统调用是-1，对于大多数的库函数来说是-1或NULL），正确的函数也可以修改`errno`。

Linux中系统调用的错误都存储于 `errno` 中，`errno` 由操作系统维护，存储就近发生的错误，即下一次的错误码会覆盖掉上一次的错误。

*  在程序启动时，**`errno`** 设置为零，系统调用或 C 标准库中的特定函数修改它的值为一些非零值以表示某些类型的错误。有效的错误number都是非零的，系统调用和库函数不会把`errno`设为0。 对于某些系统调用和库函数（比如，`getpriority(2)`），没有错误的时候也会返回-1。在这种情况下，可以在调用之前先将`errno`设为0，当不确定有没有错误的时候，可以通过查看`errno`是不是一个非零值来确定是否发生错误。 
* `errno`的值不会被任何程序清除，因此在使用`errno`的值之前，先要通过函数（系统调用/库函数）的返回值来确定有错误发生。
*  ISO C标准将`errno`定义为一个可以修改的`int`型左值，**并且不允许准确声明**，`errno`可能是一个宏，也可能被定义成一个变量，这个具体要看编译器自己的实现。
* 系统调用或库函数正确执行，并不保证`errno`的值不会被改变。

## ✏ 1、线程安全

早些时候，`POSIX.1`曾把`errno`定义成`extern int errno`这种形式，但这种形式实现的`errno`在多线程环境下是不安全的，`errno`变量是被多个线程共享的，这样可能线程A发生某些错误改变了`errno`的值，线程B虽然没有发生任何错误，但是当它检测`errno`的值的时候，线程B会以为自己发生了错误。

在GCC中，`errno`是`thread-local`，在一个线程中设置它的值不会影响它在另一个thread中的值。

其定义在`bits/errno.h`中：

```cpp
# ifndef __ASSEMBLER__
/* Function to get address of global `errno' variable.  */
extern int *__errno_location (void) __THROW __attribute__ ((__const__));
 
#  if !defined _LIBC || defined _LIBC_REENTRANT
/* When using threads, errno is a per-thread value.  */
#   define errno (*__errno_location ())
#  endif
# endif /* !__ASSEMBLER__ */
#endif /* _ERRNO_H */
```

在`error.h`中：

```cpp
/* Declare the `errno' variable, unless it's defined as a macro by
   bits/errno.h.  This is the case in GNU, where it is a per-thread
   variable.  This redeclaration using the macro still works, but it
   will be a function declaration without a prototype and may trigger
   a -Wstrict-prototypes warning.  */
#ifndef errno
extern int errno;
#endif
```

可以清晰的看到，`bits/errno.h`对`errno`进行了重定义。从 `__attribute__ ((__const__))`推测出`__errno_location ()`会返回与参数无关的与线程绑定的一个特定地址，应用层直接从该地址取出`errno`的。\(关于`__attribute__`用法可以参考[Using GNU C **attribute**](http://www.unixwiz.net/techtips/gnu-c-attributes.html)\)。但是上面使用了条件编译，也就是有两种方法可以使得`gcc`重定义`errno`：

* 不定义宏`_LIBC`
* 定义宏`_LIBC_REENTRANT`

有意思的是，我们在编译时，压根不能设置`_LIBC`， 在`gnu/stubs-64.h`中会检测，如果有`_LIBC`宏定义，直接报错终止预编译：

```cpp
#ifdef _LIBC
 #error Applications may not define the macro _LIBC
#endif
```

因此在正常情况下，我们使用`gcc`编译的程序，全局变量`errno`一定是线程安全的。

有一个问题，`__errno_location`是怎么实现的？ 在Linux 源代码中查找 `__errno_location()` 函数，在 `errno-loc.c` 中有解释：

```cpp
#include <errno.h>
#include <hurd/threadvar.h>
 
int * __errno_location (void)
{
  return (int *) __hurd_threadvar_location (_HURD_THREADVAR_ERRNO);
}
strong_alias (__errno_location, __hurd_errno_location)
libc_hidden_def (__errno_location)
```

显然，`__errno_location()` 函数的返回值就是 ****`__hurd_threadvar_location(_HURD_THREADVAR_ERRNO)` 函数的返回值被强转成 `int *` 了，那 `__hurd_threadvar_location(_HURD_THREADVAR_ERRNO)` 又是什么函数呢？ 

在头文件 `<hurd/threadvar.h>`中有：

```cpp
#include <machine-sp.h>		/* Define __thread_stack_pointer.  */
 
/* Return the location of the current thread's value for the
   per-thread variable with index INDEX.  */
 
extern unsigned long int *
__hurd_threadvar_location (enum __hurd_threadvar_index __index) __THROW
     /* This declaration tells the compiler that the value is constant
	given the same argument.  We assume this won't be called twice from
	the same stack frame by different threads.  */
     __attribute__ ((__const__));
 
_HURD_THREADVAR_H_EXTERN_INLINE unsigned long int *
__hurd_threadvar_location (enum __hurd_threadvar_index __index)
{
  return __hurd_threadvar_location_from_sp (__index,
					    __thread_stack_pointer ());
}
```

**注：在前面有定义**`#define _HURD_THREADVAR_H_EXTERN_INLINE extern __inline`**了解到其实就是** `extern __inline` **，其中** `__inline` **表示函数是内联函数。**

这是先定义了函数同时在下面直接就给出了函数代码，这个函数的又是调用了`__hurd_threadvar_location_from_sp(__index, __thread_stack_pointer ())` ****这个函数， 同样在`<hurd/threadvar.h>` 中往前看，有这样的代码：

```cpp
extern unsigned long int *__hurd_threadvar_location_from_sp
  (enum __hurd_threadvar_index __index, void *__sp);
_HURD_THREADVAR_H_EXTERN_INLINE unsigned long int *
__hurd_threadvar_location_from_sp (enum __hurd_threadvar_index __index,
				   void *__sp)
{
  unsigned long int __stack = (unsigned long int) __sp;
  return &((__stack >= __hurd_sigthread_stack_base &&
	    __stack < __hurd_sigthread_stack_end)
	   ? __hurd_sigthread_variables
	   : (unsigned long int *) ((__stack & __hurd_threadvar_stack_mask) +
				    __hurd_threadvar_stack_offset))[__index];
}
```

 往前有这些变量的定义：

```cpp
enum __hurd_threadvar_index
{
  _HURD_THREADVAR_MIG_REPLY,	/* Reply port for MiG user stub functions.  */
  _HURD_THREADVAR_ERRNO,	/* `errno' value for this thread.  */
  _HURD_THREADVAR_SIGSTATE,	/* This thread's `struct hurd_sigstate'.  */
  _HURD_THREADVAR_DYNAMIC_USER, /* Dynamically-assigned user variables.  */
  _HURD_THREADVAR_MALLOC,	/* For use of malloc.  */
  _HURD_THREADVAR_DL_ERROR,	/* For use of -ldl and dynamic linker.  */
  _HURD_THREADVAR_RPC_VARS,	/* For state of RPC functions.  */
  _HURD_THREADVAR_LOCALE,	/* For thread-local locale setting.  */
  _HURD_THREADVAR_CTYPE_B,	/* Cache of thread-local locale data.  */
  _HURD_THREADVAR_CTYPE_TOLOWER, /* Cache of thread-local locale data.  */
  _HURD_THREADVAR_CTYPE_TOUPPER, /* Cache of thread-local locale data.  */
  _HURD_THREADVAR_MAX		/* Default value for __hurd_threadvar_max.  */
};

extern unsigned long int __hurd_threadvar_stack_mask;
extern unsigned long int __hurd_threadvar_stack_offset;
extern unsigned long int __hurd_threadvar_stack_mask;
extern unsigned long int __hurd_threadvar_stack_offset;
extern unsigned long int __hurd_sigthread_stack_base;
extern unsigned long int __hurd_sigthread_stack_end;
extern unsigned long int *__hurd_sigthread_variables;
```

注释如下：

```cpp
/* The per-thread variables are found by ANDing this mask
   with the value of the stack pointer and then adding this offset.
   In the multi-threaded case, cthreads initialization sets
   __hurd_threadvar_stack_mask to ~(cthread_stack_size - 1), a mask which
   finds the base of the fixed-size cthreads stack; and
   __hurd_threadvar_stack_offset to a small offset that skips the data
   cthreads itself maintains at the base of each thread's stack.
   In the single-threaded case, __hurd_threadvar_stack_mask is zero, so the
   stack pointer is ignored; and __hurd_threadvar_stack_offset gives the
   address of a small allocated region which contains the variables for the
   single thread.  */
 
/* A special case must always be made for the signal thread.  Even when there
   is only one user thread and an allocated region can be used for the user
   thread's variables, the signal thread needs to have its own location for
   per-thread variables.  The variables __hurd_sigthread_stack_base and
   __hurd_sigthread_stack_end define the bounds of the stack used by the
   signal thread, so that thread can always be specifically identified.  */
 
/* At the location described by the two variables above,
   there are __hurd_threadvar_max `unsigned long int's of per-thread data.  */
```

再从前面 `__hurd_threadvar_location(_HURD_THREADVAR_ERRNO)` ****传过来的参数，知道 `__hurd_threadvar_location_from_sp(enum hurd_threadvar_index index, void *sp)` ****函数第一个参数是 `_HURD_THREADVAR_ERRNO` ，第二个参数在 ****`__sp` 指向 `__thread_stack_pointer()` 函数的返回值，查询  `<machine-sp.h>` 有如下：

```cpp
_EXTERN_INLINE void *
__thread_stack_pointer (void)
{
  register void *__sp__;
  __asm__ ("mr %0, 1" : "=r" (__sp__));
  return __sp__;
}
```

##  ✏ 2、宏定义

**Linux中，在头文件 `/usr/include/asm-generic/errno-base.h` 对基础常用`errno`进行了宏定义**：

```cpp
#ifndef _ASM_GENERIC_ERRNO_BASE_H
#define _ASM_GENERIC_ERRNO_BASE_H
 
#define EPERM        1  /* Operation not permitted */
#define ENOENT       2  /* No such file or directory */
#define ESRCH        3  /* No such process */
#define EINTR        4  /* Interrupted system call */
#define EIO      5  /* I/O error */
#define ENXIO        6  /* No such device or address */
#define E2BIG        7  /* Argument list too long */
#define ENOEXEC      8  /* Exec format error */
#define EBADF        9  /* Bad file number */
#define ECHILD      10  /* No child processes */
#define EAGAIN      11  /* Try again */
#define ENOMEM      12  /* Out of memory */
#define EACCES      13  /* Permission denied */
#define EFAULT      14  /* Bad address */
#define ENOTBLK     15  /* Block device required */
#define EBUSY       16  /* Device or resource busy */
#define EEXIST      17  /* File exists */
#define EXDEV       18  /* Cross-device link */
#define ENODEV      19  /* No such device */
#define ENOTDIR     20  /* Not a directory */
#define EISDIR      21  /* Is a directory */
#define EINVAL      22  /* Invalid argument */
#define ENFILE      23  /* File table overflow */
#define EMFILE      24  /* Too many open files */
#define ENOTTY      25  /* Not a typewriter */
#define ETXTBSY     26  /* Text file busy */
#define EFBIG       27  /* File too large */
#define ENOSPC      28  /* No space left on device */
#define ESPIPE      29  /* Illegal seek */
#define EROFS       30  /* Read-only file system */
#define EMLINK      31  /* Too many links */
#define EPIPE       32  /* Broken pipe */
#define EDOM        33  /* Math argument out of domain of func */
#define ERANGE      34  /* Math result not representable */
 
#endif
```

 **在 `/usr/include/asm-asm-generic/errno.h` 中，对剩余的`errno`做了宏定义**：

```cpp
#ifndef _ASM_GENERIC_ERRNO_H
#define _ASM_GENERIC_ERRNO_H
 
#include <asm-generic/errno-base.h>
 
#define EDEADLK     35  /* Resource deadlock would occur */
#define ENAMETOOLONG    36  /* File name too long */
#define ENOLCK      37  /* No record locks available */
#define ENOSYS      38  /* Function not implemented */
#define ENOTEMPTY   39  /* Directory not empty */
#define ELOOP       40  /* Too many symbolic links encountered */
#define EWOULDBLOCK EAGAIN  /* Operation would block */
#define ENOMSG      42  /* No message of desired type */
#define EIDRM       43  /* Identifier removed */
#define ECHRNG      44  /* Channel number out of range */
#define EL2NSYNC    45  /* Level 2 not synchronized */
#define EL3HLT      46  /* Level 3 halted */
#define EL3RST      47  /* Level 3 reset */
#define ELNRNG      48  /* Link number out of range */
#define EUNATCH     49  /* Protocol driver not attached */
#define ENOCSI      50  /* No CSI structure available */
#define EL2HLT      51  /* Level 2 halted */
#define EBADE       52  /* Invalid exchange */
#define EBADR       53  /* Invalid request descriptor */
#define EXFULL      54  /* Exchange full */
#define ENOANO      55  /* No anode */
#define EBADRQC     56  /* Invalid request code */
#define EBADSLT     57  /* Invalid slot */
 
#define EDEADLOCK   EDEADLK
 
#define EBFONT      59  /* Bad font file format */
#define ENOSTR      60  /* Device not a stream */
#define ENODATA     61  /* No data available */
#define ETIME       62  /* Timer expired */
#define ENOSR       63  /* Out of streams resources */
#define ENONET      64  /* Machine is not on the network */
#define ENOPKG      65  /* Package not installed */
#define EREMOTE     66  /* Object is remote */
#define ENOLINK     67  /* Link has been severed */
#define EADV        68  /* Advertise error */
#define ESRMNT      69  /* Srmount error */
#define ECOMM       70  /* Communication error on send */
#define EPROTO      71  /* Protocol error */
#define EMULTIHOP   72  /* Multihop attempted */
#define EDOTDOT     73  /* RFS specific error */
#define EBADMSG     74  /* Not a data message */
#define EOVERFLOW   75  /* Value too large for defined data type */
#define ENOTUNIQ    76  /* Name not unique on network */
#define EBADFD      77  /* File descriptor in bad state */
#define EREMCHG     78  /* Remote address changed */
#define ELIBACC     79  /* Can not access a needed shared library */
#define ELIBBAD     80  /* Accessing a corrupted shared library */
#define ELIBSCN     81  /* .lib section in a.out corrupted */
#define ELIBMAX     82  /* Attempting to link in too many shared libraries */
 
#define ELIBEXEC    83  /* Cannot exec a shared library directly */
#define EILSEQ      84  /* Illegal byte sequence */
#define ERESTART    85  /* Interrupted system call should be restarted */
#define ESTRPIPE    86  /* Streams pipe error */
#define EUSERS      87  /* Too many users */
#define ENOTSOCK    88  /* Socket operation on non-socket */
#define EDESTADDRREQ    89  /* Destination address required */
#define EMSGSIZE    90  /* Message too long */
#define EPROTOTYPE  91  /* Protocol wrong type for socket */
#define ENOPROTOOPT 92  /* Protocol not available */
#define EPROTONOSUPPORT 93  /* Protocol not supported */
#define ESOCKTNOSUPPORT 94  /* Socket type not supported */
#define EOPNOTSUPP  95  /* Operation not supported on transport endpoint */
#define EPFNOSUPPORT    96  /* Protocol family not supported */
#define EAFNOSUPPORT    97  /* Address family not supported by protocol */
#define EADDRINUSE  98  /* Address already in use */
#define EADDRNOTAVAIL   99  /* Cannot assign requested address */
#define ENETDOWN    100 /* Network is down */
#define ENETUNREACH 101 /* Network is unreachable */
#define ENETRESET   102 /* Network dropped connection because of reset */
#define ECONNABORTED    103 /* Software caused connection abort */
#define ECONNRESET  104 /* Connection reset by peer */
#define ENOBUFS     105 /* No buffer space available */
#define EISCONN     106 /* Transport endpoint is already connected */
#define ENOTCONN    107 /* Transport endpoint is not connected */
#define ESHUTDOWN   108 /* Cannot send after transport endpoint shutdown */
#define ETOOMANYREFS    109 /* Too many references: cannot splice */
#define ETIMEDOUT   110 /* Connection timed out */
#define ECONNREFUSED    111 /* Connection refused */
#define EHOSTDOWN   112 /* Host is down */
#define EHOSTUNREACH    113 /* No route to host */
#define EALREADY    114 /* Operation already in progress */
#define EINPROGRESS 115 /* Operation now in progress */
#define ESTALE      116 /* Stale file handle */
#define EUCLEAN     117 /* Structure needs cleaning */
#define ENOTNAM     118 /* Not a XENIX named type file */
#define ENAVAIL     119 /* No XENIX semaphores available */
#define EISNAM      120 /* Is a named type file */
#define EREMOTEIO   121 /* Remote I/O error */
#define EDQUOT      122 /* Quota exceeded */
 
#define ENOMEDIUM   123 /* No medium found */
#define EMEDIUMTYPE 124 /* Wrong medium type */
#define ECANCELED   125 /* Operation Canceled */
#define ENOKEY      126 /* Required key not available */
#define EKEYEXPIRED 127 /* Key has expired */
#define EKEYREVOKED 128 /* Key has been revoked */
#define EKEYREJECTED    129 /* Key was rejected by service */
 
/* for robust mutexes */
#define EOWNERDEAD  130 /* Owner died */
#define ENOTRECOVERABLE 131 /* State not recoverable */
 
#define ERFKILL     132 /* Operation not possible due to RF-kill */
 
#define EHWPOISON   133 /* Memory page has hardware error */
 
#endif
```

这些都是由`POSIX.1`确定的，错误名必须有唯一的值，有个例外是，`EAGIAN`和`EWOULDBLOCK`可能是相同的。

## ✏ 3、打印

### 🖋 3.1、打印错误信息

作用：打印系统错误信息

头文件：`#include <stdio.h>`  

函数原型：`void perror(const char *s)`

参数：s: 字符串提示符

输出形式：`const char *s: strerror(errno)` //提示符：发生系统错误的原因

### 3.2、字符串显示错误信息

作用：将错误码以字符串的信息显示出来

头文件：`#include <string.h>` 

函数原型：`char *strerror(int errnum);`

参数：`errno`

返回值：返回错误码字符串信息

```cpp
int main(){
    FILE *fp;
    if ( (fp = fopen("no/such/file","r+")) == NULL ){
        printf("errON[%d]errMsg[%s]\n",errno,strerror(errno));
    }
}
```

