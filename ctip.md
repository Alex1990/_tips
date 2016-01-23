# About C program

## 2015-06-11

- 声明一个指向结构的指针并不会分配内存，需要手动分配内存，如下代码：

  ```c
  struct tm *timeptr;

  timeptr = malloc(sizeof( struct tm ));
  ```

  不分配，直接使用会抛出 Segmentation Fault 11 错误

- 一个关于定义时间区间的问题，比如我们通常说今天距昨天隔一天，即使是今天早上距离昨天晚上，但实际上可能只差几个小时。

- 关于 `struct tm timeinfo` 的 `timeinfo` 初始化问题，我在 Mac OS 10.10.2/Mac mini 中配使用默认`gcc`编译器编译过后，得设置全六个时间值（`tm_year`, `tm_mon`, `tm_mday`, `tm_hour`, `tm_min`, `tm_sec`）才能得到准确事件，否则会得到一个大于期望时间的时间，并且会变，但是网上见到有人只设置年月日。

- “概念”的更新，今天读`GNU C Manual，里面日期与事件一章，开头就定义了 Calendar time, Elapsed time 等概念，这些有助于从本质上认识平常所说的日期与时间。当然，在命名变量或函数时也很有用。

## 2015-06-12

- 如果使用`math.h`库里面的某些函数，比如`sin`，在编译成可执行代码时，需要加上`-lm`选项，否则会报错`Undefined reference to \`sin\``。

- 声明并赋值一个`double`变量`x = 2.718281828;`，打印到小数点后20位，结果为`2.71828182799999984454`。

- 写惯了JavaScript的字符串字面量，再写C的，还是容易写单引号，切换不过来。

- `strcat()`会查找第一个参数中的字符串截止符`\0`，然后在此追加第二个参数提供的字符串，所以第一个参数必须是已有的字符串或者初始化。这点和`strcpy`不同。

- __*__ 让指针或字符数组获得一个字符串值的方法有哪些以及区别？

- 字符数组越界默认编译警告与错误，能正常访问越界位置的字符，`strncpy(char *dest, char *src, size_t n)`也能复制超出`dest`长度的字符。

## 2015-06-13

- 使用`printf()`输出`NULL`：

  ```c
  printf("NULL pointer: %s\n", NULL); // 输出"(null)"
  printf("NULL pointer: %p\n", NULL); // 输出"0x0"
  ```

- TutorialsPoint关于`strtok`的说明不详细，而且这个函数工作方式不直观，详细说明：[http://man7.org/linux/man-pages/man3/strtok.3.html](http://man7.org/linux/man-pages/man3/strtok.3.html)。

- 搜索并学习 C Library 中的函数，使用 Google 搜索“man strstr”或“man strstr”，通常第一个链接就是 Linux Man Page，里面说明比较详细。从下面学到的：

  > People don't read books and ask questions, that's very bad and not a sign of a future good programmer. Anyways, run two search on Google "man strchr" and "man strstr". Click on the first link that comes up and read what it means. You will get your answer. This technique takes less time to learn compared to posting questions on Yahoo Answers and awaiting answers.

  这同样适用于知乎、QQ群，甚至 Stack Overflow。有些问题并不适合在这些地方问，有些问题只要按部就班地学下来自然就懂了。

- C Library 函数命名含义：这个不是所有都知道，有些直观（比如`math.h`中的函数），有些得搜索下（比如`strpbrk`），有些就暂时查不到（比如`strrch`）。

## 2015-06-18

- 使用GCC编译时，`-ansi`选项可以按照 ISO C 标准来编译。GNU C 兼容 ISO C，并有自己的扩展及宽松的约束。

## 2015-06-20

- 注意一些概念：整数（integer）、正整数（positive integer）、自然数（natural number）、实数（real number）、小数（decimal）、小数部分（fractional part）、浮点数（floating number）。弄清这些好让写的正则或判断函数名字贴切。

- [vim]替换的分隔符不仅仅可以使用`/`，还可以`!`、`$`等，这可以避免转义。

## 2015-06-29

- 使用`free()`释放`struct`变量的内存时，记得释放结构成员为指针变量的内存。

  ```c
  free(buf->contents);
  free(buf);
  ```

- `malloc()` vs `calloc()`：

  - 两者请求的内存大小一样（当然前提是传合适的参数）
  - `calloc()`会将内存位用0填充，而`malloc()`不会，好比：`calloc()` = `malloc()` + `memset()`。
  - 性能对比，根据SO上面的说法，有些系统会优化`calloc()`的调用，具体不太了解

  有人这样记忆：The *alloc variants are pretty mnemonic - clear-alloc, memory-alloc, re-alloc.

  参考：http://stackoverflow.com/questions/1538420/difference-between-malloc-and-calloc

- `cd -`表示跳转到上一次路径

## 2015-07-02

- 隐藏控制台/终端的光标：`printf("\e[?25l")`。

## 2015-07-03

- 同时定义了一个函数和宏，名字都叫`max`，则默认使用宏，有两种方式调用函数定义：
  - 名字两侧加括号，如`(max)`
  - 使用`#undef max`

- 使用宏定义函数功能时，注意两个问题：side-effect 和操作符优先级问题，查看实际例子：http://stackoverflow.com/questions/3437404/min-and-max-in-c

## 2015-07-26

- 检测`char *`变量字符是否为 null character：`!*p`或`*p == '\0'`。注意空指针（`NULL`）与空字符（null character）不一样，但两者值相等，都为 0，

- **byte-wise vs character-wise**：按字节处理与按字符处理，前者只能通常处理单字节字符，后者可处理多字节字符。

- 注意字符串字面量（string literals）所占用的内存空间（allocated size）与其字符串长度（length）差 1，就是 terminating null character。

## 2015-08-10

- 创建一个目录并进入：`mkdir foo && cd $_`，`$_`存储最近的一个参数。

  或者

  ```bash
  #!/bin/sh
  mkdir $1 && cd $1
  ```

## 2015-08-12

- 删除一个 symbolic link：`rm symbolic link`

## 2015-09-18

- Mac OSX下默认编译器，为啥不能直接写 -2147483648 原始值呢？提示应该使用`long`类型，而`1 << 31`就不会报错。

## 2015-09-22

- `void *`是一个指针，可以为任一类型，但是不可以使用间接引用操作符`*`获取指针所指向的值，必须先转成相应类型的指针，假如有`void *lh`，`lh`为`int`类型：

  ```c
  int *a = lh;
  int b = *(int*) lh;
  printf("%d\n", *(int*) lh);
  ```

## 2015-10-14

- 假设有下面定义，则`malloc(sizeof(PtrToNode))`分配一个指针变量的内存空间，`malloc(sizeof(struct Node))`才会分配一个 Node 单元的空间。

  ```c
  struct Node;
  typedef struct Node *PtrToNode;
  typedef PtrToNode Position;
  struct Node {
    ElementType Element;
    Position Next;
  }
  ```

## 2015-10-21

- Mac OSX 和 Ubuntu 14.04 里面不存在`syscalls.h`，可使用`unistd.h`，但是`BUFSIZ`不知道在哪里有定义。

## 2015-12-27

- 如何快速查看 Bash 手册页的 `read` 描述？

  ```bash
  help read
  help -m read # man page like
  help -m read | less
  ```

  可以将 Bash 手册打印成 pdf 或其他格式，然后方便参考。

  Ref: http://unix.stackexchange.com/questions/96095/reading-and-searching-long-man-pages

## 2016-01-24

- `malloc`返回值是否需要强制类型转换？

  **不需要**，首先，`malloc`返回值类型是`void *`，该类型可赋值给其他类型指针；其次， C 和 C++ 是两种语言，不需要相互兼容；还要，不需要写冗余的代码。

  参考：http://stackoverflow.com/questions/605845/do-i-cast-the-result-of-malloc。

