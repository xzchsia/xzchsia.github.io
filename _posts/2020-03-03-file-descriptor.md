---
layout:     post
title:      "文件描述符（file descriptor）"
subtitle:   ""
date:       2020-03-03 
author:     "Hsia"
header-img: ""
catalog: true
tags:
    - 技术
    - Linux
    - 文件描述符

---  

## 概述  

句柄，熟悉Windows编程的人知道：句柄是Windows用来标识被应用程序所建立或使用的对象的唯一整数，windows使用各种各样的句柄标识诸如应用程序实例、窗口、控制、位图等。Windows的句柄有点像C语言中的文件句柄。更通俗的理解，句柄是一种指向指针的指针。在linux系统中文件句柄（file handles）和文件描述符(file descriptor)是一个一一对应的关系（如果错误，欢迎指正），按照C语言的理解文件句柄是FILE\*（fopen()返回）而文件描述符是fd（int型，open()函数返回），FILE这个结构体中有一个字段是_fileno其就是指fd(文章末尾通过程序验证)，且FILE\*和fd可以通过C语言函数进行互相转换，故此博主认为linux的文件句柄和文件描述符应该是一个一一对应的关系。文件指针即指FILE\*，即指文件句柄。打开文件（open files）包括文件句柄但不仅限于文件句柄，由于linux所有的事物都以文件的形式存在，要使用诸如共享内存、信号量、消息队列、内存映射等都会打开文件，但这些是不会占用文件句柄。  


## 文件描述符（file descriptor）  

对于linux而言，所有对设备和文件的操作都使用文件描述符来进行的。文件描述符是一个非负的整数，它是一个索引值，指向内核中每个进程打开文件的记录表。当打开一个现存文件或创建一个新文件时，内核就向进程返回一个文件描述符；当需要读写文件时，也需要把文件描述符作为参数传递给相应的函数。
通常，一个进程启动时，都会打开3个文件：标准输入、标准输出和标准出错处理。这3个文件分别对应文件描述符为0、1和2（宏STD_FILENO、STDOUT_FILENO和STDERR_FILENO）。

每一个文件描述符会与一个打开文件相对应，同时，不同的文件描述符也会指向同一个文件。相同的文件可以被不同的进程打开也可以在同一个进程中被多次打开。系统为每一个进程维护了一个文件描述符表，该表的值都是从0开始的，所以在不同的进程中你会看到相同的文件描述符，这种情况下相同文件描述符有可能指向同一个文件，也有可能指向不同的文件。具体情况要具体分析，要理解具体其概况如何，需要查看由内核维护的3个数据结构。  

* 1. 进程级的文件描述符表
* 2. 系统级的打开文件描述符表
* 3. 文件系统的i-node表


进程级的描述符表的每一条目记录了单个文件描述符的相关信息。

> 1. 控制文件描述符操作的一组标志。（目前，此类标志仅定义了一个，即close-on-exec标志）  
> 2. 对打开文件句柄的引用  

内核对所有打开的文件的文件维护有一个系统级的描述符表格（open file description table）。有时，也称之为打开文件表（open file table），并将表格中各条目称为打开文件句柄（open file handle）。一个打开文件句柄存储了与一个打开文件相关的全部信息，如下所示：

> 1. 当前文件偏移量（调用read()和write()时更新，或使用lseek()直接修改）
> 2. 打开文件时所使用的状态标识（即，open()的flags参数）
> 3. 文件访问模式（如调用open()时所设置的只读模式、只写模式或读写模式）
> 4. 与信号驱动相关的设置
> 5. 对该文件i-node对象的引用
> 6. 文件类型（例如：常规文件、套接字或FIFO）和访问权限
> 7. 一个指针，指向该文件所持有的锁列表
> 8. 文件的各种属性，包括文件大小以及与不同类型操作相关的时间戳


下图展示了文件描述符、打开的文件句柄以及i-node之间的关系，图中，两个进程拥有诸多打开的文件描述符。  

![文件描述符、打开的文件句柄以及i-node之间的关系][fdpng1]  

在进程A中，文件描述符1和30都指向了同一个打开的文件句柄（标号23）。这可能是通过调用dup()、dup2()、fcntl()或者对同一个文件多次调用了open()函数而形成的。

进程A的文件描述符2和进程B的文件描述符2都指向了同一个打开的文件句柄（标号73）。这种情形可能是在调用fork()后出现的（即，进程A、B是父子进程关系），或者当某进程通过UNIX域套接字将一个打开的文件描述符传递给另一个进程时，也会发生。再者是不同的进程独自去调用open函数打开了同一个文件，此时进程内部的描述符正好分配到与其他进程打开该文件的描述符一样。

此外，进程A的描述符0和进程B的描述符3分别指向不同的打开文件句柄，但这些句柄均指向i-node表的相同条目（1976），换言之，指向同一个文件。发生这种情况是因为每个进程各自对同一个文件发起了open()调用。同一个进程两次打开同一个文件，也会发生类似情况。




## 文件句柄 vs 文件描述符  

文件句柄也称为文件指针(FILE \*)：C语言中使用文件指针做为I/O的句柄。文件指针指向进程用户区中的一个被称为FILE结构的数据结构。FILE结构包括一个缓冲区和一个文件描述符。而文件描述符是文件描述符表的一个索引，因此从某种意义上说文件指针就是句柄的句柄（在Windows系统上，文件描述符被称作文件句柄）。  

C语言中FILE结构体的定义：  
```c  

/* Define outside of namespace so the C++ is happy.  */
struct _IO_FILE;
 
__BEGIN_NAMESPACE_STD
/* The opaque type of streams.  This is the definition used elsewhere.  */
typedef struct _IO_FILE FILE;
__END_NAMESPACE_STD
#if defined __USE_LARGEFILE64 || defined __USE_SVID || defined __USE_POSIX \
    || defined __USE_BSD || defined __USE_ISOC99 || defined __USE_XOPEN \
    || defined __USE_POSIX2
__USING_NAMESPACE_STD(FILE)
#endif
 
# define __FILE_defined 1
#endif /* FILE not defined.  */
#undef  __need_FILE
 
 
#if !defined ____FILE_defined && defined __need___FILE
 
/* The opaque type of streams.  This is the definition used elsewhere.  */
typedef struct _IO_FILE __FILE;

```  

```c  

struct _IO_FILE {
int _flags; /* High-order word is _IO_MAGIC; rest is flags. */
#define _IO_file_flags _flags
 
/* The following pointers correspond to the C++ streambuf protocol. */
/* Note: Tk uses the _IO_read_ptr and _IO_read_end fields directly. */
char* _IO_read_ptr; /* Current read pointer */
char* _IO_read_end; /* End of get area. */
char* _IO_read_base; /* Start of putback+get area. */
char* _IO_write_base; /* Start of put area. */
char* _IO_write_ptr; /* Current put pointer. */
char* _IO_write_end; /* End of put area. */
char* _IO_buf_base; /* Start of reserve area. */
char* _IO_buf_end; /* End of reserve area. */
/* The following fields are used to support backing up and undo. */
char *_IO_save_base; /* Pointer to start of non-current get area. */
char *_IO_backup_base; /* Pointer to first valid character of backup area */
char *_IO_save_end; /* Pointer to end of non-current get area. */
 
struct _IO_marker *_markers;
 
struct _IO_FILE *_chain;
 
int _fileno;
#if 0
int _blksize;
#else
int _flags2;
#endif
_IO_off_t _old_offset; /* This used to be _offset but it's too small. */
 
#define __HAVE_COLUMN /* temporary */
/* 1+column number of pbase(); 0 is unknown. */
unsigned short _cur_column;
 
signed char _vtable_offset;
char _shortbuf[1];
 
/* char* _save_gptr; char* _save_egptr; */
 
_IO_lock_t *_lock;
#ifdef _IO_USE_OLD_IO_FILE
};  

```  

这个_IO_FILE结构体中的“int _fileno”就是fd，即文件描述符。  


## 总结  

1. 由于进程级文件描述符表的存在，不同的进程中会出现相同的文件描述符，它们可能指向同一个文件，也可能指向不同的文件  

2. 两个不同的文件描述符，若指向同一个打开文件句柄，将共享同一文件偏移量。因此，如果通过其中一个文件描述符来修改文件偏移量（由调用read()、write()或lseek()所致），那么从另一个描述符中也会观察到变化，无论这两个文件描述符是否属于不同进程，还是同一个进程，情况都是如此。  

3. 要获取和修改打开的文件标志（例如：O_APPEND、O_NONBLOCK和O_ASYNC），可执行fcntl()的F_GETFL和F_SETFL操作，其对作用域的约束与上一条颇为类似。  
    
4. 文件描述符标志（即，close-on-exec）为进程和文件描述符所私有。对这一标志的修改将不会影响同一进程或不同进程中的其他文件描述符



## 参考  
1. [文件句柄（file handles） & 文件描述符（file descriptors）](https://blog.csdn.net/weixin_34162629/article/details/90322801)   
2. [Linux-什么是文件描述符以及相关应用](https://blog.51cto.com/watchmen/1944387)   
3. [文件句柄、文件描述符与进程和多线程的那些事](https://my.oschina.net/iuranus/blog/330397)   





[fdpng1]:/img/in-post/linux-file-descriptor/fd-handle-inode.png