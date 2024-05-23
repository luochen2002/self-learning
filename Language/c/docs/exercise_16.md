## 示例

学会如何创建`struct`，将一个指针指向它们，以及使用它们来理解内存的内部结构

```c
#include <stdio.h>
#include <assert.h>
#include <stdlib.h>
#include <string.h>

struct Person {
    char *name;
    int age;
    int height;
    int weight;
};

struct Person *Person_create(char *name, int age, int height, int weight)
{
    struct Person *who = malloc(sizeof(struct Person));
    assert(who != NULL);
	
    /*	char *strdup(const char *s);
     *	The  strdup() function returns a pointer to a new string which is a duplicate of the string s.
     *	Memory for the new string is obtained  with malloc(3), and can be freed with free(3).
     */
    who->name = strdup(name);
    who->age = age;
    who->height = height;
    who->weight = weight;

    return who;
}

void Person_destroy(struct Person *who)
{
    /*	void assert(scalar expression);
     *	If expression is false, assert() prints an error message to standard error and terminates the program by calling abort(3).   The  error  message  includes the name of the file and
     	function containing the assert() call, the source code line number of the call, and the text of the argument; 
     	something like:
     		prog: some_file.c:16: some_func: Assertion `val == 0' failed.
     */
    assert(who != NULL);

    free(who->name);
    free(who);
}

void Person_print(struct Person *who)
{
    printf("Name: %s\n", who->name);
    printf("\tAge: %d\n", who->age);
    printf("\tHeight: %d\n", who->height);
    printf("\tWeight: %d\n", who->weight);
}

int main(int argc, char *argv[])
{
    // make two people structures
    struct Person *joe = Person_create(
            "Joe Alex", 32, 64, 140);

    struct Person *frank = Person_create(
            "Frank Blank", 20, 72, 180);

    // print them out and where they are in memory
    printf("Joe is at memory location %p:\n", joe);
    Person_print(joe);

    printf("Frank is at memory location %p:\n", frank);
    Person_print(frank);

    // make everyone age 20 years and print them again
    joe->age += 20;
    joe->height -= 2;
    joe->weight += 40;
    Person_print(joe);

    frank->age += 20;
    frank->weight += 20;
    Person_print(frank);

    // destroy them both so we clean up
    Person_destroy(joe);
    Person_destroy(frank);

    return 0;
}
```

包含（`include`）

包含了一些新的头文件，来访问一些新的函数。每个头文件都提供了什么东西？（RTFM）

* `<stdio.h>`：The  standard  I/O  library  provides  a  simple and efficient buffered stream I/O interface.  Input and output is  mapped  into  logical  data streams  and the physical I/O characteristics are concealed
* `assert.h`：assert.h defines the macro `assert`. The macro can be used to verify assumptions made by the program and print a diagnostic message if this assumption is false
* `stdlib.h`：stdlib.h is the header of the general purpose standard library of C programming language which includes functions involving memory allocation, process control, conversions and others.
* `string.h`：string.h is the header in the C standard library for the C programming language which contains macro definitions, constants and declarations of functions and types used not only for string handling but also various memory handling functions; the name is thus something of a misnomer.

`struct Person`

这是创建结构体的地方，结构体含有四个成员来描述一个人。最后得到了一个复合类型，让我们通过一个名字来整体引用这些成员，或它们的每一个。

`Pearson_create` 函数

需要一个方法来创建这些结构体，定义一个函数来实现。下面是这个函数做的几件重要的事情：

- 使用用于内存分配的`malloc`来向OS申请一块原始的内存。
- 向`malloc`传递`sizeof(struct Person)`参数，它计算结构体的大小，包含其中的所有成员。
- 使用了`assert`来确保从`malloc`得到一块有效的内存。有一个特殊的常量叫做`NULL`，表示“未设置或无效的指针”。这个`assert`大致检查了`malloc`是否会返回`NULL`。
- 使用`x->y`语法来初始化`struct Person`的每个成员，它指明了所初始化的成员。
- 使用`strdup`来复制字符串`name`，是为了确保结构体真正拥有它。`strdup`的行为实际上类似`malloc`但是它同时会将原来的字符串复制到新创建的内存。

> `x->y`是`(*x).y`的简写。

`Person_destroy` 函数

如果定义了创建函数，那么一定需要一个销毁函数，它会销毁`Person`结构体。我再一次使用了`assert`来确保不会得到错误的输入。接着我使用了`free`函数来交还通过`malloc`和`strdup`得到的内存。如果你不这么做则会出现“内存泄露”。

> 不想显式释放内存又能避免内存泄露的办法是引入`libGC`库。你需要把所有的`malloc`换成`GC_malloc`，然后把所有的`free`删掉。

`Person_print` 函数

接下来我需要一个方法来打印出人们的信息，这就是这个函数所做的事情。它用了相同的`x->y`语法从结构体中获取成员来打印。

`main` 函数

在`main`函数中使用了所有前面的函数和`struct Person`来执行下面的事情：

- 创建了两个人：`joe`和`frank`。
- 把它们打印出来，注意我用了`%p`占位符，所以你可以看到程序实际上把结构体放到了哪里。
- 把它们的年龄增加20岁，同时增加它们的体重。
- 之后打印出每个人。
- 最后销毁结构体，以正确的方式清理它们。

请仔细阅读上面的描述，然后做下面的事情：

- 查询每个你不了解的函数或头文件。记住你通常可以使用`man 2 function`或者`man 3 function`来让它告诉你。你也可以上网搜索资料。
- 在每一行上方编写注释，写下这一行代码做了什么。
- 跟踪每一个函数调用和变量，你会知道它在程序中是在哪里出现的。
- 同时也查询你不清楚的任何符号。

## 你会看到什么

在你使用描述性注释扩展程序之后，要确保它实际上能够运行，并且产生下面的输出：

```shell
$ ./ex16
Joe is at memory location 0x5a5f4a0fc2a0:
Name: Joe Alex
        Age: 32
        Heigh+t: 64
        Weight: 140
Frank is at memory location 0x5a5f4a0fc2e0:
Name: Frank Blank
        Age: 20
        Height: 72
        Weight: 180
Name: Joe Alex
        Age: 52
        Height: 62
        Weight: 180
Name: Frank Blank
        Age: 40
        Height: 72
        Weight: 200
```

## 解释结构体

C中的结构体是其它数据类型（变量）的一个集合，它们储存在一块内存中，然而你可以通过独立的名字来访问每个变量。它们就类似于数据库表中的一行记录，或者面向对象语言中的一个非常简单的类。让我们以这种方式来理解它：

- 在上面的代码中，创建了一个结构体，它们的成员用于描述一个人：名称、年龄、体重、身高。
- 每个成员都有一个类型，比如是`int`。
- C会将它们打包到一起，于是它们可以用单个的结构体来存放。
- `struct Person`是一个复合类型，这意味着你可以在同种表达式中将其引用为其它的数据类型。
- 可以将这一紧密的组合传递给其它函数，就像`Person_print`那样。
- 如果结构体是指针的形式，接着可以使用`x->y`通过它们的名字来访问结构体中独立的部分。
- 还有一种创建结构体的方法，不需要指针，通过`x.y`来访问。你将会在附加题里面见到它。

如果不使用结构体，则需要自己计算出大小、打包以及定位出指定内容的内存片位置。实际上，在大多数早期（甚至现在的一些）的汇编代码中，这就是唯一的方式。在C中你就可以让C来处理这些复合数据类型的内存构造，并且专注于和它们交互。

## 如何使它崩溃

使这个程序崩溃的办法涉及到使用指针和`malloc`系统的方法：

- 试着传递`NULL`给`Person_destroy`来看看会发生什么。如果它没有崩溃，你必须移除Makefile的`CFLAGS`中的`-g`选项。

  - ``test: test.c:28: Person_destroy: Assertion `who != NULL' failed.``
  - `still reachable: 1,060 bytes in 3 blocks`
  - `Aborted (core dumped)`

- 在结尾处忘记调用`Person_destroy`，在`Valgrind`下运行程序，你会看到它报告出你忘记释放内存。弄清楚你应该向`valgrind`传递什么参数来让它向你报告内存如何泄露。

  - ```shell
    $ valgrind ./test
    ==5301== Memcheck, a memory error detector
    ==5301== Copyright (C) 2002-2024, and GNU GPL'd, by Julian Seward et al.
    ==5301== Using Valgrind-3.23.0 and LibVEX; rerun with -h for copyright info
    ==5301== Command: ./test
    ==5301== 
    Joe is at memory location 0x4a9c040:
    Name: Joe Alex
            Age: 32
            Height: 64
            Weight: 140
    Frank is at memory location 0x4a9c0f0:
    Name: Frank Blank
            Age: 20
            Height: 72
            Weight: 180
    Name: Joe Alex
            Age: 52
            Height: 62
            Weight: 180
    Name: Frank Blank
            Age: 40
            Height: 72
            Weight: 200
    sizeof_struct_Person:24
    sizeof_joe:24
    sizeof_frank:24
    ==5301== 
    ==5301== HEAP SUMMARY:
    ==5301==     in use at exit: 69 bytes in 4 blocks
    ==5301==   total heap usage: 5 allocs, 1 frees, 1,093 bytes allocated
    ==5301== 
    ==5301== LEAK SUMMARY:
    ==5301==    definitely lost: 48 bytes in 2 blocks
    ==5301==    indirectly lost: 21 bytes in 2 blocks
    ==5301==      possibly lost: 0 bytes in 0 blocks
    ==5301==    still reachable: 0 bytes in 0 blocks
    ==5301==         suppressed: 0 bytes in 0 blocks
    ==5301== Rerun with --leak-check=full to see details of leaked memory
    ==5301== 
  ==5301== For lists of detected and suppressed errors, rerun with: -s
    ==5301== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)
    ```
    
  - definitely lost有48 bytes的内存泄露并且没有变量指向该内存，indirectly lost有21字节内存泄漏，并且没有变量指向该内存，本身没有泄露但与泄露有关，indirectly lost一般预示有definitely lost错误，解决后者就解决了前者。详见[exe9](exercise_9)
    
- 使用`--leak-check=full`参数
  
    ```shell
    ==5385== HEAP SUMMARY:
    ==5385==     in use at exit: 69 bytes in 4 blocks
    ==5385==   total heap usage: 5 allocs, 1 frees, 1,093 bytes allocated
    ==5385== 
    ==5385== 33 (24 direct, 9 indirect) bytes in 1 blocks are definitely lost in loss record 3 of 4
    ==5385==    at 0x484880F: malloc (vg_replace_malloc.c:446)
    ==5385==    by 0x1091EB: Person_create (in /home/lc/learning-c/test)
    ==5385==    by 0x10936E: main (in /home/lc/learning-c/test)
    ==5385== 
    ==5385== 36 (24 direct, 12 indirect) bytes in 1 blocks are definitely lost in loss record 4 of 4
    ==5385==    at 0x484880F: malloc (vg_replace_malloc.c:446)
    ==5385==    by 0x1091EB: Person_create (in /home/lc/learning-c/test)
    ==5385==    by 0x109390: main (in /home/lc/learning-c/test)
    ==5385== 
    ==5385== LEAK SUMMARY:
    ==5385==    definitely lost: 48 bytes in 2 blocks
    ==5385==    indirectly lost: 21 bytes in 2 blocks
    ==5385==      possibly lost: 0 bytes in 0 blocks
    ==5385==    still reachable: 0 bytes in 0 blocks
    ==5385==         suppressed: 0 bytes in 0 blocks
    ==5385== 
    ==5385== For lists of detected and suppressed errors, rerun with: -s
    ==5385== ERROR SUMMARY: 2 errors from 2 contexts (suppressed: 0 from 0)
  ```
  
    可以看到泄露发生于`Person_create`位置，通过`Person_create`函数创建的两个Person结构体实例，每个实例泄漏了24字节。这解释了为什么definitely lost为48字节。通过`strdup`函数复制的name字符串，这导致每个实例相关联的21字节的indirectly lost。要修复这些泄漏问题，需要在适当的时候释放内存。
  
- 忘记在`Person_destroy`中释放`who->name`，并且对比两次的输出。同时，使用正确的选项来让`Valgrind`告诉你哪里错了。

  - ```shell
    $ valgrind --leak-check=full ./test 
    ==4190== Memcheck, a memory error detector
    ==4190== Copyright (C) 2002-2024, and GNU GPL'd, by Julian Seward et al.
    ==4190== Using Valgrind-3.23.0 and LibVEX; rerun with -h for copyright info
    ==4190== Command: ./test
    ==4190== 
    Joe is at memory location 0x4a9c040:
    Name: Joe Alex
            Age: 32
            Height: 64
            Weight: 140
    Frank is at memory location 0x4a9c0f0:
    Name: Frank Blank
            Age: 20
            Height: 72
            Weight: 180
    Name: Joe Alex
            Age: 52
            Height: 62
            Weight: 180
    Name: Frank Blank
            Age: 40
            Height: 72
            Weight: 200
    sizeof_struct_Person:24
    sizeof_joe:24
    sizeof_frank:24
    ==4190== 
    ==4190== HEAP SUMMARY:
    ==4190==     in use at exit: 21 bytes in 2 blocks
    ==4190==   total heap usage: 5 allocs, 3 frees, 1,093 bytes allocated
    ==4190== 
    ==4190== 9 bytes in 1 blocks are definitely lost in loss record 1 of 2
    ==4190==    at 0x484880F: malloc (vg_replace_malloc.c:446)
    ==4190==    by 0x491858E: strdup (strdup.c:42)
    ==4190==    by 0x10922A: Person_create (in /home/lc/learning-c/test)
    ==4190==    by 0x10935F: main (in /home/lc/learning-c/test)
    ==4190== 
    ==4190== 12 bytes in 1 blocks are definitely lost in loss record 2 of 2
    ==4190==    at 0x484880F: malloc (vg_replace_malloc.c:446)
    ==4190==    by 0x491858E: strdup (strdup.c:42)
    ==4190==    by 0x10922A: Person_create (in /home/lc/learning-c/test)
    ==4190==    by 0x109381: main (in /home/lc/learning-c/test)
    ==4190== 
    ==4190== LEAK SUMMARY:
    ==4190==    definitely lost: 21 bytes in 2 blocks
    ==4190==    indirectly lost: 0 bytes in 0 blocks
    ==4190==      possibly lost: 0 bytes in 0 blocks
    ==4190==    still reachable: 0 bytes in 0 blocks
    ==4190==         suppressed: 0 bytes in 0 blocks
    ==4190== 
    ==4190== For lists of detected and suppressed errors, rerun with: -s
    ==4190== ERROR SUMMARY: 2 errors from 2 contexts (suppressed: 0 from 0)
    ```

  - 解释同上，`strdup`与`free`成对出现

- 这一次，向`Person_print`传递`NULL`，并且观察`Valgrind`会输出什么。

  - 添加`-g`选项

  - ```
    $ valgrind --leak-check=full ./test 
    ==5087== Memcheck, a memory error detector
    ==5087== Copyright (C) 2002-2024, and GNU GPL'd, by Julian Seward et al.
    ==5087== Using Valgrind-3.23.0 and LibVEX; rerun with -h for copyright info
    ==5087== Command: ./test
    ==5087== 
    Joe is at memory location 0x4a9c040:
    Name: Joe Alex
            Age: 32
            Height: 64
            Weight: 140
    Frank is at memory location 0x4a9c0f0:
    Name: Frank Blank
            Age: 20
            Height: 72
            Weight: 180
    Name: Joe Alex
            Age: 52
            Height: 62
            Weight: 180
    ==5087== Invalid read of size 8
    ==5087==    at 0x1092CA: Person_print (test.c:36)
    ==5087==    by 0x10944D: main (test.c:65)
    ==5087==  Address 0x0 is not stack'd, malloc'd or (recently) free'd
    ==5087== 
    ==5087== 
    ==5087== Process terminating with default action of signal 11 (SIGSEGV): dumping core
    ==5087==  Access not within mapped region at address 0x0
    ==5087==    at 0x1092CA: Person_print (test.c:36)
    ==5087==    by 0x10944D: main (test.c:65)
    ==5087==  If you believe this happened as a result of a stack
    ==5087==  overflow in your program's main thread (unlikely but
    ==5087==  possible), you can try to increase the size of the
    ==5087==  main thread stack using the --main-stacksize= flag.
    ==5087==  The main thread stack size used in this run was 8388608.
    ==5087== 
    ==5087== HEAP SUMMARY:
    ==5087==     in use at exit: 1,093 bytes in 5 blocks
    ==5087==   total heap usage: 5 allocs, 0 frees, 1,093 bytes allocated
    ==5087== 
    ==5087== LEAK SUMMARY:
    ==5087==    definitely lost: 0 bytes in 0 blocks
    ==5087==    indirectly lost: 0 bytes in 0 blocks
    ==5087==      possibly lost: 0 bytes in 0 blocks
    ==5087==    still reachable: 1,093 bytes in 5 blocks
    ==5087==         suppressed: 0 bytes in 0 blocks
    ==5087== Reachable blocks (those to which a pointer was found) are not shown.
    ==5087== To see them, rerun with: --leak-check=full --show-leak-kinds=all
    ==5087== 
    ==5087== For lists of detected and suppressed errors, rerun with: -s
    ==5087== ERROR SUMMARY: 1 errors from 1 contexts (suppressed: 0 from 0)
    Segmentation fault (core dumped)
    ```

  - `Invalid read of size 8`存在一个无效读取错误。错误发生在第36行，报告说访问了地址`0x0`（空指针），但该地址不属于程序栈、堆，也不是最近释放的内存，这可能是因为程序中存在指针未正确初始化或指针操作错误

  - 可以检查所有指针的初始化，确保它们在使用前已经指向了有效的内存区域，可以添加对`strdup`调用的错误检查

- 你应该明白了`NULL`是个使程序崩溃的快速方法。

## 附加题

在这个练习的附加题中我想让你尝试一些有难度的东西：将这个程序改为不用指针和`malloc`的版本。这可能很困难，所以你需要研究下面这些东西：

- 如何在栈上创建结构体，就像你创建任何其它变量那样。
- 如何使用`x.y`而不是`x->y`来初始化结构体。
- 如何不使用指针来将结构体传给其它函数。

```c
#include <stdio.h>
#include <assert.h>
#include <stdlib.h>
#include <string.h>

struct Person {
    char *name;
    int age;
    int height;
    int weight;
};


void Person_print(struct Person who)
{
    printf("Name: %s\n", who.name);
    printf("\tAge: %d\n", who.age);
    printf("\tHeight: %d\n", who.height);
    printf("\tWeight: %d\n", who.weight);
}

int main(int argc, char *argv[])
{
    // make two people structures
    struct Person joe = { 
            .name = "Joe Alex", 
            .age = 32, 
            .height = 64, 
            .weight = 140 
    };  

    struct Person frank = { 
            .name = "Frank Blank", 
            .age = 20, 
            .height = 72, 
            .weight = 180 
    };  

    
    printf("Joe is at memory location %p:\n", &joe);
    Person_print(joe);
    printf("Frank is at memory location %p:\n", &frank);
    Person_print(frank);
    // make everyone age 20 years and print them again
    joe.age += 20; 
    joe.height -= 2;
    joe.weight += 40; 
    Person_print(joe);

    frank.age += 20; 
    frank.weight += 20; 
    Person_print(frank);
    
    printf("sizeof_struct_Person:%ld\n", sizeof(struct Person));
    printf("sizeof_joe:%ld\n", sizeof(joe));
    printf("sizeof_frank:%ld\n", sizeof(frank));
    
    return 0;
}
```

在原程序中动态分配内存，我们是在堆上创建新的结构体并返回指向该结构体的指针；修改后我们是在栈上创建结构体副本。函数栈空间释放后，函数内所有局部变量释放；堆空间不会因为函数执行结束而释放。创建结构体实例和创建结构体指针的区别如下：

1. 内存分配方式：结构体实例在栈上分配内存，而结构体指针通常在堆上分配内存（使用`malloc`或其他动态内存分配函数）
2. 生命周期：结构体实例的生命周期由其所在作用域控制，而结构体指针的生命周期可通过手动释放内存来调整
3. 使用方式：结构体实例可直接访问成员，而结构体指针使用`->`通过指针间接访问其指向的结构体成员