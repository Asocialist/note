# C++学习笔记

## 学习C++目的

- 提升编程能力，研究生项目需要
- 掌握oop编程思想
- 了解如何构筑web server 和游戏编程基础
  
### *指针*

- 指针的定义 储存值的地址
- 如何使用 先&取得地址再*表示值

```C++
int* p1, p2; //建立的是指向int的指针
  
int* address = &p1;// init pointer

```

- 内存分配  
通过new来分配内存  
`int* pn = new int`

- namespace

```C++
namespace 自定义命名空间名 {
    // 定义变量
    int myVar = 42;

    // 定义函数
    void myFunction() {
        // ...
    }

    // 定义类
    class MyClass {
    public:
        void sayHello();
    };
}
```

### *模板类*

- vector

  - 动态数组类似string在运行阶段设置vector对象长度，再末尾和中间可以加上新数据。自动管理内存
  - `#include<vector>`
  - `std::vector<typename> vt(n_elem);` 储存n_elem个类型为typename的元素
- array

  - 固定长度数组使用栈静态分布
  - `#include<array>`
  - `std::array<typename,n_elem> arr;`
  - 与vector不同arr不能为变量
- 使用成员函数`arr.at{}`越界错误

### *对象*

- 对象就是一个可以设置行为和状态的东西对象是根据类来创建的
- 声明类的对象与声明类的基本变量一样`Box box1;`
- 访问数据成员使用`.`来访问`Box1.leight = 2.0;`

### *方法*

- 方法代表行为一个类可以有多个方法。在方法中写入逻辑、操作数据以及执行所有的动作。

### *类*

- 类的定义
  
    类（class）用户定义的数据类型 描述类能表示的信息与能够进行的操作 类对于对象来说是变量 类定义数据格式和用法
    封装了数据和函数的组合

- 如何使用类  

```C++
class classname 
{
    //访问修饰词 pricate/public/protected
    Access specifiers: 
        Data memebers/variables; //变量
        Member functions(){

        }//方法 函数
};//一个类的结束

//设置成员函数/方法
```

```C++
class Box{
    public:
        double  length;
        double  breadth;
        double  height;
};

//设置方法
double Box::get(void){
    return length*breadth*height;
}
```

- 定义一个数据蓝图 类的对象包括什么 可以在这个对象上可以进行的操作

#### 类访问修饰符

- 数据封装是面向对象编程的一个重要特点，它防止函数直接访问类类型的内部成员。
- 类成员的访问限制是通过在类主体内部对各个区域标记 public、private、protected 来指定的。
- 关键字 public、private、protected 称为访问修饰符。
- 一个类可以有多个 public、protected 或 private 标记区域。
- 每个标记区域在下一个标记区域开始之前或者在遇到类主体结束右括号之前都是有效的。成员和类的默认访问修饰符是 private。

- *public*: 程序中类的外部可以访问 可以不使用任何成员函数来设置和获取公有变量的值

- *private*: 私有成员变量或函数在类的外部是不可访问的，甚至是不可查看的。只有类和友元函数可以访问私有成员。
- 一般会在私有区域定义数据，在公有区域定义相关的函数，以便在类的外部也可以调用这些函数

- *protected*: protected（受保护）成员变量或函数与私有成员十分相似，但有一点不同，protected（受保护）成员在派生类（即子类）中是可访问的。

#### 类成员函数

- 将定义和原型写在类定义内部的函数，与其余变量一致。类成员函数是类的一个成员可以操作类的任意对象，访问对象中的所有成员。
  
 ```C++
class Box{
    public:
        double  length;
        double  breadth;
        double  height;
    double getVolume(void){
        return length*breadth*height;
    }
};

//类外部使用域区分符::来定义
double BOX::getVolume(void)
{
   return length*breadth*height;
}
```

- 注意在使用::之前要使用类名在调用成员函数在对象上使用调用`.`
- `::` 可以不跟类名，表示全局数据或全局函数（即非成员函数）。

##### 内联函数inline

- C++ 内联函数是通常与类一起使用。如果一个函数是内联的，那么在编译时，编译器会把该函数的代码副本放置在每个调用该函数的地方。

- 对内联函数进行任何修改，都需要重新编译函数的所有客户端，因为编译器需要重新更换一次所有的代码，否则将会继续使用旧的函数。

- 如果想把一个函数定义为内联函数，则需要在函数名前面放置关键字 inline，在调用函数之前需要对函数进行定义。如果已定义的函数多于一行，编译器会忽略 inline 限定符。

- 在类定义中的定义的函数都是内联函数，即使没有使用 inline 说明符。

- 引入内联函数的目的是为了解决程序中函数调用的效率问题。个空间代价换时间的i节省。所以内联函数一般都是1-5行的小函数。在使用内联函数时要留神：
    1. 在内联函数内不允许使用循环语句和开关语句；
    2. 内联函数的定义必须出现在内联函数第一次调用之前；
    3. 类结构中所在的类说明内部定义的函数是内联函数。

- 使用时注意滥用内联将导致程序变慢. 内联可能使目标代码量或增或减, 这取决于内联函数的大小. 内联非常短小的存取函数通常会减少代码大小, 但内联一个相当大的函数将戏剧性的增加代码大小. 现代处理器由于更好的利用了指令缓存, 小巧的代码往往执行更快。结论: 一个较为合理的经验准则是, 不要内联超过 10 行的函数. 谨慎对待析构函数, 析构函数往往比其表面看起来要更长, 因为有隐含的成员和基类析构函数被调用!

##### 类构造函数和析构函数

- 构造函数是类的一种特殊的成员函数，它会在每次创建类的新对象时执行。
- 构造函数的名称与类的名称是完全相同的，并且不会返回任何类型，也不会返回 void。构造函数可用于为某些成员变量设置初始值。

```C++
#include <iostream>
 
using namespace std;
 
class Line
{
   public:
      void setLength( double len );
      double getLength( void );
      Line();  // 这是构造函数
 
   private:
      double length;
};
 
// 成员函数定义，包括构造函数
Line::Line(void)
{
    cout << "Object is being created" << endl;
}
 
void Line::setLength( double len )
{
    length = len;
}
 
double Line::getLength( void )
{
    return length;
}
// 程序的主函数
int main( )
{
   Line line;
 
   // 设置长度
   line.setLength(6.0); 
   cout << "Length of line : " << line.getLength() <<endl;
 
   return 0;
}
```

- 析构函数是类的一种特殊的成员函数，它会在每次删除所创建的对象时执行。
- 析构函数的名称与类的名称是完全相同的，只是在前面加了个波浪号（~）作为前缀，它不会返回任何值，也不能带有任何参数。

```C++
#include <iostream>
 
using namespace std;
 
class Line
{
   public:
      void setLength( double len );
      double getLength( void );
      Line();   // 这是构造函数声明
      ~Line();  // 这是析构函数声明
 
   private:
      double length;
};
 
// 成员函数定义，包括构造函数
Line::Line(void)
{
    cout << "Object is being created" << endl;
}
Line::~Line(void)
{
    cout << "Object is being deleted" << endl;
}
 
void Line::setLength( double len )
{
    length = len;
}
 
double Line::getLength( void )
{
    return length;
}
// 程序的主函数
int main( )
{
   Line line;
 
   // 设置长度
   line.setLength(6.0); 
   cout << "Length of line : " << line.getLength() <<endl;
 
   return 0;
}
```

### *继承*

- 继承允许我们依据另一个类来定义一个类，这使得创建和维护一个应用程序变得更容易。
- 当创建一个类时，您不需要重新编写新的数据成员和成员函数，只需指定新建的类继承了一个已有的类的成员即可。这个已有的类称为基类，新建的类称为派生类。
- 一个类可以派生自多个类，这意味着，它可以从多个基类继承数据和函数。定义一个派生类，我们使用一个类派生列表来指定基类。类派生列表以一个或多个基类命名
- `class derived-class: access-specifier base-class`访问修饰符 access-specifier 是 public、protected 或 private 其中的一个，base-class 是之前定义过的某个类的名称。如果未使用访问修饰符 access-specifier，则默认为 private。
- 如果继承时不显示声明是 private，protected，public 继承，则默认是 private 继承，在 struct 中默认 public 继承 外部类无法访问protected成员

 1. public 继承：基类 public 成员，protected 成员，private 成员的访问属性在派生类中分别变成：public, protected, private
 2. protected 继承：基类 public 成员，protected 成员，private 成员的访问属性在派生类中分别变成：protected, protected, private
 3. private 继承：基类 public 成员，protected 成员，private 成员的访问属性在派生类中分别变成：private, private, private

- 无论哪种继承方式，下面两点都没有改变：

  1. private 成员只能被本类成员（类内）和友元访问，不能被派生类访问；
  2. protected 成员可以被派生类访问。

#### *多继承*

- 多继承即一个子类可以有多个父类，它继承了多个父类的特性。

```C++
class <派生类名>:<继承方式1><基类名1>,<继承方式2><基类名2>,…
{
<派生类类体>
};
```

- 访问修饰符继承方式是 public、protected 或 private 其中的一个，用来修饰每个基类，各个基类之间用逗号分隔
