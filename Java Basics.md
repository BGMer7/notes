# Java Basics

## Quick Start

### Code Style

```java
public class Hello {
    public static void main(String[] args) {
        // code 
    }
}
```

public是访问修饰符，表示该class是公开的，不使用public也可以正常编译，但是cmd命令行无法直接执行。

static表示静态方法，Java的main函数只能是static函数，方法名必须为main，括号内的函数必须是String数组。

Java的文档注释和C++一样，使用双斜杠//或者/* */。有一种特殊的注释采用/** */，如果有多行，每行都采用 * 开头，这种特殊的多行注释需要写在类和方法的定义处，可以用于自动创建文档。



### Variable

Java基本数据类型：

整数类型：byte，short，int，long

浮点数类型：float，double

字符类型：char

布尔类型：boolean

```ascii
       ┌───┐
  byte │   │
       └───┘
       ┌───┬───┐
 short │   │   │
       └───┴───┘
       ┌───┬───┬───┬───┐
   int │   │   │   │   │
       └───┴───┴───┴───┘
       ┌───┬───┬───┬───┬───┬───┬───┬───┐
  long │   │   │   │   │   │   │   │   │
       └───┴───┴───┴───┴───┴───┴───┴───┘
       ┌───┬───┬───┬───┐
 float │   │   │   │   │
       └───┴───┴───┴───┘
       ┌───┬───┬───┬───┬───┬───┬───┬───┐
double │   │   │   │   │   │   │   │   │
       └───┴───┴───┴───┴───┴───┴───┴───┘
       ┌───┬───┐
  char │   │   │
       └───┴───┘
```

对于整型，Java只定义了带符号数的整型，因此最高位的bit表示符号位，0表示正数，1表示负数。

对于float类型，需要在数值后面加上f后缀。

理论上布尔类型只有true和false，只需要一个bit，但是JVM内部会将boolean表示为4个字节。

字符类型char表示一个字符，Java中的char除了可以表示ascii之外，还可以表示一个unicode。和C++类似，单引号表示字符，双引号表示字符串。

**除了上述的基本类型，其他的都是引用类型，引用类型最常用的就是String字符串类型，引用变量类似于C语言指针，内部存储一个地址，这个地址指向某个对象在内存中的位置。**

常量关键字由final定义，意义和C++的const关键字一样，代码规范一般将常量全部大写。

var关键字对应C++的auto。



### Calculate

整数的数值和运算永远是精确的，即使是除法运算也是精确的，因为两个整型的除法运算得到的结果只是整数部分。**特别注意：整数的除法对于除数为0时运行时将报错，但编译不会报错。在做除法运算的时候要先检查除数是否为零。**

计算机中，整数总是以二进制表示。移位的符号仍然是>>，<<，移位操作有可能存在将一个数的符号位改变。还有一种无符号的右移运算，使用>>>，它的特点是不管符号位，右移后高位总是补0，因此，对一个负数进行>>>右移，它会变成正数，原因是最高位的1变成了0。

浮点数的运算不能使用位运算和位移运算，浮点数的表示范围虽然很大，但是，浮点数无法精确表示，在运算过程中会产生误差，在对比是否相等的时候会产生错误的答案。



### Array & ArrayList

Array可以包含基本类型和对象类型，ArrayList只能包含对象类型。
Array大小是固定的，ArrayList的大小是动态变化的。
ArrayList提供了更多的方法和特性，比如：addAll()，removeAll()，iterator()等等。

ArrayList的空间是动态增长的，如果空间不够，它会创建一个空间比原空间大约0.5倍的新数组，然后将所有元素复制到新数组中，接着抛弃旧数组。而且，每次添加新的元素的时候都会检查内部数组的空间是否足够。

标准库内置的数组排序Arrays.sort()，排序之后，在数组中的元素没有发生变化，但是数组的指向改变了。

二维数组中每个数组的长度并不要求相同。



### recursion

for(int n: Array)的for each格式更加简洁，但是无法拿到数据的索引，因此for(int i.....)还是for each取决于使用场景。

**Java标准库提供了Arrays.toString()，可以快速输出整个数组内容。对于二维数组，这个标准库方法是Arrays.deepToString()。**

```java
String[] nsdd = new String[] {"a", "b", "c"};
String nsddStr = Arrays.toString(nsdd);
System.out.println(nsddStr);
// [a, b, c]
```



### Java cmd

Java的入口是main方法，main方法接受一个命令行参数，这个参数的格式是字符串数组。





## Object-Oriented

### Field

一个Java类中包含几种变量：

1. 类中的成员变量，称为字段
2. 代码块中的变量，称为局部变量
3. 方法和构造方法中的变量，称为参数

局部变量+参数=变量，而不是字段

```java
public class myField {
    // a final field  一个常量字段
    public static final String CONST_FIELD = "THIS IS A FINAL VARIABLE";

    // a static field
    public static String static_field;

    // private a variable
    private String name;

    // public method get variable
    public String getName() {
        return name;
    }

    // public method set variable
    public void setName(String name) {
        this.name = name;
    }
}
```

所谓field，就是一个成员变量。



#### 一个.java文件内只能有一个class吗？

[一个.java文件内只能写一个class吗 - Rogn - 博客园 (cnblogs.com)](https://www.cnblogs.com/lfri/p/10552111.html)

> **一个.java文件内只能有一个public类，但是可以有多个类，public的类必须与文件名保持一致。**
>
> **一个文件中可以不含有public类，如果只有一个非public类，此时可以和文件名不同。**

1. 每个编译单元只保留一个public类表示，每个编译单元都保留单一的公共接口，用public类来表现。该接口可以包含众多包含访问权限的类，如果在编译单元内有多个公共接口，编译器会报错。
2. 如果公共接口和文件名不一致，编译报错。

main函数并不要求一定要写在public中，虽然通常的做法是如此，但是也可以将main函数写在非public类中，例如：

```java
public class myField {
    // a final field  一个常量字段
    private static final String CONST_FIELD = "THIS IS A FINAL VARIABLE";

    // a static field
    private static String static_field;

    // private a variable
    private String name;

    // public method get variable
    public String getName() {
        return name;
    }

    // public method set variable
    public void setName(String name) {
        this.name = name;
    }
}

class run{
    public static void main(String[] args) {
        myField mf = new myField();
        mf.setName("Gatsby");
        System.out.println(mf.getName());
    }
}
```



### this

在方法的内部，始终有一个隐含变量this，始终指向当前的实例。通过this.field可以访问当前实例的片段。

如果没有命名冲突，可以省略this。

例如在上例中，MyField中的name变量如果是xingming，那么在setName方法中，就可以直接写mingzi=name，因为mingzi和name不冲突。但是如果已经有一个field的名字和形参一样，就要改成this.name=name。



### 可变参数

可变参数使用三点省略号表示。

```java
class Group {
    private String[] names;

    public void setNames(String... names) {
        this.names = names;
    }
}

Group g = new Group();
g.setName("a", "b", "c");
g.setName("a", "b");
g.setName("a");

```

```java
package org.example;
/*
field字段测试
 */


public class MyField {
    // a final field  一个常量字段
    private static final String CONST_FIELD = "THIS IS A FINAL VARIABLE";

    // a static field
    private static String static_field;

    // private a variable
    private String name;

    // public method get variable
    public String getName() {
        return name;
    }

    // public method set variable
    public void setName(String name) {
        this.name = name;
    }
}

class RunMyField{
    public static void main(String[] args) {
        MyField mf = new MyField();
        mf.setName("Gatsby");
        System.out.println(mf.getName());
    }
}

// 运行结果
// 2
// [cai, jinyang]
```



### Constructor

在创建参数的时候我们有时候需要直接给字段初始化。因此使用构造方法将字段初始化为合适的值。

构造方法的名称就是类名，构造方法的参数没有限制，在方法内部，也可以编写任何语句。但是构造方法特殊在于，构造方法没有返回值，也没有void，调用构造方法，必须使用new。

所有的类都有构造方法，如果我们没有自定义构造方法，编译器会为我们自动生成一个构造方法，没有参数，也没有执行语句。但是如果我们自己定义了构造方法，那么编译器就不再创建默认的构造方法。也就是说，如果我们写了类似这样一个构造方法：

```java
class Person {
    private String name;
    private int age;
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

那么我们调用的时候，

```java
Person gatsby = new Person();
```

此时调用是报错的，因为编译器找不到这个构造方法。

那么如果采用无参数构造函数的话，我们还需要定义一个构造函数，用来重载。

```java
class Person {
    private String name;
    private int age;
    
    public Person() {
        
    }
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

在Java实例化的过程中，先初始化字段，再执行构造函数。因此，如果如果构造函数中有字段的赋值，字段会被构造方法覆盖。



### Overload

方法名相同，但是参数类型不同的方式叫重载。重载的返回值一般都是相同的。



### Inherit

继承是面向对象编程中非常强大的一种机制，它首先可以复用代码。

Java使用extends关键字来继承。

**通过继承，子类自动获得了父类的所有字段，所以严禁定义和父类中字段重名的字段。**

实际上，在我们定义类的时候，Java都默认加上了extends object，Java的任何类，除了Object，都从某个类继承而来。Java只允许一个class继承一个类，因此一个类有且仅有一个父类，除了Object没有父类。

但是在继承中，子类无法访问父类的private字段和private方法，为了可以使得子类可以访问父类的字段，我们需要使用protected修饰。



### super

super表示超类，子类引用父类的字段时候，可以使用super.fieldname。

**但是所有的继承的class，在第一行都会调用父类的构造方法，如果没有显式调用父类的构造方法。编译器会帮我们自动加一条super()。**

因此，如果是一个继承的子类，且没有声明构造方法，那么编译器在自动加上构造方法的时候就会使用super()，如果此时父类的构造方法中，全部都是带参数的构造方法，那么super()就不知道调用哪个，此时就会出现编译错误。要么将父类中的构造方法添加一个无参数方法，要么将子类中的构造方法显式声明且加上参数。

子类不会继承父类的构造函数，子类的构造方法是由编译器自动生成的。

```java
package org.example;

class Person {
    protected int age = 13;
    protected String name = "gatsby";
}

class Student extends Person {
    protected int grade=9;

    public int getGrade() {
        System.out.println(this.name + " is " + this.age + " years old, and he's in grade " + grade);
        return this.grade;
    }
}

class Teacher extends Person {
    protected String course;

    public void setAge(int age) {
        this.age = age;
    }

    public void setCourse(String course) {
        this.course = course;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void getPersonName() {
        if(super.name != this.name) {
            System.out.println(this.name);
        }
        // 直接返回基类的name字段，但是实际上不会用super调用父类的字段，而是构造方法和方法
        else if(super.name == "gatsby") {
            System.out.println("The teacher's name hasn't been set.");
        }
        else {
            System.out.println(super.name);
        }
    }

}

public class MyInherit {
    public static void main(String[] args) {
        Student me = new Student();
        me.getGrade();

        Teacher t = new Teacher();
        t.setAge(40);
        t.getPersonName();
        t.setName("Alex");
        t.getPersonName();
    }
}

```



### Override

在继承关系中，子类如果定义了一个与父类方法签名完全相同的方法，被称为覆写（Override）。

注解@Override的作用是让编译器检查这个方法，保证父类中一个要被该方法重写的方法，否则会编译报错。该注解的作用是为了避免override失败，程序运行出错。

```java
public class Main {
    public static void main(String[] args) {
        Person p = new Student();
        p.run(); // 实际上调用的是student的run()->多态
    }
}

class Person {
    public void run() {
        System.out.println("Person.run");
    }
}

class Student extends Person {
    @Override
    public void run() {
        System.out.println("Student.run");
    }
}
```

**Java的实例方法调用是基于运行时的实际类型的动态调用，而非变量的声明类型。**

**这个非常重要的特性在面向对象编程中称之为多态。它的英文拼写非常复杂：Polymorphic。**



### final

有时候有的类不允许继续扩展子类，不允许扩展的类称为final类。如果需要阻止这个类被继续继承，就只需要在这个类声明的时候加上修饰符final。

```java
public final class CEO {
    ...
}
```

也可以将类中的特定的方法修饰为final，此时子类不能覆盖这个方法，当然，如果这个类是一个final类，那么这个类中的所有方法都是final方法。



### 多态

在Java中，对象变量是多态的，一个父类变量既可以引用一个父类对象，也可以引用任意一个该父类的子类的对象。

假设Manager是Employee的子类，

```java
Manager boss  = new Manager();
Employee[] staff = new Employee[10];
staff[0] = boss;
```

此时，stuff[0]和boss都是引用同一个对象，但是编译器将staff[0]看作是Employee对象。

所以此时，

```java
// legal
boss.setBonus(1000);
//illegal
staff[0].setBonus(1000);
```

并且，不能将一个父类的引用赋值给子类变量，原因很简单，不是所有的雇员都是经理，但是经理一定是雇员。



多态是指，针对某个类型的方法调用，其真正**执行的方法取决于运行时期实际类型**的方法。

多态存在的三个必要条件：

1. 继承
2. 重写
3. 父类的引用指向子类：Parent p = new Child()

<img src="https://www.runoob.com/wp-content/uploads/2013/12/2DAC601E-70D8-4B3C-86CC-7E4972FC2466.jpg" alt="img" style="zoom: 33%;" />

当使用多态方式调用方法的时候，首先检查父类中是否有该方法，如果没有的话，则编译错误，如果有的话，检查再去调用子类的方法。



### Abstract

由于多态的存在，所以每一个子类都可以覆写父类的方法。

抽象方法充当着占位的作用，在父类中写一个抽象类，却不做任何的功能设计。

```java
public abstract class Person {
    private String name;
    
    public Person(String name) {
        this.name = name;
    }
    
    public abstract class getDescription();
    
    public String name() {
        return this.name;
    }
    
}
```

我们认为，一个人类跑步，这个事情毫无一样，不能说某一种种族在做什么事情，而是应该具体到某个个人。所以Person这种抽象类的具体实现都是在子类，**扩展抽象类可以有两种选择，一种是在抽象类中定义部分抽象类方法或不定义抽象类方法，这样就必须把子类也定义成抽象类。另一种是定义全部的抽象方法，这样一来，子类就不是抽象的了。**  -----《Java核心技术 第10版》

此时通过抽象Person类，并实现getDescription来实现Student类，由于在Student类中不再含有抽象类，所以不必再将Student类定义为抽象类。

> 类即使不含有抽象方法也可以声明为抽象类。

抽象类最重要的一个特点是抽象类不能被实例化，例如

```java
new Person("Gatsby")
```

这是错误的实例化，因为Person是一个抽象类。需要注意，可以定义一个抽象类的对象变量，但是它只能引用非抽象子类的对象。例如

```java
Person gatsby = new Student("Gatsby");
```

这样的实例是合法的。

```java
package org.example;

abstract class Animal {
    // 因为这个参数要被继承，所以必须是protected，否则无法调用这个字段
    protected String name;

    public Animal(String name) {
        this.name = name;
    }

    public abstract void run();

    public String getName(){
        return this.name;
    };
}

class Cat extends Animal {
    private int weight;

    public Cat(String name, int weight) {
        super(name);
        this.weight = weight;
    }

    public String getName(){
        return this.name;
    }

    public void run() {
        System.out.println(super.name + " the cat's running");
    }

    public int getWeight() {
        return this.weight;
    }
}

public class MyAbstract {
    public static void main(String[] args) {
        // Animal无法直接实例化
        // error: Animal a = new Animal("King Tiger")

        // 但是可以引用一个非抽象类的子类
        Animal king_tiger = new Cat("King Tiger", 100);
        // 此时由于多态的特性，会自动调用子类的方法
        king_tiger.run();
        // "King Tiger the cat's running"
    }

}
```

**通俗一点，抽象方法就是，这个方法我实现不了，也不应该由我来实现，但是子类，你记得实现。你不实现你没法编译。---个人归纳。**



### interface

所谓interface，就是比抽象类还要抽象的类，纯粹就是一个抽象接口，连字段也没有。因为所有的接口的定义都是默认public abstract，所以这两个修饰符一般不需要写出，写了和没写作用是一样的。

当使用具体的class去实现一个具体的interface时，需要implements关键字。

```java
interface Person() {
    void run();
    String getName();
}
```

interface实现：

```java
class Student implements Person {
    private String name ;
    
    public Student(String name) {
        this.name = name;
    }
    
    @Override
    public void run() {
        System.out.println(this.name + " run");
    }
    
    @Override
    public String getName() {
        return this.name;
    }
} 
```

虽然Java没有多继承，一个类只能继承另外一个类，但是，一个类可以实现多个interface。

```java
class Student implements Person, Kid {
    
}
```

interface和abstract class的区别：

| abstract class | interface            |                             |
| :------------- | :------------------- | --------------------------- |
| 继承           | 只能extends一个class | 可以implements多个interface |
| 字段           | 可以定义实例字段     | 不能定义实例字段            |
| 抽象方法       | 可以定义抽象方法     | 可以定义抽象方法            |
| 非抽象方法     | 可以定义非抽象方法   | 可以定义default方法         |

#### interface inherit

一个interface可以继承另一个interface，同样是使用extends关键字。相当于扩展了接口的方法。

```java
interface Hello {
    void hello();
}

interface Person extends Hello {
    void run();
    String getName();
}
```

此时，Person interface实际上有三个抽象方法签名，其中一个是由继承Hello提供。

实际上，在使用的时候，实例化的对象永远只能是某个具体的子类，但总是通过接口去引用它，因为接口比抽象类更抽象。



### default

default主要用于interface中，default使得method在interface中可以有方法体。

例如原本一个interface应该是这样的：

```java
public interface Default {
    public void method();
}
```

加上default关键字之后就可以在interface中写方法体，

```java
public interface Default {
    default public void run() {
        System.out.println("method in the interface")
    }
}
```

这时候写一个implement

```java
public class DefaultImpl implements Default {
    @Override
    public void method() {
    	System.out.println("method in the class")
    }
}
```

但是如果子类没有Override interface中的default方法，那么就会按照default中的方法体进行调用，

```java 
public interface Default {
 
    default public void method(){
        System.out.println("method in the interface");
    }
 
    default public void doSomeThing(){
        System.out.println("do something in interface");
    }
}

public class DefaultImpl implements Default {
 
    @Override
    public void method() {
        System.out.println("method in the Class");
    }
 
    public static void main(String[] args){
        Default d = new DefaultImpl();
        d.method();
        d.doSomeThing();
    }
}

// method in the interface 
// do something in interface
```



### static

#### static field

如果将field定义为static，每个类中只有一个这样的field，这个field属于类而不是实例。但是每个对象所有的实例field都有一份自己的拷贝。

```java
class Employee {
    private static String company = 1;
    private int id;
}
```

此时，每个实例都有一个私有的id，这个id属于每个Employee的唯一标识码，但是所有的Employee实例将会共享同一个Company，换句话说，如果有1000个Employee，就会有1000个id，但是仍然是一个company，即使一个Employee对象都没有，company也依然存在，这个static field属于类而不是实例。

> 注释：在绝大多数的面向对象程序设计语言中，静态域被称为类域。术语“static”只是沿用了C++的叫法，并无实际意义。

#### staic final 

静态变量使用得比较少，但是静态常量使用很多。

```java
class Math {
    public static final PI=3.141592653;
}
```

在程序中可以直接调用Math.PI获取这个常量。如果关键字static被省略，PI就变成了Math类的一个实例域。需要通过Math类的对象访问PI，并且每一个Math对象都有它自己的一份PI拷贝。

#### static method

原理类似静态变量，访问非静态变量的时候，需要创建一个实例调用这个方法，

```java 
public class MyStatic {
    public static String staticString = "static";

    // 非静态方法，属于实例
    public String nonStaticMethod() {
        staticString += " nonStatic";
        System.out.println("after nonStaticMethod, staticString: "+staticString);
        return staticString;
    }

    // 静态方法，属于类
    public static String staticMethod1() {
        staticString += " staticMethod1";
        System.out.println("after staticMethod1, staticString: "+staticString);
        return staticString;
    }

    // 静态方法，属于类
    public static String staticMethod2() {
        staticString += " staticMethod2";
        System.out.println("after staticMethod2, staticString: "+staticString);
        return staticString;
    }

    public static void main(String[] args) {
        MyStatic ms = new MyStatic();
        ms.nonStaticMethod(); // 非静态方法，需要通过实例调用
        MyStatic.staticMethod1(); // 静态方法，通过类名调用
        staticMethod2(); // 静态方法，直接调用
    }
}
```

#### static final field in interface 

interface不能拥有field，但是可以拥有static final field

```java 
public interface Person {
    public static final int MALE = 1;
    public static final int FEMALE = 2;
}

public interface Person {
    // 编译器会自动加上public statc final:
    int MALE = 1;
    int FEMALE = 2;
}

// Person.MALE = 1
```



### package

Java的namespace叫package，一个类总是属于一个package，class只是一个简写，实际代表的是package.class。在JVM执行的时候，JVM只看完整的类名，因此只要package不同，类就不同。

> package没有父子继承关系，java.util和java.util.zip完全没有任何联系。

**位于同一个包的类，可以访问包作用域的字段和方法。**

没有使用public private protected修饰的field和method的作用域就是包作用域。

引入其他的class主要有三种办法，第一种写出完整类名，第二种import package然后写简单的class，import package  *表示引入package下面的所有class。



## naming conventions

[Google Java Style Guide](https://google.github.io/styleguide/javaguide.html)













## Exception

Java的异常也是class，继承关系如下。

```ascii
                     ┌───────────┐
                     │  Object   │
                     └───────────┘
                           ▲
                           │
                     ┌───────────┐
                     │ Throwable │
                     └───────────┘
                           ▲
                 ┌─────────┴─────────┐
                 │                   │
           ┌───────────┐       ┌───────────┐
           │   Error   │       │ Exception │
           └───────────┘       └───────────┘
                 ▲                   ▲
         ┌───────┘              ┌────┴──────────┐
         │                      │               │
┌─────────────────┐    ┌─────────────────┐┌───────────┐
│OutOfMemoryError │... │RuntimeException ││IOException│...
└─────────────────┘    └─────────────────┘└───────────┘
                                ▲
                    ┌───────────┴─────────────┐
                    │                         │
         ┌─────────────────────┐ ┌─────────────────────────┐
         │NullPointerException │ │IllegalArgumentException │...
         └─────────────────────┘ └─────────────────────────┘
```

**Error一般表示严重错误，程序对此无能为力**：

- OutOfMemory：内存耗尽

- NoClassDefFoundError：无法加载某个类

- StackOverflowError：栈溢出


Exception是运行是的错误，可以被捕获并被处理。

有些异常是应用逻辑处理的一部分，常见的有：

- NumberFormatException：数值类型的格式错误
- FileNotFoundException：未找到文件
- SocketException：读取网络失败

有些异常是程序逻辑编写不对，应该修复程序本身

- NullPointException：对某个null对象调用字段或者方法
- IndexOutOfBoundException：数组索引越界

Exception又分为两大类：

1. RuntimeException以及它的子类
2. 非RuntimeException，包括IOException等等



### try catch

```java
import java.io.UnsupportedEncodingException;
import java.util.Arrays;

/**
 * @ClassName: MyException
 * @Description: my exception
 * @author: Gatsby
 * @date: 2021/9/2 14:25
 */

public class MyException {

    public static void main(String[] args) {
        byte[] bs = toGBK("中文");
        System.out.println(Arrays.toString(bs));
    }

    static byte[] toGBK(String s) {
        try {
            // 用指定编码转换String为byte[]:
            return s.getBytes("GBK");
        } catch (UnsupportedEncodingException e) {
            // 如果系统不支持GBK编码，会捕获到UnsupportedEncodingException:
            System.out.println(e); // 打印异常信息
            return s.getBytes(); // 尝试使用用默认编码
        }
    }
}
```

如果toGBK没有写try catch的话，编译器会报错unreported exception UnsupportedEncodingException; must be caught or declared to be thrown，意思是UnsupportedEncodingException这个异常是必须要被捕获的，因为，String.getBytes(String)的定义是public byte[] getBytes(String charsetName) throws UnsupportedEncodingException。在写方法的时候，使用throws表示该方法可能抛出的异常类型，调用方在调用的时候，就必须捕获这个异常，否则编译器报错。

或者在toGBK方法之后改为

```java
static byte[] toGBK(String s) throws UnsupportedEncodingException {
    return s.getBytes("GBK");
}
```

告诉编译器这个方法可能会抛出异常，这样编译也能通过。

```java
public class MyException {

    public static void main(String[] args) {
        byte[] bs = toGBK("中文");
        System.out.println(Arrays.toString(bs));
        try {
            byte[] bs_ = to_GBK("中文"); // 如果to_GBK会抛出异常，那么就要在上一层的方法捕获
        } catch (UnsupportedEncodingException e) {
            System.out.println(e);
        }

    }

    static byte[] toGBK(String s) {
        try {
            // 用指定编码转换String为byte[]:
            return s.getBytes("GBK");
        } catch (UnsupportedEncodingException e) {
            // 如果系统不支持GBK编码，会捕获到UnsupportedEncodingException:
            System.out.println(e); // 打印异常信息
            return s.getBytes(); // 尝试使用用默认编码
        }
    }

    static byte[] to_GBK(String s) throws UnsupportedEncodingException {
        return s.getBytes("GBK");
    }
}
```

或者将main方法定义为抛出异常：

```java
public class Main {
    public static void main(String[] args) throws Exception {
        byte[] bs = toGBK("中文");
        System.out.println(Arrays.toString(bs));
    }

    static byte[] toGBK(String s) throws UnsupportedEncodingException {
        // 用指定编码转换String为byte[]:
        return s.getBytes("GBK");
    }
}

```

**因为main()方法声明了可能抛出Exception，也就声明了可能抛出所有的Exception，因此在内部就无需捕获了。代价就是一旦发生异常，程序会立刻退出。**

如果捕获了异常，即使对于异常没有什么能做的，也应该将异常先记录下来。

> 所有的异常都可以使用e.printStackTrace()打印异常栈。

```java
import java.io.UnsupportedEncodingException;
import java.util.Arrays;

/**
 * @ClassName: MyException
 * @Description: my exception
 * @author: Gatsby
 * @date: 2021/9/2 14:25
 */

public class MyException {

    public static void main(String[] args) {
        byte[] bs = toGBK("中文");
        System.out.println(Arrays.toString(bs));
        try {
            byte[] bs_ = to_GBK("中文"); // 如果to_GBK会抛出异常，那么就要在上一层的方法捕获
        } catch (UnsupportedEncodingException e) {
            System.out.println(e);
        }

        try {
            int n = 2/0;
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }

        System.out.println("is running! ");

    }

    static byte[] toGBK(String s) {
        try {
            // 用指定编码转换String为byte[]:
            return s.getBytes("gb2312");
        } catch (UnsupportedEncodingException e) {
            // 如果系统不支持GBK编码，会捕获到UnsupportedEncodingException:
            System.out.println(e); // 打印异常信息
            e.printStackTrace();
            return s.getBytes(); // 尝试使用用默认编码
        }
    }

    static byte[] to_GBK(String s) throws UnsupportedEncodingException {
        return s.getBytes("GBK");
    }
}
// [-42, -48, -50, -60]
// / by zero
// is running! 
// java.lang.ArithmeticException: / by zero
//  	at org.example.MyException.main(MyException.java:27)

```



### assert

assertion断言是一种Java常用的调试方法，

```java
public static void main(String[] args) {
    double x = Math.abs(-123.45);
    assert x >= 0; // x>=0的预期为true，如果为false则断言失败，抛出AssertionError
    System.out.println(x);
}
```

使用assert的时候，还可以添加一个可选的断言消息，

```java
assert x>=0: "x must >= 0";
```

如果出现断言失败，AssertError会带上x must >= 0的消息。

**Java断言的特点是：断言失败时会抛出`AssertionError`，导致程序结束退出。因此，断言不能用于可恢复的程序错误，只应该用于开发和测试阶段。**

**对于可恢复的程序错误，不应该使用断言。JVM默认关闭assert，遇到assert直接跳过。**



### log







## reflection

Java的反射是指，在程序的运行期间，可以拿到一个对象的所有信息。

反射是为了解决在运行期，对某个实例一无所知的情况下，如何调用其方法。

总体来说，反射机制用来

1. 在运行的时候分析类
2. 在运行的时候查看对象，例如编写一个toString()方法供所有的用户使用。
3. 实现通用的数组操作代码
4. 利用method对象，类似于C++中的函数指针



### Class

在Java语言中，除了基本类型， 所有的其他的类型都是class，包括interface，String Object Runnable Exception都是class，class的本质是数据类型，没有继承关系的数据类型无法赋值。

```java
Number n = new Double(12.3);
String s = new Double(12.3); // compile error
```

class由JVM在执行的过程中动态加载，JVM第一次读取到class的时候，将其加载进内存。

每加载一种class，JVM就为其创建一个Class类型的实例，这个Class，也是一个class。

``` {
public final class Class {
	private class() {}
}
```

以String为例，当JVM加载String类时，首先读取String.class文件到内存中，然后为String类创建一个class实例并且关联起来：

```java
class cl = new class(String);
```

这个Class是由JVM创建的，如果我们查看JDK源码，Class的构造方法是private的，只有JVM能创建Class，用户的Java程序是无法创建的。

所以此时，JVM持有的每个Class实例都指向一个数据类型（class或者interface）

```ascii
┌───────────────────────────┐
│      Class Instance       │──────> String
├───────────────────────────┤
│name = "java.lang.String"  │
└───────────────────────────┘
┌───────────────────────────┐
│      Class Instance       │──────> Random
├───────────────────────────┤
│name = "java.util.Random"  │
└───────────────────────────┘
┌───────────────────────────┐
│      Class Instance       │──────> Runnable
├───────────────────────────┤
│name = "java.lang.Runnable"│
└───────────────────────────┘
```

一个Class实例包含了该class的所有完整信息

```ascii
┌───────────────────────────┐
│      Class Instance       │──────> String
├───────────────────────────┤
│name = "java.lang.String"  │
├───────────────────────────┤
│package = "java.lang"      │
├───────────────────────────┤
│super = "java.lang.Object" │
├───────────────────────────┤
│interface = CharSequence...│
├───────────────────────────┤
│field = value[],hash,...   │
├───────────────────────────┤
│method = indexOf()...      │
└───────────────────────────┘
```

由于JVM为每个class都创建了对应的Class实例，并在实例中保存了该class的所有信息，包括类名、包名、父类、实现的接口、所有方法、字段等，所以获得了Class的实例，就是获得了class的信息。



### field access

对于任何的一个Object实例，只要获取了Class的实例，就可以获得全部信息。

下一步需要获得field信息。





## annotation

注解是直接放在Java源码的类、方法、字段、参数前的一种特殊的注释

注释被编译器直接忽略，但是注解可以被编译器打包进入class文件，因此注解是一种用作标注的元数据。

第一类是由编译器使用的注解：例如@Override让编译器检查是否正确覆写，@SuppressWarnings忽略此处产生的警告。这类注解不会被编译进入.class文件，在编译后被丢弃了。

第二类是由工具.class文件使用的注解，比如有些工具会在加载的时候对class做动态修改，实现一些特殊的功能，这类注解会被编译进入.class文件，但是不会进入内存中。

第三类是在程序运行期能够读取的注解，它们在加载后一直存在于JVM中，这也是最常用的注解。例如，一个配置了@PostConstruct的方法会在调用构造方法后自动被调用（这是Java代码读取该注解实现的功能，JVM并不会识别该注解）。



### common & useful annotation

#### bean declaration annotation

1. @Component：泛指各类组件，当这个组件不属于@Services、@Controller的时候，就可以使用@Component，把普通的pojo实例化到Spring IoC容器中。

2. @Controller：在展现层使用，控制器的声明，用于标记在一个类上，使用它标记的类就是一个SpringMVC的Controller对象，分发处理器会扫描使用该注解的类的方法，并检测该方法是否使用了@RequestMapping注解。@Controller只是定义了一个控制器类，而使用@RequestMapping注解的方法才是处理请求的处理器。

3. @Service：在业务逻辑层使用（service层注入dao）用于标注服务层，主要用于进行业务逻辑。

4. @Repository：用于标注数据库访问层，也可以说被作为持久层操作（数据库）的bean来使用，即dao层。

   @Repository(value="userDao")让Spring创建名字叫”userDao“的一个userDaoImpl实例。

   ```java
   //也可以使用@Component，效果都是一样的，只是为了声明为bean
   @Repository
   @Mapper // 作用和@Repository是一样的
   public interface UserDao {
     
     @Insert("insert into user(account, password, user_name) " +
               "values(#{user.account}, #{user.password}, #{user.name})")
       int insertUser(@Param("user") User user) throws RuntimeException;
   }
   
   作者：江夏
   链接：https://zhuanlan.zhihu.com/p/148537889
   来源：知乎
   著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
   ```



#### bean injection annotation

1. @Autowired：对类的成员变量、方法、构造方法、进行标注，完成自动装配的工作，通过Autowired来消除set和get方法。

   值得一提的是，Autowired翻译成自动装配比较合适，通过自动装配从IoC容器中查找到并返回给该属性。
   
   ```java
   @Repository("userRepository")
   public class UserRepositoryImps implements UserRepository{
   
       @Override
       public void save() {
           System.out.println("UserRepositoryImps save");
       }
   }    
   
   @Service
   public class UserService {
       @Autowired
       private UserRepository userRepository;
   
       public void save(){
           userRepository.save();
       }
   }
   
   作者：江夏
   链接：https://zhuanlan.zhihu.com/p/148537889
   来源：知乎
   著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
   ```
   
2. @Inject：Inject可以作用在变量、setter、构造方法上，根据类型自动装配，如果需要根据名称自动装配，使用@Named。

   @Inject主要使用在变量、setter方法、构造方法上，使用方法与@Autowired一样。

   ```java 
   public class User {
       private Person person;
   
       @Inject
       public void setPerson(@Named("main") Person person) {
           this.person = person;
       }
   }
   ```

   @Autowired、@Inject和@Resource都可以注解在set方法和属性上，推荐注解在属性上。

3. @Resource：@Resource是根据名称进行自动装配的，一般会指定一个name属性。

   ```java
   public class User {
       private Person person;
   
       @Resource(name="myPerson")
       public void setPerson(Person person) {
           this.person = person;
       }
   }
   ```



#### configuration annotation

1. @Bean：Spring的@Bean注解用于告诉方法，产生一个Bean对象，然后将这个对象交给Spring容器管理。产生这个Bean对象的方法Spring只会调用一次，随后这个Spring将会将这个Bean对象放在自己的IOC容器中。SpringIOC 容器管理一个或者多个bean，这些bean都需要在@Configuration注解下进行创建，在一个方法上使用@Bean注解就表明这个方法需要交给Spring进行管理。

   ```java
   @Configuration
   public class AppConfig {
       // 未指定bean 的名称，默认采用的是 "方法名" + "首字母小写"的配置方式
       @Bean
       public MyBean myBean(){
           return new MyBean();
       }
   }
   
   public class MyBean {
   
       public MyBean(){
           System.out.println("MyBean Initializing");
       }
   }
   ```

   @Bean的参数有：value、name、autowired、initMethod、destroyMethod。
   
   value和name的名称是相同的，如果没有自定义name和value，那么就使用方法的名称作为该bean的名称。
   
   autowire指定 bean 的装配方式， 根据名称 和 根据类型 装配， 一般不设置，采用默认即可。autowire指定的装配方式 有三种Autowire.NO (默认设置)、Autowire.BY_NAME、Autowire.BY_TYPE。
   
   initMethod和destroyMethod指定bean的初始化方法和销毁方法， 直接指定方法名称即可，不用带括号。
   
   ```java
   public class MyBean {
   
       public MyBean(){
           System.out.println("MyBean Initializing");
       }
   
       public void init(){
           System.out.println("Bean 初始化方法被调用");
       }
   
       public void destroy(){
           System.out.println("Bean 销毁方法被调用");
       }
   }
   
   @Configuration
   public class AppConfig {
   //  @Bean
       @Bean(initMethod = "init", destroyMethod = "destroy")
       public MyBean myBean(){
           return new MyBean();
       }
   }
   ```
   
   ：@Scope 设置Spring容器如何新建Bean实例，默认是@scope("Singleton ")，即单例模式。其设置类型包括：
   
   Singleton （单例,一个Spring容器中只有一个bean实例，默认模式）,
   Protetype （每次调用新建一个bean）,
   Request （web项目中，给每个http request新建一个bean）,
   Session （web项目中，给每个http session新建一个bean）,
   GlobalSession（给每一个 global http session新建一个Bean实例）

2. @ComponentScan：ComponentScan主要就是定义扫描的路径从中找出标识了需要装配的类自动装配到spring的bean容器中。前面说到过@Controller注解，@Service，@Repository注解，它们其实都是组件，属于@Component注解，而@ComponentScan注解默认就会装配标识了@Controller，@Service，@Repository，@Component注解的类到spring容器中。

   @WishlyConfiguration 为@Configuration与@ComponentScan的组合注解，可以替代这两个注解。



#### value annotation

1. @Value：@Value的作用是是通过注释将常量、配置文件中的数值，作为变量的初始值。

   1. 普通注入

      ```java 
      @Value("张三")
      private String name; // 注入普通字符串
      ```

   2. 属性、表达式注入：bean属性注入需要注入者和被注入者属于同一个IOC容器，或者父子IOC容器关系，在同一个作用域内。

      ```java
      // 注入其他Bean属性：注入beanInject对象的属性another，类具体定义见下面
      @Value("#{beanInject.another}")
      private String fromAnotherBean; 
      // 注入操作系统属性
      @Value("#{systemProperties['os.name']}")
      private String systemPropertiesName; 
      //注入表达式结果
      @Value("#{T(java.lang.Math).random() * 100.0 }")
      private double randomNumber;
      ```
      
   3. 配置文件注入同理
   
      ```java
      @Value("${app.name}")
      private String appName;
      ```



#### AOP annotation

见下文AOP

@Aspect 声明一个切面（类上）

使用@After、@Before、@Around定义建言（advice），可直接将拦截规则（切点）作为参数。

@After 在方法执行之后执行（方法上）

@Before 在方法执行之前执行（方法上）

@Around 在方法执行之前与之后执行（方法上）

@PointCut 声明切点



#### HTTP request

1. GET

   请求从服务器获取特定资源。

   @GetMapping("users") == @RequestMapping(value="/users",method=RequestMethod.GET)

2. POST

   在服务器上创建一个新的资源。

   @PostMapping("users") == @RequestMapping(value="/users",method=RequestMethod.POST)

3. PUT

   更新服务器上的资源。

   @PutMapping("/users/{userId}") == @RequestMapping(value="/users/{userId}",method=RequestMethod.PUT)

4. DELETE

   从服务器上删除特定的资源。

   @DeleteMapping("/users/{userId}") == @RequestMapping(value="/users/{userId}",method=RequestMethod.DELETE)	

   

#### Front-Back End DataTrans

1. @PathVariable和@RequestParam

   PathVariable用于获取路径参数，@RequestParam用于获取查询数据。

   ```java
   @RequestMapping("/class/{classId}/teachers")
   public List<Teacher> getClassRelatedTeachers(@PathVariable("classId") Long classId, @RequestParam(value="type", required = false, ) String type)
   ```

   如果我们的url传入进来/class/G3CA/teachers?type=top，那么获取到的数据就是classId为G3CA，type为top

2. @RequestBody

   用于读取 Request 请求（可能是 POST,PUT,DELETE,GET 请求）的 body 部分并且**Content-Type 为 application/json** 格式的数据，接收到数据之后会自动将数据绑定到 Java 对象上去。系统会使用`HttpMessageConverter`或者自定义的`HttpMessageConverter`将请求的 body 中的 json 字符串转换为 java 对象。

   假设JSON：

   ```json
   {
       "city": "shanghai",
       "nation": "China",
   }
   ```

   Java类：

   ```java
   @Data
   public class Person {
       privata String city;
      	private String nation;
   }
   ```

   此时

   ```java
   @PostMapping
   public ResponseEntity signUp(@RequestBody Person person) {
       service.save(person);
       return ResponseEntity.ok().build();
   }
   ```

   @RequestBody会直接将JSON转为Java对象.

















## Multithreading

Java内置了多线程支持，一个JVM其实就是一个进程，JVM用一个主线程来执行main()方法，在main()方法内部，我们还可以启动多个线程。此外JVM还有GC之类的多个工作线程。

对于Java的多任务来说，多任务实际上就是多线程处理多任务。



### Thread

创建线程的三种方式

1. 继承Thread类创建线程
2. 实现Runnable接口创建线程
3. 使用Callable和Future创建线程

```java
// 继承Thread创建
public class MyThread1 extends Thread {
    @Override 
    public void run() {
        // 重写自己的run方法的内容
    }
}
public class Main {
　　public static void main(String[] args){
　　　　new MyThread1().start(); //创建并启动线程
　　}
}

// 通过Runnable接口创建线程，和Thread继承的方法一样，都要重写run()方法，和Thread中的run()一样，都是线程的执行体。
public class MyThread2 implements Runnable {
    @Override
    public void run() {
        // 重写run方法的内容
    }
}

public static void main(String[] args) {
    // 创建线程并启动稍有区别
    MyThread2 myThread = new MyThread2();
    new Thread(new MyThread2()).start();
}
```

```java
public class Main {
    public static void main(String[] args) throws InterruptedException {
        Thread t = new Thread(() -> {
            System.out.println("hello");
        });
        System.out.println("start");
        t.start();
        t.join();
        System.out.println("end");
    }
}
// start
// hello
// end
```



### Thread & Runnable

首先说结论，Thread和Runnable本身并没有什么实质上的区别，如果又区别的话也就是class和interface的区别。

1. Runnable是一个interface，是依靠implements实现，
2. Thread的实现方法是继承
3. Runnable接口支持多继承但是基本用不上
4. Thread实现了Runnable接口并且做了扩展，而Thread和Runnable实际上是实现的关系

```java
class Test {
    public static void main(String[] args) {
        
        new ThreadNRunnable().start();
        new ThreadNRunnable().start();
    }
}


public class ThreadNRunnable extends Thread {
    private int ticket = 5;

    @Override
    public void run() {
        while(true) {
            System.out.println("Thread ticket = " + ticket--);
            if(ticket<0) {
                break;
            }
        }
    }

}

Thread ticket = 5
Thread ticket = 4
Thread ticket = 5
Thread ticket = 4
Thread ticket = 3
Thread ticket = 3
Thread ticket = 2
Thread ticket = 1
Thread ticket = 2
Thread ticket = 1
Thread ticket = 0
Thread ticket = 0
```

由此可见，Thread并不能实现资源共享，但是是由于，在代码中，我们创建了两个ThreadNRunnable对象，每个对象都有自己的ticket field，但是如果我们只创建一个代码。

```java
class Test {
    public static void main(String[] args) {
        
        ThreadNRunnable t = new ThreadNRunnable();
        new Thread(t).start();
        new Thread(t).start();
    }
}


public class ThreadNRunnable extends Thread {
    private int ticket = 5;

    @Override
    public void run() {
        while(true) {
            System.out.println("Thread ticket = " + ticket--);
            if(ticket<0) {
                break;
            }
        }
    }

}

Thread ticket = 5
Thread ticket = 3
Thread ticket = 4
Thread ticket = 2
Thread ticket = 1
Thread ticket = 0
```

因为多线程访问同一变量会有并发问题（需要加锁），所以Thread正确的写法如下：

```java
public class MyThread extends Thread {

    private int ticket = 5;

    @Override
    public void run() {
        if (ticket > 0) {
            synchronized (this) {
                if (ticket > 0) {
                    while (true) {
                        System.out.println("Thread:" + Thread.currentThread().getName() + "--Thread ticket = " + ticket--);
                        if (ticket < 0) {
                            break;
                        }
                    }
                }
            }
        }
    }

}

public class Test {
    public static void main(String[] args) {

            //Test Thread

        MyThread t1 = new MyThread();
        new Thread(t1).start();
        new Thread(t1).start();
        new Thread(t1).start();
        new Thread(t1).start();
        new Thread(t1).start();
        new Thread(t1).start();
    }
}

执行结果如下：
Thread:Thread-1--Thread ticket = 5
Thread:Thread-1--Thread ticket = 4
Thread:Thread-1--Thread ticket = 3
Thread:Thread-1--Thread ticket = 2
Thread:Thread-1--Thread ticket = 1
Thread:Thread-1--Thread ticket = 0
```



### join()

join()是Thread的一个方法，等待该线程结束。

也就是说，t.join()方法阻塞调用此方法的线程(calling thread)，直到线程t完成，此线程再继续；通常用于在main()主线程内，等待其它线程完成再结束main()主线程。我们来看看下面的例子。

如果不使用join()：

```java
public static void main(String[] args){
    System.out.println("MainThread run start.");

    //启动一个子线程
    Thread threadA = new Thread(new Runnable() {
        @Override
        public void run() {
            System.out.println("threadA run start.");
            try {
                Thread.sleep(1000);
            } catch (Exception e) {
                e.printStackTrace();
            }
            System.out.println("threadA run finished.");
        }
    });
    threadA.start();

    System.out.println("MainThread join before");
    System.out.println("MainThread run finished.");
}

MainThread run start.
threadA run start.
MainThread join before
MainThread run finished.
threadA run finished.
// 所以主线程没有等子线程结束，自己就已经结束了
    
    
public static void main(String[] args){
    System.out.println("MainThread run start.");

    //启动一个子线程
    Thread threadA = new Thread(new Runnable() {
        @Override
        public void run() {
            System.out.println("threadA run start.");
            try {
                Thread.sleep(1000);
            } catch (Exception e) {
                e.printStackTrace();
            }
            System.out.println("threadA run finished.");
        }
    });
    threadA.start();

    System.out.println("MainThread join before");
    try {
        threadA.join();    //调用join()
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
    System.out.println("MainThread run finished.");
}

MainThread run start.
threadA run start.
MainThread join before
threadA run finished.
MainThread run finished.
// 对子线程threadA使用了join()方法之后，主线程会等待子线程执行完成之后才往后执行
```

[【Java】Thread类中的join()方法原理_Mlib-CSDN博客](https://blog.csdn.net/u010983881/article/details/80257703)

首先join() 是一个synchronized方法， 里面调用了wait()，这个过程的目的是让持有这个同步锁的线程进入等待，那么谁持有了这个同步锁呢？答案是主线程，因为主线程调用了threadA.join()方法，相当于在threadA.join()代码这块写了一个同步代码块，谁去执行了这段代码呢，是主线程，所以主线程被wait()了。然后在子线程threadA执行完毕之后，JVM会调用lock.notify_all(thread);唤醒持有threadA这个对象锁的线程，也就是主线程，会继续执行。



### daemon thread

在Java中，有两种线程，用户线程User Thread和守护线程Daemon Thread。

守护线程是指在程序运行的时候在后台提供一种通用服务的线程，比如GC线程，并且这种线程并不属于程序中不可或缺的部分，因此，当所有的用户线程都已结束，程序也就终止了，同时会杀死进程中的所有守护线程。所以其实是，只要有用户线程还在运行，程序就不会终止。

> 在Java中，Java程序都运行在JVM中，每一个Java命令启动一个Java应用程序，就会启动一个JVM进程，在同一个JVM进程中，有且只有一个进程，就是JVM自己。
>
> 在JVM进程中，所有的程序代码的运行都是以线程的方式来运行的。
>
> JVM找到程序的入口main()然后开始运行main()，这就产生了一个线程，这个就是主线程。main方法运行结束之后，主线程运行完成，JVM进程退出。
>
> 所以对应关系是，一个应用程序对应一个进程，但是可以包含多个线程。

用户线程和守护线程没有任何区别，唯一的区别在于JVM离开的时候，如果用户线程已经全部退出运行了，只剩下守护线程存在了，虚拟机也就退出了。 因为没有了被守护者，守护线程也就没有工作可做了，也就没有继续运行程序的必要了。

将线程转换为守护线程可以通过**调用Thread对象的setDaemon(true)方法来实现**。

```java
Thread t = new MyThread();
t.setDaemon(true);
t.start();
```

在使用守护线程时需要注意一下几点：

(1) thread.setDaemon(true)必须在thread.start()之前设置，否则会跑出一个IllegalThreadStateException异常。你不能把正在运行的常规线程设置为守护线程。 

(2) 在Daemon线程中产生的新线程也是Daemon的。

(3) 守护线程应该永远不去访问固有资源，如文件、数据库，因为它会在任何时候甚至在一个操作的中间发生中断。



  ### synchronized

当多个程序开始运行的时候，线程的调度由操作系统决定，程序本身无法决定。

因此就必须考虑在多个线程对一个共享变量同时读写的时候，锁的问题。

假设一个共享变量的数值是n，如果两个线程同时执行n+1，得到的结果很有可能不是n+2而是n+1，因为

```ascii
┌───────┐    ┌───────┐
│Thread1│    │Thread2│
└───┬───┘    └───┬───┘
    │            │
    │ILOAD (100) │
    │            │ILOAD (100)
    │            │IADD
    │            │ISTORE (101)
    │IADD        │
    │ISTORE (101)│
    ▼            ▼
```

如果线程1在执行ILOAD后被操作系统中断，此刻如果线程2被调度执行，它执行ILOAD后获取的值仍然是100，最终结果被两个线程的ISTORE写入后变成了101，而不是期待的102。

```java
import javax.xml.ws.soap.Addressing;
import java.util.concurrent.CountDownLatch;

/**
 * @ClassName: MySynchronized
 * @Description: my synchronized
 * @author: Gatsby
 * @date: 2021/9/1 13:18
 */

class Count {
    public static final Object lock = new Object();
    public static int cnt = 0;
}

class AddOne extends Thread {
    public void run() {
        for(int i=0; i<1000000; ++i) {
                ++Count.cnt;
        }
    }
}

class SubtractOne extends Thread {
    public void run() {
        for(int i=0; i<1000000; ++i) {
                --Count.cnt;
        }
    }
}

class Add extends Thread {
    public void run() {
        for(int i=0; i<1000000; ++i) {
            synchronized (Count.lock) {
                ++Count.cnt;
            }
        }
    }
}

class Subtract extends Thread {
    public void run() {
        for(int i=0; i<1000000; ++i) {
            synchronized (Count.lock) {
                --Count.cnt;
            }
        }
    }
}

public class MySynchronized {
    public static void main(String[] args) throws Exception{
        long starTime=System.currentTimeMillis();
        AddOne add_one = new AddOne();
        SubtractOne sub_one = new SubtractOne();
        add_one.start();
        sub_one.start();
        add_one.join(); // 必须要添加throws exception
        sub_one.join();
        System.out.println("count="+Count.cnt);
        long endTime=System.currentTimeMillis();
        long Time=endTime-starTime;
        endTime=System.currentTimeMillis();
        Time=endTime-starTime;
        System.out.println("costed " +Time+"ms");

        Count.cnt = 0;
        starTime=System.currentTimeMillis();
        Add add = new Add();
        Subtract sub = new Subtract();
        add.start();
        sub.start();
        add.join();
        sub.join();
        System.out.println("count="+Count.cnt);
        endTime=System.currentTimeMillis();
        Time=endTime-starTime;
        System.out.println("costed " +Time+"ms");
    }

}

count=108044
costed 11ms
count=0
costed 104ms

```

不需要synchronized的操作：

JVM定义的规范中的原子操作：基本类型（long和double除外）和引用类型。

```java
public class Counter {
    private int count = 0;

    public void add(int n) {
        synchronized(this) {
            count += n;
        }
    }

    public void dec(int n) {
        synchronized(this) {
            count -= n;
        }
    }

    public int get() {
        return count;
    }
}
```

这样一来，线程调用add和dec方法的时候，不必关心逻辑，因为Synchronized代码块在add和dec方法内部。并且，synchronized锁住的是this对象，所以当创建多个counter的时候，不会互相影响，可以并发执行。

**如果一个类被设置成允许多线程访问，那么就说这个类是线程安全的，还有一些的类例如，String、Integer、LocalDate它们的所有成员变量都是final，多线程同时访问只能读不能写。**因此这些常量也是线程安全的。

最后，类似Math这些只提供静态方法，没有成员变量的类，也是线程安全的。

除了上述几种少数情况，大部分类，例如ArrayList，都是非线程安全的类，我们不能在多线程中修改它们。但是，如果所有线程都只读取，不写入，那么ArrayList是可以安全地在线程间共享的。

> 没有特殊说明的情况下，一个类默认是线程不安全的。

```java
public void add(int n) {
    synchronized(this) { // 锁住this
        count += n;
    } // 解锁
}
// 和
public synchronized void add(int n) {
    count += n;	
}
// 是等价的
```

特别的，如果对于一个静态方法来说，添加了同步方法，那么由于静态方法是针对类而不是实例，所以实际上锁住this锁的并不是实例，而是Class的实例。



### synchronized method

Java程序依靠的是Synchronized对线程进行同步，使用Synchronized是哪个对象非常重要，让线程自动选择锁的对象往往会使得代码显得逻辑混乱，也不利于封装，所以最好的办法是将逻辑封装起来。

```java
package org.example;

import java.util.Random;

/**
 * @ClassName: MySynchronizedMethod
 * @Description: Encapsulating 封装同步方法
 * @author: Gatsby
 * @date: 2021/10/8 13:58
 */

class Counter {
    public int count = 10;

    public int add(int n) {
        synchronized (this) {
            count += n;
        }
        return count;
    }

    public int dec(int n) {
        synchronized (this) {
            count -= n;
        }
        return count;
    }

    public int getCount() {
        synchronized (this) {
            return count;
        }
    }

    public void setCount(int n) {
        synchronized (this) {
            count = n;
        }
    }
}

class SynchronizedThread extends Thread {
    public void run(Counter cnter, int n, int flag) {
        boolean flagBoolean = flag % 2 == 0;
        if (flagBoolean) {
            cnter.add(n);
        } else {
            cnter.dec(n);
        }
    }
}

public class MySynchronizedMethod {
    public static void main(String[] args) {
        Counter cnter = new Counter();
        Random r = new Random();
        for (int i = 0; i < 10; ++i) {
            SynchronizedThread t = new SynchronizedThread();
            t.run(cnter, i, r.nextInt());
            System.out.println(cnter.getCount());
        }
    }
}
```

这样依赖调用add和dec的时候就不需要再考虑同步的问题，因为在类的内部我们已经做了同步的封装，并且我们锁住的是this，所以创建多个Counter实例的时候，他们之间户不影响，可以并发执行。



如果一个类被设计为允许多线程正确访问，我们就指这个类是线程安全的。

>java.lang.StringBuffer是线程安全的。
>
>还有一些不变类，例如String、Integer、LocalDate，所有的成员变量都是final的，多线程访问只能读不能写，所以也是线程安全的。
>
>Math之类的只提供静态方法，没有成员函数的类也是线程安全的。
>
>**绝大多数类都不是线程安全的。**



当类的方法像add()和dec()一样会同步锁，等价于public synchronized void add(int n)，表示整个方法都要对实例加上锁，即同步方法，synchronized method。

对于静态方法，public synchronized static void add(int n)，对于static方法，是没有this实例的，因为static方法是针对类而不是实例，但是由于每一个类都有一个JVM创建的Class实例，所以锁住的是这个Class实例，相当于

```java
public class Counter {
    public static void test(int n) {
        synchronized(Counter.class) {
            ...
        }
    }
}
```



### wait() & notify()

synchronized解决了多线程中的资源争用的问题，但是没有解决多线程中的线程协同的问题。

比如说最经典的生产者-消费者模型：当队列满时，生产者需要等待队列有空间才能继续往里面放入商品，而在等待的期间内，生产者必须释放对临界资源（即队列）的占用权。因为生产者如果不释放对临界资源的占用权，那么消费者就无法消费队列中的商品，就不会让队列有空间，那么生产者就会一直无限等待下去。因此，一般情况下，当队列满时，会让生产者交出对临界资源的占用权，并进入挂起状态。然后等待消费者消费了商品，然后消费者通知生产者队列有空间了。同样地，当队列空时，消费者也必须等待，等待生产者通知它队列中有商品了。这种互相通信的过程就是线程间的协作。

> 1. wait()、notify()和notifyAll()方法是本地方法，并且为final方法，无法被重写。
> 2. 调用某个对象的wait()方法能让当前线程阻塞，并且当前线程必须拥有此对象的monitor（即锁）
> 3. 调用某个对象的notify()方法能够唤醒一个正在等待这个对象的monitor的线程，如果有多个线程都在等待这个对象的monitor，则只能唤醒其中一个线程
> 4. 调用notifyAll()方法能够唤醒所有正在等待这个对象的monitor的线程

**由于每个对象都拥有monitor（即锁），所以让当前线程等待某个对象的锁，当然应该通过这个对象来操作了。而不是用当前线程来操作，因为当前线程可能会等待多个线程的锁，如果通过线程来操作，就非常复杂了。**

上面已经提到，如果调用某个对象的wait()方法，当前线程必须拥有这个对象的monitor（即锁），**因此调用wait()方法必须在同步块或者同步方法中进行（synchronized块或者synchronized方法）。**

notify()方法能够唤醒一个正在等待该对象的monitor的线程，当有多个线程都在等待该对象的monitor的话，则只能唤醒其中一个线程，具体唤醒哪个线程则不得而知。

同样地，调用某个对象的notify()方法，当前线程也必须拥有这个对象的monitor，**因此调用notify()方法必须在同步块或者同步方法中进行（synchronized块或者synchronized方法）。**



### Callable、Runnable、Future

1. Runnable作为我们多线程开发中经常使用到的接口，它定义run方法，只要对象实现这个方法，将对象作为参数输入到new Thread(Runnable A)，线程一旦start()，那么就 自动执行了，没有任何的返回结果，无法知道什么时候结束，**适用于完全异步的任务，不用关心结果**。

2. Callable定义的接口call(),它能够抛出异常，并且能够有一个返回结果。实现了Callable要想提交到线程池中， 直接通过executorService.submit(new CallAbleTask(i))，但是返回的结果是Future，结果信息从Future里面取出，具体的业务逻辑在call中执行。

3. Future提供了五个接口：

   boolean cancel(boolean mayInterruptIfRunning)：用来取消任务，成功返回true，失败则返回false

   boolean isCancelled()：表示任务是否被取消成功，如果在任务正常完成前被取消成功，则返回 true

   boolean isDone()：表示任务是否已经完成，若任务完成，则返回true

   V get()：用来获取执行结果，这个方法会产生阻塞会一直等到任务执行完毕才返回

   V get(long timeout, TimeUnit unit) 用来获取执行结果，如果在指定时间内，还没获取到结果，直接返回null。



## Thread pool

构建一个线程是有一定代价的，因为需要等待JVM和OS的交互。

在创建一个线程的过程中，必须要为线程分配堆栈资源，其中包括至少1MB的内存资源。

需要进行系统调用，以便在OS中创建和注册本地线程。

**如果程序中创建了大量的生命周期很短的线程，那么就应该使用线程池**，一个线程池中包含了许多准备运行的线程。将Runnable对象交给线程池，就会有一个线程调用run方法，当run方法退出的时候，线程没有死亡，而是在线程池中准备为下一个请求提供服务。

同时，使用线程池是用来减少并发的数量，创建大量的线程会导致性能下降甚至导致虚拟机崩溃，如果有一个会创建许多线程的算法，应该使用一个const的线程数参数以限制并发线程的总数。



### executor

Executor提供了execute()接口来执行已提交的Runnable执行目标实例。Executor作为执行者的角色，其目的是提供一种将“任务提交者”与“任务执行者”分离开来的机制。它只包含一个函数式方法：

```java
void execute(Runnable command)
```



ExecutorService继承于Executor，对外提供异步任务的接受服务，如submit、invoke等



AbstractExecutorService是一个抽象类，它实现了ExecutorService接口，这个抽象类的存在的意义在于为ExecutorService 的接口提供默认实现。



### ThreadPoolExecutor

这就是线程池的实现类，继承于AbstractExecutorService的抽象类，ThreadPoolExecutor是JUC的核心实现类，线程的创建和销毁由于需要很大的开销，所以在线程池中，预先提供了一定数量的线程，并且每个线程池都维护了一些基础的数据统计，方便线程的监控与管理。

Java通过Executors工厂类提供了4种快捷创建线程池的方法：



| Method                  |                                                              |
| ----------------------- | ------------------------------------------------------------ |
| newSingleThreadPool()   | 创建一个只有一个线程的单线程线程池                           |
| newFixedThreadPool()    | 创建固定大小的线程池                                         |
| newCachedThreadPool()   | 创建一个不限制线程数量的线程池，任何提交的任务都会执行，但是空闲的线程会被回收 |
| newScheduleThreadPool() | 创建于一个可定期或者延期执行任务的线程池                     |

```java
package org.example;

import org.junit.jupiter.api.DynamicTest;

import java.util.concurrent.Executor;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.atomic.AtomicInteger;

/**
 * @ClassName: MyThreadPool
 * @Description: thread pool test
 * @author: Gatsby
 * @date: 2021/10/11 16:31
 */

public class MyThreadPool {
    public static void main(String[] args) {
        MyThreadPool mtp = new MyThreadPool();
        mtp.testThreadPool();
    }

    static class Task implements Runnable {
        static AtomicInteger taskNo = new AtomicInteger(1);
        private String taskName;

        public Task() {
            taskName = "task " + taskNo.get();
            taskNo.incrementAndGet();
        }

        @Override
        public void run() {
            System.out.println(taskName);
            try {
                System.out.println(taskName + " started.");
                Thread.sleep(1000);
                System.out.println(taskName + " ended.");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }


    public void testThreadPool() {
        
        ExecutorService singlePool = Executors.newSingleThreadExecutor();

        for (int i = 0; i < 10; ++i) {
            singlePool.execute(new Task());
            singlePool.submit(new Task());
        }

        try {
            Thread.sleep(1000);
            singlePool.shutdown();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        
        ExecutorService fixedPool = Executors.newFixedThreadPool(10);

        for (int i = 0; i<20; ++i) {
            fixedPool.execute(new Task());
            fixedPool.submit(new Task());
        }

        try {
            Thread.sleep(1000);
            fixedPool.shutdown();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

}
```

大部分的开发规范都会要求禁止使用快捷线程池，要求通过标准构造器ThreadPoolExecutor去构造线程池，Executors工厂类创建线程池的快捷工厂方法。

```java
// 使用标准构造器构造一个普通线程池
public ThreadPoolExecutor(int corePoolSize,                   	// 核心线程数，即使线程空闲也不会回收
                          int maximumPoolSize,					// 线程数的上限
                          long keepAliveTime,					// 线程的最大空闲时长，
                          TimeUnit unit,						// 
                          BlockingQueue<Runnable> workQueue,	// 任务的排队队列
                          ThreadFactory threadFactory,			// 新线程的产生方式
                          RejectedExecutionHandler handler) 	// 拒绝策略
```











## Collection

在Java中，如果一个Java对象可以在内部持有若干其他Java对象，并对外提供访问接口，我们把这种Java对象称为集合。

数组就是最常见的一个集合，但是除了Java默认的数组之外，还需要其他的集合，因为数组存在很多的限制条件：

1. 数组一旦初始化之后，大小就不再可变
2. 数组只能按照索引顺序存储

Java自带了java.util提供集合类Collection，这是除了Map类之外所有的集合的根接口。Java的util主要提供了三种类型的集合：

- List
- Set
- Map

Java集合的设计有几个特点：一个是实现了接口与类的分离，例如，有序表的接口是List，具体的实现的类有ArrayList、LinkedList，二是支持泛型，限制在一个集合中只能放入同一种数据类型的元素。



### Iterator

iterator是Java迭代器最简单的实现，iterator的基本操作是next()，hasNext()和remove()

```java
Iterator<String> it = lsa.iterator();
System.out.println(it);
while(it.hasNext()) {
    System.out.println(it.next());
}
```

通过反复调用next方法，可以逐个访问集合中的每个元素，但是如果到了集合的尾部，next方法抛出NoSuchElementException的异常。因此，在调用next方法之前，需要先使用hasNext方法，如果迭代器对象还有多个供访问的元素，这个方法就返回true。如果想要查看集合中的所有元素，就请求一个迭代器，并在hasNext返回true时反复地调用next方法。

同时，在Java SE 8的版本中，可以直接调用iterator.forEachRemaining方法并且提供一个lambda表达式，处理每一个元素，直到没有元素位置。

```java
it = lsa.iterator();
it.forEachRemaining(ele-> System.out.println(ele));
```

和C++的iterator相比，Java的iterator只有next方法可以调用，而不是像C++那样支持+1-1的操作，并以此为控制让iterator向前或者向后。Java的next方法在执行查找操作的同时，iterator的位置同时向前。因此，应该将Java迭代器认为是位于两个元素之间。当调用next时，迭代器就越过下一个元素，并返回刚刚越过的那个元素的引用。

Iterator的remove方法会删除上次调用的next方法时返回的元素。在大多数情况下，删除这个元素之前应该先看一下这个元素是否有意义。然而想要删除这个元素的话，仍然需要越过这个元素。因此，删除第一个元素仍然需要调用next方法。

```java
it = lsa.iterator();
it.next();
it.remove();
System.out.println(it.next());
```

如果需要连续删除两个元素，需要remove之后再next之后再remove。

```java
public class MyList {
    public static void main(String[] args) {
        Collection<String> cs = new ArrayList<>();
        System.out.println(cs.size());
        System.out.println(cs.getClass()); // class java.util.ArrayList

        List<String> lsa = new ArrayList<>();
        lsa.add("A");
        lsa.add("B");
        System.out.println(lsa.add("C")); // 如果add成功就返回true
        System.out.println(lsa.size());

        Iterator<String> it = lsa.iterator();
        System.out.println(it);
        // recursion
        while(it.hasNext()) {
            System.out.println(it.next());
        }

        // recursion
        for(String letter: lsa) {
            System.out.println(letter);
        }


        // Java SE 8 lambda recursion
        it = lsa.iterator();
        it.forEachRemaining(ele-> System.out.println(ele));

        // remove the 1st element
        it = lsa.iterator();
        it.next();
        it.remove();
        System.out.println(it.next());

        // remove the adjacent elements
        it.remove();
        it.next();
        it.remove();
        System.out.println(lsa.size());
    }
}
```



<img src="C:\Users\Caijinyang\AppData\Roaming\Typora\typora-user-images\image-20210903103018556.png" alt="image-20210903103018556" style="zoom: 67%;" />



Java库中的具体集合

| 集合            | 描述                                             |
| --------------- | ------------------------------------------------ |
| ArrayList       | 一种可以动态增长和缩减的索引序列                 |
| LinkedList      | 一种可以在任意进行高效地插入和删除操作的有序序列 |
| ArrayDeque      | 一种用循环数组实现的双端队列                     |
| HashSet         | 一种没有重复元素的无序集合                       |
| TreeSet         | 一种有序集合                                     |
| EnumSet         | 一种包含枚举类型值的集                           |
| LinkedHashSet   | 一种可以记住元素插入次序的集                     |
| PriorityQueue   | 一种允许高效删除最小元素的集合                   |
| HashMap         | 一种键值无序排列的映射表                         |
| TreeMap         | 一种键值有序排列的映射表                         |
| EnumMap         | 一种键值属于枚举类型的映射表                     |
| LinkedHashMap   | 一种可以记录键值项添加次序的映射表               |
| WeakHashMap     | 一种其值没用之后会被GC回收的映射表               |
| IdentifyHashMap | 一种使用==而不是equals比较键值的映射表           |



### List

List的行为和数组几乎完全相同，List内部按照元素的放入的先后顺序存放，每个元素都可以通过索引确定自己的位置。

但是Array和ArrayList相比，Array删除一个元素需要付出很大的代价。其原因是数组中处于被删除元素之后的所有元素都要向数组的前端移动，在数组中插入一个元素也是如此。

在Java程序设计语言中，所有链表实际上都是双向链接的（doubly linked）——即每个结点还存放着指向前驱结点的引用。

List中主要分为ArrayList和LinkedList两个实现类，

```java
// ---------ArrayList----------
lsa = new ArrayList<>();
lsa.add("Hello ");
lsa.add("World");
lsa.add("!");
// lsa.size()
System.out.println("lsa.size(): "+lsa.size());
System.out.println("lsa.toString(): "+lsa.toString());
// lsa.set()
lsa.set(0, "Goodbye");
lsa.set(1, "Friends");
System.out.println("lsa toString(): "+lsa.toString());

// lsa recursion with index
for(int i=0; i<lsa.size(); ++i) {
    System.out.println(lsa.get(i));
}

// lsa.contains
if(lsa.contains("Goodbye")) {
    System.out.println("lsa contains \"Goodbye\"");
}

// lsa.remove
lsa.remove(0);
lsa.remove("Goodbye");
if(!lsa.isEmpty()) {
    // lsa.clear
    lsa.clear();
    System.out.println("lsa is not empty but now is cleared");
}


// ------------LinkedList------------
List lsl = new LinkedList();
lsl.add("Hello ");
lsl.add("World");
lsl.add("!");
System.out.println("lsl.size(): "+lsl.size());
System.out.println("lsl.toString(): "+lsl.toString());

// lsl recursion with index
for(int i=0; i<lsl.size(); ++i) {
    System.out.println(lsl.get(i));
}

// lsl.set
lsl.set(0, "Goodbye");
lsl.set(1, "Friends");
System.out.println(lsl.toString());

// lsl.remove
if(lsl.contains("Friends")) {
    lsl.remove("Friends");
    System.out.println(lsl.get(1));
}
```



LinkedList和ArrayList的性能区别：

[Java集合--List - 简书 (jianshu.com)](https://www.jianshu.com/p/25aa92f8d681)

```java
class ListCompare {
    // iteration time
    public static final int ITERATION_TIME = 100000;

    public static void main(String[] args) {
        // 一般来说都是ArrayList只需要大概LinkedList一半左右的时间
        insertPerformanceCompare();

        // 一般来说都是LinkedList需要大概ArrayList 500倍左右的时间
        getPerformanceCompare();
    }

    public static void insertPerformanceCompare() {
        try {
            Thread.sleep(1000);
            System.out.println("LinkedList新增测试开始");
            long start = System.nanoTime();
            List<Integer> linkedList = new LinkedList<Integer>();
            for (int x = 0; x < ITERATION_TIME; x++) {
                linkedList.add(x);
            }
            long end = System.nanoTime();
            System.out.println(end - start);

            System.out.println("ArrayList新增测试开始");
            start = System.nanoTime();
            List<Integer> arrayList = new ArrayList<Integer>();
            for (int x = 0; x < ITERATION_TIME; x++) {
                arrayList.add(x);
            }
            end = System.nanoTime();
            System.out.println(end - start);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

    }

    public static void getPerformanceCompare() {
        try {
            Thread.sleep(5000);

            //填充ArrayList集合：
            List<Integer> arrayList = new ArrayList<Integer>();
            for (int x = 0; x < ITERATION_TIME; x++) {
                arrayList.add(x);
            }

            //填充LinkedList集合：
            List<Integer> linkedList = new LinkedList<Integer>();
            for (int x = 0; x < ITERATION_TIME; x++) {
                linkedList.add(x);
            }

            //创建随机数对象：
            Random random = new Random();

            System.out.println("LinkedList获取测试开始");
            long start = System.nanoTime();
            for (int x = 0; x < ITERATION_TIME; x++) {
                int j = random.nextInt(x + 1);
                int k = linkedList.get(j);
            }
            long end = System.nanoTime();
            System.out.println(end - start);

            System.out.println("ArrayList获取测试开始");
            start = System.nanoTime();
            for (int x = 0; x < ITERATION_TIME; x++) {
                int j = random.nextInt(x + 1);
                int k = arrayList.get(j);
            }
            end = System.nanoTime();
            System.out.println(end - start);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

}
```

> 测试性能的时候经常会用到时间的测算，一种是使用System.nanoTime()，一种是使用System.currentTimeMills()。
>
> System.currentTimeMillis返回的是从1970.1.1 UTC 零点开始到现在的时间，精确到毫秒。currentTimeMills返回以毫秒为单位的当前时间。
>
> nanoTime使用最精确的可用系统计时器的当前值，以毫微秒为单位。此方法只能用于测量已过的时间，与系统或者钟表时间系统无关。用法是
>
> ```java
> long startTime = System.nanoTime();
> // ... the code being measured ...
> long estimatedTime = System.nanoTime() - startTime;
> ```









## String

### assign

String的赋值有两种方式：直接赋值和构造赋值。

直接赋值：只开辟一块堆内存空间，字符串的内容可以自动入池，以供下次使用；
构造方法：开辟两块堆内存空间，有一块将成为垃圾，并且不能自动入池，需要使用intern()手工入池。



直接赋值的时候都是指向的同一个堆地址，如果字符串的内容都是相同的，对象池中有了相同的字符串声明，那么就不会重新开辟新的空间。

**为什么在以下的s3和s4已经开辟了新的空间之后，再对s5直接赋值，最终显示s5和s3或者s4仍然不是同一个对象呢？**

**因为每一个对象都是String类的匿名对象，所以首先会在堆内存中开辟一块空间保存字符串“Hello”，然后使用关键字new开辟的内存，而之前定义的字符串常量的堆内存空间将不会有任何的栈内存指向空间，就成为垃圾，等待被GC回收。所以，使用构造方法开辟的字符串对象实际上会开辟两块空间，其中有一块空间将成为垃圾。另外，使用构造方法实例化的String 类对象内容不会保存在字符串对象池中，既不能够进行共享数据操作。**



![img](https://images2015.cnblogs.com/blog/709529/201511/709529-20151101160757435-489881942.png)

![img](https://images2015.cnblogs.com/blog/709529/201511/709529-20151101160821794-1394784740.png)




```java
public class MyString {
    public static void main(String[] args) {
        String s1 = "Hello"; // 直接赋值
        String s2 = "Hello"; // 直接赋值
        String s3 = new String("Hello"); // 构造赋值
        String s4 = new String("Hello"); // 构造赋值
        String s5 = "Hello"; // 是否会使用s3或者s4

        System.out.println(s1 == s2); // true
        System.out.println(s3 == s4); // false
        System.out.println(s1 == s3); // always false
        System.out.println(s3 == s5 || s4 == s5); // always false
    }

}
```

因为new关键字创建的对象不会入池，所以s5并不会指向前面两个创建的对象，但是可以手动入池intern()方法；

```java
public class MyString {
    public static void main(String[] args) {
        String s1 = "Hello";
        String s2 = "Hello";
        String s3 = new String("Hello");
        String s4 = new String("Hello").intern(); // 手动入池，后面的s5就会指向这个
        String s5 = "Hello";

        System.out.println(s1 == s2); // true
        System.out.println(s3 == s4); // false
        System.out.println(s1 == s3); // always false
        System.out.println(s3 == s5 || s4 == s5); // true
    }
}
```



### equals

String 类中重写了 equals() 方法用于比较两个字符串的内容是否相等。



### String & Char

在jdk1.9之前的Java版本都是采用了char[]的方式，

| method                                             | type      |                                      |
| -------------------------------------------------- | --------- | ------------------------------------ |
| public String(char[] value)                        | construct | 传入的全部数组变成字符串             |
| public String(char[] value, int offset, int count) | construct | 将部分字符串数组变成字符串           |
| public char charAt(int index)                      | common    | 获取普通指定索引位置的字符           |
| public char[] toCharArray()                        | common    | 将字符串中的数据以字符数组的形式返回 |



### String & Byte

| method                                                       | type      |                              |
| ------------------------------------------------------------ | --------- | ---------------------------- |
| public String(byte[] bytes)                                  | construct | 传入的全部字节数组变成字符串 |
| public String(byte[] bytes, int offset, int count)           | construct | 将部分字节数组变成字符串     |
| public byte[] getBytes()                                     | common    | 将字符串转换为字节数组       |
| public byte[] getBytes(String charsetName) throws UnsupportedEncodingException | common    | 编码转换                     |



### String compare

| method                                                | type   |                                 |
| ----------------------------------------------------- | ------ | ------------------------------- |
| public boolean equals(Object anObject)                | common | 区分大小写的普通相等判断        |
| public boolean contentEquals(StringBuffer sb)         | common |                                 |
| public boolean equalsIgnoreCase(String anotherString) | common | 不区分带小写的普通相等判断      |
| public int compareTo(String anotherString)            | common | 进行字符串大小比较，返回一个int |
| public int compareToIgnoreCase(String str)            | common |                                 |



```java
public int compareTo(String anotherString) {
    int len1 = value.length;
    int len2 = anotherString.value.length;
    int lim = Math.min(len1, len2);
    char v1[] = value;
    char v2[] = anotherString.value;

    int k = 0;
    while (k < lim) {
        char c1 = v1[k];
        char c2 = v2[k];
        if (c1 != c2) {
            return c1 - c2;
        }
        k++;
    }
    return len1 - len2;
}
```



```java
String s9 = "abcd";
String s10 = "abcde";
String s11 = "ABCD";
System.out.println(s9.compareTo(s10)); // -1
System.out.println(s10.compareTo(s9)); // 1 
System.out.println(s9.compareTo(s11)); // 32
System.out.println(s9.compareToIgnoreCase(s11)); // 0
```



### String search

| method                                               | type   |                                                              |
| :--------------------------------------------------- | :----- | :----------------------------------------------------------- |
| public boolean contains(CharSequence s)              | common | 判断指定的内容是否存在                                       |
| public int indexOf(int ch)                           | common | 由前向后查找指定字符串的位置，如果找到了则返回(第一个字母)位置索引，找不到则返回-1 |
| public int indexOf(int ch, int fromIndex)            | common | 由指定的位置从前向后查找指定字符串位置，找不到返回-1         |
| public int lastIndexOf(int ch)                       | common | 从后向前查找指定字符串的位置，找不到返回-1                   |
| public int lastIndexOf(int ch,int fromIndex)         | common | 从指定位置由后向前查找字符串的位置，找不到返回-1             |
| public boolean starsWith(String suffix)              | common | 判断是否以指定的字符串开头                                   |
| public boolean startsWith(String prefix,int toffset) | common | 从指定位置开始判断是否事以指定的字符串开头                   |
| public boolean endsWith(String suffix)               | common | 判断是否以指定的字符串结尾                                   |



### String replace

指使用一个新的字符串替换旧的字符串，支持的方法如下：

| method                                                      | type   |                            |
| :---------------------------------------------------------- | :----- | :------------------------- |
| public String replace(char oldChar,char newChar)            | common |                            |
| public String replaceAll(String regex,String replacement)   | common | 用新的内容替换全部旧的内容 |
| public String replaceFirst(String regex,String replacement) | common | 替换首个满足条件的内容     |



### String split

| method                                         | type   |      |
| ---------------------------------------------- | ------ | ---- |
| public String[] split(String regex)            | common |      |
| public String[] split(String regex, int limit) | common |      |

```java
String s12 = "hello world iphone Mac";
String[] result = s12.split(" ");
String s13 = "192.168.0.1";
String[] address = s13.split("\\."); // 转义
for (String s: address) {
    System.out.println(s);
}
```



### String Substring

| method                                                | type   |      |
| ----------------------------------------------------- | ------ | ---- |
| public String substring(int beginIndex)               | common |      |
| public String substring(int beginIndex, int endIndex) | common |      |



[String (Java SE 9 & JDK 9 ) (oracle.com)](https://docs.oracle.com/javase/9/docs/api/java/lang/String.html#contains-java.lang.CharSequence-)



### String常量池

静态常量池：指的是程序(*.class)在会自动将此程序之中保存的字符串、普通常量、类和方法的信息全部进行分配；

运行时常量池：当一个程序(*.class)加载之后，里面可能存在一些变量

```java
String s6 = "www.alibaba.com";
String s7 = "www" + ".alibaba" + ".com";

System.out.println(s6 == s7); // true. 全是静态变量所以会自动先连接起来
```

```java 
String company = "alibaba";
String s8 = "www." + company + ".com";
System.out.println(s8 == s6); // false, 程序在加载的时候不能确定company的值，company是一个变量，S8认为这是不确定的
```





### String不可修改

String在Java中是一个不可修改的类，也就是说，这个类的实例都是不可修改的，所有的实例信息在创建的时候被初始化而且信息是不可修改的。

当一个[Java String对象的问题 String s="a"+"b"+"c"+"d" - zonyZhang - 博客园 (cnblogs.com)](https://www.cnblogs.com/zgz345/p/6640826.html)创建了多少个对象？

因此如果出现循环中有String += String的写法，将会创造大量的垃圾。

[字符串修改分析 - Java面向对象编程 - 阿里云全球培训中心 - 官方网站，云生态下的创新人才工场 (aliyun.com)](https://edu.aliyun.com/lesson_1011_8840#_8840)























## IoC

使用spring框架的时候，遇到的第一个也是最核心的概念就是容器。

容器的概念：

容器可以管理对象的生命周期、对象与对象之间的依赖关系，您可以使用一个配置文件（通常是XML），在上面定义好对象的名称、如何产生（Prototype 方式或Singleton 方式）、哪个对象产生之后必须设定成为某个对象的属性等，在启动容器之后，所有的对象都可以直接取用，不用编写任何一行程序代码来产生对象，或是建立对象与对象之间的依赖关系。

容器是一种为某种特定组件的运行提供必要支持的一个软件环境。**例如，Tomcat就是一个Servlet容器，它可以为Servlet的运行提供运行环境。类似Docker这样的软件也是一个容器，它提供了必要的Linux环境以便运行一个特定的Linux进程。**

通常来说，使用容器运行组件，除了提供一个组件运行环境之外，容器还提供了许多底层服务。例如，Servlet容器底层实现了TCP连接，解析HTTP协议等非常复杂的服务，如果没有容器来提供这些服务，我们就无法编写像Servlet这样代码简单，功能强大的组件。

Spring的核心就是提供了一个IoC容器，它可以管理所有轻量级的JavaBean组件，提供的底层服务包括组件的生命周期管理、配置和组装服务、AOP支持，以及建立在AOP基础上的声明式事务服务等。



如果一个系统有大量的组件，**其生命周期和相互之间的依赖关系如果由组件自身来维护，不但大大增加了系统的复杂度，而且会导致组件之间极为紧密的耦合，继而给测试和维护带来了极大的困难。**

因此核心的问题其实是要管理：

1. 谁负责创建组件？
2. 谁负责根据依赖关系组装组件？
3. 谁负责根据依赖关系销毁组件？

传统的应用中，所有的控制都需要由组件的作者控制，一旦业务逻辑开始变得复杂起来，想要依靠一个人把所有的控制逻辑想明白，这几乎是不可能的，更何况大型的项目都是由团队协作开发。

IoC的作用就是将控制权反转，由程序员控制，变成由IoC容器控制。这样，应用程序只需要直接使用已经创建好的组件。

为了能让组件在IoC中被装配出来，需要“注入”。

Spring通过DI(依赖注入)实现IOC(控制反转)，常用的注入方式有三种：构造方法注入、setter注入、基于注解的注入。

构造器注入保证一些必要的属性在Bean实例化的时候就得到设置，并且确保Bean实例化之后就可以使用。实际上，绝大多数都在使用依赖注入，因为耦合性更低，而且容器不会用到某个容器的特定API，因此可以脱离容器使用。

因为由IoC容器负责创建所有的组件，因此必须告诉容器如何创建组件，以及各个组件之间的依赖关系。

### xml配置bean

传统的方法是通过简单的xml来配置文件，

1. 使用setter注入

   ```java
   public class BookService {
       private DataSource dataSource;
   
       public setDataSource(DataSource dataSource) {
           this.dataSource = dataSource;
       }
   }
   
   ```

   ```xml
   <beans>
       <bean id="dataSource" class="HikariDataSource" />
       <bean id="bookService" class="BookService">
           <property name="dataSource" ref="dataSource" />
       </bean>
       <bean id="userService" class="UserService">
           <property name="dataSource" ref="dataSource" />
       </bean>
   </beans>
   ```

   在整个beans的文件中创建了3个bean，并把id为dataSource的组件通过属性dataSource（**即调用setDataSource()方法**）注入到另外两个组件中。

2. 使用构造函数注入

   ```java
   public class BookService {
       private DataSource dataSource;
   
       public BookService(DataSource dataSource) {
           this.dataSource = dataSource;
       }
   }
   ```

3. 使用注解注入

   ```java
   @Configuration
   public class DemoConfig {
       @Bean
       public DemoUser demoUser_ioc() {
           return new DemoUser("User", 18);
       }
   
       @Bean
       public DemoPet demoPet_ioc() {
           return new DemoPet("Naomi");
       }
   
       // 通过@Bean("name")来直接命名Bean在IoC容器中的名字
       @Bean("Gatsby")
       public DemoUser demoUser_bean() {
           return new DemoUser("Gatsby", 23);
       }
   
       // 创建一个DemoMaster对象，其中DemoMaster中有DemoPet字段，因此存在依赖
       @Bean("keeper")
       public DemoMaster demoMaster_bean() {
           return new DemoMaster("Zoo", 23);
       }
   
   }
   ```

### application.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="userService" class="com.itranswarp.learnjava.service.UserService">
        <property name="mailService" ref="mailService" />
    </bean>

    <bean id="mailService" class="com.itranswarp.learnjava.service.MailService" />
</beans>
```

- 在两个Bean中，每个Bean都有一个独立的标识id，相当于Bean的唯一识别号。
- 在Bean中，通过<property name="..." ref="..."/>注入另一个Bean；
- Bean的顺序不重要，Spring会根据依赖关系正确初始化。

根据xml里的依赖关系，实际上的逻辑顺序是这样的：

```java
UserService userService = new UserService();
MailService mailService = new MailService();
userService.setMailService(mailService);
```

此时将maiilService注入userService。

> 特别的，如果注入的不是Bean，而是基本类型例如Integer、Boolean、String，则通过value注入。即将ref改成value="..."。

接下来，从Spring IoC的容器中取出Bean然后使用。

```java
// 1. 返回IoC容器，IoC容器里面就包含了当前应用的所有组件
ConfigurableApplicationContext run = SpringApplication.run(DemoApplication.class, args);

// 2. 我们可以来查看下IoC容器里面所有的组件，只要能查找到某一个组件，就说明这个组件是能工作的，至于怎么工作，这就是我们后来要阐述的原理了
String[] names = run.getBeanDefinitionNames(); // 获取所有组件定义的名字
for (int i = 0; i < 20; ++i) {
    System.out.println(names[i]);
}

// 3. 从容器中获取组件
DemoUser userA = run.getBean("demoUser_ioc", DemoUser.class);
DemoUser userB = run.getBean("demoUser_ioc", DemoUser.class);
```

或者

```java
ApplicationContext context = new ClassPathXmlApplicationContext("application.xml");
UserService userService = context.getBean(UserService.class);
User user = userService.login("bob@example.com", "password");
System.out.println(user.getName());
```

[ApplicationContext和ConfigurableApplicationContext解析_路漫漫兮-CSDN博客](https://blog.csdn.net/ligel136738/article/details/113533132)

ConfigurableApplicationContext是ApplicationContext的子接口，也就包含了ApplicationContext。通过方法可以知道，ConfigurableApplicationContext重在对各种属性的配置，而ApplicationContext接口主要各种属性的get方法。



### annotation configuration

1. @Configuration

   在类上标注@Configuration注解，就相当于告诉Spring Boot这个文件是一个Spring XML配置文件。因为我们创建ApplicationContext或ConfigurableApplicationContext的时候，使用的实现类是AnnotationConfigApplicationContext，必须传入一个@Configuration的类。

2. @Componet和@Bean

   1. @Component注解表明一个类会作为组件类，并告知Spring要为这个类创建bean。
   2. @Bean注解告诉Spring这个方法将会返回一个对象，这个对象要注册为Spring应用上下文中的bean。通常方法体中包含了最终产生bean实例的逻辑。

   两者的目的是一样的都是为了注入bean到spring容器中。**区别在于，Componet通常是使用类路径扫描侦测然后自动装配到IoC中，但是Bean需要在标有注解的方法中，定义产生整个bean的逻辑流程，以及返回值。@Component注释类，@Bean作注释方法。**
   
3. @Resource和@Autowired

    @Autowired是spring提供的注解，@Autowired默认使用byType进行装配，byType无法装配则使用byName，如果接口有多个实现类，需要配合@Qualifier注解使用。
   ————————————————
   版权声明：本文为CSDN博主「_云卷云舒_」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
   原文链接：https://blog.csdn.net/Alexshi5/article/details/84000678

4. @Controller、@Service、@Repository

    | 注解        | 含义                                         |
    | ----------- | -------------------------------------------- |
    | @Component  | 最普通的组件，可以被注入到spring容器进行管理 |
    | @Repository | 作用于持久层                                 |
    | @Service    | 作用于业务逻辑层                             |
    | @Controller | 作用于表现层（spring-mvc的注解）             |

    [@Component, @Repository, @Service的区别_fansili的博客-CSDN博客_service](https://blog.csdn.net/fansili/article/details/78740877)

    

### customize Bean

对于Spring容器来说，当我们把一个Bean标记为@Component后，它就会自动为我们创建一个单例（Singleton），即容器初始化时创建Bean，容器关闭前销毁Bean。**在容器运行期间，我们调用getBean(Class)获取到的Bean总是同一个实例。**

还有一种Bean，我们每次调用getBean(Class)，容器都返回一个新的实例，这种Bean称为Prototype（原型），它的生命周期显然和Singleton不同。声明一个Prototype的Bean时，需要添加一个额外的@Scope注解：

```java
@Component
@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE) // @Scope("prototype")
public class MailSession {
    ...
}
```



有时候会存在一些接口相同但是实现类不同的Bean，例如，在注册用户的时候，需要对email、password和name三个变量进行字符串合法性验证，便于扩展，我们需要先定义验证接口。

Validator.java

```java
package com.gatsby.service;

public interface Validator {
    void validate(String email, String pswd, String name);
}
```

EmailValidator.java

```java
package com.gatsby.service.impls;

import com.gatsby.service.Validator;
import org.springframework.core.annotation.Order;
import org.springframework.stereotype.Service;

/**
 * @ClassName: EmailValidator
 * @Description: validate email
 * @author: Gatsby
 * @date: 2021/9/14 15:22
 */

@Service
@Order(1)
public class EmailValidator implements Validator {
    @Override
    public void validate(String email, String pswd, String name) {
        if (!email.matches("^[a-z0-9]+\\@[a-z0-9]+\\.[a-z]{2,10}$")) {
            throw new IllegalArgumentException("invalid email");
        }
    }
}
```

PswdValidator.java

```java
package com.gatsby.service.impls;

import com.gatsby.service.Validator;
import org.springframework.core.annotation.Order;
import org.springframework.stereotype.Service;

/**
 * @ClassName: PswdValidator
 * @Description: validate password
 * @author: Gatsby
 * @date: 2021/9/14 15:47
 */

@Service
@Order(2)
public class PswdValidator implements Validator {
    @Override
    public void validate(String email, String pswd, String name) {
        if (!pswd.matches("^.{6,20}$")) {
            throw new IllegalArgumentException("invalid password");
        }
    }
}
```

NameValidator.java

```java
package com.gatsby.service.impls;

import com.gatsby.service.Validator;
import org.springframework.core.annotation.Order;
import org.springframework.stereotype.Service;

/**
 * @ClassName: NameValidate
 * @Description: validate name
 * @author: Gatsby
 * @date: 2021/9/14 15:50
 */

@Service
@Order(3)
public class NameValidator implements Validator {
    @Override
    public void validate(String email, String pswd, String name) {
        if (name == null || name.isEmpty() || name.length() > 20) {
            throw new IllegalArgumentException("invalid name");
        }
    }
}
```

Validators.java

```java
package com.gatsby.service.impls;

import com.gatsby.service.Validator;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

/**
 * @ClassName: Validators
 * @Description: emailValidator/passwordValidatot/nameValidator
 * @author: Gatsby
 * @date: 2021/9/14 16:09
 */

@Service
public class Validators {

    final List<Validator> validators;

    @Autowired
    public Validators(List<Validator> vs) {
        this.validators = vs;
    }

    public void validate(String email, String password, String name) {
        for (Validator validator : this.validators) {
            validator.validate(email, password, name);
        }
    }
}
```



Tips:

1. field injection not recommended

   涉及到Dependency Injection的三种方式，构造方法注入、字段注入、set方法注入
   相比较而言：

    优点：变量方式注入非常简洁，没有任何多余代码，非常有效的提高了java的简洁性。即使再多几个依赖一样能解决掉这个问题。

    缺点：不能有效的指明依赖。相信很多人都遇见过一个bug，依赖注入的对象为null，在启动依赖容器时遇到这个问题都是配置的依赖注入少了一个注解什么的，然而这种方式就过于依赖注入容器了，当没有启动整个依赖容器时，这个类就不能运转，在反射时无法提供这个类需要的依赖。
    在使用set方式时，这是一种选择注入，可有可无，即使没有注入这个依赖，那么也不会影响整个类的运行。
    在使用构造器方式时已经显式注明必须强制注入。通过强制指明依赖注入来保证这个类的运行。

    另一个方面：
    依赖注入的核心思想之一就是被容器管理的类不应该依赖被容器管理的依赖，换成白话来说就是如果这个类使用了依赖注入的类，那么这个类摆脱了这几个依赖必须也能正常运行。然而使用变量注入的方式是不能保证这点的。
    既然使用了依赖注入方式，那么就表明这个类不再对这些依赖负责，这些都由容器管理，那么如何清楚的知道这个类需要哪些依赖呢？它就要使用set方法方式注入或者构造器注入。
   [使用@Autowired注解警告Field injection is not recommended_下雨了_简的博客-CSDN博客](https://blog.csdn.net/zhangjingao/article/details/81094529)
   
   [spring为何推荐使用构造器注入 - 简书 (jianshu.com)](https://www.jianshu.com/p/4694b1a82546)

2. 由于Bean已经注册到IoC容器中，所以在Controller中不能new一个Validators，而是要从IoC将这个Bean注入进来，也就是使用Autowired。





## AOP

[使用AOP - 廖雪峰的官方网站 (liaoxuefeng.com)](https://www.liaoxuefeng.com/wiki/1252599548343744/1266265125480448)

AOP: Aspect Oriented Programming

当编程的思维还是面向过程编程的时候，我们更多根据整个业务的流程从前往后思考。但是OOP流行之后，我们更多的思维放在了设计对象上，例如有三个对象，都有各自不同的核心功能，但是此时我们需要在每个对象中都加入这个公共方法，有一种方法是，再新建一个类，但是原有的三个对象会频繁调用这个类的接口。

AOP的思想在于，既然这部分流程是公共的，不如直接将这部分的内容当作一个Aspect，然后以某种自动化的方式，把切面组织到核心代码中，实现代理。

因此AOP的使用场景主要是：日志记录、性能统计、安全控制、事务处理、异常处理等。

例如：日志功能，日志代码往往横向散布在所有对象层次中，而与之对应的对象的核心功能毫无关系。对于其他类型的代码，如安全性、异常处理、透明的持续性等也都是如此，这种散布在各处而且无关的代码被称为横切（cross-cutting）。在OOP设计中，它们会导致大量重复的代码，不利用模块的重用。

![img](https://upload-images.jianshu.io/upload_images/4933701-200f890ecfb3c14c.png?imageMogr2/auto-orient/strip|imageView2/2/format/webp)

AOP可以说是OOP的补充和完善，OOP引入封装、继承、多态等概念建立了一种对象层次结构，用来模拟公共行为的一个集合。当需要为分散的对象引入公共行为的时候，OOP显得无能为力，也就是说，OOP允许定义从上到下的关系，但并不适合定义从左到右的关系。

[AOP 面向切面编程 - 简书 (jianshu.com)](https://www.jianshu.com/p/f1770b9dce27)

### concepts

- Aspect:：切面，一个横跨多个核心功能的功能，或者称之为系统关注点
- Joinpoint：连接点，定义在何处从核心逻辑功能切入到系统功能
- Pointcut：切入点，即一组连接点的集合
- Advice：增强，指的是特定Joinpoint需要特定的动作
- Introduction：引介，为一个已有的Java对象动态新增新的接口
- Weaving：织入，将整个切面整合到完整的流程中
- Interceptor：拦截器，一种增强实现的方式
- Target Object：目标对象，真正需要执行业务的核心逻辑对象
- AOP Proxy：客户端持有的增强后的对象引用

### 静态代理和动态代理

静态代理是指AOP框架会在编译阶段就生成AOP代理类，因此也被称为编译时增强，AspectJ是最具有代表性的静态代理。由于在编译阶段就已经生成了代理类，因此会在效率上比动态代理高一些。AspectJ可以单独使用，也可以结合Spring使用。



动态代理是指AOP框架不会去修改编译时生成的字节码，而是在运行的时候生成一个AOP代理对象，这个AOP对象包括了目标对象的全部内容，并且在特定的切点做了增强处理，并回调原对象的方法。

Spring中的动态代理主要有两种方式，JDK动态代理和CGLIB动态代理。



### annotation

@Aspect

标记这个类是一个aspect

@Before

前置通知，在方法执行之前执行

@After

后置通知，在方法执行之后执行

@AfterRunning

返回通知，在方法返回结果之后执行

@AfterThrowing

异常通知，在方法抛出异常之后执行

@Around

环绕通知，围绕着方法执行

[AspectJ 切面注解中五种通知注解：@Before、@After、@AfterRunning、@AfterThrowing、@Around_u010502101的博客-CSDN博客](https://blog.csdn.net/u010502101/article/details/78823056)

[Spring AOP——Spring 中面向切面编程 - SharpCJ - 博客园 (cnblogs.com)](https://www.cnblogs.com/joy99/p/10941543.html)

```java
execution * com.springinaction.springidol.Instrucment.play(..)
```

![img](https://img2018.cnblogs.com/blog/758949/201905/758949-20190529225530124-1714809272.png)

我们使用execution指示器选择instrument的play方法，方法表达式以*开始，表示我们不关心返回值类型，然后我们指定了全限定类名和方法名。对于方法参数列表，我们使用..标识切点，选择任意的play方法，无论该方法的入参是什么。

多个匹配之间我们可以使用链接符&&、||、!来表示逻辑关系。但是在xml方式下配置使用and or not。

Interface - Consumer.java

```java
package com.gatsby.entity;


public interface IConsumer {
    String buy();
}
```



implments - FemaleConsumer.java

```java
package com.gatsby.entity.impls;

import com.gatsby.entity.IConsumer;
import org.springframework.stereotype.Component;

/**
 * @ClassName: FemaleConsumerImpl
 * @Description: female consumer
 * @author: Gatsby
 * @date: 2021/9/16 14:45
 */

@Component
public class FemaleIConsumerImpl implements IConsumer {
    @Override
    public String buy() {
        System.out.println("The female consumer bought a Chanel");
        return "Chanel";
    }
}
```



implements - MaleConsumer.java

```java
package com.gatsby.entity.impls;

import com.gatsby.entity.IConsumer;
import org.springframework.stereotype.Component;

/**
 * @ClassName: FemaleConsumerImpl
 * @Description: female consumer
 * @author: Gatsby
 * @date: 2021/9/16 14:45
 */

@Component
public class FemaleIConsumerImpl implements IConsumer {
    @Override
    public String buy() {
        System.out.println("The female consumer bought a Chanel");
        return "Chanel";
    }
}
```



Aspect - BuyAspect.java

```java
package com.gatsby.aspects;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;


/**
 * @ClassName: BuyAspect
 * @Description: the aspect which is common "buy"
 * @author: Gatsby
 * @date: 2021/9/17 9:39
 */


@Aspect
@Component
public class BuyAspect {
    @Pointcut("execution(* com.gatsby.entity.IConsumer.buy(..))")
    public void buyPoint() {}

    @Before("buyPoint()")
    public void beforeBuy() {
        System.out.println("Before...");
    }

    @After("buyPoint()")
    public void afterBuy() {
        System.out.println("After...");
    }

    @AfterReturning("buyPoint()")
    public void afterRunningBuy() {
        System.out.println("After returning...");
    }

    @Around("buyPoint()")
    public void aroundBuy(ProceedingJoinPoint pj){
        try {
            System.out.println("Around...once");
            pj.proceed();
            System.out.println("Around...twice");
        } catch (Throwable throwable) {
            throwable.printStackTrace();
        }
    }
}
```



Test - AopTest.java

```java
package com.gatsby.aoptest;

import com.gatsby.entity.IConsumer;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.stereotype.Component;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import javax.annotation.Resource;

/**
 * @ClassName: AopTest
 * @Description: test the main aop class
 * @author: Gatsby
 * @date: 2021/9/17 10:21
 */

@Component
@SpringBootTest
@RunWith(SpringJUnit4ClassRunner.class)
public class AopTest {
    @Resource(name = "maleIConsumerImpl")
    private IConsumer mc;

    @Resource(name = "femaleIConsumerImpl")
    private IConsumer fc;

    @Test
    public void test() {
        mc.buy();
        fc.buy();
    }
}
```



Output

```java
Around...once
Before...
The male consumer bought a PS5
After returning...
After...
Around...twice
Around...once
Before...
The female consumer bought a Chanel
After returning...
After...
Around...twice
```



## Design patterns

设计模式的目标是将代码复用，提高代码的可扩展性和可维护性。

[设计模式－单例模式(Singleton )（７种） - 掘金 (juejin.cn)](https://juejin.cn/post/7011368125425778695)

### Factory Method

工厂模式是一个对象创建型模式。核心思想是

>定义一个用于创建对象的接口，让子类决定实例化哪一个类。
>
>Factory Method使一个类的实例化延迟到子类。

工厂模式使得创建对象和使用对象总是分离的，客户端总是引用抽象工厂和抽象产品。



**简单工厂**

现在我们定义一个工厂类，**它可以根据参数的不同返回不同类的实例，被创建的实例通常都具有共同的父类。**

Phone class:

```java 
public interface Phone {
    void make();
}
```



Xiaomi class:

```java
public class Xiaomi implements Phone {
    public Xiaomi() {
        this.make();
    }
    
    @Override
    void make() {
        System.out.println("make xiaomi phone!");
    }
}
```



Iphone class:

```java 
public class Iphone implements Phone {
    public Iphone() {
        this.make();
    }
    
    @Override
    void make() {
        System.out.println("make Iphone!");
    }
}
```



PhoneFactory class:

```java 
public class PhoneFactory {
    public Phone makePhone(String phoneType) {
        if(phoneType.equalsIgnoreCase("Xiaomi")) {
            return new Xiaomi();
        }
   		else if(phoneType.equalsIgnoreCase("Iphone")) {
            return new Iphone();
        }
        return null;
    }
}
```



main class

```java
public class Demo {
    public static void main(String[] arg) {
        PhoneFactory factory = new PhoneFactory();
        Phone miPhone = factory.makePhone("xiaomi");
        IPhone iPhone = (IPhone)factory.makePhone("iPhone");
    }
}
```



**工厂方法**

> 和简单工厂模式中工厂负责生产所有产品相比，工厂方法模式将生成具体产品的任务分发给具体的产品工厂。
>
> 也就是定义一个抽象工厂，其定义了产品的生产接口，但不负责具体的产品，将生产任务交给不同的派生类工厂。这样不用通过指定类型来创建对象了。

AbstractFactory interface

```java
package org.example.factory;

public interface AbstractFactory {
    AbstractPhone makePhone();
}
```



AbstractPhone interface

```java
package org.example.factory;

public interface AbstractPhone {
    
}
```



Iphone class

```java
package org.example.factory;

/**
 * @ClassName: Iphone
 * @Description: apple iphone
 * @author: Gatsby
 * @date: 2021/9/30 9:57
 */

public class Iphone implements AbstractPhone {
    Iphone() {
        System.out.println("Apple Phone");
    }
}
```



Xiaomi class

```java
package org.example.factory;

/**
 * @ClassName: Xiaomi
 * @Description: xiaomi phone
 * @author: Gatsby
 * @date: 2021/9/30 9:56
 */

public class Xiaomi implements AbstractPhone {
    Xiaomi() {
        System.out.println("Xiaomi Phone");
    }
}
```



IphoneFactory class 

```java
package org.example.factory;

/**
 * @ClassName: IphoneFactory
 * @Description: iphone factory
 * @author: Gatsby
 * @date: 2021/9/30 9:59
 */

public class IphoneFactory implements AbstractFactory {
    @Override
    public AbstractPhone makePhone() {
        return new Iphone();
    }
}
```



XiaomiFactory class

```java
package org.example.factory;

/**
 * @ClassName: XiaomiFactory
 * @Description: xiaomi factory
 * @author: Gatsby
 * @date: 2021/9/30 10:01
 */

public class XiaomiFactory implements AbstractFactory {
    @Override
    public AbstractPhone makePhone() {
        return new Xiaomi();
    }
}
```



### Abstract Factory

要求苹果工厂和小米工厂同时还要生产笔记本。

AbstractNotebook interface

```java
package org.example.factory;

public interface AbstractNotebook {
}
```



Macbook class

```java
package org.example.factory;

import com.squareup.okhttp.Route;

/**
 * @ClassName: Macbook
 * @Description: MacBook
 * @author: Gatsby
 * @date: 2021/9/30 13:24
 */

public class Macbook implements AbstractNotebook{
    Macbook() {
        System.out.println("this is a MacBook");
    }
}
```



Xiaomibook

```java
package org.example.factory;

/**
 * @ClassName: Xiaomibook
 * @Description: xiaomi notebook
 * @author: Gatsby
 * @date: 2021/9/30 13:25
 */

public class Xiaomibook implements AbstractNotebook{
    Xiaomibook() {
        System.out.println("this is a xiaomi book");
    }
}
```



IphoneFactory class

```java
package org.example.factory;

/**
 * @ClassName: IphoneFactory
 * @Description: iphone factory
 * @author: Gatsby
 * @date: 2021/9/30 9:59
 */

public class IphoneFactory implements AbstractFactory {
    @Override
    public AbstractPhone makePhone() {
        return new Iphone();
    }

    @Override
    public AbstractNotebook makeNotebook() {
        return new Macbook();
    }
}
```



XiaomiFactory class 

```java
package org.example.factory;

/**
 * @ClassName: XiaomiFactory
 * @Description: xiaomi factory
 * @author: Gatsby
 * @date: 2021/9/30 10:01
 */

public class XiaomiFactory implements AbstractFactory {
    @Override
    public AbstractPhone makePhone() {
        return new Xiaomi();
    }

    @Override
    public AbstractNotebook makeNotebook() {
        return new Xiaomibook();
    }
}
```



FactoryTest class

```java
package org.example.factory;

/**
 * @ClassName: FactoryTest
 * @Description: test factory
 * @author: Gatsby
 * @date: 2021/9/30 10:02
 */

public class FactoryTest {
    public static void main(String[] args) {
        AbstractFactory iphoneFactoryTest = new IphoneFactory();
        AbstractFactory xiaomiFactoryTest = new XiaomiFactory();
        iphoneFactoryTest.makePhone();
        xiaomiFactoryTest.makePhone();
        iphoneFactoryTest.makeNotebook();
        xiaomiFactoryTest.makeNotebook();
    }

}
```



### Singleton

> 保证一个类仅有一个实例，并且提供一个访问它的全局访问点。

想要满足这个类只有一个实例，就要控制这个类不能被外部new，所以将这个类的构造函数设置为private，这样就防止了调用方创建实例，但是在类的内部，可以用一个静态字段来引用唯一创建的实例。



Singleton class

```java
package org.example.singleton;

/**
 * @ClassName: SingletonTest
 * @Description: singleton pattern test
 * @author: Gatsby
 * @date: 2021/9/30 13:47
 */

public class Singleton {
    // new a single instance
    private static final Singleton INSTANCE = new Singleton();

    // let the constructor private
    private Singleton() {

    };

    // provide a interface to get the instance
    public static Singleton getInstance() {
        return INSTANCE;
    }

    public void saySomething() {
        System.out.println("say something");
    }

}
```



SingletonTest class

```java
package org.example.singleton;

/**
 * @ClassName: SingletonTest
 * @Description: SingletonTest
 * @author: Gatsby
 * @date: 2021/9/30 13:55
 */

public class SingletonTest {
    public static void main(String[] args) {
        Singleton instance = Singleton.getInstance();
        instance.saySomething();
    }
}
```



**单例模式的基本思路就是**

1. **构造方法是private，确保外部无法实例化**
2. **通过private static保证变量持有唯一实例，保证全局唯一性**
3. **通过public static方法返回唯一实例**



### Builder

> 将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。

1. 相同的方法，不同的执行顺序，产生不同的事件结果时；
2. 多个部件或零件，都可以装配到一个对象中，但是产生的运行结果又不相同时；
3. 产品类非常复杂，或者产品类中的调用顺序不同产生了不同的效能，这个时候使用建造者模式非常合适；

一个常用的使用方式，**就是当一个类构造器需要传入很多参数时，如果创建这个类的实例，代码可读性会非常差**，而且很容易引入错误，此时就可以利用 builder模式进行重构.



常规的Computer class

```java
public class Computer {
    private String cpu;
    private String screen;
    private String memory;
    private String mainBoard;

    public Computer(String cpu, String screen, String memory, String mainBoard) {
        this.cpu = cpu;
        this.screen = screen;
        this.memory = memory;
        this.mainBoard = mainBoard;
    }
}
```
使用builder的写法
```java
public class NewComputer {
    private String cpu;
    private String screen;
    private String memory;
    private String mainBoard;

public static final class Builder {
        private NewComputer mNewComputer;
    
        public Builder() {
            mNewComputer = new NewComputer();
        }
    
        public Builder cpu(String val) {
            mNewComputer.cpu = val;
            return this;
        }
    
        public Builder screen(String val) {
            mNewComputer.screen = val;
            return this;
        }
    
        public Builder memory(String val) {
            mNewComputer.memory = val;
            return this;
        }
    
        public Builder mainBoard(String val) {
            mNewComputer.mainBoard = val;
            return this;
        }
    
        public NewComputer create() {
            return mNewComputer;
        }
    
    }
}

```



Test class

```java
class Test {
    public static void main(String[] args) {

        //非Builder 模式 
        Computer computer = new Computer("cpu", "screen", "memory", "mainboard");
        
        //Builder 模式 
        NewComputer newComputer = new NewComputer.Builder()
                .cpu("cpu")
                .screen("screen")
                .memory("memory")
                .mainBoard("mainBoard")
                .create();
    }
}
```



### Prototype

Java的两种拷贝模式：

1. 浅拷贝：浅拷贝是对基本数据类型进行值传递，对引用数据类型进行引用传递般的拷贝。
2. 深拷贝：深拷贝是对于基本数据类型进行值传递，对于引用数据类型，创建一个新的对象，并复制内容。（除了浅拷贝的八种基本数据类型，其他的拷贝都是属于深拷贝，例如数组、对象）



### Decorator







### Facade

> 要求一个子系统的外部与其内部的通信必须通过一个统一的对象进行。外观模式提供一个高层次的接口，使得子系统更易于使用。

以注册公司为例，假设注册公司需要三步：

1. 向工商局申请公司营业执照；
2. 在银行开设账户；
3. 在税务局开设纳税号。

```java
// 工商注册:
public class AdminOfIndustry {
    public Company register(String name) {
        ...
    }
}

// 银行开户:
public class Bank {
    public String openAccount(String companyId) {
        ...
    }
}

// 纳税登记:
public class Taxation {
    public String applyTaxCode(String companyId) {
        ...
    }
}
```

如果子系统比较复杂，并且客户对流程也不熟悉，那就把这些流程全部委托给中介：

```java
public class Facade {
    public Company openCompany(String name) {
        Company c = this.admin.register(name);
        String bankAccount = this.bank.openAccount(c.getId());
        c.setBankAccount(bankAccount);
        String taxCode = this.taxation.applyTaxCode(c.getId());
        c.setTaxCode(taxCode);
        return c;
    }
}
```



### Decorator 

通俗来说，Decorator就是创建一个子类在父类的基础上创建一个新的方法，但是又不完全是创建一个新的子类，开销要比完全创建一个开销要小。就增加功能而言，相比生成一个子类要更加灵活。

主要用于在运行期间给某个对象的实例增加功能。



IMan interface 

```java 
package org.example.decorator;

public interface IMan {
    void walk();
}

```



ManImpl class

```java
package org.example.decorator;

/**
 * @ClassName: ManImpl
 * @Description: man impl
 * @author: Gatsby
 * @date: 2021/9/30 17:23
 */

public class ManImpl implements IMan{
    @Override
    public void walk() {
        System.out.println("I can walk");
    }
}
```

假设此时这个Man，过了几天之后又学会了跑

但是如果直接修改ManImpl会对之前的代码或者后续的代码产生影响，于是可以使用装饰者模式实现

此时再创建一个抽象类

Decorator abstract class

```java
package org.example.decorator;

/**
 * @ClassName: Decorator
 * @Description: decorator abstract class
 * @author: Gatsby
 * @date: 2021/9/30 17:34
 */

public abstract class Decorator implements IMan{
    // 这个就是这个抽象类的核心，它已经持有一个正常的实现类，其余的东西再在这个抽象类的实现类里面加
    protected ManImpl man;

    // 此时调用的就是这个man的方法
    @Override
    public void walk() {
        man.walk();
    }

    // 开放一个public接口用于调用
    public Decorator(ManImpl man) {
        this.man = man;
    }
}
```



此时是继承这个抽象类，这个抽象类作为一个父类，设计一个子类来给他加新的功能。因为是抽象类而不是接口，所以是extends而不是implments。

ManDecorator class

```java
package org.example.decorator;

/**
 * @ClassName: ManDecorator
 * @Description: man's decorator
 * @author: Gatsby
 * @date: 2021/9/30 17:41
 */

// 由于只是一个抽象类而不是一个interface，所以是用继承而不是一个implements
public class ManDecorator extends Decorator{
    // 此时调用父类的构造函数，那么ManImpl的man就构造好了
    public ManDecorator(ManImpl man) {
        super(man);
    }

    @Override
    public void walk() {
        super.walk();

        // 添加新的功能
        run();
    }

    // 将新的功能写在这个新的类中
    public void run() {
        System.out.println("Now I can even run");
    }

}
```



DecoratorTest class

```java
package org.example.decorator;

/**
 * @ClassName: DecoratorTest
 * @Description: test decorator
 * @author: Gatsby
 * @date: 2021/9/30 17:47
 */

public class DecoratorTest {
    public static void main(String[] args) {
        // 先构造一个ManImpl，用于后面的NewMan的构造
        ManImpl man = new ManImpl();
        ManDecorator newMan = new ManDecorator(man);

        // 此时这个新的man的walk中甚至可以run
        newMan.walk();
    }
}
```



## Lambda

```java
Thread t = null;
for (int i = 0; i < 10; ++i) {
    int lambdaI = i;
    t = new Thread( ()-> {
        System.out.println("this is the " + lambdaI + "th lambda");
    });
    t.start();
}

ArrayList<Integer> ary = new ArrayList<Integer>();
ary.add(1);
ary.add(4);
ary.add(3);
ary.add(6);
ary.add(2);
ary.add(5);

ary.sort((a, b) -> a-b); // 默认从小到大序
ary.sort((a, b) -> b-a); // 从大到小序重排
System.out.println(ary.get(0));
```

针对第一段代码中的，lambdaI，为什么没有直接引用循环变量中的i，Java技术核心卷中给出的解释是：

> 可以看到，lambda表达式可以捕获外围作用域中变量的值。在Java中，要确保所捕获的值是明确定义的，这里有一个重要的限制。在lambda表达式中，只能引用值不会改变的变量。之所以有这个限制是有原因的。如果在lambda表达式中改变变量，并发执行多个动作时就会不安全。对于目前为止我们看到的动作不会发生这种情况，不过一般来讲，这确实是一个严重的问题。

因此我们必须重新初始化一个新的变量，并且确保这个新的变量一经初始化就不会在再改变，

> 之所以有这个限制是有原因的。如果在lambda表达式中改变变量，并发执行多个动作时就会不安全。对于目前为止我们看到的动作不会发生这种情况，不过一般来讲，这确实是一个严重的问题。





