## 示例

到现在为止，只使用了作为`stdio.h`头文件一部分的函数。在这个练习中你将要编写并使用自己的函数

```c
#include <stdio.h>
#include <ctype.h>

// forward declarations
int can_print_it(char ch);
void print_letters(char arg[]);

void print_arguments(int argc, char *argv[])
{
    int i = 0;

    for(i = 0; i < argc; i++) {
        print_letters(argv[i]);
    }
}

void print_letters(char arg[])
{
    int i = 0;

    for(i = 0; arg[i] != '\0'; i++) {
        char ch = arg[i];

        if(can_print_it(ch)) {
            printf("'%c' == %d ", ch, ch);
        }
    }

    printf("\n");
}

int can_print_it(char ch)
{
    return isalpha(ch) || isblank(ch);
}


int main(int argc, char *argv[])
{
    print_arguments(argc, argv);
    return 0;
}
```

在这个例子中你创建了函数来打印任何属于“字母”和“空白”的字符。下面是一个分解：

* ex14.c:2
  * 包含了新的头文件，所以你可以访问`isalpha`和`isblank`。

* ex14.c:5-6
  * 告诉C语言你稍后会在你的程序中使用一些函数，它们实际上并没有被定义。这叫做“前向声明”，它解决了要想使用函数先要定义的鸡和蛋的问题。
* ex14.c:8-15
  * 定义`print_arguments`，它知道如何打印通常由`main`函数获得的相同字符串数组。
* ex14.c:17-30
  * 定义了`can_print_it`，它只是简单地将`isalpha(ch) || isblank(ch)`的真值（0或1）返回给它的调用者`print_letters`。
* ex14.c:38-42
  * 最后`main`函数简单地调用`print_arguments`，来启动整个函数链。

我不应该描述每个函数里都有什么，因为这些都是你之前遇到过的东西。你应该看到的是，我只是像你定义`main`函数一样来定义其它函数。唯一的不同就是如果你打算使用当前文件中没有碰到过的函数，你应该事先告诉C。这就是代码顶部的“前向声明”的作用。

## 你会看到什么

```c
$ make ex14
cc -Wall -g    ex14.c   -o ex14

$ ./ex14
'e' == 101 'x' == 120

$ ./ex14 hi this is cool
'e' == 101 'x' == 120
'h' == 104 'i' == 105
't' == 116 'h' == 104 'i' == 105 's' == 115
'i' == 105 's' == 115
'c' == 99 'o' == 111 'o' == 111 'l' == 108

$ ./ex14 "I go 3 spaces"
'e' == 101 'x' == 120
'I' == 73 ' ' == 32 'g' == 103 'o' == 111 ' ' == 32 ' ' == 32 's' == 115 'p' == 112 'a' == 97 'c' == 99 'e' == 101 's' == 115
```

`isalpha`和`isblank`做了检查提供的字符是否是字母或者空白字符的所有工作。当我最后一次运行时，它打印出除了`'3'`之外的任何东西，因为它是一个数字。

> ```c
> int isblank(int ch);
> // 其中，ch参数为要进行检查的字符，可以是任意的int型数据，返回值则为整型，1代表该字符是空格或制表符，0则代表不是。
> ```

## 如何使它崩溃

下面是使它崩溃的两种不同的方法：

- 通过移除前向声明来把编译器搞晕。它会报告`can_print_it` 和 `print_letters`的错误。
  - `ex14.c:13:9: warning: implicit declaration of function ‘print_letters’`
  - `ex14.c:17:6: warning: conflicting types for ‘print_letters’; have ‘void(char *)’`
  - `ex14.c:24:12: warning: implicit declaration of function ‘can_print_it’`
  - `ex14.c:32:5: error: conflicting types for ‘can_print_it’; have ‘int(char)’`
- 当你在`main`中调用`print_arguments`时，试着使`argc`加1，于是它会越过`argv`数组的最后一个元素。
  - `Segmentation fault`
  - `still reachable`

## 附加题

- 重新编写这些函数，使它们的数量减少。比如，你真的需要`can_print_it`吗？

  - `if(isalpha(ch) || isblank(ch))`

- 使用`strlen`函数，让`print_arguments`知道每个字符串参数都有多长，之后将长度传入`print_letters`。然后重写`print_letters`，让它只处理固定的长度，不按照`'\0'`终止符。你需要`#include <string.h>`来实现它。

  - ```c
    void print_arguments(int argc, char *argv[]){
        int i = 0;
        int len = 0;
        for(i = 0; i < argc; i++){
            len = strlen(argv[i]);
            print_letters(argv[i]);
        }
    }
    ```

- 使用`man`来查询`isalpha`和`isblank`的信息。使用其它相似的函数来只打印出数字或者其它字符。

  - ```c
    isalpha()
    	checks for an alphabetic character; in the standard  "C"
    	locale,  it is equivalent to (isupper(c) || islower(c)).
    	In some locales, there may be additional characters  for
    	which isalpha() is true—letters which are neither upper‐
    	case nor lowercase.
    ```

  - ```c
    isblank()
    	checks for a blank character; that is, a space or a tab.
    ```

- 上网浏览不同的人喜欢什么样的函数格式。永远不要使用“K&R”语法，因为它过时了，而且容易使人混乱，但是当你碰到一些人使用这种格式时，要理解代码做了什么。