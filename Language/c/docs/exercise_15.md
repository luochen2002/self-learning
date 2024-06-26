## 示例

指针是C中的一个著名的谜之特性，实际上并不复杂，只不过它们经常以一些奇怪的方式被滥用，这样使它们变得难以使用。如果你避免这些愚蠢的方法来使用指针，你会发现它们难以置信的简单。

```c
#include <stdio.h>

int main(int argc, char *argv[])
{
    // create two arrays we care about
    int ages[] = {23, 43, 12, 89, 2};
    char *names[] = {
        "Alan", "Frank",
        "Mary", "John", "Lisa"
    };

    // safely get the size of ages
    int count = sizeof(ages) / sizeof(int);
    int i = 0;

    // first way using indexing
    for(i = 0; i < count; i++) {
        printf("%s has %d years alive.\n",
                names[i], ages[i]);
    }

    printf("---\n");

    // setup the pointers to the start of the arrays
    int *cur_age = ages;
    char **cur_name = names;

    // second way using pointers
    for(i = 0; i < count; i++) {
        printf("%s is %d years old.\n",
                *(cur_name+i), *(cur_age+i));
    }

    printf("---\n");

    // third way, pointers are just arrays
    for(i = 0; i < count; i++) {
        printf("%s is %d years old again.\n",
                cur_name[i], cur_age[i]);
    }

    printf("---\n");

    // fourth way with pointers in a stupid complex way
    for(cur_name = names, cur_age = ages;
            (cur_age - ages) < count;
            cur_name++, cur_age++)
    {
        printf("%s lived %d years so far.\n",
                *cur_name, *cur_age);
    }

    return 0;
}
```

* ex15.c:6-10
  * 创建了两个数组，`ages`储存了一些`int`数据，`names`储存了一个字符串数组。
* ex15.c:13-14
  * 为之后的`for`循环创建了一些变量。
* ex15.c:17-20
  * 你知道这只是遍历了两个数组，并且打印出每个人的年龄。它使用了`i`来对数组索引。
* ex15.c:25
  * 创建了一个指向`ages`的指针。注意`int *`创建“指向整数的指针”的指针类型的用法。它很像`char *`，意义是“指向字符的指针”，而且字符串是字符的数组。是不是很相似呢？
* ex15.c:26
  * 创建了指向`names`的指针。`char *`已经是“指向`char`的指针”了，所以它只是个字符串。你需要两个层级，因为`names`是二维的，也就是说你需要`char **`作为“指向‘指向字符的指针’的指针”。把它学会，并且自己解释它。
* ex15.c:29-32
  * 遍历`ages`和`names`，但是使用“指针加偏移`i`”。`*(cur_name+i)`和`name[i]`是一样的，你应该把它读作“‘`cur_name`指针加`i`’的值”。
* ex15.c:37-40
  * 这里展示了访问数组元素的语法和指针是相同的。
* ex15.c:45-51
  * 另一个十分愚蠢的循环和其它两个循环做着相同的事情，但是它用了各种指针算术运算来代替：
* ex15.c:45
  * 通过将`cur_name`和`cur_age`置为`names`和`age`数组的起始位置来初始化`for`循环。
* ex15.c:46
  * `for`循环的测试部分比较`cur_age`指针和`ages`起始位置的距离，为什么可以这样写呢？

* ex15.c:47
  * `for`循环的增加部分增加了`cur_name`和`cur_age`的值，这样它们可以只想`names`和`ages`的下一个元素。

* ex15.c:49-50
  * `cur_name`和`cur_age`的值现在指向了相应数组中的一个元素，我们我可以通过`*cur_name`和`*cur_age`来打印它们，这里的意思是“`cur_name`和`cur_age`指向的值”。

## 你会看到什么

在运行这个程序之后，尝试根据打印出的每一行追溯到代码中产生它们的那一行。在必要情况下，修改`printf`调用来确认你得到了正确的行号：

```shell
$ make ex15
cc -Wall -g    ex15.c   -o ex15
$ ./ex15
Alan has 23 years alive.
Frank has 43 years alive.
Mary has 12 years alive.
John has 89 years alive.
Lisa has 2 years alive.
---
Alan is 23 years old.
Frank is 43 years old.
Mary is 12 years old.
John is 89 years old.
Lisa is 2 years old.
---
Alan is 23 years old again.
Frank is 43 years old again.
Mary is 12 years old again.
John is 89 years old again.
Lisa is 2 years old again.
---
Alan lived 23 years so far.
Frank lived 43 years so far.
Mary lived 12 years so far.
John lived 89 years so far.
Lisa lived 2 years so far.
```

## 解释指针

当写下一些类似`ages[i]`的东西时，实际上在用`i`中的数字来索引`ages`。如果`i`的值为0，那么就等同于写下`ages[0]`。我们把`i`叫做下标，因为它是`ages`中的一个位置。它也能称为地址，这是“想要`ages`位于地址`i`处的整数”中的说法。

如果`i`是个下标，那么`ages`又是什么？对C来说`ages`是在计算机中那些整数的起始位置。当然它也是个地址，C编译器会把任何你键入`ages`的地方替换为数组中第一个整数的地址。另一个理解它的办法就是把`ages`当作“数组内部第一个整数的地址”，但是它是整个计算机中的地址，而不是像`i`一样的`ages`中的地址。`ages`数组的名字在计算机中实际上是个地址。

这就产生了一种特定的实现：C把你的计算机看成一个庞大的字节数组。显然这样不会有什么用处，于是C就在它的基础上构建出类型和大小的概念。你已经在前面的练习中看到了它是如何工作的，但现在你可以开始了解C对你的数组做了下面一些事情：

- 在你的计算机中开辟一块内存。
- 将`ages`这个名字“指向”它的起始位置。
- 通过选取`ages`作为基址，并且获取位置为`i`的元素，来对内存块进行索引。
- 将`ages+i`处的元素转换成大小正确的有效的`int`，这样就返回了你想要的结果：下标`i`处的`int`。

如果你可以选取`ages`作为基址，之后加上比如`i`的另一个地址，你是否就能随时构造出指向这一地址的指针呢？是的，这种东西就叫做指针。这也是`cur_age`和`cur_name`所做的事情，它们是指向计算机中这一位置的变量，`ages`和`names`就处于这一位置。之后，示例程序移动它们，或者做了一些算数运算，来从内存中获取值。在其中一个实例中，只是简单地将`cur_age`加上`i`，这样等同于`array[i]`。在最后一个`for`循环中，这两个指针在没有`i`辅助的情况下自己移动，被当做数组基址和整数偏移合并到一起的组合。

指针仅仅是指向计算机中的某个地址，并带有类型限定符，所以你可以通过它得到正确大小的数据。它类似于将`ages`和`i`组合为一个数据类型的东西。C了解指针指向什么地方，所指向的数据类型，这些类型的大小，以及如何为你获取数据。你可以像`i`一样增加它们，减少它们，对他们做加减运算。然而它们也像是`ages`，你可以通过它获取值，放入新的值，或执行全部的数组操作。

指针的用途就是让你手动对内存块进行索引，一些情况下数组并不能做到。绝大多数情况中，你可能打算使用数组，但是一些处理原始内存块的情况，是指针的用武之地。指针向你提供了原始的、直接的内存块访问途径，让你能够处理它们。

在这一阶段需要掌握的最后一件事，就是你可以对数组和指针操作混用它们绝大多数的语法。你可以对一个指针使用数组的语法来访问指向的东西，也可以对数组的名字做指针的算数运算。

## 实用的指针用法

你可以用指针做下面四个最基本的操作：

- 向OS申请一块内存，并且用指针处理它。这包括字符串，和一些你从来没见过的东西，比如结构体。
- 通过指针向函数传递大块的内存（比如很大的结构体），这样不必把全部数据都传递进去。
- 获取函数的地址用于动态调用。
- 对一块内存做复杂的搜索，比如，转换网络套接字中的字节，或者解析文件。

对于你看到的其它所有情况，实际上应当使用数组。在早期，由于编译器不擅长优化数组，人们使用指针来加速它们的程序。然而，现在访问数组和指针的语法都会翻译成相同的机器码，并且表现一致。由此，你应该每次尽可能使用数组，并且按需将指针用作提升性能的手段。

## 指针词库

现在我打算向你提供一个词库，用于读写指针。当你遇到复杂的指针语句时，试着参考它并且逐字拆分语句（或者不要使用这个语句，因为有可能并不好）：

```
type *ptr
```

`type`类型的指针，名为`ptr`。

```
*ptr
```

`ptr`所指向位置的值。

```
*(ptr + i)
```

（`ptr`所指向位置加上`i`）的值。

> 以字节为单位的话，应该是`ptr`所指向的位置再加上`sizeof(type) * i`。

```
&thing
```

`thing`的地址。

```
type *ptr = &thing
```

名为`ptr`，`type`类型的指针，值设置为`thing`的地址。

```
ptr++
```

自增`ptr`指向的位置。

我们将会使用这份简单的词库来拆解所有的指针用例。

## 指针并不是数组

无论怎么样，你都不应该把指针和数组混为一谈。它们并不是相同的东西，即使C让你以一些相同的方法来使用它们。例如，如果你访问上面代码中的`sizeof(cur_age)`，你会得到指针的大小，而不是它指向数组的大小。如果你想得到整个数组的大小，你应该使用数组的名称`age`，就像第13行那样。

## 如何使它崩溃

你可以通过将指针指向错误的位置来使程序崩溃：

- 试着将`cur_age`指向`names`。可以需要C风格转换来强制执行，试着查阅相关资料把它弄明白。

  - `warning: initialization of ‘int *’ from incompatible pointer type ‘char * (*)[5]’`

  - `int *cur_age = (int *)&names;`

  - ```shell
    Alan is 729247748 years old.
    Frank is 25923 years old.
    Mary is 729247753 years old.
    John is 25923 years old.
    Lisa is 729247759 years old.
    ```

  - 输出结果中的数字是不确定的，它们只是未定义的内存值。因为你将字符指针数组解释为整数指针数组，因此在打印时会将这些未定义的内存值解释为整数。

- 在最后的`for`循环中，用一些古怪的方式使计算发生错误。

- 试着重写循环，让它们从数组的最后一个元素开始遍历到首个元素。

  - ```c
    for(i = 0; i < count; i++) {
    	printf("%s is %d years old again.\n",
               cur_name[count-i-1], cur_age[count-i-1]);
    }
    ```

## 附加题

- 使用访问指针的方式重写所有使用数组的地方。

  - ```c
    int main(int argc, char *argv[])
    {
        int ages[] = {23, 43, 12, 89, 2}; 
        char *names[] = { 
            "Alan", "Frank",
            "Mary", "John", "Lisa"
        };  
    
        int count = sizeof(ages) / sizeof(int);
        int i = 0;
    
        for(i = 0; i < count; i++) {
           printf("%s has %d years alive.\n", *(names + i), *(ages + i));
        }   
    
        printf("---\n");
    
        int *cur_age = ages;
        char **cur_name = names;
    
        for(i = 0; i < count; i++) {
            printf("%s is %d years old.\n",
                    *(cur_name+i), *(cur_age+i));
        }   
    
        printf("---\n");
    
        for(i = 0; i < count; i++) {
            printf("%s is %d years old again.\n",
                    *(cur_name + i), *(cur_age + i));
        }   
    
        printf("---\n");
    
        for(cur_name = names, cur_age = ages;
                (cur_age - ages) < count;
                cur_name++, cur_age++)
        {   
            printf("%s lived %d years so far.\n",
                    *cur_name, *cur_age);
        }   
    
        return 0;
    }
    ```

- 使用访问数组的方式重写所有使用指针的地方。

  - ```c
    int main(int argc, char *argv[])
    {
        int ages[] = {23, 43, 12, 89, 2};
        char *names[] = {
            "Alan", "Frank",
            "Mary", "John", "Lisa"
        };
    
        int count = sizeof(ages) / sizeof(int);
        int i = 0;
    
        for(i = 0; i < count; i++) {
           printf("%s has %d years alive.\n", names[i], ages[i]);
        }
    
        printf("---\n");
    
        int *cur_age = ages;
        char **cur_name = names;
    
        for(i = 0; i < count; i++) {
            printf("%s is %d years old.\n",
                    cur_name[i], cur_age[i]);
        }
    
        printf("---\n");
    
        for(i = 0; i < count; i++) {
            printf("%s is %d years old again.\n",
                    cur_name[i], cur_age[i]);
        }
    
        printf("---\n");
    
        return 0;
    }
    ```

- 在其它程序中使用指针来代替数组访问。

- 使用指针来处理命令行参数，就像处理`names`那样。

- 将获取值和获取地址组合到一起。

- 在程序末尾添加一个`for`循环，打印出这些指针所指向的地址。你需要在`printf`中使用`%p`。

  - ```c
    int main(int argc, char *argv[]) {
        // create two arrays we care about
        int ages[] = {23, 43, 12, 89, 2};
        char *names[] = {
            "Alan", "Frank",
            "Mary", "John", "Lisa"
        };
    
        // safely get the size of ages
        int count = sizeof(ages) / sizeof(int);
        int i = 0;
    
        // first way using indexing
        for (i = 0; i < count; i++) {
            printf("%s has %d years alive. Address: %p\n", names[i], ages[i], &ages[i]);
        }
        printf("---\n");
    
        // second way using combined value and address access
        for (i = 0; i < count; i++) {
            printf("%s is %d years old. Address: %p\n", *(names + i), *(ages + i), &names[i]);
        }
        printf("---\n");
    
        // third way, combined value and address access
        for (i = 0; i < count; i++) {
            printf("%s is %d years old again. Address: %p\n", names[i], ages[i], (names + i));
        }
        printf("---\n");
    
        // fourth way with combined value and address access
        for (i = 0; i < count; i++) {
            printf("%s lived %d years so far. Address: %p\n", *(names + i), *(ages + i), (ages + i));
        }
    
        return 0;
    }
    ```

- 对于每一种打印数组的方法，使用函数来重写程序。试着向函数传递指针来处理数据。记住你可以声明接受指针的函数，但是可以像数组那样用它。

  - ```c
    #include <stdio.h>
    void print(char** names,int* ages);
    
    void print(char** names,int* ages){
    
        printf("%s has %d years alive.\n", *names, *ages);
    };
    
    
    int main(int argc, char *argv[])
    {
        // create two arrays we care about
        int ages[] = {23, 43, 12, 89, 2};
        char *names[] = {
            "Alan", "Frank",
            "Mary", "John", "Lisa"
        };
    
        // safely get the size of ages
        int count = sizeof(ages) / sizeof(int);
        int i = 0;
    
        // first way using indexing
        for(i = 0; i < count; i++) {
            // printf("%s has %d years alive.\n",
            //      names[i], ages[i]);
            print(&names[i],&ages[i]);
        }
        printf("---\n");
    
        // setup the pointers to the start of the arrays
        int *cur_age = ages;
    
        //    int *cur_age =(int *)&names;
    
        char **cur_name = names;
    
        // second way using pointers
        for(i = 0; i < count; i++) {
            // printf("%s is %d years old.\n",
            //      *(cur_name+i), *(cur_age+i));
            print((cur_name+i),(cur_age+i));
        }
        printf("---\n");
    
        // third way, pointers are just arrays
        for(i = 0; i < count; i++) {
            // printf("%s is %d years old again.\n",
            //      cur_name[count-i-1], cur_age[count-i-1]);
            print(&cur_name[count-i-1],&cur_age[count-i-1]);
        }
        printf("---\n");
    
    
        // fourth way with pointers in a stupid complex way
        for(cur_name = names, cur_age = ages;
                (cur_age - ages) < count;
                cur_name++, cur_age++)
        {
            // printf("%s lived %d years so far.\n",
            //      *cur_name, *cur_age);
            print(cur_name,cur_age);
        }
        return 0;
    }
    ```

- 将`for`循环改为`while`循环，并且观察对于每种指针用法哪种循环更方便。

  - ```c
    cur_name = names;
    cur_age = ages;
    while((cur_age - ages) < count){
        print(cur_name,cur_age);    
        cur_name++;
        cur_age++;
    }
    ```