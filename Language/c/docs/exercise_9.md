## 示例

上一个练习中，我们学习了如何创建基本的数组，以及数组如何映射为字符串。这个练习中我们会更加全面地展示数组和字符串的相似之处，并且深入了解更多内存布局的知识。

这个练习向你展示了C只是简单地将字符串储存为字符数组，并且在结尾加上`'\0'`（空字符）。你可能在上个练习中得到了暗示，因为我们手动这样做了。下面我会通过将它与数字数组比较，用另一种方法更清楚地实现它。

```c
#include <stdio.h>

int main(int argc, char *argv[])
{
    int numbers[4] = {0};
    char name[4] = {'a'};

    // first, print them out raw
    printf("numbers: %d %d %d %d\n",
            numbers[0], numbers[1],
            numbers[2], numbers[3]);

    printf("name each: %c %c %c %c\n",
            name[0], name[1],
            name[2], name[3]);

    printf("name: %s\n", name);

    // setup the numbers
    numbers[0] = 1;
    numbers[1] = 2;
    numbers[2] = 3;
    numbers[3] = 4;

    // setup the name
    name[0] = 'Z';
    name[1] = 'e';
    name[2] = 'd';
    name[3] = '\0';

    // then print them out initialized
    printf("numbers: %d %d %d %d\n",
            numbers[0], numbers[1],
            numbers[2], numbers[3]);

    printf("name each: %c %c %c %c\n",
            name[0], name[1],
            name[2], name[3]);

    // print the name like a string
    printf("name: %s\n", name);

    // another way to use name
    char *another = "Zed";

    printf("another: %s\n", another);

    printf("another each: %c %c %c %c\n",
            another[0], another[1],
            another[2], another[3]);

    return 0;
}
```

在这段代码中，我们创建了一些数组，并对数组元素赋值。在`numbers`中我们设置了一些数字，然而在`names`中我们实际上手动构造了一个字符串。

## 你会看到什么

当你运行这段代码的时候，你应该首先看到所打印的数组的内容初始化为0值，之后打印初始化后的内容：

```shell
$ make ex9
cc -Wall -g    ex9.c   -o ex9
$ ./ex9
numbers: 0 0 0 0
name each: a   
name: a
numbers: 1 2 3 4
name each: Z e d
name: Zed
another: Zed
another each: Z e d
```

你会注意到这个程序中有一些很有趣的事情：

- 我并没有提供全部的4个参数来初始化它。这是C的一个简写，如果你只提供了一个元素，剩下的都会为0。
- `numbers`的每个元素被打印时，它们都输出0。
- `names`的每个元素被打印时，只显示了第一个元素`'a'`，因为`'\0'`是特殊字符而不会显示。
- 然后我们首次打印`names`，打印出了`"a"`，因为在初始化表达式中，`'a'`字符之后的空间都用`'\0'`填充，是以`'\0'`结尾的有效字符串。
- 我们接着通过手动为每个元素赋值来建立数组，并且再次把它打印出来。看看他们发生了什么改变。现在`numbers`已经设置好了，看看`names`字符串如何正确打印出我的名字。
- 创建一个字符串也有两种语法：第六行的`char name[4] = {'a'}`，或者第44行的`char *another = "name"`。前者不怎么常用，你应该将后者用于字符串字面值。

注意我使用了相同的语法和代码风格来和整数数组和字符数组交互，但是`printf`认为`name`是个字符串。再次强调，这是因为对C语言来说，字符数组和字符串没有什么不同。

最后，当你使用字符串字面值时你应该用`char *another = "Literal"`语法，它会产生相同的东西，但是更加符合语言习惯，也更省事。

## 如何使它崩溃

C中所有bug的大多数来源都是忘了预留出足够的空间，或者忘了在字符串末尾加上一个`'\0'`。事实上，这些bug是非常普遍并且难以改正的，大部分优秀的C代码都不会使用C风格字符串。下一个练习中我们会学到如何彻底避免C风格字符串。

使这个程序崩溃的的关键就是拿掉字符串结尾的`'\0'`。下面是实现它的一些途径：

- 删掉`name`的初始化表达式。
- 无意中设置`name[3] = 'A'`，于是它就没有终止字符了。
- 将初始化表达式设置为`{'a','a','a','a'}`，于是就有过多的`'a'`字符，没有办法给`'\0'`留出位置。

试着想出一些其它的办法让它崩溃，并且在`Valgrind`下像往常一样运行这个程序，你可以看到具体发生了什么，以及错误叫什么名字。有时`Valgrind`并不能发现你犯的错误，则需要移动声明这些变量的地方看看是否能找出错误。这是C的黑魔法的一部分，有时变量的位置会改变bug。

```c
// 使用指针给name[3]赋值
int *ptr;
ptr = &name[3];
*ptr = 'A';
```

```shell
// 编译时显示
test.c:9:13: warning: assignment to ‘int *’ from incompatible pointer type ‘char *’
```

```
// 输出显示
name each: a   A
name: a
// Valgrind报告错误
*** stack smashing detected ***: terminated
==3352== 
==3352== Process terminating with default action of signal 6 (SIGABRT): dumping core
==3352==    at 0x49069FC: __pthread_kill_implementation (pthread_kill.c:44)
==3352==    by 0x49069FC: __pthread_kill_internal (pthread_kill.c:78)
==3352==    by 0x49069FC: pthread_kill@@GLIBC_2.34 (pthread_kill.c:89)
==3352==    by 0x48B2475: raise (raise.c:26)
==3352==    by 0x48987F2: abort (abort.c:79)
==3352==    by 0x48F9675: __libc_message (libc_fatal.c:155)
==3352==    by 0x49A6599: __fortify_fail (fortify_fail.c:26)
==3352==    by 0x49A6565: __stack_chk_fail (stack_chk_fail.c:24)
==3352==    by 0x109359: main (test.c:57)
==3352== 
==3352== HEAP SUMMARY:
==3352==     in use at exit: 1,024 bytes in 1 blocks
==3352==   total heap usage: 1 allocs, 0 frees, 1,024 bytes allocated
==3352== 
==3352== LEAK SUMMARY:
==3352==    definitely lost: 0 bytes in 0 blocks
==3352==    indirectly lost: 0 bytes in 0 blocks
==3352==      possibly lost: 0 bytes in 0 blocks
==3352==    still reachable: 1,024 bytes in 1 blocks
==3352==         suppressed: 0 bytes in 0 blocks
==3352== Rerun with --leak-check=full to see details of leaked memory
==3352== 
==3352== For lists of detected and suppressed errors, rerun with: -s
==3352== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)
Aborted (core dumped)
```

`stack smashing detected`这是一种堆栈溢出错误。通常表示在程序中发生了缓冲区溢出，导致堆栈被破坏，从而触发了堆栈保护机制。

在Valgrind的LEAK SUMMARY中，显示1024字节的堆内存仍可达，我们看HEAP SUMMARY分配了1024字节，说明该内存没有被释放，从而导致了可达内存块。按照提示我们使用`--leak-check=full`看看

```
==3388== LEAK SUMMARY:
==3388==    definitely lost: 0 bytes in 0 blocks
==3388==    indirectly lost: 0 bytes in 0 blocks
==3388==      possibly lost: 0 bytes in 0 blocks
==3388==    still reachable: 1,024 bytes in 1 blocks
==3388==         suppressed: 0 bytes in 0 blocks
```

遇到问题记得RTFM，在[Valgrind FAQ](https://valgrind.org/docs/manual/faq.html#faq.deflost)可以找到相关信息。

- "definitely lost" means your program is leaking memory -- fix those leaks!
- "indirectly lost" means your program is leaking memory in a pointer-based structure. (E.g. if the root node of a binary tree is "definitely lost", all the children will be "indirectly lost".) If you fix the "definitely lost" leaks, the "indirectly lost" leaks should go away.
- "possibly lost" means your program is leaking memory, unless you're doing unusual things with pointers that could cause them to point into the middle of an allocated block; see the user manual for some possible causes. Use `--show-possibly-lost=no` if you don't want to see these reports.
- "still reachable" means your program is probably ok -- it didn't free some memory it could have. This is quite common and often reasonable. Don't use `--show-reachable=yes` if you don't want to see these reports.
- "suppressed" means that a leak error has been suppressed. There are some suppressions in the default suppression files. You can ignore suppressed errors.

`still reachable`：可以访问，未丢失但也未释放。如果程序是正常结束的，那么它可能不会造成程序崩溃，但长时间运行有可能耗尽系统资源，因此建议修复它。如果程序是崩溃（如访问非法的地址而崩溃）而非正常结束的，则应当暂时忽略它，先修复导致程序崩溃的错误，然后重新检测。

> 希望编写的程序都能：no leaks, no errors

## 附加题

- 将一些字符赋给`numbers`的元素，之后用`printf`一次打印一个字符，你会得到什么编译器警告？
  - `int numbers[4] = {'abc', 'def'};`
  - `test.c:5:23: warning: multi-character character constant `，这表示在程序中多字符字符常量，字符常量通常只能包含一个字符
  - 上述输出为`numbers: c f`，多字符字符常量的行为是依赖于编译器的，不同的编译器可能会有不同的实现
  - 试试修改输出`printf("numbers: %c %d %c %d\n"，...);`会得到`numbers: c 6579558  0`
  - 先将`6579558`换算成二进制，然后查表可知为`def`
- 对`names`执行上述的相反操作，把`names`当成`int`数组，并一次打印一个`int`，`Valgrind`会提示什么？
  - `char name[4] = {123, 321};`
  - `test.c:6:26: warning: overflow in conversion from ‘int’ to ‘char’ changes value from ‘321’ to ‘65’`，表示在转换从`int`到`char`时发生了溢出，值从`321`变成了`65`
  - 输出为`name each: { A`，在ASCII中：`123`为`{`，`65`为`A`
- 有多少种其它的方式可以用来打印它？
- 如果一个字符数组占四个字节，一个整数也占4个字节，你可以像整数一样使用整个`name`吗？你如何用黑魔法实现它？
  - 用指针看看？
  - 大端和小端
- 拿出一张纸，将每个数组画成一排方框，之后在纸上画出代码中的操作，看看是否正确。
- 将`name`转换成`another`的形式，看看代码是否能正常工作。
  - 无错误