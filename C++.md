# C++

[你遇到过哪些高质量的 C++ 面试？ - 知乎 (zhihu.com)](https://www.zhihu.com/question/60911582/answer/1783988850)

[Linux C/C++方向大公司面试必备攻略 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/134724369)

### 虚函数

面向对象语言的三大特点：封装、继承、多态。

C++的多态的实现方式是虚函数。

封装的定义：封装就是将抽象对象的属性和行为特征结合，形成类。尽可能地隐藏对象的内部实现细节，控制用户对类的修改和访问的程度以及权限。

继承的定义：继承就是新类从已有类那里得到已有的特性。在一组相同或类似的对象中，抽取出共性的特征（**属性**）和行为（**方法**），实现代码的复用。

多态的定义：多态是指，一个接口，多种实现。多态有两种方式，一种是静态多态，一种是动态多态。

因为C++中的对象的函数都存放在代码区(code area)，而变量都存放在堆区(heap area)， 如果一个没有带虚函数的类对象，例如

```cpp
class Base{
    void funcA();
    void funcB();
    int var;
}
```

那么在代码区中存放funcA和funcB，然后在堆中存放var，然后就没了。

<img src="https://pic4.zhimg.com/v2-928a8502d32a42a7d25ed804381b133f_r.jpg" alt="preview" style="zoom:33%;" />

**其中成员函数放在代码区，为该类的所有对象公有，即不管新建多少个该类的对象，所对应的都是同一个函数存储区的函数。而成员变量则为各个对象所私有，即每新建一个对象都会新建一块内存区用来存储var值。在调用成员函数时，程序会根据类的类型，找到对应代码区所对应的函数并进行调用。**所以归根到底调用的是基类对象的函数。

但是如果是虚函数的话，例如：

```cpp
class BaseVirtual{
    void funcA();
    virtual void funcB();
    int var;
}
```

这时如果sizeof一个类BaseVirtual的对象，会发现比类Base的对象大4个字节。多出来的这4个字节就是实现虚函数的关键----虚函数表指针vptr。这个指针指向一张名为“虚函数表”（vtbl）的表，而表中的数据则为函数指针，存储了虚函数fun_b()具体实现所对应的位置。

<img src="https://pic1.zhimg.com/v2-7ec4f04db976b943c9d2bd9414d893e4_r.jpg" alt="preview" style="zoom: 33%;" />

注意，普通函数、虚函数、虚函数表都是同一个类的所有对象公有的，只有成员变量和虚函数表指针是每个对象私有的，sizeof的值也只包括vptr和var所占内存的大小（也是个常出现的问题），并且vptr通常会在对象内存的最起始位置。因此，好比想要查看BaseVirtual的虚函数指针地址的话，只需要将这个类型强制转换为int，也就是只保留了前面四个字节，那么就是vptr的地址。

[C++面试必备之虚函数 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/28530472)

**在有继承关系的父子类中，构建和析构一个子类对象时，父子构造函数和析构函数的执行顺序分别是怎样的？**

父构造函数–>子构造函数–>子析构函数–>父析构函数



### 纯虚函数

纯虚函数是在基类中声明的虚函数，它在基类中没有任何定义，但要求任何派生类都要定义自己的实现方法。

纯虚函数的实现是在虚函数的后面加上"=0"。`virtual function()=0`

```cpp
//抽象类
class Person{
    public:
        //纯虚函数
        virtual void GetName()=0;
};
class Student:public Person{
    public:
        Student(){
        };
        void GetName(){
            cout<<"StudentName:xiaosi"<<endl;
        };
};
int main(){
    Student student;
}
```

在很多情况下，基类本身生成对象是不合情理的。例如，动物作为一个基类可以派生出老虎、孔雀等子类，但动物本身生成对象明显不合常理。为了解决上述问题，将函数定义为纯虚函数，则编译器要求在派生类中必须予以重写以实现多态性。同时含有纯虚拟函数的类称为抽象类，它不能生成对象。这样就很好地解决了上述两个问题。

将函数定义为纯虚函数表示，这个函数为派生类提供了可以重写接口，但是在这个类中绝不会调用这个函数。

事实上，当一个类中有虚函数的时候，这个类不能构建对象实例，只能创建一个派生类的实例，必须要在继承的派生类中重新声明函数。

纯虚函数的意义，让所有的类对象（主要是派生类对象）都可以执行纯虚函数的动作，但类无法为纯虚函数提供一个合理的缺省实现。所以类纯虚函数的声明就是在告诉子类的设计者，“你必须提供一个纯虚函数的实现，但我不知道你会怎样实现它”。



### 抽象类

含有纯虚函数的类被称为抽象类。抽象类只能作为派生类的基类，不能定义对象，但可以定义指针。在派生类实现该纯虚函数后，定义抽象类对象的指针，并指向或引用子类对象。

抽象类的主要作用是将有关的操作作为结果接口组织在一个继承层次结构中，由它来为派生类提供一个公共的根，派生类将具体实现在其基类中作为接口的操作。所以派生类实际上刻画了一组子类的操作接口的通用语义，这些语义也传给子类，子类可以具体实现这些语义，也可以再将这些语义传给自己的子类。

抽象类只能作为基类来使用，其纯虚函数的实现由派生类给出。**如果派生类中没有重新定义纯虚函数，而只是继承基类的纯虚函数，则这个派生类仍然还是一个抽象类。**

**如果派生类中给出了基类纯虚函数的实现，则该派生类就不再是抽象类了，它是一个可以建立对象的具体的类。抽象类是不能定义对象的。**

```cpp
#include <iostream>
using namespace std;

class Person
{
public:
    Person(){};
    virtual void sex() = 0;
};

class Woman : public Person
{
public:
    Woman(string sex_name){
        sex_attr = sex_name;
    };
    void sex()
    {
        cout << "This is a " << sex_attr << "." << endl;
    }

private:
    string sex_attr;
};

int main()
{
    Woman w = Woman("woman"); // 声明一个Woman类
    w.sex(); // This is a woman.
    Person *p; // 抽象类不能实例化但是可以定义指针
    Woman f = Woman("female"); // 再实例化一个Woman类
    Woman *ww = &f; // 声明一个指针指向这个Woman实例
    ww->sex(); // This is a female
}
```

[[面试题\]虚函数和纯虚函数_Ying-CSDN博客](https://blog.csdn.net/SunnyYoona/article/details/39137385)



### 构造函数和析构函数可以是虚函数吗？

构造函数不可以是虚函数，但是析构函数可以是虚函数，并且析构函数最好是虚函数，这样子可以调用派生类自己的析构函数。

首先，我们已经知道虚函数的实现则是通过对象内存中的vptr来实现的。而构造函数是用来实例化一个对象的，通俗来讲就是为对象内存中的值做初始化操作。那么在构造函数完成之前，vptr是没有值的，也就无法通过vptr找到作为虚函数的构造函数所在的代码区，所以构造函数只能作为普通函数存放在类所指定的代码区中。那么为什么析构函数推荐最好设置为虚函数呢？如文章开头的例子中，当我们delete(a)的时候，如果析构函数不是虚函数，那么调用的将会是基类base的析构函数。而当继承的时候，通常派生类会在基类的基础上定义自己的成员，此时我们当时是希望可以调用派生类的析构函数对新定义的成员也进行析构啦。

除此之外，内联函数、静态函数都不能是虚函数。内联函数在编译阶段就已经确定，而虚函数会一直到运行阶段才能确定。静态函数是以类为单位的函数，与具体对象无关，虚函数和对象动态绑定，因此也矛盾。





### C++对象在内存中的布局

在C++中有**两种类的成员变量：static和非static**，**三种成员函数：static、非static和虚函数**。

C++的内存布局分为四个部分：全局数据区(data area)、代码区(code area)、栈区(stack area)和堆区(heap area)。

全局**数据区存放全局变量，静态数据和常量**。

**函数都在代码区。**

为了运行函数而**分配的变量都在栈区**。

剩下的空间都是堆区，可以由程序员自由分配。

> OS中的内存布局分配，**栈也主要是OS自动分配的**，拥有较高的权限，通常用来存储局部变量和函数参数，函数调用之后返回的地址。**堆用来存储用户空间的内存**，使用new分配，使用delete释放。自由存储区使用malloc分配，free释放。



### new/delete和malloc/free

- new/delete是关键字，效率高于malloc和free
- new和delete是C++关键字，**需要编译器支持**；malloc和free是库函数，**需要头文件支持**。
- new/delete 主要是用在类对象的申请和释放。申请的时候会调用构造器完成初始化，释放的时候，会调用析构器完成内存清理。
- new做两件事：分配内存和调用类的构造函数，delete是：调用类的析构函数和释放内存。而malloc和free只是分配和释放内存。
- 使用new操作符申请内存分配时无须指定内存块的大小，编译器会根据类型信息自行计算，而malloc则需要显式地指出所需内存的尺寸。

[c++ new 与malloc有什么区别 - ywliao - 博客园 (cnblogs.com)](https://www.cnblogs.com/ywliao/articles/8116622.html)



### 静态多态和动态多态

[C++---静态多态与动态多态_江城的博客-CSDN博客_静态多态和动态多态](https://blog.csdn.net/qq_37934101/article/details/81365449)

静态多态：也称为编译期间的多态，编译器在编译期间完成的，编译器根据函数实参的类型(可能会进行隐式类型转换)，可推断出要调用那个函数，如果有对应的函数就调用该函数，否则出现编译错误。
静态多态有两种实现方式：

- 函数重载：包括普通函数的重载和成员函数的重载
- 函数模板的使用

动态多态：即运行时的多态，在程序执行期间(非编译期)判断所引用对象的实际类型，根据其实际类型调用相应的方法。



### 几个特殊继承：

**菱形继承**：多个类继承了同一个公共基类，而这些派生类又继承被同一个类继承。

```cpp
A:public base
B:public base
D:public A,public B
D d;//声明一个D对象
d.fun();//fun是base的一个函数，此时编译器报错：调用不明确
```

D的数据模型中保存了两份base，当我们需要调用的时候，就会出现调用不明确，造成数据冗余。

一种方法是直接写明，d::A.fun();或者d::B.fun();

另一种方法是**虚继承**。

A和B在继承的时候加上virtual关键词。

A和B不再保存base内容，而是保存了一份偏地址（偏移量），将base保存在一个公共位置，保证了数据冗余性降低的同时，也能直接使用d.fun()调用base里的函数。

```cpp
A:virtual public base
B:virtual public base
D:public A,public B
D d;//声明一个D对象
d.fun();//调用base的fun函数成功
```



### const/static/extern/volatile

[C语言const、static、extern、volatile关键字总结_心中的日月的博客-CSDN博客](https://blog.csdn.net/rentan0930/article/details/92764913)

- const

  const要求修饰的对象类型为常量，不可以对其修改或者赋值，不能作为左值出现。

  修饰常量时，`const int a=10;`的声明方式和`int const a=10;`的作用是一样的。

  修饰指针时，`const int *a;`用来修饰指针指向的变量是常量变量。`int *const a;` 指针本身a是一个常量。

  - const离谁近，谁就不能被修改；
  - 因为常量在定义以后就不能被修改，所以使用const定义变量时必须初始化。

  修饰函数的参数，传递的参数值不能发生改变。如果`function(const &n)`那么在函数体中对于n赋值的语句是会报错的。

- static

  不使用static关键字时，该变量是自动变量，每次都分配存在堆栈中，函数或者函数代码块执行结束之后就自动销毁了，每次执行都会重新分配内存，每次执行结束之后都会销毁内存。

  使用static关键字时，该变量是静态变量，变量分配在静态内存中，是一个全局变量，数值改动之后不会恢复，一直存在直到程序结束。

- extern

  extern关键字用来修饰变量，表示该变量在别的文件中已有声明，**若一个变量需要在同一个工程中不同文件里直接使用或修改，则需要将变量做extern声明。只需将该变量在其中一个文件中定义，然后在另外一个文件中使用extern声明便可使用，但两个变量类型需一致。**

  ```cpp
  // 第一种形式
  // file1.c中定义一个变量
  int i=1;
  // file2.c和file3.c中引用这个变量
  extern int i; // 声明一下就可以直接使用
  
  // 第二种形式
  // file1.h头文件中声明
  extern int i;
  // file1.c和file2.c中引用这个变量
  int i = 0;
  // 其他文件中引用这个变量只需要include头文件
  ```

- volatile

  volatile的作用是告知编译器，它修饰的变量随时都可能被改变，因此，编译后的程序每次在使用该变量的值时，都会从变量的地址中读取数据，而不是从寄存器中获取。



### C++的四种强制转换

[C++强制类型转换_q610098308的专栏-CSDN博客_强制类型转换](https://blog.csdn.net/q610098308/article/details/115915802)



### Struct & Union

struct和union都是由多个不同的数据类型成员组成，但在任何同一时刻，union中只存放了一个被选中的成员（所有成员共用一块地址空间），而struct的所有成员都存在（不同成员的存放地址不同）。 

对于union的不同成员赋值，将会对其它成员重写，原来成员的值就不存在了，而对于struct的不同成员赋值是互不影响的。

union和struct的内存占用都需要考虑到字节对齐的问题，但是，字节对齐考虑结束之后，union占用的空间由最大的那个变量决定，而struct需要将所有的占用加起来，这也体现出了这两种的成员模式不同。 



### 左值和右值

C++中的赋值操作符为=，其意义是将赋值号右边的值传送给左边变量所标识的内存单元中。

左值：可以出现在赋值语句的左边和右边，不光有数值，还有一个存储地址，能用&取得地址值，具有持久性。

右值：可以是常量、变量或者表达式，但一定能取得确定的值，无法用&取得地址值，只是字面数值。

左值引用：传统的左值引用

```cpp
#include <iostream>
using namespace std;
int main(){
    int a = 10;
    int &b = a; // 直接引用a的地址
    b = 20;
    cout << b << endl; // 20
    cout << a << endl; // 20
}
```

如果我们需要对一个立即数取地址，是不能直接引用的，因为立即数存储在寄存器中，而不是内存中，是没有地址的。如果使用这样的代码`int &var = 10;`编译器是会报错的。

但是我们可以使用一个常引用来引用常数10，

```cpp
#include <iostream>
using namespace std;
int main(){
    const int &var = 10;
    // 上面的常引用等价于
    const int temp = 10;
    const int &var_temp = temp;
    cout << var << endl; // 10
    cout << temp << endl; // 10
    cout << var_temp << endl; // 10
}
```

C++11引入右值引用：从本质上理解，创建和销毁由编译器幕后控制，程序员只能确保在本行代码有效的，就是右值(包括立即数)；而用户创建的，**通过作用域规则可知其生存期的，就是左值**(包括函数返回的局部变量的引用以及const对象)。

```cpp
#include <iostream>
using namespace std;
int main(){
    int &&var =10;
    cout << var <<endl; // 10
    var = 12;
    cout << var << endl; // 12
}
```

右值引用的存在并不是为了取代左值引用，而是**充分利用右值(特别是临时对象)的构造来减少对象构造和析构操作以达到提高效率的目的。**

[C++11 ---右值引用 ，转移语义，完美转发_sores的博客-CSDN博客](https://blog.csdn.net/sores/article/details/88939334?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_title-0&spm=1001.2101.3001.4242)



### 泛型编程

C++是一门强类型语言，所以无法像动态语言(JS Python)一样，编写一段通用的逻辑，可以把任意的变量传入进去处理，泛型编程弥补了这个问题，通过将通用逻辑设计为模板，摆脱了类型的限制。提供了继承机制之外的另一种抽象机制，提高了代码的可用性。

> 注意：模板定义本身不参与编译，而是编译器根据模板的用户使用模板时提供的类型参数生成代码，再进行编译，这一过程被称为模板实例化。用户提供不同的类型参数，就会实例化出不同的代码。

```cpp
int compare(const int &a, const int &b) {
	if(a<b) return -1;
    if(b<a) return 1;
    return 0;
}
int compare(const double &a, const double &b) {
    if(a<b) return -1;
    if(b<a) return 1;
    return 0;
}
```

这两个函数除了类型不一样，实现的逻辑是一样的，因此我们可以定义一个通用的函数模板，而不是为每一个类型都定义一个新的函数。

```cpp
#include<iostream>
using namespace std;
template <typename T>
int compare(const T &a, const T &b)
{
    return a < b ? -1 : 1;
    return 0;
}
int main(){
    cout << compare(1,2) << endl; // -1
    cout << compare(1.0000000, 2.0000000) << endl; //-1
}
```



### 构造函数和析构函数

构造函数是一个特殊的成员函数，在创建类的时候被系统自动调用，用于构造类对象。如果程序员没有编写构造函数，那么C++运行的时候会自动提供一个，自动提供的构造函数是看不到的，是在后台静默运行的。构造函数通常被用来初始化对象的成员变量，但实际上，构造函数和普通的成员函数没有什么区别，可以做成员函数做的任何事情。构造函数看起来像一个常规函数，除了**它的名称必须与它所属类的名称相同**，这就是为什么编译器知道这个特定的成员函数是一个构造函数。**此外，构造函数不允许有返回类型**。

析构函数是具有与类相同名称的公共成员函数，前面带有"~"。当对象被销毁时，会自动调用析构函数。在创建对象时，构造函数使用某种方式来进行设置，**那么当对象停止存在时，析构函数也会使用同样的方式来执行关闭过程。**例如，当**具有对象的程序停止执行或从创建对象的函数返回**时，就会发生这种情况。



### i++和++i

```cpp
i++:
const int  	int::operator(int){
	int oldValue = *this;
	++(*this);
	return oldValue;
}
```

```cpp
++i:
int& int::operator++(){
	*this+=1;
	return *this;
}
```



### 内存溢出

指程序在申请内存时，没有足够的内存空间供其使用，出现out of memory；比如申请了一个integer,但给它存了long才能存下的数，那就是内存溢出。

内存溢出就是你要求分配的内存超出了系统能给你的，系统不能满足需求，于是产生溢出。



### 内存泄漏

内存泄漏是指你向系统申请分配内存进行使用(new)，可是使用完了以后却不归还(delete)，结果你申请到的那块内存你自己也不能再访问（也许你把它的地址给弄丢了），而系统也不能再次将它分配给需要的程序。

1. 常发性内存泄漏。造成内存泄露的代码被多次执行，每次执行都会造成内存泄漏。
2. 偶发性内存泄漏。发生内存泄漏的代码只有在某些特定的环境下才会发生，但是对于特定的环境，偶发性的就会变成常发性的。
3. 一次性内存泄漏。发生内存泄漏的代码只会执行一次。比如，在类的构造函数中分配内存，在析构函数中却没有释放该内存，所以内存泄漏只会发生一次。
4. 隐式内存泄漏。程序在运行过程中不停的分配内存，但是直到结束的时候才释放内存。严格的说这里并没有发生内存泄漏，因为最终程序释放了所有申请的内存。但是对于一个服务器程序，需要运行几天，几周甚至几个月，不及时释放内存也可能导致最终耗尽系统的所有内存。所以，我们称这类内存泄漏为隐式内存泄漏。

从用户使用程序的角度来看，内存泄漏本身不会产生什么危害，作为一般的用户，根本感觉不到内存泄漏的存在。真正有危害的是内存泄漏的堆积，这会最终消耗尽系统所有的内存。从这个角度来说，一次性内存泄漏并没有什么危害，因为它不会堆积，而隐式内存泄漏危害性则非常大，因为较之于常发性和偶发性内存泄漏它更难被检测到。

内存泄漏最终会演变为内存溢出。

**内存泄漏的检测：**

为了判断内存是否泄露，我们一方面可以使用linux环境下的内存泄漏检查工具Valgrind,另一方面我们在写代码时可以添加内存申请和释放的统计功能，统计当前申请和释放的内存是否一致，以此来判断内存是否泄露。



### 智能指针

[C++11中智能指针的原理、使用、实现 - wxquare - 博客园 (cnblogs.com)](https://www.cnblogs.com/wxquare/p/4759020.html)

1. shared_ptr:

   

   ```cpp
   #include <memory>
   #include <iostream>
   
   using namespace std;
   
   int main()
   {
       int a = 10;
       shared_ptr<int> ptrA1 = make_shared<int>(a);
       shared_ptr<int> ptrA2 = ptrA1;
       shared_ptr<int> ptrA3 = ptrA2;
   
       cout << sizeof(ptrA1) << endl;     // 16
       cout << &ptrA1 << endl;            // 0x61fd20
       cout << &ptrA2 << endl;            // 0x61fd10
       cout << &ptrA3 << endl;            // 0x61fd00
       cout << ptrA1.use_count() << endl; // 3
   
       int b = 20;
       int *pB = &a;
       shared_ptr<int> ptrB = make_shared<int>(b);
       ptrA1 = ptrB;
       pB = ptrB.get();
   
       cout << ptrA1.use_count() << endl; // 2
       cout << ptrB.use_count() << endl;  // 2
   }
   ```

2. unique_ptr:
   
   unique_ptr“唯一”拥有其所指对象，同一时刻只能有一个unique_ptr指向给定对象（通过禁止拷贝语义、只有移动语义来实现）。相比与原始指针unique_ptr用于其RAII的特性，使得在出现异常的情况下，动态资源能得到释放。unique_ptr指针本身的生命周期：从unique_ptr指针创建时开始，直到离开作用域。离开作用域时，若其指向对象，则将其所指对象销毁(默认使用delete操作符，用户可指定其他操作)。unique_ptr指针与其所指对象的关系：在智能指针生命周期内，可以改变智能指针所指对象，如创建智能指针时通过构造函数指定、通过reset方法重新指定、通过release方法释放所有权、通过移动语义转移所有权。
   
   ```cpp
   #include <iostream>
   #include <memory>
   
   int main() {
       {
           std::unique_ptr<int> uptr(new int(10));  //绑定动态对象
           //std::unique_ptr<int> uptr2 = uptr;  //不能賦值
           //std::unique_ptr<int> uptr2(uptr);  //不能拷貝
           std::unique_ptr<int> uptr2 = std::move(uptr); //轉換所有權
           uptr2.release(); //释放所有权
       }
       //超過uptr的作用域，內存釋放
   }


3. weak_ptr:

   weak_ptr是为了配合shared_ptr而引入的一种智能指针，因为它不具有普通指针的行为，没有重载operator*和->,它的最大作用在于协助shared_ptr工作，像旁观者那样观测资源的使用情况。weak_ptr可以从一个shared_ptr或者另一个weak_ptr对象构造，获得资源的观测权。但weak_ptr没有共享资源，它的构造不会引起指针引用计数的增加。使用weak_ptr的成员函数use_count()可以观测资源的引用计数，另一个成员函数expired()的功能等价于use_count()==0,但更快，表示被观测的资源(也就是shared_ptr的管理的资源)已经不复存在。weak_ptr可以使用一个非常重要的成员函数lock()从被观测的shared_ptr获得一个可用的shared_ptr对象， 从而操作资源。但当expired()==true的时候，lock()函数将返回一个存储空指针的shared_ptr。

   ```cpp
   #include <iostream>
   #include <memory>
   
   int main() {
       {
           std::shared_ptr<int> sh_ptr = std::make_shared<int>(10);
           std::cout << sh_ptr.use_count() << std::endl;
   
           std::weak_ptr<int> wp(sh_ptr);
           std::cout << wp.use_count() << std::endl;
   
           if(!wp.expired()){
               std::shared_ptr<int> sh_ptr2 = wp.lock(); //get another shared_ptr
               *sh_ptr = 100;
               std::cout << wp.use_count() << std::endl;
           }
       }
       //delete memory
   }
   ```

   




### 深拷贝和浅拷贝

浅拷贝：浅拷贝是**增加了一个指针，指向原来已经存在的内存**。而**深拷贝是增加了一个指针，并新开辟了一块空间，新指针指向新空间**。

```cpp
#include <iostream>
using namespace std;

class Test
{
public:
    int x;
    int y;
    Test(int n);
    void show(); // 用来验证这个Test的内存空间中没有这两个函数的位置
    int showTime(){ // 函数的空间保存在代码区
        return 2021;
    };
};

Test::Test(int n) : x(n) {}

void Test::show()
{
    cout << x << endl;
}

int main(){
    cout << sizeof(Test);
    
    Test *pt1 = new Test(100);
    Test *pt2 = pt1;
    
    pt1->x = 5;

    pt1->show(); // 5
    pt2->show(); // 5

    delete pt1;
    delete pt2; // 因为这个是浅拷贝，所以共享同一个内存，所以当delete pt1执行之后，那一块内存已经被释放，此时再delete pt2的话，程序会崩溃，造成内存泄漏

    return 0;
}
```

深拷贝的例子比比皆是，除了上面的变长数组类，我们在《[C++ throw关键字](http://c.biancheng.net/view/2332.html)》一节中使用的动态数组类也需要深拷贝；此外，标准模板库（[STL](http://c.biancheng.net/stl/)）中的 string、vector、stack、set、map 等也都必须使用深拷贝。

深拷贝的现代写法：因为浅拷贝的话，地址会在原先的变量上面累加。

1. 先创建一个临时变量，此时程序会先去调用构造函数开辟出一段和S1一样大的地址空间出来。

2. 将S1的内容放在tmp所指的空间中。
3. 交换S2和temp。

```cpp
STRING( const STRING& s ):_str(NULL)
    {
        STRING tmp(s._str);// 调用了构造函数，完成了空间的开辟以及值的拷贝
        swap(this->_str, tmp._str); //交换tmp和目标拷贝对象所指向的内容
    }

    STRING& operator=(const STRING& s)
    {
        if ( this != &s )//不让自己给自己赋值
        {
            STRING tmp(s._str);//调用构造函数完成空间的开辟以及赋值工作
            swap(this->_str, tmp._str);//交换tmp和目标拷贝对象所指向的内容
        }
        return *this;
    }
```

可以看到s2的地址值和拷贝构造里的tmp的地址值是一样。



### new和new[]，delete和delete[]

首先，new和delete并不是函数，是C++的保留关键字，通过特定的语法组成表达式。

```cpp
void *operator new(size_t);     //allocate an object
void *operator delete(void *);    //free an object

void *operator new[](size_t);     //allocate an array
void *operator delete[](void *);    //free an array
```

new做了三件事：

1. 调用operator new标准库函数，传入的参数是class的大小。
2. 上面分配的内存是未初始化的，也没有类型化。调用构造函数，对于这块内存进行初始化，例如成员函数赋值。
3. 最后异步返回新分配并且构造好的对象的指针

delete做了两件事：

1. 调用了指针指向的对象的析构函数，对于打开的文件关闭。
2. 调用operator delete标准库函数释放该对象的内存。

**如果我们需要对于一个数组进行动态分配，就需要用到new[]。**

```cpp
string *psa = new string[10];      //array of 10 empty strings
int *pia = new int[10];           //array of 10 uninitialized ints
```

此时的释放内存需要使用

```cpp
delete [] psa;
delete [] pia;
```

new[]的时候会保存一个数组的维度，这样在分配数组空间的时候就多分配了4个字节专门用来保存数组的大小，delete[]的时候就是用这个数字，调用这么多次的析构函数。

```cpp
class A *pAa = new class A[3];
delete pAa;
```

这样只会调用一次析构函数，只能释放掉一个对象，其余的都会变成内存泄漏。

总的来说，记住一点即可：**new/delete、new[]/delete[] 要配套使用。**



### 缺省参数

C++中，定义函数的时候可以让**最右边**的连续若干个参数有缺省值，在调用函数的时候，如果不写相应位置的参数，则调用的参数就为缺省值。在调用时，如果参数b和参数c没有给出，则默认为缺省值。 函数缺省参数的作用在于提高程序的可扩充性 。比如某个以及写好的函数需要添加新的参数，而原先调用函数的的那些语句未必需要新增加的参数，为了避免对原来所有调用该函数的地方进行修改，就可以使用函数缺省参数了。



### std::initializar_list<T>



### C++11的多线程机制

在C++11之前，C++语言层面是不支持多线程的，想利用C++实现并发程序，借助操作系统的API实现跨平台的并发程序存在着诸多不便，当C++11在语言层面支持多线程后，编写跨平台的多线程代码就方便了许多。

[c++11 多线程入门教程（一） - aircraft - 博客园 (cnblogs.com)](https://www.cnblogs.com/DOMLX/p/10945309.html)

```cpp
#include <windows.h>
#include <iostream>
#include <chrono>
#include <thread>
#include <condition_variable>
#include <mutex>
using namespace std;

int number = 1;

int ThreadProc1()
{
    while (number < 100)
    {
        cout << "thread 1 :" << number << endl;
        ++number;
        this_thread::sleep_for(std::chrono::milliseconds(100));
    }
    return 0;
}

int ThreadProc2()
{
    while (number < 100)
    {
        cout << "thread 2 :" << number << endl;
        ++number;
        this_thread::sleep_for(std::chrono::milliseconds(100));
    }
    return 0;
}

void ThreadProc3(int a){
    cout << "the argument is " << a << endl;
}

void ThreadProc4(int a)
{
    cout << "the number and the argument is " << (number += a) << endl;
    while (number < 100)
    {
        number += a;
        cout << number << endl;
        this_thread::sleep_for(std::chrono::milliseconds(100));
    }
}

int main()
{
    thread t1(ThreadProc1);
    thread t2(ThreadProc2);
    int i = 5;
    thread t3(ThreadProc3, i);
    thread t4(ThreadProc4, i);
    t1.join();
    t2.join();
    t3.join();
    t4.join();
    return 0;
}

```

在Linux中的多线程编译会用到posix实现，所以编译的时候要加上-lpthread参数

```shell
gatsby@ubuntu:~/Desktop/cpp$ g++ multi_thread.cpp -o multi_thread -lpthread
gatsby@ubuntu:~/Desktop/cpp$ ./multi_thread
```

在windows平台下输出的线程id是2、3、4、5，

在Linux平台下输出为：

t1 thread id is 140357989508864
t2 thread id is 140357981116160
t3 thread id is 140357972723456
t4 thread id is 140357964330752



### thread::join()和thread::detach()



### C++的类继承中哪些不会被派生类继承

[哪些成员函数不能被继承_zhuzhubility的博客-CSDN博客_哪些函数不能被继承](https://blog.csdn.net/weixin_41469381/article/details/88855479)

[为什么C++赋值运算符重载函数不能被继承？_pbmichael的博客-CSDN博客](https://blog.csdn.net/pbmichael/article/details/1608250?utm_medium=distribute.pc_relevant_t0.none-task-blog-2~default~BlogCommendFromMachineLearnPai2~default-1.control&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2~default~BlogCommendFromMachineLearnPai2~default-1.control)

有三类成员函数不能被继承：构造函数、析构函数、赋值运算符重载函数

1. 构造函数用来构造类的对象，与父类的其他函数不一样，它不能被子类继承（子类可以继承父类的所有的成员变量和成员函数，但是不能继承构造方法）。因此在创建子类的对象的时候，为了初始化从父类继承来的数据成员，系统需要调用其父类的构造方法。如果没有显式的构造函数的话，系统会默认创建一个。**并且，构造函数不能为虚函数。虚函数的作用是允许在派生类中重新定义与基类同名的函数，并且可以通过基类指针或引用来访问基类和派生类中的同名函数**。

   要成为虚函数必须满足两点，一就是**这个函数依赖于对象调用**，因为虚函数就是依赖于对象调用，**因为虚函数是存在于虚函数表中，有一个虚函数指针指向这个虚表**，所以要调用虚函数，必须通过虚函数指针，**而虚函数指针是存在于对象中的**。

   二就是这个函数必须可以取地址，因为我们的虚函数表中存放的是虚函数函数入口地址，如果函数不能寻址，就不能成为虚函数。构造函数内联函数不可作为虚函数。

2. 析构函数也不会被继承。在子类的析构函数中会调用父类的析构函数。

3. **如果派生类中声明的成员与基类的成员同名，那么，基类的成员会被覆盖，哪怕基类的成员与派生类的成员的数据类型和参数个数都完全不同。**



### public private protected

[C++中public、protected、private的区别_SCOTT 技术博客-CSDN博客_private](https://blog.csdn.net/scottly1/article/details/24354489)

第一：private,public,protected的访问范围

- public：可以被该类中的函数、其友元函数访问、子类的函数，也可以由该类的对象访问
- protected：**可以被该类中的函数、友元函数、子类的函数访问**，但不能被该类的对象访问
- private：**只能由该类中的函数、其友元函数访问**，不能被任何其他访问，该类的对象也不能访问

第二：类的继承后方法属性变化

- 使用public，父类中的方法属性不会发生改变
- 使用protected，父类中的protected和public方法在子类中变为protected，private方式不变。
- 使用private，父类中的所有方法在子类中变为private

综上：

1. public继承不改变基类成员的访问权限
2. protected继承将基类中public成员变为子类的protected成员，其它成员的访问权限不变。
3. private继承使得基类所有的权限变成private

此外，在使用private继承时，还存在另外一种机制：准许访问 。



### #include 尖括号和双引号

C++已经有一些编写好的头文件（比如标准函数库等等），它们存放在VC++的include文件夹里。

当我们使用#include <文件名>命令时，编译器就到这个文件夹里去找对应的文件。显然，用这种写法去包含一个我们自己编写的头文件（不在那个include文件夹里）就会出错了。**所以包含C++提供的头文件时，应该使用尖括号。**

**相反地，#include "文件名"命令则是先在当前文件所在的目录搜索是否有符合的文件，如果没有再到include文件夹里去找对应的文件。**因此，无论这个文件是C++提供的还是自己编写的，使用#include "文件名"命令一定是正确的。这也正是书中本节之前的程序一律使用#include "文件名"命令的原因。



### C++的虚函数表是针对类还是针对对象

[C++ 虚函数表是针对类还是针对对象的？虚表存在哪里？_Magnum的博客-CSDN博客_虚函数表属于类还是对象](https://blog.csdn.net/qq_28584889/article/details/88756022#:~:text=总结：虚函数表是针对类的，一个类的所有对象的虚函数表都一样。. 虚指针 (vptr)：每个含有虚方法（虚函数）对象里有虚表指针，指向虚表。. 虚函数表：虚函数表是顺序存放虚函数地址的，虚表是顺序表，表里存放了虚函数的地址。.,C%2B%2B的编译器应该是保证虚函数表的指针存在于对象实例中最前面的位置（这是为了保证取到虚函数表的有最高的性能——如果有多层继承或是多重继承的情况下）。. 这意味着我们通过对象实例的地址得到这张虚函数表，然后就可以遍历其中函数指针，并调用相应的函数。. 目前gcc 和微软的编译器都是将vptr放在对象内存布局的最前面。.)

虚指针(vtpr)：每个含有虚函数的对象里有虚表指针，指向虚表。

虚函数表(vtable)：虚函数表是顺序存放虚函数地址的，虚表是顺序表，表中存放的是虚函数的地址。

C++的编译器会把虚函数表的指针存放在对象实例的最前面的位置----为了保证取到虚函数表有更好的性能，如果有多层继承或是多重继承的情况下。这意味着我们通过对象实例的地址得到这张虚函数表，然后就可以快速遍历其中函数指针，并调用响应的函数。目前gcc和微软的编译器都是将虚函数放在对象内存布局的最前面。

- **虚函数表属于类，类的所有对象共享这个类的虚函数表**
- **不同对象虚函数表是一样的（虚函数表的第一个函数地址相同）**
- 每个对象内部都**保存一个指向该类虚函数表的指针vptr**，每个对象的vptr的存放地址都不一样，但是**都指向同一虚函数表**

```cpp

#include <iostream>
using namespace std;
 
class Base {
public:
	virtual void f() { cout << "Base::f" << endl; }
	virtual void g() { cout << "Base::g" << endl; }
	virtual void h() { cout << "Base::h" << endl; }
 
};
typedef void(*Fun)(void);
 
int main()
{
	Fun pFun = NULL;
	Base obj_1,obj_2;
	// obj_1 虚函数表 — 第一个函数
	pFun = (Fun)*((int*)*(int*)(&obj_1));
	pFun();
	pFun = (Fun)*(((int*)*(int*)(&obj_1))+1);
	pFun();
	// 输出 虚函数表地址 与 虚函数表元素 的值
	cout << "obj_1 虚函数表地址：" << (int*)(&obj_1) << endl;
	cout << "obj_2 虚函数表地址：" << (int*)(&obj_2) << endl;
	cout << "obj_1 虚函数表 — 第一个函数地址：" << (int*)*(int*)(&obj_1) << endl;
	cout << "obj_2 虚函数表 — 第一个函数地址：" << (int*)*(int*)(&obj_2) << endl;
	return 0;
}
/*
输出 ：
Base::f      
Base::g 
obj_1 虚函数表地址：0018FF40                         
obj_2 虚函数表地址：0018FF3C                         
obj_1 虚函数表 — 第一个函数地址：0046F0AC           
obj_2 虚函数表 — 第一个函数地址：0046F0AC           
Press any key to continue                                                                                 
*/
————————————————
版权声明：本文为CSDN博主「MagnumLu」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/qq_28584889/article/details/88756022
```



### 虚函数表和虚函数指针的存放位置

虚函数表的地址，由上面一张已经知道，虚函数表在一个类的最前面，32位编译器的前四个字节就是虚函数表的地址，将它转换为int类型就可以直接得到。所以第一步是获取虚函数表指针，来获取虚函数表的地址。

```cpp
class A
{
public:
	A(){};
	~A(){};
	virtual void vfun(){cout<<"vfun called!"<<endl;}
};
```

class A

```cpp
A *a = new A();
long vbaddr=*(int *)a;   //虚函数表地址
```

由于只有一个虚函数，所以虚函数表的前4个字节肯定就是vfun的函数地址，因此根据虚函数表的地址还可以得到虚函数vfun的地址：

```cpp
long vfaddr= *(int *)vbaddr; //虚函数A::vfun的地址
```

然后还可以根据vfun的地址来调用这个函数：

```cpp
((void(*)(void))vfaddr)();  //根据得到的地址来调用虚函数 
```

C++中**虚函数表位于只读数据段（.rodata），也就是C++内存模型中的常量区/全局数据区；而虚函数则位于代码段（.text），也就是C++内存模型中的代码区。**



### 指针和引用

[C++中指针与引用的区别 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/140966943)

1. 指针是一个变量，存储的是一个地址，指向内存的一个存储单元。引用是原变量的一个别名，和原来的变量实际是同一个东西。

2. 指针可以在定义的时候不初始化，但是引用在定义的时候必须初始化。

3. 指针可以指向null，引用不能引用null

4. 指针在初始化之后可以改变指向，但是引用一旦初始化之后就不能再改变

   ```cpp
   int a = 996;
   int *p = &a; // 初始化, p 是 a 的地址
   int &r = a; // 初始化, r 是 a 的引用
   
   int b = 885;
   p = &b;	// 合法, p 更改为 b 的地址
   r = b; 	// 不合法, r 不可以再变
   ```

5. sizeof的结果不一样，在64位机器上，int* 类型的大小为8个字节，int类型的大小为4个字节。

6. 自增运算意义不同，p++之后指向指针后面的内存，r++相当于a++。

```cpp
#include <iostream>

using namespace std;

int main()
{
    int &&a = 10; // 右值引用，直接引用一个常量
    int ten = 10;
    int &b = ten; // 这两句左值引用的作用和右值引用是一样的
    cout << a << endl; // 10
    int *p = &a; // 指针p指向a的地址
    cout << sizeof(p) << endl; // 在64位的机器上int*的指针占用八个字节
    cout << p << endl; // 0x61fdf4
    cout << sizeof(a) << endl; // a是一个int类型，sizeof为4
    int &r = a; // r是a的别名
    cout << r << endl; // 10
    int *pp = &r; // 此处的&是求地址运算
    cout << pp << endl;
    int *ppp = &r; 
    cout << ppp << endl; // 10
}
```



### C++的动态绑定

[C++动态绑定原理 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/216258189)

继承的作用在于我们可以更加容易地定义和其他类很类似，但是又不同的新类

多态的作用在于我们可以通过动态绑定，在使用这些类进行编写的时候，一定程度上忽略这些类的区别

所谓动态绑定，其实就是我们在调用虚函数的时候，虚函数会根据绑定的对象的实际类型，选择调用函数的版本。

```cpp
class Quote{
    public:
        string isbn() const;
        virtual double net_price(size_t n) const;//定义虚函数，派生类各自去定义net_price
};

class Bulk_quote:public Quote{
    public:
        double net_price(size_t n) const override;
};
```

**动态绑定虚函数工作原理：** 前面提到，每一个类会为所有虚函数数维护一个虚函数表，并且有一个指针指向这个表的首地址，这个虚函数表记录着所有的虚函数的入口地址。那么在动态绑定时，编译器是如何给基类指针返回一个正确的函数入口地址，这里给出一个例子：

```cpp
class A{
        protected:
        int a1;
        int a2;
        public:
        virtual void display(){ cout<<"A::display()"<<endl;}
        virtual void clone(){ cout<<"A::clone()"<<endl;}
};
class B: public A{
        protected:
        int b;
        public:
        virtual void display(){ cout<<"B::display()"<<endl;} override
        virtual void init(){ cout<<"B::init()"<<endl;}
};
class C: public B{
        protected:
        int c;
        public:
        virtual void display(){ cout<<"C::display()"<<endl;} override
        virtual void execute(){ cout<<"C::execute()"<<endl;} 
        virtual void init(){cout<<"C::init()"<<endl;} override
};
```

这里给出A,B,C三个类的内存情况，如下图。可以得出这么几个结论：

1. **类的内存占用由成员变量和指向虚函数表的指针组成**，同时派生类的成员变量是会把基类的成员变量都继承的
2. 同名虚函数在基类和派生类中的虚函数表中，索引是一致的，如下图，A,B,C的display的索引都是0
3. **派生类中，一旦对基类中的虚函数进行了覆盖，那么派生类的虚函数表中响应函数的入口地址会被替换成覆盖后的函数的地址。**
4. 一旦有新的虚函数定义，会加入到当前虚函数表的末端。

![preview](https://pic4.zhimg.com/v2-f9a4b5c7d5bfa1232001cb89c4b9b0bf_r.jpg)

上面是虚函数表在继承关系中的更新过程，在实际使用过程中，假定指针p调用虚函数display(), 发生调用时：

1. 首先会找到函数的索引，这里display索引是0
2. 然后编译器会做一个替换，（*(p->vptr)[0]），找到p指针的函数入口地址
3. 程序运行后会执行这条语句，完成函数的调用，这就是动态绑定。

对于不同的虚函数，仅仅就是索引的不同。



### C++多线程的传参问题

[C++11多线程thread参数传递问题_A_Bo的博客-CSDN博客_c++ thread 参数](https://blog.csdn.net/li1615882553/article/details/85342101)



### #pragma 

pragma的作用是设定编译器的状态

[#pragma的用法 - Boblim - 博客园 (cnblogs.com)](https://www.cnblogs.com/fnlingnzb-learner/p/5854494.html)



### 常见的sizeof

[C/C++刁钻问题各个击破之细说sizeof - 坚持学习/暴露问题/不断提升 - C++博客 (cppblog.com)](http://www.cppblog.com/w57w57w57/archive/2011/08/09/152845.html)

sizeif作用很简单，求对象或者类型的大小。

1. sizeof是运算符，不是函数

2. sizeof不能求得void类型的长度

3. sizeof可以求得void类型的指针的长度

4. sizeof可以求得静态分配内存的数组的长度

   ```cpp
   int arr[10];
   cout << sizeof(arr) << endl; // 传递的是指针，64位机器都是8    
   cout << sizeof(arr[1]) << endl; // 这是int，所以是4
   cout << (sizeof(arr)/sizeof(arr[0])) << endl; // 输出10，sizeof确实可以计算长度
   ```

5. sizeof不能求得动态分配的内存的大小

6. sizeof不能对不完整数组求长度

7. 当表达式作为sizeof的操作数时，对于表达式计算结果求大小而不是对表达式求大小

8. 当函数作为sizeof的操作数的时候，对函数的返回类型求大小，不会调用函数体

9. sizdof求得的结构体的大小不一定等于各个成员之和

   ```cpp
   struct A {
       int a;
       double b;
       int c;
   };
   
   struct B {
       int a;
       int b;
       double c;
   };
   
   cout << sizeof(A) << " " << sizeof(B) << endl; // 24 16
   1、 结构体的大小等于结构体内最大成员大小的整数倍
   
   2、 结构体内的成员的首地址相对于结构体首地址的偏移量是其类型大小的整数倍，比如说double型成员相对于结构体的首地址的地址偏移量应该是8的倍数。
   
   3、 为了满足规则1和2编译器会在结构体成员之后进行字节填充！
   ```

10. sizeof不能用于求结构体的位域成员的大小，但是可以求得包含位域成员的结构体的大小











#### 参考文章

[C++的基础知识 | 大专栏 (dazhuanlan.com)](https://www.dazhuanlan.com/2019/11/24/5dd9b0a7c3751/)