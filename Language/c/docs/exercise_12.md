## 示例

`if`语句是每个编程语言中共有的特性，包括C语言。下面是一段代码，使用了`if`语句来确保只传入了一个或两个命令行参数：

```c
#include <stdio.h>

int main(int argc, char *argv[])
{
    int i = 0;

    if(argc == 1) {
        printf("You only have one argument. You suck.\n");
    } else if(argc > 1 && argc < 4) {
        printf("Here's your arguments:\n");

        for(i = 0; i < argc; i++) {
            printf("%s ", argv[i]);
        }
        printf("\n");
    } else {
        printf("You have too many arguments. You suck.\n");
    }

    return 0;
}
```

`if`语句的格式为：

```c
if(TEST) {
    CODE;
} else if(TEST) {
    CODE;
} else {
    CODE;
}
```

下面是其它语言和C的差异：

- 像之前提到的那样，`TEST`表达式值为0时为`false`，其它情况为`true`。
- 你需要在`TEST`周围写上圆括号，其它语言可能不用。
- （只有单条语句时）你并不需要使用花括号`{}`来闭合代码，但是这是一种非常不好的格式，不要这么写。花括号让一个分支的代码的开始和结束变得清晰。如果你不把代码写在里面会出现错误。

除了上面那些，就和其它语言一样了。`else if`或者`else`的部分并不必须出现。

## 你会看到什么

```shell
$ make ex12
cc -Wall -g    ex12.c   -o ex12
$ ./ex12
You only have one argument. You suck.
$ ./ex12 one
Here's your arguments:
./ex12 one
$ ./ex12 one two
Here's your arguments:
./ex12 one two
$ ./ex12 one two three
You have too many arguments. You suck.
```

## 如何使它崩溃

使这段代码崩溃并不容易，因为它太简单了。尝试把`if`语句的测试表达式搞乱：

- 移除`else`部分，使它不能处理边界情况。
- 将`&&`改为`||`，于是你会把“与”操作变成“或”操作，并且看看会发生什么。

## 附加题

- 简短地介绍了`&&`，执行“与”操作。若试图了解“布尔运算符”，请自行STFW。
- 为这个程序编写更多的测试用例，看看能写出什么。
- 回到练习10和11，使用`if`语句使循环提前退出。你需要`break`语句来实现它，搜RTFM。
- 第一个判断所输出的话真的正确吗？由于你的“第一个参数”不是用户输入的第一个参数，把它改正。