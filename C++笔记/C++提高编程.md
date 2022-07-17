# C++提高编程

本阶段主要针对C++==泛型编程==和==STL(Standard Templates Library)==

## 1 模板

### 1.1 模板的概念

**概念：**模板就是简历==通用的模具==，大大提高==复用性== 

**特点：**

+ 不可以直接使用，它只是一个框架
+ 模板的通用不是万能的 

**应用：**C++另一种编程思想就是==泛型编程==，主要利用的就是模板，模板又包括==函数模板==和==类模板==

> 学习模板不是为了写模板，而是为了在STL能使用系统提供的模板

### 1.2 函数模板

#### 1.2.1 函数模板语法

**作用：**建立一个通用函数，其==函数的返回值类型==和==形参类型==可以不具体指定，用一个==虚拟的类型==来代表

**语法：**

```C++
template<typename T>
下方紧接函数的声明或定义
```

**解释：**

+ template	声明创建模板
+ typename	表明其后面的符号是一种数据类型，可以用 ==class== 代替
+ T		通用的数据类型，名称可以替换，通常为大写字母

**使用：**

+ 自动类型推导，编译器根据传参的类型进行推导
+ 显示指定类型，利用’<数据类型>‘来指定T的数据类型``模板函数名<数据类型>(形参列表)``

```C++
//下面两个函数除了形参的类型不一致，逻辑是一致的
//如果需要交换更多数据类型的值，则需要更多的函数
//利用 函数模板 可以大大提高复用性
void swapInt(int& a, int& b) {
	int temp = a;
	a = b;
	b = temp;
}
void swapDouble(double& a, double& b) {
	double temp = a;
	a = b;
	b = temp;
}
//声明一个模板+
template<typename T>
void mySwap(T& a, T& b) {
	T temp = a;
	a = b;
	b = temp;
}

int main() {
	//使用模板有两种方法：
	//1.自动类型推导：
	int a = 329;
	int b = 1128;
	mySwap(a, b);//编译器会将T认为是int类型
	cout << a << "+" << b << endl;
	//2.显示指定类型
	mySwap<int>(a, b);//函数调用时用<数据类型>来进行类型指定
	cout << a << "+" << b << endl;

	system("pause");
	return 0;
}
```

> 模板的本质就是将数据类型也参数化

#### 1.2.2 函数模板的注意事项

+ 自动类型推导，必须推导出一致的数据类型才可以使用
+ 模板必须要确定出T的数据类型才可以使用.

```C++
template<typename T>
void func() {
	cout << "test" << endl;
}

int main() {
	func();//报错！编译器不知道该模板函数应该使用什么数据类型
	func<int>();//正确！

	system("pause");
	return 0;
}
```

#### 1.2.3 函数模板案例

```C++
//排序模板
template<typename T>
void bubbleSort(T * array, int size) {
	for (int i = 0; i < size - 1; i++) {
		for (int j = 0; j < size - 1 - i; j++) {
			if (array[j] < array[j + 1]) {
				T temp = array[j];
				array[j] = array[j + 1];
				array[j + 1] = temp;
			}
		}
	}
}

//打印模板
template<typename T>
void print(T* array, int size) {
	for (int i = 0; i < size; i++) {
		cout << array[i] << " ";
	}
}

void testInt() {
	int array[10] = { 6,5,4,9,0,2,1,3,8,7 };
	int size = sizeof(array) / sizeof(array[0]);
	bubbleSort(array, size);
	//输出
	print(array, size);
}

void testChar() {
	char array[] = "edacb";
	int size = sizeof(array) / sizeof(array[0]);
	bubbleSort(array, size);
	//输出
	print(array, size);
}

int main() {
	testInt();
	cout << endl;
	testChar();

	system("pause");
	return 0;
}
```

#### 1.2.4 普通函数和函数模板的区别

+ 普通函数调用时，可以发生自动类型转换（隐式类型转换）
+ 函数模板调用时，如果利用了自动类型推导，则不会发生隐式类型转换
+ 如果利用显示指定类型，则可以发生自动类型转换

```C++
int add(int a, int b) {
	return a + b;
}

template<typename T>
T aDD(T a, T b) {
	return a + b;
}

int main() {
	int a = 10;
	char b = 'b';
	cout << add(a, b) << endl;//此处就发生了形参的隐式转换，char类型转换成int类型
	cout << aDD<int>(a, b) << endl;//利用显示指定类型，则函数模板可以发生隐式类型转换
	//cout << minus(a, b) << endl;//报错！如果需要编译器自动推导类型，则无法发生隐式类型转换

	system("pause");
	return 0;
}
```

#### 1.2.5 普通函数与函数模板的调用规则

+ 如果普通函数和函数模板都可以实现，**优先调用普通函数**
+ 可以通过空模板参数列表来强制调用函数模板
+ 函数模板也可以发生**重载**
+ 如果函数模板可以产生更好的匹配，优先调用函数模板

```C++
void add(int a, int b) {
	cout << "普通函数" << endl;
}

template<typename T>
void add(T a, T b) {
	cout << "函数模板" << endl;
}

template<typename T>
void add(T a) {
	cout << "函数模板的重载" << endl;
}

int main() {
	int a = 1;
	int b = 2;
	add(a, b);//优先调用普通函数
	add<>(a, b);//利用空模板参数强制调用函数模板
	add(a);//函数模板的重载
	char c1 = 'a';
	char c2 = 'b';
	add(c1, c2);//虽然这个表达式可以走普通函数也可以走函数模板，但走普通函数要发生隐式类型转换，编译器会认为走函数模板更匹配

	system("pause");
	return 0;
}
```

#### 1.2.6 模板的局限性

+ 模板中对数据的操作不一定对所有数据类型奏效

```C++
template<typename T>
void add(T a, T b) {
	a = b;
}
//该函数模板无法对数组进行操作
```

```C++
template<typename T>
void add(T a, T b) {
	if(a > b){...};
}
//该函数模板无法对自定义数据类型进行操作
```

> 为了解决上述的问题，C++提供了模板的**重载**，可以为**特定的类型**提供**具体化的模板**
>
> 对模板进行**特例化重载**时，必须为每个模板参数都**指定类型**

```C++
class Person {
public:
	int age;
	string name;
	Person(int a, string n) {
		age = a;
		name = n;
	}
};

template<typename T>
bool compare(T& a, T& b) {
	if (a == b) { return true; }
	return false;
}//无法用此版本的函数模板来对比两个类，解决方式为重载一个模板的具体化版本
template<> bool compare(Person& a, Person& b) {
	if (a.age == b.age && a.name == b.name) { return true; }
	return false;
}

int main() {
	Person p1(22, "Kallen");
	Person p2(22, "Kallen");
	cout << compare(p1, p2) << endl;

	system("pause");
	return 0;
}
```

### 1.3 类模板

#### 1.3.1 类模板的用法

**作用：**建立一个通用类，类中的成员的数据类型可以不具体指定，用一个**虚拟的类型**来代表

**语法：**

```C++
template<typename T>
类的定义
```

```C++
//示例：
template<typename NameT, typename AgeT>
class Person {
public:
	NameT name;
	AgeT age;
	Person(NameT name, AgeT age) {
		this->name = name;
		this->age = age;
	}
};

int main() {
	Person<string, int> p1("Kallen", 22);//利用尖括号来指定数据类型

	system("pause");
	return 0;
}
```

#### 1.3.2 类模板和函数模板的区别

+ 类模板没有自动类型推导的使用方式，**必须显示指定类型**
+ 类模板在模板参数列表中可以有默认参数，通过``=``来指定默认的参数类型

```C++
template<typename NameT, typename AgeT = int>//指定默认类型
class Person {
public:
	NameT name;
	AgeT age;
	Person(NameT name, AgeT age) {
		this->name = name;
		this->age = age;
	}
};
```

#### 1.3.3 类模板中成员函数的创建实际

+ 普通类中的成员函数在一开始就可以创建
+ 类模板中的成员函数在==**调用时**==才创建

```C++
class Person1 {
public:
	void showPerson1(){ cout << "Person1" << endl; }
};

class Person2 {
public:
	void showPerson2() { cout << "Person2" << endl; }
};

template<typename T>
class Person {
public:
	T obj;
	void func1() { obj.showPerson1(); }
	void func2() { obj.showPerson2(); }
};
//上述代码可以正常生成，因为类模板中的函数在调用时才创建

int main() {
	Person<Person1> p;
	p.func1();
	p.func2();//报错，因为指定T为Person1时无法调用showPerson2

	system("pause");
	return 0;
}
```

#### 1.3.4 类模板对象做函数参数

类模板实例化出的对象，向函数传参的方式：

+ 指定传入的类型         直接显示对象的数据类型
+ 参数模板化                将对象中的参数变为模板进行传递
+ 整个类模板化            将这个对象类型模板化进行传递

```C++
template<typename T1, typename T2>
class Person {
public:
	T1 name;
	T2 age;
	Person(T1 name, T2 age) {
		this->name = name;
		this->age = age;
	}
	void show() { cout << "姓名：" << this->name << "\t年龄：" << this->age << endl; }
};
//1.指定传入类型
void method1(Person<string, int>& p) {
	p.show();
}
//2.参数模板化  Person<T1, T2>& p = Person<string, int>("Kallen", 22);
template<typename T1, typename T2>
void method2(Person<T1, T2>& p) {
	p.show();
	//可以利用 typeid 来看编译器推断T1和T2分别是什么类型
	cout << "T1:" << typeid(T1).name() << endl;
	cout << "T2:" << typeid(T2).name() << endl;
}
//3.整个类模板化		T& p = Person<string, int>("Kallen", 22);
//本质就是函数模板
template<typename T>
void method3(T& p) {
	p.show();
}

int main() {
	Person<string, int> p("Kallen", 22);
	method1(p);
	method2(p);
	method3(p);

	system("pause");
	return 0;
}
```

> 日常开发中**指定传入类型**更常用

#### 1.3.5 类模板与继承

+ 当父类是一个类模板的时候，子类在声明时需要指定父类中T的类型
+ 如果不指定，编译器无法给子类分配内存
+ 如果想灵活指定出父类中T的类型，子类也需要变成类模板

```C++
template<typename T>
class Base {
public:
	T m;
};

//1.需要指定父类中的通用数据类型
class Son1 :public Base<string> {};

//2.如果不想指定数据类型，则子类也需要写作模板，同时要记得将通用数据类型T2传递给父类的T
template<typename T1, typename T2>
class Son2 :public Base<T2> {
public:
	T1 obj;
};
```

> 如果父类是类模板，子类必须要指定出父类中T的数据类型（可以用**现有数据类型指定**也可以用**模板中的通用数据类型指定**）

#### 1.3.6 类模板成员的类外实现

```C++
template<typename T>
class Base {
public:
	Base(T m);
	T m;
	void show();
};
//要加作用域，同时作用域也要指定T的类型，进一步要求要有模板的关键词 template<typename T>
template<typename T> //<typename T>中的T传递给Base<T>中的T，再进一步传递给Base(T m)中的T
Base<T>::Base(T m) { this->m = m; }

template<typename T>
void Base<T>::show(){ cout << m << endl; }
```

> 类模板中的成员函数在类外实现时，需要加上**模板的参数列表**

#### 1.3.7 类模板分文件编写

**问题：**类模板中成员函数创建时机是在调用阶段，导致分文件编写时链接不到

1. 按照传统的方式是在**主程序.cpp**文件中采用``#include "类模板.h"``

2. **类模板.h**文件中的成员函数只有声明没有实现，所有的成员函数的实现都写在了**类模板.cpp**文件中

3. 类模板的成员函数是在调用时才生成的，所以采用此种方法编译**类模板.h**时成员函数并不会生成
4. 也就是**类模板.cpp**中的函数实现不会被编译

**解决：**

+ **直接包含.cpp文件**

  编译器会直接查找**.cpp**文件中的内容，而**.cpp**文件中有包含**.h**文件的语句，编译器则会进一步查找**.h**文件

+ **将声明和实现写到同一个文件中，并更改后缀名为.hpp**==（常用）==

#### 1.3.8 类模板与友元

+ 友元全局函数的类内实现：直接在类内实现即可
+ 友元全局函数的类外实现：需要提前让编译器知道全局函数实现的存在

```C++
//类的前置声明
template<typename T1, typename T2>
class Person;

//如果友元全局函数是类外实现的话，要让编译器提前知道这个函数的存在
//这个函数还要对后文的类进行操作，所以在这个函数前还要加类的前置声明
//全局函数的类外实现也要写模板的关键词
template<typename T1, typename T2>
void printInfo2(Person<T1, T2>& p) {
	cout << "类外实现" << endl;
	cout << p.name << ":" << p.age << endl;
}

template<typename T1, typename T2>
class Person {
	//全局函数可以在类内实现，前提是要加friend关键字，使其成为这个类的友元
	friend void printInfo1(Person<T1, T2>& p) { cout << p.name << ":" << p.age << endl; }
	//友元全局函数的类内声明，类外实现，要加空的参数列表（一对尖括号）告诉编译器这是一个函数模板的函数声明
	friend void printInfo2<>(Person<T1, T2>& p);
public:
	Person(T1 name, T2 age) {
		this->name = name;
		this->age = age;
	}
private:
	T1 name;
	T2 age;
};

int main() {
	Person<string, int> p("Kallen", 22);
	printInfo1(p);
	printInfo2(p);

	system("pause");
	return 0;
}
```

#### 1.3.9 类模板案例

```C++
//案例描述：
//实现一个通用的数组类，需求如下：
//1.可以对内置的数据类型和自定义数据类型的数据进行存储
//2.将数组中的数据存储到堆区
//3.构造函数中可以传入数组的容量
//4.提供对应的拷贝构造函数以及operator=防止浅拷贝问题
//5.提供尾插法和尾删法对数组中的数据进行增加和删除
//6.可以通过下标的方式访问数组中的元素
//7.可以获取数组中当前元素的个数和数组的容量

//下述代码只包含MyArray.hpp文件，不包含测试程序
template<class T>
class MyArray {
private:
	int capacity;
	int size;
	T* p;
public:
	MyArray(int capacity);//有参构造-声明
	~MyArray();//析构-声明
	MyArray(const MyArray<T>& arr);//拷贝构造-声明
	MyArray<T>& operator=(const MyArray<T>& arr);//重载赋值运算符-声明
	void add(T t);//添加元素-声明
	void remove();//删除元素-声明
	T& operator[](int index);//重载中括号-声明
	int getCapacity();
	int getSize();
};

//有参构造-实现
template<class T>
MyArray<T>::MyArray(int capacity) {
	this->capacity = capacity;
	this->size = 0;
	this->p = new T[this->capacity];
	cout << "有参构造" << endl;
}

//析构-实现
template<class T>
MyArray<T>::~MyArray() {
	if (this->p != NULL) {
		delete[] p;
		this->p = NULL;
	}
	cout << "析构" << endl;
}

//拷贝构造-实现
template<class T>
MyArray<T>::MyArray(const MyArray<T>& arr) {
	this->capacity = arr.capacity;
	this->size = arr.size;
	this->p = new T[this->capacity];
	for (int i = 0; i < this->capacity; i++) {
		this->p[i] = arr.p[i];
	}
	cout << "拷贝构造" << endl;
}

//重载赋值运算符-实现
template<class T>
MyArray<T>& MyArray<T>::operator=(const MyArray<T>& arr) {
	if (this->p != NULL) {
		delete[] p;
		this->p = NULL;
		this->capacity = 0;
		this->size = 0;
	}
	this->capacity = arr.capacity;
	this->size = arr.size;
	this->p = new T[this->capacity];
	for (int i = 0; i < this->capacity; i++) {
		this->p[i] = arr.p[i];
	}
	cout << "重载赋值运算符" << endl;
	return *this;
}

//添加元素-实现
template<class T>
void MyArray<T>::add(T t) {
	if (this->size == this->capacity) {
		cout << "数组已满" << endl;
		return;
	}
	this->p[this->size] = t;
	size++;
}

//删除元素-实现
template<class T>
void MyArray<T>::remove() {
	if (this->size == 0) {
		cout << "数组已空" << endl;
		return;
	}
	size--;
}

//重载中括号-实现
template<class T>
T& MyArray<T>::operator[](i	nt index) {
	return this->p[index];
}

template<class T>
int MyArray<T>::getCapacity() {
	return this->capacity;
}

template<class T>
int MyArray<T>::getSize() {
	return this->size;
}

```

## 2 STL初识

### 2.1 STL的诞生

+ 软件界一直希望能建立一种可重复利用的东西
+ C++的**面向对象**和**泛型编程**思想，目的就是**提升复用性**
+ 大多数情况下，数据结构和算法都未能有一套标准，导致被迫从事大量重复工作
+ 为了建立数据结构和算法的一套标准，诞生了**STL**

### 2.2 STL基本概念

+ ==**STL**==是 ***Standard Tempalte Library*** 的简称，即**标准模板库**
+ STL广义上分为**容器container，算法algorithm，迭代器iterator**
+ **容器**和**算法**直接通过**迭代器**进行无缝衔接
+ STL几乎所有的代码都采用了模板类或者模板函数

### 2.3 STL的六大组件

六大组件分别是：**容器，算法，迭代器，仿函数，适配器（配接器），空间配置器**

1. 容器：各种数据结构，如***vecotr, list, deque, set, map*** 等，用以存放数据
2. 算法：各种常用的算法，如 ***sort, find, copy, for_each*** 等
3. 迭代器：容器和算法之间的胶合剂
4. 仿函数：行为类似函数，可以作为算法的某种策略
5. 适配器：一种用来==修饰容器==或者==仿函数==或者==迭代器==接口的东西
6. 空间配置器，负责空间的配置与管理

### 2.4 STL中容器、算法、迭代器

**容器：**置物之所也

STL容器就是将运用最广泛的一些**数据结构**实现出来

常用的数据结构包括：**数组，链表，树，栈，队列，集合，映射表**等

这些容器分为**序列式容器**和**关联式容器**：

+ **序列式容器：**强调值的排序，每个元素都有固定的位置
+ **关联式容器：**二叉树结构，各元素之间没有严格的物理上的顺序结构



**算法：**问题之解也

有限的步骤，解决逻辑或数学上的问题，这一门学科叫做**算法 *Algorithms***

算法分为**质变算法**和**非质变算法**：

+ **质变算法：**运算过程中会更改区间内元素的内容，如拷贝，删除，替换等
+ **非质变算法：**运算过程中不会更改区间内的元素的内容，如查找，计数，遍历，寻找极值等等



**迭代器：**容器和算法之间的粘合剂

提供一种方法，使之能够依序寻访某个容器所含的各个元素，而又无需暴露该容器的内部表示方式

每个容器都有自己专属的迭代器

**迭代器的使用非常类似于指针**

|      种类      |                           功能                           |                支持运算                 |
| :------------: | :------------------------------------------------------: | :-------------------------------------: |
|   输入迭代器   |                     对数据的只读访问                     |          只读，支持++、==、!=           |
|   输出迭代器   |                     对数据的只写访问                     |              只写，支持++               |
|   前向迭代器   |               读写操作，并能向前推进迭代器               |          读写，支持++、==、!=           |
|   双向迭代器   |                读写操作，并能向前向后操作                |            读写，支持++，--             |
| 随机访问迭代器 | 读写操作，可以以跳跃的方式访问任意数据，功能最强的迭代器 | 读写，支持++、--、[n]、-n、<、<=、>、>= |

> 常用的迭代器为双向迭代器和随机访问迭代器

### 2.5 容器算法迭代器初识

#### 2.5.1 vector存放内置数据类型

**容器：**``vector``

**算法：**``for_each``

**迭代器：**``vector<int>::iterator``

```C++
void myPrint(int value) {
	cout << value << endl;
}

void test01() {
	vector<int> v;//创建一个vecotr容器
	//利用.push_back()向容器中插入数据
	v.push_back(10);
	v.push_back(20);
	v.push_back(30);
	//第一种遍历方式：利用while循环
	//要通过迭代器来访问容器中的数据
	//利用 vector 作用域下的迭代器 iterator 创建一个变量 first
	vector<int>::iterator first = v.begin();//起始迭代器 .begin() 的返回值是当前容器中第一个元素的地址
	vector<int>::iterator last = v.end();//结束迭代器 .end() 的返回值是当前容器的最后一个元素的下一个地址
	while (first != last) {
		cout << *first << endl;
		first++;
	}

	//第二种遍历方式：利用for循环，其中 != 也可以改成 <
	for (vector<int>::iterator it = v.begin(); it != v.end(); it++) {
		cout << *it << endl;
	}

	//第三种遍历方式：利用for循环另一种实现
	for (int& value : v) {
		cout << value << endl;
	}

	//第四种遍历方式：利用for循环和.size()函数
	for (int i = 0; i < v.size(); i++) {
		cout << v[i] << endl;
	}

	//第五种遍历方式：利用算法中的for_each，需要传入的有三个形参：起始左区间，结束右区间，以及要实现的函数
	//函数只要写名字即可，无需写形参列表，这里利用了回调的技术，在for_each内部才会调用该函数
	for_each(v.begin(), v.end(), myPrint);
}

int main() {
	test01();

	system("pause");
	return 0;
}
```

#### 2.5.2  Vector存放自定义类型的数据

```C++
class Person {
public:
	Person(){}
	Person(string name) { this->name = name; }
	void show() { cout << this->name << endl; }
private:
	string name;
};

void myPrint(Person* p) {
	p->show();
}

//存放自定义类型
void test01() {
	vector<Person> v;
	v.push_back(Person("Kallen"));
	v.push_back(Person("C.C."));

	for (Person& value : v) {
		value.show();
	}

	for (vector<Person>::iterator it = v.begin(); it < v.end(); it++) {
		(*it).show();
		it->show();
	}
}

//存放自定义类型的指针
void test02() {
	vector<Person*> v;
	Person p1("Kozuki");
	Person p2("CodeGeass");
	v.push_back(&p1);
	v.push_back(&p2);

	vector<Person*>::iterator first = v.begin();
	vector<Person*>::iterator last = v.end();
	while (first != last) {
		(*first)->show();
		first++;
	}

	for (int i = 0; i < v.size(); i++) {
		v[i]->show();
	}

	for_each(v.begin(), v.end(), myPrint);
}

int main() {
	//test01();
	test02();

	system("pause");
	return 0;
}
```

#### 2.5.3 Vector容器嵌套容器

```C++
void test01() {
	vector<int> v1;
	v1.push_back(11);
	v1.push_back(12);

	vector<int> v2;
	v2.push_back(21);
	v2.push_back(22);

	vector<vector<int>> v;//容器中放入另一个容器，类似二维数组
	v.push_back(v1);
	v.push_back(v2);

	for (vector<vector<int>>::iterator outer = v.begin(); outer < v.end(); outer++) {
		for (vector<int>::iterator inner = (*outer).begin(); inner < (*outer).end(); inner++) {
			cout << (*inner) << endl;
		}
	}
}

int main() {
	test01();

	system("pause");
	return 0;
}
```

## 3 STL-常用容器

### 3.1 string容器

#### 3.1.1 string基本概念

**本质：**string是C++风格的字符串，而string本质上是一个类

**string和char*的区别：**

+ char* 是一个指针
+ string是一个类，类内部封装了char*，管理这个字符串，是一个char\* 型的容器

**特点：**

+ string 内部封装了很多成员方法，如查找find，拷贝copy，删除delete，替换replace，插入insert
+ string管理char* 所分配的内存，不用担心复制越界和取值越界等，由类内部进行负责

#### 3.1.2 string构造函数

**构造函数原型：**

+ ``string();//创建一个空的字符串 例如：string str;``

  ``string(const char* s);//使用字符串s初始化``

+ ``string(const string& str);//使用一个string对象初始化另一个string对象``
+ ``string(int n, char c);//使用n个字符c初始化``

```C++
	const char* str = "Hello World";//Hello World是字符串常量，所以写了const
	//与上述构造一一对应
	string s1;
	string s2(str);
	string s3(s2);
	string s4(10, 'a');
```

#### 3.1.3 string赋值操作

**赋值函数原型：**

+ ``string& operator=(const char* s);//把char* 类型字符串赋值给当前的string 字符串``
+ ``string& operator=(const string &str);//把字符串str赋值给当前字符串``
+ ``string& operator=(char c);//把字符赋值给当前字符串``
+ ``string& assign(const char* s);//把字符串s赋值给当前字符串``
+ ``string& assign(const char* s, int n);//把字符串s的前n个字符赋值给当前的字符串``
+ ``string& assign(const string& str);//把字符串str赋值给当前字符串``
+ ``string& assign(int n, char c);//用n个字符c赋值给当前字符串``

```C++
	const char* str = "Kallen";
	char c = 'c';
	string s1, s2, s3, s4, s5, s6, s7;
	//与上述赋值一一对应
	s1 = str;
	s2 = s1;
	s3 = c;
	s4.assign(str);
	s5.assign(str, 3);
	s6.assign(s5);
	s7.assign(2, 'c');
```

#### 3.1.4 string字符串拼接

**功能描述：**在字符串末尾拼接字符串

**函数原型：**

+ ``string& operator+=(const char* s);//重载+=``
+ ``string& operator+=(const char c);//重载+=``
+ ``string& operator+=(const string&  str);//重载+=``
+ ``string& append(const char* s);//将字符串s连接到当前字符串结尾``
+ ``string& append(const char* s, int n);//将字符串s的前n个字符连接到当前字符串结尾``
+ ``string& append(const string& str);//将字符串str连接到当前字符串结尾``
+ ``string& append(const string& str, int pos, int n);//将字符串str中从pos开始的n个字符串连接到当前字符串结尾``

```C++
	const char* s = "Kallen";
	const char c = 'c';
	const string str = "C.C.";
	string s1, s2, s3, s4, s5, s6, s7;
	//与上述函数一一对应
	s1 += s;
	s2 += c;
	s3 += str;
	s4.append(s);
	s5.append(s, 3);
	s6.append(str);
	s7.append(str, 2, 2);
```

#### 3.1.5 string查找和替换

**功能描述：**

+ 查找：查找指定字符串是否存在
+ 替换：在指定的位置替换字符串

**函数原型：**

+ ``int find(const string& str, int pos = 0) const;//查找str第一次出现的位置，从pos开始查找``
+ ``int find(const char* s, int pos = 0) const;//查找s第一次出现的位置，从pos开始查找``
+ ``int find(const char* s, int pos, int n) const;//从pos位置查找s的前n个字符串第一次出现的位置``
+ ``int find(const char c, int pos = 0) const;//查找字符c第一次出现的位置``
+ ``int rfind(const string& str, int pos = npos) const;//查找str最后一次出现的位置，从pos开始查找``
+ ``int rfind(const char* s, int pos = npos) const;//查找s最后一次出现的位置，从pos开始查找``
+ ``int rfind(const char* s, int pos, int n) const;//从pos开始查找s前n个字符串最后一次出现的位置``
+ ``int rfind(const char c, int pos = 0) const;//查找字符c最后一次出现的位置``
+ ``string& replace(int pos, int n, const string& str);//替换从pos开始的n个字符为字符串str``
+ ``string& replace(int pos, int n, const char* s);//替换从pos开始的n个字符为字符串s``

```C++
	const string str = "bc";
	const char* s = "cb";
	const string str1 = "AB";
	const char* s1 = "CDDDDD";
	string s0 = "abcdedcba";
	//与上述函数一一对应
	int f1 = s0.find(str);//1
	int f2 = s0.find(s);//6
	int f3 = s0.find(s, 3, 1);//6
	int f4 = s0.find('c');//2
	int rf1 = s0.rfind(str);//1
	int rf2 = s0.rfind(s);//6
	int rf3 = s0.rfind(s, 8, 2);//6
	int rf4 = s0.rfind('b');//7
	s0.replace(0, 2, str1);//ABcdedcba
	s0.replace(2, 2, s1);//ABCDDDDDedcba
```

> find是从**左往右**查找，rfind是从**右往左**查找，查找不到的返回值是**-1**
>
> **注意：**涉及pos和n形参的查找函数不支持``const string&`` 形式的形参，只能传入``const char*``

#### 3.1.6 string字符串比较

**比较方式：**字符串是按照字符的ASCII码进行对比

**比较结果返回值：**

+ = 返回 0
+ \> 返回 1
+ < 返回 -1

**函数原型：**

+ ``int compare(const string &str) const;//与字符串str比较``
+ ``int compare(const char* s) const;//与字符串s比较``

```C++
	string kallen = "Kallen";
	const string str = "Kallen";
	const char* s1 = "CodeGeass";
	const char* s2 = "Kozuki";
	int result = kallen.compare(str);//0
	result = kallen.compare(s1);//1
	result = kallen.compare(s2);//-1
```

> 比较方式为逐字符比较，按照每个字符的ASCII码来比较，英文中即按照字母顺序比较，例如A>B
>
> 大写英文字母的ASCII码要小于其对应的小写字母的ASCII码，如A<a

#### 3.1.7 string字符存取

**含义：：**可以对字符串中的单个字符进行存取操作

**函数原型：**

+ ``char& operator[](int n);//通过[]的方式获取第n个字符``
+ ``char& at(int n);//通过at函数获取第n个字符``

```C++
	string kallen = "Kallen";
	char a;
	a = kallen[1];//a
	kallen[1] = 'A';
	a = kallen.at(0);//K
	kallen.at(0) = 'k';
```

#### 3.1.8 string插入和删除

**功能描述：**对string字符串进行插入和删除字符的操作

**函数原型：**

+ ``string& insert(int pos, const char* s);//在s的pos位置插入字符串``
+ ``string& insert(int pos, const string& str);//在str的pos位置插入字符串``
+ ``string& insert(int pos, int n, char c);//在pos位置插入n个字符c``
+ ``string& erase(int pos, int n = npos);//删除从pos开始的n个字符``

```C++
	string kallen = "Kallen";
	string result;
	string str = "Kozuki";
	const char* s = "C.C.";
	char c = 'c';
	//与上述函数一一对应
	kallen.insert(6, str);//KallenKozuki
	kallen.insert(0, s);//C.C.KallenKozuki
	kallen.insert(16, 2, c);//C.C.KallenKozukicc
	kallen.erase(0, 4);//KallenKozukicc
```

> 使用**insert**时，是在pos位置的**前端**插入，每个字符串的末尾会自动补一个**’\0'**，pos从***0***开始计数
>
> 使用**erase**时，删除是从pos位置开始（即包括pos位置本身）

#### 3.1.9 string子串

**功能描述：**从字符串中获取想要的子串

**函数原型：**

``string substr(int pos, int n = npos) const;//返回由pos开始的n个字符组成的字符串``

```C++
	string kallen = "KallenKozuki";
	string kozuki;
	kozuki = kallen.substr(6, 6);//Kozuki
```

### 3.2 vector容器

#### 3.2.1 vector基本概念

**功能：**vector数据结构和**数组非常相似**，也称为**单端数组**

**与普通数组的区别：**数组是静态空间，而vector可以**动态拓展**

**动态拓展：**

+ 并不是直接在原空间之后续接新空间，而是寻找更大的内存空间，将**原数据拷贝至新空间再加上新数据**，并**释放原空间**
+ vector容器的迭代器是支持随机访问的迭代器

**vector容器常用的函数：**

+ ``.rend()//容器内第一个元素的前一个位置的地址``
+ ``.end()//容器内最后一个元素的下一个位置的地址``
+ ``.rbegin()//容器内最后一个元素的地址``
+ ``.begin()//容器内第一个元素的地址``
+ ``.push_back()//在容器最后一个元素后插入一个新的元素``
+ ``.pop_back()//弹出容器的最后一个元素``
+ ``.insert()//在指定位置插入一个新元素``

#### 3.2.2 vector构造函数*

**构造函数原型：**

+ ``vector<T> v;//采用模板实现，默认构造函数``
+ ``vector(v.begin(), v.end());//将v对象的v.begin()到v.end()区间中的元素拷贝到新对象，区间可以自定义``
+ ``vector(int n, elem);//构造函数将n个elem拷贝给本身``
+ ``vector(const vector &vec)//拷贝构造函数``

```C++
template<class T>
void myPrint(vector<T> &v) {
	for (T t : v) {
		cout << t << " ";
	}
	cout << endl;
}

void test01() {
	vector<int> v1;
	for (int i = 0; i < 10; i++) {
		v1.push_back(i);
	}
	myPrint(v1);
	vector<int> v2(v1.begin(), v1.end() - 1);//本质就是传入两个迭代器构成一个区间
	myPrint(v2);
	vector<int> v3(10, 1);//形参列表中前者是个数，后者是值
	myPrint(v3);
	vector<int> v4(v2);
	myPrint(v4);
}

int main() {
	test01();

	system("pause");
	return 0;
}
```

> ``vector(v.begin(), v.end());``本质是**传入两个迭代器**构成一个区间，区间左值右值根据需求选定
>
> ``vector(int n, elem);``切记形参列表中前者是个数，后者是值
>
> 如果形参列表中要传入一个**const的容器**，则该函数内的迭代器要用 ***const_iterator***

#### 3.2.3 vector赋值操作

**函数原型：**

+ ``vector& operator=(const vector &vec);//重载赋值运算符``
+ ``assign(begin, end);//传入两个迭代器构成一个区间，将区间内的值赋值给调用assign的对象``
+ ``assign(int n, elem);//将n个elem拷贝赋值给调用assign的对象``

```C++
	vector<int> v1;
	for (int i = 0; i < 10; i++) {
		v1.push_back(i);
	}
	vector<int> v2, v3, v4;
	//与上述函数一一对应
	v2 = v1;
	v3.assign(v1.begin(), v1.end() - 1);
	v4.assign(10, 2);
```

#### 3.2.4 vector容量和大小

**功能描述：**对vector容器的容量和大小进行操作

**函数原型：**

+ ``empty();//判断是否为空，空返回1，非空返回0``
+ ``capacity();//返回容器的容量``
+ ``size();//返回容器中元素的个数``
+ ``resize(int num);//重新指定容器的长度为num，若容器变长，则以默认值填补空位，若变短，超出新长度的元素被删除``
+ ``resize(int num, elem);//重新指定容器的长度为num，若容器变长，则以elem填补空位，若变短，超出新长度的元素被删除``

```C++
	vector<int> v1, v2;
	for (int i = 0; i < 10; i++) {
		v1.push_back(i);
	}
	//empty()
	int result = v1.empty();//0
	result = v2.empty();//1
	//capacity()
	result = v1.capacity();//13
	//size()
	result = v1.size();//10
	//resize()
	v1.resize(5);
	result = v1.capacity();//13
	result = v1.size();//5
	v1.resize(18);
	result = v1.capacity();//19
	result = v1.size();//18
	v1.resize(20, 5); 
	result = v1.capacity();//28
	result = v1.size();//20
```

> ***capacity*** 是容量，指当前容器所能容纳的最多元素数量，***size*** 是大小，指当前容器的元素个数
>
> 如果``resize()``使得vector容器的**大小减少**，则容器的**容量不变**
>
> 如果``resize()``使得vector容器的**大小大于原先的容量**，则该容器会重新分配内存并且**容量也会变大**

#### 3.2.5 vector插入和删除

**函数原型：**

+ ``push_back(elem);//尾部插入元素elem``
+ ``pop_back();//删除最后一个元素``
+ ``insert(const_iterator pos, elem);//迭代器指向位置pos插入元素elem``
+ ``insert(const_iterator pos, int count, elem);//迭代器向指定位置pos插入count个元素elem``
+ ``erase(const_iterator pos);//删除迭代器指向的元素``
+ ``erase(const_iterator start, const_iterator end);//删除迭代器start到end区间的元素``
+ ``clear();//删除容器中的所有内容``

```C++
	vector<int> v1;
	for (int i = 0; i < 5; i++) {
		v1.push_back(i);
	}
	//与上述函数一一对应
	v1.push_back(329);//0 1 2 3 4 329
	v1.pop_back();//0 1 2 3 4
	//.begin() 可以看做是 index = 0，.begin() + n 则可以访问第n + 1（即下标为n的元素）
	v1.insert(v1.begin() + 3, 0);//0 1 2 0 3 4
	v1.insert(v1.begin() + 2, 5, 9);//0 1 9 9 9 9 9 2 0 3 4
	v1.erase(v1.begin() + 8);//0 1 9 9 9 9 9 2 3 4
	v1.erase(v1.begin() + 2, v1.begin() + 7);//0 1 2 3 4
	v1.clear();//nothing
```

#### 3.2.6 vector数据存取

**函数原型：**

+ ``at(int index);//返回索引index所指向的数据``
+ ``operator[index];//返回索引index所指向的数据``
+ ``front();//返回容器中第一个数据元素``
+ ``back();//返回容器中最后一个数据元素``

```C++
	vector<int> v1;
	for (int i = 0; i < 5; i++) {
		v1.push_back(2 * i);
	}
	//与上述函数一一对应
	v1.at(4);//8
	v1[2];//4
	v1.front();//0
	v1.back();//8
```

#### 3.2.7 vector互换容器

**功能描述：**实现两个容器内的元素互换

**函数原型：**

+ ``swap(vec);//将vec与本身的元素进行互换``

```C++
	vector<int> v1, v2;
	for (int i = 0; i < 5; i++) {
		v1.push_back(2 * i);
		v2.push_back(rand() % 10);
	}
	v2.push_back(rand() % 10);
	v2.push_back(rand() % 10);

	cout << v1.capacity() << " " << v1.size() << endl;//6 5
	cout << v2.capacity() << " " << v2.size() << endl;//9 7
	cout << "--------------" << endl;
	v1.swap(v2);
	cout << v1.capacity() << " " << v1.size() << endl;//9 7
	cout << v2.capacity() << " " << v2.size() << endl;//6 5
```

> ``swap()``会互换两个容器的**容量，大小，和容器中所有的元素**

**用途：**

+ 可以利用``swap()``巧妙地重新分配内存占用（收缩内存）

```C++
	vector<int> v;
	for (int i = 0; i < 100000; i++) {
		v.push_back(i);
	}
	cout << v.capacity() << "  " << v.size() << endl;//138255  100000
	//目前容器v占用了 13w 的内存空间，其中使用了 10w

	v.resize(329);//将容器v的大小缩小至329
	cout << v.capacity() << "  " << v.size() << endl;//138255  329
	//目前容器v占用了 13w 的内存空间，但只使用了 329，造成了内存空间的大量浪费

	vector<int>(v).swap(v);//分析见后文
	cout << v.capacity() << "  " << v.size() << endl;//329  329
	//此时内存空间不再大量浪费

	/* vector<int>(v).swap(v); 的作用：
	1. 这段代码需要分为两部分，vector<int>(v) 和 .swap(v)
	2. 前半部分 vector<int>(v) 是利用 vector 的拷贝构造函数创建的 匿名对象，为方便分析可以指定其名字为 x
	3. 构造发生时，容器的 容量capacity 会根据 大小size 分配合适的内存空间
	4. .swap(v) 执行后交换了容器 x 和容器 v 中的所有元素
	5. 原先容器 v 指向的内存空间是 容量13w 大小329，容器 x 指向的内存空间是 容量329 大小329
	6. 交换后变为容器 v 指向的内存空间是 容量329 大小329
	7. 又因为容器 x 是匿名对象，在当行代码运行结束后，编译器会调用析构函数将匿名对象析构，因此释放了大量被占用但空置的内存
	8. swap()便实现对 resize 后的容器 v 的容量的瘦身，大大节省了内存
	*/
```

#### 3.2.8 vector预留空间

**功能描述：**减少vector在动态扩展容量时的扩展次数

**函数原型：**

+ ``reserve(int length);//容器预留length个元素长度，预留位置不初始化，不可访问``

```C++
	vector<int> v;
	int count = 0;//重新分配内存计数器
	int* p = NULL;//指向vector容器的首地址
	for (int i = 0; i < 10000; i++) {
		v.push_back(i);
		//计算容器重新分配了多少次内存
		if (p != &v[0]) {
			p = &v[0];
			count++;
		}
	}
	cout << "count = " << count << endl;//count = 24

	count = 0;
	vector<int> v1;
	v1.reserve(10000);//预留地址
	for (int i = 0; i < 10000; i++) {
		v1.push_back(i);
		//计算容器重新分配了多少次内存
		if (p != &v1[0]) {
			p = &v1[0];
			count++;
		}
	}
	cout << "count = " << count << endl;//count = 1
```

> 如果数据量比较大，可以一开始利用``reserve()``预留空间，减少后续重新分配内存的次数

### 3.3 deque容器

#### 3.3.1 deque容器的概念

**功能：**双端数组，可以对头端进行插入删除操作

**deque和vector的区别：**

+ vector对于头部的插入删除效率低，数据量越大，效率越低
+ deque相对而言，对头部的插入删除速度比vector快
+ vector访问元素时的速度会比deque快，这和两者的内部实现有关

**deque内部工作原理：**

deque内部有个**中控器**，维护每段缓冲区中的内容，缓冲区中存放真实数据

中控器维护的是每个缓冲区的地址，使得使用deque时想一片连续的内存空间

deque容器的迭代器也支持随机访问

#### 3.3.2 deque构造函数

**函数原型：**

+ ``deque<T> deq;//默认构造函数``
+ ``deque(const_iterator begin, const_iterator end);//构造函数将区间begin到end内的元素拷贝给新对象``
+ ``deque(int n, elem);//构造函数将n个元素elem拷贝给新对象``
+ ``deque(const deque &deq);//拷贝构造函数``

```C++
	//与上述函数一一对应
	deque<int> deq1;
	for (int i = 0; i < 5; i++) {
		deq1.push_back(i);
	}
	deque<int> deq2(deq1.begin(), deq1.end());
	deque<int> deq3(10, 1);//10个1
	deque<int> deq4(deq3);
```

#### 3.3.3 deque赋值操作

**函数原型：**

+ ``deque& operator=(const deque &deq);//重载赋值运算符``
+ ``assign(const_iterator begin, const_iterator end);//将区间begin到end中的数据拷贝赋值给自身``
+ ``assign(int n, elem);//将n个elem拷贝赋值给自身``

```C++
	deque<int> deq1;
	for (int i = 0; i < 5; i++) {
		deq1.push_back(i);
	}
	//与上述函数一一对应
	deque<int> deq2;
	deq2 = deq1;
	deq2.assign(deq1.begin(), deq1.end());
	deq2.assign(10, 1);
```

#### 3.3.4 deque大小操作

**函数原型：**

+ ``empty();//判断容器是否为空，空则返回1，非空返回0``
+ ``size();//返回容器中元素的个数``
+ ``resize(int num);//重新指定容器的长度为num，变长则以默认值填充新位置(末尾)；变短则末尾超出容器长度的元素被删除``
+ ``resize(int num, elem);//重新指定容器的长度为num，变长则以elem填充新位置(末尾)；变短则末尾超出容器长度的元素被删除``

```C++
	deque<int> deq1, deq2;
	for (int i = 0; i < 5; i++) {
		deq1.push_back(i);
	}
	//与上述函数一一对应
	cout << deq1.empty() << " " << deq2.empty() << endl;//0 1
	cout << deq1.size() << endl;//5
	deq1.resize(3);
	deq1.resize(9, 10);//size = 9，新增元素以 10 填充
```

> deque没有容量的概念

#### 3.3.5 deque插入和删除

**插入操作函数原型：**

+ ``push_back(elem);//在容器尾部添加一个数据``
+ ``push_front(elem);//在容器头部插入一个数据``
+ ``pop_back();//删除容器最后的一个数据``
+ ``pop_front();//删除容器的第一个数据``

```C++
	deque<int> deq1;
	for (int i = 0; i < 3; i++) {
		deq1.push_back(i);
		deq1.push_front(2 * i);
	}
	deq1.pop_back();
	deq1.pop_front();
```

**指定位置操作函数原型：**

+ ``insert(const_iterator pos, elem);//在pos位置插入一个元素elem的拷贝，返回新数据的位置``
+ ``insert(const_iterator pos, int n, elem);//在pos位置插入n个元素elem，无返回值``
+ ``insert(const_iterator pos, const_iterator begin, const_iterator end);//在pos位置插入[begin, end)区间内的数据，无返回值``
+ ``erase(const_iterator begin, const_iterator end);//删除[begin, end)区间内的数据，返回下一个数据的位置``
+ ``erase(const_iterator pos);//删除pos位置的数据，返回下一个数据的位置``
+ ``clear();//清空容器``

```c++
	deque<int> deq1;
	for (int i = 0; i < 5; i++) {
		deq1.push_back(i);
	}
	//与上述函数一一对应
	deq1.insert(deq1.begin() + 3, 33);//0 1 2 33 3 4
	deq1.insert(deq1.begin() + 1, 3, 11);//0 11 11 11 1 2 33 3 4
	deq1.insert(deq1.begin(), deq1.begin(), deq1.end());//0 11 11 11 1 2 33 3 4 0 11 11 11 1 2 33 3 4
	deq1.erase(deq1.begin(), deq1.begin() + 9);//0 11 11 11 1 2 33 3 4 ,切记删除区间是左闭右开
	deq1.erase(deq1.begin() + 6);//0 11 11 11 1 2 3 4
	deq1.clear();//nothing
```

> 插入 ***insert*** 和删除 ***erase*** 操作都要提供迭代器来指明位置

#### 3.3.6 deque数据存取

**函数原型：**

+ ``at(int index);//返回索引index所指的数据``
+ ``operator[];//返回索引index所指的数据``
+ ``front();//返回容器中第一个数据元素``
+ ``back();//返回容器中最后一个数据元素``

```c++
	deque<int> deq1;
	for (int i = 0; i < 5; i++) {
		deq1.push_back(i);
	}
	//与上述函数一一对应
	cout << deq1.at(4) << endl;//4
	cout << deq1[3] << endl;//3
	cout << deq1.front() << endl;//0
	cout << deq1.back() << endl;//4
```

#### 3.3.7 deque排序

**功能描述：**利用算法实现对deque容器的排序==（升序）==

**算法：**

+ ``sort(const_iterator begin, const_iterator end);//对区间[begin, end)内的元素进行排序``

```c++
void myPrint(const deque<int>& deq) {
	for (deque<int>::const_iterator it = deq.begin(); it < deq.end(); it++) {
		cout << *it << " ";
	}
	cout << endl;
}

void test01() {
	deque<int> deq1;
	for (int i = 0; i < 10; i++) {
		deq1.push_back(rand() % 10);
	}
	myPrint(deq1);//1 7 4 0 9 4 8 8 2 4

	//sort() 算法在头文件 <algorithm> 中
	sort(deq1.begin(), deq1.end());
	myPrint(deq1);//0 1 2 4 4 4 7 8 8 9
}

int main() {
	test01();

	system("pause");
	return 0;
}
```

> 对于支持随机访问的迭代器的容器，都可以利用 ***sort*** 算法直接对其进行排序

### 3.4 stack容器

#### 3.4.1 stack容器基本概念

**概念：**stack是一种==**先进后出*(First In Last Out, FILO)***== 的数据结构，它只有一个开口

+ 栈底：stack的底部，存放着最先入栈的数据元素
+ 栈顶：stack的顶部，存放着最晚入栈的数据元素

> 存入数据叫**入栈**，弹出数据叫**出栈**
>
> 栈中只有顶端的元素可以被外界使用，因此栈**不允许遍历操作**

#### 3.4.2 stack常用接口

**构造函数：**

+ ``stack<T> stk;//stack采用模板类实现，stack对象的默认构造形式``
+ ``stack(const stack &stk);//拷贝构造函数``

**赋值操作：**

+ ``stack& operator=(const stack &stk);//重载等号运算符``

**数据存取：**

+ ``push(elem);//向栈顶添加元素``
+ ``pop();//从栈顶移除第一个元素``
+ ``top();//返回栈顶元素``

**大小操作：**

+ ``empty();//判断栈是否为空``
+ ``size();//返回栈的大小``

```c++
	stack<int> stk1;//默认构造函数
	stk1.push(1);//向栈顶添加元素
	cout << "stk1的大小：" << stk1.size() << endl;

	stack<int> stk2(stk1);//拷贝构造函数
	stk2.pop();//移除栈顶元素
	if (stk2.empty()) {
		cout << "此时stk2为空" << endl;
	}

	stk2 = stk1;//重载=
	cout << "stk2的栈顶元素：" << stk2.top() << endl;//返回栈顶元素
```

### 3.5 queue容器

#### 3.5.1 queue基本概念

**概念：**Queue是一种==**先进先出 *(First In First Out, FIFO)***== 的数据结构，有两个开口

+ 队尾：只允许存入数据
+ 队头：只允许弹出数据

> 队列容器允许从一端新增元素，从另一端移除元素
>
> 存入数据叫**入队**，弹出数据叫**出队**
>
> 队列中只有队头和队尾可以被外界使用，因此队列**不允许遍历操作**

#### 3.5.2 queue常用接口

**构造函数：**

+ ``queue<T> que;//默认构造函数``
+ ``queue(const queue &que);//拷贝构造函数``

**赋值操作：**

+ ``queue& operator=(const queue &que);//重载=``

**数据存取：**

+ ``push(elem);//往队尾添加元素``
+ ``pop();//弹出队头的元素``
+ ``back();//返回队尾的元素``
+ ``front();//返回队头的元素``

**大小操作：**

+ ``empty();//判断队列是否为空``
+ ``size();//返回队列的大小``

```c++
	queue<int> que1;//默认构造函数
	que1.push(1);//向队尾添加元素
	que1.push(2);
	cout << "size of que1: " << que1.size() << endl;//返回que1的大小
	cout << "front of que1: " << que1.front() << endl;//返回que1的队头 1
	cout << "back of que2: " << que1.back() << endl;//返回que1的队尾 2

	queue<int> que2(que1);//拷贝构造函数
	que2.pop();//弹出队头的元素，也就是 1
	que2.pop();//弹出 2
	
	if (que2.empty()) {
		cout << "que2 is empty" << endl;
	}

	que2 = que1;//重载=
```

### 3.6 list容器

#### 3.6.1 list基本概念

**功能：**将数据进行链式存储

**概念：**==链表（list）==是一种物理存储单元上==非连续==的数据结构，数据元素的逻辑顺序是通过链表中的==指针链接==实现的

**链表的组成：**链表由一系列==结点==组成

**结点的组成：**一个是存储数据元素的==数据域==，另一个是存储下一个结点地址的==指针域==

**优点：**

+ 动态分配内存，不会造成内存浪费和溢出
+ 执行插入和删除非常方便，修改指针即可

**缺点：**

+ 遍历速度慢
+ 占用空间大

> STL中的链表是一个双向循环的链表
>
> 由于链表的存储方式并不是连续的内存空间，所以list的迭代器只支持前移和后移，属于**双向迭代器**
>
> 插入和删除操作都**不会造成原有list迭代器失效**，这在vector中是不成立的，这二者是STL最常用的容器

#### 3.6.2 list构造函数

**函数原型：**

+ ``list<T> lst;//默认构造函数``
+ ``list(const_iterator begin, const_iterator end);//将区间[begin, end)内的元素拷贝给新元素``
+ ``list(int n, elem);//将n个elem元素拷贝给新元素``
+ ``list(const list &lst);//拷贝构造函数``

```c++
	list<int> lst1;
	lst1.push_back(1);
	lst1.push_back(2);

	list<int> lst2(lst1.begin(), lst1.end());
	list<int> lst3(3, 0);
	list<int> lst4(lst1);
```

#### 3.6.3 list赋值和交换

**函数原型：**

+ ``assign(const_iterator begin, const_iterator end);//将区间[begin, end)内的元素赋值给自身``
+ ``assign(int n, elem);//将n个elem元素的拷贝赋值给自身``
+ ``list& operator=(const list &lst);//重载等号操作符``
+ ``swap(lst);//将lst与自身的元素进行交换``

```c++
	list<int> lst1, lst2, lst3, lst4;
	lst1.push_back(1);
	lst1.push_back(2);

	lst2.assign(lst1.begin(), lst1.end());
	lst3.assign(5, 0);
	lst4 = lst3;
	lst1.swap(lst4);
```

#### 3.6.4 list大小操作

**函数原型：**

+ ``size();//返回容器中元素的个数``
+ ``empty();//判断元素是否为空``
+ ``resize(int num);//重新指定容器的长度为num，变长则以默认值填充新位置(末尾)；变短则末尾超出容器长度的元素被删除``
+ ``resize(int num, elem);//重新指定容器的长度为num，变长则以elem填充新位置(末尾)；变短则末尾超出容器长度的元素被删除``

```c++
	list<int> lst;
	if (lst.empty()) {
		cout << "lst is empty now" << endl;
	}
	lst.push_back(10);
	lst.push_back(20);
	lst.push_back(30);
	lst.push_back(40);
	lst.push_back(50);
	cout << "size of lst: " << lst.size() << endl;
	lst.resize(7);//10 20 30 40 50 0 0
	lst.resize(10, 3);//10 20 30 40 50 0 0 3 3 3
```

#### 3.6.5 list插入和删除

**函数原型：**

+ ``push_back(elem);//尾插``
+ ``pop_back();//尾删``
+ ``push_front(elem);//头插``
+ ``pop_front();//头删``
+ ``insert(const_iterator pos, elem);//在pos位置插入elem元素的拷贝，返回新数据的地址``
+ ``insert(const_iterator pos, int n, elem);//在pos位置插入n个elem元素的拷贝，无返回值``
+ ``insert(const_iterator pos, const_iterator begin, const_iterator end);//在pos位置插入[begin, end)区间内的数据，无返回值``
+ ``erase(const_iterator begin, const_iterator end);//删除[begin, end)区间内的数据``
+ ``erase(const_iterator pos);//删除pos位置的数据``
+ ``remove(elem);//删除容器中所有与elem值匹配的元素``
+ ``clear();//清空容器``

```c++
	list<int> lst;
	for (int i = 0; i < 2; i++) {
		lst.push_back(i);
		lst.push_front(i + 1);
	}//2 1 0 1
	lst.pop_back();//2 1 0
	lst.pop_front();//1 0
	lst.insert(lst.begin(), 9);//9 1 0
	lst.insert(++lst.begin(), 2, 8);//9 8 8 1 0
	lst.insert(lst.end(), lst.begin(), lst.end());//9 8 8 1 0 9 8 8 1 0
	lst.remove(8);//9 1 0 9 1 0
	lst.erase(lst.begin());//1 0 9 1 0
	lst.erase(++lst.begin(), --lst.end());//1 0
	lst.clear();
```

> ***list*** 的迭代器是**双向迭代器**，所以不可以在 ***lst.begin()*** 上 ***+ n*** 操作来跳转位置，也就是**不支持随机访问**

#### 3.6.6 list数据存取

**函数原型：**

+ ``front();//返回第一个元素``
+ ``back();//返回最后一个元素``

```c++
	list<int> lst;
	for (int i = 0; i < 2; i++) {
		lst.push_back(i);
		lst.push_front(i + 1);
	}//2 1 0 1

	cout << lst.back() << endl;//1
	cout << lst.front() << endl;//2
```

> ***list*** 不支持用 **[]** 和 **.at()** 访问内部元素

#### 3.6.7 list反转和排序

**功能描述：**将容器中的元素反转，以及将容器中的数据进行排序

**函数原型：**

+ ``reverse();//反转链表``
+ ``sort();//链表按升序排序``

```c++
bool myCompare(int num1, int num2) {
	//想要降序就写一个自己的比较函数，返回 num1 > num2
	return num1 > num2;
}

int main() {
	list<int> lst;
	for (int i = 0; i < 10; i++) {
		lst.push_back(rand() % 10);
	}//1 7 4 0 9 4 8 8 2 4

	lst.reverse();// 4 2 8 8 4 9 0 4 7 1
	lst.sort();//0 1 2 4 4 4 7 8 8 9
	//降序：将函数名放进形参列表即可
	lst.sort(myCompare);//9 8 8 7 4 4 4 2 1 0

	system("pause");
	return 0;
}
```

> 反转和排序都是调用 **<list\>** 库自带的成员函数，而不是算法库 **<algorithm\>** 中的算法
>
> 因为所有不支持随机访问迭代器的容器，都不可以使用标准算法库**<algorithm\>** 
>
> 记住上述代码块中利用 **sort** 降序的方法

#### 3.7.8 练习案例

```c++
class Person {
public:
	int age;
	int height;
	string name;
	Person(int age, int height, string name) {
		this->age = age;
		this->height = height;
		this->name = name;
	}
};

void myPrint(const list<Person>& lst) {
	for (Person p : lst) {
		cout << "姓名：" << p.name << "\t年龄：" << p.age << "\t身高：" << p.height << endl;
	}
	cout << endl;
}

bool ageCompare(Person& p1, Person& p2) {
	if (p1.age == p2.age) {
		return p1.height > p2.height;
	}
	return p1.age < p2.age;
}

int main() {
	list<Person> lst;
	string nameSeed = "ABCDEabcde";
	//生成十个人
	for (int i = 0; i < 10; i++) {
		int age = rand() % 10 + 10;
		int height = rand() % 20 + 150;
		string name = "";
		name += nameSeed[i];
		lst.push_back(Person(age, height, name));
	}
	myPrint(lst);//打印当前链表

	lst.sort(ageCompare);
	myPrint(lst);//打印当前链表

	system("pause");
	return 0;
}
```

> 对自定义类型的链表需要指定排序规则

### 3.7 set/multiset 容器

#### 3.7.1 set基本概念

**概念：**所有元素在插入容器时都==自动被排序==

**本质：**set/multiset属于**关联式容器**，底层结构是用**二叉树**实现的

**区别：**

+ set不允许容器中有重复的元素
+ multiset允许容器中有重复的元素

#### 3.7.2 set构造和赋值

**构造函数原型：**

+ ``set<T> st;//默认构造函数``
+ ``set(const set &st);//拷贝构造函数``

**赋值函数原型：**

+ ``set& operator=(const set &st);//重载=``

```c++
	set<int> st1, st3;
	set<int> st2(st1);
	st3 = st1;
```

#### 3.7.3 set大小和交换

**函数原型：**

+ ``size();//返回容器中元素的数目``
+ ``empty();//判断容器是否为空``
+ ``swap();//交换两个容器的内容``

```c++
	set<int> st1, st2;
	if (st1.empty()) {
		cout << "st1 is empty now!" << endl;
	}
	st1.insert(10);
	st1.insert(50);
	st1.insert(30);
	cout << "size of st1: " << st1.size() << endl;
	st2.insert(1);
	st2.insert(3);
	st1.swap(st2);
```

> set 容器不允许 resize 函数来重新指定大小，因为容器变大会导致填充许多默认值，而 set 容器不允许重复值的出现

#### 3.7.4 set插入和删除

**函数原型：**

+ ``insert(elem);//在容器中插入元素elem``
+ ``erase(const_iterator pos);//删除pos位置的元素``
+ ``erase(const_iterator begin, const_iterator end);//删除[begin, end)区间内的元素``
+ ``erase(elem);//删除容器中所有和elem值相同的元素``
+ ``clear();//清空容器``

```c++
	set<int> st1;
	st1.insert(10);
	st1.insert(90);
	st1.insert(80);
	st1.insert(50);//10 50 80 90 
	st1.erase(++st1.begin());//10 80 90
	st1.erase(10);//80 90
	st1.erase(st1.begin(), --st1.end());//90
	st1.clear();//nothing
```

#### 3.7.5 set查找和统计

**函数原型：**

+ ``find(key);//查找key是够存在，找到则返回其地址，若不存在则返回set.end();``
+ ``count(key);//统计key元素的个数``·

```c++
	set<int> st1;
	st1.insert(10);
	st1.insert(30);
	st1.insert(20);
	st1.insert(15);
	st1.insert(15);
	set<int>::iterator it1 = st1.find(80);
	set<int>::iterator it2 = st1.find(30);

	if (it1 != st1.end()) { cout << *it1 << endl; }
	else { cout << "not found" << endl; }//not found

	if (it2 != st1.end()) { cout << *it2 << endl; }
	else { cout << "not found" << endl; }//30

	int count = st1.count(15);
	cout << "count of 15: " << count << endl;//count of 15: 1, 因为set中元素不可重复
```

#### 3.7.6 set和multiset的区别

+ set不可以插入重复的数据，而multiset可以
+ set插入数据的同时会返回插入结果，表示插入是否成功
+ multiset不会检测数据，因此可以重复插入

```c++
	set<int> st1;
	//set.insert() 插入语句的返回值是一个对组，包括 插入地址的迭代器 和 是否插入成功 两个数据
	pair<set<int>::iterator, bool> result1 = st1.insert(10);
	pair<set<int>::iterator, bool> result2 = st1.insert(10);
	if (result1.second == true) { cout << "第一次插入成功！" << endl; }
	else { cout << "第一次插入失败！" << endl; }//第一次插入成功
	if (result2.second == true) { cout << "第二次插入成功！" << endl; }
	else { cout << "第二次插入失败！" << endl; }//第二次插入失败

	multiset<int> multist;
	//multiset.insert() 插入语句的返回值是一个 插入地址的迭代器
	multiset<int>::iterator result3 = multist.insert(10);
	multiset<int>::iterator result4 = multist.insert(10);
	if (result3 != result4) {
		cout << "插入成功" << endl;
	}//插入成功
```

#### 3.7.7 pair对组的创建

**功能描述：**成对出现的数据，利用对组可以返回两个数据

**两种创建方式：**

+ ``pair<type1, type2> p(value1, value2);``
+ ``pair<type1, type2> p = make_pair(value1, value2);``

```c++
	pair<string, int> p1("Kallen", 18);
	pair<string, int> p2 = make_pair("C.C.", 19);

	cout << p1.first << "\t" << p1.second << endl;
	cout << p2.first << "\t" << p2.second << endl;
```

#### 3.7.8 set容器的排序规则

**含义：**set容器的默认排序规则是从小到大，利用仿函数则可以改变排序规则

+ 改变内置数据类型的排序方式

```c++
//利用仿函数改变排序规则
class MyCompare {
public:
	bool operator()(int num1, int num2) const{
		return num1 > num2;
	}
};

void myPrint(const set<int>& st) {
	for (set<int>::const_iterator it = st.begin(); it != st.end(); it++) {
		cout << *it << " ";
	}
	cout << endl;
}

void myPrint2(const set<int, MyCompare>& st) {
	for (set<int, MyCompare>::const_iterator it = st.begin(); it != st.end(); it++) {
		cout << *it << " ";
	}
	cout << endl;
}

int main() {
	set<int> st1;
	st1.insert(10);
	st1.insert(50);
	st1.insert(30);
	st1.insert(70);
	myPrint(st1);//10 30 50 70

	//若想改变排序规则，则需要在插入前就声明，即创建set容器处
	set<int, MyCompare> st2;
	st2.insert(10);
	st2.insert(50);
	st2.insert(30);
	st2.insert(70);
	myPrint2(st2);//70 50 30 10

	system("pause");
	return 0;
}
```

+ 指定自定义数据类型在set容器中的排序方式

```c++
class Person {
public:
	Person(string name, int age) {
		this->age = age;
		this->name = name;
	}
	//show()函数必须写const关键字
	void show() const{
		cout << "name:" << this->name << "\tage:" << this->age << endl;
	}
	string name;
	int age;
};

class MyCompare {
public:
	bool operator()(Person p1, Person p2) const {
		return p1.age > p2.age;
	}
};

//因为本函数的形参是const，所以调用类的成员函数时只能调用含const关键字的函数
void myPrint(const set<Person, MyCompare>& st) {
	for (set<Person, MyCompare>::iterator it = st.begin(); it != st.end(); it++) {
		it->show();
	}
}

int main() {
	Person kallen("Kallen", 18);
	Person cc("C.C.", 19);
	Person lelouch("Lelouch", 20);
	Person euphemia("euphemia", 16);

	set<Person, MyCompare> st1;
	st1.insert(cc);
	st1.insert(lelouch);
	st1.insert(euphemia);
	st1.insert(kallen);

	myPrint(st1);

	system("pause");
	return 0;
}
```

### 3.8 map/multimap容器

#### 3.8.1 map基本概念

**简介：**

+ map中所有元素都是pair
+ pair中第一个元素为key（键），起到索引作用，第二个元素为value（值）
+ 所有元素都会根据元素的键值自动排序

**本质：**map/multimap都是**关联式容器**，底层结构是用二叉树实现

**优点：**可以根据key值快速找到value

**map/multimap区别：**

+ map不允许容器中有重复的key值
+ multimap允许容器中有重复的key值

#### 3.8.2 map构造和赋值

**函数原型：**

+ ``map<T1, T2> mp;//默认构造``
+ ``map<T1, T2> mp(const map& mp);//拷贝构造``

**赋值：**

+ ``map& operator=(const map& mp);//重载=``

```c++
	map<int, string> mp1;
	pair<int, string> p1(18, "Kallen");
	pair<int, string> p2(19, "C.C.");

	mp1.insert(p1);
	map<int, string> mp2(mp1);
	mp2.insert(p2);
	mp1 = mp2;
```

#### 3.8.3 map大小和交换

**函数原型：**

+ ``size();//返回容器中元素的个数``
+ ``empty();//判断容器是否为空``
+ ``swap(st);//交换两个容器``

```c++
	map<int, string> mp1, mp2;
	if (mp1.empty()) {
		cout << "mp1 is empty now!" << endl;
	}
	mp1.insert(pair<int, string>(1 	8, "Kallen"));
	mp1.insert(pair<int, string>(19, "C.C."));
	cout << mp1.size() << endl;//2
	mp1.swap(mp2);
	if (!mp2.empty()) {
		cout << "size of mp2:" << mp2.size() << endl;//size of mp2: 2
		cout << "size of mp1:" << mp1.size() << endl;//size of mp1: 0
	}
```

#### 3.8.4 map插入和删除

**函数原型：**

+ ``insert(elem);//插入元素``
+ ``clear();//清空``
+ ``erase(const_iterator pos);//删除迭代器pos指向的元素，返回下一个元素的迭代器``
+ ``erase(const_iterator begin, const_iterator end);//删除区间[begin, end)内的元素，返回下一个元素的迭代器``
+ ``erase(key);//删除容器中值为key的元素``

```c++
	map<int, string> mp1;
	mp1.insert(pair<int, string>(18, "Kallen"));
	mp1.insert(pair<int, string>(20, "Lelouch"));
	mp1.insert(pair<int, string>(19, "C.C."));
	mp1.erase(mp1.begin());//删除key=18
	mp1.erase(19);//删除key=19
	mp1.erase(mp1.begin(), mp1.end());//删除所有
	mp1.insert(make_pair(21, "Koziki"));//插入的第二种格式
	mp1.clear();
```

> 插入语句还有一种形式是``mp1[key] = value;``，但不建议使用，可能会导致意外创建不需要的数据
>
> 但可以利用中括号加key值的方式访问value

#### 3.8.5 map查找和统计

**函数原型：**

+ ``find(key);//查找key是否存在，存在则返回该元素的迭代器；不存在则返回map.end();``
+ ``count(key);//统计key元素的个数``

```c++
int main() {
	map<int, string> mp;
	mp.insert(make_pair(1, "A"));
	mp.insert(make_pair(2, "B"));
	mp.insert(make_pair(3, "C"));

	map<int, string>::iterator it = mp.find(1);
	if (it != mp.end())
		cout << "存在" << endl;
	
	int result = mp.count(2);
	cout << result << endl;

	system("pause");
	return 0;
}
```

> 对map的count统计而言，count只可能是1或者0

#### 3.8.6 map容器排序

==利用仿函数改变map容器中的排序规则==

+ 默认数据类型的排序

```c++
class MyCompare {
public:
	bool operator()(int num1, int num2) const {
		return num1 > num2;
	} 
};

int main() {
	map<int, string> mp1;
	mp1.insert(pair<int, string>(18, "Kallen"));
	mp1.insert(pair<int, string>(20, "Lelouch"));
	mp1.insert(pair<int, string>(19, "C.C."));
	for (map<int, string>::const_iterator it = mp1.begin(); it != mp1.end(); it++) {
		cout << (*it).first << "\t" << it->second << endl;
	}

	map<int, string, MyCompare> mp2;
	mp2.insert(pair<int, string>(18, "Kallen"));
	mp2.insert(pair<int, string>(20, "Lelouch"));
	mp2.insert(pair<int, string>(19, "C.C."));
	for (map<int, string, MyCompare>::iterator it = mp2.begin(); it != mp2.end(); it++) {
		cout << (*it).first << "\t" << it->second << endl;
	}

	system("pause");
	return 0;
}
```

+ 自定义数据类型的排序

```c++
class Person {
public:
	int age;
	string name;
	Person(int age, string name) {
		this->age = age;
		this->name = name;
	}
};

class MyCompare {
public:
	bool operator()(Person p1, Person p2) const {
		return p1.age > p2.age;
	} 
};

int main() {
	map<Person, int, MyCompare> mp;
	Person kallen(18, "Kallen");
	Person lelouch(20, "Lelouch");
	Person cc(19, "C.C.");
	Person euphemia(16, "Euphemia");

	mp.insert(pair<Person, int>(kallen, 0));
	mp.insert(pair<Person, int>(lelouch, 3));
	mp.insert(pair<Person, int>(cc, 1));
	mp.insert(pair<Person, int>(euphemia, 2));

	for (map<Person, int, MyCompare>::iterator it = mp.begin(); it != mp.end(); it++) {
		cout << it->second << ":" << it->first.age << "\t" << it->first.name << endl;
	}

	system("pause");
	return 0;
}
```

> MyCompare实现仿函数时，重载的operator=不能传引用，且在函数声明末尾需要加const

## 4 STL-函数对象

### 4.1 函数对象

#### 4.1.1 函数对象的概念

**概念：**重载**函数调用操作符**的类，其对象通常称为**函数对象**，也叫做**仿函数**

**本质：**函数对象（仿函数）是一个类，不是一个函数

#### 4.1.2 函数对象的使用

**特点：**

+ 函数对象在使用时，可以像普通函数那样调用，有==参数==也有==返回值==
+ 函数对象超出普通函数的概念，可以用自己的状态
+ 函数对象可以作为参数传递

```c++
class MyAdd {
public:
	int operator()(int num1, int num2) {
		return num1 + num2;
	}
};

class MyPrint {
public:
	void operator()(string text) {
		count++;
		cout << text << endl;
		cout << "调用了" << count << "次" << endl;
	}
	int count = 0;//记录这个仿函数调用了多少次,如果是普通函数，则需要一个全局变量或者静态变量来实现
};

void test(MyPrint& myPrint, string text) {
	myPrint(text);
}

int main() {
	//1.可以有参数也可以用返回值
	MyAdd myAdd;
	int result = myAdd(329, 1128);
	cout << result << endl;
	//2.函数对象也可以有自己的状态
	MyPrint myPrint;
	myPrint("鲁路修");
	myPrint("卡莲");
	//3.函数对象也可以作为形参传递
	test(myPrint, "C.C.");

	system("pause");
	return 0;
}
```

### 4.2 谓词

#### 4.2.1 谓词概念

**概念：**

+ 返回bool类型的仿函数称为谓词
+ 如果``operator()``接收一个参数，则称为一元谓词
+ 如果``operator()``接收两个参数，则称为二元谓词

#### 4.2.2 一元谓词

```c++
class MyCompare {
public:
	bool operator()(int num) {
		return num > 5;
	}
};

int main() {
	vector<int> v;
	for (int i = 0; i < 10; i++) {
		v.push_back(i);
	}
	vector<int>::iterator it = find_if(v.begin(), v.end(), MyCompare());//传入一个匿名函数对象
	cout << *it << endl;

	system("pause");
	return 0;
}
```

#### 4.2.3 二元谓词

```c++
class MyCompare {
public:
	bool operator()(int num1, int num2) {
		return num1 > num2;
	}
};

void print(vector<int>& v) {
	for (int num : v) {
		cout << num << " ";
	}
	cout << endl;
}

int main() {
	vector<int> v;
	for (int i = 0; i < 5; i++) {
		v.push_back(rand() % 10);
	}
	print(v);//1 7 4 0 9
	sort(v.begin(), v.end());
	print(v);//0 1 4 7 9
	sort(v.begin(), v.end(), MyCompare());
	print(v);//9 7 4 1 0

	system("pause");
	return 0;
}
```

> 函数形参列表中的 ***pred*** 代表的就是谓词

### 4.3 内建函数对象

#### 4.3.1 内建函数对象的意义

**概念：**STL内建了一些函数对象

**分类：**

+ 算术仿函数
+ 关系仿函数
+ 逻辑仿函数

**用法：**

+ 这些仿函数所产生的对象，用法和一般函数完全相同
+ 使用内建函数对象需要引入头文件``#include <functional>`` 

#### 4.3.2 算术仿函数

**功能描述：**

+ 实现四则运算
+ 其中negate是一元运算，其他都是二元运算

**仿函数原型：**

+ ``template<class T> T plus<T> //加法仿函数``
+ ``template<class T> T minus<T> //减法仿函数``
+ ``template<class T> T multiplies<T> //乘法仿函数``
+ ``template<class T> T divides<T> //除法仿函数``
+ ``template<class T> T modulus<T> //取模仿函数``
+ ``template<class T> T negate<T> //取反仿函数``

```c++
	negate<int> n;
	cout << n(329) << endl;//-329
	plus<int> p;
	cout << p(329, 1128) << endl;//1457
	minus<int> mi;
	cout << mi(1128, 329) << endl;//799
	multiplies<int> mu;
	cout << mu(329, 1128) << endl;//371112
	divides<int> d;
	cout << d(1128, 329) << endl;//3
	modulus<int> mo;
	cout << mo(1128, 329) << endl;//141
```

#### 4.3.3 关系仿函数 

**仿函数原型：**

+ ``template<class T> bool equal_to<T> //等于``
+ ``template<class T> bool not_equal_to<T> //不等于``
+ ``template<class T> bool greater<T> //大于``
+ ``template<class T> bool greater_equal<T> //大于等于``
+ ``template<class T> bool less<T> //小于``
+ ``template<class T> bool less_equal<T> //等于``

```c++
void print(vector<int>& v) {
	for (int num : v) {
		cout << num << " ";
	}
	cout << endl;
}

int main() {
	vector<int> v;
	for (int i = 0; i < 5; i++) {
		v.push_back(rand() % 10);
	}
	print(v);//1 7 4 0 9
	sort(v.begin(), v.end());
	print(v);//0 1 4 7 9
	sort(v.begin(), v.end(), greater<int>());
	print(v);//9 7 4 1 0

	system("pause");
	return 0;
}
```

> 上述代码中只展示了``greater``的用法，其他的也类似

#### 4.3.4 逻辑仿函数

**仿函数原型：**

+ ``template<class T> bool logical_and<T> //与运算`` 
+ ``template<class T> bool logical_or<T> //或运算`` 
+ ``template<class T> bool logical_not<T> //非运算`` 

```c++
	logical_and<bool> a;
	cout << a(true, false) << endl;//0
	cout << a(true, true) << endl;//1
	logical_or<bool> o;
	cout << o(true, false) << endl;//1
	cout << o(false, false) << endl;//0
	logical_not<int> n;
	cout << n(true) << endl;//0
```

```c++
//容器搬运并取反示例
void print(vector<bool>& v) {
	for (bool a : v) {
		cout << a << " ";
	}
	cout << endl;
}

int main() {
	vector<bool> v1;
	v1.push_back(true);
	v1.push_back(false);
	v1.push_back(false);
	v1.push_back(true);
	print(v1);

	vector<bool> v2;
	v2.resize(v1.size());
	transform(v1.begin(), v1.end(), v2.begin(), logical_not<int>());
	print(v2);

	system("pause");
	return 0;
}
```

## 5 STL-常用算法

**概述：**

+ 算法主要是由头文件``<algorithm>`` ``<functional>`` ``<numeric>`` 包含
+ ``<algorithm>``是所有STL头文件中最大的一个，涉及范围包括比较、交换、查找、遍历、复制、修改等等
+ ``<functional>``定义了一些模板类，以及声明函数对象
+ ``<numeric>``体积很小，只包括几个在序列上进行简单数学运算的模板函数

### 5.1 常用遍历算法

**算法：**

+ ``for_each //遍历算法``
+ ``transform //搬运算法``

#### 5.1.1 for_each

**功能描述：**实现遍历容器

**函数原型：**

+ ``for_each(iterator begin, iterator end, _Func);``

```c++
//for_each 的形参列表包括：
//begin: 遍历的起始迭代器
//end: 遍历的结束迭代器
//_Func: 对遍历出的数据内容要进行何种操作（一般来说是传入一个函数名，可以是普通函数或者仿函数）

//普通函数
void print(int num) {
	cout << num << " ";
}

//仿函数
class MyPrint {
public:
	void operator()(int num) {
		cout << num << " ";
	}
};

int main() {
	vector<int> v;
	for (int i = 0; i < 10; i++) {
		v.push_back(i);
	}

	for_each(v.begin(), v.end(), print);//传入普通函数
	cout << endl;
	for_each(v.begin(), v.end(), MyPrint());//传入匿名函数对象

	system("pause");
	return 0;
}
```

#### 5.1.2 transform

**功能描述：**搬运容器到另一个容器中

**函数原型：**

+ ``transform(iterator begin1, iterator end1, iterator begin2, _Func);``

```c++
//transform 的形参列表包括：
//begin1: 源容器的起始迭代器
//end1: 源容器的结束迭代器
//begin2: 目标容器的起始迭代器
//_Func: 搬运过程中要进行何种操作（传入函数名，可以是普通函数或者仿函数）


//普通函数
int doubleNum(int num) {
	return num;
}

//仿函数
class Double {
public:
	int operator()(int num) {
		return num * 2;
	}
};

void print(vector<int>& v) {
	for (int num : v) {
		cout << num << " ";
	}
	cout << endl;
}

int main() {
	vector<int> v1, v2, v3;
	for (int i = 0; i < 3; i++) {
		v1.push_back(i);
	}
	v2.resize(v1.size());
	v3.resize(v1.size());
	transform(v1.begin(), v1.end(), v2.begin(), Double());//传入仿函数
	transform(v1.begin(), v1.end(), v3.begin(), doubleNum);//传入普通函数

	print(v1);//0 1 2
	print(v2);//0 2 4 
	print(v3);//0 1 2

	system("pause");
	return 0;
}
```

> 目标容器只能用``resize()``拓展空间，而不能用``reserve()``

### 5.2 常用查找算法

**算法：**

+ ``find //查找元素``
+ ``find_if //按条件查找元素``
+ ``adjacent_find //查找相邻重复元素``
+ ``binary_search //二分查找法``
+ ``count //统计元素个数``
+ ``count_if //按条件统计元素个数``

#### 5.2.1 find

**功能描述：**查找指定元素，找到则返回指定元素的迭代器，否则返回``end()``

**函数原型：**

+ ``find(iterator begin, iterator end, elem);``

```c++
//find 的形参列表包括：
//begin: 查找范围的起始迭代器
//end: 查找范围的结束迭代器
//elem: 需要查找的元素

class Person {
public:
	string name;
	int age;
	Person(string name, int age) {
		this->name = name;
		this->age = age;
	}
	bool operator==(const Person& p) {
		if (this->name == p.name && this->age == p.age)
			return true;
		return false;
	}
};

int main() {
	//操纵默认数据类型
	vector<int> v1;
	for (int i = 0; i < 10; i++) {
		v1.push_back(i);
	}
	vector<int>::iterator it1 = find(v1.begin(), v1.end(), 2);
	cout << *it1 << endl;//2
	it1 = find(v1.begin(), v1.end(), 11);
	if (it1 == v1.end())
		cout << "Not Found!" << endl;//Not Found

	//操纵自定义数据类型
	vector<Person> v2;
	Person p1("Kallen", 18);
	Person p2("C.C.", 19);
	Person p3("Lelouch", 18);
	Person p4("Euphemia", 16);
	v2.push_back(p1);
	v2.push_back(p2);
	v2.push_back(p3);
	v2.push_back(p4);
	vector<Person>::iterator it2 = find(v2.begin(), v2.end(), p1);
	cout << it2->name << " " << it2->age << endl;

	system("pause");
	return 0;
}
```

> 利用``find``查找自定义数据类型时，需要在自定义数据类型中重载``==``，切记重载时**传引用**应该加``const``关键字

#### 5.2.2 find_if

**功能描述：**按条件查找元素，找到则返回指定元素的迭代器，否则返回``end()``

**函数原型：**

+ ``find_if(iterator begin, iterator end, _Pred);``

```C++
//find_if 的形参列表包括：
//begin: 查找范围的起始迭代器
//end: 查找范围的结束迭代器
//_Pred: 查找条件的谓词或者普通函数（返回值为bool类型）

class Person {
public:
	string name;
	int age;
	Person(string name, int age) {
		this->name = name;
		this->age = age;
	}
};

//普通函数（默认数据类型）
bool biggerTwo(int num) {
	return num > 2;
}

//仿函数（默认数据类型）
class BiggerFive {
public:
	bool operator()(int num) const {
		return num > 5;
	}
};

//普通函数（自定义数据类型）
bool underage(const Person& p) {
	if (p.age < 18)
		return true;
	return false;
}

//仿函数（自定义数据类型）
class Audlt {
public:
	bool operator()(Person& p)const {
		if (p.age > 18)
			return true;
		return false;
	}
};

int main() {
	//按条件查找默认数据类型
	vector<int> v1;
	for (int i = 0; i < 10; i++) {
		v1.push_back(i);
	}
	vector<int>::iterator it1;
	it1 = find_if(v1.begin(), v1.end(), biggerTwo);//传入普通函数
	cout << *it1 << endl;//3
	it1 = find_if(v1.begin(), v1.end(), BiggerFive());//传入仿函数
	cout << *it1 << endl;//6

	//按条件查找自定义数据类型
	vector<Person> v2;
	v2.push_back(Person("Kallen", 18));
	v2.push_back(Person("C.C.", 19));
	v2.push_back(Person("Lelouch", 18));
	v2.push_back(Person("Euphemia", 16));
	vector<Person>::iterator it2;
	it2 = find_if(v2.begin(), v2.end(), underage);//传入普通函数
	cout << it2->name << endl;//Euphemia
	it2 = find_if(v2.begin(), v2.end(), Audlt());//传入仿函数
	cout << it2->name << endl;//C.C.

	system("pause");
	return 0;
}
```

#### 5.2.3 adjacent_find

**功能描述：**查找相邻的重复元素，找到则返回相邻元素第一个位置的迭代器，否则返回``end()``

**函数原型：**

+ ``adjacent_find(iterator begin, iterator end);//``

```c++
//adjacent_find 的形参列表包括：
//begin: 查找范围的起始迭代器
//end: 查找范围的结束迭代器

class Person {
public:
	string name;
	int age;
	Person(string name, int age) {
		this->name = name;
		this->age = age;
	}
	bool operator==(const Person& p) {
		if (this->age == p.age && this->name == p.name)
			return true;
		return false;
	}
};

int main() {
	//按条件查找默认数据类型
	vector<int> v1;
	for (int i = 0; i < 10; i++) {
		v1.push_back(i);
	}
	vector<int>::iterator it1;
	it1 = adjacent_find(v1.begin(), v1.end());
	if (it1 == v1.end())
		cout << "NOT FOUND" << endl;
	v1[5] = v1[6];
	it1 = adjacent_find(v1.begin(), v1.end());
	cout << *it1 << endl;//6

	//按条件查找自定义数据类型
	vector<Person> v2;
	v2.push_back(Person("Kallen", 18));
	v2.push_back(Person("C.C.", 19));
	v2.push_back(Person("Lelouch", 18));
	v2.push_back(Person("Lelouch", 18));
	vector<Person>::iterator it2;
	it2 = adjacent_find(v2.begin(), v2.end());
	cout << it2->name << endl;//Lelouch

	system("pause");
	return 0;
}
```

#### 5.2.4 binary_search

**功能描述：**查找指定元素是否存在，找到返回**true**，否则返回**false**

**函数原型：**

+ ``bool binary_search(iterator begin, iterator end, elem);//``

```c++
//binary_search 的形参列表包括：
//begin: 查找范围的起始迭代器
//end: 查找范围的结束迭代器
//elem: 需要查找的值

int main() {
	vector<int> v1;
	for (int i = 0; i < 10; i++) {
		v1.push_back(i);
	}
	bool result = binary_search(v1.begin(), v1.end(), 11);
	cout << result << endl;//0
	result = binary_search(v1.begin(), v1.end(), 9);
	cout << result << endl;//1

	system("pause");
	return 0;
}
```

> ``binary_search``在**无序序列**中不可用，如果是**无序序列**，结果未知
>
> 同时，**有序序列**必须是**==升序==**才可以正确查找，否则结果依旧未知
>
> ``find`` 和``binary_search``的区别是返回不同，前者返回迭代器，后者返回布尔值
>
> 自定义数据类型的查找类似前文，需要重载``==``，不再赘述

#### 5.2.5 count

**功能描述：**

+ 统计元素的个数

**函数原型：**

+ ``count(iterator begin, iterator end, elem);``

```c++
//count 的形参列表包括：
//begin: 统计范围的起始迭代器
//end: 统计范围的结束迭代器
//elem: 需要统计的值

int main() {
	vector<int> v1;
	for (int i = 0; i < 10; i++) {
		v1.push_back(2);
	}
	cout << count(v1.begin(), v1.end(), 2) << endl;//10
	cout << count(v1.begin(), v1.end(), 5) << endl;//0

	system("pause");
	return 0;
}
```

#### 5.2.6 count_if

**功能描述：**按条件统计元素个数

**函数原型：**

+ ``count(iterator begin, iterator end, _Pred);``

```c++
//count_if 的形参列表包括：
//begin: 统计范围的起始迭代器
//end: 统计范围的结束迭代器
//_Pred: 统计条件（传入谓词，仿函数或者普通函数实现）

class Person {
public:
	string name;
	int age;
	Person(string name, int age) {
		this->name = name;
		this->age = age;
	}
};

//普通函数（内置数据类型）
bool biggerTwo(int num) {
	return num > 2;
}

//仿函数（内置数据类型）
class BiggerFive {
public:
	bool operator()(int num) {
		return num > 5;
	}
};

//仿函数（自定义数据类型）
class Adult {
public:
	bool operator()(const Person& p) {
		return p.age > 18;
	}
};

//普通函数（自定义数据类型）
bool underage(const Person& p) {
	return p.age < 18;
}

int main() {
	//统计内置数据类型
	vector<int> v1;
	for (int i = 0; i < 10; i++) {
		v1.push_back(i);
	}
	//传入普通函数
	cout << count_if(v1.begin(), v1.end(), biggerTwo) << endl;//7
	//传入仿函数
	cout << count_if(v1.begin(), v1.end(), BiggerFive()) << endl;//4

	//统计自定义数据类型
	vector<Person> v2;
	v2.push_back(Person("Kallen", 22));
	v2.push_back(Person("Lelouch", 22));
	v2.push_back(Person("Euphemia", 16));
	v2.push_back(Person("C.C.", 23));
	v2.push_back(Person("Suzaku", 17));
	//传入普通函数
	cout << count_if(v2.begin(), v2.end(), underage) << endl;//2
	//传入仿函数
	cout << count_if(v2.begin(), v2.end(), Adult()) << endl;//3

	system("pause");
	return 0;
}
```

### 5.3 常用排序算法

**算法：**

+ ``sort //对容器内元素进行排序``
+ ``random_shuffle //洗牌，指定范围内的元素随机调整次序``
+ ``merge //容器元素合并，并存储到另一容器中``
+ ``reverse //反转指定范围的元素``

#### 5.3.1 sort

**功能描述：**对容器内元素进行排序，**默认是升序**，若想降序则利用谓词实现

**函数原型：**

+ ``sort(iterator begin, iterator end, _Pred);``

```c++
//sort 的形参列表包括：
//begin: 排序范围的起始迭代器
//end: 排序范围的结束迭代器
//_Pred: 排序条件（传入谓词，仿函数或者普通函数实现），若不传入谓词，则默认升序

//普通函数
bool upSort(int num1, int num2) {
	return num1 > num2;
}

//仿函数
class UpSort {
public:
	bool operator()(int num1, int num2) {
		return num1 > num2;
	}
};

void print(const vector<int>& v) {
	for (int num : v) {
		cout << num << " ";
	}
	cout << endl;
}

int main() {
	vector<int> v1;
	for (int i = 0; i < 10; i++) {
		v1.push_back(rand() % 10);
	}
	print(v1);//1 7 4 0 9 4 8 8 2 4

	sort(v1.begin(), v1.end());//不传谓词，默认升序
	print(v1);//0 1 2 4 4 4 7 8 8 9

	sort(v1.begin(), v1.end(), UpSort());//传入自定义仿函数
	print(v1);//9 8 8 7 4 4 4 2 1 0

	sort(v1.begin(), v1.end(), less<int>());//传入默认仿函数
	print(v1);//0 1 2 4 4 4 7 8 8 9

	sort(v1.begin(), v1.end(), upSort);//传入普通函数
	print(v1);//9 8 8 7 4 4 4 2 1 0
	
	system("pause");
	return 0;
}
```

#### 5.3.2 random_shuffle

**功能描述：**指定范围内的元素随机调整次序

**函数原型：**

+ ``random_shuffle(iterator begin, iterator end);``

```c++
//random_shuffle 的形参列表包括：
//begin: 洗牌范围的起始迭代器
//end: 洗牌范围的结束迭代器

void print(int num) {
	cout << num << " ";
}

int main() {
	vector<int> v1;
	for (int i = 0; i < 5; i++) {
		v1.push_back(rand() % 10);
	}
	for_each(v1.begin(), v1.end(), print);
	cout << endl;//1 7 4 0 9
	random_shuffle(v1.begin(), v1.end());
	for_each(v1.begin(), v1.end(), print);
	cout << endl;//4 1 9 7 0
	
	system("pause");
	return 0;
}
```

#### 5.3.3 merge

**功能描述：**两个容器的元素合并，存储到另一个容器中，使用``merge``的前提是两个容器必须是**有序的**

**函数原型：**

+ ``merge(iterator begin1, iterator end1, iterator begin2, iterator end2, iterator dest);``
+ ``merge(iterator begin1, iterator end1, iterator begin2, iterator end2, iterator dest, _Pred);``

```c++
//merge 的形参列表包括：
//begin1: 容器1的起始迭代器
//end1: 容器1的结束迭代器
//begin2: 容器2的起始迭代器
//end2: 容器2的结束迭代器
//dest: 目标容器的开始迭代器
//_Pred: 谓词，指定合并规则（如降序）

void print(int num) {
	cout << num << " ";
}

int main() {
	vector<int> v1, v2, v3, v4;
	for (int i = 0; i < 5; i++) {
		v1.push_back(i);
		v2.push_back(i + 2);
	}//此时v1和v2是升序
	
	v3.resize(v1.size() + v2.size());
	merge(v1.begin(), v1.end(), v2.begin(), v2.end(), v3.begin());
	for_each(v3.begin(), v3.end(), print);//0 1 2 2 3 3 4 4 5 6
	cout << endl;
	
	reverse(v1.begin(), v1.end());//反转v1
	reverse(v2.begin(), v2.end());//反转v2
	//此时v1和v2是降序
	v4.resize(v1.size() + v2.size());
	merge(v1.begin(), v1.end(), v2.begin(), v2.end(), v4.begin(), greater<int>());//想要合并降序的容器，必须指定谓词
	for_each(v4.begin(), v4.end(), print);//6 5 4 4 3 3 2 2 1 0
	cout << endl;
	
	system("pause");
	return 0;
}
```

> 默认情况下``merge``只能合并**升序**的两个容器，且合并后的容器依旧是**升序**
>
> 若想要合并降序的容器，则需要指定**谓词**

#### 5.3.4 reverse

**功能描述：**将容器内的元素反转

**函数原型：**

+ ``reverse(iterator begin, iterator end);``

```c++
//reverse 的形参列表包括：
//begin: 反转范围的起始迭代器
//end: 反转范围的结束迭代器

void print(int num) {
	cout << num << " ";
}

int main() {
	vector<int> v1;
	for (int i = 0; i < 5; i++) {
		v1.push_back(i);
	}
	for_each(v1.begin(), v1.end(), print);//0 1 2 3 4 
	cout << endl;
	reverse(v1.begin(), v1.end());
	for_each(v1.begin(), v1.end(), print);//4 3 2 1 0 
	cout << endl;

	system("pause");
	return 0;
}
```

### 5.4 常用拷贝算法

**算法：**

+ ``copy //将容器指定范围内的元素拷贝到另一个容器中``
+ ``replace //将容器指定范围的旧元素替换成新元素``
+ ``replace_if //将容器指定范围内满足条件的元素替换成新元素``
+ ``swap //交换两个容器的元素``

#### 5.4.1 copy

**功能描述：**容器内指定范围的元素拷贝到另一容器中

**函数原型：**

+ ``copy(iterator begin, iterator end, iterator dest);``

```c++
//copy 的形参列表包括：
//begin: 拷贝范围的起始迭代器
//end: 拷贝范围的结束迭代器
//deat: 目标容器的起始迭代器

void print(int num) {
	cout << num << " ";
}

int main() {
	vector<int> v1, v2;
	for (int i = 0; i < 5; i++) {
		v1.push_back(i);
	}
	for_each(v1.begin(), v1.end(), print);//0 1 2 3 4 
	cout << endl;
	
	v2.resize(v1.size());
	copy(v1.begin(), v1.end(), v2.begin());
	for_each(v2.begin(), v2.end(), print);
	cout << endl;

	system("pause");
	return 0;
}
```

> ``transform``和``copy``的区别是前者可以做增强操作，后者只是单纯的拷贝

#### 5.4.2 replace

**功能描述：**将容器内指定范围的旧元素替换成新元素

**函数原型：**

+ ``replace(iterator begin, iterator end, oldvalue, newvalue);``

```c++
//replace 的形参列表包括：
//begin: 替换范围的起始迭代器
//end: 替换范围的结束迭代器
//oldvalue: 旧元素
//newvalue: 新元素

void print(int num) {
	cout << num << " ";
}

int main() {
	vector<int> v1;
	for (int i = 0; i < 5; i++) {
		v1.push_back(i);
	}
	for_each(v1.begin(), v1.end(), print);//0 1 2 3 4 
	cout << endl;
	
	replace(v1.begin(), v1.end(), 2, 200);
	for_each(v1.begin(), v1.end(), print);//0 1 200 3 4 
	cout << endl;

	system("pause");
	return 0;
}
```

#### 5.4.3 replace_if

**功能描述：**将容器范围内的满足条件的元素替换成新元素

**函数原型：**

+ ``replace_if(iterator begin, iterator end, _Pred, newvalue);``

```c++
//replace_if 的形参列表包括：
//begin: 替换范围的起始迭代器
//end: 替换范围的结束迭代器
//_Pred: 谓词（替换条件）
//newvalue: 新元素

void print(int num) {
	cout << num << " ";
}

class MyCompare {
public:
	bool operator()(int num) {
		return num > 2;
	}
};

int main() {
	vector<int> v1;
	for (int i = 0; i < 5; i++) {
		v1.push_back(i);
	}
	for_each(v1.begin(), v1.end(), print);//0 1 2 3 4 
	cout << endl;
	
	replace_if(v1.begin(), v1.end(), MyCompare(), 200);
	for_each(v1.begin(), v1.end(), print);//0 1 2 200 200 
	cout << endl;

	system("pause");
	return 0;
}
```

#### 5.4.4 swap

**功能描述：**替换两个容器内的元素

**函数原型：**

+ ``swap(container c1, container c2);``

```c++
//swap 的形参列表包括：
//c1: 容器1
//c2: 容器2

void print(int num) {
	cout << num << " ";
}

int main() {
	vector<int> v1, v2;
	for (int i = 0; i < 5; i++) {
		v1.push_back(i);
		v2.push_back(10 - i);
	}
	for_each(v1.begin(), v1.end(), print);//0 1 2 3 4 
	cout << endl;
	for_each(v2.begin(), v2.end(), print);//10 9 8 7 6 
	cout << endl;
	swap(v1, v2);
	for_each(v1.begin(), v1.end(), print);//10 9 8 7 6
	cout << endl;
	for_each(v2.begin(), v2.end(), print);//0 1 2 3 4 
	cout << endl;

	system("pause");
	return 0;
}
```

> 交换的两个容器必须是相同类型
>
> ``<algorithm>``中的``swap``和``<vector>``中的``.swap()``功能类似但用法不同

### 5.5 常用算术生成算法

**注意：**需要包含头文件``<numeric>``

**算法：**

+ ``accumulate //计算容器元素累计的总和``
+ ``fill //向容器中添加元素``

####  5.5.1 accumulate

**功能描述：**计算区间内容器元素累计的总和

**函数原型：**

+ ``accumulate(iterator begin, iterator end, value);``

```c++
//accumulate 的形参列表包括：
//begin: 计算范围的起始迭代器
//end: 计算范围的结束迭代器
//value: 计算结果的初始值

int main() {
	vector<int> v1, v;
	for (int i = 0; i < 5; i++) {
		v1.push_back(i);
	}
	int result = accumulate(v1.begin(), v1.end(), 10);
	cout << result << endl;//20

	system("pause");
	return 0;
}
```

#### 5.5.2 fill

**功能描述：**向容器中填充指定元素

**函数原型：**

+ ``fill(iterator begin, iterator end, value);``

```c++
//fill 的形参列表包括：
//begin: 填充范围的起始迭代器
//end: 填充范围的结束迭代器
//value: 填充的值

class MyPrint {
public:
	void operator()(int num) {
		cout << num << " ";
	}
};

int main() {
	vector<int> v1;
	v1.resize(3);
	for_each(v1.begin(), v1.end(), MyPrint());//0 0 0
	cout << endl;
	fill(v1.begin(), v1.end(), 329);
	for_each(v1.begin(), v1.end(), MyPrint());//329 329 329
	cout << endl;	

	system("pause");
	return 0;
}
```

### 5.6 常用的集合算法

**算法：**

+ ``set_intersection //求两个容器的交集``
+ ``set_union //求两个容器的并集``
+ ``set_difference //求两个容器的差集``

> 在不插入**谓词**的情况下，上述三个算法都只能操作==**升序序列**==
>
> 利用重载的版本（在形参列表末尾多插入一个``_Pred``谓词），可以实现对**==降序序列==**的集合算法
>
> 对**降序序列**操作时插入的谓词一般来说是``greater<T>()``，在``<functional>``库中
>
> 上述三个算法的返回值都是集合运算结束后**最后**一个元素的迭代器位置

#### 5.6.1 set_intersection

**功能描述：**求两个容器的并集，放入到目标容器中，==**同时返回一个迭代器指向交集结束的位置**==

**函数原型：**

+ ``set_intersection(iterator begin1, iterator end1, iterator begin2, iterator end2, iterator dest);``

```c++
//set_intersection 的形参列表包括：
//begin1: 容器1的起始迭代器
//end1: 容器1的结束迭代器
//begin2: 容器2的起始迭代器
//end2: 容器2的结束迭代器
//dest: 目标容器的起始迭代器

class MyPrint {
public:
	void operator()(int num) {
		cout << num << " ";
	}
};

int main() {
	vector<int> v1, v2, v3;
	for (int i = 0; i < 5; i++) {
		v1.push_back(i);
		v2.push_back(2 * i);
	}
	for_each(v1.begin(), v1.end(), MyPrint());//0 1 2 3 4
	cout << endl;
	for_each(v2.begin(), v2.end(), MyPrint());//0 2 4 6 8 
	cout << endl;

	v3.resize(min(v1.size(), v2.size()));
	vector<int>::iterator it = set_intersection(v1.begin(), v1.end(), v2.begin(), v2.end(), v3.begin());
	for_each(v3.begin(), it, MyPrint());//0 2 4
	cout << endl;

	system("pause");
	return 0;
}
```

#### 5.6.2 set_union

**功能描述：**求两个集合的并集

**函数原型：**

+ ``set_union(iterator begin1, iterator end1, iterator begin2, iterator end2, iterator dest);``

```c++
//set_union 的形参列表包括：
//begin1: 容器1的起始迭代器
//end1: 容器1的结束迭代器
//begin2: 容器2的起始迭代器
//end2: 容器2的结束迭代器
//dest: 目标容器的起始迭代器

class MyPrint {
public:
	void operator()(int num) {
		cout << num << " ";
	}
};

int main() {
	vector<int> v1, v2, v3;
	for (int i = 0; i < 5; i++) {
		v1.push_back(5 - i);
		v2.push_back(8 - i);
	}
	for_each(v1.begin(), v1.end(), MyPrint());//5 4 3 2 1
	cout << endl;
	for_each(v2.begin(), v2.end(), MyPrint());//8 7 6 5 4 
	cout << endl;

	v3.resize(v1.size() + v2.size());
	vector<int>::iterator it = set_union(v1.begin(), v1.end(), v2.begin(), v2.end(), v3.begin(), greater<int>());
	for_each(v3.begin(), it, MyPrint());//8 7 6 5 4 3 2 1
	cout << endl;

	system("pause");
	return 0;
}
```

> 合并后重复的元素只会保留一份副本

#### 5.6.3 set_difference

**功能描述：**求两个集合的差集

**函数原型：**``set_difference(iterator begin1, iterator end1, iterator begin2, iterator end2, iterator dest);``

```c++
//set_difference 的形参列表包括：
//begin1: 容器1的起始迭代器
//end1: 容器1的结束迭代器
//begin2: 容器2的起始迭代器
//end2: 容器2的结束迭代器
//dest: 目标容器的起始迭代器

class MyPrint {
public:
	void operator()(int num) {
		cout << num << " ";
	}
};

int main() {
	vector<int> v1, v2, v3;
	for (int i = 0; i < 5; i++) {
		v1.push_back(5 - i);
		v2.push_back(8 - i);
	}
	for_each(v1.begin(), v1.end(), MyPrint());//5 4 3 2 1
	cout << endl;
	for_each(v2.begin(), v2.end(), MyPrint());//8 7 6 5 4 
	cout << endl;
	v3.resize(max(v1.size(), v2.size()));//若两个容器没有交集，则差集的大小上限等于最大的容器的大小

	vector<int>::iterator it = set_difference(v1.begin(), v1.end(), v2.begin(), v2.end(), v3.begin(), greater<int>());
	for_each(v3.begin(), it, MyPrint());//3 2 1 
	cout << endl;

	it = set_difference(v2.begin(), v2.end(), v1.begin(), v1.end(), v3.begin(), greater<int>());
	for_each(v3.begin(), it, MyPrint());//8 7 6
	cout << endl;

	system("pause");
	return 0;
}
```

> 先求两个容器的**并集**，函数形参列表中的**前者**减去**并集**可得**差集的结果**
