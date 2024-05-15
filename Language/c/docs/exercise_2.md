## 使用Make

使用make的第一阶段就是用它已知的方式来构建程序。Make预置了一些知识，来从其它文件构建多种文件。上一个练习中，你已经使用像下面的命令来这样做了：

```shell
$ make ex1
# or this one too
$ CFLAGS="-Wall" make ex1
```

第一个命令中你告诉make，“我想创建名为ex1的文件”。于是Make执行下面的动作：

- 文件`ex1`存在吗？
- 没有。好的，有没有其他文件以`ex1`开头？
- 有，叫做`ex1.c`。我知道如何构建`.c`文件吗？
- 是的，我会运行命令`cc ex1.c -o ex1`来构建它。
- 我将使用`cc`从`ex1.c`文件来为你构建`ex1`。

上面列出的第二条命令是一种向make命令传递“修改器”的途径。如果你不熟悉Unix shell如何工作，你可以创建这些“环境变量”，它们会在程序运行时生效。有时你会用一条类似于`export CFLAGS="-Wall"`的命令来执行相同的事情，取决于你所用的shell。然而你可以仅仅把它们放到你想执行的命令前面，于是环境变量只会在程序运行时有效。

在这个例子中执行了`CFLAGS="-Wall" make ex1`，所以它会给make通常使用的`cc`命令添加`-Wall`选项。这行命令告诉`cc`编译器要报告所有的警告（然而实际上不可能报告所有警告）。

实际上你可以深入探索使用make的上述方法，但是先让我们来看看`Makefile`，以便让你对make了解得更多一点。首先，创建文件并写入以下内容：

```shell
CFLAGS=-Wall -g

clean:
    rm -f ex1
```

将文件在你的当前文件夹上保存为`Makefile`。Make会自动假设当前文件夹中有一个叫做`Makefile`的文件，并且会执行它。此外，一定要注意：确保你只输入了 TAB 字符，而不是空格和 TAB 的混合。

`Makefile`向你展示了make的一些新功能。首先我们在文件中设置`CFLAGS`，所以之后就不用再设置了。并且，我们添加了`-g`标识来获取调试信息。接着我们写了一个叫做`clean`的部分，它告诉make如何清理我们的小项目。

确保它和你的`ex2.c`文件在相同的目录中，之后运行以下命令：

```shell
$ make clean
$ make ex2
```

> ex2.c文件是ex1.c没有包含`#include<stdio.h>`

## 你会看到什么

如果正常工作，应该会显示以下内容

```shell
+$ make clean
rm -f ex2
$ make ex2
cc -Wall -g    ex1.c   -o ex1
ex2.c: In function 'main':
ex2.c:3: warning: implicit declaration of function 'puts'
...
```

执行`make clean`，它告诉make执行`clean`目标。再去看一眼Makefile，之后你会看到在它的下面，缩进并且输入了一些想要make为我运行的shell命令。可以在此处输入任意多的命令，所以它是一个非常棒的自动化工具。

> 如果修改了`ex2.c`，添加了`#include<stdio>`，输出中的关于`puts`的警告就会消失（这其实应该算作一个错误）。

## 如何使它崩溃

让我们以一种特定的方式来破坏make文件，以便可以看到发生了什么。找到`rm -f ex1`的那一行并去掉缩进（让它左移），之后你可以看到发生了什么。再次运行`make clean`，你就会得到下面的信息：

```shell
$ make clean
Makefile:4: *** missing separator.  Stop.
```

## 附加题

- 创建目标`all:ex1`，可以以单个命令`make`构建`ex1`。
  - 直接在Makefile文件中添加`all: ex1`
  - 同理可以创建`all: ex1 ex2`，这样在运行make命令时，它将构建all目标，并能使用`make clean all`
- 阅读`man make`来了解关于如何执行它的更多信息。
- 阅读`man cc`来了解关于`-Wall`和`-g`行为的更多信息。
- 在互联网上搜索Makefile文件，看看你是否能改进你的文件。
- 在另一个C语言项目中找到`Makefile`文件，并且尝试理解它做了什么。