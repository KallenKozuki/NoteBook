# C++基础入门

## 1 C++初识

### 1.1 第一个	C++程序

步骤：创建项目→创建文件→编写代码→运行程序

### 1.2 注释

单行注释：`// 描述信息`

 - 通常在一行代码的上方，或某一句的末尾，==对该行代码说明==

多行注释：`/* 描述信息 */`

- 通常在一段代码的上方，==对该段代码做整体说明==

### 1.3 变量

**作用：**给一段制定的内存空间起名，方便操作内存

**语法：**`数据类型 变量名 = 初始值`

### 1.4 常量

**作用：**用于记录程序中不可更改的数据

- **#define 宏常量：**`#define 常量名 常量值` ==通常在文件上方定义==，表示一个常量
- **const修饰的变量：**`const 数据类型 常量名 = 常量值` ==通常在变量定义前加关键字const==，修饰该变量为常量，不可修改

### 1.5 关键字

**作用：**关键字是C++中预先保留的单词，==定义变量或常量时，不要用关键字==

### 1.6 标识符命名规则

+ 不能是关键字

+ 只能由字母、数字、下划线组成

+ 开头字符必须是字母或下划线

+ 字母区分大小写

> 命名尽量见名知意
## 2 数据类型

C++规定在创建一个变量或者常量时，必须指定其数据类型，否则无法分配内存空间

### 2.1 整型

**定义：**整型变量表示==整数类型==的数据

**四种不同的整型：**

|       数据类型        |     取值范围     |                      占用空间                       |
| :-------------------: | :--------------: | :-------------------------------------------------: |
|    short（短整型）    | -2^15^ ~ 2^15^-1 |                        2字节                        |
|      int（整型）      | -2^31^ ~ 2^31^-1 |                        4字节                        |
|    long（长整型）     | -2^31^ ~ 2^31^-1 | windows为4字节，Linux为4字节（32位），8字节（64位） |
| long long（长长整型） | -2^63^ ~ 2^63^-1 |                        8字节                        |

### 2.2 sizeof关键字

**作用：**利用sizeof关键字可以==统计数据类型所占的内存大小==

**语法：**`sizeof( 数据类型 / 变量 )`

### 2.3 实型（浮点型）

**作用：**用于表示小数

- 单精度float（4字节，7位有效数字）

- 双精度double（8字节，15~16位有效数字）

区别在于==有效数字==范围不同，默认情况下，输出一个小数会显示6位有效数字

```c++
	//float数的定义
	float f1 = 3.14f;//创建float数时，在数值后加一个f可以让编译器少一步转换（输入小数默认是double）
	double f2 = 3.14;
	//科学计数法
	float f2 = 3e2;//e2 = 10 ^ 2
```

> ``sizeof``计算的是变量在内存上的大小，单位是字节
>
> 后续STL中的容器的``.size()``函数计算的是容器的大小，指的是合法元素的个数

### 2.4 字符型

**作用：**用于显示单个字符

**语法：**`char 变量名 = '单个字符'`

- 占用1字节，内存中存放的是字符的ASCII码
> 单引号，单字符

```c++
	//char的定义
	char a = 'a';
	cout << "a的长度：" << sizeof(a) << endl;//a的长度：1
	cout << a << endl;//a

	int b = (int)a;//将a转化为整型（即a的ASCII码）
	cout << b << endl;//97
```

### 2.5 转义字符

**作用：**用于表示一些不能显示出来的ASCII码

**常用转义字符：**

| 转义字符 |                         含义                         | ASCII码（十进制） |
| :------: | :--------------------------------------------------: | :---------------: |
|    \n    |                换行，跳转至下一行开头                |        010        |
|    \t    | 水平制表，跳转至下一个TAB位置（一个指标位含8个空格） |        009        |
|   \\\    |                  代表一个反斜线字符                  |        092        |

### 2.6 字符串型

**作用：**表示一串字符

**包含头文件：**``#include <string>`` 

**语法：**``string 变量名 = "多个字符"``

> 养成良好的变成习惯，用到string时需要加头文件



### 2.7 布尔类型

**作用：**表示真或假

**语法：**``bool 变量名 = 变量值``

+ 有且仅有两个值：true==(绝对值大于等于1)== 和 false==(绝对值小于1)==
+ 占一个字节

> Java中布尔类型的关键字是boolean，C++中是bool

### 2.8 数据的输入

**作用：**从键盘获取输入

**语法：**``cin >> 变量``

```C++
	//cin的应用
	int a = 0;
	cout << a << endl;//0
	cin >> a;//输入329
	cout << a << endl;//329

	string b = "Kallen";
	cout << b << endl;//Kallen
	cin >> b;//输入Kozuki
	cout << b << endl;//Kozuki
```

## 3 运算符

**作用：**代码运算

| 运算符类型 | 作用                             |
| ---------- | -------------------------------- |
| 算数运算符 | 处理四则运算                     |
| 赋值运算符 | 用于将表达式的值赋值给变量       |
| 比较运算符 | 用于表达式的比较，并返回真或假   |
| 逻辑运算符 | 用于根据表示式的值，并返回真或假 |

### 3.1 算术运算符

**算数运算符类型：**

+ 加	+
+ 减	- 
+ 乘	*
+ 除	/
+ 取模	%
+ 递增	++
+ 递减	--

```C++
	//前置递增和后置递增（递减同理）
	int a1 = 10;
	int b1 = ++a1 * 2;//前置递增（减）：先变量+1，后表达式运算
	cout << "a1=" << a1 << endl;//11
	cout << "b1=" << b1 << endl;//22

	int a2 = 10;
	int b2 = a2++ * 2;//后置递增（减）：先表达式运算，后变量+1
	cout << "a2=" << a2 << endl;//11
	cout << "b2=" << b2 << endl;//20
```

> 整型与整型运算后仍是整型（若有小数位则舍弃小数位）；
>
> 除数不能为0（包括除法运算和取模运算）
>
> 两个小数不可做取模运算

### 3.2 赋值运算符

**赋值运算符类型：**

+ 等于（赋值）	=
+ 加等于	+=
+ 减等于	-=
+ 乘等于	*=
+ 除等于	/=
+ 模等于	%=

```C++
	//加等于（其余同理）
	int a = 10;
	a += 2;//a = a + 2
	cout << a << endl;//12 
```

### 3.3 比较运算符

**比较运算符类型：**

+ 相等	==
+ 不等	!=
+ 小于	<
+ 大于	>
+ 小于等于	<=
+ 大于等于	>=

### 3.4 逻辑运算符

**逻辑运算符类型：**

+ 非	！
+ 与	&&
+ 或	||

## 4 程序流程结构

C++支持三种程序运行结构：顺序结构、选择结构、循环结构

+ 顺序结构：程序按顺序进行，不发生跳转
+ 选择结构：依据条件是否满足，有选择的执行相应功能
+ 循环结构：依据条件是否满足，循环多次执行某段代码

### 4.1 选择结构

#### 4.1.1 if语句

**作用：**执行满足条件的语句

+ **单行if**

  **语法：**``if(条件){ 满足条件时执行的指令 }``

+ **多行if**

  **语法：**``if(条件){ 满足条件时执行的指令 }else{ 不满足条件时执行的指令 }``

+ **多条件if**

  **语法：**``if(条件1){ 满足条件1时执行的指令 }else if(条件2){ 满足条件2时执行的指令}...else{ 前述条件都不满足时执行的指令 }``

#### 4.1.2 三目运算符

**作用：**通过三目运算符实现简单的逻辑判断

**语法：**``表达式1 ? 表达式2 : 表达式3``

> 如果表达式1的值为真，则执行表达式2，并返回表达式2的结果；
>
> 如果表达式1的值为假，则执行表达式3，并返回表达式3的结果；
>
> 三目运算符返回的是变量，可以进一步赋值；

```C++
	//三目表达式的运用
	int a = 1128;
	int b = 329;
	int c = a >= b ? a : b;
	cout << "c=" << c << endl;//c=1128
	//三目表达式的赋值
	(a <= b ? a : b)= 417;
	cout << "a=" << a << endl << "b=" << b << endl;//a=1128	b=417

```

#### 4.1.3 switch语句

**作用：**执行多条件分支语句

**语法：**

```C++
	switch(表达式){
        case 常量结果1: 执行语句; break;
        case 常量结果2: 执行语句; break;
       	...
       	default: 执行语句; break;
    }
```

```C++
	//switch的用法
	int a = 0;
	cin >> a;
	switch (a) {
	case 59: 
        cout << "not pass." << endl;
		break;
	case 61:
        cout << "pass" << endl;
		break;
	default: 
        cout << "not mark." << endl;
		break;
	}
```

> switch关键字后的表达式类型只能是==整型==或者==字符型==
>
> case关键字后的语句必须是常量结果，不可以是条件判断语句，因为条件判断语句的返回值是真或假而不是常量
>
> switch与if语句相比，对多条件判断时结构更加清晰，但switch语句不可以判断区间

### 4.2 循环结构

#### 4.2.1 while循环语句

**作用：**满足循环条件，执行循环语句

**语法：**``while(循环条件){ 循环语句 }``

```C++
	//while循环猜随机数的大小
	int num = rand() % 100 + 1;//生成一个范围在0+1~99+1范围内的随机数
	int guess = 0;
	while (guess != num) {
		cout << "Enter a number:" << endl;
		cin >> guess;
		if (guess > num) {
			cout << "Your guess is larger than that number!" << endl;
		}
		else if (guess < num) { 
			cout<< "Your guess is smaller than that number!" << endl;
		}
		else{
			cout << "Success! The number is " << guess << endl;
		}
	}
```

#### 4.2.2 do...while循环语句

**作用：**执行一次循环语句，满足条件后继续执行循环语句

**语法：**``do{ 循环语句 } while(循环条件)``

> while和do...while的差别是do...while会至少运行一次“循环语句”

```C++
	//do...while求三位数中的水仙花数（每一位数字的三次方之和等于水仙花数本身）
	//解法一
	int a = 1;//百位
	int b = 0;//十位
	int c = 0;//个位
	int d, e;
	do {
		//判断是不是水仙花数
		d = 100 * a + 10 * b + c;
		e = a * a * a + b * b * b + c * c * c;
		if (e == d) {
			cout << d << endl;
		}
		//个位自加
		c++;
		//进位
		if (c == 10) {
			c = 0;
			b++;
		}
		if (b == 10) {
			b = 0;
			a++;
		}
	} while (d != 999);

	//解法二
	int num = 100;
	do {
		//一个数对10取余可得这个数的个位
		int a = (num / 1) % 10;//个位
		int b = (num / 10) % 10;//十位
		int c = (num / 100) % 10;//百位
		int sum = a * a * a + b * b * b + c * c * c;
		if (sum == num) {
			cout << num << endl;
		}
		num++;
	} while (num < 1000);
```

#### 4.2.3 for循环语句

**作用：**满足循环条件，执行循环语句

**语法：**``for(起始表达式; 条件表达式; 末尾循环体){ 循环语句 }``

```C++
	//敲桌子：从1到100中，如果一个数包含7或者它是7的倍数，则打印敲桌子，否则直接输出
	for (int i = 1; i <= 100; i++) {
		if ((i % 10 == 7)
			|| (i / 10 % 10 == 7)
			|| (i % 7 == 0)){
			cout << "敲桌子" << endl;
		}
		else {
			cout << i << endl;
		}
	}
```

#### 4.2.4 嵌套循环

**作用：**在循环中嵌套一层循环，以解决实际问题

```C++
	//嵌套循环打印乘法口诀表
	for (int i = 1; i < 10; i++) {
		for (int j = 1; j <= i; j++) {
			int mul = i * j;
			cout << i << "×" << j << "=" << mul << "\t";
			if (i == j) {
				cout << endl;
			}
		}
	}
```

> 需要写嵌套循环时，优先从底层循环开始写

### 4.3 跳转语句

#### 4.3.1 break语句

**作用：**跳出==选择结构==或者==循环结构==

+ 出现在switch条件语句中，作用是终止当前case并跳出switch
+ 出现在循环语句中，作用是跳出循环语句
+ 出现在嵌套循环中，写在哪一层循环就跳出哪一层循环

#### 4.3.2 continue语句

**作用：**在==循环==语句中，直接跳转至下一次循环

#### 4.3.3 goto语句

**作用：**可以无条件跳转语句

**语法：**``goto 标记``

> 如果==标记==的名称存在，则执行到goto语句时跳转到标记的位置

## 5 数组

### 5.1 概述

**定义：**数组是一个集合，里面存放了相同类型的数据元素

+ 数组中==每个元素都是相同的数据类型==
+ 数组是由==连续的内存位置==组成的

### 5.2 一维数组

#### 5.2.1 一维数组的定义方式

+ ``数据类型 数组名[ 数组长度 ]``
+ ``数据类型 数组名[ 数组长度 ] = { 值1, 值2... }``
+ ``数据类型 数组名[ ] = { 值1, 值2... } ``

#### 5.2.2 一维数组的数组名的用途

+ 统计整个数组在内存中的长度（利用==sizeof==关键字）
+ 可以获取数组在内存中的首地址

​	``cout << 数组名;//打印数组首地址``

​	``cout << &数组名[下标];//打印某个元素的地址，其中 & 是取址符``

#### 5.2.3 一维数组练习案例

```C++
	//数组练习案例1：一个数组中五只小猪的体重分别为300/350/200/400/250，找到并打印最重的小猪的体重
	int weight[5] = { 300,350,200,400,250 };
	int max = weight[0];
	for (int i = 0; i < sizeof(weight)/4; i++) {
		if (weight[i] > max) {
			max = weight[i];
		}
	}
	cout << "最重的小猪的体重为：" << max << endl;
```

```C++
	//数组练习案例2：倒置一个数组{ 1,3,2,5,4 } → { 4,5,2,3,1 }
	//解法一
	int array[5] = { 1,3,2,5,4 };
	int temp[5];
	for (int i = 0; i <= 4; i++) {
		temp[i] = array[4 - i];
	}
	for (int i = 0; i < 5; i++) {
		array[i] = temp[i];
		cout << array[i] << endl;
	}

	//解法二
	int array[5] = { 1,3,2,5,4 };
	int start = 0;
	int end = sizeof(array) / sizeof(array[0]) - 1;
	while (start < end) {
		int temp = array[start];
		array[start] = array[end];
		array[end] = temp;
		start++;
		end--;
	}
	for (int i = 0; i < 5; i++) {
		cout << array[i] << endl;
	}
```

#### 5.2.4 冒泡排序

**作用：**最常用的算法，对数组内的数进行排序

+ 升序
  1. 比较相邻的元素，如果第一个比第二个大则交换他们的位置；
  2. 对每一对相邻元素都重复 1 中的步骤，执行完毕后，末尾的位置即为最大值；
  3. 重复 1 和 2，每重复一次1 和 2就使比较次数-1.直到不需要比较。
+ 降序只需要把上述 1 中的交换条件改为==“第一个比第二个小”==即可

```C++
	//冒泡排序升序
	int sort[] = { 2,4,0,5,7,1,3,8,9,6 };
	//排序算法
	for (int i = 0; i < sizeof(sort)/sizeof(sort[0] - 1); i++) {
		for (int j = 0; j < sizeof(sort) / sizeof(sort[0]) - 1 - i; j++) {
			if (sort[j] > sort[j + 1]) {//比较改为 < 则是降序
				int temp = sort[j + 1];
				sort[j + 1] = sort[j];
				sort[j] = temp;
			}
		}
	}
	//打印数组
	for (int i = 0; i < 10; i++) {
		cout << sort[i] << " ";
	}
```

#### 5.2.5 数组的两个特性（重要）

+ 不允许拷贝和赋值

  ```C++
  int a[] = {0,1,2};    // 含有三个整数的数组
  int s2 = a;           // 错误：不允许使用一个数组初始化另一个数组
  a2 = a;               // 错误：不能把一个数组直接赋值给另一个数组
  ```

+ 使用数组通常是将其转换为指针

> **重要：**正因为上述两个特性，**数组无法以值传递的形式作为形参**，所以当为函数传递一个数组时，实际上传递的是一个**指针**
>
> 同时，正因为把数组向函数传递之后它会**坍缩成一个指针**，所以无法在函数体内通过``sizeof``求出数组的长度
>
> 因此，向函数传递一个数组时，一般还会**传入它的长度**

### 5.3 二维数组

**本质：**一维数组中每一个元素的位置存放长度相同的一维数组

#### 5.3.1 二维数组的定义方式

+ ``数据类型 数组名[ 行数 ][ 列数 ]``
+ ``数据类型 数组名[ 行数 ][ 列数 ] = {{数据1, 数据2}, {数据3, 数据4}...}``
+ ``数据类型 数组名[ 行数 ][ 列数 ] = {数据1, 数据2, 数据3...}``
+ ``数据类型 数组名[][ 列数 ] = {数据1, 数据2, 数据3...} ``

> 建议用第二种，更为直观，提高了代码的可读性

```C++
	//二维数组的定义的最佳格式（相当直观）
	int array[2][3] = {
		{1,2,3},
		{4,5,6}
	};
```

#### 5.3.2 二维数组的数组名的用途

+ 查看二维数组所占内存空间（利用==sizeof==关键字）
+ 获取二维数组首地址

#### 5.3.3 二维数组练习案例

```C++
	//二维数组练习案例
	/*
	三个同学的三科成绩如下:
	张三（语100 数100 英100）
	李四（语90  数50  英100）
	王五（语60  数70  英80）
	用二维数组输出每个同学的总成绩
	*/
	int scores[3][3] = {
		{100,100,100},
		{90,50,100},
		{60,70,80}
	};
	string name[3] = { "张三", "李四", "王五" };
	for (int i = 0; i < 3; i++) {
		int sum = 0;
		for (int j = 0; j < 3; j++) {
			sum += scores[i][j];
		}
		cout << name[i] << "的总成绩：" << sum << endl;
	}
```

## 6 函数

### 6.1 概述

**作用：**将一段代码封装起来，减少重复代码

一个较大的程序，一般分为若干个程序块，每个模块实现的特定功能。

### 6.2 函数的定义

函数的定义一般分为五个步骤：

+ 返回值类型
+ 函数名
+ 参数表列
+ 函数体语句
+ return表达式

**语法：**

```C++
返回值类型 函数名 (参数列表)
{
    函数体语句
    
    return表达式
}
```

> 实参：有值的实际参数
>
> 形参：没有值的参数（函数定义处）
>
> 调用函数时，实参传递给形参

### 6.3 函数的调用

**作用：**使用定义好的函数

**语法：**``函数名(参数)``

### 6.4 值传递

+ 函数调用时实参将数值传递给形参
+ 值传递时，如果==形参发生改变==，==并不会影响实参==

```C++
void swap(int a, int b);

int main() {
	int a = 1128;
	int b = 329;
	cout << "实参交换前：" << endl << "a=" << a << endl << "b=" << b << endl;
	swap(a, b);
	cout << "实参交换后：" << endl << "a=" << a << endl << "b=" << b << endl;

	system("pause");
	return 0;
}

void swap(int num1, int num2) {
	cout << "形参交换前：" << endl << "num1=" << num1 << endl << "num2=" << num2 << endl;
	int temp = num1;
	num1 = num2;
	num2 = temp;
	cout << "形参交换后：" << endl << "num1=" << num1 << endl << "num2=" << num2 << endl;
	return;
	}
//上述代码的输出结果仅交换了num1和num2的值，并没有交换a和b的值
```

### 6.5 函数的常见样式

+ 无参无返
+ 有参无返
+ 无参有返
+ 有参有返

### 6.6 函数的声明

**作用：**告诉编译器函数名称及如何调用函数。函数的实际主题可以单独定义

> 函数的声明可以多次（一般在main函数前），但定义只能有一次（一般在main函数后）

### 6.7 函数的分文件编写

**作用：**让代码结构更加清晰

函数分文件编写的4个步骤：

1. 创建后缀名为.h的头文件
2. 创建后缀名为.cpp的源文件
3. 在头文件中写函数的声明
4. 在源文件中写函数的定义

> 创建的头文件需要写C++的框架语句（#include <iostream>	using namespace std;)
>
> 创建的源文件需要导入第一步中创建的头文件（自创的头文件用” “包裹）
>
> main.cpp也要导入需要用到的函数所在的头文件

## 7 指针

### 7.1 指针的基本概念

**作用：**可以通过指针间接访问内存空间

+ 内存编号是从0开始记录的，一般用十六进制数字表示
+ 可以利用指针变量保存地址==（一般来说指针就是地址）==

``int a = 10;//假设变量a的地址为0x0000，则可以用一个指针p来保存这个地址``

### 7.2 指着变量的定义和使用

**语法：**``数据类型 * 指针名``

```C++
	//指针的定义
	int a = 1128;
	int* p;
	p = &a;//& 为取址符

	//指针的使用（指针前加 * 代表解引用，代表指针所指内存中的数据）
	*p = 329;
```

### 7.3 指针所占的内存空间

不论指针是什么类型，32位操作系统占用==4个字节==，64位操作系统占用==8个字节==

### 7.4 空指针和野指针

**空指针：**指针变量指向内存中编号为0的空间

**用途：**初始化指针变量

**注意：**空指针指向的内存是不可以访问的

**语法**``数据类型 * 指针名 = NULL``

> 访问空指针会报错(0~255编号的内存为系统占用,不可访问)

**野指针:**指针变量指向非法的内存空间

### 7.5 const修饰指针

+ const修饰指针 → 常量指针

  **语法：**``const 数据类型 * 指针名 = &变量名 ``

  **特性：**指针的指向可以修改，但指针指向的值不可修改

  **示例：**

  ```C++
  	int a = 10;
  	int b = 10;
  	const int* p = &a;
  	p = &b;//正确，a和b的值相同
  	*p = 20；//错误，指针指向的值不可修改
  ```

+ const修饰常量 → 指针常量

  **语法：**``数据类型 * const 指针名 = &变量名``

  **特性：**指针的指向不可修改，但指向的值可以修改

  **示例：**

  ```C++
  	int a = 10;
  	int b = 10;
  	int* const p = &a;
  	p = &b;//错误，指向不可修改
  	*p = 20;//正确，指向的值可以修改
  ```

+ const既修饰指针,也修饰常量

  **语法：**``const 数据类型 * const 指针名 = &变量名``

  **特性：**指针的指向和指针指向的内容都不可修改

  **示例：**

  ```C++
  	int a = 10;
  	int b = 10;
  	const int* const p = &a;
  	p = &b;//错误，指向不可修改
  	*p = 20;//错误，指向的值不可修改
  ```

  > 记忆技巧：定义语句中，const可翻译为常量，*可翻译为指针，按照定义语句中上述二者的==出现顺序==即可分辨是==指针常量==还是==常量指针==
  >
  > 如 ``int * const p;`` 先出现*后出现const，所以这是==指针常量==
  >
  > 如果只有一个const，谁在后面谁可修改
  >
  > 如``int * const p;`` ==指针常量==，后者可以修改 → 指针指向的==内存空间不可修改==，但内存空间的==值可修改==
  >
  > 如``const int * p;`` ==常量指针==，后者可以修改 → 指针指向的==内存空间可以修改==，但内存空间的==值不可修改==

### 7.6 指针和数组

**作用：**利用指针访问数组中的元素

```C++
	int array[10] = { 0,1,2,3,4,5,6,7,8,9 };
	int* p;
	cout << "直接访问数组中的第一个元素：" << array[0] << endl;//0
	p = array;//数组名就是数组的首地址
	cout << "利用指针访问数组中的第一个元素：" << *p << endl;//0
	
	//利用指针遍历数组中的元素
	for (int i = 0; i < 10; i++) {
		cout << *p << endl;
		p++;//表示指针向后偏移4个字节（字节数取决于数据类型）
	}
```

### 7.7 指针和函数

**作用：**利用指针作为函数的参数，可以改变实参的值（==6.4 值传递==）

```C++
//地址传递
void swap(int* p1, int* p2) {
	int temp = *p1;
	*p1 = *p2;
	*p2 = temp;
}

int main() {
	int a = 1128;
	int b = 329;
	cout << "交换前：" << endl << "a=" << a << endl << "b=" << b << endl;
	swap(&a, &b);//利用取址符传入a和b的地址（即指针）
	cout << "交换后：" << endl << "a=" << a << endl << "b=" << b << endl;
    //交换成功
```

> 不想修改实参，就使用值传递；想修改实参就使用地址传递

### 7.8 指针、数组、函数联合练习

```C++
/*指针、数组、函数练习案例：
  封装一个函数，利用冒泡排序，实现对整型数组的升序排序
  如数组{ 4,3,6,9,1,2,10,8,7,5 }
*/
//解法一：只传入一个指针，且数组长度必须固定
void BubbleSort(int* p);

int main() {
	int array[10] = { 4,3,6,9,1,2,10,8,7,5 };
	int* p = array;
	BubbleSort(p);
	for (int i = 0; i < 10; i++) {
		cout << array[i] << endl;
	}

	system("pause");
	return 0;
}
//传入指向数组首地址的指针
void BubbleSort(int* p1) {
	int* p2;//创建一个临时指针
	for (int i = 0; i < 9; i++) {
		p2 = p1;//将p2指针指向数组的首地址
		for (int j = 0; j < 9-i; j++) {
			int left = *p2;//取出数组左值
			p2++;//指针偏移到下一个位置
			int right = *p2;//取出数组右值
			if (left > right) {
				*p2 = left;
				p2--;
				*p2 = right;
				p2++;
			}
		}
	}
}
```

```C++
//解法二：传入数组首地址和数组长度
void bubbleSort(int* array, int length);

int main() {
	int array[10] = { 4,3,6,9,1,2,10,8,7,5 };
	int length = sizeof(array) / sizeof(array[0]);
	bubbleSort(array, length);
	for (int i = 0; i < 10; i++) {
		cout << array[i] << endl;
	}

	system("pause");
	return 0;
}
//传入数组首地址，即可通过下标访问整个数组
void bubbleSort(int* array, int length) {
	for (int i = 0; i < length - 1; i++) {
		for (int j = 0; j < length - 1 - i; j++) {
			if (array[j] > array[j+1]) {
				int temp = array[j];
				array[j] = array[j + 1];
				array[j + 1] = temp;
			}
		}
	}
}
```

## 8 结构体

### 8.1 结构体的基本概念

结构体属于==用户自定义的数据类型==，允许用户存储不同类型的数据

### 8.2 结构体的定义和使用

**语法：**``struct 结构体名 { 结构体成员列表 }``

```C++
//创建结构体
struct Student {
	string name;
	int age;
	int score;
};
```

通过结构体创建变量的方式有三种：

+ ``struct 结构体名 变量名``

  ```C++
  	struct Student kallen;
  	//给成员赋值
  	kallen.name = "Kallen";
  	kallen.age = 22;
  	kallen.score = 100;
  ```

+ ``struct 结构体名 变量名 = { 成员1值, 成员2值,... }``

  ```C++
  	struct Student kallen = { "Kallen",22,100 };
  ```

+ 定义结构体时顺便创建变量

  ```C++
  //创建结构体
  struct Student {
  	string name;
  	int age;
  	int score;
  }kallen, aldebaran;//在结构体的末尾可以创建多个变量，用逗号分隔开
  
  int main() {
  	kallen.name = "Kallen";
  	kallen.age = 22;
  	kallen.score = 100;
      
  	aldebaran.name = "Aldebaran";
  	aldebaran.age = 24;
  	aldebaran.score = 99;
  }
  ```

> C++中创建结构体变量时，struct关键字可以省略，但定义结构体的时候不能省略

### 8.3 结构体数组

**作用：**将自定义的结构体放入数组中方便维护

**语法：**``struct 结构体名 数组名[元素个数] = { {}, {}, ...{} }``

```C++
int main() {
	//创建结构体数组并赋值
	struct Student codeGeass[3] = {
		{"Lelouch", 18, 100},
		{"Kallen", 17, 120},
		{"C.C.", 19, 500}
	};
	//修改数组中结构体变量的成员参数
	codeGeass[2].score = 110;

	for (int i = 0; i < 3; i++) {
		cout << codeGeass[i].name << ":" << endl 
			<< codeGeass[i].age << " years old\t" 
			<< codeGeass[i].score << "points" << endl;
	}

	system("pause");
	return 0;
}
```

### 8.4 结构体指针

**作用：**通过指针访问结构体中的成员

+ 利用操作符``->``，可以通过结构体指针访问结构体属性

```C++
	//创建结构体变量
	struct Student kallen = { "Kallen",18,100 };
	//通过指针指向结构体变量
	struct Student* p = &kallen;
	//通过指针访问结构体变量中的数据
	cout << p->name << endl;
```

### 8.5 结构体嵌套结构体

**作用：**结构体中的成员可以是另一个结构体

```C++
struct Student {
	string name;
	int age;
	int score;
};
//Teacher结构体中包含一个Student结构体变量
struct Teacher {
	int id;
	string name;
	int age;
	struct Student student;
};

int main() {
	struct Teacher hemiko;
	hemiko.age = 27;
	hemiko.id = 1;
	hemiko.name = "Himeko";
	hemiko.student.name = "Kiana";
	hemiko.student.age = 19;
	hemiko.student.score = 59;

	system("pause");
	return 0;
}
```

### 8.6 结构体做函数的参数

**作用：**将结构体作为参数向函数中传递

传递方式包括：

+ 值传递
+ 地址传递

```C++
struct Student {
	string name;
	int age;
	int score;
};

void passValue(struct Student stu);
void passAddress(struct Student* stu);
void printStudent(struct Student stu);

int main() {
	struct Student stu = { "Kiana",19,59 };
	cout << "Create a student:" << endl;
	printStudent(stu);//Kiana

	passValue(stu);
	cout << "After passing value:" << endl;
	printStudent(stu);//Kiana

	passAddress(&stu);
	cout << "After passing address:" << endl;
	printStudent(stu);//Mei

	system("pause");
	return 0;
}

void passValue(struct Student stu) {
	stu.name = "Mei";
	stu.age = 20;
	stu.score = 100;
}

void passAddress(struct Student* stu) {
	stu->name = "Mei";//指针用->来访问结构体中的成员
	stu->age = 20;
	stu->score = 100;
}

void printStudent(struct Student stu) {
	cout << stu.name << ":" << endl
		<< "age:" << stu.age << endl
		<< "score:" << stu.score << endl;
}
```

### 8.7 结构体中const的使用场景

**作用：**用const来==防止误操作==

```C++
struct Student {
	string name;
	int age;
	int score;
};

void printStudent(const struct Student* stu);

int main() {
	struct Student stu = { "Kiana",19,59 };
	printStudent(&stu);//传入指针可以减少内存空间的占用，不必再复制一个副本

	system("pause");
	return 0;
}
//传入的参数加const可以禁止在本函数中对结构体成员进行修改
void printStudent(const struct Student* stu) {
	stu->age = 20;//报错，加了const之后不能修改成员的值,可以防止误操作
	cout << stu->name << ":" << endl
		<< "age:" << stu->age << endl
		<< "score:" << stu->score << endl;
}
```

### 8.8 结构体练习案例

```C++
/*结构体练习案例1：
  学校正在做毕设项目，每个老师带领5个学生，总共有三个老师，需求如下
  设计学生和老师的结构体，在老师的结构体中，有老师的姓名和一个存放5名学生的数组作为成员
  学生结构体的成员有姓名、考试分数，创建数组存放3个老师，通过函数给每个老师及所带的学生赋值
  最终打印出老师数据以及老师所带学生的数据
*/
struct Student {
	string sName;
	int score;
};
struct Teacher {
	string tName;
	struct Student sArray[5];
};

void setInfo(struct Teacher* tArray, int length);
void printInfo(struct Teacher* tArray, int length);

int main() {
	struct Teacher tArray[3];
	int length = sizeof(tArray) / sizeof(tArray[0]);
	
	setInfo(tArray, length);
	printInfo(tArray, length);

	system("pause");
	return 0;
}

void setInfo(struct Teacher* tArray, int length) {
	string tNameSeed = "ABC";
	string sNameSeed = "abcdefijklmnopq";

	for (int i = 0; i < length; i++) {
		tArray[i].tName = "Teacher_";
		tArray[i].tName += tNameSeed[i];
		for (int j = 0; j < 5; j++) {
			tArray[i].sArray[j].sName = "Student_";
			tArray[i].sArray[j].sName += sNameSeed[j + 5 * i];
			tArray[i].sArray[j].score = rand() % 100 + 1;
		}
	}
}

void printInfo(struct Teacher* tArray, int length) {
	for (int i = 0; i < length; i++) {
		cout << tArray[i].tName << ":" << endl;
		for (int j = 0; j < 5; j++) {
			cout << "\t" << tArray[i].sArray[j].sName 
				<< ": " << tArray[i].sArray[j].score << endl;
		}
	}
}
```

```C++
/*结构体案例练习2：
  设计一个英雄的结构体，包括成员姓名，年龄，性别 
  创建结构体数组，数组中存放5名英雄
  通过冒泡排序的算法，将数组中的英雄按照年龄进行升序排序，最终打印出排序后的结果
  {"刘备",23,"男"} {"关羽",22,"男"} {"张飞",20,"男"} {"赵云",21,"男"} {"貂蝉",19,"女"}
*/
struct Hero {
	string name;
	int age;
	string gender;
};

void bubbleSort(struct Hero* hArray, int length);
void printInfo(struct Hero* hArray, int length);

int main() {
	struct Hero hArray[5] = {
		{"刘备",23,"男"},
		{"关羽",22,"男"},
		{"张飞",20,"男"},
		{"赵云",21,"男"},
		{"貂蝉",19,"女"}
	};
	int length = sizeof(hArray) / sizeof(hArray[0]);

	bubbleSort(hArray, length);
	printInfo(hArray, length);

	system("pause");
	return 0;
}

void bubbleSort(struct Hero* hArray, int length) {
	for (int i = 0; i < length - 1; i++) {
		for (int j = 0; j < length - 1 - i; j++) {
			if (hArray[j].age > hArray[j + 1].age) {
				struct Hero temp = hArray[j];
				hArray[j] = hArray[j + 1];
				hArray[j + 1] = temp;
			}
		}
	}
}

void printInfo(struct Hero* hArray, int length) {
	for (int i = 0; i < length; i++) {
		cout << hArray[i].name << ":"
			<< hArray[i].age << "岁\t"
			<< hArray[i].gender << endl;
	}
}
```

