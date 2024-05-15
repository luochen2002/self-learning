## 示例

给出一个简单的程序

```c
int main(int argc, char *argv[])
{
    puts("Hello world.");
    
    return 0;
}
```

把它写进`ex1.c`并在命令行输入：

```shell
$ make ex1
cc     ex1.c   -o ex1
```

这样你就在该目录下生成了一个名为`ex1`的可执行文件。

## 你会看到什么

现在可以运行程序并显示输出

```shell
./ex1
Hello world.
```

## 如何使它崩溃

一味得到正确的结果并不能让你学到很多，你应该尝试接触错误，了解错误过程并解决和避免错误。

对于该程序，打开所有编译警告重新构建它：

```shell
$ rm ex1
$ CFLAGS="-Wall" make ex1
cc Wall    ex1.c   -o ex1
ex1.c: In function ‘main’:
ex1.c:3:9: warning: implicit declaration of function ‘puts’ [-Wimplicit-function-declaration]
...
$ ./ex1
Hello world.
```

> `...`即省略信息

现在可以看到一个`warring`，显示`puts`函数是隐式声明的。最后仍能生成`ex1`可执行文件。C语言的编译器很智能，它能够理解你想要什么。但是如果可以的话，你应该去除所有编译器警告。把下面一行添加到`ex1.c`文件的最上面，之后重新编译来去除它：

```c
#include<stdio.h>
```

现在重新执行make命令后，所有warring都消失了

## 附加题

* 在你的文本编辑器中打开`ex1`文件，随机修改或删除一部分，之后运行它看看发生了什么。
  * 打开文件后发现了许多乱码，但是你看不懂，不过没关系，你看见文本里有很多`^@`，你试着删除了几个，发现`^`和`@`居然是一起删除的，然后试着看看`^A`、`^B`，这些字符都是一起删除的
  * 删除一个`^@`再执行文件试试
  * 搜索`puts`发现文本中有两个，删除第一个再执行文件试试，删除第二个再执行文件试试
* 再多打印5行文本或者其它比`"Hello world."`更复杂的东西。
  * 你想着括号括起来算作一个整体，然后使用回车打印五行文本，执行文件看看
* 执行`man 3 puts`来阅读这个函数和其它函数的文档。
  * output of characters and strings



