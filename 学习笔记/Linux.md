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

mman映射磁盘文件至用户态缓冲(实际上只是映射到用户态缓冲同一物理地址，虚拟地址不同，以为自己在用户态，实际上用的是内核中的文件对象)

munman关闭映射

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

| 终止方式                             | 终止情况                           |
| ------------------------------------ | ---------------------------------- |
| main函数调用return                   | 正常                               |
| 调用exit函数 (任意函数)              | 正常                               |
| 调用Exit函数或者exit函数  (任意函数) | 正常，**但不刷新用户态FILE流缓冲** |
| 调用abort函数(自我放弃)              | 异常                               |
| 接受到能引起进程终止的信号           | 异常                               |

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
int mkfifo(const char *pathname, mode_t mode);
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
* 递送完成**自动重启**低速系统调用
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

**s_mask:**

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

* 进程每次执行都会创建一个私有共享内存

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





## 线程创建

**库函数**

```c
int pthread_create(pthread_t *thread, const pthread_attr_t *attr, void *(*start_routine) (void *), void *arg);
					//线程tid				线程属性，null默认属性			线程入口地址					参数
```

main主线程终止，则进程终止，则所有子线程终止



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
* 

