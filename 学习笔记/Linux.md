#  英文缩写

软件开发工具包(SDK: Software Development Kit)

optimize 优化 macro 宏

batch 批处理 .bat脚本文件 

multi programing  多道程序 time sharing 分时

# Bash Shell

**概念：** 

其实壳程序的功能只是提供使用者操作系统的一个接口，因此这个壳程序需要可以调用其他 软件才好。 我们在第四章到第九章提到过很多指令，包括 man, chmod, chown, vi, fdisk, mkfs 等等指令，这些指令都是独立的应用程序， 但是我们可以通过壳程序 （就是命令行界 面） 来操作这些应用程序，让这些应用程序调用核心来运行所需的工作哩！

<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230413142730884.png" alt="image-20230413142730884" style="zoom:80%;" />

## XShell 配置

.xcs文件

[skycolor]
text=2775b6
cyan(bold)=00ffff
text(bold)=e9e9e9
magenta=c000c0
green=80ff00
green(bold)=3c5a38
background=042028
cyan=00c0c0
red(bold)=ff0000
yellow=c0c000
magenta(bold)=ff00ff
yellow(bold)=ffff00
red=ff4500
white=c0c0c0
blue(bold)=1e90ff
white(bold)=fdf6e3
black=000000
blue=00bfff
black(bold)=808080
[Names]
name0=skycolor
count=1

突出显示集

<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230414142939770.png" alt="image-20230414142939770" style="zoom:80%;" />



## 常用命令

<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230413112913058.png" alt="image-20230413112913058" style="zoom:80%;" />

<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230415111918524.png" alt="image-20230415111918524" style="zoom:80%;" />

### 重定向

<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230417191419320.png" alt="image-20230417191419320" style="zoom:80%;" />

**输出重定向(不存在都会创建)**

1. 标准输入 （stdin） ：代码为 0 ，使用 < 或 << ；
   2. 标准输出 （stdout）：代码为 1 ，使用 > 或 >> ； 
      3. 标准错误输出（stderr）：代码为 2 ，使用 2> 或 2>> ；

1> ：以**覆盖**的方法将“正确的数据”输出到指定的文件或设备上； 1>>：以**累加**的方法将“正确的数据”输出到指定的文件或设备上； 

2> ：以**覆盖**的方法将“错误的数据”输出到指定的文件或设备上； 2>>：以**累加**的方法将“错误的数据”输出到指定的文件或设备上；

写入同一个文件：既然有 2>&1 来将 2> 转到 1> 去，那么应该也会有 1>&2

# 编译工具链

系统调用 返回指针 需要注意要不要手动free , 注意参数是否有const

### makefile 

command要缩进，不然不能视作上一行的命令， %：%.c要顶格写
若一个目标后的依赖不存在，则寻找以其依赖为目标的规则

### GDB

```shell
gcc -g  补充C语言相关信息，对应行号
```

* gdb调试多进程时默认只能调试父进程，set fllow-fork-mode child ，使接下来fork时转向子进程
* 工作中多进程报错——查看日志反推，因为调试器多进程执行顺序(可能固定好了)可能和实际有差别
* 定位段错误，gdb——bt 查看堆栈 —— 找第一个自己写的函数——打断点
  * 参数
  * errno
  * 内存(help x)





# 泥鳅阶段重点



# 文件

## 有缓冲和无缓冲

<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230419140451765.png" alt="image-20230419140451765" style="zoom:80%;" />

**有缓冲（某些库函数加的）操作流**
**无缓冲**(系统调用)操作文件描述符(指针数组，指向文件，文件打开表，通过序号进行操作)
(stderr的无缓冲是指数据一到FILE流缓冲就输出，相当于FILE流缓冲大小只有1，其对应的是慢缓冲，行缓冲，无缓冲，并不指有没有用户态缓冲)

首先，有无缓冲只针对用户空间，内核空间对用户屏蔽。
其次，有缓冲是为了优化无缓冲系统调用次数而做的(系统调用切换管态，开销大)，底层也是用无缓冲实现的，。

用户使用文件时，查找操作系统缓存(页表，快表)，若无，则程序挂起，等待I/O，将磁盘中的数据读入一块至操作系统高速缓存中(读入缓冲的是块，可能大于所需)，再将内核缓冲中的一部分读入流缓冲，用户从流缓冲中读取数据(读的可能是流缓冲的一部分，读下一部分可能不需要在从内核取，从而减少系统调用开销)

写同理，写满流缓冲区再送入内核缓冲 ， 另外，全写法等控制的是内核缓冲——磁盘。

内核空间100  流缓冲50  用户每次读10   若无缓冲 则系统调用10次，有缓冲则两次

## 基于文件指针(FILE 流)

C语言中的fopen等

### 文件

```c
FILE* fopen(const char* path, const char* mode);//文件名 模式
int fclose(FILE* stream);
size_t fread(void *ptr, size_t size, size_t nmemb, FILE *stream);
size_t fwrite(void *ptr, size_t size, size_t nmemb, FILE *stream);
int printf(const char *format, ...);
//相当于fprintf(stdout,format,…);
int scanf(const char *format, …);
int fprintf(FILE *stream, const char *format, ...);
int fscanf(FILE *stream, const char *format, …);
int sprintf(char *str, const char *format, ...);
//eg:sprintf(buf,”the string is;%s”,str);
int sscanf(char *str, const char *format, …);
int feof(FILE * stream);
//通常的用法为while(!feof(fp))
int fseek(FILE *stream, long offset, int whence);
//设置当前读写点到偏移whence 长度为offset处
long ftell(FILE *stream);
//用来获得文件流当前的读写位置
void rewind(FILE *stream);
//把文件流的读写位置移至文件开头 fseek(fp, 0, SEEK_SET);
```

### 目录

```c
char *getcwd(char *buf, size_t size);//获取当前工作目录
int chdir(const char *path);//
int mkdir(const char *pathname, mode_t mode);
int chmod(const char *pathname, mode_t mode);//改变目录权限
int rmdir(const char *pathname);//删除空目录
```

### 目录流

```c
DIR *opendir(const char *name);//可以打开一个目录，得到一个指向目录流的指针 DIR*。
int closedir(DIR *dirp);
struct dirent *readdir(DIR *dirp);//读目录流，得到指向下一个目录项的指针。
void seekdir(DIR *dirp, long loc);//移动数据流指向
long telldir(DIR *dirp);
void rewinddir(DIR *dirp)
int link(const char *oldpath, const char *newpath);//硬链接
int unlink(const char *pathname);//删除
int symlink(const char *target, const char *linkpath);//符号连接
```

```c
struct dirent {
 ino_t d_ino;       /* inode编号 */
   off_t d_off;      
       unsigned short d_reclen;    /* 结构体的长度(d_name在有些实现上是一个
可变长数组) */
     unsigned char  d_type;      /* 文件的类型 */
       char   d_name[256]; /* 文件名 */
 };
```



## 基于文件描述符(无缓冲)

### 相关函数

```c
int open(const char *pathname, int flags); //文件名 打开方式
int open(const char *pathname, int flags, mode_t mode);//文件名 打开方式 权限
int close(int fd);//fd表示文件描述词,是先前由open或creat创建文件时的返回值。
ssize_t read(int fd, void *buf, size_t count);//文件描述符 缓冲区 长度
ssize_t write(int fd, const void *buf, size_t count);
int ftruncate(int fd, off_t length);//截断文件
void *mmap(void *adr, size_t len, int prot, int flag, int fd, off_t off);//文件映射
off_t lseek(int fd, off_t offset, int whence);//fd文件描述词
int dup(int oldfd);
int dup2(int oldfd, int newfd);
int stat(const char *file_name, struct stat *buf); //文件名 stat结构体指针  //获取文件信息
int fstat(int fd, struct stat *buf); //文件描述词 stat结构体指针
```

**open**

* flag 看做32bit 的权限设置,一定位数确定一种属性，不同属性按位或可以同时拥有(计组) 00 01 02  读， 写， 读写 可见读写不等一读和写或(同一属性)

* 若flag有creat则使用三参数，mode为创建文件的权限，但受到umask影响

* open文件，操作系统就会调页
* open后用close关闭，关闭减少**引用计数**

### read

* read前使用memset清空buf
* 读设备文件，无数据时会阻塞

**ftruncate **

* 截断文件，当扩充时会导致文件空洞(超出部分全为0)，文件空洞相当于声明我要占用的空间，但未用完，size变大，block按实际使用的来

### 文件映射

mmap映射磁盘文件至用户态缓冲(实际上只是映射到用户态缓冲同一物理地址，虚拟地址不同，以为自己在用户态，实际上用的是内核中的文件对象)

munmap关闭映射

使用lseek获取文件长度时要将其回到开头

优点：

* 随机读写
* 配合ftruncate实现扩充文件读写

缺点：

* 不适合大文件
* 只能映射磁盘文件(设备文件网卡等不行)



### dup

* 文件描述符复制，复制的是描述符指向的文件对象数据结构(文件光标位置也复制)，关闭时是引用计数（类似硬链接）

* open两次同一文件，描述符文件对象数据结构不同，指向同一文件，但光标等不同

* 重定向：close1，open fd  ， dup（fd） (自动找最小可用描述符)

* dup2(old,new) 将旧的复制给新的，新的若已指向，则close后重新赋值

### 管道文件

有名管道：进程间通信机制在文件系统中的映射
有名 ：在文件系统中有路径（两个进程可根据该路径找到管道文件进行通信）

禁止使用vim来打开管道文件

```c
$ mkfifo [管道名字]
```

1. 初始为空的管道

   * 管道读写两端都open才能正常执行，否则会阻塞在open函数处

   * 两端open后，读端先read，写端后write，读端read阻塞至写端写入后(先读后写，读阻塞)
   * 两端open后，写段先write，读端后read，write不阻塞(先写后读不阻塞)
   * 两端open后，写端close，读端read解除阻塞返回0，**并执行后续指令**
   * 两端open后，读端close，写端write触发SIGELPIPE信号，**终止程序执行**



## Select多路I/O复用

**串联等待 ——> 并联等待**

<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230421215602863.png" alt="image-20230421215602863" style="zoom:80%;" />

<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230421215630424.png" alt="image-20230421215630424" style="zoom:80%;" />

<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230421215913915.png" alt="image-20230421215913915" style="zoom:80%;" />

* select使得调用者阻塞，直到有就绪资源(由OS负责检查)
* select会修改fdset集合为就绪集合，所以每一次循环需要重新初始化和加入监听；select返回值为就绪fd个数
* 监听时若写端close，则读端read返回0，一直就绪，造成死循环，所以要判0break

**超时机制**

* timeout结构体，select时会减少其中计数值，循环需要重新赋值；
* 设置对面不发消息10s终止，自己发消息不影响，设置1s为timeout，每次就绪与超时都进行判断；

**写阻塞**

<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230421220528189.png" alt="image-20230421220528189" style="zoom:80%;" />

* 写缓冲有数据则写阻塞，读缓冲无数据则读阻塞
* 管道是读写缓冲区，暂存更大，？？？？？？
* O_RDWR非阻塞打开(只有一端打开也不会阻塞，因为可以自己读自己写)

### 实现原理

1. 在用户态创建fd_set位图

2. 将位图copy至内核态

3. OS轮询查看(poll)(只查看位图0-fds-1)————————————————**还可深入了解**

4. 得到就绪集合，放在之前监听集合位置

   * 若fd＝5,执行FD_SET(fd,&set);后set变为**0001,0000**(第5位置为1），若再加入fd＝2，fd=1,则set变为**0001,0011** 若fd=1,fd=2上都发生可读事件，则select返回，此时set变为**0000,0011**。注意：没有事件发生的fd=5被清空。

   * 对于每一个fd，都会将其加入到**驱动程序的设备IO**中，特定的硬件设备驱动程序的事件等待队列头是有限个数的，通常是有读事件和写事件的等待队列头；

   do_select函数中，遍历所有n个fd，对每一个fd调用对应驱动程序中的poll函数。poll函数调用poll_wait函数，poll_wait函数调用__pollwait()，这个函数会初始化等待队列项（有个pollwake函数），并将该等待队列项添加到从驱动程序中传递过来的等待队列头中去。**驱动程序**在得知设备有IO事件时（通常是该设备上IO事件**中断**），会调用wakeup，wakeup –> _wake_up_common -> curr->func(即pollwake)。pollwake函数里面调用_pollwake函数, 通过pwq->triggered = 1将进程标志为唤醒。再调用default_wake_function(&dummy_wait, mode, sync, key)这个默认的通用唤醒函数唤醒调用select的进程。 请注意，poll函数会返回一个mask码值，通过这个值我们可以判断是否可读写。
   
   
   

   <img src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9zdGF0aWMub3NjaGluYS5uZXQvdXBsb2Fkcy9zcGFjZS8yMDE3LzA1MjkvMTcwOTQ1X241b1pfMjg5Njg5NC5wbmc?x-oss-process=image/format,png" alt="img" style="zoom:80%;" />

### 优点

* 简单、移植性好
* timeout时间控制精细
* 

### 缺陷

* [位图即open文件数量有上限](特定的硬件设备驱动程序的事件等待队列头是有限个数的)，修改得话需要重新编译内核
* 用户态与内核频繁copy位图等
* 监听集合与就绪集合耦合(循环中监听集合不能灵活变化)
* 海量监听，少量就绪，表现很差，要全部监听isset一次才能知道哪个就绪了
* 监听集合大时，内核对所有fd也是持续轮询poll，开销大(内核要遍历，出来了也要遍历)

### 适用场景

* 连接较少，查询一遍消耗低
* 要求不高的适用场景，简单、移植性好
* 

### ==现有比较==

# 进程

<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230423212520631.png" alt="image-20230423212520631" style="zoom:80%;" />

特殊权限

真实用户id和有效用户

最高位：suid 视作拥有者

次高位：sgid 视作拥有者组

粘滞位：t 只有文件创建者和root能删

suid:具有 **SUID** 的文件始终以拥有该文件的用户身份执行，而不管用户传递命令。如果文件所有者没有执行权限，请在此处使用大写 **S**。

sgid:

- 如果在文件上设置，则允许将文件作为拥有该文件的**组**执行（类似于 SUID）
- 如果在目录上设置，则在该目录中创建的任何文件的组所有权都将设置为目录所有者的**组**所有权
- 如果所属组没有执行权限，则使用大写 **S**。

sticky:此权限不会影响单个文件。但是，在目录级别，它限制文件删除。只有文件的**所有者**（和**根**）才能删除该目录中的文件

## 优先级

Linux的优先级总共的范围有140，对于ubuntu操作系统而言，其范围是-40到99，优先级的 数值越低，表示其优先级越高。renice 命令

**nice** 命令可以给要启动的进程赋予 NI 值，但是**不能修改已运行进程**的 NI 值。后续不能用renice修改
同 nice 命令恰恰相反，**renice** 命令可以在**进程运行时**修改其 NI 值，从而调整优先级。

普通调度策略其调度规则即CFS算法。
普通调度策略不会一定保证某 个进程会在规定时间执行。(**时间片，优先级高先执行，执行完了放队尾**)优先级是从**60到99**范围之间。在ubuntu系统 中，当一个普通进程创建时，**其默认优先级是80**。普通调度策略优先级的调整是依赖于nice 值的。nice值可以用来调整优先级，其范围为**-20～19**。其中正数表示降低权限，负数表示 提升权限。root用户可以任意地修改进程的nice值，**其他用户只能提升自己的进程**的nice 值。

Linux 的实时调度策略有两种，分别是RR和FIFO。
其中FIFO以按照先进先出的方式运行进程，除非主动退出，它不会被同级或着更低优先级的进程抢占，只能被更高优先级的进程抢占； RR(轮转)在FIFO的基础上增加时间片管理，相同优先级的进程会分配相同的时间片，而低优先级 的进程无法抢占高优先级的进程，即使高优先级的进程时间片耗尽。

## 相关命令

```shell
命令名 	详细信息
ps 		查看系统当中的进程
top 	动态显示系统当中的进程
nice 	用于shell脚本中，指定程序的优先级
renice 	改变正在运行进程的优先级
kill 	发送信号（可以给后台进程发送）  kill -9 exename 以异常终止进程
crontab 控制cron后台进程
bg 		将暂停的进程放到后台运行
fg		将后台运行、后台暂停进程——>前台运行
jobs    列出本窗口放入后台的工作，
man sched 查看调度相关的系统调用
```

exename & 进程后台运行； 前台运行进程 + ctrl+z——> 后台暂停

## 系统调用

### **system**

```c
int system(const char *command); //system函数可以创建一个新进程，新进程使用shell脚本执行传入的命令command。
```

三个进程：本进程 + 创建sh-c进程执行command + command命令进程

**system函数以及从bash或者是其他shell启动进程的本质就是 fork+exec**

### **fork**

拷贝当前进程以创建一个新进程，新进程处于fork()完成时

新旧进程空间：

* 新旧进程有着几乎一致的进程空间(进程控制块几乎复制(自己理解),指针啊，地址啊，逻辑上都是一样的)PC指针指向相同位置，但逻辑上仍然是两片独立的内存空间(进程的隔离性，无论底层如何实现)
* fork()返回值，父进程返回子进程pid，子进程返回0，以此控制后续不同执行
* 写时复制：注意，首先得保证进程隔离性，在此基础上为了节省资源而采用写时复制，所以必然要保证隔离性；写时复制指代码、堆等在只读时实际上映射至同一物理内存，若有写操作，则在此之前会复制出去，保证父子进程不会干扰
* 打开文件：内核态文件描述符数组(打开文件表)复制，还是同一文件对象，共享指针等。fork后同时打开则不是同一文件对象，不共享指针等，但打开的是同一文件。若文件流方式，则复制时会将用户态内文件流缓冲中未写入内核的数据一起复制(ptrintf未加\n时，写入流缓冲，再fork后，父子进程流缓冲都有相应数据)
* 内核管道缓冲区在fork之后是采用dup机制进行拷贝的，而用户态数据则是拷贝了 一份副本。

### **exec函数族**

通常适用于在fork之后，将子进程的指令部分进行替换修改，后续直接执行参数相应指令，本程序后续指令不再执行

execl —— list(可变参数)
```c
int execl(const char *path, const char *arg0, ... /*, (char *)0*/);
execl("./add","add","3","4",(char *)0);//路径  命令参数  NULL结尾
```



execv —— vector(向量，数组)

```c
int execv(const char *path, char *const argv[]);
char *const argv[] = {"add","3","4",NULL};//字符串数组约定以NULL结尾，因为传递字符串数组不知道长度
execv("./add",argv);
```

1. 清空堆栈
2. 用可执行程序代码段取代旧代码段
3. 重置PC指针

**system函数以及从bash或者是其他shell启动进程的本质就是 fork+exec**

## 父子进程

### 孤儿进程

由于父进程先于子进程终止，子进程成为孤儿进程，将自动变更父进程为1号进程，由1号进程负责在其终止后回收其资源

### 僵尸进程

由于子进程终止而父进程未及时回收，导致子进程成为僵尸资源，

### wait

父进程使用 wait 或 waitpid 命令清理子进程。当一个进程执行结束时，它会向它的父进程发送一个SIGCHLD信号

```c
pid_t wait(int *stat_loc);//
pid_t waitpid(pid_t pid, int *stat_loc, int options);//pid为-1则等待任意子进程
```

* wait、 waitpid会导致父进程**阻塞**， waitpid 中的options 设置为 WNOHANG 则非阻塞，进程会立刻检查是否有子进程发送子进程终止信号，如果没有则系统调用立即返回。

* wait随机地等待一个已经退出的子进程，并返回该子进程的PID;waitpid等待指定 PID的子进程；如果为-1表示等待所有子进程。

* stat_loc参数是一个整型指针，可以传入进去获取子进程退出状态，**return的值只作为stat_loc中的8个bit**

  WIFEXITED(status) 子进程正常退出的时候返回真，此时可以使用 WEXITSTATUS(status)，获取子进程的返回情况 WIFSIGNALED(status) 子进程异常退出的时候返回真，此时可以使用 WTERMSIG(status)获取信号编号，可以使用WCOREDUMP(status)获取是否产生core文件 WIFSTOPPED(status) 子进程暂停的时候返回真，此时可以使用WSTOPSIG(status)获 取信号编号 WIFCONTINUED(status) 只适用于waitpid，子进程暂停后恢复时返回真

## 进程终止

| 终止方式                                 | 终止情况                           |
| ---------------------------------------- | ---------------------------------- |
| main函数调用return                       | 正常                               |
| 调用exit函数 (任意函数)                  | 正常                               |
| 调用\_Exit函数或者\_exit函数  (任意函数) | 正常，**但不刷新用户态FILE流缓冲** |
| 调用abort函数(自我放弃)                  | 异常                               |
| 接受到能引起进程终止的信号               | 异常                               |

```shell
echo $?  获取上一次进程的退出状态码
```

* 按下ctrl + c, ctrl + / 会给当前的前台进程组发送SIGINT和SIGQUIT信号

## 守护进程

### 进程组

进程组：进程的集合，进程组ID即组长ID

* 利用shell命令，bash来启动的进程，自己就是进程组的组长

* 组长G了， 组长不顶替，组ID不变

* 父进程创建子进程，子进程为父进程组的组员

* 组长进程不能脱离原进程

  ```c
  pid_t getpgrp(void);//获取进程组ID
  pid_t getpgid(pid_t pid);//获取PID为pid的进程的进程组ID，如果pid为0，则获取本进程所属进程组ID
  int setpgid(pid_t pid, pid_t pgid);//将pid进程的进程组ID设置为pgid
  //如果pid为0，使用调用者的进程ID
  //如果pgid为0，则进程组ID和pid一致
  ```

* setpgid(0,0) 自立新进程组
* 中断信号只能发送至同一进程组的进程

### 会话组

会话组：进程组的集合

* 一个终端对应一个会话

* 会话ID即会话首进程组的组长ID

* 一个会话有一个前台进程组和任意个后台进程组

* ctrl+c 只有前台进程组会受到信号

* 会话终端关闭，会话内所有进程收到连接断开信号

* 非组长进程可以建立新会话(建立会话——建立新进程组——非组长进程)

  ```c
  pid_t setsid(void);//建立新会话
  pid_t getsid(pid_t pid);//获取pid进程的会话ID，0为本会话
  ```

  

### 守护进程(daemon)

守护进程：防止终端断开而进程关闭——跳出会话——守护进程(一般以d为后缀)——脱离了启动环境的进程

1. 脱离原会话
2. 修改启动环境属性
   1. chdir "/"
   2. umask(0)
   3. 关闭输入输出，清空堆栈
3. **syslog()** , 使用守护进程经常可以用记录日志。操作系统的日志文件存储在/var/log/messages中

<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230427190739608.png" alt="image-20230427190739608" style="zoom:80%;" />



## 进程间通信

inter processes communication

**管道、信号、共享内存**、信号量、消息队列

### 管道

有名管道：任意进程间使用

```c
int mkfifo(const char *pathname, mode_t mode);//在外部创建一个pipe文件，后续还是使用open打开
#include <unistd.h>
int unlink(const char *path);
```



**无名管道**：亲属进程间使用——父子进程内核态文件对象共享

库函数：
```c
FILE *popen(const char *command, const char *type);//command启动子进程(system，即fork+exec)
int pclose(FILE *stream);
```

* 父进程"w", 子进程重定向STDIN
* 父进程"r"，子进程重定向STDOUT
* 父进程使用返回值操作管道

系统调用：

```c
int pipe(int pipefd[2]);//fd[0] 读， fd[1]写
```

* 先pipe后fork——半双工(不建议)
* 使用双工——两个pipe

### 信号

信号——软件层面事件机制，信号目标——进程，终端目标——计算机/硬件
信号——修改目标进程控制块(task_struct)中的signal字段

信号一般不用来处理业务逻辑，而是组织**有序退出**

<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230427200723401.png" alt="image-20230427200723401" style="zoom:80%;" />

**信号分类**：kill -l， man 7 signal

默认递送行为：man 7 signal

**注册**：更换信号递送行为(除了 **SIGKILL** 和 **SIGSTOP**)		

#### signal

```c
typedef void (*sighandler_t)(int);//声明函数指针类型
sighandler_t signal(int signum, sighandler_t handler);//将signum信号的递送改为handler函数(回调函数)
```

* 一次修改，永久生效，注册只能修改当前进程的信号递送行为(单个进程)
* 递送信号x时，屏蔽信号x
* 递送完成**自动重启**低速系统调用(会阻塞的那些)
* 递送行为可以改为默认、忽略、自定函数
* bash进程会注册SIGINT信 号，这样当从键盘输入中断时，bash进程不会终止了。

递送时产生信号：

mask：产生信号时，是否使其处于未决状态(阻塞、屏蔽)——位图——递送时为1，递送完为0

pending：未决信号集——位图

* mask改变时，去pending找相应信号，有则递送
* 屏蔽某信号时，出现该信号，将其加入pending
* 简单说就是**一个递送时间段内**只能接受一个**同类信号**待下次处理
* 不同类信号不会屏蔽

低速系统调用：可能引发阻塞，read，(管道，输入输出)

#### sigaction

```c
int sigaction(int signum, const struct sigaction *act, struct sigaction *oldact);//old存储之前旧的act
struct sigaction {
void (*sa_handler)(int);//1
void (*sa_sigaction)(int, siginfo_t *, void *);//2 两种行为2选一，使用三参数 SA_SIGINFO
sigset_t sa_mask;//临时屏蔽
int sa_flags;
void (*sa_restorer)(void);
};
```

* 一次注册，永久生效
* 递送信号x，临时屏蔽x
* **不会自动重启**低速系统调用(除非flag设置SA_RESTART)

**flags:**

* SA_RESTART 自动重启低速系统调用
* SA_SIGINFO 选用三参数
* SA_NODEFER 不屏蔽递送中的信号
* SA_RESETHAND 递送一次后递送行为回到默认

**s_mask:**设置信号执行中额外屏蔽的信号

```c
int sigemptyset(sigset_t *set);//初始化信号集，全置0
int sigfillset(sigset_t *set);//初始化信号集，全置1
int sigaddset(sigset_t *set, int signum);//相应信号置1
int sigdelset(sigset_t *set, int signum);//相应信号置0
int sigismember(const sigset_t *set, int signum);//判断信号是否在集合中
```

<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230428193955604.png" alt="image-20230428193955604" style="zoom:80%;" />

**sigpending**

```c
int sigpending(sigset_t *set);//获取当前未决系统调用
```

**sigprocmask**全程屏蔽

```c
int sigprocmask(int how, const sigset_t *set, sigset_t *oldset);//操作类型，参数，原来的mask
```

只能屏蔽本线程

how

* SIG_BLOCK 在原屏蔽上增加
* SIG_UNBLOCK 新的屏蔽字是set的补集和当前屏蔽字的交集 
* SIG_SETMASK 将屏蔽设置为新的set

kill 和 raise

```c
int kill(pid_t pid, int sig);//向进程发送信号
int raise(pid_t pid); //给自己发送信号
```

pause

```c
int pause(void);//阻塞自己，直到接受一个信号
```



#### alarm

定闹钟，定时发送SIGALRM信号

```c
unsigned int alarm(unsigned int seconds);
```

alarm + pause 可以实现sleep功能

**间隔定时器itimer**

```c
int getitimer(int which, struct itimerval *curr_value);·
int setitimer(int which, const struct itimerval *new_value,
```

which:

* ITIMER_REAL，真实现实时间，产生一个 SIGALRM信号。 
* ITIMER_VIRTUAL，虚拟世界，只记录用户态下CPU时间，会产生一个 SIGVTALRM信号。 
* ITIMER_PROF，实用计时器，记录用户态以及内核态模式下的CPU时间，会产生 一个SIGPROF信号。



### 共享内存

不同进程虚拟页映射至同一物理页框

```c
int shmget(key_t key, size_t size, int shmflg);//根据key创建或者获取一个共享内存，并返回ID,size一般为页(4096)整数倍
void *shmat(int shmid, const void *shmaddr, int shmflg);//根据shmid将共享内存加载至本进程虚拟内存，shmaddr一般为NULL，则其自行分配空间，shmflg为属性，一般为 0
shmdt() //用法类似free，释放本进程内的虚拟内存
int shmctl(int shmid, int cmd, struct shmid_ds *buf);//管理
```

* shmflg 低 9 bit 为权限设置，一般或上 IPC_CREAT
* 第一次开辟共享内存，每bit都是0
* ipcrm  删除共享内存——延迟删除(标记删除，status为dest) ——只有引用数为0才删除
* ipcs 查看共享内存

**私有共享内存**

参数key的取值是宏IPC_PRIVATE,则为私有共享内存，只能亲属进程共享

* 进程每次执行都会创建一个ID不同的私有共享内存(如果不删除)

**竞争状态**

多个并发的执行流访问共享内存
原因：++操作非原子操作

### 信号量

信号量是一种用于进程间同步的IPC。信号量本身是一个计数器，表示有多少个共享资源可 以共享使用。当进程访问共享资源时，首先需要需要检查信号量的数值，如果信号量为正， 那么进程就可以使用该资源，并且信号量数值减1，也就是操作系统所述的P操作；如果信 号量的数值为0，那么就进程就休眠直到信号量大于0。当进程不再访问共享资源的时候， 信号量加1，并且唤醒休眠的进程，这就是操作系统所述的V操作。为了在进程切换的时候 也能保证信号量的正确性，信号量值的测试和减1操作是原子操作。

### 消息队列

广义的消息队列指的是一种用于在多个进程（通常是跨机器的）之间的通信机制，它的设计 目标是：将不同进程的功能进行解耦；维持高峰值的请求数据；支持一定的一致性，并且能 够实现请求广播和流量控制。我们这里所描述的是狭义的消息队列，它是一种进程间通信的 手段，只适用于在本地的多进程进行通信。区别于管道的流式结构，消息队列最显著的特点 就是它维持一个消息的链表，并且可以使用先进先出的方式来进行取出消息。除此以外， 消息队列使用的时候不需要先打开读端写端，可以直接往其中写入数据。

# 多线程

## 线程

* CPU调度基本单位
* 进程独立地址空间，线程共享地址空间
* 线程独享栈区，每多一个线程就多一个栈区
* 线程依托进程存在
* 每个线程/进程有一个task_struct
* 多个线程会形成竞争条件
* man 7 pthreads
* 链接时需要加 -pthread 选项，链接pthread动态库
* 线程报错不能用perror，errno可能形成竞争条件
* **多线程不适应与信号机制一起使用**

缓冲区

**多线程并发执行**
printf 和 scanf 自带锁，在其写入写出 ( ) 内数据时不会切换线程，即不会导致写括号内数据一半换成其他的
printf 和 scanf 为行缓冲，不带\n时，数据只存在于流缓冲中，未写入文件对象(会导致未带换行符的不同pfsf数据混到一起(以一次输入输出为单位，内部正常，外部无序))

## 线程创建

**库函数**

```c
int pthread_create(pthread_t *thread, const pthread_attr_t *attr, void *(*start_routine) (void *), void *arg);
					//线程tid				线程属性，null默认属性			线程入口地址					参数
```

main主线程终止，则进程终止，则所有子线程终止

```shell
ps -elLf   //列出所有线程
```

**线程内数据共享：**

* 全局变量

* 堆区

* 各自栈区数据(**非main线程栈区数据传递可能会有悬空指针问题**)

* 通过pthread_create参数，可以传指针(传入传出参数,上述数据)，或者long字面值(仅传值)

* void* 和 long

  <img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230509092359389.png" alt="image-20230509092359389" style="zoom:80%;" />

## 线程终止

```c
pthread_join(tid,void ** retval); //获取线程退出状态,线程返回值是void*
```

线程主动退出的两种方式：

1. pthread_exit，类exit，但只终止线程，并且不清空缓冲区，线程中exit也会终止进程
2. return 

被动退出：

1. 线程取消cancel



### 线程取消

```c
int pthread_cancel(pthread_t thread);
void pthread_testcancel(void);//手动添加取消点
```

线程收到取消信号后，执行至取消点才终止

取消点：几乎所有引发阻塞的函数，文件函数 man 7 pthreads


线程取消引发的问题：申请资源清理问题

### 线程取消的资源清理函数

```c
void pthread_cleanup_push(void (*routine)(void *),void *arg);
void pthread_cleanup_pop(int execute);
```

pthread_cleanup_push 负责将清理函数压入一个栈中，这个栈会在下列情况下弹出： 

* 线程因为取消而终止时，所有清理函数按照后进先出的顺序从栈中弹出并执行。 
* 线程调用 pthread_exit 而主动终止时，所有清理函数按照后进先出的顺序从栈中弹出并执行。 
* 线程调用 pthread_clean_pop 并且 execute 参数非0时，弹出栈顶的清理函数并执行。 
* 线程调用 pthread_clean_pop 并且 execute 参数为0时，弹出栈顶的清理函数不执行。 
* 值得特别注意的是：当线程在 start_routine 中执行**return**语句而终止的时候，清理函数**不会弹栈**！

## 线程互斥

锁

```c
int pthread_mutex_init(pthread_mutex_t *mutex, const pthread_mutexattr_t *mutexattr);//动态初始化
int pthread_mutex_destroy(pthread_mutex_t *mutex);
int pthread_mutex_lock(pthread_mutex_t *mutex);
int pthread_mutex_unlock(pthread_mutex_t *mutex);
int pthread_mutex_trylock(pthread_mutex_t *mutex);//不阻塞加锁，不成功则立即返回
```

为什么要临界区越小越好：饥饿问题

### 死锁

1. 循环等待死锁——调整资源获取顺序 / trylock
2. 持有锁进程，在有锁状态终止——终止前解锁 / 将解锁写入清理函数
3. 有锁情况下，再次加同一把锁——不重复加锁 / 检错锁 / 可重入锁

trylock 非阻塞加锁，若未锁，则加锁；若已锁，则返回，常与while搭配使用
trylock可解决第一种死锁

自旋锁——pthread_spin_  不满足条件，死循环
读写锁——读者写者问题，

锁的类型

```c
int pthread_mutexattr_gettype(const pthread_mutexattr_t *restrict attr, int *restrict type);
int pthread_mutexattr_settype(pthread_mutexattr_t *attr, int type);
```

锁类型：普通锁(默认)，检测锁，递归/可重入锁

<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230503221319860.png" alt="image-20230503221319860" style="zoom:80%;" />

检错锁：多次加锁会报错,会解锁再加锁，可以运行

可重入锁：多次加锁，引用计数增加，其他线程还是会阻塞

## 线程同步

flag：事件能否运行的条件

**使用互斥锁**：循环检查

**条件变量**(无竞争 的线程间同步(不会死循环检查))——配合锁与flag(可能是size，num等值)使用



```c
pthread_cond_t cond = PTHREAD_COND_INITIALIZER;
int pthread_cond_init(pthread_cond_t *cond, pthread_condattr_t *cond_attr);
int pthread_cond_signal(pthread_cond_t *cond);//通知
int pthread_cond_broadcast(pthread_cond_t *cond);//广播
int pthread_cond_wait(pthread_cond_t *cond, pthread_mutex_t *mutex);.//等待
int pthread_cond_timedwait(pthread_cond_t *cond, pthread_mutex_t *mutex, const struct timespec *abstime);//限时等待
int pthread_cond_destroy(pthread_cond_t *cond);
```

wait的原理：

<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230503222545863.png" alt="image-20230503222545863" style="zoom:80%;" />

虚假唤醒: **使用 while 判断**

* 使用 broadcast 时，唤醒全部wait，都会执行后续操作，但资源并不够
* 同类线程使用同一标志时，singal唤醒的wait加锁前被同类进程抢占，直接执行完，后续wait抢占时不会再次检查资源

```c
while(flag > 0)//若真唤醒，重新检查会弹出；若虚假唤醒，会重新wait
{
    wait(xxxxx);
}
```

## 线程属性

setdetach :detach(不可合并，即不能被join)， join(可合并)

## 线程安全

可重入性：函数执行过程中再次调用自己，递归，多线程，信号
若多次调用彼此无影响则是可重入，否则非可重入

ctime_r:若函数有_r版本，则代表无r版本是线程不安全的(ctime使用同一片数据区，多线程会互相覆盖)
返回值为指针的函数才可能有线程不安全，堆空间一般无该问题

线程安全

* 使用栈上的数据
* 使用可重入函数
* 使用非栈数据记得加锁
* 减少使用全局，static数据

# 网络编程

## socket套接字

```c
//man 7 ip
struct sockaddr_in {//ipv4  ipv6为sockaddr_in6
sa_family_t sin_family; /* address family: AF_INET */
in_port_t sin_port; /* port in network byte order */
struct in_addr sin_addr; /* internet address */
};
/* Internet address. */
struct in_addr {
uint32_t s_addr; /* address in network byte order */ //ipv4
};
```

struct sockaddr是一种通用的地址结构，它可 以描述一个IPv4或者IPv6的结构，所有涉及到地址的接口都使用了该类型的参数，但是过于 通用的结果是直接用它来描述一个具体的IP地址和端口号十分困难。所以用户一般**先使用 struct sockaddr_in**来构造地址，再将其进行**强制类型转换成struct sockaddr**以作为网 络接口的参数。
<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230510135855559.png" alt="image-20230510135855559" style="zoom:80%;" />

## 主机字节序与网络字节序 

主机字节序：x86小端，ARM可大可小

网络字节序：网络设备，大端

man INET

```c
#include <arpa/inet.h>
//h --> host n --> net l --> 32bit s --> 16bit
uint32_t htonl(uint32_t hostlong);//主机long字节序变netlong字节序
uint16_t htons(uint16_t hostshort);
uint32_t ntohl(uint32_t netlong);
uint16_t ntohs(uint16_t netshort);
```

利用char *p = (char *) & i;查看该int值是大端还是小端 (看第一个字节数据)

## IP地址转换

```c
int inet_aton(const char *cp, struct in_addr *inp);//点分十进制换32bit int大端(网络字节序)	
int_addr_t inet_addr(const char *cp);//点分十进制换32bit int大端(网络字节序)
char *inet_ntoa(struct in_addr in);//32bit大端换点分十进制
//线程安全版本是inet_atop 、inet_ptoa
```

## 域名

* 查host文件：cat /etc/hosts
* DNS协议:查看主机DNS协议
* nslookup命令:nslookup www.baidu.com
* 系统调用：

```c
struct hostent *gethostbyname(const char *name);//底层使用了DNS，断网不可用
struct hostent {
char *h_name; /* official name of host
*/
char **h_aliases; /* alias list */
int h_addrtype; /* host address type */
int h_length; /* length of address */
char **h_addr_list; /* list of addresses *///指针数组，每一项是一个指针，指向一个ip地址(二进制网络字节序)
}
```

* 此函数报错使用**herror**，(h_errno)
* h_addr_list为指针数组，其指向的是二进制的地址(而非点分十进制),网络字节序

MSS:保证不触发分片的大小

## TCP 通信

<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230509084826224.png" alt="image-20230509084826224" style="zoom:80%;" />

```c
int socket(int domain, int type, int protocol);
//domain AF_INET --> IPv4 AF_INET6 --> IPv6
//type SOCK_STREAM --> TCP SOCK_DGRAM --> UDP
//protocol IPPROTO_TCP --> TCP IPPROTO_UDP -->UDP
int connect(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
int listen(int sockfd, int backlog);
int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen);//长度要初始化
ssize_t send(int sockfd, const void *buf, size_t len, int flags);
ssize_t recv(int sockfd, void *buf, size_t len, int flags);
int setsockopt(int sockfd, int level, int optname, const void *optval, socklen_t optlen);//更改socket属性
```

* socket创建文件对象，内含一个输入缓冲和一个输出缓冲
* connect发送第一次握手，(也可看做去完成了三次握手，具体由os实现)
* bind用于给socket对象赋予一个本地协议地址（即IP地址加端口号），(服务器必须要用，ip只能是本机ip)
* 只要客户端端口号随机，timewait意义不大，设置socket属性，SO_REUSEADDR，可无视timewait
     <img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230510143808405.png" alt="image-20230510143808405" style="zoom:80%;" />
* listen会将socket中的缓冲区改为两个队列，半连接(有第一次握手)，全连接(完成三次握手)，**listen允许os对一次握手进行返回二次握手**
* accept，在全连接队列中取出一个，返回值为新的文件对象fd，该fd中有一个输入缓冲和一个输出缓冲(socket)
* 服务器bind 的ip地址：192.168.118.128 1234 //外网可访问
  	                                 127.0.0.1 1234 //外网不可访问
                                       0.0.0.0  1234 //外网可访问，同192
* 客户端多次bind会导致同一连接多次建立，TCP最后的timewait
* DDOS攻击，只发送第一次连接，使得半连接满了
* send / recv 只是实现文件对象与用户态缓存中的数据copy，具体什么时候发送接收，由os内核协议栈实现
* send / recv 是特殊的write read
* TCP是一种流式协议，send和recv不是一一对应，tcp数据无边界

**tcpdump**

wireshark:Ncap抓包，wireshark分析

linux抓包：
				1. su 切换到root
				1. tcpdump (-i 指定网卡)(-i any 任意)(-w 保存文件，cap文件)(-n 显示完全的ip地址)

<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230509090308073.png" alt="image-20230509090308073" style="zoom:80%;" />

**解决网络问题的一般流程**

1. netstat 观察连接状态， netstat -an，netstat -ntlp 只显示监听进程
2. tcpdump抓包，-w保存
3. wireshark打开抓包数据进行分析



## UDP通信

<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230509091402184.png" alt="image-20230509091402184" style="zoom:80%;" />

```c
ssize_t sendto(int sockfd, const void *buf, size_t len, int flags, const struct sockaddr *dest_addr, socklen_t addrlen);
ssize_t recvfrom(int sockfd, void *buf, size_t len, int flags,struct sockaddr *src_addr, socklen_t *addrlen);//长度要初始化
```



* tcp udp 可以bind同一端口
* socket使用SOCK_DGRAM
* 使用recvfrom、sendto，必须客户端先sendto，服务端先recvfrom
* udp消息有边界，
* 聊天终止必须手动终止(向服务端发送一个长度0消息)

## epoll 

man 7 epoll

<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230509210800646.png" alt="image-20230509210800646" style="zoom:80%;" />

* 采用文件对象；select监听与就绪耦合
* 监听集合使用红黑树，大小无限制；select fdset为1024，扩容不方便
* 监听和就绪分离；select每次都要重新将fdset拷贝内核
* 遍历就绪集合；select遍历监听集合(只是就绪的位为1)

```c
int epoll_create(int size);
int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event);
int epoll_wait(int epfd, struct epoll_event *events, int maxevents, int timeout);//-1永久等待
typedef union epoll_data {
	void *ptr;
	int fd;
	uint32_t u32;
uint64_t u64;
} epoll_data_t;
struct epoll_event {
	uint32_t events; /* Epoll events */
	epoll_data_t data; /* User data variable */
};
```

**阻塞与非阻塞**

* read磁盘文件不会阻塞
* read管道、stdin；read/recv socket会阻塞

**阻塞改为非阻塞**

```c
int fcntl(int fd, int cmd, .../* args */);//永久非阻塞
cmd: F_GETFL(void)   F_SETFL(int)
recv(sockfd, buf, sizeof(buf), MSG_DONTWAIT);//临时阻塞
```

通过getfl获取已打开的描述符fd的属性，修改其属性(或上O_NONBLOCK)，再setfl回去
<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230509211715315.png" alt="image-20230509211715315" style="zoom:80%;" />

* 非阻塞无数据时返回-1
* 遇到EOF,返回0

**IO模型**

<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230509211826779.png" alt="image-20230509211826779" style="zoom:80%;" />

**边缘触发与水平触发**

<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230509211942081.png" alt="image-20230509211942081" style="zoom:80%;" />

水平触发：只要读缓冲区有数据，就会读就绪(epoll默认水平触发)

边缘触发：只有读缓冲区增加数据时会读就绪(可能读不完)，不增加时即使有数据也不会就绪

epoll默认水平触发，增加边缘触发属性
```c
event.events = EPOLLIN | EPOLLET;//增加边缘触发属性
```

# 进程池线程池

<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230509212313604.png" alt="image-20230509212313604" style="zoom:80%;" />

<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230509212355130.png" alt="image-20230509212355130" style="zoom:80%;" />

池(poll):申请大量资源，使用时只转移使用权，不回收，避免了切换开销，全部做完再回收

![image-20230510105035360](E:\CS\markdown notes\学习笔记\Linux.assets\image-20230510105035360.png)

## 进程池



**进程池：**
客户——>服务器父进程——>子进程
进程池——>分配任务——>需要跨进程传递文件对象——>sendmsg、recvmsg——>需要利用socket对象传递——>socketpair(本地套接字、socket管道)
父进程共享文件对象给子进程：sendmsg、recvmsg
父进程分配任务给子进程：epoll、sendmsg、
父子进程通信：子进程状态转换；socketpair、
进程有序退出：异步拉起同步；信号+epoll+sendmsg(正文标记)
子进程工作：

问题：

1. 客户端G——>服务端G(子进程写，客户端读，读端G，写端SIGPIPE)
2. 粘包:流式协议无边界——火车
3. 半包：网络质量速度等问题——recvn或者waitall
4. 文件校验：md5sum ( vimdiff 只适用于文本文件)

调优：(注意性能瓶颈在哪里)
零拷贝：trancate+mmap、sendfile
文件内容：大火车
进度条：与大火车相斥

man CMSG

```c
//					父子进程共享文件对象
//-----------------------------------------------------------------------------------------------------------
ssize_t sendmsg(int sockfd, const struct msghdr *msg, int flags);//在套接字间传递数据
ssize_t recvmsg(int sockfd, struct msghdr *msg, int flags);

int socketpair(int domain, int type, int protocol, int sv[2]);//创建本地套接字，数组中每一个都是一个socket，有读写

struct msghdr
{
	void *msg_name; /* optional address */					//前两个填0
	socklen_t msg_namelen; /* size of address */
    struct iovec *msg_iov; /* scatter/gather array */		//正文字段，数组，暂时无用，但要传
	size_t msg_iovlen; /* # elements in msg_iov */			//数组有几个元素
	void *msg_control; /* ancillary data, see below */ 	//控制字段，可以用来传文件描述符，存储struct cmsghdr类型的地址(申请在堆上)
	size_t msg_controllen; /* ancillary data buffer len */
	int msg_flags; /* flags on received message */
};
struct iovec
{ /* Scatter/gather array items */
	void *iov_base; /* Starting address */
	size_t iov_len; /* Number of bytes to transfer */
};
size_t CMSG_LEN(size_t length);//根据结构体最后一个数组大小确定结构体大小
unsigned char *CMSG_DATA(struct cmsghdr *cmsg);//已知结构体起始地址，算出结构体最后一个数组地址

struct cmsghdr {//申请在堆上
               size_t cmsg_len;    /* Data byte count, including header 	//填结构体长度。CSMG_LEN
                                      (type is socklen_t in POSIX) */
               int    cmsg_level;  /* Originating protocol */           	//填SOL_SOCKET
               int    cmsg_type;   /* Protocol-specific type */          	//SCM_RIGHTS
           /* followed by					
              unsigned char cmsg_data[]; */								//CSMG_DATA 找到data[]地址，解引用赋值fd		
           };

//零拷贝
ssize_t sendfile(int out_fd, int in_fd, off_t *offset, size_t count);//in是磁盘文件，fd随意 ，一般为网络文件
```

* sendmsg 需要 **msghdr** 数据结构；**msghdr** 中的 contronl 需要填 **cmsghdr** 类型地址(只能申请在堆上)

# 线程池

优势：传递文件描述符只需传递一个整数
注意：互斥锁(mutex)、(同步)cond

问题：

1. pthread_cancel与cond_wait同时存在，(wait 是取消点)，走到wait时线程终止，但是未解锁，需要用资源清理函数解锁
2. cancel中的push,pop是宏，有花括号，要成对出现，所以push和pop内部变量只在其间作用域，若后续要用，需要在push前声明

<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230514143703765.png" alt="image-20230514143703765" style="zoom:80%;" />



## 目录系统

实际目录，虚拟目录；栈；

## 加密：

/etc/shadow 下的盐值和密文

```c
char *crypt(const char *key, const char *salt);//给出明文和盐值，返回密文
struct spwd *getspnam(const char *name);//root下调用，获取用户密文密码
struct spwd{
    char *sp_name;//登录名
    char *sp_pwdp;//密文密码
}
```

## 日志

写一个宏实现日志：LOG_OPERATION
syslog(),操作系统的日志文件存储在/var/log/messages中

## C库的一些宏

```c
__LINE__ 	//当前行号
__FUNCTION__ //当前函数名
__FILE__	//当前文件名
```

## md5

<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230514133243234.png" alt="image-20230514133243234" style="zoom:80%;" />

* openssl库
* 或者使用他人写好的库

## 

## 连接数据库

```c
MYSQL * mysql_init(MYSQL *mysql);	//为MySQL连接分配资源，参数一般填NULL，互斥访问
//数据结构MYSQL是操作资源的句柄
void mysql_close(MYSQL *mysql);		//关闭MYSQL连接
MYSQL *mysql_real_connect(MYSQL *mysql, const char *host, const char *user, const char *passwd, const char *db, unsigned int port, const char *unix_socket, unsigned long client_flag);//连接到MySQL服务端，必须互斥访问
//一般的参数
// host -> "localhost" user -> "root" passwd-> 密码 db->数据库名
// 其余参数选择默认 port -> 0 unix_socket -> NULL client_flag -> 0
// 如果出现报错，返回值为NULL，使用mysql_error函数可以获取报错原因

int mysql_query(MYSQL *mysql, const char *stmt_str);
// 执行SQL语句，stmt_str -> SQL语句的内容 不需要加分号

MYSQL_RES *mysql_store_result(MYSQL *mysql)//获取结果，每一次读sql之后就要使用，否则报错
// 在mysql_query之后调用，假如执行的SQL语句会得到结果，需要使用该函数将结果存入数据结构MYSQL_RES当中
void mysql_free_result(MYSQL_RES *result);// 释放数据结构MYSQL_RES占据的内存空间
my_ulonglong mysql_num_rows(MYSQL_RES *result);// SQL语句结果的行数
unsigned int mysql_num_fields(MYSQL_RES *result);// SQL语句结果的列数
MYSQL_ROW mysql_fetch_row(MYSQL_RES *result);// 从结果当中取出一行，row[0]就是一行结果，字符串

```

* 每一次读sql(查询)后，必须用store接收，否则报错
* init 和 connect 要放入临界区保护





## 有状态与无状态

**长连接：**用户登录后一直处于连接状态，直到做完所有任务退出(不管用户有没有请求)

优点：性能好

缺点：并发性能差，可能导致饥饿

**短连接：**用户每次请求结束就断开连接，下一次请求时重新连接

优点：并发性能好

缺点：同一个用户的多次请求，可能分配给不同线程，会导致用户相关信息维护的困难

凭证系统：用户登录后每次请求都要携带凭证

有状态：session 会话，服务端有一张用户凭证表，记录了用户及其凭证； 不支持水平扩展(增加服务端机器数量来提高并发能力)

无状态：用户登录时，服务端会根据用户名和其他数据生成密文，后续每次请求都要携带这些信息和密文(token)
<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230517094426727.png" alt="image-20230517094426727" style="zoom:80%;" />

<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230517094440586.png" alt="image-20230517094440586" style="zoom:80%;" />

**token**

JWT token生成

github

<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230517094826030.png" alt="image-20230517094826030" style="zoom:80%;" />

**长短命令分离**

将cd、ls等短命令由主线程执行，上传下载等长命令由子线程验证token后处理 
存在的长连接：用户和主线程、用户上传下载和子线程

长连接需要占用资源——>超时退出
<img src="E:\CS\markdown notes\学习笔记\Linux.assets\image-20230517095535371.png" alt="image-20230517095535371" style="zoom:80%;" />

# 杂项

* linux中r，rb都一样，二进制读写，是什么写成什么，；windows中r和rb不一样，r: \n -> \r\n
* strlen计算字符串中文本长度(无空字符)，sizeof计算数据所占字节
* **memset**(buf,o,sizeof(buf)) 清空buf，置0
* **stat filename**  查看文件详细信息
* fileno()从FILE流中提取文件描述符
* **lseek**改变内核缓冲(文件对象)中光标位置，**fseek**改变文件流缓冲中光标位置
* **memcmp**按字节比较是否相同
* ctime 返回日历时间字符串，localtime返回时区时间结构体
* 给框架——背接口——看整体结构设计、数据机构(看源码也是)
* 工作中多进程报错——查看日志反推，因为调试器多进程执行顺序(可能固定好了)可能和实际有差别
* 定位段错误，gdb——bt 查看堆栈 —— 找第一个自己写的函数——打断点
  * 参数
  * errno
  * 内存(help x)
* man -k shm 查找man中有shm的
* ctrl + \ 是三号信号 quit
* atoi库函数，将数字字符串转int，如果非有效转换，返回0
* perf 、epbf 性能分析工具， 性能测试，火焰图，找到粗壮顶峰(平坦的)进行优化
* 面试问题——放入相应场景——分析——联系已学知识——解决方案
* mmap的flag要跟open fd时的参数一样
* open的flag TRUNC (文件存在则截为0)
* 定义结构体：其成员为指针的话，一定要分配内存
* strtok(),从字符串中提取信息
* access判断文件是否存在或者对文件是否

# 面试

**进程池**



问题：
客户端G——>服务端G(子进程写，客户端读，读端G，写端SIGPIPE)——MSG_NOSIGNAL
粘包:	流式协议无边界——火车
半包：	网络质量速度等问题，当一次没发完，接收端只接受缓冲区的，下一次获取length就是错的，——recvn或者WAIT_ALL
文件校验：md5sum ( vimdiff 只适用于文本文件)
进程有序退出：异步拉起同步；信号+epoll+sendmsg(正文标记)

调优：(注意性能瓶颈在哪里)(perf 、epbf 性能分析工具， 性能测试，火焰图，找到粗壮顶峰(平坦的)进行优化)
零拷贝：trancate+mmap、sendfile
文件内容：大火车
进度条：与大火车相斥

