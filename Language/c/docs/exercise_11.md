## 示例

在C语言中，实际上没有真正的“布尔”类型，而是用一个整数来代替，0代表`false`，其它值代表`true`。上一个练习中表达式`i < argc`实际上值为1或者0，并不像Python是显式的`Ture`或者`False`。

```c
#include <stdio.h>

int main(int argc, char *argv[])
{
    // go through each string in argv

    int i = 0;
    while(i < argc) {
        printf("arg %d: %s\n", i, argv[i]);
        i++;
    }

    // let's make our own array of strings
    char *states[] = {
        "California", "Oregon",
        "Washington", "Texas"
    };

    int num_states = 4;
    i = 0;  // watch for this
    while(i < num_states) {
        printf("state %d: %s\n", i, states[i]);
        i++;
    }

    return 0;
}
```

你可以看到`while`循环的语法更加简单：

```c
while(TEST) {
    CODE;
}
```

只要`TEST`为`true`（非0），就会一直运行`CODE`中的代码。这意味着如果要达到和`for`循环同样的效果，我们需要自己写初始化语句，以及自己来使`i`增加。

## 你会看到什么

输出基本相同

```shell
$ make ex11
cc -Wall -g    ex11.c   -o ex11
$ ./ex11
arg 0: ./ex11
state 0: California
state 1: Oregon
state 2: Washington
state 3: Texas
$
$ ./ex11 test it
arg 0: ./ex11
arg 1: test
arg 2: it
state 0: California
state 1: Oregon
state 2: Washington
state 3: Texas
```

## 如何使它崩溃

在你自己的代码中，应优先选择`for`循环而不是`while`循环，因为`for`循环不容易崩溃。下面是几点普遍的原因：

- 忘记初始化`int i`，使循环发生错误。
- 忘记初始化第二个循环的`i`，于是`i`还保留着第一个循环结束时的值。你的第二个循环可能执行也可能不会执行。
- 忘记在最后执行`i++`自增，你会得到一个“死循环”。

## 附加题

- 让这些循环倒序执行，通过使用`i--`从`argc`开始递减直到0。你可能需要做一些算数操作让数组的下标正常工作。

  - ```c
    int i = argc; 
    while(i > 0) { 
    	printf("arg %d: %s\n", argc - i, argv[argc - i]); 
        i--; 
    }
    ```

  - ```shell
    $ ./test abc def qwe asd
    ...
    arg 0: ./test
    arg 1: abc
    arg 2: def
    arg 3: qwe
    arg 4: asd
    ```

- 使用`while`循环将`argv`中的值复制到`states`。

  - ```c
    i = 0;
    while(i < argc) {
    	states[i] = argv[i];
    	printf("state %d: %s\n", i, states[i]);
    	i++;
    }
    ```

- 让这个复制循环不会执行失败，即使`argv`之中有很多元素也不会全部放进`states`。

  - 可以使用`num_states`作为循环条件
  - 如果没有`argc`，要如何计算得到？
  - `for(int i = 0; argv[i] != NULL; i++) {count++;}`

- 研究你是否真正复制了这些字符串。答案可能会让你感到意外和困惑。

  - 对比变量的内存地址，发现并不相同

