#                                                                                                                                                                                                                                                                                                                                                                                                   一 C语言概述

## 生成可执行程序过程

* 预处理：头文件包含+define简单文本替换
* 编译：将预处理文件编译成目标代码文件或者汇编(以源文件为单位 )
* 汇编：将编译后文件汇编成机器代码文件
* 链接：将汇编好的文件和其他附加代码(库函数,,,)整合，形成可执行文件

## int main(void)

main函数无参用void，否则表示参数不确定，可有参可无参

## return 0

作用：终止main函数，给操作系统返回状态码，0为无错终止，非零为异常终止

## define 

简单文本替换，使用宏函数需注意括号

宏函数相比函数：减少开销，提高效率

考虑宏函数：逻辑简单+调用频繁

不够写 \ 换行继续写

## 编译错误和链接错误

编译错误：一般为语法错误

链接错误：一般为找不到相应库文件（有声明没定义）

## 数据类型

决定了：

1. 存储空间
2. 编码方式
3. 操作

## 进程的内存空间

* 内核(cpu处于内核态才能访问)

* 栈

* 堆

* 数据段
  初始化的(data) 未初始化的(bss)，字符串常量处于数据段
  
* 代码段
  程序代码
  
  <img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230409162202581.png" alt="image-20230409162202581" style="zoom:80%;" />

## C语言内存空间分配(语言层面)

* **栈区**
  函数中定义的变量

* 堆区
  动态分配内存

* **全局区(静态区)**
  全局变量和静态变量(包括静态局部变量)，初始化的(**data**)和未初始化(**bss**)分开存放

* 代码区
  程序的二进制代码，内存由系统管理

* **文字常量区**

  **字符串**、**数字**等常量存放在常量区。const修饰的全局变量存放在常量区。程序运行期间，**常量**区的内容不可以被修改

  常量在统一运行被创建，常量区的内存是只读的，程序结束后由系统释放。

代码段，数据段，堆栈段是cpu级别的概念，五大分区属于语言级别的概念，两者是不同的概念。

* 

------



# 二 格式化输入输出



## 转换说明

%m.px:

m:最小字段宽度，小于最小字段宽度补空格，大于按实际输出，-m表示左对齐，在右边补空格

p:精度，根据x的不同而发生改变。

​	%d：待显示数字的最小个数，不足在前面补0

​	%f：小数位数（默认6）

![image-20230328164741290](E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230328164741290.png)

![image-20230328164804278](E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230328164804278.png)

![image-20230328164817904](E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230328164817904.png)

## scanf和printf

**防止报错： #define _CRT_SECURE_NO_WARNINGS**

* scanf 读取时**忽略前面的空白字符**，遇到**空白字符**停止(读字符串会自动添加空字符)
* scanf返回读取成功的项数 (判断成功读取几项)

![image-20230402175947701](E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230402175947701.png)

****

* scanf 读取对应转换说明的数据项，读取成功则继续处理；读取失败则立刻返回，不再处理，失败数据项还在输入队列，供下次读取。

除空白字符外严格匹配：

```c
scanf("%d %d",&a,&b);
```

读取时略过中间一系列空白字符（空格 换行 水平制表  换页 ）直到下一个非空白字符

```c
scanf("%d/%d",&a,&b);
```

严格匹配，输入数据必须按照 x/y 

如果需要跳过前面的空白 字符，则要在转换说明符 %c 前面加一个空格：

***修饰符**

```c
printf("%*d", width, numbe);//width指定字段宽度，
printf("%*.*d", width, prevision, numbe);

scanf("%*d %*d %d", &n);//跳过前两个输入项;
//2013 2014 2015   只输入了2015
```

--------------------

## getchar putchar

getchar()读取每个字符，包括空格换行制表

scanf跳过开头空白字符，遇到下一次空白字符停止，(读字符串则会添加一个空字符)

![image-20230331205649755](E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230331205649755.png)

## 



# 三 基本数据类型

![image-20230327210812997](E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230327210812997.png)

## 浮点数的比较

浮点数舍入误差会使逻辑相等的数不相等

3*（1/3）=1.0     但将1/3表示成小数则为0.333333 相乘得0.999999  与1不相等	

fabs()函数（math.h)比较浮点数  返回一个浮点数的绝对值

![image-20230328090821565](E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230328090821565.png)



## 补码值的计算

第一位值为负 +剩余值

x+(-x) :值为0 (进位1 其余为0)

x+(~x) :值为-1(全1)

## 转义字符 

<img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230328164919260.png" alt="image-20230328164919260" style="zoom:80%;" />

'\33' '\x1b'

## 类型转换

**整数提升**

操作数中有低于int或者unsigned int 的类型，先将其提升到int和unsigned int  再进行操作

**同一等级**

同一等级的有符号数和无符号数，会将有符号数转换无符号数（仅解释方式更改）再操作

[^注意]: 尽量避免无符号数和有符号数混用.



## define 和typedef的区别

```c
//宏定义只是简单的字符串代换(原地扩展)，而typedef则不是原地扩展，它的新名字具有一定的封装性，以致于新命名的标识符具有更易定义变量的功能。
#define INTPTR1 int*
typedef int* INTPTR2;
INTPTR1 p1, p2;//声明一个指针变量p1和一个整型变量p2;同int *a,b
INTPTR2 p3, p4;//声明两个指针变量p3、p4;同int *a,int *b

#define INTPTR1 int*
typedef int* INTPTR2;
const INTPTR1 p1 = &a;//常量指针,不可修改p1指向的内容
const INTPTR2 p2 = &b;//指针常量，不可修改p2的指向，相当于char*   const。
INTPTR2 const p3 = &c;//指针常量
```

* typedef更具可读性
* typedef 是对已有数据类型取别名(alias)
* define 是定义常量，利用常量名代替值(实际上还是值)

-------------------------------------

# 四 表达式

## 逗号表达式

逗号表达式的值为右侧项的值

```c
int x;
x = 249, 500;//x为249，式子为500
int x = (249,500);//x为500
```

逗号表达式保证了**左侧项副作用在执行右侧项前**发生完。

## 运算符 /

/运算符==**向0取整**==：-3/4==-0.75—>0

## 运算符 %

* 只能操作两个正数

* ==**取余结果可以为负数**==

* i % j , 结果**符号同 i**

  [^注意]:所以对于判断奇数：i%2\==1\|\|i%2\== -1,或i%2 != 0.

## 自增自减运算符

尽量避免多个自增自减出现  

 j= (i++) + (++i) ，a[i] = b[i++];   编译器只保证该句执行完下句执行前 i增加，

**++优先级高于***： *start++ == *(start++)   start为指针

## 移位运算符

* i<<2,不改变i值，只是用来计算表达式(i<<2)的值

> 注意：
>
> ```c
> //两种移位并不相同
> int a = 1;
> for(int i = 0; i<32 ;i++)
>     a << 1; //a==0
> 
> a<<32；//a==1,实际上编译器在移位前做了 a<<(x%32) ,32是int的位数，功能上有点类似循环左移
>     
> ```

### ==常用技巧==

* a^a (为0) 用来置零
* 判断一个数为奇数：a & 0x1  利用到<img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230330104423173.png" alt="image-20230330104423173" style="zoom:67%;" />

* 找最低有效位：x&(-x) <img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230330104829845.png" alt="image-20230330104829845" style="zoom:67%;" />

* 交换两个数 不使用中间变量

  ```c
  a = a^b;
  b = a^b;
  a = a^b;
  //但该方法处理自己交换自己会导致值清零，所以还是使用中间变量较好
  ```

* 判断非零正数是否为2的幂：n&(n-1)

* 找出单独的数：全异或

* 找出两个单独的数：全异或，找最低有效位，拿最低有效位相与每个项进行分类，最后两个类分别全异或

# 五 语句

## if else的缩进问题

else默认与最近if匹配，编译器是忽略缩进的

<img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230328212249146.png" alt="image-20230328212249146" style="zoom:80%;" />

## switch语句

switch中判断类似索引，哈希，根据值直接算出地址，跳转到case代码位置，不需要进行比较，效率较高

利用case穿透时要加注释

## goto 语句

1. 用来进行错误处理
2. **跳出外层嵌套**

# 六 数组

二维数组的本质：元素是一维数组的一维数组。

# 七 函数

## 函数定义

* 函数不能返回数组（其他都可以）

* 函数功能要单一，不要输出

* 如果**调用在声明前**，C编译器会自动推测函数（隐式声明），默认int类型 ，形式参数为传入参数类型，后续遇到定义就会发生重定义错误

## 参数传递

* C语言中所有参数传递**都是值传递**

### 	数组作为参数

* **==数组作为参数传递时，会退化成指向数组第一个元素的指针==（丢失了长度，参数多传一个长度）。**
* 数组类型的形式参数可以改为指针类型，对形参来说，声明数组和声明指针一样，但对变量来说，不同。
* 声明多维数组，只有**第一维可以省略**，其他不能（相当于数组的元素类型）

### 指针作为参数

* 返回多个值，指定要放回的位置，在函数中进行操作
* 修改形参会改变实参的值

### 指针变量作为返回值

* ==不能反回指向当前栈帧区域的指针==

## 局部变量和外部变量

* 静态局部变量：
  * 拥有静态存储期限，永久存储单元（与程序共存），初始化只会执行一次
  * 拥有块作用域，函数外不可见
* 全局变量（外部变量）
  * 静态存储期限
  * 文件内作用域
* 静态全局变量
  * 静态存储期限
  * 会对其他文件隐藏（C语言只有一个命名空间，防止了重名（有函数重名）问题

	* 静态存储期限 （代码区数据区）  动态存储期限（堆区，malloc） 自动存储期限（栈区）



## 程序终止	

### return

*  main函数中return终止程序，0为正常终止，非零异常终止
* 其它函数return仅返回值，void函数仅返回；

### exit

* exit在其他函数中起到return在main函数中作用，终止程序(但递归中return只能返回上层函数)
* exit（0）正常终止  exit（编号）
* stdlib.h

# 八 指针

## 指针运算

<img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230402193956529.png" alt="image-20230402193956529" style="zoom:80%;" />

*  C语言支持三种格式(**只有三种**)的指针运算：a.指针加上一个整数，b.指针减去一个整数，c.两个指针相减
*  指针和整数数相加，结果为 **指针地址 + 指针类型所占字节 * 整数**
   相加结果如果超过指针指向的数组地址范围，结果是未定义的，**除非仅超过数组末尾第一个位置**，C
   保证其有效(只要不解引用就行)
*  指针相减：两个指针指向相同数组，相减结果为两元素相隔x个int所占字节(两个指针之间的距离)
*  <img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230401082116816.png" alt="image-20230401082116816" style="zoom:80%;" />

<img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230402144311279.png" alt="image-20230402144311279" style="zoom:80%;" />

<img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230402144410984.png" alt="image-20230402144410984" style="zoom:80%;" />

* 数组名可以作为指向数组第一个元素的指针，也有解引用，指针运算，但不能给数组名赋新值，其为==指针常量==

## 指针和多维数组

> zippo[4] [2]

* zippo是数组首个元素的地址，所以zippo与&zippo[0]相同，而zippo[0] 是一个包含两个整数的地址，所以zippo[0]与其首个元素地址相同，即zippo[0]与&zippo[0][0]相同，zippo[0]是一个占用1个int大小对象的地址，zippo是一个占用两个int大小对象的地址，它俩值相同（起始于一个位置)
* zippo + 1 与zippo[0] + 1 不同，一个加4字节 一个加8字节
* *zippo[0] 是 zippo[0] [0]; *zippo 是 zippo[0]数组的地址 即&zippo[0] [0]; **zippo == * &zippo[0] [0] == zippo[0] [0]

### 指向多维数组的指针

* **int (* pz) [2]   pz指向一个内含两个int类型值的数组**
* int * pax[2]    pax是一个数组，每个元素都是指向int的指针（声明了一个指针数组）

## 保护数组中的数据

* 対形式参数使用const修饰，使其不可修改指针指向地址所存的值
* 普通指针无法指向const 数据(无法通过编译，不然可以用指针修改值了)，const指针可以指向const数据和非const数据（形参指针有const 才能处理const数据）
* const指针赋给非const指针是不安全的
* 非const指针赋给const指针是有效的，但仅限一级解引用,p306

## 指针常量和常量指针

**常量指针**——指向常量的指针——不能修改值可以修改指向

method 1: const double * ptr;——const与*ptr分开

method 2: double const *ptr;

**指针常量**——一个指针型的常量——不能改变指向，但指向内的值可以改

double * const ptr;

## 空指针和野指针

空指针：不指向任何对象，指向特殊地址0地址

野指针：指向未知区域的指针，未初始化指针为野指针

# 九 指针和数组

## 数组指定位置初始化

```c
int arr[5] = {31,28,[2] = 10,11,12,[1]= 5};
//10 11 12 会从2号下标依次存储   ， 1号下标再次初始化会覆盖原本的28
```

## 数组名的含义

取arr[n]的值 的含义为  *(arr+n)

## 数组名与指针差别

数组名不是指针但神似指针

1. 数组名的内涵在于其指代实体是一种数据结构，这种数据结构就是数组；

   > 所以sizeof可以求出整个数组大小

2. 数组名的外延在于其可以转换为指向其指代实体的指针，而且是一个指针常量

   > 数组名可以赋值给指针,可以作为指针形参传进去

3. 指向数组的指针则是另外一种变量类型（在WIN32平台下，长度为4），仅仅意味着数组的存放地址

4. **数据名可能失去其数据结构内涵** 

   数组名作为函数形参时，在函数体内，其失去了本身的内涵，仅仅只是一个指针

   ==在失去其内涵的同时，它还失去了其常量特性，可以作自增、自减等操作，可以被修改==。

   >  sort( int arr[])  sort函数体内 arr变成了指针



## sizeof求数组长度

sizeof(arr)/sizeof(arr[0]) 为数组长度，但是有时候这样计算会出错

因为**数组作为函数参数传递后，会退化为指针**，所以计算 sizeof(s) = 4 ，实质等价于计算 sizeof(char *s) 的大小，指针变量大小为 4 字节（在 32 位平台上）

# 十 字符串

## 字面值

* 本质上，C 语言是把**字符串字面值**作为**字符数组**来处理的（特殊的char数组）
* 如果字符串字面量之间没有间隔，或用空白字符分隔，C会将其视为串联起来的字符串字面量
* 字符串常量，静态存储类别，如果函数中使用字符串常量，只会存储一次，即使函数调用多次。
* 双引号括起来的内容被视为指向字符串存储位置的指针，类似数组名

<img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230403155452854.png" alt="image-20230403155452854" style="zoom:80%;" />

* 

* 字符串字面值，字符数组\==字符指针常量

* 字符串字面值不可修改

  ```c
  char c = "abc"[1];//c=='b',合法
  char *p = "abc";
  *p = 'A';//将第一个元素改为大写，非法，字符串字面值不可修改(字符数组则可以，改的是字符数组里的)
  const char*q = "abc"//应该吧const数据赋给const指针
  ```

## 字符串变量

* C语言本质上没有专门的字符串，知识逻辑上有，字符串依赖字符数组存在，但不同于字符数组，它认定以'\0'结尾的一些字符为字符串(可能超出字符数组或小于)
* 只有遍历字符串才能确定其长度
* 无法确定字符数组能否标识字符串
* 编写自己处理字符串函数时，要自己处理字符串
* 长度为n的字符串会有n+1的空间，有一个'\0'，char arr[] 字符串容量要比存储字符数大1  留给空字符

### 初始化

* ```c
  char date1[8] = "June 14";//不是字符串字面值
  char date1[8] = {'J', 'u', 'n', 'e', ' ', '1', '4', '\0'};//是数组初始化的简写,不满的地方会补'\0'
  char date3[7] = "June 14";//注意事项：一定要确保字符数组的长度大于初始化式的长度，否则，编译器将忽略空字符，这将使得字符数组无法作为字符串使用。
  char date4[] = "June 14";// **推荐**编译器会自动分配空间，用来存储 "June 14" 中的字符和一个空字符
  ```

### 字符数组和字符指针

* 数组名是常量，指针是变量，都能用数组表示法[i]
* 字符数组char c[ ] , 指针常量，不能改方向，可以修改值
* 字符指针 char *c, 可以改方向，不能修改字符串字面值(声明指针为const类型)
* 字符数组是拷贝了一份，可以arr+1但不能++arr,使用字符指针只有一份字符串，并且字符指针可以自增

### 读写字符串

* ```c
  str[ ] = "abc";
  printf("%m.ps", str);//写到遇到'\0'字符
  puts(str);//并自动输出换行符，
  //弊端：不检查数组越界，可能数组里没'\0'，就输出数组外的值了
  ```
  
* ```c
  char str[10];
  scanf("%s", str);//会忽略前置空白字符，读入，直到遇到空白字符，并会添加'\0'字符
  //弊端：1.不能读取空白字符 2.不检查数组越界(可能填入数据超出数组范围)
  gets(str);//一只读取直到遇到换行符，再把换行符替换成'\0'字符(以行为单位)
  //弊端：1.不检查数组越界，填入超出数组范围了 (被很多标准摒弃)
  ```

* scanf和puts不检查数组越界，**C程序员经常自己写readline函数**

## 字符串函数

### strcpy和strncpy

```c
char *strcpy(char *s1, const char *s2);//效率高，可读性高
//strcpy 函数把 s2 中的字符复制到 s1 指向的数组中，直到遇到 s2 中的第一个空字符为止(该空字符也需要复制)。 strcpy 函数的返回值为 s1。
//弊端：strcpy 是不安全的，它不会检查 s1 指向的数组是否能容纳字符串 s2 中的所有字符，会数组越界
strncpy(str1, str2, sizeof(str1));//但可能没地方存空字符
strncpy(str1, str2, sizeof(str1) - 1);//(指明s1可写字符大小，记得-1)
str1[sizeof(str1) - 1] = '\0';
```

### strlen

```c
size_t strlen (const char *s);//函数返回字符串 s 的长度,不包括空字符
```

### strcat  (concatenate)  strncat

```C
char *strcat(char *s1, const char *s2);//效率高，可读性高
//strcat 函数把字符串 s2 的内容追加到字符串 s1 的末尾，并且返回字符串 s1。
//弊端：strcat 是不安全的，它不会检查 s1 指向的数组是否能容纳所有的字符
strncat(str1, str2, sizeof(str1) - strlen(str1) - 1);//(s1中还可容纳的字符数)
str1[sizeof(str1) - 1] = '\0';
```

### strcmp

```c
int strcmp(const char *s1, const char *s2);
//strcmp 函数比较字符串 s1 和字符串 s2。如果 s1 < s2，则返回小于0的值；如果 s1= s2，则返回0；如果 s1 > s2，则返回大于0的值。(按ASCII)
```



## 字符串数组

1. 二维数组，但浪费空间，不灵活

   ![image-20230404102805018](E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230404102805018.png)

2. 字符指针数组

   ```c
   char* planets[] = {"abc", "def","ghd"};
   ```

   <img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230404102815308.png" alt="image-20230404102815308" style="zoom:80%;" />

## 命令行参数

```c
//操作系统>>应用程序
int main(int argc, char* argv[])//传入参数数量，和字符指针数组
//默认第一个参数是可执行程序的名字，从第二个开始就是自己写在命令行参数栏的
//项目——属性——调试——命令参数
```

<img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230409145756083.png" alt="image-20230409145756083" style="zoom:80%;" />

<img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230409145812376.png" alt="image-20230409145812376" style="zoom:80%;" />

# 十一 结构体和枚举

* 未初始化成员会被赋0(但得初始化过)

<img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230404143723333.png" alt="image-20230404143723333" style="zoom:80%;" />

## 基本操作

1. 成员获取运算符 . 
2. 赋值运算符= (将一个结构体的内容复制到另一个结构体)(**传递或返回结构体**时会引发复制，结构体大则开销很大，所以一般传递结构体指针)
3. ->运算符(结构体指针操作成员，相当于(*p).name

## 初始化

除了基本初始化外
<img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230409205558398.png" alt="image-20230409205558398" style="zoom:80%;" />

## 定义别名

```c
typedef struct labal {
   int number;
    char name[25];
    char gender; //f: female, m: male
    int chinese;
    int math;
    int english;
} Student;// *stu   stu p = &a;
//千万不要定义成指针，没有可读性
```

* 结构体名和指针的区别，结构体是数据类型

  类比变量a与a的地址(int *p = &a)

  访问方式也不一样 .和->

* 若结构体内有结构体自己的类型，则标签不可省略

## 枚举

表示离散的值

```c
// 定义枚举类型
enum Suit{
    // 罗列枚举值，默认0,1,2,3...
    DIAMONDS,
    HEARTS,
    SPADES,
    CLUBS
};
enum Suit s = HEARTS; // 定义枚举类型的变量
typedef enum {
    DIAMONDS = 1,//会从1开始递增
    HEARTS,
    SPADES,
    CLUBS
} Suit;
Suit s = HEARTS;
```

## 复合字面量和结构(C99)

<img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230409212606416.png" alt="image-20230409212606416" style="zoom:80%;" />
<img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230409212659461.png" alt="image-20230409212659461" style="zoom:80%;" />

## 伸缩型数组成员

<img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230409212932011.png" alt="image-20230409212932011" style="zoom:80%;" />
<img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230409212951200.png" alt="image-20230409212951200" style="zoom:80%;" />

## 匿名结构(C11)

## 联合(union)

union可以存储不同类型的元素(**同一时间只能一个**)，以最大元素类型分配空间，

```c
union hold{
    int digit;
    double bigfl;
    char letter;
};
union hold fit;
union hold save[10];
union hold *pu;
```

初始化

```c
union hold valB = valA;
union hold valC = {88};
union hold valD = {.bigfl = 118.2};
```









# 十二 指针高级应用

## 内存分配函数

* malloc(效率最高)

  ```c
  void* malloc(size_t size)//分配 size 个字节的内存块，不对内存块进行清零；如果无法分配指定大小的内存,返回空指针。
  ```

* calloc(清零)

  ```c
  void* calloc(size_t nmemb, size_t size)//为有 nmemb 个元素的数组分配内存块，其中每个元素占 size 个字节，并且对内存块进行清零；如果无法分配指定大小的内存块，返回空指针。
  ```

* realloc(==因为 realloc 可能会移动内存块(when?)，所以一定要记得更新所有指向旧内 存块的指针==)

  ```c
  void* realloc(void *ptr, size_t size)//调整先前分配内存块的大小。如果重新分配内存大小成功，返回指向新内存块的指针，否则返回空指针。第一个参数为空指针，那么它的行为和 malloc 一样。第二个参数为 0，那么它会释放 ptr 指向的内存块。
  ```

* 内存分配函数free

  可能导致的问题：1.只能free由内存分配函数返回的指针 2.统一内存不能free两次 3.指针悬空

注意：
     		1. 字符串分配空间n + 1,有一个'\0'

## 动态分配的结构体(链表)

1. 结构体定义中如果用到指向自己的指针，就不能省略定义的标签

2. 要对指针指向进行修改，需要用到二级指针

## 指向函数的指针(回调函数callback)

### 函数指针

指向函数指令地址的指针

```c
double integrate(double (*f)(double), double a, double b);//建议
double integrate(double f(double), double a, double b);
f  = //给f赋值，要函数类型对应
```

声明并赋值

```c
void toupper(char*);
void (*P)（char*);//声明
p = toupper;//赋值
```



### qsort()

给任意**数组**排序

```c
void qsort(void *base, size_t nmemb, size_t size, int (*compar)(const void *, const void *));

```









## 

# 十五 文件操作

## 缓冲输入和无缓冲输入

缓冲输入：按下Enter后程序才使用（可修改输入错误）

*  行缓冲：遇到换行符刷新缓冲区（发送内容至目标）
*  完全缓冲：缓冲区填满再刷新（文件系统）

* 无缓冲输入：按下后程序立即使用该字符，并打印该字符（游戏）

stdin,stdout行缓冲，siderr无缓冲(C语言给每个进程提供)

<img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230407160241566.png" alt="image-20230407160241566" style="zoom:80%;" />

getchar  scanf读取文件，检测到文件结尾时会返回EOF（-1）

要把键盘输入抽象成文件，那么需要输出文件结尾，就要查看对应操作系统的要求，有的是ctrl+d，有的ctrl+z（windows为一行开始处的ctrl+z）

可以用其读取文件，输出到文件

int fflush( [FILE](https://c-cpp.com/c/io.html) *stream ); 刷新缓冲区

## 文本文件和二进制文件

**区分**：文本内容和二进制内容、文本文件格式和二进制文件格式、文件的文本模式和二进制模式

所有文件的内容都以二进制形式存储

* 若文件使用二进制编码的字符表示文本(ASCII,Unicode)，则是**文本文件**,包含**文本内容**

* 若文件中的二进制代表机器语言代码或者数值数据，就为**二进制文件**，包含**二进制内容**
* 访问文件的两种模式：**文本模式**、**二进制模式**

文本模式：所见内容与实际内容不同，换行符不同，结尾标志不同

二进制模式：可以访问文件的每个字节，换行符不同，结尾标志不同



文本文件有行的概念
文本文件可能有文件末尾标识(windows ctrl+z \x1a)

## ==重定向输入输出p222==

## 打开关闭文件

==打开关闭文件都要判NULL==

### fopen

fopen 不仅打开了文件，还创建了缓冲区(读写模式则两个缓冲区)以及一个包含文件和缓冲区的数据的结构。

```c
FILE* fopen(const char* filename, const char* mode)
//第一个参数是文件的路径，用来定位文件的；第二个参数表示是以何种模式打开文件的。如果无法打开文件， fopen 返回空指针。
```

### fclose

```c
fclose(fp);
```

文件路径：绝对，相对(利于移植)

打开模式：

文本文件：<img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230407161821757.png" alt="image-20230407161821757" style="zoom:80%;" />

二进制文件：<img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230407161838934.png" alt="image-20230407161838934" style="zoom:80%;" />

r：只能读
a:追加写，不存在则创建
w:文件存在则长度清零，不存在则创建

r+:从文件头写(有内容则覆盖)
a+:从文件末尾写入,不存在则创建
w+:清空文件再写入,不存在则创建

[^注]: 写模式和追加模式是不一样的。如果文件存在，写模式会清空原有的数据，而追加模 式会在原有数据的后面写入新的内容(即使光标在前面)。 

**linux中只有一种文件类型，所以带不带b模式都一样**

## 读写文件

### fgetc和fputc

### fgets 和 fputs

* fgets读入n-1个字符，读完文件(EOF)，或者遇到换行符则结束读取，并存储换行符(gets会丢弃)，fgets会在结尾添加空字符(gets不存储换行符)
* fputs原样输出,puts会额外输出换行符

### fscanf和fprintf

## 序列化与反序列化

C语言把文件看作一个字符（字节）的序列，即由一个一个字符（字节）的数据顺序组成。根据数据的组织形式，可分为ASCII文件和二进制文件。ASCII文件又称为文本（text）文件，它的每个字节放一个ASCII代码，代表一个字符。二进制文件是把内存中的数据按其在内在中的存储形式原样输出到磁盘上存放。

C语言中，序列化：将内存中的二进制比特流以哪种方式存储

printf：内存中的数据按转换说明格式转化为字符(即ASCII，每个字符一字节),反序列化再以ASCII转化为内存中的二进制

fwrite：按内存中的二进制形式直接进行序列化，不做更改

[^注]: 如果是直接发送比特流，接受方如何解析这些接收到的比特流呢？这个时候就需要对数据进行序列化，把相应的数据转化成可以自解释比特流。将内存数据转化为数据格式字符串的过程叫做序列化，将数据格式字符串转化为内存数据的过程叫做反序列化。

<img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230409111646374.png" alt="image-20230409111646374" style="zoom:80%;" />

<img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230409150031353.png" alt="image-20230409150031353" style="zoom:80%;" />

### fread和fwrite(二进制文件)

## 文件定位

### fseek

```c
fseek(fp, 0L, SEEK_SET);//fseek 会返回 0；如果发生错误 (比如，请求的位置不存在)，那么fseek 会返回非 0 值
```

### ftell(只能以二进制打开)

ftell 以长整数形式返回当前文件位置(相对文件开头偏移量)

### rewind

回到文件起始位置

### copy文件

```c
void copyFile(const char* src, const char* dst) {
    FILE* src_fp = fopen(src, "rb");
    ERROR_CHECK(src_fp, NULL, "fopen");

    FILE* dst_fp = fopen(dst, "wb");
    ERROR_CHECK(dst_fp, NULL, "fopen");

    char buf[1024];
    int n;
    while ((n = fread(buf, 1, SIZE(buf), src_fp)) > 0) {
        fwrite(buf, 1, n, dst_fp);
    }
```



## 错误处理

errno： int全局变量，每个线程都有一个，定义再errno.h中，默认为0

以通过 perror 和 strerror 来显示错误信息。其中， perror 定义在stdio.h  头文件中， strerror 定义在 string.h 头文件中。

```c
printf("%d\n", errno);
FILE* fp = fopen("not_exist.txt", "r");
printf("%d\n", errno);
printf("%s\n", strerror(errno));//返回指针，指向指定错误码的错误文本；
perror("not_exist.txt");//显示自定文本+当前错误码的错误文本
fprintf(stderr, "open errod%s", str);//以标准错误方式输出(只是改变了输出对象)
```









# 存储类别

## 作用域

块作用域：一对花括号
函数作用域：goto的标签
函数原型作用域：
文件作用域：变量定义在函数外（全局变量）

翻译单元：
<img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230409152346732.png" alt="image-20230409152346732" style="zoom:80%;" />

## 链接

无链接变量：
内部链接变量：
外部链接变量：
<img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230409152506583.png" alt="image-20230409152506583" style="zoom:80%;" />

<img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230409152607194.png" alt="image-20230409152607194" style="zoom:80%;" />

定义全局变量时

```c
int giants = 5; //文件作用域，外部链接(全局作用域，外部变量)
static int dogers = 3; //文件作用域，内部链接(文件私有)
```

## 存储期

静态存储期：程序执行期间一直存在，文件存储期变量(无论有没有static，对于该类变量static指定的是链接属性)
线程存储期：从声明到线程结束
动态存储期：块作用域变量，加static可以变成静态存储期，但只能块内**直接**访问，也只会初始化一次
动态分配存储期：

### 外部链接的静态变量(外部变量)

```c
extern char Coal;//若coal被定义再另一个文件，则必须这样声明
```

外部变量未初始化则默认为0
只能用常量表达式初始化，只能初始化一次

### 存储类别和函数

外部函数：函数默认，其他文件可以使用
静态函数：static，其他文件不可用，可以避免重名

<img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230409162216978.png" alt="image-20230409162216978" style="zoom:80%;" />

# 惯用法

```c
while (getchar() != '\n'); /* skip reset of line */

char ch;
while ((ch = getchar()) == ' '); /* skip blanks */

While(*s)
    s++;//搜索字符串结尾，或者 While(*s++);

While(*p++ = *s++);//复制字符串

while( (ch = getc(fp)) != EOF){//
	/* your code */         
}//getc从文件中逐个读入字符直到遇到文件末尾

while( scanf("%d", &i) == 1){
	/* your code */
}//scanf逐个读取一串整数，在遇到首个问题字符处停止

while( scanf("%d", &i) != 1)
    scanf("%*s");//处理非整数输入

i &= ~(1 << j);//位清零，将第j位清零

i |= 1 << j;//位设置，将第j位设置为1
```

![image-20230402175947701](E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230402175947701.png)

scanf和puts不检查数组越界，自己写readline函数

自己编写strlen和strcat



# 头文件

## limits.h float.h

![image-20230327210812997](E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230327210812997.png)

## math.h

浮点数舍入误差会使逻辑相等的数不相等
3*（1/3）=1.0     但将1/3表示成小数则为0.333333 相乘得0.999999  与1不相等	
fabs()函数（math.h)比较浮点数  返回一个浮点数的绝对值

![image-20230328090821565](E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230328090821565-1680673395518-3.png)

## ctype.h

![image-20230328212008348](E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230328212008348.png)

## stdbool.h

使用该头文件后才可使用bool，并用true和false代表1和0，否则使用_Bool

## iso646.h

可以使用and、or、not代替

![image-20230328213103913](E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230328213103913.png)

## time.h

使用time(NULL) 返回当前时间

## <stdlib.h>

伪随机数生成

rand()生成伪随机数，srand()播种伪随机数，种子一样则随机数一样，利用srand(time(NULL));

exit();

## errno.h

errno： int全局变量，每个线程都有一个，定义再errno.h中，默认为0

以通过 perror 和 strerror 来显示错误信息。其中， perror 定义在stdio.h  头文件中， strerror 定义在 string.h 头文件中。







# 概念

数据对象：存储值的空间

副作用：对数据对象或文件的修改

标识符：名称，指定一个数据对象

左值：指向对象的表达式
<img src="E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230409151247018.png" alt="image-20230409151247018" style="zoom:80%;" />

# C语言陷阱

1. C语言信任程序员原则，不检查数组越界

2. 在C语言中，const 变量不是一个真真正正的常量，其代表的含义仅仅是只读。使用const声明的对象是一个运行时对象，无法使用其作为某个量的初值、数组的长度、case的值或在类型的情形中使用
   变量可以声明数组，但不可以同时初始化
   
   ![image-20230330223630369](E:\CS\markdown notes\学习笔记\C语言笔记.assets\image-20230330223630369.png)

# C与C++的区别

## const

* C++允许声明数组时使用const整数，C不允许
* C++指针赋值检查更加严格，不允许const指针赋给非const指针
* malloc再C++中要强制类型转换，所以都写强制类型转换易于移植
