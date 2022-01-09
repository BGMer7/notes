# Java Questions

Java找工作都只是背答案吗？ - 帅地的文章 - 知乎 https://zhuanlan.zhihu.com/p/386380153

## Java基础问题

### 1. 面向对象和面向过程

面向对象编程：Object Oriented Programming

面向过程编程：Procedure Oriented Programming

面向对象是一种基于面向过程的编程思想，是向现实世界模型的自然延伸，这是一种“万物皆对象”的编程思想。由执行者变为指挥者，在现实生活中的任何物体都可以归为一类事物，而每一个个体都是一类事物的实例。面向对象的编程是以对象为中心，以消息为驱动。

区别:
（1）编程思路不同：面向过程以实现功能的函数开发为主，而面向对象要首先抽象出类、属性及其方法，然后通过实例化类、执行方法来完成功能。

（2）封装性：都具有封装性，但是面向过程是封装的是功能，而面向对象封装的是数据和功能。

（3）面向对象具有继承性和多态性，而面向过程没有继承性和多态性，所以面向对象优势很明显



### 2. 面向对象的三大特性

（1）封装：通常认为封装是把数据和操作数据的方法封装起来，对数据的访问只能通过已定义的接口。
（2）继承：继承是从已有类得到继承信息创建新类的过程。提供继承信息的类被称为父类（超类/基类），得到继承信息的被称为子类（派生类）。
（3）多态：分为编译时多态（方法重载）和运行时多态（方法重写）。要实现多态需要做两件事：一是子类继承父类并重写父类中的方法，二是用父类型引用子类型对象，这样同样的引用调用同样的方法就会根据子类对象的不同而表现出不同的行为。



补充：

1. 子类拥有父类对象所有的属性和方法（包括私有属性和私有方法），但是父类中的私有属性和方法子类是无法访问，只是拥有。因为在一个子类被创建的时候，首先会在内存中创建一个父类对象，然后在父类对象外部放上子类独有的属性，两者合起来形成一个子类的对象；
2. 子类可以拥有自己独立的字段和方法；
3. 子类可以用自己的方法重写父类的方法Override



### 3. JDK、JRE、JVM

JDK（Java Development Kit）：是 Java 开发工具包，是整个 Java 的核心，包括了 Java 运行环境 JRE、Java 工具和 Java 基础类库。
JRE（ Java Runtime Environment）：是 Java 的运行环境，包含 JVM 标准实现及 Java 核心类库。
JVM（Java Virtual Machine）：是 Java 虚拟机，是整个 Java 实现跨平台的最核心的部分，能够运行以 Java 语言写作的软件程序。所有的 Java 程序会首先被编译为 .class 的类文件，这种类文件可以在虚拟机上执行。



### 4. 重载和重写的区别

（1）重载：编译时多态、同一个类中同名的方法具有不同的参数列表、不能根据返回类型进行区分【因为：函数调用时不能指定类型信息，编译器不知道你要调哪个函数】；
（2）重写（又名覆盖）：运行时多态、子类与父类之间、子类重写父类的方法具有相同的返回类型、更好的访问权限。

总结：重载是针对两个函数的不同参数列表的多态，重写是针对两个类，子类重写父类的方法。



### 5. 可否重写private或者static方法

private：别的类是访问不到的，更不用说重写

static：静态方法不能被重写因为static方法是在编译期间静态绑定的，而方法重写是在运行期间动态绑定的。



静态方法补充：

静态方法不可以被重写，但是可以被继承。如果父类中有同名的方法，那么可以单独在子类中继续写同名方法，但是并非是Override关系，换句话说，父类提供了静态方法就用，想再写一个也行。

父类代码：

```java
package com.gatsby.test.staticOverride;

/**
 * @author -- gatsby
 * @date -- 2022/1/8
 * @description -- 测试静态方法的继承问题的父类
 */


public class StaticMethodBase {
    public String getClassName() {
        return "Base";
    }

    public static void print() {
        System.out.println("hello base");
    }
}
```

子类代码：

```java
package com.gatsby.test.staticOverride;

/**
 * @author -- gatsby
 * @date -- 2022/1/8
 * @description -- 测试静态方法的继承问题的子类
 */


public class StaticMethodDerived extends StaticMethodBase {
    @Override
    public String getClassName() {
        return "derived";
    }

    public static void print() {
        System.out.println("hello derived");
    }
}
```

Main类代码：

```java
package com.gatsby.test.staticOverride;

/**
 * @author -- gatsby
 * @date -- 2022/1/8
 * @description -- 测试静态方法的继承问题的main类
 */


public class StaticMethodMain {
    public static void main(String[] args) {
        StaticMethodBase.print();
        StaticMethodDerived.print();

        StaticMethodBase base = new StaticMethodBase();
        StaticMethodDerived derived = new StaticMethodDerived();
        System.out.println(base.getClassName());
        System.out.println(derived.getClassName());
    }
}
```

运行结果：

```shell
hello base
hello derived
Base
derived
```

​	

### 6. 构造方法的特性

1. 名字和类名相同
2. 没有返回值，但不是void
3. 自动执行，无需调用



### 7. 子类构造函数

Java程序在执行子类的构造方法之前，如果没有使用super()方法，就会调用父类中的默认的无参构造方法。

如果父类中没有显式定义无参构造方法，那么编译的时候会发生错误，因此可以在父类中写一个无参数的构造方法。



### 8. 构造一个对象的几种方法

1. new关键字

2. `Person person = new Person.class.newInstance();`

   但是这种方法调用的是类中的public无参构造方法，如果没有显式创建，就会报错。

3. `Person person = new Person("John", 18);`

   `Object clonePerson = person.clone();`

   完成了对象的clone但是是一个全新的对象。

4. 反序列化
   ```java
   Person person = new Person("fsx", 18);
   byte[] bytes = SerializationUtils.serialize(person);
    
   // 字节数组：可以来自网络、可以来自文件（本处直接本地模拟）
   Object deserPerson = SerializationUtils.deserialize(bytes);



### 9. 抽象类和接口

1. 抽象类可以定义构造函数，接口不能定义构造方法。
2. 抽象类中允许存在public具体方法，但是接口只能写public abstruct。
3. 抽象类可以public、private、protected，但是接口就只能是public，因为接口写出来就是为了别的类对方法进行重写的。
4. 抽象类可以包含静态方法，但是接口不可能带静态方法。



### 10. 静态变量和实例变量

静态变量属于类，实例变量属于对象。

静态变量：是被 static 修饰的变量，也称为类变量，**它属于类，因此不管创建多少个对象，静态变量在内存中有且仅有一个拷贝**；静态变量可以实现让多个对象共享内存。

实例变量：属于某一实例，需要先创建对象，然后通过对象才能访问到它。



### 11. short+=1和short = short+1

对于 short s1 = 1; s1 = s1 + 1; 来说，在 s1 + 1 运算时会自动提升表达式的类型为 int ，那么将 int 型值赋值给 short 型变量，s1 会出现类型转换错误。

对于 short s1 = 1; s1 += 1; 来说，+= 是 Java 语言规定的运算符，Java 编译器会对它进行特殊处理，因此可以正确编译。

```java
Required type:
short
Provided:
int
```



### 12. Integer和int

1. int是八种基本类型之一，而Integer是封装类；
2. int类型的默认值是0，Integer的默认值是null，Integer可以区分出对象是未赋值还是值就等于0；
3. int不需要实例化，但是Integer需要实例化



补充：

1、由于 Integer 变量实际上是对一个 Integer 对象的引用，所以两个通过 new 生成的 Integer 变量永远是不相等的，因为其内存地址是不同的；

2、Integer 变量和 int 变量比较时，只要两个变量的值是相等的，则结果为 true。因为包装类 Integer 和基本数据类型 int 类型进行比较时，Java 会自动拆包装类为 int，然后进行比较，实际上就是两个 int 型变量在进行比较；

3、非 new 生成的 Integer 变量和 new Integer() 生成的变量进行比较时，结果为 false。因为非 new 生成的 Integer 变量指向的是 Java 常量池中的对象，而 new Integer() 生成的变量指向堆中新建的对象，两者在内存中的地址不同；

4、对于两个非 new 生成的 Integer 对象进行比较时，如果两个变量的值在区间 [-128, 127] 之间，则比较结果为 true，否则为 false。Java 在编译 Integer i = 100 时，会编译成 Integer i = Integer.valueOf(100)，而 Integer 类型的 valueOf 的源码如下所示：

```java
public static Integer valueOf(int var0) {           
  return var0 >= -128 && var0 <= Integer.IntegerCache.high ? Integer.IntegerCache.cache[var0 + 128] : new Integer(var0);
}
```













