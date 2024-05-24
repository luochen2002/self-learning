# 数据结构概述

## 1. 数据结构的概念

### 数据与数据结构

* 数据（data）：是信息的载体，是描述客观事物的数、字符，以及所有能输入到计算机中并被计算机程序识别和处理的符号的集合。如学生表。
* 数据元素（data element）：数据的基本单位，由若干数据项组成。如个人纪录。
* 数据项（data item）：不可分割的最小单位，可分为初等项和组合项。如学号，姓名。
* 结构：数据元素之间的关系。

数据结构的定义：数据结构由某一数据元素的集合和该集合中数据元素之间的关系组成。记为：
$$
Date\_Structure=\{D, R\}
$$
其中，$D$是某一数据元素的集合，$R$是该集合中所有数据元素之间的关系的有限集合。

### 分类

- 线性结构（linear structure）
  - 一般线性表
  - 受限线性表：栈、队列、串
  - 线性表推广：数组、广义表

* 非线性结构（nonlinear structure）
  * 集合结构
  * 树形结构
  * 图状结构

### 存储结构/物理结构

值数据结构在计算机中的表示（映像）

* 顺序存储方法（sequential storage）
  * 逻辑上相邻的元素存储在物理位置上也相邻的存储单元中
* 链接存储方法（linked storage）
  * 借助指示元素存储地址的指针来表示元素之间的逻辑关系
* 索引存储方法（indexed storage）
  * 存储元素信息的同时，建立附加的索引表。类似按姓名排序的手机通讯录
* 散列存储方法（hashing storage）
  * 根据元素的关键字直接计算出该元素的存储地址（哈希存储）

## 2. 数据结构的抽象形式

### 数据类型

类型是一组值的集合，可分为原子类型和结构类型。

数据类型是指一种类型，以及定义于这个值集合上的一组操作的总称。

数据类型的传统实现方法：	

* 基于数组的顺序表示
* 基于链表的链接表示

抽象：抽取反应问题本质的东西，忽略非本质的细节

抽象数据类型：指由用户定义，用以表示应用问题的数据模型，是将数据结构作为一个软件构件的实现。其特征是使用与实现分离，实行封装和信息隐蔽。

## 3. 作为 ADT 的 C++ 类

### 面向对象的概念

什么是面向对象（Object-oriented）？
$$
面向对象=对象+类+继承+消息通信
$$
对象（Objects）：对象是系统中描述客观事物的一个实体，它是构成系统的一个基本单位，由一组属性和对这组属性进行操作的一组服务组成。属性值确定了对象的状态，可以通过服务改变对象的属性值。

类（Class）：类是对象的抽象，是一组具有相同数据结构和相同操作的对象的集合。它描述了属于该对象类型的所有对象的性质。对象是该类的一个实例。

> 对象和类都是由属性、操作和方法构成的
>
> 属性是类中对象所具有的数据值，它依附于类和对象，并且以某种方式描述类或对象。对象所能执行的操作称为方法，也就是类中所定义的服务。方法描述了对象执行操作的算法、响应消息的操作

继承（Inheritance）：继承用于描述类之间的共同性质，它减少了相似类的重复说明。继承是指子类可以自动拥有父类的全部属性和服务。

> 继承具有传递性，如果类 C 继承类 B ，类 B 继承类 A，则类 C 继承类 A。因此，一个类实际继承了它上层的全部基类的所有描述

消息通信（Communication）：消息是对象发出的服务请求，对象间只能通过发送消息进行联系，一个对象向另一个对象发出消息请求某项服务，接收消息的对象响应该消息，激发所要求的服务操作，并将操作结果返回给请求服务的对象。

### C++ 中的类

类成员由三级存取：

* 公共（public）
* 私有（private）
* 保护（protected）

> 对于在 public 域中声明的数据成员和函数成员（也叫成员函数），程序中其他类的对象或操作都能请求该类对象执行它们，因此这些数据成员和成员函数构成类的接口部分
>
> 在 private 和 protected 域中声明的数据成员和成员函数构成类的私有部分，只能由该类的对象和成员函数以及被声明为友元（friend）的函数或类的对象访问。
>
> 在 protected 域中声明的数据成员和成员函数还允许该类的派生类访问；在 private 域中则不允许

在`struct`中，默认访问级别是 public。若在 struct 内部自始至终缺省访问级别，则所有成员都在公共接口中

在`union`中，默认访问级别是 public。

在`class`中，默认访问级别是 private。

### C++中的对象

**1. 建立类的对象**

```c++
// 建立Point类实例
Point p(6, 3);					// 自动地
Point q;						// 自动地
static Point s(3, 4);			// 静态地
Point * t = new Point(1, 1);	// 通过动态分配
```

对象 p、q 和 s 都是 Point 类的对象，而 t 是对象指针。

**2. 构造函数**

当遇到对象实例化时，将隐式地调用一个构造函数（constructor），该函数属于与它同名的类。

```c++
// 当声明Point p(6, 3)建立Point类的对象p时，调用了构造函数Point(int, int)
Point::Point(int a, int b) {x = a; y = b;}
// 或
Point::Point(int a, int b):x(a),y(b){} 
```

构造函数可以定义默认值。

```c++
Point::Point(int a = 0, int b = 0):x(a), y(b){}

// 当定义实例时给定初始值则以其初始化
Point p(6, 3)	// 用 x = a = 6, y = b = 3
// 当定义实例时未给定初始值则以默认值初始化
Point q			// x = a = 0, y = b = 0
```

**3. 析构函数**

若要撤销对象，需要隐式调用析构函数（destructor），属于名称相同的类，但前面要加`~`，如`~Point()`。

一个类可以定义几个不同的构造函数，但只能定义一个析构函数。当控制要退出自动变量的作用域时，或当通过`delete`命令释放一个动态分配的变量时，就要调用析构函数。当`main`函数执行结束时，将释放静态声明的变量。

析构函数用于在删除一个类的对象时进行清除操作。

### C++ 的 I/O

需要`#include`预处理指令包括一个头文件`<iostream.h>`。其可支持 C++ 的流（stream）操作。在 C++中有两个预定义类`istream`和`ostream`定义了输入/输出流。

**1.键盘屏幕 I/O**

在 C 中有用于定向到键盘输入设备、屏幕输出设备和错误文件的命令`stdin`、`stdout`、`stderr`。

在 C++ 中用`cin`、`cout`、`cerr`来定义键盘输入类、屏幕输出类和错误信息输出类。操作符`<<`用于写出类`ostream`的对象，操作符`>>`用于读入类`istream`的对象

**2.文件I/O**

1. 在程序开头需要用预处理指令`#include`包含头文件`<fstream.h>`，它定义了类`ifstream`、`ofstream`和`fstream`
2. 要创建一个输入流，必须声明它为`ifstream`类的实例
3. 要创建一个输出流，必须声明它为`ofstream`类的实例
4. 执行输入和输出操作的流必须声明它为`fstream`类的实例

```c++
#include<fstream.h>
#include<iostream.h>
#include<stdlib.h>

int main(){
	ifstream inFile;								// inFile为输入流对象
	ofstream outFile;								// outFile为输出流对象
	out File.open("my.dat", ios::out);				// 建立输出文件"my.dat"
	char univ[] = "Tsinghua", name[10];
	int course = 2401, number;
	outFile << univ << endl;						// 输出到"my.dat"
	outFile << course << endl;
	inFile.open("my.dat", ios::in|ios::nocreate);	// 打开输入文件"my.dat"
	if(!inFile){
		cerr << "不能打开 my.dat" << endl;
		exit(1);
	}
	char c;
	inFile >> name >> c >> number;
	outFile << "name: " << name << endl;
	outFile << "number: " << number << endl;
}
```

`ifstream`类、`ofstream`类和`fstream`类都是从`istream`类和`ostream`类派生出来的，而类`istream`和`ostream`又是从类`ios`派生出来的，因此这些类都可以使用类`ios`的所有运算。

在调用打开文件函数`open()`时，函数参数表包括实际文件名和数据流动的方向，函数返回文件的开始地址。系统在存储文件时，在其末尾添加有文件结束标记。如果文件未被打开，则`outFile = 0`；如果成功打开，则它将代替`cout`，将输出引导到文件"my.dat"中。

在文件打开操作中，指定文件模式有以下几种：

* `ios::app`：把所有对文件的输出添加在文件末尾。它只用于输出文件
* `ios::binary`：文件以二进制方式打开。此项缺省时文件以文本方式打开
* `ios::nocreate`：若文件不存在则导致打开操作失败
* `ios::out`：表明该文件用于输出。此项可缺省
* `ios::in`：表明该文件用于输入。此项可缺省

### C++ 中的函数

**1. 概述**

C++ 中有两种函数：常规函数和成员函数，函数定义包括4个部分：函数名、形式参数表、返回类型和函数体。通过函数名调用该函数，调用时把实际参数传送给形式参数表作为数据输入，通过函数体中的处理程序实现该函数的功能，最后得到返回值作为输出。在 C++ 中所有函数都有返回值，或者返回计算结果，或者返回执行状态。如果函数不需要返回值，可使用void来表示它的返回类型。函数通过`return`返回，`return`的作用是返回一个与返回类型相同类型的值，并终止函数执行。

**2. 参数传递**

函数调用时传送给形参表的实参必须与形参在类型、个数、顺序上保持一致。参数传递有两种方式：一种是传值，这是默认的参数传递方式；一种是引用类型

传值：把实参的值传递给函数局部工作区相应的副本，函数使用这个副本执行必要的功能，这样函数修改的是副本的值，而实参不变

引用：需将形参声明为引用类型，即在参数名前加一个`&`。当实参与引用型形参结合时，被传递的不是实参的值，而是实参的地址，函数通过地址直接使用被引用的实参。

> 在使用传值型参数时，参数可以是常数、常量、变量或表达式
>
> 但在使用引用型参数时，参数只能是变量或对象
>
> 数组作为形参可按传值方式声明，但实际采用引用方式传递，传递的是数组第一个元素的地址

一种特殊的引用调用方式叫做常值引用，其格式为`const Type& a`，在函数体中不能修改常值参数

若传送的值参是一个对象（作为类的实例）时，在函数中就创建了该对象的一个副本，在创建这个副本时会调用该对象的复制构造函数。如果该类没有显式定义的复制构造函数，那么编译器会自动创建一个默认的复制构造函数，而在函数结束前要调用该副本的析构函数撤销这个副本。需要注意，如果一个类在构造函数中用`new`为指针成员分配了内存空间，并在析构函数中用`delete`进行释放，那么必须手工定义它的复制构造函数。因为编译器自动创建的复制构造函数只能够进行指针的简单复制，并不会分配新的内存空间，这样当副本析构后，母本占有的动态空间会被释放掉，造成错误。若采用引用方式传递对象，在函数中不创建该对象的副本，也不存在最后撤销副本的问题。但是，通过引用传递的是对象时，函数对对象的改变将影响调用的对象。

**3. 成员函数的返回值**

成员函数返回值为传值方式时，允许改变该对象的私有数据成员。当返回值为常值传值方式时，需在函数说明中加上`const`标识，使得该对象的私有成员不能被改变。当成员函数的返回值为引用方式时，该成员函数返回值应是一个已存在变量（或对象）的别名。当该成员函数的返回值被改变时，其对应变量（或对象）的值将改变。当成员函数的返回值为常值引用方式时，其返回值与引用方式的成员函数返回值类同。但该成员函数不能改变该对象的私有成员。

当成员函数返回值为常值传值方式或常值引用方式时，`const`标识符一般放在最后`float GetHighTemp()const`

**4. 友元函数**

在类声明中可使用保留字`friend`定义友元函数。友元函数实际上并不是这个类的成员函数，它可以是一个常规函数，也可以是另一个类的成员函数。如果想通过这种函数存取类的私有成员和保护成员，则必须在类的声明中给出函数的原型，并在该函数原型前面加上`friend`。

### 动态存储分配

C++ 为动态存储分配提供了`new`和`delete`，它们操纵属于可利用空间的存储并取代了 C 中的库函数`malloc`和`free`。操作`new`要求以被建立对象的类型作为参数，并返回一个指向新分配空间的指针。此时返回指针自动根据类型说明进行类型转换。

```c++
int *ip = new int;
// 或
Point *p = new Point;
```

在 C++ 中没用无用单元收集，这样使用`new`分配的存储必须显式地使用`delete`释放。`delete`函数不需要明确指出`new`分配多少存储。

```c++
Point *P = new Point[100];
delete[] p;
```

若释放时遗漏了`[]`则将只释放`p`所指示的第一个元素。

### C++ 中的继承

继承性（inheritance）是渐增式地修改已有的类定义以产生新类的技术。下列声明的类`Polygon`（多边形）是`Quadrilateral`（四边形）类的基类，其中`:public Polygon`指明类`Quadrilateral`是类`Polygon`的子类，而且类`Polygon`的公有成员应是类`Quadrilateral`的公有成员。在成员函数`Polygon::draw`的声明中记号`=0`指明这个方法的定义应由子类提供，记号`=0`还使得`Polygon`成为一个抽象类。这意味着其没有实例，因而所有操作都没有实现代码。

```c++
// Polygon类的定义
class Polygon {
	public:
		Polygon(Point);
		void move(Point);
		void isInside(Point);
		Point getREferencePoint();
		virtual void draw() = 0;
	private:
		Point referencePoint;
};
```

```c++
// 类Polygon的子类Quadrilateral的定义
class Quadrilateral: public Polygon {
    public:
    	Quadrilateral(Point, Point);
    	void isInside(Point);
    	void draw();
    private:
    	Point vertex2;
};
```

下列展示了类`Quadrilateral`的完整定义，是复合了上面两个类的定义而得到的结果。注意，`Polygon`类的私有成员`referencePoint`并未成为`Quadrilateral`类的私有成员，只能使用在`Polygon`类中定义的成员函数`getReferencePoint()`来进行存取。因此`Quadrilateral`对象的第一个顶点（即referencePoint）不能在`Quadrilateral`类中直接存取。

```c++
// Quadrilateral类的实际定义
class Quadrilateral {
    public:
    	Quadrilateral(Point, Point);
    	Point getReferencePoint();		// 从Polygon类继承的属性
    	void isInside(Point);
    	void move(Point);				// 从Polygon类继承的属性
    	void draw();
    private:
    	Point vertex2;
};
```

现在改变`Polygon`的声明，以使其原来被封装的数据成员对它的子类有效。下列程序改变了`Quadrilateral`类继承`Polygon`类的方式。使用保留字`protected`代替之前的`private`，则`Polygon`类的子类就能继承那些被声明为`protected`的成员。`Quadrilateral`类的成员函数现在就能直接存取在程序中给定的`referencePoint`了

```c++
class Quadrilateral {
    public:
    	Quadrilateral(Point, Point);
    	Point getReferencePoint();		// 从Polygon类继承的属性
    	void isInside(Point);
    	void move(Point);				// 从Polygon类继承的属性
    	void draw();
    protected:
    	Point referencePoint();			// 从Polygon类继承的属性
    	Point vertex2;
};
```

### 多态性

多态性（polymorphism）是指允许同一个函数（或操作符）有不同的版本，对于不同的对象执行不同的版本。C++ 支持以下两种多态性：

1. 编译时的多态性，表现为函数名（或操作符）的重载
2. 运行时的多态性，通过派生类和虚函数来实现

**1. C++ 中的函数名重载**

函数名重载运行 C++ 程序中多个函数取相同的函数名，但其形参或返回类型可以不同。例如，C 标准库中有`abs()`、`labs()`和`fabs()`，分别计数整型数、长整型数和双精度型数的绝对值。在 C 中因处理的数据类型不同，必须取不同的函数名。在 C++ 中，可以把这三个函数都命名为`abs()`：

```c++
int abs(int);
long abs(long);
double abs(double);
```

编译器能够比较具有同名的函数特征，通过识别实参的数目和每个实参的类型，来标识使用于一个特定调用的是哪个版本的`abs()`

**2. C++ 的操作符重载**

C++ 可用同一个名字定义多个操作，即操作符符重载。例如可命名函数：`clear(int *)`，它将一个整数清零。还可以再命名另一个函数`clear(int[])`，它把一个整数数组清零。为了支持面向对象，C++ 提供了双目重载操作符，如`+`和`<`。这种操作可使得程序更可读、写得更自然。例如，可定义“点（Point）”的运算（作为成员函数），如：

* `p1 + p2`：把两个点`(x1, y1)`和`(x2, y2)`相加成一个点`(x1 + x2, y1 + y2)`
* `p1 < p2`：两个点`p1`和`p2`的“小于关系”，表示`p1`比`p2`更靠近原点`(0, 0)`
* `p1 / i`：一个点`p = (x, y)`除以一个整数`i`的除法`(x / i, y / i)`

可以按照下述方式说明重载操作：

```c++
Point operator+(const Point& p);
Point operator/(int i);
int operator<(const Point& p);
```

使用这些新操作表达式：

```c++
Point midPoint = (point1 + point2) / 2;
// 或
if(midPoint < referencePoint)...
```

注意：每一个这样的操作符在调用时可看成是该操作符左边对象的成员函数。比如，`point1 + point2`实际上是一个消息。由类`Point`的实例`point1`调用成员函数`+`，该对象的属性确定第一个操作数的值；函数参数表中指定的`Point`的实例`point2`的属性确定第二操作数的值。这种重载能力允许像使用内建类型（如`int`，`float`）那样来使用用户自定义类型。与在不允许重载操作的语言中相同的语句比，可以改善可读性。

但重载作为非成员函数的（双目）操作符时，参与运算的两个操作数必须都出现在参数表中。第一个参数是第一操作数，第二参数是第二个操作数。例如，若用`struct`定义`worker`记录结构如下：

```c++
struct worker{
    int id;
    char name[20];
    float age;
}
```

设`wk`是属于`worker`的一个对象，可使用如下语句输入或输出`wk`数据：

```c++
cin >> wk.id >> wk.name >> wk.age;
cout << wk.id << "" << wk.name << "" << wk.age << endl;
```

若要对该对象进行整体输入或输出，可将提取操作符`>>`或插入操作符`<<`重载，使它们直接适用于`worker`类的对象。

```c++
istream& operator>>(istream& istr, worker& x){
    istr >> x.id >> x.name >> x.age;
    return istr;
}
ostream& operator<<(ostream& ostr, const worker& x){
    ostr << x.id << "" << wk.name << "" << wk.age << endl;
    return ostr;
}
```

按照上述定义后，就可以直接进行输入输出：

```c++
cin >> wk;
cout << wk;
```

执行第一条语句时将把实际参数`cin`和`wk`引用（即按地址方式）传送给被调用函数中的`istr`和`x`形参，使得`istr`和`x`分别被取代（或称换名）为`cin`和`wk`,函数中对`istr`和`x`的操作实际上就是对`cin`和`wk`的操作。该函数返回`istr`（即`cin`），以便能够在一条输人语句中连续使用`>>`操作符对多个对象进行输入。
注意：当在同一行上输入多个数据时，其数据之间要用空格隔开。
输出同理。

**3. 虚函数与动态绑定**

虚函数（virtual function）是在基类被声明为`virtual`，并在一个或多个派生类中被重定义的函数。如果在基类声明中，在一个函数的函数特征之前加上关键字`virtual`，则编译器将建立一个可由运行环境解释的特殊结构，并由程序执行时而不是编译时由运行环境来执行对该函数的调用。

用`virtual`声明的函数实现：

1. 如果在上面`Polygon`类的声明中`draw()`后面没有`=0`，同时代码文件中提供了`draw()`操作的默认实现，则所有没有给出自己特殊的`draw()`操作的子类将继承这个默认实现。
2. 不提供`draw()`的实现，`draw()`操作被设置为`=0`。此时声明的函数成为纯虚函数（pure virtual function），如果一个类至少有一个纯虚函数，则该类是抽象类（abstract class）。

一个抽象类必须作为基类被其他类继承，自己不能生成实例，因为抽象类至少有一个函数没有实现，如果基类中某函数声明为虚函数，则该基类的任何派生都必须定义自己的实现。

在`draw()`的声明中记号`virtual`指明实际调用的成员将在运行时动态地确定，而不是在编译时确定。这种做法就是动态绑定（dynamic binding）

```c++
// 动态绑定的例子
void display(Polygon& p){
    ...
    p.draw();
    ...
}
Quadrilateral q(Point p1(1, 1), Point p2(2, 2));
...
display(q);
...
```

`display()`函数接收了一个在参数表中传递来的`Quadrilateral`对象`q`，并在函数执行时调用了`Polygon`类的操作`draw()`。然而`Polygon`类中`draw()`操作是纯虚函数，没有提高实现，而具体实现是由其子类`Quadrilateral`完成的。这样在运行时动态地将要执行的成员函数从`Polygon`类的`draw()`连接到`Quadrilateral::draw()`。这种成员函数调用成员函数的绑定如下：

![image-20240524144646742](https://lc-typora-pic.oss-cn-hangzhou.aliyuncs.com/img_for_typora/image-20240524144646742.png)

在讨论线性结构时可知，线性表有两种存储表示：基于数组的存储表示（顺序表）和基于链表的存储表示（单链表）。若将它们定义为抽象基类和两个具体派生类的关系：

```c++
class linearList					// 抽象基类：线性表
    ...
class seqList:public linearList		// 派生类：顺序表
    ...
class linkedList:public linearList	// 派生类：单链表
    ...
```

如果需要定义和使用一个线性表对象，可以如下定义选用某种存储表示：

```c++
linearList *p;
seqList seqList_obj;
p = &seqList_obj;
int add = p -> Locate(5);		// 按顺序表使用线性表
...
// 或
linearList *p;
linkedList linkedList_obj;
p = &linkedList_obj;
int add = p -> Locate(5);		// 按单链表使用线性表
...
```

### C++ 的模板

在 C++ 中支持抽象的方式有三种：使用抽象类，使用模板及使用`void * struct`。把模板的概念加到 C++ 中，是想要抽出构件的不依赖于数据类型的构件的逻辑功能。在考虑定义`Point`类时，`x`，`y`值的数据类型可能因问题而异，在图形系统中对于屏幕坐标需要整数值，但在地球坐标系统中则需要浮点值。因此可使用模板机制进行重写。将类声明中可能涉及的数据类型参数化，仅使用符合`T`代表，并在类声明的签名或每个成员函数的实现程序前面增加一条类型参数化语句：

```c++
template<class T>
```

在程序内部可以直接使用参数`T`定义相应的变量的数据类型

```c++
// 类Point的C++模板
template<class T>
class Point {
    public:
    	Point(T, T);
    	Point(Point p);
    	T get_x();
    	T_get_y();
    private:
    	T x;
    	T y;
};
```

这样定义的类成为类属（generic）类，它提供一个类模板，既可用于整数坐标值的实例化对象，也可用于实数坐标值的实例化对象。在实际使用时，用语句：

```c++
Point<int> a
```

建立一个类属类`Point`的实例，其将使用整数类型来给出`x`，`y`值，在执行环境下通过简单的代换，变成针对`int`数据类型的对象定义或算法

## 4. 算法定义

