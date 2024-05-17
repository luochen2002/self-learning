## 示例

已经有一个现成的字符串数组，`main`函数参数中的`char *argv[]`。下面这段代码打印出了所有传入的命令行参数：

```c
#include <stdio.h>

int main(int argc, char *argv[])
{
    int i = 0;

    // go through each string in argv
    // why am I skipping argv[0]?
    for(i = 1; i < argc; i++) {
        printf("arg %d: %s\n", i, argv[i]);
    }

    // let's make our own array of strings
    char *states[] = {
        "California", "Oregon",
        "Washington", "Texas"
    };
    int num_states = 4;

    for(i = 0; i < num_states; i++) {
        printf("state %d: %s\n", i, states[i]);
    }

    return 0;
}
```

`for`循环的格式是这样的：

```c
for(INITIALIZER; TEST; INCREMENTER) {
    CODE;
}
```

下面是`for`循环的工作机制：

- `INITIALIZER`中是用来初始化循环的代码，这个例子中它是`i = 0`。
- 接下来会检查`TEST`布尔表达式，如果为`false`（0）则跳过`CODE`，不做任何事情。
- 执行`CODE`，做它要做的任何事情。
- 在`CODE`执行之后会执行`INCREMENTER`部分，通常情况会增加一些东西，比如这个例子是`i++`。
- 然后跳到第二步继续执行，直到`TEST`为`false`（0）为止。

例子中的`for`循环使用`argc`和`argv`，遍历了命令行参数，像这样：

- OS将每个命令行参数作为字符串传入`argv`数组，程序名称`./ex10`在下标为0的位置，剩余的参数紧随其后。
- OS将`argc`置为`argv`数组中参数的数量，所以你可以遍历它们而不会越界。要记住如果你提供了一个参数，程序名称是第一个，参数应该在第二个。
- 接下来程序使用`i < argc`测试`i`是否使用`argc`，由于最开始`1 < 2`，测试通过。
- 之后它会执行代码，输出`i`，并且将`i`用做`argv`的下标。
- 然后使用`i++`来运行自增语句，它是`i = i + 1`的便捷形式。
- 程序一直重复上面的步骤，直到`i < argc`值为`false`（0），这时退出循环但程序仍然继续执行

> * `argc`代表命令行参数的个数，是一个整数值，虽然它也是一个参数，但不用去单独输入，在给`argv[ ]`传参后，系统会根据空格自动记录共有多少参数，并赋值给`argc`。
> * `argc`至少为1，因为**第一个参数永远是程序的名称（即执行程序的文件名）**所以，当没有额外的命令行参数传入时，`argc`的值为1。
> * `argv`是一个指向字符串数组的指针，每个元素都是一个指向传递给程序的参数的指针。

## 你会看到什么

你需要用两种方法运行它来玩转这个程序。第一种方法是向命令行参数传递一些东西来设置`argc`和`argv`。第二种是不传入任何参数，于是你可以看到第一次的`for`循环没有被执行，由于`i < argc`值为`false`。

```shell
// 将i改为i=0可以看到程序名称为第一个参数
$ valgrind ./ex10 abc
...
arg 0: ./ex10
arg 1: abc
...
```

## 理解字符串数组

你应该可以从这个练习中弄明白，你在C语言中通过混合`char *str = "blah"`和`char str[] = {'b','l','a','h'}`语法构建二维数组来构建字符串数组。第十四行的`char *states[] = {...}`语法就是这样的二维混合结构，其中每个字符串都是数组的一个元素，字符串的每个字符又是字符串的一个元素。

感到困惑吗？多维的概念是很多人从来都不会去想的，所以你应该在纸上构建这一字符串数组：

- 在纸的左边为每个字符串画一个小方格，带有它们的下标。
- 然后在方格上方写上每个字符的下标。
- 接着将字符串中的字符填充到方格内。
- 画完之后，在纸上模拟代码的执行过程。

## 如何使它崩溃

- 使用你喜欢的另一种语言，来写这个程序。传入尽可能多的命令行参数，看看是否能通过传入过多参数使其崩溃。

- 将`i`初始化为0看看会发生什么。是否也需要改动`argc`，不改动的话它能正常工作吗？为什么下标从0开始可以正常工作？

  - 不需要改动`argc`

- 将`num_states`改为错误的值使它变大，来看看会发生什么。

  - ```shell
    ==3976== Conditional jump or move depends on uninitialised value(s)
    ==3976==    at 0x48E573B: __vfprintf_internal (vfprintf-internal.c:1517)
    ==3976==    by 0x48D079E: printf (printf.c:33)
    ==3976==    by 0x109235: main (ex10.c:21)
    ==3976== 
    state 4: (null)
    ==3976== Invalid read of size 1
    ==3976==    at 0x4851E56: strlen (vg_replace_strmem.c:505)
    ==3976==    by 0x48E6D30: __vfprintf_internal (vfprintf-internal.c:1517)
    ==3976==    by 0x48D079E: printf (printf.c:33)
    ==3976==    by 0x109235: main (ex10.c:21)
    ==3976==  Address 0x5be42d09d928d200 is not stack'd, malloc'd or (recently) free'd
    ==3976== 
    ==3976== 
    ==3976== Process terminating with default action of signal 11 (SIGSEGV): dumping core
    ==3976==  General Protection Fault
    ==3976==    at 0x4851E56: strlen (vg_replace_strmem.c:505)
    ==3976==    by 0x48E6D30: __vfprintf_internal (vfprintf-internal.c:1517)
    ==3976==    by 0x48D079E: printf (printf.c:33)
    ==3976==    by 0x109235: main (ex10.c:21)
    ==3976== 
    ==3976== HEAP SUMMARY:
    ==3976==     in use at exit: 1,024 bytes in 1 blocks
    ==3976==   total heap usage: 1 allocs, 0 frees, 1,024 bytes allocated
    ==3976== 
    ==3976== LEAK SUMMARY:
    ==3976==    definitely lost: 0 bytes in 0 blocks
    ==3976==    indirectly lost: 0 bytes in 0 blocks
    ==3976==      possibly lost: 0 bytes in 0 blocks
    ==3976==    still reachable: 1,024 bytes in 1 blocks
    ==3976==         suppressed: 0 bytes in 0 blocks
    ==3976== Rerun with --leak-check=full to see details of leaked memory
    ==3976== 
    ==3976== Use --track-origins=yes to see where uninitialised values come from
    ==3976== For lists of detected and suppressed errors, rerun with: -s
    ==3976== ERROR SUMMARY: 2 errors from 2 contexts (suppressed: 0 from 0)
    Segmentation fault (core dumped)
    ```

  - `Conditional jump or move depends on uninitialised value(s)`，表明程序中存在使用未初始化的值进行条件跳转或移动的情况。

  - `Invalid read of size 1`，表明程序中存在对无效内存地址进行大小为1的读取操作。可能是由于试图读取一个空指针（NULL）或非法内存地址导致。

## 附加题

- 弄清楚在`for`循环的每一部分你都可以放置什么样的代码。

  - `for(INITIALIZER; TEST; INCREMENTER) {CODE;}`
  - INITIALIZER：做循环初始化，可不写
  - TEST：循环条件，可设为死循环
  - INCREMENTER：迭代语句
  - 循环内可以使用`,`来分隔小块，例如可以设置多个迭代语句，每个语句用`,`分隔

- 查询如何使用`','`（逗号）字符来在`for`循环的每一部分中，`';'`（分号）之间分隔多条语句。

  - `for(INITIALIZER, INITIALIZER; TEST; INCREMENTER, INCREMENTER, INCREMENTER) {CODE;}`

- 查询`NULL`是什么东西，尝试将它用做`states`的一个元素，看看它会打印出什么。

  - STFW，[why is there a NULL in the C language](https://stackoverflow.com/questions/61718737/why-is-there-a-null-in-the-c-language)
  - RTFM，Section 6.3.2.3p3 of the [C standard](http://www.open-std.org/jtc1/sc22/wg14/www/docs/n1570.pdf) states and section 7.19p3 states
  - 如果将NULL用作char \*states[]的一个元素，它会被视为一个指向空字符串的指针，即一个空指针。打印一个空指针时，它会打印 (null)

- 看看你是否能在打印之前将`states`的一个元素赋值给`argv`中的元素，再试试相反的操作。

  - ```c
    argv[2] = states[2];
    printf("argv[2] = states[2]: %s\n", argv[2]);
        
    states[2] = argv[0];
    printf("states[2] = argv[0]: %s\n", states[2]);
    ```

  - ```c
    argv[2] = states[2]: Washington
    states[2] = argv[0]: ./ex10
    ```