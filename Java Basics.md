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



### enum







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

因为编译器检测到我们自定义的构造方法之后，就不会默认提供一个无参的构造方法。

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



#### extends & combines

面向对象编程中，有一条非常经典的设计原则，那就是组合优先于继承，多用组合，少用继承。

在《阿里巴巴Java开发手册》中有一条规定：**谨慎使用继承的方式进行扩展，优先使用组合的方式实现。**如果不得已的话，必须符合里氏代换法则。

>里氏代换原则（Liskov Substitution Principle，LSP）是面向对象编程中的一个重要原则，由Barbara Liskov和Jeannette Wing在1987年提出。该原则表述如下：
>
>“如果对于每一个类型为S的对象o1，都有类型为T的对象o2，使得以T定义的所有程序P在所有的对象o1都代换成o2时，程序P的行为没有发生变化，那么类型S是类型T的子类型。”
>
>简单来说，LSP原则要求子类对象必须能够替换掉其父类对象，并且程序逻辑不会发生错误或者异常。也就是说，子类对象在运行时必须能够完全替代父类对象的行为。
>
>该原则是开闭原则的具体实现，它使得程序的扩展更加容易，可以增加新的子类，也可以通过重写父类方法来实现新的功能，同时也保证了程序的健壮性和可维护性。

[在设计原则中，为什么反复强调组合要优于继承？ - Kevin.ZhangCG - 博客园 (cnblogs.com)](https://www.cnblogs.com/Kevin-ZhangCG/p/14892610.html)

继承是面向对象的四大特性之一，用来表示类之间的is-a关系，可以解决代码复用的问题。虽然继承有诸多作用，但继承层次过深、过复杂，也会影响到代码的可维护性。



假设我们要设计一个关于鸟的类。我们将“鸟”这样一个抽象的事物概念，定义为一个抽象类AbstractBird。所有更细分的鸟，比如麻雀、鸽子、乌鸦等，都继承这个抽象类。我们知道，大部分鸟都会飞，那我们可不可以在 AbstractBird抽象类中，定义一个fly()方法呢？

答案是否定的。尽管大部分鸟都会飞，但也有特例，比如鸵鸟就不会飞。鸵鸟继承具有fly()方法的父类，那鸵鸟就具有“飞”这样的行为，这显然不对。如果在鸵鸟这个子类中重写fly() 方法，让它抛出UnSupportedMethodException异常呢？

这种写法虽然可以解决问题，但不优雅。因为除了鸵鸟之外，不会飞的鸟还有很多，比如企鹅。对于这些不会飞的鸟来说，全部都去重写fly()方法，抛出异常，完全属于代码重复。理论上这些不会飞的鸟根本就不应该拥有fly()方法，让不会飞的鸟暴露fly()接口给外部，增加了被误用的概率。

要解决上面的问题，就得让AbstractBird类派生出两个更加细分的抽象类：会飞的鸟类AbstractFlyableBird和不会飞的鸟类AbstractUnFlyableBird，让麻雀、乌鸦这些会飞的鸟都继承 AbstractFlyableBird，让鸵鸟、企鹅这些不会飞的鸟，都继承 AbstractUnFlyableBird 类。

这样一来，继承关系变成了三层。但是如果我们不只关注“鸟会不会飞”，还要继续关注“鸟会不会叫”，将鸟划分得更加细致时呢？两个关注行为自由搭配起来会产生四种情况：会飞会叫、不会飞会叫、会飞不会叫、不会飞不会叫。如果继续沿用刚才的设计思路，继承层次会再次加深。

如果继续增加“鸟会不会下蛋”这样的行为，类的继承层次会越来越深、继承关系会越来越复杂。而这种层次很深、很复杂的继承关系，一方面，会导致代码的可读性变差。因为我们要搞清楚某个类具有哪些方法、属性，必须阅读父类的代码、父类的父类的代码……一直追溯到最顶层父类的代码。另一方面，这也破坏了类的封装特性，将父类的实现细节暴露给了子类。子类的实现依赖父类的实现，两者高度耦合，一旦父类代码修改，就会影响所有子类的逻辑。

继承最大的问题就在于：**继承层次过深、继承关系过于复杂时会影响到代码的可读性和可维护性。**



**组合相比继承有哪些优势**

复用性是面向对象技术带来的很棒的潜在好处之一。如果运用的好的话可以帮助我们节省很多开发时间，提升开发效率。但是，如果被滥用那么就可能产生很多难以维护的代码。作为一门面向对象开发的语言，代码复用是Java引人注意的功能之一。Java代码的复用有继承、组合以及委托三种具体的实现形式。

对于上面提到的继承带来的问题，可以利用组合（composition）、接口、委托（delegation）三个技术手段一块儿来解决。

接口表示具有某种行为特性。针对“会飞”这样一个行为特性，我们可以定义一个Flyable接口，只让会飞的鸟去实现这个接口。对于会叫、会下蛋这些行为特性，我们可以类似地定义Tweetable接口、EggLayable接口。

```java 
public interface Flyable {
  void fly();
}
public interface Tweetable {
  void tweet();
}
public interface EggLayable {
  void layEgg();
}
public class Ostrich implements Tweetable, EggLayable {//鸵鸟
  //... 省略其他属性和方法...
  @Override
  public void tweet() { //... }
  @Override
  public void layEgg() { //... }
}
public class Sparrow implements Flayable, Tweetable, EggLayable {//麻雀
  //... 省略其他属性和方法...
  @Override
  public void fly() { //... }
  @Override
  public void tweet() { //... }
  @Override
  public void layEgg() { //... }
}
```



不过，接口只声明方法，不定义实现。也就是说，**每个会下蛋的鸟都要实现一遍layEgg()方法，并且实现逻辑几乎是一样的（可能极少场景下会不一样），这就会导致代码重复的问题。那这个问题又该如何解决呢？有以下两种方法。**

1. 使用委托：针对三个接口再定义3个类，它们分别是：实现了fly()方法的 FlyAbility类、实现了tweet()方法的TweetAbility类、实现了layEgg()方法的 EggLayAbility类。然后，通过组合和委托技术来消除代码重复。

   ```java
   public interface Flyable {
     void fly()；
   }
   public class FlyAbility implements Flyable {
     @Override
     public void fly() { //... }
   }
   //省略Tweetable/TweetAbility/EggLayable/EggLayAbility
   
   public class Ostrich implements Tweetable, EggLayable {//鸵鸟
     private TweetAbility tweetAbility = new TweetAbility(); //组合
     private EggLayAbility eggLayAbility = new EggLayAbility(); //组合
     //... 省略其他属性和方法...
     @Override
     public void tweet() {
       tweetAbility.tweet(); // 委托
     }
     @Override
     public void layEgg() {
       eggLayAbility.layEgg(); // 委托
     }
   }
   ```

2. 使用Java 8的接口默认方法

   在Java8中，我们可以在接口中写默认实现方法。使用关键字default定义默认接口实现，当然这个默认的方法也可以重写。

   ```java 
   public interface Flyable {
     default void fly() {
       //默认实现... 
     }
   }
   
   
   public interface Flyable {
     default void fly() {
       //默认实现... 
     }
   }
   
   public interface Tweetable {
     default void tweet() {
       //默认实现... 
     }
   }
   
   public interface EggLayable {
     default void layEgg() {
       //默认实现... 
     }
   }
   
   public class Ostrich implements Tweetable, EggLayable {//鸵鸟
     //... 省略其他属性和方法...
   }
   public class Sparrow implements Flayable, Tweetable, EggLayable {//麻雀
     //... 省略其他属性和方法...
   }
   ```

   

3. 廖雪峰提出的关于继承还是组合的一个判断标准：is关系采用继承，has关系采用组合。

   此外，关于父类和子类：

   所有的class的构造方法，第一行语句必须是调用父类的构造方法。如果没有明确地调用父亲的构造方法，编译器会自动帮我们加一句super()，如果父类没有一个无参的构造方法，就会导致编译失败。

   同时还引出另外一个问题，即子类*不会继承*任何父类的构造方法。子类默认的构造方法是编译器自动生成的，不是继承的。





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



#### Abstract + AOP

```java
@Slf4j
@Aspect
@Component("abstract_service_aspect")
public class AbstractServiceAspect {
    @Pointcut("execution(* com.gatsby.abstractclass.AbstractService.getRawServiceCode())")
    public void getRawAbstractServiceCode() {
        log.info("This is an abstract aop point cut");
    }

    @Before("getRawAbstractServiceCode()")
    public void printRawCodeInAbstractService() {
        System.out.println(this.getClass());
        System.out.println("before, this is a abstract aop");
    }

    @AfterReturning(value = "getRawAbstractServiceCode()", returning = "returnValue")
    public void printRawCodeInAbstractService(JoinPoint jp, Object returnValue) {
        // getSignature用于获取代理类和被代理类的信息
        String name = jp.getSignature().getName();
        System.out.println(name);
        String res = returnValue.toString();
        System.out.println(res);
    }
}
```



#### AbstractClass & Interface

对于抽象类的定义为：包含抽象方法的类就是抽象类，因此，abstract class也是class，内部可以包含普通方法和属性，甚至是构造方法。

1. 但是抽象类不能实例化。
2. 抽象方法不能是private，因为抽象方法需要子类实现，所以至少是protected，默认是public。
3. 抽象方法不能被final和static修饰，因为抽象方法需要被子类重写。
4. 抽象类必须被继承， 并且子类继承之后要重写父类的方法， 否则子类仍然是一个抽象类，必须使用abstract关键词修饰。

> 但普通的类也可以被继承, 普通的方法也可以被重写, 为什么非得用抽象类和抽象方法呢?
>
> 确实如此. 但是使用抽象类相当于多了一重编译器的校验. 使用抽象类的场景就如上面的代码, 实际工作不应该由父类完成, 而应由子类完成. 那么此时如果不小心误用成父类了, 使用普通类编译器是不会报错的. 但是父类是抽象类就会在实例化的时候提示错误, 让我们尽早发现问题
>
> 很多语法存在的意义都是为了 "预防出错", 例如我们曾经用过的 final 也是类似. 创建的变量用户不去修改, 不就相当于常量嘛? 但是加上 final 能够在不小心误修改的时候, 让编译器及时提醒我们.
>
> 充分利用编译器的校验, 在实际开发中是非常有意义的.
>
> 
>
> 作者：凌海儿
> 链接：https://juejin.cn/post/7197986984970879031
> 来源：稀土掘金
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。





#### interface

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

**对比Java 8之后的抽象类和接口：**

接口和抽象类有一些异同点：

1. 接口和抽象类都可以定义方法和属性，方法都可以拥有方法体，但是接口中的有的方法体需要用static或者default修饰，并且接口中的方法只能是public的，抽象类中的方法可以是private或者protected。接口中定义的属性只能是常量并且是静态的，接口的默认修饰符是public static final。
2. 抽象类有构造方法，用于子类访问父类数据的初始化，接口没有构造方法。
3. 抽象类和接口都是不能实例化的，但是都可以通过多态的机制通过子类来实现。
4. 接口可以继承接口，可以单继承也可以多继承，抽象类只能单继承。
5. 接口只能继承接口， 抽象类可以继承抽象类也可以继承普通类。
6. Java类不支持多继承，但是一个类可以实现多个接口。
7. Java中类和类是单继承的，但是接口和接口之间可以多继承，用接口可以达到多继承的目的。接口与接口之间的继承使用extends关键字。



抽象类和接口的核心区别在于：抽象类中可以包含普通方法和普通字段，这样的普通方法和字段可以被子类直接使用（不必重写），而接口中不能包含普通方法，子类必须重写所有的抽象方法。

如果有一些默认的方法实现，那么就是用抽象类。如果需要多重继承，那么只能选用接口方式。

抽象类存在的意义是为了让编译器更好地校验。



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

#### static block

静态块，形式上为“static{...}”，静态块里的内容在类被加载的时候执行，存在方法区（静态区）中，能被线程共享。

类加载就是把需要的类的代码加载到内存中，且在此类首次使用时静态代码块被加载执行，也就是说静态代码块只执行一次。

```java
public class StaticDemo {
    public static void main(String[] args) {
        try {
            Class.forName("org.Demo1");
            Class.forName("org.Demo1");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
}

class Demo {
    static {
        System.out.println("Demo1 static block!");
    }
}
```

输出结果：

```
Demo1 static block!
```

静态代码块的调用：

1. 第一次创建一个对象的时候；
2. 调用该类的静态方法时（静态块代码优先于静态函数先执行）
3. 使用该类的非常量静态字段
4. 使用反射
5. 初始化该类的子类（首先执行父类的静态内容，然后执行子类的静态内容，然后执行父类的构造函数，然后执行子类的构造函数）

[Java 静态块（static块)浅析 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/45107365)



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

   @RequestBody会直接将JSON转为Java对象。



#### validation

```java
@NotEmpty 被注释的字符串不能为Null也不能为空
@NotBlank 被注释的字符串非null，必须包含一个非空白字符
@Null 被注释的元素必须为Null
@NotNull 被注释的元素不徐为Null
@AssertTrue 被注释的元素必须为True
@AssertFalse 被注释的元素必须为false
@Pattern(regex=, flag=) 被注释的元素必须符合指定的正则表达式
@Email 被注释的元素必须是Email格式
@Min(value) 被注释的元素必须是一个数字，其值必须大于等于指定的最小值
@Max(value) 被注释的元素必须是一个数字，其值必须小于等于指定的最大值
@DecimalMin(value) 被注释的元素必须是一个数字，其值必须大于等于指定的最小值
@DecimalMax(value) 被注释的元素必须是一个数字，其值必须小于等于指定的最大值
@Past 被注释的元素必须是一个过去的日期
@Future 被注释的元素必须是一个将来的日期 
```



















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



## Concurrency

### concepts

并发编程产生了三个基本概念：

1. 原子性

   定义：要求保证每个操作的步骤都是原子的，也就是不可以被打断的，要么是全部执行并且全部的执行过程不会被打断，要么是就都没有执行。

   原子性是拒绝多线程操作的。因为同一时刻只能有一个线程在对其进行操作，简而言之，在整个过程中，不会被线程调度器中断的操作，都可以被认为是原子操作。例如对a赋值a=1是一个原子操作，但是a++和++a都不是原子操作。

   简单列举Java中的原子操作：

   1. 基本类型的读取与赋值，并且赋值必须是直接右值赋值，变量之间的相互赋值不是源自操作。
   2. 所有的引用reference的赋值操作
   3. java.concurrent.Atomic.* 包中所有类的一切操作

2. 可见性

   定义：当多个线程同时操作某个变量的时候，一个线程修改了变量的值，其他的线程必须立刻获得修改后的值。

   在多线程环境下，一个线程对共享变量的操作对于其他线程是不可见的。Java通过提供volatile来保证可见性，当一个变量被volatile修饰之后，表示本地线程是无效的，当一个线程修改了整个共享变量之后，会立刻将整个变量更新到主存中，其他线程进行读写。

   当然，synchronize和Lock都可以保证可见性。synchronized和Lock能保证**同一时刻只有一个线程获取锁然后执行同步代码**，并且在释放锁之前会将对变量的修改刷新到主存当中。因此可以保证可见性。

3. 有序性

   定义：程序执行的先后顺序是按照代码的顺序执行的。

   Java内存模型中的有序性可以总结为：如果在本线程内观察，所有操作都是有序的；如果在一个线程中观察另一个线程，所有操作都是无序的。前半句是指“线程内表现为串行语义”，后半句是指“指令重排序”现象和“工作内存主主内存同步延迟”现象。

   在Java内存模型中，为了效率是允许编译器和处理器对指令进行重排序，当然重排序不会影响单线程的运行结果，但是对多线程会有影响。Java提供volatile来保证一定的有序性。最著名的例子就是单例模式里面的DCL（双重检查锁）。另外，可以通过synchronized和Lock来保证有序性，synchronized和Lock保证每个时刻是有一个线程执行同步代码，相当于是让线程顺序执行同步代码，自然就保证了有序性。
   



### volatile

相比`synchronized`这种重型锁，`volatile`是Java提供的一种轻量级的锁，因为它不会引起线程上下文的切换和调度。但是volatile 变量的同步性较差（有时它更简单并且开销更低），而且其使用也更容易出错。

所谓的原子性是指在一次操作或者多次操作中，要么所有的操作全部都得到了执行并且不会受到任何因素的干扰而中断，要么所有的操作都不执行。

在多线程环境下，volatile 关键字可以保证共享数据的可见性，但是并不能保证对数据操作的原子性。也就是说，多线程环境下，使用 volatile 修饰的变量是**线程不安全的**。

要解决这个问题，我们可以使用锁机制，或者使用原子类（如 AtomicInteger）。

这里特别说一下，对任意单个使用 volatile 修饰的变量的读 / 写是具有原子性，但类似于 `flag = !flag` 这种复合操作不具有原子性。简单地说就是，**单纯的赋值操作是原子性的**。

**可见性：**

　　可见性是一种复杂的属性，因为可见性中的错误总是会违背我们的直觉。通常，我们无法确保执行读操作的线程能适时地看到其他线程写入的值，有时甚至是根本不可能的事情。为了确保多个线程之间对内存写入操作的可见性，必须使用同步机制。

　　**可见性，是指线程之间的可见性，一个线程修改的状态对另一个线程是可见的。**也就是一个线程修改的结果。另一个线程马上就能看到。比如：用volatile修饰的变量，就会具有可见性。volatile修饰的变量不允许线程内部缓存和重排序，即直接修改内存。所以对其他线程是可见的。但是这里需要注意一个问题，volatile只能让被他修饰内容具有可见性，但不能保证它具有原子性。比如 volatile int a = 0；之后有一个操作 a++；这个变量a具有可见性，但是a++ 依然是一个非原子操作，也就是这个操作同样存在线程安全问题。

> 现代计算机中，由于 `CPU` 直接从主内存中读取数据的效率不高，所以都会对应的 `CPU` 高速缓存，先将主内存中的数据读取到缓存中，线程修改数据之后首先更新到缓存，之后才会更新到主内存。如果此时还没有将数据更新到主内存其他的线程此时来读，取就是修改之前的数据。
>
> 对应的就是缓存写策略中的写回法，直接将最新的值更新到内存中，但这个过程也并非是原子操作，而是分步骤的。
>
> 使用 `volatile` 关键词修饰的变量每次读取都会得到最新的数据，不管哪个线程对这个变量的修改都会立即刷新到主内存。
>
> `synchronized`和加锁也能能保证可见性，实现原理就是在释放锁之前其余线程是访问不到这个共享变量的。但是和 `volatile` 相比开销较大。

　　在 Java 中 volatile、synchronized 和 final 实现可见性。

**原子性：**

　　**原子是世界上的最小单位，具有不可分割性。**比如 a=0；（a非long和double类型） 这个操作是不可分割的，那么我们说这个操作时原子操作。再比如：a++； 这个操作实际是a = a + 1；是可分割的，所以他不是一个原子操作。非原子操作都会存在线程安全问题，需要我们使用同步技术（sychronized）来让它变成一个原子操作。一个操作是原子操作，那么我们称它具有原子性。java的concurrent包下提供了一些原子类，我们可以通过阅读API来了解这些原子类的用法。比如：AtomicInteger、AtomicLong、AtomicReference等。

　　在 Java 中 synchronized 和在 lock、unlock 中操作保证原子性。

**有序性：**

　　Java 语言提供了 volatile 和 synchronized 两个关键字来保证线程之间操作的有序性，volatile 是因为其本身包含“禁止指令重排序”的语义，synchronized 是由“一个变量在同一个时刻只允许一条线程对其进行 lock 操作”这条规则获得的，此规则决定了持有同一个对象锁的两个同步块只能串行执行。



以下这段代码:

```
int a = 100 ; //1
int b = 200 ; //2
int c = a + b ; //3
```

​    

正常情况下的执行顺序应该是 `1>>2>>3`。但是有时 `JVM` 为了提高整体的效率会进行指令重排导致执行的顺序可能是 `2>>1>>3`。但是 `JVM` 也不能是什么都进行重排，是在保证最终结果和代码顺序执行结果一致的情况下才可能进行重排。

重排在单线程中不会出现问题，但在多线程中会出现数据不一致的问题。

Java 中可以使用 `volatile` 来保证顺序性，`synchronized 和 lock` 也可以来保证有序性，和保证原子性的方式一样，通过同一段时间只能一个线程访问来实现的。

除了通过 `volatile` 关键字显式的保证顺序之外， `JVM` 还通过 `happen-before` 原则来隐式的保证顺序性。

其中有一条就是适用于 `volatile` 关键字的，针对于 `volatile` 关键字的写操作肯定是在读操作之前，也就是说读取的值肯定是最新的。



Java语言提供了一种稍弱的同步机制，即volatile变量，用来确保将变量的更新操作通知到其他线程。当把变量声明为volatile类型后，编译器与运行时都会注意到这个变量是共享的，因此不会将该变量上的操作与其他内存操作一起重排序。volatile变量不会被缓存在寄存器或者对其他处理器不可见的地方，因此在读取volatile类型的变量时总会返回最新写入的值。

volatile 的读性能消耗与普通变量几乎相同，但是写操作稍慢，因为它需要在本地代码中插入许多内存屏障指令来保证处理器不发生乱序执行。



volatile应用

**双重检查锁的单例模式**

可以用 `volatile` 实现一个双重检查锁的单例模式：

```
    public class Singleton {
        private static volatile Singleton singleton;

        private Singleton() {
        }

        public static Singleton getInstance() {
            if (singleton == null) {
                synchronized (Singleton.class) {
                    if (singleton == null) {
                        singleton = new Singleton();
                    }
                }
            }
            return singleton;
        }

    }
```

​    

这里的 `volatile` 关键字主要是为了防止指令重排。 如果不用 `volatile` ，`singleton = new Singleton();`，这段代码其实是分为三步：

- 分配内存空间。(1)
- 初始化对象。(2)
- 将 `singleton` 对象指向分配的内存地址。(3)

加上 `volatile` 是为了让以上的三步操作顺序执行，反之有可能第三步在第二步之前被执行就有可能导致某个线程拿到的单例对象还没有初始化，以致于使用报错。

#### 

**控制停止线程的标记**

```
    private volatile boolean flag ;
    private void run(){
        new Thread(new Runnable() {
            @Override
            public void run() {
                while (flag) {
                    doSomeThing();
                }
            }
        });
    }

    private void stop(){
        flag = false ;
    }
```

​    

这里如果没有用 volatile 来修饰 flag ，就有可能其中一个线程调用了 `stop()`方法修改了 flag 的值并不会立即刷新到主内存中，导致这个循环并不会立即停止。

这里主要利用的是 `volatile` 的内存可见性。

总结一下:

- `volatile` 关键字只能保证可见性，顺序性，**不能保证原子性**。







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

**大部分的开发规范都会要求禁止使用快捷线程池，要求通过标准构造器ThreadPoolExecutor去构造线程池，Executors工厂类创建线程池的快捷工厂方法。**

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



### submit & execute

简单来说，submit和execute的关系在于提交数据的不同

**Callable类型的任务是可以返回执行结果的，而Runnable类型的任务不可以返回执行结果。**

**Runnable和Callable的主要区别为：Callable允许有返回值，Runnable不允许有返回值；Callable允许抛出异常，Runnable不允许抛出异常。**

- submit既可以提交Runnable类型的任务，也可以提交Callable类型的任务，**会有一个类型为Future的返回值**，但当任务类型为Runnable时，返回值为null。
- **execute在执行任务时，如果遇到异常会直接抛出，而submit不会直接抛出**，**只有在使用Future的get方法获取返回值时，才会抛出异常**。



execute(runnable)

```java
package com.javaBase.LineDistancePond;

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

/**
 * 〈一句话功能简述〉;
 * 〈execute与submit的区别〉
 */
public class TestThreadPoolBegin {
    public static void main(String[] args) throws Exception{
        ExecutorService es = Executors.newSingleThreadExecutor();
        Runnable runnable = new Runnable() {
            @Override
            public void run() {
                System.out.println("Runnable线程处理开始...");
                int a = 0;
                int b = 3;
                System.out.println("除以0的结果为：" + b/a);
                System.out.println("Runnable线程处理结束...");
            }
        };
        es.execute(runnable);
        es.shutdown();
    }
}
```

运行结果：

```java
Exception in thread "pool-1-thread-1" java.lang.ArithmeticException: / by zero
Runnable线程处理开始...
    at com.javaBase.LineDistancePond.TestThreadPoolBegin$1.run(TestThreadPoolBegin.java:24)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
    at java.lang.Thread.run(Thread.java:745)

Process finished with exit code 0
```



submit(runnable)

```java
package com.javaBase.LineDistancePond;

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

/**
 * 〈一句话功能简述〉;
 * 〈execute与submit的区别〉
 */
public class TestThreadPoolBegin {

    public static void main(String[] args) throws Exception{
        ExecutorService es = Executors.newSingleThreadExecutor();
        Runnable runnable = new Runnable() {
            @Override
            public void run() {
                System.out.println("Runnable线程处理开始...");
                int a = 0;
                int b = 3;
                System.out.println("除以0的结果为：" + b/a);
                System.out.println("Runnable线程处理结束...");
            }
        };
        es.submit(runnable);
        es.shutdown();
    }
}
```

运行结果：

```
Runnable线程处理开始...

Process finished with exit code 0
```

 

如果get结果：

```java
package com.javaBase.LineDistancePond;

import java.util.concurrent.Callable;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

/**
 * 〈一句话功能简述〉;
 * 〈execute与submit的区别〉
 */
public class TestThreadPoolBegin {

    public static void main(String[] args) throws Exception{
        ExecutorService es = Executors.newSingleThreadExecutor();
        Callable callable = new Callable() {
            @Override
            public Object call() throws Exception {
                System.out.println("线程处理开始...");
                int a = 0;
                int b = 3;
                System.out.println("除以0的结果为：" + b/a);
                System.out.println("线程处理结束...");
                return "0";
            }
        };
        Future<String> future = es.submit(callable);
        System.out.println("任务执行完成，结果为：" + future.get());
    }
}
```

运行结果：

```
Exception in thread "main" java.util.concurrent.ExecutionException: java.lang.ArithmeticException: / by zero
    at java.util.concurrent.FutureTask.report(FutureTask.java:122)
    at java.util.concurrent.FutureTask.get(FutureTask.java:192)
    at com.javaBase.LineDistancePond.TestThreadPoolBegin.main(TestThreadPoolBegin.java:32)
Caused by: java.lang.ArithmeticException: / by zero
    at com.javaBase.LineDistancePond.TestThreadPoolBegin$1.call(TestThreadPoolBegin.java:26)
    at java.util.concurrent.FutureTask.run(FutureTask.java:266)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
    at java.lang.Thread.run(Thread.java:745)
线程处理开始...
```





### ThreadToolUtil

```java
package com.gszq.work.staff.utils;

/**
 * @classname ThreadPoolUtils
 * @description:
 * @author: caijinyang
 * @create: 2022/12/6
 **/

import com.google.common.util.concurrent.ThreadFactoryBuilder;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.*;
import java.util.concurrent.atomic.AtomicInteger;


/**
 * @program: syscore
 * @description: 线程池工具类
 * @author: caijinyang
 * @create: 2022/12/6
 */
public class ThreadPoolUtils {
    private static final Logger log = LoggerFactory.getLogger(ThreadPoolUtils.class);
    /**
     * ExecutorService 线程池
     * 使用方式一  ThreadPoolUtils.submit();
     */
    private static ExecutorService pool = null;

    /*初始化线程池*/
    public static void init() {
        if (pool == null) {
            ThreadFactory namedThreadFactory = new ThreadFactoryBuilder().setNameFormat("thread-call-runner-%d").build();
            pool = new ThreadPoolExecutor(5, 200, 0L, TimeUnit.MILLISECONDS, new LinkedBlockingQueue<Runnable>(1024), namedThreadFactory, new ThreadPoolExecutor.AbortPolicy());
        }
    }

    /**
     * 提交任务执行
     *
     * @param r
     * @return void
     * @throws
     * @author caijinyang
     * @date 2022/12/6
     */
    public static void submit(Runnable r) {
        init();
        pool.execute(r);
    }

    /**
     * 关闭线程池
     *
     * @param
     * @return void
     * @throws
     * @author caijinyang
     * @date 2022/12/6
     */
    public static void unInit() {
        if (pool == null || pool.isShutdown()) {
            return;
        }
        pool.shutdownNow();
        pool = null;
    }

    /**
     * ThreadPoolExecutor-线程池 可记录线程执行详情
     * 使用方式二  ThreadPoolUtils pool = new ThreadPoolUtils(5,5,10, "A业务线程池");
     * pool.execute()
     */
    private ThreadPoolExecutor executor;
    /**
     * 线程工厂
     */
    private CustomThreadFactory threadFactory;
    /**
     * 异步执行结果
     */
    private List<CompletableFuture<Void>> completableFutures;
    /**
     * 拒绝策略
     */
    private CustomAbortPolicy abortPolicy;
    /**
     * 失败数量
     */
    private AtomicInteger failedCount;

    /**
     *
     * @param corePoolSize 核心线程数大小，当线程数< corePoolSize ，会创建线程执行runnable
     * @param maximumPoolSize 最大线程数， 当线程数 >= corePoolSize的时候，会把runnable放入workQueue中
     * @param keepAliveTime  保持存活时间，当线程数大于corePoolSize的空闲线程能保持的最大时间
     * @param unit 时间单位
     * @param queueSize 阻塞队列，存放来不及执行的线程
     *         ArrayBlockingQueue：构造函数一定要传大小
     *         LinkedBlockingQueue：构造函数不传大小会默认为（Integer.MAX_VALUE ），当大量请求任务时，容易造成 内存耗尽。
     *         SynchronousQueue：同步队列，一个没有存储空间的阻塞队列 ，将任务同步交付给工作线程。
     *         PriorityBlockingQueue : 优先队列
     * @param poolName 创建线程的名字
     * @param threadFactory  创建线程的工厂
     * @param handler  拒绝策略
     * @return
     * @author  caijinyang
     * @date    2022/12/6
     * @throws
     */
    public ThreadPoolUtils(int corePoolSize,
                           int maximumPoolSize,
                           int queueSize,
                           String poolName) {
        this.failedCount = new AtomicInteger(0);
        this.abortPolicy = new CustomAbortPolicy();
        this.completableFutures = new ArrayList<>();
        this.threadFactory = new CustomThreadFactory(poolName);
        this.executor = new ThreadPoolExecutor(corePoolSize, maximumPoolSize, 60L, TimeUnit.SECONDS, new ArrayBlockingQueue<>(queueSize), this.threadFactory, abortPolicy);
    }

    /**
     * 执行任务
     *
     * @param runnable
     */
    public void execute(Runnable runnable) {
        CompletableFuture<Void> future = CompletableFuture.runAsync(runnable, executor);
        // 设置好异常情况
        future.exceptionally(e -> {
            failedCount.incrementAndGet();
            log.error("Task Failed..." + e);
            e.printStackTrace();
            return null;
        });
        // 任务结果列表
        completableFutures.add(future);
    }

    /**
     * 执行自定义runnable接口（可省略，只是加了个获取taskName）
     *
     * @param runnable
     */
    public void execute(SimpleTask runnable) {
        CompletableFuture<Void> future = CompletableFuture.runAsync(runnable, executor);
        // 设置好异常情况
        future.exceptionally(e -> {
            failedCount.incrementAndGet();
            log.error("Task [" + runnable.taskName + "] Failed..." + e);
            e.printStackTrace();
            return null;
        });
        // 任务结果列表
        completableFutures.add(future);
    }

    /**
     * 停止线程池
     */
    public void shutdown() {
        executor.shutdown();
        log.debug("************************停止线程池************************");
        log.debug("** 活动线程数：" + executor.getActiveCount() + "\t\t\t\t\t\t\t\t\t\t**");
        log.debug("** 等待任务数：" + executor.getQueue().size() + "\t\t\t\t\t\t\t\t\t\t**");
        log.debug("** 完成任务数：" + executor.getCompletedTaskCount() + "\t\t\t\t\t\t\t\t\t\t**");
        log.debug("** 全部任务数：" + executor.getTaskCount() + "\t\t\t\t\t\t\t\t\t\t**");
        log.debug("** 拒绝任务数：" + abortPolicy.getRejectCount() + "\t\t\t\t\t\t\t\t\t\t**");
        log.debug("** 成功任务数：" + (executor.getCompletedTaskCount() - failedCount.get()) + "\t\t\t\t\t\t\t\t\t\t**");
        log.debug("** 异常任务数：" + failedCount.get() + "\t\t\t\t\t\t\t\t\t\t**");
        log.debug("**********************************************************");
    }

    /**
     * 获取任务执行情况
     * 之所以遍历taskCount数的CompletableFuture，是因为如果有拒绝的任务，相应的CompletableFuture也会放进列表，而这种CompletableFuture调用get方法，是会永远阻塞的。
     *
     * @return
     */
    public boolean getExecuteResult() {
        // 任务数，不包含拒绝的任务
        long taskCount = executor.getTaskCount();
        for (int i = 0; i < taskCount; i++) {
            CompletableFuture<Void> future = completableFutures.get(i);
            try {
                // 获取结果，这个是同步的，目的是获取真实的任务完成情况
                future.get();
            } catch (InterruptedException | ExecutionException e) {
                // log.error("java.util.concurrent.CompletableFuture.get() Failed ..." + e);
                return false;
            }
            // 出现异常，false
            if (future.isCompletedExceptionally()) {
                return false;
            }
        }
        return true;
    }

    /**
     * 线程工厂
     */
    private static class CustomThreadFactory implements ThreadFactory {
        private String poolName;
        private AtomicInteger count;

        private CustomThreadFactory(String poolName) {
            this.poolName = poolName;
            this.count = new AtomicInteger(0);
        }

        @Override
        public Thread newThread(Runnable r) {
            Thread thread = new Thread(r);
            // 线程名，利于排查
            thread.setName(poolName + "-[线程" + count.incrementAndGet() + "]");
            return thread;
        }
    }

    /**
     * 自定义拒绝策略
     */
    private static class CustomAbortPolicy implements RejectedExecutionHandler {
        /**
         * 拒绝的任务数
         */
        private AtomicInteger rejectCount;

        private CustomAbortPolicy() {
            this.rejectCount = new AtomicInteger(0);
        }

        private AtomicInteger getRejectCount() {
            return rejectCount;
        }

        /**
         * 这个方法，如果不抛异常，则执行此任务的线程会一直阻塞
         *
         * @param r
         * @param e
         */
        public void rejectedExecution(Runnable r, ThreadPoolExecutor e) {
            log.error("Task " + r.toString() +
                    " rejected from " +
                    e.toString() + " 累计：" + rejectCount.incrementAndGet());
        }
    }

    /**
     * 只是加了个taskName，可自行实现更加复杂的逻辑
     */
    public abstract static class SimpleTask implements Runnable {
        /**
         * 任务名称
         */
        private String taskName;

        public void setTaskName(String taskName) {
            this.taskName = taskName;
        }
    }
}


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



### Queue





### Deque

Deque是指双端队列，含义是Double End Queue，即双端队列，既可以当作栈使用，也可以当作队列使用。





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



### StringBuilder & StringBuffer

在使用StringBuffer类时，每次都会对StringBuffer对象本身进行操作，**而不是生成新的对象**，所以如果需要对字符串进行修改推荐使用StringBuffer。

StringBuilder类在Java 5中被提出，它和StringBuffer之间的最大不同在于StringBuilder的方法不是线程安全的（不能同步访问）。

由于StringBuilder相较于StringBuffer有速度优势，所以多数情况下建议使用StringBuilder类。

```java
public class RunoobTest{
    public static void main(String args[]){
        StringBuilder sb = new StringBuilder(10);
        sb.append("Runoob..");
        System.out.println(sb);  
        sb.append("!");
        System.out.println(sb); 
        sb.insert(8, "Java");
        System.out.println(sb); 
        sb.delete(5,8);
        System.out.println(sb);  
    }
}
```

<img src="https://www.runoob.com/wp-content/uploads/2013/12/2021-03-01-java-stringbuffer.svg" alt="img" style="zoom: 80%;" />

[StringBuilder - 廖雪峰的官方网站 (liaoxuefeng.com)](https://www.liaoxuefeng.com/wiki/1252599548343744/1260471862687712)

考察下面的循环代码：

```
String s = "";
for (int i = 0; i < 1000; i++) {
    s = s + "," + i;
}
```

虽然可以直接拼接字符串，但是，在循环中，每次循环都会创建新的字符串对象，然后扔掉旧的字符串。这样，绝大部分字符串都是临时对象，不但浪费内存，还会影响GC效率。

为了能高效拼接字符串，Java标准库提供了`StringBuilder`，它是一个可变对象，可以预分配缓冲区，这样，往`StringBuilder`中新增字符时，不会创建新的临时对象：

```
StringBuilder sb = new StringBuilder(1024);
for (int i = 0; i < 1000; i++) {
    sb.append(',');
    sb.append(i);
}
String s = sb.toString();
```

`StringBuilder`还可以进行链式操作：

```java
public class Main {
    public static void main(String[] args) {
        var sb = new StringBuilder(1024);
        sb.append("Mr ")
          .append("Bob")
          .append("!")
          .insert(0, "Hello, ");
        System.out.println(sb.toString());
    }
}
```

如果我们查看`StringBuilder`的源码，可以发现，进行链式操作的关键是，定义的`append()`方法会返回`this`，这样，就可以不断调用自身的其他方法。

注意：对于普通的字符串`+`操作，并不需要我们将其改写为`StringBuilder`，因为Java编译器在编译时就自动把多个连续的`+`操作编码为`StringConcatFactory`的操作。在运行期，`StringConcatFactory`会自动把字符串连接操作优化为数组复制或者`StringBuilder`操作。

你可能还听说过`StringBuffer`，这是Java早期的一个`StringBuilder`的线程安全版本，它通过同步来保证多个线程操作`StringBuffer`也是安全的，但是同步会带来执行速度的下降。

`StringBuilder`和`StringBuffer`接口完全相同，现在完全没有必要使用`StringBuffer`。



### String常用操作(API)

[总结 | Java字符串常用操作(方法)](https://blog.csdn.net/Cherils/article/details/105889084)

1. String对象是一个final对象，一旦创建了一个String对象，值就不能再次改变。

   如果想要对String改变，需要使用StringBuilder或者StringBuffer。

   ```java 
   // 直接创建的方式
   String s = "hello world!";
   // 使用一个字符数组创建
   char[] ch = {'h', 'e', 'l', 'l', 'o'};
   String s = new String(ch);
   ```

   String的内部存储是一个char的数组。

2. 字符串长度

   ```java 
   int len = s.length();
   ```

3. 字符串拼接

   ```java
   // String对象的拼接
   s1.concat(s2);
   ```

4. 字符串查找

   int indexOf(String s)：字符串s在指定字符串中**首次**出现的索引位置，如果没有检索到字符串s，该方法返回-1
   int lastIndexOf(String s)：字符串s在指定字符串中**最后一次**出现的索引位置，如果没有检索到字符串s，该方法返回-1；如果s是空字符串，则返回的结果与length方法的返回结果相同，即返回整个字符串的长度。

   ```java
   int index = s.indexOf(sub);
   int index = s.lastIndexOf(sub);
   ```

5. 获取指定未知的字符串

   ```java 
   String s = "abdc";
   char c = s.charAt(3);
   ```

6. 获取子串

   String substring()方法：实现截取字符串，利用字符串的下标索引来截取(字符串的下标是从0开始的，在字符串中空格占用一个索引位置)

   - substring(int beginIndex)：截取从指定索引位置开始到字符串结尾的子串
   - substring(int beginIndex, int endIndex)：从beginIndex开始，**到endIndex结束(不包括endIndex)**

   ```java 
   String str = "abcde";
   String substr1 = str.substring(2); //substr1为"cde"
   String substr2 = str.substring(2,4); //substr2为"cd"
   ```

7. 去除字符串首尾的空格

   ```java
   String str = " abcd ";
   String strTrim = str.trim(); // "abcd"
   ```

8. 字符串替换

   - String replace(char oldChar, char newChar)：将指定的字符/字符串oldchar全部替换成新的字符/字符串newChar

   - String replaceAll(String regex, String replacement)：使用给定的参数 replacement替换字符串所有匹配给定的正则表达式的子字符串

   - String replaceFirst(String regex, String replacement)：使用给定replacement 替换此字符串匹配给定的正则表达式的第一个子字符串

     regex是正则表达式，替换成功返回替换的字符串，替换失败返回原字符串

   ```java
   String str = "abcde";
   String newstr = str.replace("a","A"); //newstr为"Abcde"
   ```

9. 字符串相等

   boolean equals(Object anObject)：将此字符串与指定的对象比较，区分大小写
   boolean equalsIgnoreCase(String anotherString)：将此 String 与另一个 String 比较，不考虑大小写

   ```java
   String str1 = "abcde";
   String str2 = str1;//字符串str1和str2都是一个字符串对象
   String str3 = "ABCDE";
   boolean isEqualed = str1.equals(str2);//返回true
   boolean isEqualed = str1.equals(str3);//返回false
   boolean isEqualed = str1.equlasIgnoreCase(str3);//返回true
   ```

10. 比较两个字符串

    int compareTo(Object o)：把这个字符串和另一个对象比较。
    int compareTo(String anotherString)：按字典顺序比较两个字符串。
    比较对应字符的大小(ASCII码顺序)，如果参数字符串等于此字符串，则返回值 0；如果此字符串小于字符串参数，则返回一个小于 0 的值；如果此字符串大于字符串参数，则返回一个大于 0 的值。

11. 字符串转换

    ```java 
    // String转int
    //第一种
    int i = Integer.parseInt(String str)
    int i = Integer.valueOf(s).intValue();
    
    // String转long
    long l = Long.parseLong(String str);
    
    // String转double
    double d = Double.parseDouble(str);
    double d = Double.valueOf(str);
    ```
    
12. 字符串大小写转换

    String toLowerCase()：将字符串中的所有字符从大写字母改写为小写字母
    String toUpperCase()：将字符串中的所有字符从小写字母改写为大写字母

    ```java
    String str1 = "abcde";
    String str2 = str1.toUpperCase(); //str2 = "ABCDE";
    String str3 = str2.toLowerCase(); //str3 = "abcde";
    ```

13. 字符串分割
    
    String[] split()：根据匹配给定的正则表达式来拆分字符串，将分割后的结果存入字符数组中。
    
    ```java 
    String str = "Hello World A.B.C"
    String[] res = str.split(" ");//res = {"Hello","World","A.B.C"}
    String[] res = str.split(" ",2);//res = {"Hello","World A.B.C"}
    String[] res = str.split("\\.");//res = {"Hello World A","B","C"}
    
    String str = "A=1 and B=2 or C=3"
    String[] res = str.split("and|or");//res = {"A=1 "," B=2 "," C=3"}
    ```

14. 字符串和byte[]的转换

    byte[] getBytes()

    1. byte[] getBytes()：使用平台的默认字符集将此 String 编码为 byte 序列，并将结果存储到一个新的 byte 数组中。
    2. byte[] getBytes(String charsetName)：使用指定的字符集将此 String 编码为 byte 序列，并将结果存储到一个新的 byte 数组中。

    ```java
    byte[] str = s.getBytes();
    ```



## I/O

### File

首先，想要了解Java中的处理文件的思路和类的结构，**首先要有一个二进制的思维。**所有文件，不论是可执行文件、图片文件、视频文件、Word文件、压缩文件、txt文件，都没什么可神秘的，它们都是以0和1的二进制形式保存的。我们所看到的图片、视频、文本，都是应用程序对这些二进制的解析结果。

虽然所有的数据都是以二进制保存的，但是高级语言为了方便处理数据，引入了数据类型的概念。

#### File System

文件系统也是类似，虽然所有的文件都是以二进制的形式进行存储，但是文件也有文件类型的概念。文件类型通常以扩展名的形式体现，例如pdf文件的扩展名为.pdf等等。

**文件类型可以粗略分为两种类型，一类是文本文件，一种是二进制文件。**

文本文件的例子有普通的文本txt文件、程序源代码文件.java文件、HTML文件等等；二进制文件的例子有压缩文件zip、pdf文件、MP3文件、excel文件等等。

文件系统在不同的操作系统上也是不同的，Windows可以有多个逻辑盘，C盘D盘等，每个盘都可以被格式化为一种不同的文件系统，常见的文件系统有FAT32和NTFS。在Linux中，只有一个逻辑的根目录，用`/`表示。Linux支持不同的文件系统，常见的有`Ext2/Ext3/Ext4`。

在逻辑上，Windows有多个根目录，Linux中有一个根目录，每个根目录瞎有一颗子目录和文件构成的树，每个文件都有文件路径的概念，**路径有两种形式：一种是绝对路径，一种是相对路径。**

大部分文件系统的文件和目录具有访问权限的概念，对所有者、用户组可以有不同的权限，具体权限包括读、写、执行。

文件名有大小写是否敏感的概念。在Windows系统中，一般是大小写不敏感的，而Linux则一般是大小写敏感的。也就是说，同一个目录下，abc.txt和ABC.txt在Windows中被视为同一个文件，而在Linux中则被视为不同的文件。

操作系统中有一个临时文件的概念。临时文件位于一个特定目录，比如Windows 7中，临时文件一般位于“C:\Users\用户名\AppData\Local\Temp”; Linux系统中，临时文件位于/tmp。

操作系统会有一定的策略自动清理不用的临时文件。临时文件一般不是用户手工创建的，而是应用程序产生的，用于临时目的。



#### File Read/Write

文件都是存储在硬盘上的，程序处理文件需要将文件读入内存，修改后，需要写回硬盘，操作系统提供了对文件读写的基本API。Java封装了操作系统的功能，提供了统一的API。

文件读写之前，有两个基本常识是显而易见的：

1. **硬盘的读写速度，也就是访问延时，相比内存而言，是很慢的。**

2. **一般读写文件需要两次数据复制。比如读文件，需要先从硬盘复制到操作系统内核，再从内核复制到应用程序分配的内存中。**

   **这是因为操作系统的环境和应用程序的环境是不一样的，操作系统所在的环境是内核态，但是应用程序的环境是用户态。应用程序调用操作系统的功能，需要两次环境的切换，先从用户态切换到内核态，再从内核态切换到用户态。**

   **这种用户态和内核态的切换无疑是有开销的，应该尽量减少这种切换。**

基于以上两个基本常识，应用程序经常使用一种常见的策略，**使用缓冲区**。如果预期后面还需要对该文件读写，那么就第一次多读取一些内容存放到缓冲区，然后下次读写的时候就可以直接在缓冲区读写，减少访问操作系统环境的次数。



#### Java File

**Java中处理文件有一些基本概念和类，包括流、装饰器设计模式、Reader/Writer、随机读写文件、File、NIO、序列化和反序列化。**

在计算机系统中，文件是一个很重要的存储方式，Java的标准库java.io提供了File对象来操作文件和目录。

要构造一个File对象，需要传入文件的路径。

```java 
import java.io.*;

public class Main {
    public static void main(String[] args) {
        File f = new File("C:\\Windows\\notepad.exe");
        System.out.println(f);
    }
}
```

构造File对象的时候，可以传入绝对路径，也可以传入相对路径。

绝对路径是以根目录开头的完整路径，例如：

```Java
File f = new File("C:\\Windows\\notepad.exe");
```

注意Windows平台使用`\`作为路径分隔符，在Java字符串中需要用\\\表示一个 \\。Linux平台使用`/`作为路径分隔符。

File对象有三种形式表示路径，一种是`getPath()`，将会返回构造方法传入的路径，

一种是`getAbsolutePath()`，返回绝对路径，

一种是`getCanonicalPath()`，返回规范路径。



### Stream

在很多编程语言中，文件都不是单独处理的，而是视为一种输入输出设备（IO），Java使用基本统一的概念处理所有的IO，包括键盘，显示终端、网络等等。

这个统一的概念就是流，流有**输入流和输出流**之分。输入流就是可以从中获取数据，输入流的实际提供者可以是键盘、文件、网络等；输出流就是可以向其中写入数据，输出流的实际目的地可以是显示终端、文件、网络等。

Java IO的基本类大多位于包java.io中。类InputStream表示输入流，OutputStream表示输出流，而FileInputStream表示文件输入流，FileOutputStream表示文件输出流。



#### InputStream

InputStream是Java提供的最基础的输入流，位于java.io包中，这个包中提供了所有同步io的功能。

InputStream是一个抽象类，最主要的方法就是read()；这个方法会读取输入流的下一个字节，并返回字节表示的`int`值（0~255）。如果已读到末尾，返回`-1`表示不能继续读取了。

```java
public abstract int read() throws IOException;

public int read(byte b[]) throws IOException {
    return read(b, 0, b.length);
}

public int read(byte b[], int off, int len) throws IOException

private static void readFile(String filePath) throws IOException {
    InputStream inputStream = new FileInputStream(filePath);
    for (; ; ) {
        int n = inputStream.read();
        if (n == -1){
            break;
        }
        System.out.println(n);
    }
    inputStream.close();
}

// 输出
35
32
74
97
118
97
67
111
114
101
84
101
99
104
13
10
74
97
118
97
230
160
184
229
191
131
230
138
128
230
156
175
13
10
```

不管read是否抛出了异常，都应该调用close方法，所以close方法通常应该放在finally语句中，close方法自身有可能也会抛出异常，但是通常可以被捕获并忽略。

```java
String path = "README.md";
InputStream inputStream = null;
try {
    inputStream = new FileInputStream(path);
} catch (Exception e) {
    e.printStackTrace();
} finally {
    try {
        inputStream.close();
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```

InputStrea和OutputStream都是通过close()方法关闭流，关闭流释放相对应的底层资源。

用`try ... finally`来编写上述代码会感觉比较复杂，更好的写法是利用Java 7引入的新的`try(resource)`的语法，只需要编写`try`语句，让编译器自动为我们关闭资源。推荐的写法如下：

```java
public void readFile() throws IOException {
    try (InputStream input = new FileInputStream("src/readme.txt")) {
        int n;
        while ((n = input.read()) != -1) {
            System.out.println(n);
        }
    } // 编译器在此自动为我们写入finally并调用close()
}
```

实际上，编译器并不会特别地为`InputStream`加上自动关闭。编译器只看`try(resource = ...)`中的对象是否实现了`java.lang.AutoCloseable`接口，如果实现了，就自动加上`finally`语句并调用`close()`方法。`InputStream`和`OutputStream`都实现了这个接口，因此，都可以用在`try(resource)`中。

```java
private static void readFileBuffer(String filePath) throws IOException, FileNotFoundException {
    try (InputStream inputStream = new FileInputStream(filePath)) {
        byte[] buffer = new byte[1000];
        int n;
        while ((n = inputStream.read(buffer)) != -1) {
            System.out.println("read "+ n + " byte.");
        }
    }
}
// 输出，表明是一个大小为2.167KB的文件
read 1000 byte.
read 1000 byte.
read 167 byte.
```



**阻塞**

```java
int n;
n = input.read(); // 必须等待read()方法返回才能执行下一行代码
int m = n;
```

因为读取IO相比执行普通代码，速度会慢很多，因此无法确定read()方法调用到底需要花费多少时间。



#### InputStream implements

`FileInputStream`可以从文件获取输入流，这是`InputStream`常用的一个实现类，此外，`ByteArrayInputStream`可以在内存中模拟一个`InputStream`，

```java
private static void byteArrayInputStream() throws IOException {
    byte[] data = {72, 101, 108, 108, 111, 33};
    try (InputStream input = new ByteArrayInputStream(data)) {
        int n;
        while ((n = input.read()) != -1) {
            System.out.println((char) n);
        }
    }
}
// 输出
H
e
l
l
o
!
```

同理，如果是一个String类型的文件，例如txt文件，写法也是类似的：

```java
public static void readTxt(String filePath) throws IOException {
    String s;
    try (InputStream input = new FileInputStream(filePath)) {
        int n;
        StringBuilder sb = new StringBuilder();
        while ((n = input.read()) != -1) {
            sb.append((char) n);
        }
        s = sb.toString();
    }
    System.out.println(s);
}
```



#### OutputStream

和InputStream相反，OutputStream使用来作为最基础的输出流。

和InputStream类似，OutputStream也是抽象类，是所有的抽象类的超类，这个类定义的最主要的方法是`void write(int b)`，

```java
public abstract void write(int b) throws IOException;
```

这个方法会写入一个字节到输出流。要注意的是，虽然传入的是`int`参数，但只会写入一个字节，即只写入`int`最低8位表示字节的部分（相当于`b & 0xff`）。

和`InputStream`类似，`OutputStream`也提供了`close()`方法关闭输出流，以便释放系统资源。要特别注意：`OutputStream`还提供了一个`flush()`方法，它的目的是将缓冲区的内容真正输出到目的地。

>为什么需要flush？
>
>因为向磁盘、网络写入数据的时候，出于效率的考虑，操作系统并不是输出一个字节就立刻写入到文件或者发送到网络，而是把输出的字节先放到内存的一个缓冲区里（本质上就是一个`byte[]`数组），等到缓冲区写满了，再一次性写入文件或者网络。对于很多IO设备来说，一次写一个字节和一次写1000个字节，花费的时间几乎是完全一样的，所以`OutputStream`有个`flush()`方法，能强制把缓冲区内容输出。

通常情况下，我们不需要调用这个`flush()`方法，因为缓冲区写满了`OutputStream`会自动调用它，并且，在调用`close()`方法关闭`OutputStream`之前，也会自动调用`flush()`方法。

>小明正在开发一款在线聊天软件，当用户输入一句话后，就通过`OutputStream`的`write()`方法写入网络流。小明测试的时候发现，发送方输入后，接收方根本收不到任何信息，怎么肥四？
>
>原因就在于写入网络流是先写入内存缓冲区，等缓冲区满了才会一次性发送到网络。如果缓冲区大小是4K，则发送方要敲几千个字符后，操作系统才会把缓冲区的内容发送出去，这个时候，接收方会一次性收到大量消息。
>
>解决办法就是每输入一句话后，立刻调用`flush()`，不管当前缓冲区是否已满，强迫操作系统把缓冲区的内容立刻发送出去。
>
>实际上，`InputStream`也有缓冲区。例如，从`FileInputStream`读取一个字节时，操作系统往往会一次性读取若干字节到缓冲区，并维护一个指针指向未读的缓冲区。然后，每次我们调用`int read()`读取下一个字节时，可以直接返回缓冲区的下一个字节，避免每次读一个字节都导致IO操作。当缓冲区全部读完后继续调用`read()`，则会触发操作系统的下一次读取并再次填满缓冲区。



#### OutputStream implements

以FileOutputStream为例子，演示将字节写入文件流：

```java
public static void writeFile(String filePath) throws IOException {
    outputStream = new FileOutputStream(filePath);
    outputStream.write(72); // H
    outputStream.write(101); // e
    outputStream.write(108); // l
    outputStream.write(108); // l
    outputStream.write(111); // o
    outputStream.close();
}
```

写入之后，可以调用InputStreamTest中的读取String类的文件的方法，

```java 
public static void main(String[] args) throws IOException {
    String filePath = "out.txt";
    writeFile(filePath);
    InputStreamTest inputStreamTest = new InputStreamTest();
    inputStreamTest.readFileBuffer(filePath);
    inputStreamTest.readFile(filePath);
    inputStreamTest.readTxt(filePath);
}
// 输出
read 5 byte. // 文件中一共5字节
72
101
108
108
111
Hello // 将二进制流转成char再拼接成String
```

每次写入一个字节非常麻烦，更常见的方法是一次性写入若干字节。这时，可以用`OutputStream`提供的重载方法`void write(byte[])`来实现：

```java
public void writeFile(String filePath) throws IOException {
    OutputStream output = new FileOutputStream(filePath);
    output.write("Hello".getBytes("UTF-8")); // Hello
    output.close();
}
```

和`InputStream`一样，上述代码没有考虑到在发生异常的情况下如何正确地关闭资源。写入过程也会经常发生IO错误，例如，磁盘已满，无权限写入等等。我们需要用`try(resource)`来保证`OutputStream`在无论是否发生IO错误的时候都能够正确地关闭：

```java
public void writeFile() throws IOException {
    try (OutputStream output = new FileOutputStream("out/readme.txt")) {
        output.write("Hello".getBytes("UTF-8")); // Hello
    } // 编译器在此自动为我们写入finally并调用close()
}
```





## JDBC

程序运行的时候，数据都是在内存中的。当程序终止的时候，通常都需要将数据保存到磁盘上，无论是保存到本地磁盘，还是通过网络保存到服务器上，最终都会将数据写入磁盘文件。

> 涉及到MySQL的安装参见《Database.md》-Linux(CentOS 7)安装MySQL

JDBC的全称是Java DataBase Connectivity。

```java
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.47</version>
    <scope>runtime</scope>
</dependency>
```





## Generic

泛型，即参数化类型，顾名思义，就是将类型由原来的具体的类型参数化，类似于方法中的变量参数，此时类型也定义成参数形式（可以称之为类型形参），然后在使用/调用时传入具体的类型（类型实参）。

泛型的本质是为了参数化类型（在不创建新的类型的情况下，通过泛型指定的不同类型来控制形参具体限制的类型）。也就是说在泛型使用过程中，操作的数据类型被指定为一个参数，这种参数类型可以用在类、接口和方法中，分别被称为泛型类、泛型接口、泛型方法。



```java 
List arrayList = new ArrayList();
arrayList.add("aaaa");
arrayList.add(100);

for(int i = 0; i< arrayList.size();i++){
    String item = (String)arrayList.get(i);
    Log.d("泛型测试","item = " + item);
} // 程序崩溃
```



```java 
List<String> stringArrayList = new ArrayList<String>();
List<Integer> integerArrayList = new ArrayList<Integer>();

Class classStringArrayList = stringArrayList.getClass();
Class classIntegerArrayList = integerArrayList.getClass();

if(classStringArrayList.equals(classIntegerArrayList)){
    Log.d("泛型测试","类型相同");
} // 泛型测试，类型相同
```

通过上面的例子可以证明，在编译之后程序会采取去泛型化的措施。也就是说Java中的泛型，只在编译阶段有效。在编译过程中，正确检验泛型结果后，会将泛型的相关信息擦出，并且在对象进入和离开方法的边界处添加类型检查和类型转换的方法。也就是说，泛型信息不会进入到运行时阶段。

对此总结成一句话：泛型类型在逻辑上看以看成是多个不同的类型，实际上都是相同的基本类型。

### generic class

```java 
public class Pair<T> {
    private T first;
    private T second;

    public Pair() {
        first = null;
        second = null;
    }

    public Pair(T first, T second) {
        this.first = first;
        this.second = second;
    }

    public T getFirst() {
        return this.first;
    }

    public T getSecond() {
        return this.second;
    }

    public void setFirst(T newValue) {
        this.first = newValue;
    }

    public void setSecond(T newValue) {
        this.second = newValue;
    }
}
```

PairTest.java

```java 
class ArrayAlg {
    public static Pair<String> sort(String[] a) {
        if (a == null || a.length == 0) return null;

        String min = a[0];
        String max = a[0];

        for (int i = 1; i < a.length; ++i) {
            if (min.compareTo(a[i]) > 0) min = a[i];
            if (max.compareTo(a[i]) < 0) max = a[i];
        }
        return new Pair<>(min, max);
    }
}

public class PairTest {
    public static void main(String[] args) {
        String[] words = {
                "mary", "had", "a", "little", "lamb"
        };

        Pair<String> mm = ArrayAlg.sort(words);
        System.out.println("min = " + mm.getFirst());
        System.out.println("max = " + mm.getSecond());
    }
}
```



### generic method

```java
class ArrayAlg {
    public static <T> T getMiddle(T...a) {
        return a[a.length/2];
    }
}
```















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

[@Pointcut 的 12 种用法，你知道几种？ - 腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1655923)





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





## Garbage Collection

垃圾回收（Garbage Collection，GC），顾名思义就是释放垃圾占用的空间，防止内存泄露。有效的使用可以使用的内存，对内存堆中已经死亡的或者长时间没有使用的对象进行清除和回收。

[咱们从头到尾说一次 Java 垃圾回收 ](https://zhuanlan.zhihu.com/p/73628158)

### Reachability Counting

引用计数算法（RC）是通过在对象头中分配一个空间来保存该对象被引用的次数（Reference Count）。

如果该对象被其它对象引用，则它的引用计数加1，如果删除对该对象的引用，那么它的引用计数就减1，当该对象的引用计数为0时，那么该对象就会被回收。

`String m = new String("Jack");`S

先创建一个字符串，这时候"jack"有一个引用，就是 m。

此时如果设置``m = null`，那么Jack的引用就变成了0，意味着这个空间可以被回收了。

**引用计数算法的优点**：

1. 实现简单,垃圾对象便于辨识;判定效率高,回收没有延迟性。

**引用计数算法的缺点**：

1. 它需要单独的字段存储计数器,这样的做法增加了`存储空间的开销`。
2. 每次复制都需要更新计数器,伴随着加法和减法操作,这增加了`时间开销`。
3. 引用计数器有一个严重的问题,即`无法处理循环引用`的情况。这是一条致命缺陷,导致在Java的垃圾回收器中没有使用这类算法。

主要的原因是因为Java会存在循环引用的问题，引用计数没有办法排除这种问题。



### Reachability Analysis

可达性分析算法（Reachability Analysis）的基本思路是，通过一些被称为引用链（GC Roots）的对象作为起点，从这些节点开始向下搜索，搜索走过的路径被称为（Reference Chain)，当一个对象到 GC Roots 没有任何引用链相连时（即从 GC Roots 节点到该节点不可达），则证明该对象是不可用的。



### GC root

在Java中，可以作为GC root的对象有四种类型

1. 虚拟机栈（栈帧中的本地变量表）中引用的对象

   ```java
   public class StackLocalParameter {
   	public StackLocalParameter(String name){
           
       }
   }
   
   public static void testGC(){
   	StackLocalParameter s = new StackLocalParameter("localParameter");
   	s = null;
   }
   ```

   此时的s即为GC root，当s设为null之后，GC root和StackLocalParameter的引用链被切断，将被JVM回收。

2. 方法区中类静态属性引用的对象

   s为GC Root，s置为null，经过GC后，s所指向的properties对象由于无法与GC Root建立关系被回收。

   而m作为类的静态属性，也属于GC Root，parameter对象依然与GC root建立着连接，所以此时parameter对象并不会被回收。

   ```java 
   public class MethodAreaStaicProperties {
   	public static MethodAreaStaicProperties m;
   	public MethodAreaStaicProperties(String name){
           
       }
   }
   
   public static void testGC(){
   	MethodAreaStaicProperties s = new MethodAreaStaicProperties("properties");
   	s.m = new MethodAreaStaicProperties("parameter");
   	s = null;
   }
   
   ```

3. 方法区常量引用的对象

   m即为方法区中的常量引用，也为GC Root，s置为 null后，final对象也不会因没有与GC Root建立联系而被回收。

   ```java
   public class MethodAreaStaicProperties {
   	public static final MethodAreaStaicProperties m = MethodAreaStaicProperties("final");
   	public MethodAreaStaicProperties(String name){
           
       }
   }
   
   public static void testGC(){
   	MethodAreaStaicProperties s = new MethodAreaStaicProperties("staticProperties");
   	s = null;
   }
   ```

4. 本地方法栈中JNI（即一般说的Native方法）引用的对象

   任何 native 接口都会使用某种本地方法栈，实现的本地方法接口是使用 C 连接模型的话，那么它的本地方法栈就是 C 栈。当线程调用 Java 方法时，虚拟机会创建一个新的栈帧并压入 Java 栈。然而当它调用的是本地方法时，[虚拟机](https://www.zhihu.com/search?q=虚拟机&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"73628158"})会保持 Java 栈不变，不再在线程的 Java 栈中压入新的帧，虚拟机只是简单地动态连接并直接调用指定的本地方法。



### GC algorithms

1. 标记-清除算法（Mark-Swap）是最基础的一种垃圾回收算法，分为两部分，先将内存中的可回收的对象进行标记，然后将这些垃圾拎出来清理掉，就像上图一样，清理掉的垃圾就变成未使用的内存区域，等待被再次使用。

   但是标记-清除算法会存在一个问题就在于，内存会被切割为很多小块，碎片化的内存是没有办法使用的。

2. 复制算法（Copying）是在标记清除算法上演化而来，能够保证碎片化内存的问题可以得到解决，内存可以变成整段连续的。

​		但是问题也很明显，为了照顾需要复制的内存，必须要预留一大块内存，因此可以利用的内存实际上大约就只有总内存的一半左右。

3. 标记整理算法（Mark-Compact）标记过程仍然与标记-清除算法一样，但后续步骤不是直接对可回收对象进行清理，而是让所有存活的对象都向一端移动，再清理掉端边界以外的内存区域。

   标记整理算法一方面在标记-清除算法上做了升级，解决了内存碎片的问题，也规避了复制算法只能利用一半内存区域的弊端。但是它对内存变动更频繁，需要整理所有存活对象的引用地址，在效率上比复制算法要差很多。

分代收集算法分代收集算法（Generational Collection）严格来说并不是一种思想或理论，而是融合上述3种基础的算法思想，而产生的针对不同情况所采用不同算法的一套组合拳。对象存活周期的不同将内存划分为几块。

**一般是把 Java 堆分为新生代和老年代，这样就可以根据各个年代的特点采用最适当的收集算法。**在新生代中，每次垃圾收集时都发现有大批对象死去，只有少量存活，那就选用复制算法，只需要付出少量存活对象的复制成本就可以完成收集。而老年代中因为对象存活率高、没有额外空间对它进行分配担保，就必须使用标记-清理或者标记 --- 整理算法来进行回收。

![img](https://pic4.zhimg.com/v2-2dcba4d81807c67df8309aa181bb00c3_b.jpg)

堆空间 = 新生代(1/3)+老年代(2/3)

新生代 = eden(8/10) + From(1/10) + To(1/10)

>Java 堆（Java Heap）是JVM所管理的内存中最大的一块，堆又是GC管理的主要区域，主要分析一下 Java 堆的结构。
>
>Java 堆主要分为2个区域-年轻代与老年代，其中年轻代又分 Eden 区和 Survivor 区，其中 Survivor 区又分 From 和 To 2个区。可能这时候大家会有疑问，为什么需要 Survivor 区，为什么Survivor 还要分2个区。



**Eden区**

大多数情况下，对象会在新生代 Eden 区中进行分配，当 Eden 区没有足够空间进行分配时，虚拟机会发起一次 Minor GC，Minor GC 相比 Major GC 更频繁，回收速度也更快。

通过 Minor GC 之后，Eden 会被清空，Eden 区中绝大部分对象会被回收，而那些无需回收的存活对象，将会进到 Survivor 的 From 区（若 From 区不够，则直接进入 Old 区）。



**Survivor区**

Survivor 区相当于是 Eden 区和 Old 区的一个缓冲，类似于我们交通灯中的黄灯。Survivor 又分为2个区，一个是 From 区，一个是 To 区。每次执行 Minor GC，会将 Eden 区和 From 存活的对象放到 Survivor 的 To 区（如果 To 区不够，则直接进入 Old 区）。

1. 为什么需要survivor?

   survivor的存在的意义在于减少被送到老年代的对象，进而减少MajorGC的发生，Survivor的预筛选保证，只有经历16次Minor GC还在Eden中存活的对象才有资格进入Old。

2. 为什么需要两个Survivor？

   解决内存碎片化。

   Minor GC 执行后，Eden 区被清空了，存活的对象放到了 Survivor 区，而之前 Survivor 区中的对象，可能也有一些是需要被清除的。问题来了，这时候我们怎么清除它们？在这种场景下，我们只能标记清除，而我们知道标记清除最大的问题就是内存碎片，在新生代这种经常会消亡的区域，采用标记清除必然会让内存产生严重的碎片化。因为 Survivor 有2个区域，所以每次 Minor GC，会将之前 Eden 区和 From 区中的存活对象复制到 To 区域。第二次 Minor GC 时，From 与 To 职责兑换，这时候会将 Eden 区和 To 区中的存活对象再复制到 From 区域，以此反复。

   这种机制最大的好处就是，整个过程中，永远有一个 Survivor space 是空的，另一个非空的 Survivor space 是无碎片的。那么，Survivor 为什么不分更多块呢？比方说分成三个、四个、五个?显然，如果 Survivor 区再细分下去，每一块的空间就会比较小，容易导致 Survivor 区满，两块 Survivor 区可能是经过权衡之后的最佳方案。

   **概括来说就是在局部应用了Copying算法，来保证经常发生回收的Eden区域不会产生大量碎片。**

   

**Old区**

老年代占据着2/3的堆内存空间，只有在 Major GC 的时候才会进行清理，每次 GC 都会触发“Stop-The-World”。内存越大，STW 的时间也越长，所以内存也不仅仅是越大就越好。由于复制算法在对象存活率较高的老年代会进行很多次的复制操作，效率很低，所以老年代这里采用的是标记 --- 整理算法。

在内存担保机制之下，无法安置的对象会直接进入老年代，以下几种情况也会进入老年代。

1. 大对象

   大对象指需要大量连续内存空间的对象，这部分对象不管是不是“朝生夕死”，都会直接进到老年代。这样做主要是为了避免在 Eden 区及2个 Survivor 区之间发生大量的内存复制。当你的系统有非常多“朝生夕死”的大对象时，得注意了。

2. 长期存活对象

   虚拟机给每个对象定义了一个对象年龄（Age）计数器。正常情况下对象会不断的在 Survivor 的 From 区与 To 区之间移动，对象在 Survivor 区中没经历一次 Minor GC，年龄就增加1岁。当年龄增加到15岁时，这时候就会被转移到老年代。当然，这里的15，JVM 也支持进行特殊设置。

3. 动态对象年龄

   虚拟机并不重视要求对象年龄必须到15岁，才会放入老年区，如果 Survivor 空间中相同年龄所有对象大小的综合大于 Survivor 空间的一般，年龄大于等于该年龄的对象就可以直接进去老年区，无需等你“成年”。



### JVM memory

线程私有的运行时数据区：程序计数器、Java虚拟机栈、本地方法栈

线程共享的运行时数据区：Java堆、方法区









## MyBatis

### select

多数情况下，查询要比修改频繁，对每个插入、更新或者删除操作，通常对应多个查询。是 MyBatis 的基本原则之一，也是将焦点和努力放到查询和结果映射的原因。简单查询的 select 元素是非常简单的。

```xml
<select id="selectPerson" parameterType="int" resultType="hashmap">
    SELECT * FROM PERSON WHERE ID = #{id}
</select>
```

接受一个int传入参数，返回一个hashmap。

| 属性             | 描述                                                         |
| :--------------- | :----------------------------------------------------------- |
| `id`             | 在命名空间中唯一的标识符，可以被用来引用这条语句。           |
| `parameterType`  | 将会传入这条语句的参数类的完全限定名或别名。这个属性是可选的，因为 MyBatis 可以通过 TypeHandler 推断出具体传入语句的参数，默认值为 unset。 |
| ~~parameterMap~~ | ~~这是引用外部 parameterMap 的已经被废弃的方法。使用内联参数映射和 parameterType 属性。~~ |
| `resultType`     | 从这条语句中返回的期望类型的类的完全限定名或别名。注意如果是集合情形，那应该是集合可以包含的类型，而不能是集合本身。使用 resultType 或 resultMap，但不能同时使用。 |
| `resultMap`      | 外部 resultMap 的命名引用。结果集的映射是 MyBatis 最强大的特性，对其有一个很好的理解的话，许多复杂映射的情形都能迎刃而解。使用 resultMap 或 resultType，但不能同时使用。 |
| `flushCache`     | 将其设置为 true，任何时候只要语句被调用，都会导致本地缓存和二级缓存都会被清空，默认值：false。 |
| `useCache`       | 将其设置为 true，将会导致本条语句的结果被二级缓存，默认值：对 select 元素为 true。 |
| `timeout`        | 这个设置是在抛出异常之前，驱动程序等待数据库返回请求结果的秒数。默认值为 unset（依赖驱动）。 |
| `fetchSize`      | 这是尝试影响驱动程序每次批量返回的结果行数和这个设置值相等。默认值为 unset（依赖驱动）。 |
| `statementType`  | STATEMENT，PREPARED 或 CALLABLE 的一个。这会让 MyBatis 分别使用 Statement，PreparedStatement 或 CallableStatement，默认值：PREPARED。 |
| `resultSetType`  | FORWARD_ONLY，SCROLL_SENSITIVE 或 SCROLL_INSENSITIVE 中的一个，默认值为 unset （依赖驱动）。 |
| `databaseId`     | 如果配置了 databaseIdProvider，MyBatis 会加载所有的不带 databaseId 或匹配当前 databaseId 的语句；如果带或者不带的语句都有，则不带的会被忽略。 |
| `resultOrdered`  | 这个设置仅针对嵌套结果 select 语句适用：如果为 true，就是假设包含了嵌套结果集或是分组了，这样的话当返回一个主结果行的时候，就不会发生有对前面结果集的引用的情况。这就使得在获取嵌套的结果集的时候不至于导致内存不够用。默认值：`false`。 |
| `resultSets`     | 这个设置仅对多结果集的情况适用，它将列出语句执行后返回的结果集并每个结果集给一个名称，名称是逗号分隔的。 |



### insert

```xml
<insert
  id="insertAuthor"
  parameterType="domain.blog.Author"
  flushCache="true"
  statementType="PREPARED"
  keyProperty=""
  keyColumn=""
  useGeneratedKeys=""
  timeout="20">
```



### update

```xml
<update
  id="updateAuthor"
  parameterType="domain.blog.Author"
  flushCache="true"
  statementType="PREPARED"
  timeout="20">
```



### delete

```xml
<delete
  id="deleteAuthor"
  parameterType="domain.blog.Author"
  flushCache="true"
  statementType="PREPARED"
  timeout="20">
```

| 属性               | 描述                                                         |
| :----------------- | :----------------------------------------------------------- |
| `id`               | 命名空间中的唯一标识符，可被用来代表这条语句。               |
| `parameterType`    | 将要传入语句的参数的完全限定类名或别名。这个属性是可选的，因为 MyBatis 可以通过 TypeHandler 推断出具体传入语句的参数，默认值为 unset。 |
| `parameterMap`     | ~~这是引用外部 parameterMap 的已经被废弃的方法。使用内联参数映射和 parameterType 属性。~~ |
| `flushCache`       | 将其设置为 true，任何时候只要语句被调用，都会导致本地缓存和二级缓存都会被清空，默认值：true（对应插入、更新和删除语句）。 |
| `timeout`          | 这个设置是在抛出异常之前，驱动程序等待数据库返回请求结果的秒数。默认值为 unset（依赖驱动）。 |
| `statementType`    | STATEMENT，PREPARED 或 CALLABLE 的一个。这会让 MyBatis 分别使用 Statement，PreparedStatement 或 CallableStatement，默认值：PREPARED。 |
| `useGeneratedKeys` | （仅对 insert 和 update 有用）这会令 MyBatis 使用 JDBC 的 getGeneratedKeys 方法来取出由数据库内部生成的主键（比如：像 MySQL 和 SQL Server 这样的关系数据库管理系统的自动递增字段），默认值：false。 |
| `keyProperty`      | （仅对 insert 和 update 有用）唯一标记一个属性，MyBatis 会通过 getGeneratedKeys 的返回值或者通过 insert 语句的 selectKey 子元素设置它的键值，默认：`unset`。如果希望得到多个生成的列，也可以是逗号分隔的属性名称列表。 |
| `keyColumn`        | （仅对 insert 和 update 有用）通过生成的键值设置表中的列名，这个设置仅在某些数据库（像 PostgreSQL）是必须的，当主键列不是表中的第一列的时候需要设置。如果希望得到多个生成的列，也可以是逗号分隔的属性名称列表。 |
| `databaseId`       | 如果配置了 databaseIdProvider，MyBatis 会加载所有的不带 databaseId 或匹配当前 databaseId 的语句；如果带或者不带的语句都有，则不带的会被忽略。 |



#### #{}&${}

1）#{}是预编译处理，$ {}是字符串替换。

2）MyBatis在处理#{}时，会将SQL中的#{}替换为?号，使用PreparedStatement的set方法来赋值；MyBatis在处理 ${} 时，就是把 ${} 替换成变量的值。

3）使用 #{} 可以有效的防止SQL注入，提高系统安全性。



### sql

这个元素可以被用来定义可重用的 SQL 代码段，可以包含在其他语句中。它可以被静态地（在加载参数）参数化. 不同的属性值通过包含的实例变化. 比如：

```xml
<sql id="userColumns"> ${alias}.id,${alias}.username,${alias}.password </sql>
```

这个SQL片段可以包括在其他语句中间

```xml
<select id="selectUsers" resultType="map">
	select
	 	<include refid="userColumns"><property name="alias" value="t1"/><include>,
	 	<include refid="userColumns"><property name="alias" value="t2"/><include>
	from some_table1 t1
	cross join some_table2 t2
</select>
```



### resultMap

MyBatis中的简单的映射不需要resultMap，只需要一个map就可以将结果映射。

```xml
<select id="selectUsers" resultType="map">
    select id, username, gender
    from table_name
    where city=#{city}
</select>
```

**查询结果将所有的结果都映射到HashMap的键值映射上**，由resultType的属性指定。

但是并不是所有的逻辑映射关系都可以使用HashMap解决，在实际逻辑中，大概率会遇到需要使用自定义的Java Bean或者POJO作为领域模型，如果是Java Bean或者POJO，Mybatis的支持如下：

假设一个Java Bean被设计为：

```java 
@Data
public class User {
    public int id;
    public String username;
    public String gender;
    public String city;
}
```

**此时每个Java Bean模型都可以和数据库中的数据形成映射关系**，假设这个Java Bean的路径是com.someapp.model.User，那么我们可以直接在MyBatis中直接传入这resultType、

```xml
<select id="selectUsers" resultType="com.someapp.model.User">
    select id, username, gender
    from table_name
    where city=#{city}
</select>
```

但是如果这个Java Bean需要在MyBatis中频繁使用，**使用路径的话就输入比较麻烦，所以可以采用类型别名的办法，给这个Java Beanqi一个名字。**

```xml
<!-- In mybatis-config.xml file -->
<typeAlias type="com.someapp.model.User" alias="User"/>

<!-- In SQL Mapping XML file -->
<select id="selectUsers" resultType="User">
    select id, username, hashedPassword
    from some_table
    where id = #{id}
</select>
```

在这种情况下，MyBatis会在幕后创建一个resultMap，再基于属性名进行映射到Java Bean上，如果列名和属性名没有精确对应，可以再select中对列使用别名，来匹配标签，

```xml
<select id="selectUser" resultType="User">
	select 
    	user_id as "id",
    	user_name as "name",
    	user_gender as "gender"
    from table_name
    where city=#{city}
</select>
```

除此之外，还可以自定义外部的resultMap，这也可以解决列名不匹配

```xml
<resultMap id="userResultMap" type="User">
	<id property="id" column="user_name"></id>
    <result property="name" column="user_name"></result>
    <result property="gender" column="user_gender"></result>
</resultMap>
```

这个有唯一id的resultMap就可以被别的xml语句直接引用，xml将查询结果映射到Java Bean。

```xml
<select id="selectUsers" resultMap="userResultMap">
  	select user_id, user_name, hashed_password
  	from some_table
  	where id = #{id}
</select>
```



#### resultMap model

1. constructor-用于在实例化的时候，注入结果到构造方法中，

   idArg-ID参数：标记处用作id的结果参数可以提升整体性能

   arg：被注入到构造方法中的普通结果

2. id-标记出作为id的结果参数可以提升整体性能

3. result-注入到字段或者Java Bean的普通结果

4. association-一个复杂类型的关联，许多结果包装成这种类型

   嵌套结果映射：关联也可以指定为一个resultMap，或者引用一个

5. collection-一个复杂类型的集合

   嵌套结果映射，集合可以指定为一个resultMap元素，或者引用一个

6. discriminator-使用结果值来确定使用哪个resultMap



```xml
<id property="id" column="post_id"/>
<result property="subject" column="post_subject"/>
```

这两者之间的唯一不同是， id 表示的结果将是对象的标识属性，这会在比较对象实例时用到。这样可以提高整体的性能，尤其是缓存和嵌套结果映射(也就是联合映射)的时候。



resultMap 属性的结果映射不同。

| 属性          | 描述                                                         |
| :------------ | :----------------------------------------------------------- |
| `property`    | 映射到列结果的字段或属性。如果用来匹配的 JavaBeans 存在给定名字的属性，那么它将会被使用。否则 MyBatis 将会寻找与给定名称相同的字段。这两种情形你可以使用通常点式的复杂属性导航。比如,你可以这样映射一 些 东 西 :“ username ”, 或 者 映 射 到 一 些 复 杂 的 东 西 :“address.street.number”。 |
| `javaType`    | 一个 Java 类的完全限定名,或一个类型别名(参考上面内建类型别名的列表)。如果你映射到一个 JavaBean,MyBatis 通常可以断定类型。然而,如javaType果你映射到的是 HashMap,那么你应该明确地指定 javaType 来保证所需的行为。 |
| `jdbcType`    | 在这个表格之前的所支持的 JDBC 类型列表中的类型。JDBC 类型是仅仅需要对插入,更新和删除操作可能为空的列进行处理。这是 JDBC 的需要,jdbcType而不是 MyBatis 的。如果你直接使用 JDBC 编程,你需要指定这个类型-但仅仅对可能为空的值。 |
| `typeHandler` | 我们在前面讨论过默认的类型处理器。使用这个属性,你可以覆盖默认的typeHandler类型处理器。这个属性值是类的完全限定名或者是一个类型处理器的实现,或者是类型别名。 |



#### 关联的嵌套查询

| 属性        | 描述                                                         |
| :---------- | :----------------------------------------------------------- |
| `column`    | 来自数据库的列名,或重命名的列标签。这和通常传递给resultSet.getString(columnName)方法的字符串是相同的。column注 意 : 要 处 理 复 合 主 键 , 你 可 以 指 定 多 个 列 名 通 过 column= ”{prop1=col1,prop2=col2} ” 这种语法来传递给嵌套查询语 句。这会引起prop1 和 prop2 以参数对象形式来设置给目标嵌套查询语句。 |
| `select`    | 另外一个映射语句的 ID,可以加载这个属性映射需要的复杂类型。获取的在列属性中指定的列的值将被传递给目标 select 语句作为参数。表格后面有一个详细的示例。select注 意 : 要 处 理 复 合 主 键 , 你 可 以 指 定 多 个 列 名 通 过 column= ”{prop1=col1,prop2=col2} ” 这种语法来传递给嵌套查询语 句。这会引起prop1 和 prop2 以参数对象形式来设置给目标嵌套查询语句。 |
| `fetchType` | 可选的。有效值为 `lazy`和`eager`。如果使用了，它将取代全局配置参数`lazyLoadingEnabled`。 |



[Mybatis基本sql语法_jtkjtj的博客-CSDN博客_mybatis sql](https://blog.csdn.net/jtkjtj/article/details/80242936)



## Tips

### == & equals

> 第一个区别在于双等号是二元比较运算符，equals是一个方法。作为运算符，双等号可以用于连接两个基本类型的变量（或字面量），也可以连接两个对象。相比之下，equals只能用来连接两个对象，一个作为调用方，一个作为参数。
>
> 第二个区别在于比较方式不同。



对于基本类型，双等号用于比较二者的值是否相等，

```java
int a=1;
int b=2;
System.out.println(a==b) // false
```

对于引用变量，双等号用于比较是否指向同一个对象，

```java 
Student john = new Student("John", 25);
Student mary = new Student("Mary", 25);
System.out.println(john==mary); // false
```



equals方法不能用于比较基本类型数据。

```java 
public boolean equals(Object obj) {
    return (this == obj);
}
```

这里没有考虑对象为null的情况，如果调用的对象为null，将引发空指针异常。

因为很多我们常用的类都重写了equals方法，比如String。换句话说，只有重写了equals方法的类，比较双等号和equals才有意义。
 接下来，我们就看一下几种特殊的类中双等号和equals都是如何表现的。

**String**

比较两个字符串的内容是否相等时，应该使用equals方法，因为String类对equals方法进行了重写。

**字符串的equals比较的是这两个字符串的内容是否相等。**



除了equals之外，有时候不完全只需要比较字符串是否内容相等，还需要对比是否对象相等，这时候就需要双等号比较。

```java
String a = "abc";
String b = "abc";
String c = new String("abc");
String d = new StringBuilder("abc").toString();
String e = new String("abc").intern();
String f = "ab" + "c";
String g = new String("ab") + new String("c");

System.out.println(a == b); // true
System.out.println(a == c); // false
System.out.println(a == d); // false
System.out.println(a == e); // true
System.out.println(a == f); // true
System.out.println(a == g); // false
```

1. a==b

   这里就要引入字符串常量池的概念了。为了提高效率，java在使用字符串的时候并不会每次都生成一个新的字符串，而是在内存中分配一块区域作为常量池，遇到字符串的变量时，优先到常量池中找是否有相同的字符串，如果有的话，直接复用当前的字符串，如果没有的话，再创建新的字符串对象。因此，实际上a和b指向的是同一个内存对象。

2. a!=c

   那么a和c的比较结果为什么又是false呢？因为变量c显式地调用了String的构造池，这时字符串将无视常量池中是否有相同的变量，强制重新创建一个字符串，创建的字符串也不会加入常量池中。因此，a和c指向的不是同一个对象。

3. a!=d

   我们再来看a和d的比较结果。d中使用了StringBuilder，字符串字面量"abc"作为StringBuilder的参数，按理说不会创建新的字符串呀。问题出在后面的toString()方法上，想要把StringBuilder转换为String对象，就需要调用toString()方法。通过查看toString()方法的源码，我们发现在该方法中调用了String的构造器，因此和c一样，重新创建了字符串的对象。

4. a==e

   a和e的比较结果比较有意思。e明明调用了String的构造器，为什么比较结果为true呢？关键在于后面的intern()方法，这个方法大家可能不太熟悉，它的作用就是判断常量池中有没有对应的字符串，如果有的话，直接复用该字符串，如果没有的话，创建字符串并加入常量池中。也就是说，调用了intern()方法之后，效果和直接使用字面量是相同的。事实上，按照官方文档的说法，字符串字面量和字符串常量都属于被intern的字符串。调用intern()方法之后，a和e指向了同一个字符串对象。

5. a==f/a!=g

   a与f和g的比较结果我们一起看。先说a和f，变量f创建字符串时，使用了字符串拼接。java在编译过程中，如果发现能够计算的结果，会在编译期直接将结果计算出来。也就是说，编译结束后，f变量也是字符串"abc"这一字面量了。a和f完全相同，根据上述常量池的原理，a和f指向同一对象。再来看g，变量g使用String的构造器进行字符串拼接，由于需要创建对象，在编译期编译器无法对该结果进行求值，因此g只能等待运行期创建两个字符串对象，再进行拼接，二者不会指向同一个对象。



**重写equals和hashcode**

```java
@Override
public boolean equals(Object o) {
    if (this == o) {
        return true;
    }
    if (o == null || getClass() != o.getClass()) {
        return false;
    }
    Student student = (Student) o;
    return age == student.age &&
        Objects.equals(name, student.name);
}

@Override
public int hashCode() {
    return Objects.hash(name, age);
}
```



### 字符串哈希

为什么选31作为Hash的乘子？

Java源码中String的hashCode方法：

```java 
public int hashCode() {
    int h = hash;
    if (h == 0 && value.lenght > 0) {
        char val[] = value;
        
        for (int i = 0; i < value.length; ++i) {
            h = 31 * h + val[i];
        }
        hash = h;
    }
    return h;
}
```

这里说明一下，上面的 s 数组即源码中的 val 数组，是 String 内部维护的一个 char 类型数组。这里我来简单推导一下这个公式：

```txt
假设 n=3
i=0 -> h = 31 * 0 + val[0]
i=1 -> h = 31 * (31 * 0 + val[0]) + val[1]
i=2 -> h = 31 * (31 * (31 * 0 + val[0]) + val[1]) + val[2]
       h = 31*31*31*0 + 31*31*val[0] + 31*val[1] + val[2]
       h = 31^(n-1)*val[0] + 31^(n-2)*val[1] + val[2]
```

从网上的资料来看，31作为乘子有两个理由：

1. 31是个不大不小的质数。
2. 31可以被JVM优化，`31 * i = (i << 5) - i`。

> **为什么Hash要选质数作为乘子？Why Hash picks prime numbers as multipliers**
>
> 1. 众所周知，Hash是算出来的，而不是比出来的。
>
>    在顺序结构以及平衡树中，元素的Key和其存储位置没有固定的规律对应关系。因此在查找一个元素的时候，必须要经过多次比较。凡是存在比较，就可能出现O(n)的时间复杂度。
>
>    所以理想的搜索方法是，不通过比较，一次性直接从表中获得想要的元素。通过某种函数可以直接将元素的Key和存储位置对应起来，在查找的时候就可以一次性定位到元素的位置，取出value。
>
>    以上方法即为Hash，能够将key对应上存储的函数叫做Hash函数，构造出来的结构叫做Hash表。
>
> 2. Hash冲突
>
>    一个Hash涉及到Capacity，这个Capacity可以理解为桶排序里的桶，如果一个萝卜一个坑，那么总元素就不能超过这个Capacity，否则就会出现两个萝卜占用同一个坑，就出现了Hash冲突。假设Capacitity为10，Hash算法为对Capacity取模，那么4和14的hash码就是一样的，都对应到4，出现了两个不同的Key却对应到同一个存储位置的情况。14和4这样的情况被称为同义词。
>
>    因此判断一个Hash函数的好坏，主要就从以下几个角度：
>
>    1. Hash值是否分布均匀，呈现出随机性，有利于Hash表的空间利用率提升，增加hash的破解难度；
>    2. 哈希碰撞的概率很低，碰撞概率应该控制在一定范围；
>    3. 是否计算得更快，一个哈希函数计算时间越短效率越高。
>
>    计算hash冲突的理论值和实际值见[如何判断一个哈希函数的好坏 - SegmentFault 思否](https://segmentfault.com/a/1190000040928322)
>
> 3. 为什么取质数？（数学原理）
>
>    StackOverFlow上给出的一个回答--[java - Why use a prime number in hashCode? - Stack Overflow](https://stackoverflow.com/questions/3613102/why-use-a-prime-number-in-hashcode)
>
>    ```
>    Prime numbers are chosen to best distribute data among hash buckets.  
>    If the distribution of inputs is random and evenly spread, then the  choice of the hash code/modulus does not matter. It only has an impact  when there is a certain pattern to the inputs.
>                                                                   
>    This is often the case when dealing with memory locations. 
>    For  example, all 32-bit integers are aligned to addresses divisible by 4.  
>    Check out the table below to visualize the effects of using a prime vs.  non-prime modulus:
>    ```
>
>    ```java
>    Input       Modulo 8    Modulo 7
>    0           0           0
>    4           4           4
>    8           0           1
>    12          4           5
>    16          0           2
>    20          4           6
>    24          0           3
>    28          4           0
>    ```
>
>    Notice the almost-perfect distribution when using a prime modulus vs. a non-prime modulus.
>
>    However, although the above example is largely contrived, the general principle is that  when dealing with a *pattern of inputs*, using a prime number modulus will yield the best distribution.
>
>    针对31这个数字有一个更好的解释：
>
>    Because you want the number you are multiplying by and the number of  buckets you are inserting into to have orthogonal prime factorizations.
>
>    Suppose there are 8 buckets to insert into. If the number you are  using to multiply by is some multiple of 8, then the bucket inserted  into will only be determined by the least significant entry (the one not multiplied at all). Similar entries will collide. Not good for a hash  function.
>
>    31 is a large enough prime that the number of buckets is unlikely to  be divisible by it (and in fact, modern java HashMap implementations  keep the number of buckets to a power of 2).

[科普：为什么 String hashCode 方法选择数字31作为乘子 - SegmentFault 思否](https://segmentfault.com/a/1190000010799123)



那么针对[【字符串哈希】字符串哈希入门 (qq.com)](https://mp.weixin.qq.com/s?__biz=MzU4NDE3MTEyMA==&mid=2247489813&idx=1&sn=7f3bc18ca390d85b17655f7164d8e660&chksm=fd9cb20acaeb3b1cc78abf05d6fea6d093098998ce877f799ac478247604bd267fbee6fcd989&token=1342991619&lang=zh_CN#rd)

[【宫水三叶】一题双解 :「字符串哈希 + 二分」&「后缀数组」 - 最长重复子串 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/longest-duplicate-substring/solution/gong-shui-san-xie-zi-fu-chuan-ha-xi-ying-hae9/)的题解

一开始没有理解h和p两个数组的含义，实际上回到

```java
假设 n=3
i=0 -> h = 31 * 0 + val[0]
i=1 -> h = 31 * (31 * 0 + val[0]) + val[1]
i=2 -> h = 31 * (31 * (31 * 0 + val[0]) + val[1]) + val[2]
       h = 31*31*31*0 + 31*31*val[0] + 31*val[1] + val[2]
       h = 31^(n-1)*val[0] + 31^(n-2)*val[1] + val[2]
```

就可以理解。就把Hash函数当作一个多项式，不完全也不严谨的推导如下：

![2e0716ceba8d061a876d215b48b5cfd](C:\Users\Caijinyang\Desktop\2e0716ceba8d061a876d215b48b5cfd.png)

因此三叶叫p是次方数组。

一开始也只是我自己的想法，但是我就结合着题目自己试了一下：

```java
public class _187RepeatedDNASequences {
    private int n = 0;
    int N = (int) 1e5 + 10, P = 131313;
    int[] h = new int[N], p = new int[N];

    public List<String> findRepeatedDnaSequencesWithHash(@NotNull String s) {
        int n = s.length();
        List<String> ans = new ArrayList<>();
        p[0] = 1;
        for (int i = 1; i <= n; i++) {
            h[i] = h[i - 1] * P + s.charAt(i - 1);
            p[i] = p[i - 1] * P;
        }
        for (int i: p) 
            System.out.println(i)
        
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 1; i + 10 - 1 <= n; i++) {
            int j = i + 10 - 1;
            int hash = h[j] - h[i - 1] * p[j - i + 1];
            int cnt = map.getOrDefault(hash, 0);
            if (cnt == 1) ans.add(s.substring(i - 1, i + 10 - 1));
            map.put(hash, cnt + 1);
        }
        return ans;
    }

    public int getN() {
        return this.N;
    }

    public int getP() {
        return this.P;
    }

    public static void main(String[] args) {
        _187RepeatedDNASequences solution = new _187RepeatedDNASequences();
        System.out.println(solution.findRepeatedDnaSequencesWithHash("AAAAAAAAAAAA"));
        System.out.println(solution.getN());
        
    }
}
```

程序运行之前我仍然很疑惑，131313的二次方三次方一直次方下去不会int溢出吗？我就采用P=131作为乘子：

```java
1
131
17161
2248091
294499921
-75216013
-1263363111
2003157003
420562337
-740908701
1725207977
-1631021701
1084521969
```

结果就是他确实会溢出的。

```java
class Solution {
    private int n = 0;
    int N = (int) 1e5 + 10, P = 131;
    int[] h = new int[N], p = new int[N];
    public List<String> findRepeatedDnaSequences(String s) {
        int n = s.length();
        List<String> ans = new ArrayList<>();
        p[0] = 1;
        for (int i = 1; i <= n; i++) {
            h[i] = h[i - 1] * P + s.charAt(i - 1);
            p[i] = p[i - 1] * P;
        }
        
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 1; i + 10 - 1 <= n; i++) {
            int j = i + 10 - 1;
            int hash = h[j] - h[i - 1] * p[j - i + 1];
            int cnt = map.getOrDefault(hash, 0);
            if (cnt == 1) ans.add(s.substring(i - 1, i + 10 - 1));
            map.put(hash, cnt + 1);
        }
        return ans;
    }
}
```

<img src="C:\Users\Caijinyang\AppData\Roaming\Typora\typora-user-images\image-20211224184103017.png" alt="image-20211224184103017" style="zoom: 67%;" />

Capacity选取131过不了第30个case，选取131313可以通过。



