# C++核心编程

## 1 内存分区模型

C++程序在执行时，内存将被大致分为==**4个区域**==

+ **代码区：**存放==函数体==的二进制代码，由==操作系统==进行管理
+ **全局区：**存放==全局变量==和==静态变量==以及==常量==
+ **栈区（stack）：**由==编译器==自动分配释放，存放==函数的参数值==，==局部变量==等
+ **堆区（heap）：**由==程序员==分配和释放，若程序员没有释放，程序结束时由操作系统自动回收

> 不同区域存放的数据，赋予不同的生命周期，实现灵活编程
>
> 局部变量（内部变量）：由某函数或者某对象创建的变量，只能被内部引用，而无法被其他对象和函数引用
>
> 全局变量：可以是某对象的函数创建，也可以在程序中的任何地方创建，可被所有对象和函数引用
>
> 静态变量：程序执行前系统就为之静态分配内存空间的一类变量

### 1.1 程序运行前

在程序编译后，生成了exe可执行程序，==未执行该程序前==分为两个区域

**代码区：**

+ 存放CPU执行的机器指令
+ 代码区是==共享的==，共享的目的是对于频繁被执行的程序，只需要在内存中有一份代码即可
+ 代码区是==只读的==，使其只读的原因是防止程序意外地修改了他的指令

**全局区：**

+ ==全局变量==和==静态变量==存放在此
+ 全局区还包含了==常量==区，字符串常量和其他常量（const）也存放在此
+ 该区域的数据在程序结束后由操作系统释放

```C++
int g_a = 10;//全局变量
const int c_g_a = 10;//全局常量
int main() {
	static int s_a = 10; //静态变量
	const int c_l_a = 10; //局部常量
	int a = 10; //局部变量

	//----------------全局区中的数据--------------------
	cout << "全局变量的地址：" << (int)&g_a << endl;
	cout << "全局常量的地址：" << (int)&c_g_a << endl;
	cout << "静态变量的地址：" << (int)&s_a << endl;
	cout << "字符串常量的地址：" << (int)&"kallen" << endl << endl;

	//---------------不在全局区中的数据------------------
	cout << "局部变量的地址：" << (int)&a << endl;
	cout << "局部常量的地址：" << (int)&c_l_a << endl;

	system("pause");
	return 0;
}
```

### 1.2 程序运行后

**栈区：**栈区的数据由编译器开辟和释放

> 不要返回栈区局部变量的地址

```C++
int* func(int b) { 
	b = 100;//b是形参，也会放在栈区
	int a = 10;//局部变量，存放在栈区，栈区的数据在函数执行完毕后自动释放
	return &a;//故返回栈区局部变量的地址会导致错误的出现
}

int main() {
	int* p = func(1);
	cout << *p << endl;//10
	cout << *p << endl;//错误，该指针指向的栈区地址已被释放
    
	system("pause");
	return 0;
}
```

**堆区：**由程序员分配释放，若程序员不释放，程序结束时由操作系统回收

> C++中主要利用 new 关键字开辟内存

```C++
int* func() { 
	int* p = new int(10);//利用new关键字把数据存放到堆区，返回值是数据的地址
	return p;//指针p本质还是一个局部变量，但它指向了堆区的地址
}

int main() {
	int* p = func();
	cout << *p << endl;//10
	cout << *p << endl;//10，堆区数据未被释放
    
	system("pause");
	return 0;
}
```

> 总结：
>
> 代码区主要存放代码段，具有**共享**和**只读**的特性
>
> 全局区中的数据包括：**全局变量，静态变量，全局常量，字符串常量**
>
> 上述两个区在**编译后且未执行该程序前**就已经分配完成
>
> 栈区中的数据包括：**局部变量，局部常量**。栈区会在函数体执行完毕后由编译器释放
>
> 可以通过**new**关键字将数据存放至堆区，堆区的数据由程序员主动释放（若程序员没有主动释放，则程序结束时由操作系统回收）

### 1.3 new操作符

**相关概念：**C++中利用``new``操作符在堆区开辟数据（由程序员手动开辟手动释放，释放利用关键字``delete``）

**语法：**``new 数据类型//返回值是内存地址，需用指针接收）``

```c++
int* heapValue () {
	return new int(10);
}

void testHeap() {
	int* p = heapValue();
	cout << *p << endl;//10
	cout << *p << endl;//10, 堆区数据需要程序员主动释放
	delete p;//释放堆区内存
	cout << *p << endl;//报错，内存被释放，无法访问
}

void heapArray() {
	int* p = new int[10]{ 1 };
	for (int i= 0; i < 10; i++) {
		p[i] = i;//数组名本质就是一个指针
		cout << p[i] << endl;
	}
	delete[] p;//释放堆区数组的时候需要加一个中括号
}

int main() {
	testHeap();
	heapArray();

	system("pause");
	return 0;
}
```

## 2 引用

### 2.1 引用的基本使用

**作用：**给变量起别名

**语法：**``数据类型 &别名 = 原名``

```C++
	int a = 10;
	int& b = a;
	b = 20;
	cout << a << endl;//20
```

> 对别名的操作等同于对原名的操作

### 2.2 引用的注意事项

+ 引用必须初始化
+ 引用在初始化后不可改变

```C++
	int a = 10;
	int& b = a;
	//int& c;//报错，引用必须初始化
	int c = 100;
	b = c;//本质上是赋值操作，因为引用指向不可更改
	cout << b << endl;//100
```

### 2.3 引用做函数参数

**作用：**函数传参时，利用引用可以让形参修饰实参

**优点：**简化指针修改实参

```C++
//值传递
void swap01(int a, int b) {
	int temp = a;
	a = b;
	b = temp;
}

//地址传递
void swap02(int* a, int* b) {
	int temp = *a;
	*a = *b;
	*b = temp;
}

//引用传递（形参a是实参a的别名，b同理）
void swap03(int& a, int& b) {
	int temp = a;
	a = b;
	b = temp;
}

int main() {
	int a = 1;
	int b = 2;
	swap01(a, b);
	cout << "swap01:\na=" << a << endl << "b=" << b << endl;//a=1,b=2
	swap02(&a, &b);
	cout << "swap02:\na=" << a << endl << "b=" << b << endl;//a=2,b=1
	swap03(a, b);
	cout << "swap03:\na=" << a << endl << "b=" << b << endl;//a=1,b=2

	system("pause");
	return 0;
}
```

> 地址传递和引用传递的效果是一样的，但引用的语法更简单

### 2.4 引用做函数的返回值

+ 引用可以作为函数的返回值

+ 函数调用可以作为左值

> 不要返回局部变量的引用

```C++
//返回局部变量的引用
int& localVaule() {
	int a = 1;
	return a;
}
//返回静态变量的引用
int& staticValue() {
	static int a = 2;
	return a;
}

int main() {
	int& b1 = localVaule();//返回值是引用，需要用引用去接收
	cout << b1 << endl;//1
	cout << b1 << endl;//乱码，栈区中的局部变量已被释放

	int& b2 = staticValue();
	cout << b2 << endl;//2
	cout << b2 << endl;//2

	//引用的返回可以理解为返回了一个具体的变量而不是具体的值
	staticValue() = 1000;
	cout << b2 << endl;//1000
	cout << b2 << endl;//1000

	system("pause");
	return 0;
}
```

### 2.5 引用的本质

**本质：**引用的本质在C++内部是一个==指针常量==

> 指针常量：指针指向的地址不可修改，但该地址保存的值可以修改

```C++
	int a = 1;
	int& ref = a;//自动转换为 int* const ref = &a;
	ref = 2;//编译器发现 ref 是引用，自动把本行转换为 *ref = 2;
```

### 2.6 常量引用

**作用：**主要用来修饰形参，防止误操作

> 在函数形参列表中加==const==修饰形参可以防止形参改变实参

```C++
	int& ref = 10;//错误，引用必须传入一个合法的变量空间而不是一个值
	const int& ref = 10;//加上const后, 编译器将代码修改为 int temp = 10; const int& ref = temp;
	ref = 20;//错误，加入const之后只读不写
```

```C++
void print(const int& a) {
	a = 20;//报错，形参列表加入const后禁止修改该参数，防止误操作
	cout << a << endl;
}


int main() {
	int a = 10;
	print(a);

	system("pause");
	return 0;
}
```

> 不仅是引用，在函数的形参列表中加入const修饰，都可以防止对该参数进行误操作，不论是什么类型的形参

## 3 函数进阶

### 3.1 函数的默认参数

**定义：**C++中，函数的形参列表中的形参是可以有默认参数的

**语法：**``返回值类型 函数名 (参数=默认值){函数体}``、

```C++
//如果某个位置的参数有默认值，那么从这个位置往后的参数都必须有默认值
int func(int a, int b = 2, int c = 3);

//如果函数的声明有默认值，函数实现的时候就不能有默认值(二者只能有一个有默认值)
int func(int a, int b, int c) {
	return a + b + c;
}

int main() {
    //有传值用传值，没传值用默认值
	cout << func(1, 6) << endl;//10

	system("pause");
	return 0;
}
```

### 3.2 函数占位参数

**定义：**C++中函数的形参表里可以有占位参数，调用函数是必须填补该位置

**语法：**``返回值类型 函数名(数据类型){函数体}``

```C++
//占位参数：填一个数据类型即可
void func0(int a, int) {
	cout << "test0" << endl;
}

//占位参数也可以有默认值
void func1(int a, int = 10) {
	cout << "test1" << endl;
}

int main() {
	func0(1, 1);//调用时必须传入一个对应类型的参数
	func1(1);//有默认值时可以不传占位参数

	system("pause");
	return 0;
}
```

### 3.3 函数重载

#### 3.3.1 概述

**作用：**函数名可以相同，提高复用性

**条件：**

+ 同一个作用域下
+ 函数名称相同
+ 函数参数==类型不同== 或者 ==个数不同== 或者 ==顺序不同==

> 函数的返回值不能作为函数重载的条件，即重载函数的返回值类型必须相同

```C++
void func() { cout << "func" << endl; }
void func(int a) { cout << "func(int a)" << endl; }
void func(double a) { cout << "func(double a)" << endl; }
void func(int a, double b) { cout << "func(int a, double b)" << endl; }

int main() {
	func();
	func(10);
	func(3.14);
	func(1, 1.1);

	system("pause");
	return 0;
}
```

#### 3.3.2 注意事项

+ 引用作为重载对象

```C++
void func(const int& a) { cout << "func(const int& a)" << endl; }
void func(int& a) { cout << "func(int& a)" << endl; }

int main() {
	int a = 10;
	func(a);//func(int& a), 因为变量a可读可写
	func(10);//func(const int& a), 形参传入等同于 const int& a = 10; 合法形式

	system("pause");
	return 0;
}
```

+ 函数重载碰到函数的默认参数

```C++
void func(int a, int b = 10) { cout << "func(int a, int b = 10)" << endl; }
void func(int a) { cout << "func(int a)" << endl; }

int main() {
	int a = 10;
	func(10);//报错，重载遇上函数默认值时，会出现二义性（歧义）

	system("pause");
	return 0;
}
```

## 4 类和对象

C++面向对象的三大特性：**==封装、继承、多态==**

### 4.1 封装

#### 4.1.1 封装的意义

==**封装意义一**：将属性和行为作为一个整体，表现生活中的事物==

**创建类的语法：**``class 类名{ 访问权限： 属性/行为 }``

> 属性和行为 统一称为 成员
>
> 属性 也叫做 成员属性 或者 成员变量
>
> 行为 也叫做 成员函数 或者 成员方法

```C++
#define PI 3.14

class Circle {
//访问权限
public:
	//属性
	double radius;
	//行为（函数）
	double calculateP() {
		return 2 * PI * radius;
	}
};

int main() {
	Circle small;//创建对象
	small.radius= 100;
	cout << "The Perimeter of this circle:" << small.calculateP() << endl;//628

	system("pause");
	return 0;
}
```

==**封装意义二**：将属性和行为加以权限控制==

|   公共权限   |    public     |   类内可以访问   |          类外可以访问          |
| :----------: | :-----------: | :--------------: | :----------------------------: |
| **保护权限** | **protected** | **类内可以访问** | **类外不可访问，子类可以访问** |
| **私有权限** |  **private**  | **类内可以访问** |        **类外不可访问**        |

#### 4.1.2 struct和class的区别

二者本质都是类，但默认的访问权限不同：

+ **struct**默认**public**
+ **class**默认**private**

#### 4.1.3 成员属性设为私有

**优点1：**可以主动控制读和写的权限

**优点2：**对于写权限，我们可以检测数据的有效性

```C++
class Student {
public:
	//利用getter和setter控制private属性读写的权限
	void setId(int id) {
		//检测数据的有效性
		if (id < 0) { return; }
		this->id = id;
	}
	int getId() { return this->id; }
private:
	int id;
};

int main() {
	Student kallen;
    kallen.id;//报错
	kallen.setId(329);
	cout << kallen.getId() << endl;

	system("pause");
	return 0;
}
```

#### 4.1.4 封装练习案例

```C++
//练习案例1：
//设计立方体类，包含长宽高，分别在类内和全局判断两个立方体是否相等
class Cube{
private:
	int m_L, m_W, m_H;
public:
	//类内比较函数
	void compareInside(Cube &c) {
		//C++允许同一个类的不同对象访问彼此的私有成员
		if (m_L == c.m_L && m_W == c.m_W && m_H == c.m_H) {
			cout << "True!" << endl;
		}
		else {
			cout << "False!" << endl;
		}
	}
	//getter
	int getL() { return m_L; }
	int getW() { return m_W; }
	int getH() { return m_H; }
	//setter
	void setCube(int l, int w, int h) {
		m_L = l;
		m_W = w;
		m_H = h;
	}
};
//全局对比函数用引用传递可以提高程序效率
void compareGlobal (Cube &b, Cube &c) {
	if (c.getH() == b.getH() 
		&& c.getL() == b.getL() 
		&& c.getW() == b.getW()) {
		cout << "True!" << endl;
	}
	else {
		cout << "False!" << endl;
	}
}

int main() {
	Cube a, b;
	a.setCube(1, 2, 3);
	b.setCube(1, 2, 3);
	compareGlobal(a, b);//True!
	a.compareInside(b);//True!
	b.compareInside(a);//True!

	system("pause");
	return 0;
}
```

> C++允许同一个类的不同对象访问彼此的私有成员
>
> **封装是针对类而不是对象**，相同类之间所有的成员都是**public**，**在类的成员函数中可以访问同类型实例对象的私有成员**

```C++
//练习案例2：
//给出一点的坐标，一个圆的圆心坐标和其半径，判断点与圆的相对关系，需要拆分成多文件开发
//详见 D:\Study\My Project\ClassExercisePointCircle
```

> 类的前置声明不足以生成对象，当两个类需要互相调用成员函数时，应该用多文件开发
>
> 头文件中加入 #pragma once 可以让该头文件在源文件中只被包含一次
>
> 双冒号 **==::==** 表示作用域，如 Piont :: setX() 表示 Point 作用域的 setX() 函数

### 4.2 对象的初始化和清理

#### 4.2.1 构造函数和析构函数

对象的**初始化和清理**是两个非常重要的安全问题

+ 一个对象或者变量没有初始化状态，对其使用的后果是未知
+ 使用完一个对象或变量，没有及时清理，也会造成一定的安全问题

C++利用**构造函数**和**析构函数**来解决上述问题，这两个函数会被编译器自动调用，完成对象的初始化和清理工作。

对象的初始化和清理工作是编译器强制要求我们实现的功能，当程序员不提供**构造和析构**时，**编译器会提供**。

**编译器提供的构造和析构函数是空实现。**

**概念：**

+ 构造函数：主要作用在于创建对象时为对象的成员赋值，构造函数由编译器自动调用，无需手动
+ 析构函数：主要作用在于对象**销毁前**系统自动调用，执行一些清理工作

**构造函数语法：**``类名(){}``

1. 没有返回值，也不写void
2. 函数名称与类名相同
3. 构造函数==可以有参数==，因此==可以发生重载==
4. 程序在==生成==对象时会==自动调用构造==，无需手动调用，且只会==调用一次==

**析构函数语法：**``~类名(){}``

1. 没有返回值，也不写void
2. 函数名称与类名相同，前加符号~
3. 析构函数==不可以有参数==，因此==不可以发生重载==
4. 程序在对象==销毁==前会==自动调用析构==，无需手动调用，且只会==调用一次==

```C++
class Person {
public:
	Person() { cout << "构造函数" << endl; }
	~Person() { cout << "析构函数" << endl; }
};

void test01() { Person p; }

int main() {
	test01();//同时打印“构造函数”和“析构函数”，因为test01的数据在栈区，调用完毕后会释放内存，故也会调用析构函数

	system("pause");
	return 0;
}
```

#### 4.2.2 构造函数的分类和调用

**两种分类方式：**

+ 按参数分为：有参构造 和 无参构造
+ 按类型分为：普通构造 和 拷贝构造

**三种调用方式：**

+ 括号法
+ 显示法
+ 隐式转换法

```C++
class Person {
public:
	Person() { cout << "无参构造函数" << endl; }
	Person(int a) {
		age = a;
		cout << "有参构造函数" << endl;
	}
	//拷贝构造函数传参必须是const+引用传递
	Person(const Person& p) {
		age = p.age;
		cout << "拷贝构造函数" << endl;
	}
	~Person() { cout << "析构函数" << endl; }
private:
	int age;
};

void test01() {
	Person p1;//无参（默认）构造
	//1.括号法调用
	Person p2(329);//有参构造
	Person p3(p2);//拷贝构造
	//2.显示法
	Person p4 = Person(1128);//有参构造
	Person p5 = Person(p4);//拷贝构造
	//3.隐式转换法
	Person p6 = 417;//有参构造，相当于 Person p6 = Person(417);
	Person p7 = p6;//拷贝构造，相当于 Person p7 = Person(p6);

}
int main() {
	test01();

	system("pause");
	return 0;
}
```

> 调用**默认构造函数**的时候，**不要加括号**，如 ``Person p1();`` 这会被编译器误认为是某个函数的声明
>
> 利用**显示法**调用构造函数时，如``Person p1 = Person(1128);`` 等式的右边被称为**匿名对象**
>
> **匿名对象**的特点是当前行执行结束后，系统会**立即回收**掉匿名对象
>
> 不要利用拷贝构造函数来初始化**匿名对象**，如``Person(p3);`` 编译器会认为这是一个对象声明即``Person p3;`` 导致**重定义**

#### 4.2.3 拷贝构造函数调用时机

+ 使用一个已经创建完毕的对象来初始化一个新对象
+ 值传递的方式给函数参数传参
+ 以值方式返回局部对象

```C++
class Person {
public:
	Person() { cout << "无参构造" << endl; }
	Person(int a) {
		age = a;
		cout << "有参构造" << endl;
	}
	Person(const Person& p) {
		age = p.age;
		cout << "拷贝构造" << endl;
	}
	~Person() { cout << "析构" << endl; }
private:
		int age;
};

void test00(Person p) {}

Person test01() {
	Person p;
	return p;
}

void test02() {
	//1.使用一个已经创建完毕的对象来初始化一个新对象
	Person p1(329);
	Person p2(p1);
	//2.值传递的方式给函数参数传参
	test00(p1);//值传递传入一个对象时，实现会拷贝一个副本生成形参，这个过程调用了拷贝构造函数
	//3.以值方式返回局部对象
	Person p3 = test01();//以值的方式返回时，会调用拷贝构造函数复制局部对象，生成一个新的对象作为返回值
}
int main() {
	test02();

	system("pause");
	return 0;
}
```

#### 4.2.4 构造函数的调用规则

默认情况下，C++编译器会至少给一个类添加三个函数：

+ 默认构造函数（无参，函数体为空）
+ 默认析构函数（无参，函数体为空）
+ 默认拷贝构造函数，对属性进行值拷贝

==**构造函数调用规则：**==

+ 如果用户定义了**有参构造函数**，C++不再提供默认无参构造，但会提供默认拷贝构造

  ```C++
  class Person {
  public:
  	Person(int a) {
  		age = a;
  		cout << "有参构造" << endl;
  	}
  private:
  	int age;
  };
  
  int main() {
  	Person p;//报错，当程序员提供了 有参构造函数 且没有 重载默认构造函数 时，C++不再提供 无参构造
  	Person p1(329);
  	Person p2(p1);//正常运行，C++虽然不提供 默认构造函数，但提供 默认拷贝函数
  
  	system("pause");
  	return 0;
  }
  ```

+ 如果用户定义了**拷贝构造函数**，C++不再提供其他构造函数

  ```C++
  class Person {
  public:
  	Person(const Person& p) {
  		age = p.age + 1;
  		cout << "拷贝构造" << endl;
  	}
  private:
  		int age;
  };
  
  int main() {
  	Person p;//报错，当程序员提供了 拷贝构造函数 时，C++不再提供 其他构造函数
  
  	system("pause");
  	return 0;
  }
  ```

#### 4.2.5 深拷贝和浅拷贝（重要）

**概念：**

+ 浅拷贝：简单的赋值拷贝操作
+ 深拷贝：在堆区重新申请空间，进行拷贝操作

```C++
class Person {
public:
	int age;
	int* id;//利用指针可以将数据开辟到堆区
	Person(int a, int b) {
		age = a;
		id = new int(b);//new 将这个数据开辟到堆区
		cout << "有参构造" << endl;
	}
	//假如不重写 拷贝构造函数，则在调用 析构函数 时会出现内存重复释放导致报错的问题，深层分析见后文
	Person(const Person& p) {
		age = p.age;
		//利用深拷贝解决浅拷贝的问题
		id = new int(*p.id);//重写 拷贝构造函数 的关键一步，不是拷贝 指针的地址，而是拷贝 指针所指的堆区内存的内容
		cout << "拷贝构造" << endl;
	}
	~Person() {
		if (id != NULL) {
			delete id;//释放id指针指向的堆区内存
			id = NULL;//将id这个指针置空
		}
		cout << "析构函数" << endl;
	}
};

void test01() {
	Person p1(1, 2);
	Person p2(p1);
	//1.C++默认的 拷贝构造函数 会逐字节复制成员属性的数据
	//2.因为Person类中的属性id是一个指针，且数据开辟到了堆区，故p1.id存储的是一个堆区地址，堆区数据需要程序员手动释放
	//3.又因为C++默认的 拷贝构造函数 是逐字节复制，所以p2.id会完全复制p1.id所存储的地址，这两个指针指向同一个堆区内存
	//4.调用 析构函数 时，栈区数据遵循先进后出的原则，故先析构p2，此时p2.id指向的堆区内存就会被释放掉
	//5.当编译器析构p1时，p1.id所指向的数据已经被p2释放掉了，此时会导致释放失败，程序报错！！！

	cout << "p2年龄:" << p2.age << endl << "p2号码:" << *p2.id << endl;
}

int main() {
	test01();

	system("pause");
	return 0;
}
```

> 如果属性有在堆区开辟的，一定要重写**拷贝构造函数**进行**深拷贝**，防止**浅拷贝**带来的问题

#### 4.2.6 初始化列表

**作用：**C++提供了初始化列表语法，用来初始化属性

**语法：**``构造函数(): 属性1(值1),属性2(值2)...{}``

```C++
class Person {
public:
	int a, b, c;
	Person(int a0, int b0, int c0) :a(a0), b(b0), c(c0) { }
};

int main() {
	Person p(1,2,3);

	cout << "a=" << p.a << endl;//a=1
	cout << "b=" << p.b << endl;//b=2
	cout << "c=" << p.c << endl;//c=3

	system("pause");
	return 0;
}
```

> 初始化语句发生在构造函数语句**前**，传统的构造方法需要一次**初始化**和一次**拷贝（赋值）**的开销
>
> 利用初始化列表可以减少这种开销

#### 4.2.7 类对象作为类成员

C++允许类中的成员是另一个类的对象，称该成员为**对象成员**

```C++
class Phone {
public:
	string brand;
	Phone(string b) :brand(b) { cout << "Phone的构造函数被调用" << endl; }
	~Phone() { cout << "Phone的析构函数被调用" << endl; }
};

class Person {
public:
	string name;
	Phone pBrand;
	//pBrand(b) 等价于 Phone pBrand = b; 或者 Phone pBrand(b)
	Person(string n, string b) :name(n), pBrand(b) { cout << "Person的构造函数的调用" << endl; }
	~Person() { cout << "Person的析构函数被调用" << endl; }
};

void test() {
	Person kallen("Kallen", "Code");
	cout << kallen.name << "\t" << kallen.pBrand.brand << endl;
}

int main() {
	test();//输出如下：
	//Phone的构造函数被调用
	//Person的构造函数被调用
	//Kallen Code
	//Phone的析构函数被调用
	//Person的析构函数被调用

	system("pause");
	return 0;
}
```

> 当其他类的对象作为本类成员，构造时**先构造成员对象**，再构造自身
>
> 析构的时候**先析构成员对象**，再析构自身

#### 4.2.8 静态成员

**定义：**静态成员就是在成员变量或者成员函数前加上关键字**static**

==**分类：**==

+ 静态成员变量

  + 所有对象共享同一份数据
  + 在编译阶段分配内存（放在全局区）
  + 类内声明，类外初始化

  ```C++
  class D {
  public:
  	static int a;//静态成员 类内声明
  private:
  	static int b;//静态变量也有作用域，故此静态变量在类外不可访问
  };
  
  int D::a = 1;//静态成员 类外初始化
  int D::b = 2;
  
  int main() {
  	cout << D::a << endl;//1，访问静态变量不需要创建对象，可直接通过类名访问
  	D d;
  	cout << d.a << endl;//1，静态变量也可以用对象访问
  	D d1;
  	d1.a = 329;
  	cout << d.a << endl;//329，所有对象共享一份数据
  	
  	system("pause");
  	return 0;
  }
  ```

+ 静态成员函数

  + 所有对象共用一个函数
  + 静态成员函数只能访问静态成员变量

  ```C++
  class D {
  public:
  	static int a;//静态成员
  	int b;//非静态成员
  	static void func() {
  		a = 2;//静态成员函数 可以访问 静态成员变量
  		b = 1;//报错，静态成员函数 禁止访问 非静态成员变量，因为它不知道这个变量属于哪个对象
  		cout << "访问静态成员函数" << endl;
  	}
  private:
  	static void func1() {  }//静态成员函数也有作用域，故该函数类外不可访问
  };
  
  int D::a = 1;//静态成员 类外初始化
  
  int main() {
  	D::func();//静态成员函数可以通过类名直接访问
  	D d;
  	d.func();//也可以通过对象访问
  	
  	system("pause");
  	return 0;
  }
  ```

### 4.3 C++模型和this指针

#### 4.3.1 成员变量和成员函数分开存储

```C++
class Person {  };

class Human {
	int id;
	static int a;
	void func1(){  }
	static void func2() {  }
};

int Human::a = 1;

void test01() {
	Person p;
	//C++编译器会给每个空对象也分配一个字节的空间，用以区分不同空对象所占的位置
	//即每个空对象都有一个独一无二的内存地址
	cout << "size of p = " << sizeof(p) << endl;//1
}

void test02() {
	Human h;
	//不是空对象，就无需特地分配1个字节的空间用以区分，对象内的非静态成员总共占多少内存，对象就占多少内存
	//同时，类内的所有静态成员都不占对象的内存，所有成员函数都不占对象的内存
	cout << "size of h = " << sizeof(h) << endl;//4，int占4个字节，静态变量和成员函数则不占用对象的空间
}

int main() {
	test01();
	test02();

	system("pause");
	return 0;
}
```

> 归根结底，**只有非静态成员变量**，属于类的对象上，即**成员变量和成员函数是分开存储的**

#### 4.3.1 this指针概念

**已知：**C++中成员变量和成员函数是分开存储的，每一个非静态成员函数==只会产生一份函数实例==，多个对象共用一份代码

**问题：**这一块代码如何区分哪个对象调用自己？

**解决：**C++通过特殊指针 ***this*** 解决上述问题。==***this*** 指针指向被调用的成员函数所属的对象==

+ ***this*** 指针隐含在每一个非静态成员函数内
+ ***this*** 指针不需要定义，可直接使用

***this*** **指针的用途：**

+ 当形参和成员变量同名的时候，可以用 ***this*** 指针来区分
+ 在类的非静态成员函数中返回对象本身，可以使用``return *this``

```C++
class Person {
public:
	int age;
	Person(int age) {
		this->age = age;
	}
	//返回值应写作 类的引用 的形式，否则就是值返回，会调用拷贝构造函数
	Person& AgeAdd(Person& p) {
		this->age += p.age;
		//this指向 被调用成员函数（在本例中是 AgeAdd() ）所属的对象
		//对 this 解引用可得对象本身
		return *this;
	}
};

void test01() {
	Person p(18);
	cout << p.age << endl;
}

void test02() {
	Person p1(10);
	Person p2(10);
	//AgeAdd()的返回值是p2的引用，所以仍可以继续通过 .AgeAdd() 调用成员函数
	//这就是 链式编程思想
	p2.AgeAdd(p1).AgeAdd(p1).AgeAdd(p1);
	cout << "p2的年龄：" << p2.age << endl;//40
	//如果 AgeAdd() 的返回值类型是 Person值返回， 那上述输出的p2.age就是20而不是40，分析如下
	//1.第一次调用 AgeAdd() 时，p1.age成功的加到了p2.age的身上，此时p2.age=20
	//2.第一次调用后返回时，因为是值返回，所以会调用拷贝构造函数复制一份和p2完全一致的新对象
	//3.所以后续所有的 AgeAdd() 操作不再是对p2进行操作
}

int main() {
	test01();
	test02();

	system("pause");
	return 0;
}
```

#### 4.3.3 空指针访问成员函数

C++中的空指针也可以调用成员函数，但要注意成员函数中有没有用到 ***this*** 指针

如果用到了 ***this*** 指针，需要加判断保证代码能正常运行

```C++
class Person {
public:
	int age;
	void showName() { cout << "Person" << endl; }
	void showAge() {
		//如果少了下面的if语句，则主函数中p->showAge(); 会报错
		if (this == NULL) {
			cout << "this = NULL" << endl;
			return;
		}
		cout << "age = " << age << endl;
	}//对象中所有成员的前面默认加了一个 this->
};

int main() {
	Person* p = NULL;
	p->showName();//正常
	p->showAge();//因为写了if判断this是否为空，所以可以正常运行

	system("pause");
	return 0;
}
```

#### 4.3.4 const修饰成员函数

**常函数：**

+ 成员函数**后**加const后我们称这个函数为**常函数** ``返回值类型 函数名() const {}``
+ 常函数内不可以修改成员属性
+ 成员属性在声明时加关键字**mutable**，在常函数中依然可以修改

**常对象：**

+ 声明对象加const称该对象为**常对象**
+ 常对象只能调用常函数

```C++
class Person {
public:
	int age;
	mutable int id;//mutable adj. 可变的
	static int num;
	void editAge()const {
		age = 12;//报错，常函数不可修改成员变量
		id = 11;//成员变量加了mutable关键字后，可以在常函数中被修改
	}
	void test0() { }
	static void test1() { }
};

int Person::num = 1;

int main() {
	const Person p;//常对象
	p.age = 100;//报错，常对象不能访问成员变量
	p.test0();//报错，常对象只能访问常函数

	p.id = 120;//正常，加了mutable关键字的属性可以被常对象访问
	p.editAge();//正常，常对象可以访问常函数
	p.num = 2;//正常，常对象可以访问 静态成员变量
	p.test1();//正常，常对象可以访问 静态成员函数

	system("pause");
    
	return 0;
}
```

> ***this*** 指针的本质是一个**指针常量**，它的指向是不可以修改的，当成员函数加了**const**后，***this*** 指针指向的内容也不可以修改
>
> 成员函数后加**const**，本质是用**const**去修饰 ***this*** 指针
>
> 常对象的本质是**不可修改成员变量**，所以所有可能会对成员变量造成修改的成员都不可以被常对象访问
>
> **静态成员变量，静态成员函数，常函数，以及加了mutable的成员变量**，常对象都可以访问

### 4.4 友元

当想让私有属性被类外一些特殊的函数或者类进行访问时，可以借助**友元**

**友元的目的：**让一个函数或者类可以访问另一个类中的私有成员

**关键字：**``friend``

**实现：**

+ 全局函数做友元
+ 类做友元
+ 成员函数做友元

#### 4.4.1 全局函数做友元

**语法：**``friend 返回值 函数名();//也就是friend+函数的声明``

```C++
class House {
	//全局函数做友元
	friend void testFriend(House& h);//friend + 全局函数的声明 -> 代表这个全局函数可以访问类的私有成员
public:
	string livingRoom;
	House() {
		livingRoom = "客厅";
		bedRoom = "房间";
	}
private:
	string bedRoom;
};

void testFriend(House& h) {
	cout << "朋友正在访问：" << h.livingRoom << endl;
	cout << "朋友正在访问：" << h.bedRoom << endl;
}

int main() {
	House h;
	testFriend(h);

	system("pause");
	return 0;
}
```

#### 4.4.2 类做友元

**语法：**``friend class 类名;``

```C++
class House;//类的前置声明
class Brother {
public:
	House* h;
	Brother();
	void visit();
};

class House {
	//类做友元
	friend class Brother;//friend class + 类名 -> 代表这个类可以访问本类的私有成员
public:
	string livingRoom;
	House() {
		livingRoom = "客厅";
		bedRoom = "房间";
	}
private:
	string bedRoom;
};

//类的成员函数也可以类内声明，类外实现，类外需要加作用域
Brother::Brother() {
	h = new House;//这一行必须在 House 类定义的后面，否则编译器会不认识 House
}

void Brother::visit() {
	cout << "朋友正在访问：" << h->livingRoom << endl;
	cout << "朋友正在访问：" << h->bedRoom << endl;
}

int main() {
	Brother b;
	b.visit();

	system("pause");
	return 0;
}
```

##### ==※类的前置声明注意事项==

+ 当前置声明的类**作为成员变量**时，只能以**指针**或**引用**的形式，不能定义该类的对象，因为编译器申请空间时，**对象形式的成员**变量**需要其定义**，而指针形式的成员变量需要的空间是固定的（引用的实现也基于指针）。
+ **不能使用前置声明类的实现**，如果想调用前置声明类的具体某个成员变量或者成员函数，一定要**包含它的头文件**。因为前置声明就只是一个声明并**不包含定义**。
+ **前置声明的类不能作为父类**

#### 4.4.3 成员函数做友元

``friend 返回值 类名::函数名();``

```C++
class House;//类的前置声明
class Brother {
public:
	House* h;
	Brother();
	void visit();
};

class House {
	//成员函数做友元
	friend void Brother::visit();//friend + 成员函数的声明 -> 成员函数可以访问该类的私有成员（记得加作用域）
public:
	string livingRoom;
	House() {
		livingRoom = "客厅";
		bedRoom = "房间";
	}
private:
	string bedRoom;
};

//类的成员函数也可以类内声明，类外实现，类外需要加作用域
Brother::Brother() {
	h = new House;//这一行必须在 House 类定义的后面，否则编译器会不认识 House
}

void Brother::visit() {
	cout << "朋友正在访问：" << h->livingRoom << endl;
	cout << "朋友正在访问：" << h->bedRoom << endl;
}


int main() {
	Brother b;
	b.visit();

	system("pause");
	return 0;
}
```

### 4.5 运算符重载（重要）

**概念：**对已有的运算符进行重定义，赋予其另一种功能，以适应不同的数据类型

#### 4.5.1 加号运算符重载

**作用：**实现两个自定义数据类型相加的运算

**方法：**利用C++编译器提供的``operator+``函数名来重载==加号运算符==

**方式：**

+ 利用类的**成员函数**重载==加号运算符==
+ 利用**全局函数**重载==加号运算符==

```C++
class Person {
public:
	int a;
	int b;
	//成员函数重载加号运算符
	Person operator+(const Person& p) {
		Person temp;
		temp.a = this->a + p.a;
		temp.b = this->b + p.b;
		return temp;//这里应该用值传递，调用拷贝构造函数创建一个新的对象
	}
};

//全局函数重载加号运算符
Person operator+(const Person& p1, const Person& p2) {
	Person temp;
	temp.a = p1.a + p2.a;
	temp.b = p1.b + p2.b;
	return temp;//如果传引用的话，会导致该函数结束后内存被释放，返回的对象也就不存在了
}

int main() {
	Person p1;
	p1.a = 1;
	p1.b = 2;
	Person p2;
	p2.a = 9;
	p2.b = 8;
	Person p3 = p1 + p2;//简化表达，实际上是：
	//全局函数重载：Person p3 = operator+(p1, p2);
	//成员函数重载：Person p3 = p1.operator+(p2);
	cout << "a=" << p3.a << endl << "b=" << p3.b << endl;

	system("pause");
	return 0;
}
```

> 运算符重载也可以发生函数重载
>
> 对于内置的数据类型的表达式的运算符规则不可改变（如两个int相加，不可改变）
>
> 不要滥用运算符重载（见名知意，加就是加，减就是减）

#### 4.5.2 左移运算符重载

**作用：**可以输出自定义的数据类型（类似Java的``toString()``）

**方法：**利用C++编译器提供的``operator<<``函数名来重载==左移运算符==

**方式：**利用**全局函数**重载==左移运算符==

```C++
//cout本质是一个<ostream>类型的对象
class Person {
	//重载运算符的函数一般都作为类的友元
	friend ostream& operator<<(ostream& cout, Person& p);
public:
	Person() {
		a = 1;
		b = 2;
	}
private:
	//假如要利用成员函数重载左移运算符，如 p.operator<<(cout)，简化之后变成 p << cout（参考operator+）
	//而我们实际上需要的是 cout << p，如果利用成员函数重载左移运算符，就会导致难以达到要求
	int a, b;
};

//只能利用全局函数重载左移运算符
//形参列表只有 cout 在左 需要输出的对象在右，才能简化为 cout << 对象
//cout 对象全局只能有一个，所以必须传引用
ostream& operator<<(ostream &cout, Person &p) {
	cout << "a=" << p.a << endl << "b=" << p.b << endl;
	return cout;//为了使 main 函数中 cout << p 之后能继续输出其他内容，则必须返回 cout 的引用
	//重载函数中的 cout 也可以改成其他的名字，因为其本质就是一个引用 
}

int main() {
	Person p1;
	cout << p1 << endl;//这也是体现了链式编程思想

	system("pause");
	return 0;
}
```

> 核心记住``operator<<``的简化方式，参考``operator+``

#### 4.5.3 递增运算符重载

**作用：**使得递增运算符适应自定义的数据类型

**方法：**利用C++编译器提供的``operator++``函数名来重载==递增运算符==

**方式：**利用**成员函数**重载==递增运算符==

```C++
class MyInteger {
	friend ostream& operator<<(ostream& cout, MyInteger myint);
public:
	MyInteger() { num = 0; }
	//重载前置++运算符，返回引用代表一直是对一个对象进行操作
	MyInteger& operator++() {
		num++;
		return *this;
	}
	//重载后置++运算符，在函数名中加入一个占位运算符 int，可以使得返回值类型不同的两个函数发生重载
	//占位运算符仅仅是为了区分两个重载函数，在此是可以区分前置递增和后置递增
	//C++语法规定，使用一个int作为占位参数的函数就是后置递增
	//后置递增的重载只能是 值返回 类型，因为不可以返回局部变量的引用
	MyInteger operator++(int) {
		MyInteger temp = *this;
		this->num++;
		return temp;
	}
private:
	int num;
};
//此种情况下，左移运算符重载函数传入的对象必须是 值传递 类型
//因为后置递增重载函数是 值返回，而且没有用一个新对象去接收
//所以返回的是一个 匿名对象，匿名对象不能作为引用传递
ostream& operator<<(ostream& cout, MyInteger myint) {
	cout << myint.num;
	return cout;
}

int main() {
	MyInteger myint; 
	cout << ++(++myint) << endl;//2，前置递增 可以链式编程，因为返回的是引用，后续操作依旧针对 myint
	cout << myint << endl;//2
	cout << (myint++)++ << endl;//2，后置递增 不能链式编程，因为返回的是值，后续操作不再是针对 myint
	cout << myint << endl;//3

	system("pause");
	return 0;
}
```

> **前置递增**返回类型是**引用**，且不需要传参；**后置递增**返回类型是**值**，且需要一个**占位参数*int***作为重载标识（C++规定）
>
> 正因为两者的返回值类型不同，所以前置递增可以链式编程，而后置递增不行
>
> 如果需要直接``cout << 某对象++;``，则左移运算符的重载函数中相应的形参必须是**值传递**
>
> 因为``某对象++``是匿名对象，而**匿名对象不能作为引用传递**

#### 4.5.4 赋值运算符重载

+ 除==**默认构造，析构，拷贝构造**==三个函数之外，C++还会添加一个默认的**赋值运算符 operator=**

+ 如果类中有属性指向堆区，做赋值操作时也会出现深拷贝问题

**作用：**对属性值进行拷贝

**方法：**利用C++编译器提供的``operator=``函数名来重载==赋值运算符==

**方式：**利用**成员函数**重载==赋值运算符==

```C++
class Person {
public:
	int* age;
	Person(int num) { age = new int(num); }
	~Person() {
		if (age != NULL) {
			delete age;
			age = NULL;
		}
	}
	//编译器提供的是浅拷贝
	Person& operator=(Person& p) {
		//先判断当前对象在堆区是否有属性，有就释放再深拷贝
		if (this->age != NULL) {
			delete this->age;
			this->age = NULL;
		}
		//深拷贝
		this->age = new int(*p.age);
		return *this;//返回本身以实现链式编程
	}
};

void test01() {
	Person p1(329);
	Person p2(1128);
	Person p3(417);
	p3 = p2 = p1;
	cout << "p1的年龄:" << *p1.age << endl;//329
	cout << "p2的年龄:" << *p2.age << endl;//329
	cout << "p3的年龄:" << *p3.age << endl;//329
}

int main() {
	test01();

	system("pause");
	return 0;
}
```

> 赋值运算符是从右往左运算的
>
> 实现链式编程思想的关键是函数需要**返回对象自身的引用**

#### 4.5.5 关系运算符重载

**作用：**重载关系运算符，可以让两个自定义类型对象进行比较

**方法：**利用C++编译器提供的``operator==``和``operator!=``函数名来重载==关系运算符==

**方式：**利用**成员函数**重载==关系运算符==

```C++
class Person {
public:
	int age;
	string name;
	Person(int a, string s) :age(a), name(s) {  }
	bool operator==(Person& p) {
		if (this->age == p.age && this->name == p.name) {
			return true;
		}
		return false;
	}
	bool operator!=(Person& p) {
		if (this->age != p.age || this->name != p.name) {
			return true;
		}
		return false;
	}
};

void test01() {
	Person p1(22, "Kallen");
	Person p2(22, "Kallen");

	if (p1 == p2) { cout << "同一个人" << endl; }
	else { cout << "不同人" << endl; }

	if (p1 != p2) { cout << "不同人！" << endl; }
	else { cout << "同一个人!" << endl; }
}
int main() {
	test01();

	system("pause");
	return 0;
}
```

#### 4.5.6 函数调用运算符重载

**作用：**函数调用运算符``()``也可以重载，重载后的使用方式非常像函数的调用，因此也称为==**仿函数**==

> 仿函数没有固定写法，非常灵活

**方法：**利用C++编译器提供的``operator()``函数名来重载==函数调用运算符==

**方式：**利用**成员函数**重载==函数调用运算符==

```C++
class MyPrint {
public:
	void operator()(string s) { cout << s << endl; }
};

class MyAdd {
public:
	int operator()(int a, int b) { return a + b; }
};

void test01() {
	MyPrint kallen;
	kallen("Kallen");//Kallen
	//外观酷似函数调用，实际上是 kallen 这个对象利用小括号访问了类内的 operator() 重载函数 

	MyAdd add;
	cout << add(329, 1128) << endl;//1457
	//不创建对象的调用仿函数方法：
	//其中 MyAdd() 是创建了一个匿名对象，后续的 (417, 329) 则是这个匿名对象调用仿函数的形参列表
	cout << MyAdd()(417, 329) << endl;//746
}
int main() {
	test01();

	system("pause");
	return 0;
}
```

> 函数调用是**函数名(形参列表)**，仿函数是**对象名(形参列表)**

### 4.6 继承

#### 4.6.1 继承的基本语法

**语法：**``class 子类名: 继承方式 父类名 { };``

**优点：**减少重复代码

```C++
class WarsShips {
public:
	void showName() { cout << "这是军舰:"; }
};

class Frigate : public WarsShips {
public:
	void showThis() { cout << "护卫舰" << endl; }
};

class Destroyer :public WarsShips {
public:
	void showThis() { cout << "驱逐舰" << endl; }
};

int main() {
	Frigate frigate;
	frigate.showName();
	frigate.showThis();
	Destroyer destroyers;
	destroyers.showName();
	destroyers.showThis();

	system("pause");
	return 0;
}
```

> 子类也称为派生类，父类也称为基类
>
> 子类中包含两大部分，一是基类继承过来的成员**（共性）**，二是自己专属的成员**（个性）**

#### 4.6.2 继承方式

+ 公共继承：私有成员不可访问，其余成员的作用域不变
+ 保护继承：私有成员不可访问，其余成员的作用域变为 ***protected***
+ 私有继承：私有成员不可访问，其余成员的作用域变为 ***private***

#### 4.6.3 继承中的对象模型

+ 父类中的**所有非静态成员属性**都会被子类继承下去
+ 虽然子类无法访问父类的私有成员，但**私有成员也会被继承**，只是被编译器隐藏了

```C++
class Parent {
	friend class Child1;
public:
	int a;
protected:
	int b;
private:
	int c;//虽然子类无法访问父类的私有成员，但是私有成员仍会被继承
};

class Child1 :public Parent {
public:
	int d;
};

int main() {
	Child1 c;
	cout << "size of c:" << sizeof(c) << endl;//16，4个int，每个4

	system("pause");
	return 0;
}
```

> 可以利用**VS开发者命令提示符**中的``cl /d1 reportSingleClassLayout类名 文件名.cpp``来查看类的布局

#### 4.6.4 继承中的构造和析构顺序

+ 子类继承父类后，当创建子类对象时，也会调用父类的构造函数
  + 先调用**父类的构造函数**
  + 再调用**子类的构造函数**
+ 析构对象时，也会调用父类的析构函数
  + 先调用**子类的析构函数**
  + 再调用**父类的析构函数**

#### 4.6.5 继承同名成员的处理方式

+ 访问**子类**同名成员，**直接访问**即可
+ 访问**父类**同名成员，需要加**作用域**

```C++
class Parent {
public:
	int a;
	void printA() { cout << "Parent:" << a << endl; }
	void printA(int a) { cout << "Override function!" << endl; }
};

class Child :public Parent {
public:
	int a;
	void printA() { cout << "Child:" << a << endl; }
};

int main() {
	Child c;
	c.a = 1;
	c.Parent::a = 2;
	c.printA();//1
	c.Parent::printA();//2
	cout << "size of c:" << sizeof(c) << endl;//8，因为有两个int，同名
	c.printA(1);//报错，当子类和父类中有函数重名，且在父类中该函数发生了重载，则不加作用域时无法调用该重载函数
	c.Parent::printA(1);//正确，父类的同名重载函数需要加作用域

	system("pause");
	return 0;
}
```

> 当子类出现和父类同名的成员函数后，子类会隐藏父类中所有的同名成员函数（重载）

#### 4.6.6 继承同名静态成员的处理方式

+ 访问**子类**同名成员，**直接访问**即可
+ 访问**父类**同名成员，需要加**作用域**

```C++
class Parent {
public:
	static int a;
	static void func() { cout << ++a << "-父类的静态函数" << endl; }
};

class Child :public Parent {
public:
	static int a;
	static void func() { cout << ++a << "-子类的静态函数-" << ++Parent::a << endl; }
};

int Parent::a = 1;
int Child::a = 10;

int main() {
	//1.通过对象访问静态成员
	Child c;
	cout << "对象名访问子类的静态变量：" << c.a << endl;
	cout << "对象名访问父类的静态变量：" << c.Parent::a << endl;
	c.func();
	c.Parent::func();

	//2.通过类名访问静态成员
	cout << "类名访问子类的静态变量：" << Child::a << endl;
	cout << "类名访问子类的静态变量：" << Child::Parent::a << endl;//第一个双冒号表示Child作用域下，第二个表示Child作用域下的 Parent::a
	Child::func();
	Child::Parent::func();

	system("pause");
	return 0;
}
```

> 子类的静态成员函数可以访问父类的静态成员变量

#### 4.6.7 多继承语法

C++允许一个类继承多个类

**语法：**``class 子类: 继承方式 父类1, 继承方式 父类2...``

```C++
class WangSitu {
public:
	string name;
	WangSitu() {
		name = "王司徒";
		cout << name << endl;
	}
	~WangSitu() { cout << "王司徒析构" << endl; }
};

class DongZhuo {
public:
	string name;
	DongZhuo() {
		name = "董卓"; 
		cout << name << endl;
	}
	~DongZhuo() { cout << "董卓析构" << endl; }
};

class LvBu : public DongZhuo, public WangSitu {
public:
	string name;
	LvBu() {
		name = "吕布";
		cout << name << endl;
	}
	~LvBu() { cout << "吕布析构" << endl; }
};

void test() {
	LvBu lvbu;
	cout << "名字:" << lvbu.name << endl;
	cout << "父亲1的名字：" << lvbu.DongZhuo::name << endl;
	cout << "父亲2的名字：" << lvbu.WangSitu::name << endl;
}

int main() {
	test();

	system("pause");
	return 0;
}
```

> 多继承可能会出现同名成员的出现，需要加作用域来区分
>
> 构造顺序按照继承语法中父类名字出现的顺序，最后是子类，析构则按照先进后出的规则

#### 4.6.8 菱形继承

**概念：**

+ 两个派生类继承自同一个基类
+ 某个类又同时继承这两个派生类

```C++
//菱形继承典型案例：
//羊类和驼类都继承自动物类，动物类有一个成员变量是年龄
//草泥马类多继承自羊类和驼类，羊类和驼类都有一个成员变量年龄继承给了草泥马类
//但草泥马类只需要一个年龄
class Animal {
public:
	int age;
};
//本例中，Alpaca继承了两个父类的vbptr
//Sheep的vbptr和Camel的vbptr指向的内存空间相同，所以Alpaca中只有一份age
class Sheep :virtual public Animal {};
class Camel :virtual public Animal {};
class Alpaca :public Sheep, public Camel {};

int main() {	
	Alpaca a;
	//不用虚继承时，此时类内有两个age，需要加作用域区分
	//使用虚继承后，可以有多重访问方式，age的数据只有一份
	a.Sheep::age = 100;
	a.Camel::age = 80;
	cout << a.age << endl;

	system("pause");
	return 0;
}
```

> 继承语句前加 ***virtual*** 关键字可以将继承方式变为**虚继承**，此时**基类**也就变成了**虚基类**
>
> 派生类继承的内容也**不再只是**基类的成员，会多包涵一个 ***vbptr(virtual base pointer)，虚基类指针***，占4个字节的内存空间
>
> ***vbptr*** 指向的是 ***vbtable(virtual base table)，虚基类表***
>
> ***vbtable*** 记录了一个数据，是**当前类到虚基类**的**地址偏移量**，通过偏移地址，这样就找到了虚基类成员
>
> 虚基类依旧会在子类里面存在拷贝，只是**仅仅最多存在一份**而已
>
> 当虚继承的子类被当做父类继承时，虚基类指针也会被继承
>
> 而虚继承也不用像普通多继承那样维持着公共基类（虚基类）的两份同样的拷贝，节省了存储空间

### 4.7 多态

#### 4.7.1 多态的基本概念

多态分为两类：

+ 静态多态：**函数重载**和**运算符重载**属于静态多态，复用函数名

+ 动态多态：**派生类**和**虚函数**实现运行时多态

二者的区别：

+ 静态多态的函数地址**早绑定**：编译阶段确定函数地址
+ 动态多态的函数地址**晚绑定**：运行阶段确定函数地址

```C++
//早绑定
class Animal {
public:
	void speak() { cout << "动物在说话" << endl; }
};

class Cat :public Animal {
public:
	void speak() { cout << "古德猫宁" << endl; }
};

//C++允许父类的引用或者指针可以直接指向子类的对象，反之不行
void doSpeak(Animal& animal) {
	animal.speak();//地址早就确定好了，所以不管传什么子类对象进来都是走Animal::doSpeak
}

int main() {
	Cat cat;
	doSpeak(cat);//动物在说话
	//因为doSpeak函数是早绑定，在编译阶段就确定了函数的地址
	//如果想让猫说话，那就让地址晚绑定

	system("pause");
	return 0;
}
```

```C++
//晚绑定
class Animal {
public:
	//加 virtual 关键字使得父类中的speak函数变为虚函数
	virtual void speak() { cout << "动物在说话" << endl; }
};

class Cat :public Animal {
public:
	void speak() { cout << "古德猫宁" << endl; }
};

void doSpeak(Animal& animal) {
	animal.speak();
}

int main() {
	Cat cat;
	doSpeak(cat);//古德猫宁

	system("pause");
	return 0;
}
```

> 实现动态多态的条件：1.有**继承**关系；2.子类要**重写**父类的**虚函数**
>
> 动态多态的使用：父类的**指针**或者**引用**指向子类的**对象**
>
> 重载：函数名和返回值类型相同，形参列表不同（也有返回值不同，函数名相同，但是使用了占位符的重载类型）
>
> 重写：函数名、返回值类型和形参列表完全相同

#### 4.7.2 多态底层原理（重要）

1. 父类内的成员函数加了 ***virtual*** 关键字后，会生成一个 ***vfptr (virtual function pointer) 虚函数指针***
2. ***vfptr*** 指向的是 ***vftable (virtual function table) 虚函数表***
3. ***vftable*** 内部记录的是**虚函数**的地址，记录：``&父类作用域::虚函数名``
4. 子类继承也会一并继承 ***vfptr***，且 ***vfptr*** 的指向不变
5. 当子类重写父类的虚函数时，子类的 ***vfptr*** 指向的 ***vftable*** 的内容会替换成子类的虚函数地址，``&子类作用域::虚函数名``
6. 当父类的指针或者是引用指向子类对象的时候，就发生了多态
7. 此时调用的虚函数就不再是父类的虚函数，而是子类的虚函数

#### 4.7.3 多态案例一：计算器类

```C++
//利用普通方法和多态的方法，实现一个计算器：
//1.普通方法：
//普通方法的问题在于，后期的拓展还需要修改源码，发现问题也要在源码中修改
class Calculator {
public:
	int num1;
	int num2;
	int calculate(string oper) {
		//项目最初只提供了3个运算关系
		if (oper == "+") { return num1 + num2; }
		else if (oper == "-") { return num1 - num2; }
		else if (oper == "*") { return num1 * num2; }
		//如果想要拓展功能，如除法，开方等等，都需要修改源码
	}
};

void test01() {
	Calculator c1;
	c1.num1 = 10;
	c1.num2 = 20;
	cout << c1.num1 << "+" << c1.num2 << "=" << c1.calculate("+") << endl;
	cout << c1.num1 << "-" << c1.num2 << "=" << c1.calculate("-") << endl;
	cout << c1.num1 << "*" << c1.num2 << "=" << c1.calculate("*") << endl;
}

int main() {
	test01();

	system("pause");
	return 0;
}
```

```C++
//2.多态实现
//实现计算器抽象类
class AbstractCalculator {
public:
	int num1;
	int num2;
	virtual int calculator() {
		return 0;
	}
};

class Add :public AbstractCalculator {
public:
	int calculator() {
		return num1 + num2;
	}
};

class Minus :public AbstractCalculator {
public:
	int calculator() {
		return num1 - num2;
	}
};
//如果想要实现其他功能，如乘法和除法等，不再需要修改源码，只需要从抽象的计算器类中继承并写一个新的类即可

int calculate(AbstractCalculator& cal) {
	return cal.calculator();
}

void test01() {
	//1.用父类的引用去接收子类对象
	Add a;
	a.num1 = 1;
	a.num2 = 1;
	Minus m;
	m.num1 = 17;
	m.num2 = 7;
	cout << "引用接收实现add:" << calculate(a) << endl;
	cout << "引用接收实现minus:" << calculate(m) << endl;

	//2.用父类的指针去接收子类对象
	AbstractCalculator* bptr = new Add;
	bptr->num1 = 2;
	bptr->num2 = 8;
	cout << "指针接收实现add:" << bptr->calculator() << endl;
	delete bptr;
	bptr = new Minus;
	bptr->num1 = 100;
	bptr->num2 = 66;
	cout << "指针接收实现minus:" << bptr->calculator() << endl;
}

int main() {
	test01();

	system("pause");
	return 0;
}
```

>  多态的好处：**1.组织结构清晰；2.可读性强；3.后期的扩展和维护性高**
>
> C++的设计过程中尽量遵循**开闭原则**：对扩展进行开放，对修改进行关闭
>
> C++提倡多使用**多态**

#### 4.7.4 纯虚函数和抽象类

通常来说父类中的**虚函数**的实现是毫无意义的，主要都是调用子类的**虚函数重写**的内容，因为可以将虚函数改为**纯虚函数**

**语法：**``virtual 返回值类型 函数名(参数列表) = 0;``

**抽象类：**当类中有了纯虚函数，这个类也被称为==抽象类==

**抽象类特点：**

+ 无法实例化对象
+ 子类必须重写抽象类中的纯虚函数，否则也属于抽象类

```C++
//只要有一个纯虚函数，这个类就是抽象类
class Base {
public:
	//纯虚函数
	virtual void func() = 0;
};

class Child1 :public Base {
public:
	void func() { cout << "重写纯虚函数" << endl; }
};

class Child2 :public Base {};

int main() {
	Base b;//报错，抽象类无法实例化
	Child2 c2;//报错，抽象类的子类如果不重写纯虚函数，那也是个抽象类
	Child1 c1;//重写纯虚函数后可以实例化

	system("pause");
	return 0;
}
```

#### 4.7.5 多态案例二：制作饮品

```C++
/*
1.制作饮品的大致流程为：煮水，冲泡，倒入杯中，加入辅料
2.利用多态技术实现，提供抽象制作饮品基类，提供子类制作咖啡和茶叶
3.咖啡流程：煮水，冲泡咖啡，倒入杯中，加糖和牛奶
4.茶叶流程：煮水，冲泡茶叶，倒入杯中，加柠檬
*/
//制作饮品抽象类
class Drinks {
public:
	void boilWater() { cout << "1.煮水" << endl; }
	virtual void makeDrink() = 0;
	void pour() { cout << "3.倒入杯中" << endl; }
	virtual void add() = 0;
	void process() {
		boilWater();
		makeDrink();
		pour();
		add();
	}
};

class Coffee :public Drinks {
public:
	void makeDrink() { cout << "2.冲泡咖啡" << endl; }
	void add() { cout << "4.加糖和牛奶" << endl; }
};

class Tea :public Drinks {
public:
	void makeDrink() { cout << "2.冲泡茶叶" << endl; }
	void add() { cout << "4.加柠檬" << endl; }
};

void doWork(Drinks* abs) {
	abs->process();
	delete abs;
}

int main() {
	doWork(new Coffee);
	doWork(new Tea);

	system("pause");
	return 0;
}
```

#### 4.7.6 虚析构和纯虚析构

**问题：**多态使用时，如果子类中有属性开辟到了堆区，则父类指针在释放时无法调用到子类的析构代码

**解决：**将父类中的析构函数改为**虚析构**后者**纯虚析构**

**共性：**

+ 可以使得父类指针释放子类对象
+ 都需要有聚类的函数实现

**区别：**如果是纯虚析构，则该类属于抽象类，无法实例化对象

**虚析构语法：**``virtual ~类名(){ }``

**纯虚析构语法：**``virtual ~类名() = 0;``

```C++
class Animal {
public:
	Animal() { cout << "Animal构造函数" << endl; }
	~Animal() { cout << "Animal析构函数" << endl; }
	virtual void speak() = 0;
};

class Cat :public Animal {
public:
	Cat(string s) {
		cout << "Cat构造函数" << endl;
		name = new string(s);
	}
	~Cat() {
		if (name != NULL) {
			cout << "Cat的析构函数" << endl;
			delete name;
			name = NULL;
		}
	}
	void speak() { cout << *name << ":古德猫宁" << endl; }
	string* name;
};

void test01() {
	Animal* animal = new Cat("Tom");
	animal->speak();
	delete animal;
	//1.父类指针在析构的时候不会调用子类中的析构函数
	//2.如果子类有堆区数据，则会导致内存泄露
}
int main() {
    //子类不会调用析构函数
	test01();//输出如下：
	//Animal构造函数
	//Cat构造函数
	//Tom : 古德猫宁
	//Animal析构函数
    
	system("pause");
	return 0;
}
```

```C++
//上述问题的解决方法1：父类析构函数改为虚析构，加入virtual关键字
class Animal {
public:
	Animal() { cout << "Animal构造函数" << endl; }
	virtual ~Animal() { cout << "Animal虚析构函数" << endl; }
	virtual void speak() = 0;
};

class Cat :public Animal {
public:
	Cat(string s) {
		cout << "Cat构造函数" << endl;
		name = new string(s);
	}
	~Cat() {
		if (name != NULL) {
			cout << "Cat的析构函数" << endl;
			delete name;
			name = NULL;
		}
	}
	void speak() { cout << *name << ":古德猫宁" << endl; }
	string* name;
};

void test01() {
	Animal* animal = new Cat("Tom");
	animal->speak();
	delete animal;//delete 父类指针本质是调用父类的析构函数
}
int main() {
	test01();//输出如下：
	//Animal构造函数
	//Cat构造函数
	//Tom : 古德猫宁
	//Cat的析构函数
	//Animal虚析构函数

	system("pause");
	return 0;
}
```

```C++
//上述问题的解决方法2：父类析构函数改为纯虚析构，且在类外需要写这个纯虚析构的实现
class Animal {
public:
	Animal() { cout << "Animal构造函数" << endl; }
	virtual ~Animal() = 0;//需要声明
	virtual void speak() = 0;
};

Animal::~Animal() { cout << "Animal纯虚析构函数" << endl; }//需要实现

class Cat :public Animal {
public:
	Cat(string s) {
		cout << "Cat构造函数" << endl;
		name = new string(s);
	}
	~Cat() {
		if (name != NULL) {
			cout << "Cat的析构函数" << endl;
			delete name;
			name = NULL;
		}
	}
	void speak() { cout << *name << ":古德猫宁" << endl; }
	string* name;
};

void test01() {
	Animal* animal = new Cat("Tom");
	animal->speak();
	delete animal;//delete 父类指针本质是调用父类的析构函数
}
int main() {
	test01();//输出如下：
	//Animal构造函数
	//Cat构造函数
	//Tom : 古德猫宁
	//Cat的析构函数
	//Animal纯虚析构函数

	system("pause");
	return 0;
}
```

> ``delete 类的指针``本质是调用该类的**析构**函数
>
> 如果这个指针指向子类对象，且**子类数据开辟在堆区**，且父类没有使用**虚析构或者纯虚析构**，则会导致子类的析构函数不会被调用
>
> 父类使用**虚析构或者纯虚析构**可以解决这个问题（4.7.5的案例也出现了这个问题）
>
> 子类没有堆区数据则可以不写**虚析构或者纯虚析构**
>
> 拥有**纯虚析构**的类也属于抽象类

#### 4.7.7 多态案例三：电脑组装

```C++
/*
电脑主要组成部件为CPU（用于计算），GPU（用于显示），内存条（用于存储）
将每个零件封装出抽象基类，并且提供不同厂商生产不同的零件，例如Intel和AMD
创建电脑类提供让电脑工作的函数，并且调用每个零件工作的接口
测试时组装三台不同的电脑进行工作
*/
//CPU抽象类
class AbsCPU {
public:
	virtual void calculator() = 0;
	virtual ~AbsCPU() { }//必须写虚析构
};
//Intel的CPU
class IntelCPU :public AbsCPU {
public:
	void calculator() { cout << "Intel的CPU工作中" << endl; }
};
//AMD的CPU
class AMDCPU :public AbsCPU {
public:
	void calculator() { cout << "AMD的CPU工作中" << endl; }
};
//GPU抽象类
class AbsGPU {
public:
	virtual void display() = 0;
	virtual ~AbsGPU() {}
};
//Intel的GPU
class IntelGPU :public AbsGPU{
public:
	void display() { cout << "Intel的GPU工作中" << endl; }
};
//AMD的GPU
class AMDGPU :public AbsGPU {
public:
	void display() { cout << "AMD的GPU工作中" << endl; }
};
//Memory抽象类
class AbsMemory {
public:
	virtual void storage() = 0;
	virtual ~AbsMemory(){}
};
//Intel的Memory
class IntelMemory :public AbsMemory {
public:
	void storage() { cout << "Intel的Memory工作中" << endl; }
};
//AMD的Memory
class AMDMemory :public AbsMemory {
public:
	void storage() { cout << "AMD的Memory工作中" << endl; }
};

class Computer {
public:
	AbsCPU* cpu;
	AbsGPU* gpu;
	AbsMemory* memory;
	Computer(AbsCPU* c, AbsGPU* g, AbsMemory* m) {
		cpu = c;
		gpu = g;
		memory = m;
	}
	void Work() {
		cpu->calculator();
		gpu->display();
		memory->storage();
	}
	~Computer() {
		if (cpu != NULL) {
			delete cpu;
			cpu = NULL;
		}
		if (gpu != NULL) {
			delete gpu;
			gpu = NULL;
		}
		if (memory != NULL) {
			delete memory;
			memory = NULL;
		}
	}
};

void test01() {
	Computer* c1 = new Computer(new IntelCPU, new IntelGPU, new IntelMemory);
	Computer* c2 = new Computer(new AMDCPU, new AMDGPU, new AMDMemory);
	Computer* c3 = new Computer(new AMDCPU, new IntelGPU, new AMDMemory);
	c1->Work();
	delete c1;
	c1 = NULL;
	cout << "-------------------" << endl;
	c2->Work();
	delete c2;
	c2 = NULL;
	cout << "-------------------" << endl;
	c3->Work();
	delete c3;
	c3 = NULL;
}

int main() {
	test01();

	system("pause");
	return 0;
}
```

> Computer类中的三个指针是父类指针，且指向子类对象，且子类对象开辟在堆区，所以必须在父类中写虚析构

## 5 文件操作

C++中对文件操作需要包含头文件``<fstream>``

文件类型主要分为两种：

+ **文本文件：**文件以文本的**ASCII码**的形式存储在计算机中
+ **二进制文件：**文件以文本的**二进制**形式存储在计算机中

操作文件的三大类：

+ ``ofstream`` 写操作
+ ``ifstream`` 读操作
+ ``fstream`` 读写操作

### 5.1 文本文件

#### 5.1.1 写文件

**步骤：**

1. ==包含头文件==``#include <fstream>``
2. ==创建流对象==``ofstream ofs``
3. ==打开文件==``ofs.open("文件路径", 打开方式)``
4. ==写数据==``ofs << "写入的数据"``
5. ==关闭文件==``ofs.close()``

**文件打开方式：**

| 关键词          | 打开方式                     |
| --------------- | ---------------------------- |
| ``ios::in``     | 为了**读文件**而打开文件     |
| ``ios::out``    | 为了**写文件**而打开文件     |
| ``ios::ate``    | 初始位置：文件尾             |
| ``ios::app``    | 追加方式写文件               |
| ``ios::trunc``  | 如果文件存在则先删除，再创建 |
| ``ios::binary`` | **二进制**方式               |

> 文件打开方式可以配合使用，利用 ``|`` 操作符，例如``ios::binary | ios::out``

```C++
#include <iostream>
#include <fstream>//记得头文件
using namespace std;

int main() {
	ofstream ofs;
	//直接写文件名会创建在.cpp所在的目录下
	ofs.open("fstream.txt", ios::out);
	ofs << "C.C.";
	ofs.close();

	system("pause");
	return 0;
}
```

#### 5.1.2 读文件

**步骤：**

1. ==包含头文件==``#include <fstream>``
2. ==创建流对象==``ifstream ifs``
3. ==打开文件并判断是否打开成功==``ifs.open("文件路径", 打开方式)``
4. ==读数据==``有四种方式，详见下方代码块``
5. ==关闭文件==``ifs.close()``

```C++
int main() {
	fstream ifs1;
	ifs1.open("fstream.txt", ios::in);
	if (!ifs1.is_open()) {
		cout << "打开失败" << endl;
		return 0;
	}
	//读数据有四种方法
	//1.利用char数组配合右移读取
	char buf1[1024] = { 0 };
	//当ifs右移后没有接受到任何数据时，ifs >> buf会返回一个false
	while (ifs1 >> buf1) {
		cout << buf1 << endl;
	}

	//2.利用char数组配合.getline 函数读取
	fstream ifs2;
	ifs2.open("fstream.txt", ios::in);
	char buf2[1024] = { 0 };
	while (ifs2.getline(buf2, sizeof(buf2))) {
		cout << buf2 << endl;
	}

	//3.利用string来读取——————————>最推荐用这种
	fstream ifs3;
	ifs3.open("fstream.txt", ios::in);
	string buf3;
	while (getline(ifs3, buf3)) {
		cout << buf3 << endl;
	}

	//4.利用EOF(end of file)的判断来读取
	fstream ifs4;
	ifs4.open("fstream.txt", ios::in);
	char c;
	while ((c = ifs4.get()) != EOF) {
		cout << c;
	}

	ifs1.close();
	ifs2.close();
	ifs3.close();
	ifs4.close();

	system("pause");
	return 0;
}
```

### 5.2 二进制文件

打开方式要指定为``ios::binary``

#### 5.2.1 写文件

二进制写文件主要利用**流对象**调用**成员函数write**

**函数原型：**``ostream& write(const char * buffer, int len);``

**参数解释：**字符指针 ***buffer*** 指向内存中一段存储空间，***len*** 是读写的字节数

```C++
class Person {
public:
	int age;
	char name[64];
};

int main() {
	fstream ofs("fstream.txt", ios::out | ios::binary);
	//上述代码等同于：
	//fstream ofs;
	//ofs.open("fstream.txt", ios::out | ios::binary);
	Person p = { 22, "Kallen" };//类也可以用结构体赋值的方式进行赋值
	ofs.write((const char*)&p, sizeof(Person));
	ofs.close();

	system("pause");
	return 0;
}
```

#### 5.2.2 读文件

二进制写文件主要利用**流对象**调用**成员函数read**

**函数原型：**``ostream& read(char * buffer, int len);``

**参数解释：**字符指针 ***buffer*** 指向内存中一段存储空间，***len*** 是读写的字节数

```C++
class Person {
public:
	int age;
	char name[64];
};

int main() {
	fstream ifs("fstream.txt", ios::in | ios::binary);
	Person p;//创建这个对象来接受读到的内容

	ifs.read((char*)&p, sizeof(Person));
	cout << p.name << ":" << p.age << endl;
	ifs.close();

	system("pause");
	return 0;
}
```

