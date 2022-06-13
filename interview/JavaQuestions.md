# Java Questions

Java找工作都只是背答案吗？ - 帅地的文章 - 知乎 https://zhuanlan.zhihu.com/p/386380153

## Java Basic

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



### 13. 装箱和拆箱的区别

自动装箱是 Java 编译器在基本数据类型和对应得包装类之间做的一个转化。比如：把 int 转化成 Integer，double 转化成 Double 等等。反之就是自动拆箱。

原始类型：boolean、char、byte、short、int、long、float、double

封装类型：Boolean、Character、Byte、Short、Integer、Long、Float、Double



### 14. final、finally、finalize

final：用于声明属性、方法和类，分别表示属性不可变、方法不可覆盖、被其修饰的类不可继承；

finally：异常处理语句结构的一部分，表示总是执行；

finallize：Object类的一个方法，在垃圾回收时会调用被回收对象的finalize



### 15. ==和equals

==：如果是两个基本类型比对，那么比较的是数值，如果是两个对象比对，那么比较的是地址

equals 方法：用来比较两个对象的内容是否相等。注意：equals 方法不能用于比较基本数据类型的变量。如果没有对 equals 方法进行重写，则比较的是引用类型的变量所指向的对象的地址（很多类重写了 equals 方法，比如 String、Integer 等把它变成了值比较，所以一般情况下 equals 比较的是值是否相等）。



### 16. 两个对象hashCode()相等，一定equals()吗？

哈希冲突的时候哈希值也是相等的，所以不一定能够确定是equals()的。

equal一定hashCode相等，反之不成立。



### 17. 重写equals()一定要重写hashCode()

> 首先问题在于，一定是使用到了hashCode才需要重写hashCode()方法，如果并没有使用到Hash函数，那么hashCode并不那么重要。
>
> 但是由于一般来说经常用到hash的集合，因此强烈推荐重写equals()就要重写hashCode()方法。

对于对象集合的判重，如果一个集合含有 10000 个对象实例，仅仅使用 equals() 方法的话，那么对于一个对象判重就需要比较 10000 次，随着集合规模的增大，时间开销是很大的。但是同时使用哈希表的话，就能快速定位到对象的大概存储位置，并且在定位到大概存储位置后，后续比较过程中，如果两个对象的 hashCode 不相同，也不再需要调用 equals() 方法，从而大大减少了 equals() 比较次数。

所以从程序实现原理上来讲的话，既需要 equals() 方法，也需要 hashCode() 方法。那么既然重写了 equals()，那么也要重写 hashCode() 方法，以保证两者之间的配合关系。



**hashCode（）与equals（）的相关规定**：

1、如果两个对象相等，则 hashCode 一定也是相同的；

2、两个对象相等，对两个对象分别调用 equals 方法都返回 true；

3、两个对象有相同的 hashCode 值，它们也不一定是相等的；

4、因此，equals 方法被覆盖过，则 hashCode 方法也必须被覆盖；

5、hashCode() 的默认行为是对堆上的对象产生独特值。如果没有重写 hashCode()，则该 class 的两个对象无论如何都不会相等（即使这两个对象指向相同的数据）。



### 18. &和&&

Java 中 && 和 & 都是表示与的逻辑运算符，都表示逻辑运输符and，当两边的表达式都为 true 的时候，整个运算结果才为 true，否则为 false。

&&：有短路功能，当第一个表达式的值为 false 的时候，则不再计算第二个表达式；

&：不管第一个表达式结果是否为 true，第二个都会执行。除此之外，& 还可以用作位运算符：当 & 两边的表达式不是 Boolean 类型的时候，& 表示按位操作。



### 19. 按值传递和按引用传递

简单来说，基本数据类型按值传递，对象按引用传递。

> 值传递（pass by value）是指在调用函数时将实际参数`复制`一份传递到函数中，这样在函数中如果对`参数`进行修改，将不会影响到实际参数。
>
> 引用传递（pass by reference）是指在调用函数时将实际参数的地址`直接`传递到函数中，那么在函数中对`参数`所进行的修改，将影响到实际参数。

所以值传递和引用传递的根本问题在于：

值传递会创建副本，引用传递不会创建副本。

函数中无法改变原对象，函数中可以改变原对象。

Java 到底是值传递还是引用传递？ - Hollis的回答 - 知乎 https://www.zhihu.com/question/31203609/answer/576030121



### 20. Math.round(-1.5)等于多少

等于 -1，因为在数轴上取值时，中间值（0.5）向右取整，所以正 0.5 是往上取整，负 0.5 是直接舍弃。



### 21. 两个二进制数的异或结果是什么

两个二进制数异或结果是这两个二进制数差的绝对值。表达式如下：a^b = |a-b|

两个二进制 a 与 b 异或，即 a 和 b 两个数按位进行运算。如果对应的位相同，则为 0（相当于对应的算术相减），如果不同即为 1（相当于对应的算术相加）。由于二进制每个位只有两种状态，要么是 0，要么是 1，则按位异或操作可表达为按位相减取值相对值，再按位累加。



### 22. 如何对象的克隆

1. 实现 Cloneable 接口并重写 Object 类中的 clone() 方法；

2. 实现 Serializable 接口，通过对象的序列化和反序列化实现克隆，可以实现真正的深克隆。



### 23. Java的序列化

序列化：对象序列化是一个用于将对象状态转换为字节流的过程，可以将其保存到磁盘文件中或通过网络发送到任何其他程序，一般保存在内存、文件、数据库中。

反序列化：将磁盘或者文件中的字节流转化为对象。

创建的字节流与平台是无关的，创建的字节流可以在任何平台上反序列化。序列化是为了解决在对象流进行读写操作所引发的问题。

ObjectOutputStream类的writeObject()方法可以实现序列化。

ObjectInputStream类的readObject()方法用于反序列化。

> java.io.ObjectOutputStream：表示对象输出流
>  它的writeObject(Object obj)方法可以对参数指定的obj对象进行序列化，把得到的字节序列写到一个目标输出流中。

> java.io.ObjectInputStream：表示对象输入流
>  它的readObject()方法从源输入流中读取字节序列，再把它们反序列化成为一个对象，并将其返回。

假定一个Student类，它的对象需要序列化，可以有如下三种方法：

- 方法一：若Student类仅仅实现了Serializable接口，则可以按照以下方式进行序列化和反序列化。
   ObjectOutputStream采用默认的序列化方式，对Student对象的非transient的实例变量进行序列化。
   ObjcetInputStream采用默认的反序列化方式，对对Student对象的非transient的实例变量进行反序列化。
- 方法二：若Student类仅仅实现了Serializable接口，并且还定义了readObject(ObjectInputStream in)和writeObject(ObjectOutputSteam out)，则采用以下方式进行序列化与反序列化。
   ObjectOutputStream调用Student对象的writeObject(ObjectOutputStream out)的方法进行序列化。
   ObjectInputStream会调用Student对象的readObject(ObjectInputStream in)的方法进行反序列化。
- 方法三：若Student类实现了Externalnalizable接口，且Student类必须实现readExternal(ObjectInput in)和writeExternal(ObjectOutput out)方法，则按照以下方式进行序列化与反序列化。
   ObjectOutputStream调用Student对象的writeExternal(ObjectOutput out))的方法进行序列化。
   ObjectInputStream会调用Student对象的readExternal(ObjectInput in)的方法进行反序列化。



### 24. 什么情况需要序列化

1. 当想要把内存中的对象状态保存到一个文件中或者数据库的时候。
2. 使用套接字在网络上传输的时候。
3. 通过RMI传输对象的时候。



### 25. 泛型编译和类型擦除

泛型类型是一种编译器行为，而不是JVM行为，JVM运行的时候不支持泛型，只支持基本类型和Object的对象类型，所以JVM运行的时候会将泛型类型转换为实际的类型。

**所有的泛型类型在运行的时候都会被擦除。**

因为类型擦出的原理，我们不能用基本类型实例化泛型类型：我们可以写`List<Integer>`，但是不能写`List<int>`。

类型擦除的实例：

```java
public static <T extends Comparable> T min(T[] n);
```

类型擦除之后变成：

```java
public static Comparable T min(Comparable[] n);
```

类型参数T已经被擦除了，只留下了限定类型Comparable。



### 26. 限定通配符和非限定通配符

限定通配符包括两种：

1. 表示类型的上界，格式为：`<？ extends T>`，即类型必须为T类型或者T子类
2. 表示类型的下界，格式为：`<？ super T>`，即类型必须为T类型或者T的父类

非限定通配符：

 	`<T>`



### 27. 反射

能够分析类能力的程序称为反射（reflective）。

反射机制的功能极其强大，在下面可以看到，反射机制可以用来：

- 在运行时分析类的能力。
- 在运行时查看对象，例如，编写一个toString方法供所有类使用。
- 实现通用的数组操作代码。
- 利用Method对象，这个对象很像C++中的函数指针。



### 28. 反射的优缺点

**优点：**

运行期类型的判断，class.forName() 动态加载类，提高代码的灵活度；

**缺点：**

尽管反射非常强大，但也不能滥用。如果一个功能可以不用反射完成，那么最好就不用。在我们使用反射技术时，下面几条内容应该牢记于心。

（1）性能开销 ：反射涉及了动态类型的解析，所以 JVM 无法对这些代码进行优化。因此，反射操作的效率要比那些非反射操作低得多。我们应该避免在经常被执行的代码或对性能要求很高的程序中使用反射。

（2）安全限制 ：使用反射技术要求程序必须在一个没有安全限制的环境中运行。如果一个程序必须在有安全限制的环境中运行，如 Applet，那么这就是个问题了。

（3）内部暴露：由于反射允许代码执行一些在正常情况下不被允许的操作（比如：访问私有的属性和方法），所以使用反射可能会导致意料之外的副作用，这可能导致代码功能失调并破坏可移植性。反射代码破坏了抽象性，因此当平台发生改变的时候，代码的行为就有可能也随着变化。

```java
package com.gatsby.chapter5_inherit;

import com.gatsby.chapter4_object_and_class.Employee;

/**
 * @author -- gatsby
 * @date -- 2022/1/13
 * @description -- 反射机制测试
 */


public class ReflectiveTest {
    public static void main(String[] args) {
        Employee employee = new Employee();
        Manager manager  = new Manager(15000);

        System.out.println(employee.getClass());
        System.out.println(employee.getClass().getName());
        System.out.println(manager.getClass().getName());
    }
}
```



### 29. 动态代理

为其他对象提供一个代理以控制对某个对象的访问。代理类主要负责为委托了（真实对象）预处理消息、过滤消息、传递消息给委托类，代理类不现实具体服务，而是利用委托类来完成服务，并将执行结果封装处理。

其实就是代理类为被代理类预处理消息、过滤消息并在此之后将消息转发给被代理类，之后还能进行消息的后置处理。代理类和被代理类通常会存在关联关系(即上面提到的持有的被带离对象的引用)，代理类本身不实现服务，而是通过调用被代理类中的方法来提供服务。

接口：

```csharp
public interface HelloInterface {
    void sayHello();
}
```

实现类/被代理类：

```java
public class Hello implements HelloInterface{
    @Override
    public void sayHello() {
        System.out.println("Hello zhanghao!");
    }
}
```

代理类：

```java
public class HelloProxy implements HelloInterface{
    private HelloInterface helloInterface = new Hello();
    @Override
    public void sayHello() {
        System.out.println("Before invoke sayHello" );
        helloInterface.sayHello();
        System.out.println("After invoke sayHello");
    }
}
```

动态代理：

利用反射机制在运行时创建代理类。
接口、被代理类不变，我们构建一个handler类来实现InvocationHandler接口。

```kotlin
public class ProxyHandler implements InvocationHandler{
    private Object object;
    public ProxyHandler(Object object){
        this.object = object;
    }
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("Before invoke "  + method.getName());
        method.invoke(object, args);
        System.out.println("After invoke " + method.getName());
        return null;
    }
}
```

执行动态代理：

```java
public static void main(String[] args) {
    System.getProperties().setProperty("sun.misc.ProxyGenerator.saveGeneratedFiles", "true");

    HelloInterface hello = new Hello();

    InvocationHandler handler = new ProxyHandler(hello);

    HelloInterface proxyHello = (HelloInterface) Proxy.newProxyInstance(hello.getClass().getClassLoader(), hello.getClass().getInterfaces(), handler);

    proxyHello.sayHello();
}
输出：
    Before invoke sayHello
    Hello zhanghao!
    After invoke sayHello
```

通过Proxy类的静态方法newProxyInstance返回一个接口的代理实例。针对不同的代理类，传入相应的代理程序控制器InvocationHandler。
如果新来一个被代理类Bye，如：

```java
public interface ByeInterface {
    void sayBye();
}
public class Bye implements ByeInterface {
    @Override
    public void sayBye() {
        System.out.println("Bye zhanghao!");
    }
}
```

```java
public static void main(String[] args) {
    System.getProperties().setProperty("sun.misc.ProxyGenerator.saveGeneratedFiles", "true");

    HelloInterface hello = new Hello();
    ByeInterface bye = new Bye();

    InvocationHandler handler = new ProxyHandler(hello);
    InvocationHandler handler1 = new ProxyHandler(bye);

    HelloInterface proxyHello = (HelloInterface) Proxy.newProxyInstance(hello.getClass().getClassLoader(), hello.getClass().getInterfaces(), handler);

    ByeInterface proxyBye = (ByeInterface) Proxy.newProxyInstance(bye.getClass().getClassLoader(), bye.getClass().getInterfaces(), handler1);
    proxyHello.sayHello();
    proxyBye.sayBye();
}
    输出：
    Before invoke sayHello
    Hello zhanghao!
    After invoke sayHello
    Before invoke sayBye
    Bye zhanghao!
    After invoke sayBye
```

[JAVA动态代理 - 简书 (jianshu.com)](https://www.jianshu.com/p/9bcac608c714)



### 29-1. 动态代理的本质

1. 动态代理类对应的class文件

   首先需要了解Java源文件实例对象的一个简单的流程：

   <img src="https://pic4.zhimg.com/v2-dc600925916a238e6871a4354a4c42af_r.jpg" alt="preview" style="zoom: 50%;" />

   Java文件->class文件（字节码）->class对象->实例对象

2. 动态代理的本质

   动态代理的本质是省略了.java文件，因为我们实际上并没有编写相应的Java代码，但是我们直接使用了字节码，也就是class文件。所以动态代理的实际本质是省略源文件的编写，直接在运行期生成class文件。



### 29-1. 动态代理的原理

动态代理最主要的几个原生类：

1. java.lang.reflect.InvocationHandler;

   ```Java
   public interface InvocationHandler {
       public Object invoke(Object proxy, Method method, Object[] args)
           throws Throwable;
   }
   ```

2. java.lang.reflect.Proxy;

```java
public class ProxyHandler implements InvocationHandler {
    private Object object;

    ProxyHandler(Object object) {
        this.object = object;
    }

    /*
     重写这部分的业务逻辑
     */
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("Before invoke "  + method.getName());
        method.invoke(object, args);
        System.out.println("After invoke " + method.getName());
        return null;
    }
}
```

有了NewProxy之后，最关键的两步就是

```java 
// 先利用实体类创建一个实体Impls
InvocationHandler handler = new ProxyHandler(hello);
// 使用Proxy类的静态方法
IHello proxyHello = (IHello) Proxy.newProxyInstance(hello.getClass().getClassLoader(), 																			hello.getClass().getInterfaces(), 
                                                    handler);
```











### 30. static

（1）静态变量：又称为类变量，也就是说这个变量属于类的，类所有的实例都共享静态变量，可以直接通过类名来访问它。静态变量在内存中只存在一份；

（2）静态方法：静态方法在类加载的时候就存在了，它不依赖于任何实例。所以静态方法必须有实现，也就是说它不能是抽象方法。只能访问所属类的静态字段和静态方法，方法中不能有 this 和 super 关键字；

（3）静态语句块：静态语句块在类初始化时运行一次；

（4）静态内部类：非静态内部类依赖于外部类的实例，而静态内部类不需要。静态内部类不能访问外部类的非静态的变量和方法；

（5）初始化顺序：静态变量和静态语句块优先于实例变量和普通语句块，静态变量和静态语句块的初始化顺序取决于它们在代码中的顺序。

**初始化补充**

存在继承的情况下，初始化顺序为：

1. 父类（静态变量、静态语句块）
2. 子类（静态变量、静态语句块）
3. 父类（实例变量、普通语句块）
4. 父类（构造函数）
5. 子类（实例变量、普通语句块）
6. 子类（构造函数）



### 31. super

1. 访问父类的构造方法
2. 访问父类的成员：如果某个方法已经被子类重写，仍然可以通过super访问父类的成员方法。
3. this和super不能同时出现在一个构造方法里



### 32. 为什么String被设置为final类

1. String在字符串常量池中的需要：字符串常量池在堆内存中是一个非常特殊的区域，当创建一个String对象的时候，如果字符串值已经存在于字符串常量池中，就不会创建新的字符串对象，而是引用已经存在的对象。

   因此如果多个对象共享同一个String对象，如果其中一个对象改了，其他的引用都会改变。但是其他对象可能并不想要改变，因此要保持String不能再有改动。

2. 由于String类不可变的特性，所以经常被用作HashMap的key，如果String类是可变的，内容改变，hashCode也会改变，当根据这个key从HashMap中取的时候有可能取不到value，或者取到错的value。

3. 不可变对象天生就是线程安全的，这样可以避免在多线程环境下对String做同步操作。



### 33. String、StringBuffer、StringBuilder

StringBuilder不是线程安全的，但是效率高。

StringBuffer对方法加了同步锁，是线程安全的。

StringBuilder使用：

```java
public class Test{
  public static void main(String args[]){
    StringBuffer sBuffer = new StringBuffer("菜鸟教程官网：");
    sBuffer.append("www");
    sBuffer.append(".runoob");
    sBuffer.append(".com");
    System.out.println(sBuffer);  
  }
}
```

**当用 String 类型来对字符串进行修改时，其实现方法是首先创建一个 StringBuffer，其次调用 StringBuffer 的 append() 方法，最后调用 StringBuffer 的 toString() 方法把结果返回。**



### 34. String str = “i” 与 String str = new String(“i”) 

String str = "i"的写法JVM会将其分配到常量池中。

String str = new String("i")的写法会将其分配到堆内存中，也就是字符串常量池。



### 35. IO流的分类

按功能分类分为输入流和输出流

按类型分类分为字节流和字符流

```yml
InputStream:
	FileInputStream:
	FilterInputStream:
		BufferedInputStream:
		DataInputStream:
		PushBackInputStream:
	PipedInputStream:
	SequenceInputStream:
	ByteArrayInputStream:
	ObjectInputStream:
	
OutputStream:
	FileOutputStream:
	FilterOutputStream:
		BufferedOutputStream:
		DataOutputStream:
		PushBackOutputStream:
	PipedInputStream:
	ByteArrayOutputStream:
	ObjectOutputStream:
```



### 36. BIO、NIO、AIO

BIO：Block IO同步阻塞式IO，就是我们平常使用的传统IO，它的特点是模式简单使用方便，并发处理能力低。同步阻塞I/O模式，数据的读取写入必须阻塞在一个线程内等待其完成。在活动连接数不是特别高（小于单机 1000）的情况下，这种模型是比较不错的，可以让每一个连接专注于自己的 I/O 并且编程模型简单，也不用过多考虑系统的过载、限流等问题。线程池本身就是一个天然的漏斗，可以缓冲一些系统处理不了的连接或请求。但是，当面对十万甚至百万级连接的时候，传统的BIO模型是无能为力的。因此，我们需要一种更高效的I/O处理模型来应对更高的并发量。

NIO：New IO 同步非阻塞 IO，是传统 IO 的升级，客户端和服务器端通过Channel（通道）通讯，实现了多路复用。NIO是一种同步非阻塞的I/O模型，在 Java1.4 中引入了NIO框架，对应java.nio包，提供了Channel , Selector，Buffer等抽象。NIO中的N可以理解为Non-blocking，不单纯是New。它支持面向缓冲的，基于通道的I/O操作方法。NIO提供了与传统BIO模型中的Socket和ServerSocket相对应的SocketChannel和ServerSocketChannel两种不同的套接字通道实现，两种通道都支持阻塞和非阻塞两种模式。阻塞模式使用就像传统中的支持一样，比较简单，但是性能和可靠性都不好；非阻塞模式正好与之相反。对于低负载、低并发的应用程序，可以使用BIO来提升开发速率和更好的维护性；对于高负载、高并发的（网络）应用，应使用NIO的非阻塞模式来开发。

AIO：Asynchronous IO 是 NIO 的升级，也叫 NIO2，实现了异步非堵塞 IO ，异步 IO 的操作基于事件和回调机制。也就是应用操作之后会直接返回，不会堵塞在那里，当后台处理完成，操作系统会通知相应的线程进行后续的操作。AIO 是异步 IO 的缩写，虽然 NIO 在网络操作中，提供了非阻塞的方法，但是 NIO 的 IO 行为还是同步的。对于 NIO 来说，我们的业务线程是在 IO 操作准备好时，得到通知，接着就由这个线程自行进行 IO 操作，IO操作本身是同步的。





## Java Exception

### 1. 什么情况执行finally

finally就是为了保证无论出现什么情况，finally里的代码都一定会被执行。

有以下几种情况不会被执行：

1. 程序还没有进入try catch就已经出现了异常，会直接结束。
2. 当程序在try中就已经写了exit方法。



### 2. 如果 catch 中 return 了，finally 还会执行吗

程序在执行到 return 时会首先将返回值存储在一个指定的位置，其次去执行 finally 块，最后再返回。

因此，对基本数据类型，在 finally 块中改变 return 的值没有任何影响，直接覆盖掉；而对引用类型是有影响的，返回的是在 finally 对 前面 return 语句返回对象的修改值。



### 3. Error和Exception

Error类和Exception都是Throwable的子类，主要使用中的区别在于：

Error 类： 一般是指与虚拟机相关的问题，如：系统崩溃、虚拟机错误、内存空间不足、方法调用栈溢出等。这类错误将会导致应用程序中断，仅靠程序本身无法恢复和预防；

Exception 类：分为运行时异常和受检查的异常。



### 4. throw和throws

（1）throw：在方法体内部，表示抛出异常，由方法体内部的语句处理；throw 是具体向外抛出异常的动作，所以它抛出的是一个异常实例；

（2）throws：在方法声明后面，表示如果抛出异常，由该方法的调用者来进行异常的处理；表示出现异常的可能性，并不一定会发生这种异常。



### 5. 常见的Exception

NullPointerException：当应用程序试图访问空对象时，则抛出该异常。

SQLException：提供关于数据库访问错误或其他错误信息的异常。

IndexOutOfBoundsException：指示某排序索引（例如对数组、字符串或向量的排序）超出范围时抛出。

FileNotFoundException：当试图打开指定路径名表示的文件失败时，抛出此异常。

IOException：当发生某种 I/O 异常时，抛出此异常。此类是失败或中断的 I/O 操作生成的异常的通用类。

ClassCastException：当试图将对象强制转换为不是实例的子类时，抛出该异常。

IllegalArgumentException：抛出的异常表明向方法传递了一个不合法或不正确的参数。





## Java Collection 

### 1. ArrayList和LinkedList

ArrayList底层基于数组实现，查找快，增删比较慢。

LinkedList底层基于双向链表实现，查找慢，增删比较快。

LinkedList 链表由一系列表项连接而成，一个表项包含 3 个部分：元素内容、前驱表和后驱表。链表内部有一个 header 表项，既是链表的开始也是链表的结尾。header 的后继表项是链表中的第一个元素，header 的前驱表项是链表中的最后一个元素。

**ArrayList 的增删未必就是比 LinkedList 要慢：**

1. 如果增删都是在末尾来操作【每次调用的都是 remove() 和 add()】，此时 ArrayList 就不需要移动和复制数组来进行操作了。如果数据量有百万级的时，速度是会比 LinkedList 要快的。
2. 如果删除操作的位置是在中间。由于 LinkedList 的消耗主要是在遍历上，ArrayList 的消耗主要是在移动和复制上（底层调用的是 arrayCopy() 方法，是 native 方法）。LinkedList 的遍历速度是要慢于 ArrayList 的复制移动速度的如果数据量有百万级的时，还是 ArrayList 要快。

ArrayList 用 for 循环遍历比 iterator 迭代器遍历快，LinkedList 用 iterator 迭代器遍历比 for 循环遍历快。所以说，当我们在做项目时，应该考虑到 List 集合的不同子类采用不同的遍历方式，能够提高性能。



### 2. ArrayList扩容机制

1. 当使用 add 方法的时候首先调用 ensureCapacityInternal 方法，传入 size+1 进去，检查是否需要扩充 elementData 数组的大小；
2. newCapacity = 扩充数组为原来的 1.5 倍(不能自定义)，如果还不够，就使用它指定要扩充的大小 minCapacity ，然后判断 minCapacity 是否大于 MAX_ARRAY_SIZE(Integer.MAX_VALUE – 8) ，如果大于，就取 Integer.MAX_VALUE；
3. ArrayList 中 copy 数组的核心就是 System.arraycopy 方法，将 original 数组的所有数据复制到 copy 数组中，这是一个本地方法。

 

### 3. HashMap的底层原理

HashMap中的每个节点在源码中的指向为：

```java
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    V value;
    Node<K,V> next;

    Node(int hash, K key, V value, Node<K,V> next) {
        this.hash = hash;
        this.key = key;
        this.value = value;
        this.next = next;
    }

    public final K getKey()        { return key; }
    public final V getValue()      { return value; }
    public final String toString() { return key + "=" + value; }

    public final int hashCode() {
        return Objects.hashCode(key) ^ Objects.hashCode(value);
    }

    public final V setValue(V newValue) {
        V oldValue = value;
        value = newValue;
        return oldValue;
    }

    public final boolean equals(Object o) {
        if (o == this)
            return true;
        if (o instanceof Map.Entry) {
            Map.Entry<?,?> e = (Map.Entry<?,?>)o;
            if (Objects.equals(key, e.getKey()) &&
                Objects.equals(value, e.getValue()))
                return true;
        }
        return false;
    }
}
```

> HashMap是基于hash表实现的，而hash表底层是由**数组**加**链表**实现的。
>
> HashMap其实就是一个Entry数组，Entry对象中包含了键和值，**其中next也是一个Entry对象，它就是用来处理hash冲突的，形成一个链表。**

HashMap的初始化方式：

```java
HashMap();
HashMap(int initialCapacity);
HashMap(int initialCapacity, float loadFactor);
```



**为什么默认的负载因子是0.75？**

**DEFAULT_LOAD_FACTOR**负载因子=0.75。

0.75的含义表达当HashMap的容量达到总容量的75%时HashMap会进行扩容。

>我们假设一下，如果我们将**负载因子设置成1**，会发生什么情况：
>
>我们将HashMap的负载因子设置成1也就是HashMap的容量必须要全部装满，才允许扩容，HashMap的容量如果要全部装满必定会伴随着大量的hash冲突，这时候put和get操作效率就会低下。
>
>**如果我们将负载因子设置成0.5**，会发生什么情况：
>
>这时HashMap的容量达到50%时就会进行扩容，这样虽然能减少hash冲突的概率，但是会存在还有一半的空间没有被使用到，会造成空间的利用率低下。这时1和0.5都不行，难道我们随便取个中间值可以吗。显然不是的，而是根据一个数学公式推导出来的，被叫做**牛顿二项式**。
>
>下面摘自StackOverFlow一个回答，在牛顿二项式公式下算出来的负载因子～0.69
>
>[HashMap底层实现原理 - 半分、 - 博客园 (cnblogs.com)](https://www.cnblogs.com/semi-sub/p/12953414.html)
>
>公式算出来为0.693，而0.75是HashMap为了后面方便计算，0.75*size=整数，方便后面HashMap容量达到这个整数进行扩容。



**为什么HashMap每次扩容都是2的指数倍？** --- 为了最小化hash冲突

每次HashMap resize()时，扩容后都是之前的容量的2倍。

我们可以看到putVal时，会去判断table[i]的值是否为空，以判断是否冲突、是否需要后续存储链表。

而数组下标i是怎么得出来的呢，是通过key的hash值&(n - 1)得到的数组下标i，n代表数组的长度。

在这里提另外一点，我们都知道数组下标i的值可以由hash%length(数组长度)得到，但是HashMap为什么没有采用这种方式了，而是采用hash&(length - 1)这种方式，是因为&运算速度是要高于%运算速度的，&运算是直接进行二进制位运算的。

我们假设一下**扩容是2的指数倍**，则n就会是2，4，8，16，32，64等这些2的指数倍，(n - 1)就会是1，3，7，15，31等这些数对应的二进制就是00001，00011，00111，01111，11111等这些数。我们再来看下这些二进制数&hash值对应的二进制数。



**HashMap为什么要引入红黑树？**

为什么HashMap使用红黑树作为底层的数据结构而不是完全平衡二叉树？

我们知道引入红黑树是为了解决链表的O(n)的时间复杂度的问题，达到一定的条件之后，链表就会自动转换为红黑树。

红黑树和完全平衡二叉树都可以解决链表查询O(n)的问题，因为树的结构可以将链表直线的查询效率从O(n)变成O(logn)。红黑树是一种相对平衡的二叉树，而完全平衡二叉树则是绝对平衡的。

假设HashMap引入完全平衡二叉树，每当key插入进来时，完全平衡二叉树为了保持绝对的平衡，就会对树进行左旋，右旋操作来保持树的绝对平衡。这时插入的效率就会低下。

而红黑树只需保持相对的平衡，并不会有过多的旋转操作，来使插入效率降低。完全平衡二叉树适合读多写少的场景，也就是get操作多，而put操作少。这时完全平衡二叉树的效率就会比红黑树的效率要高。



**HashMap的转红黑树的阀值为什么是8？**

```
* 0:    0.60653066
* 1:    0.30326533
* 2:    0.07581633
* 3:    0.01263606
* 4:    0.00157952
* 5:    0.00015795
* 6:    0.00001316
* 7:    0.00000094
* 8:    0.00000006
* more: less than 1 in ten million
```

大概意思就是在负载因子为0.75的基础上，链表长度达到8个元素的概率为0.00000006，这个概率几乎很小了，这个概率是怎么算出来的呢，是通过一个叫泊松分布概率统计得出来的。并不是随随便便定义的这个数字。



**HashMap达到什么条件才会转化为红黑树结构？**

在HashMap中有一个转变成红黑树的阀值**TREEIFY_THRESHOLD=8**。

除此之外，

所以可以得出转变成红黑树必须满足两个条件：

  **(1):链表节点必须达到8个**

  **(2):数组tab的长度必须大于等于64**



### 4. HashMap.put()

当我们想往一个 HashMap 中添加一对 key-value 时，系统首先会计算 key 的 hash 值，然后根据 hash 值确认在 table 中存储的位置。

这个计算过程就是使用二级制&运算计算出是否没有元素，如果没有元素，就直接插入，否则迭代该处元素链表并依次比较其 key 的 hash 值。如果两个 hash 值相等且 key 值相等(e.hash hash && ((k = e.key) key || key.equals(k)))，则用新的 Entry 的 value 覆盖原来节点的 value。如果两个 hash 值相等但 key 值不等 ，则进行插入操作。

JDK1.8，底层采用**数组 + 链表 / 红黑树**，并且把头插法改成了**尾插法**，主要是为了减少线程安全的问题，另外，当链表长度大于8，且数组长度大于64时，会把链表转化为红黑树处理，这个时候，就无关是头插还是尾插了，得按照红黑树的规则来插了。



### 5. HashMap.resize()

有两种方法会调用resize()：

1. 第一次调用HashMap的put方法的时候，会调用resize()方法对table数组进行初始化，如果不传入指定的数值，默认大小为16。
2. 扩容时会调用resize()，当size()>threshold的时候，table数组的大小翻倍。

每次扩容之后容量都是**翻倍**。扩容后要将原数组中的所有元素找到在新数组中合适的位置。



### 6. HashMap头插法改为尾插法

1. 头插法在并发下有致命问题，就是可能形成数据环，get 数据时死循环，而在 1.8 之前因为处理 hash 冲突的方式是用链表存放数据，使用头插法可以提升一定效率。

   但是在 1.8 之后这个效率提升就可有可无了，链表长度超过 7 就要考虑升级红黑树了，所以哪怕进行尾插遍历次数也会很有限，效率影响不大。

2. 就是因为 1.8 之后数据结构的变动，当链表长度达到阈值，升级为红黑树后头插法就不适用了，因为构建红黑树需要进行比对更新序列，也就不能去说是头插法还是尾插了。



### 7. HashMap的size是2的整数次方

1. 这样做总是能够保证 HashMap 的底层数组长度为 2 的 n 次方。当 length 为 2 的 n 次方时，h & (length – 1) 就相当于对 length 取模，而且速度比直接取模快得多，这是 HashMap 在速度上的一个优化。而且每次扩容时都是翻倍。
2. 如果 length 为 2 的次幂，则 length – 1 转化为二进制必定是 11111……的形式，在与 h 的二进制进行与操作时效率会非常的快，而且空间不浪费。但是，如果 length 不是 2 的次幂，比如：length 为 15，则 length – 1 为 14，对应的二进制为 1110，在于 h 与操作，最后一位都为 0 ，而 0001，0011，0101，1001，1011，0111，1101 这几个位置永远都不能存放元素了，空间浪费相当大，更糟的是这种情况中，数组可以使用的位置比数组长度小了很多，这意味着进一步增加了碰撞的几率，减慢了查询的效率，这样就会造成空间的浪费。

### 8. HashMap的get不能判断是否包含

HashMap 的 get 函数的返回值不能判断一个 key 是否包含在 map 中，因为 get 返回 null 有可能是不包含该 key，也有可能该 key 对应的 value 为 null。因为 HashMap 中允许 key 为 null，也允许 value 为 null。



### 9. ConcurrentHashMap

ConcurrentHashMap 参考了 JDK 8 HashMap 的实现，采用了数组 + 链表 + 红黑树的实现方式来设计，内部大量采用 CAS 操作。

ConcurrentHashMap 采用了非常精妙的”分段锁”策略，ConcurrentHashMap 的主干是个 Segment 数组。

Segment 继承了 ReentrantLock，所以它就是一种可重入锁（ReentrantLock)。在 ConcurrentHashMap，一个 Segment 就是一个子哈希表，Segment 里维护了一个 HashEntry 数组，并发环境下，对于不同 Segment 的数据进行操作是不用考虑锁竞争的。就按默认的 ConcurrentLevel 为 16 来讲，理论上就允许 16 个线程并发执行。

所以，对于同一个 Segment 的操作才需考虑线程同步，不同的 Segment 则无需考虑。Segment 类似于 HashMap，一个 Segment 维护着一个HashEntry 数组：

```java
final  Segment<K,V>[]  segments;
```

Segment 继承了 ReentrantLock，所以它就是一种可重入锁（ReentrantLock)。在 ConcurrentHashMap，一个 Segment 就是一个子哈希表，Segment 里维护了一个 HashEntry 数组，并发环境下，对于不同 Segment 的数据进行操作是不用考虑锁竞争的。就按默认的 ConcurrentLevel 为 16 来讲，理论上就允许 16 个线程并发执行。

所以，对于同一个 Segment 的操作才需考虑线程同步，不同的 Segment 则无需考虑。Segment 类似于 HashMap，一个 Segment 维护着一个HashEntry 数组：

```java
transient  volatile  HashEntry<K,V>[]  table;
```

HashEntry 是目前我们提到的最小的逻辑处理单元了。一个 ConcurrentHashMap 维护一个 Segment 数组，一个 Segment 维护一个 HashEntry 数组。因此，ConcurrentHashMap 定位一个元素的过程需要进行两次 Hash 操作。第一次 Hash 定位到 Segment，第二次 Hash 定位到元素所在的链表的头部。



### 10. HashMap和ConcurrentHashMap

HashMap 不是线程安全的，而 ConcurrentHashMap 是线程安全的。

ConcurrentHashMap 采用锁分段技术，将整个Hash桶进行了分段segment，也就是将这个大的数组分成了几个小的片段 segment，而且每个小的片段 segment 上面都有锁存在，那么在插入元素的时候就需要先找到应该插入到哪一个片段 segment，然后再在这个片段上面进行插入，而且这里还需要获取 segment 锁，这样做明显减小了锁的粒度。



### 11. HashTable和ConcurrentHashMap

HashTable 和 ConcurrentHashMap 相比，效率低。 Hashtable 之所以效率低主要是使用了 synchronized 关键字对 put 等操作进行加锁，而 synchronized 关键字加锁是对整张 Hash 表的，即每次锁住整张表让线程独占，致使效率低下，而 ConcurrentHashMap 在对象中保存了一个 Segment 数组，即将整个 Hash 表划分为多个分段。

而每个Segment元素，即每个分段则类似于一个Hashtable；这样，在执行 put 操作时首先根据 hash 算法定位到元素属于哪个 Segment，然后对该 Segment 加锁即可，因此， ConcurrentHashMap 在多线程并发编程中可是实现多线程 put操作。



### 12. HashSet

HashSet 的实现是依赖于 HashMap 的，HashSet 的值都是存储在 HashMap 中的。在 HashSet 的构造法中会初始化一个 HashMap 对象，HashSet 不允许值重复。

因此，HashSet 的值是作为 HashMap 的 key 存储在 HashMap 中的，当存储的值已经存在时返回 false。



### 13. iterator

迭代器是一种设计模式，它是一个对象，它可以遍历并选择序列中的对象，而开发人员不需要了解该序列的底层结构。迭代器通常被称为“轻量级”对象，因为创建它的代价小。Java 中的 Iterator 功能比较简单，并且只能单向移动：　　

1. 使用方法 iterator() 要求容器返回一个 Iterator。第一次调用 Iterator 的 next() 方法时，它返回序列的第一个元素。注意：iterator() 方法是 java.lang.Iterable 接口，被 Collection 继承。　　
2. 使用 next() 获得序列中的下一个元素。　
3. 使用 hasNext() 检查序列中是否还有元素。　　
4. 使用 remove() 将迭代器新返回的元素删除。　





## Java Concurrency(TBD)

### 1. 并发和并行

concurrency和parallelism

1. 并行是指两个或者多个事件在同一时刻发生；而并发是指两个或多个事件在同一时间间隔发生；
2. 并行是在不同实体上的多个事件，并发是在同一实体上的多个事件；
3. 在一台处理器上“同时”处理多个任务，在多台处理器上同时处理多个任务。如 Hadoop 分布式集群。所以并发编程的目标是充分的利用处理器的每一个核，以达到最高的处理性能。



### 2. 线程和进程

**进程：**是程序运行和资源分配的基本单位，一个程序至少有一个进程，一个进程至少有一个线程。进程在执行过程中拥有独立的内存单元，而多个线程共享内存资源，减少切换次数，从而效率更高。

**线程：**是进程的一个实体，是 cpu 调度和分派的基本单位，是比程序更小的能独立运行的基本单位。同一进程中的多个线程之间可以并发执行。



### 3. 守护进程

**当 JVM 中不存在任何一个正在运行的非守护线程时，则 JVM 进程即会退出。**

守护线程（即 Daemon thread），是个服务线程，准确地来说就是服务其他的线程



### 4. 创建线程的几种方式

1. 继承Thread类创建线程
2. 实现Runnable接口创建线程
3. 通过 Callable 和 Future 创建线程
4. **通过线程池创建线程**，// 线程通过线程池创建



### 5. Runnable和Callable

1. Runnable 接口中的 run() 方法的返回值是 void，它做的事情只是纯粹地去执行 run() 方法中的代码而已；
2. Callable 接口中的 call() 方法是有返回值的，是一个泛型，和 Future、FutureTask 配合可以用来获取异步执行的结果。



### 6. 线程状态和转换

Thread 的源码中定义了6种状态：

首先明确线程在Java的源代码中定义了六种基本状态：

new\ runnable\ blocked\ waiting\ time waiting\ terminated

>1. 初始(NEW)：新创建了一个线程对象，但还没有调用start()方法。
>2. 运行(RUNNABLE)：Java线程中将就绪（ready）和运行中（running）两种状态笼统的称为“运行”。
>     线程对象创建后，其他线程(比如main线程）调用了该对象的start()方法。该状态的线程位于可运行线程池中，等待被线程调度选中，获取CPU的使用权，此时处于就绪状态（ready）。就绪状态的线程在获得CPU时间片后变为运行中状态（running）。
>3. 阻塞(BLOCKED)：表示线程阻塞于锁。
>4. 等待(WAITING)：进入该状态的线程需要等待其他线程做出一些特定动作（通知或中断）。
>5. 超时等待(TIMED_WAITING)：该状态不同于WAITING，它可以在指定的时间后自行返回。
>6. 终止(TERMINATED)：表示该线程已经执行完毕。

![img](https://img-blog.csdnimg.cn/20181120173640764.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3BhbmdlMTk5MQ==,size_16,color_FFFFFF,t_70)



各个状态的详细说明：

1. 初始状态(new)

   当用new操作符创建一个新线程时，如`new Thread(r)`，该线程还没有开始运行。这意味着它的状态是new。当一个线程处于新创建状态时，程序还没有开始运行线程中的代码。在线程运行之前还有一些基础工作要做。

2. 可运行线程(runnable---ready)

   就绪状态只是说你资格运行，调度程序没有挑选到你，你就永远是就绪状态。
   调用线程的start()方法，此线程进入就绪状态。
   当前线程sleep()方法结束，其他线程join()结束，等待用户输入完毕，某个线程拿到对象锁，这些线程也将进入就绪状态。
   当前线程时间片用完了，调用当前线程的yield()方法，当前线程进入就绪状态。
   锁池里的线程拿到对象锁后，进入就绪状态。

3. 运行中(runnable--running)

   线程调度程序从可运行池中选择一个线程作为当前线程时线程所处的状态。这也是线程进入运行状态的唯一的一种方式。

4. 阻塞状态(blocked)

   阻塞状态是线程阻塞在进入synchronized关键字修饰的方法或代码块(获取锁)时的状态。

5. 等待(waiting)

   处于这种状态的线程不会被分配CPU执行时间，它们要等待被显式地唤醒，否则会处于无限期等待的状态。

6. 超时等待(timed_waiting)

   处于这种状态的线程不会被分配CPU执行时间，不过无须无限期等待被其他线程显示地唤醒，在达到一定时间后它们会自动唤醒。

[Java线程的6种状态及切换(透彻讲解)_潘建南的博客-CSDN博客_线程状态](https://blog.csdn.net/pange1991/article/details/53860651)



### 7. sleep()和wait()

sleep()和wait()主要有五大区别：

1. 使用限制：

   使用sleep()让线程休眠，时间到了之后线程会继续进行。在任何地方都能用，但是要捕获InterruptedException异常。

   ```java 
   try {
       Thread.sleep(4000);
   } catch(InterruptedException e) {
       e.printStackTrace();
   }
   ```

   使用wait()一定是在同步里，必须放在synchronized块里面，同样需要捕获InterruptedException异常。

   ```java
   synchronized (lock){
       try {
           lock.wait();
       } catch (InterruptedException e) {
           e.printStackTrace();
       }
   }
   ```

   而且 wait 还需要额外的方法 notify/ notifyAll 进行唤醒，它们同样需要放在 synchronized 块里面，且获取对象的锁。

   当然也可以使用带时间的 wait(long millis) 方法，时间一到，无需其他线程唤醒，也会重新竞争获取对象的锁继续执行。

   ```java
   synchronized (lock) {
       // 随机唤醒
       lock.notify();
   
       // 唤醒全部
       lock.notifyAll();
   }
   ```

2. 使用场景：

   sleep()多用于当前线程的休眠，或者轮询暂停操作，wait则多用于多线程之间的通信。

3. 所属类：

   sleep()是Thread类的静态本地方法，wait()是Object的本地方法。

   > java.lang.Thread#sleep

   ```java
   public static native void sleep(long millis) throws InterruptedException;
   ```

   > java.lang.Object#wait

   ```java
   public final native void wait(long timeout) throws InterruptedException;
   ```

4. 释放锁：

   wait()会释放当前线程对lock对象锁的持有，但是sleep不会。

   ```java
   Object lock = new Object();
   synchronized (lock) {
       try {
           lock.wait(3000L);
           Thread.sleep(2000L);
       } catch (InterruptedException e) {
           e.printStackTrace();
       }
   }
   ```

5. 线程切换：

   sleep 会让出 CPU 执行时间且强制上下文切换，而 wait 则不一定，wait 后可能还是有机会重新竞争到锁继续执行的。



### 8. run()和start()

首先明确线程在Java的源代码中定义了六种基本状态：

new\ runnable\ blocked\ waiting\ time waiting\ terminated

>1. 初始(NEW)：新创建了一个线程对象，但还没有调用start()方法。
>2. 运行(RUNNABLE)：Java线程中将就绪（ready）和运行中（running）两种状态笼统的称为“运行”。
>     线程对象创建后，其他线程(比如main线程）调用了该对象的start()方法。该状态的线程位于可运行线程池中，等待被线程调度选中，获取CPU的使用权，此时处于就绪状态（ready）。就绪状态的线程在获得CPU时间片后变为运行中状态（running）。
>3. 阻塞(BLOCKED)：表示线程阻塞于锁。
>4. 等待(WAITING)：进入该状态的线程需要等待其他线程做出一些特定动作（通知或中断）。
>5. 超时等待(TIMED_WAITING)：该状态不同于WAITING，它可以在指定的时间后自行返回。
>6. 终止(TERMINATED)：表示该线程已经执行完毕。
Java中启动线程

1. start方法：通过该方法启动线程的同时也创建了一个线程，真正实现了多线程。**无需等待run()方法中的代码执行完毕，就可以接着执行下面的代码。此时start()的这个线程处于就绪状态，当得到CPU的时间片后就会执行其中的run()方法。**这个run()方法包含了要执行的这个线程的内容，run()方法运行结束，此线程也就终止了。
2. run方法：通过run方法启动线程其实就是调用一个类中的方法，**当作普通的方法的方式调用**。并没有创建一个线程，程序中依旧只有一个主线程，必须等到run()方法里面的代码执行完毕，才会继续执行下面的代码，这样就没有达到写线程的目的。

```java
public class Test {
    public static void main(String[] args) {
        Thread t = new Thread(){
            public void run() {
                pong();
            }
        };
        t.run();
        System.out.println("ping");
    }
 
    static void pong() {
        System.out.println("pong");
    }
}
// pong
// ping
```

如果`t.start();`那么就会输出ping pong。

总结：

说到底，run()只是一个普通的方法，只是这个方法内部调用的别的方法，所以在main函数中还是按照一行一行执行的，只是一个主线程按照顺序执行而已。如果直接执行run方法，是不会创建一个新的线程的，因为没有start()。

但是start()方法会创建一个新的线程，并且执行，和主线程就没有关系。这也就是为什么t.start()会直接输出ping pong，因为代码会直接执行print("ping")，输出pong的任务交给t。



### 9. TBD Java如何保证线程安全

1. 原子性：**提供互斥访问，同一时刻只能有一个线程对数据进行操作**，(atomic,synchronized)
2. 可见性：**一个线程对主内存的修改可以及时地被其他线程看到**，(synchronized,volatile)
3. 有序性：**一个线程观察其他线程中的指令执行顺序，由于指令重排序，该观察结果一般杂乱无序**，(happens-before)



### 10. Java线程同步的方法

1. 使用Synchronized关键字
2. wait和notify
3. 使用特殊变量volatile实现同步
4. 使用可重入锁实现同步
5. 使用阻塞队列实现线程同步
6. 使用信号semaphore



### 11. Thread.interrupt()

在一个线程对象上调用 interrupt() 方法，真正有影响的是 wait、join、sleep 方法，当然这 3 个方法包括它们的重载方法。请注意：上面这三个方法都会抛出 InterruptedException。



首先，当我们调用线程的interrupt方法的时候，有两个作用：

1. 如果线程此时处于阻塞状态，例如调用了wait()方法，就会立刻退出，抛出InterruptedException异常，线程可以通过捕捉InterruptedException来做处理。

   实际上，Object.wait()、Thread.join()、Thread.sleep()被阻塞的时候，调用interrupt方法都会抛出一个InterruptedException。

2. 如果此线程正处于运行状态，线程就可以不受影响，继续运行，仅仅是线程的中断标记被设置为true。所以，程序可以在适当的位置通过调用isInterrupted()方法来查看自己是否被中断，并执行退出操作。



### 12. ThreadLocal

```java
package com.gatsby.chapter14_concurrency;

/**
 * @author -- gatsby
 * @date -- 2022/6/10
 * @description -- ThreadLocal 测试
 */


public class ThreadLocalTest {
    static ThreadLocal<String> local = new ThreadLocal<>();

    static void print(String s) {
        System.out.println(s + " " + local.get());
        // 清除本地内存中的变量
        // local.remove();
    }

    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(() -> {
            local.set("local 1");
            print("thread 1");
            // 因为print之后会把ThreadLocal中的变量清除，此时再获取一次
            System.out.println("after remove : " + local.get());
        });

        Thread t2 = new Thread(() -> {
            local.set("local 2");
            print("thread 2");
            // 因为print之后会把ThreadLocal中的变量清除，此时再获取一次
            System.out.println("after remove : " + local.get());
        });

        t1.start();
        Thread.sleep(1000);
        t2.start();
        Thread.sleep(1000);
        System.out.println(local.get());
        Thread.sleep(1000);
        /*
        ThreadLocal的价值主要在于线程隔离，ThreadLocal中的数据仅属于当前线程，其本地值对于其他线程是不可见的。

这个例子可以从上面的代码中发现，虽然local是一个静态变量，但是由于set是通过t1和t2的线程去赋值的，所以main线程通过local.get去获取local的值获取的结果是null
		*/
        local.set("thread 3");
        System.out.println(local.get());
    }
}
thread 1 local 1
after remove : local 1
thread 2 local 2
after remove : local 2
null
thread 3
```

通过输出的结果可以看出，在ThreadLocal中，每一个线程都绑定了一个独立的值，这些值的对象是线程的私有属性。



### 12-1. ThreadLocal的使用场景

ThreadLocal是解决线程安全问题的一个较好的方案，它通过为每个线程提供一个独立的本地值去解决并发访问的冲突问题。

在很多情况下，使用ThreadLocal比直接使用同步机制（如synchronized）解决线程安全问题更简单、更方便，且结果程序拥有更高的并发性。

ThreadLocal的使用场景大致可以分为以下两个类别：

1. 线程隔离

   ThreadLocal的价值主要在于线程隔离，ThreadLocal中的数据仅属于当前线程，其本地值对于其他线程是不可见的。

   这个例子可以从上面的代码中发现，虽然local是一个静态变量，但是由于set是通过t1和t2的线程去赋值的，所以main线程通过local.get去获取local的值获取的结果是null。

   由于各个线程之间的数据是相互隔离的，不需要加锁，也就避免了加锁带来的性能损失。大大提升了并发的性能。

   线程隔离的情况下的场景有：

   1. 为每一个用户绑定用户信息
   2. 数据库连接
   3. HTTP请求

   常见的ThreadLocal使用场景为数据库连接独享、Session数据管理等。在“线程隔离”场景中，使用ThreadLocal的典型案例为：可以为每个线程绑定一个数据库连接，使得这个数据库连接为线程所独享，从而避免数据库连接被混用而导致操作异常问题。

2. 跨函数传递数据

   通常会涉及到同一个线程之内的多个类、方法传递数据，如果没有使用ThreadLocal的话，传递数据就需要依靠返回值和传参，这样无形中增加了类和方法之间的耦合度。

   由于ThreadLocal的特性，同一线程在某些地方进行设置，在随后的任意地方都可以获取到。线程执行过程中所执行到的函数都能读写ThreadLocal变量的线程本地值，从而可以方便地实现跨函数的数据传递。使用ThreadLocal保存函数之间需要传递的数据，在需要的地方直接获取，也能避免通过参数传递数据带来的高耦合。



### 12-2. ThreadLocal的内部结构

在早期的JDK版本中，ThreadLocal的内部结构是一个Map，其中每一个线程实例作为Key，线程在“线程本地变量”中绑定的值为Value（本地值）。早期版本中的Map结构，其拥有者为ThreadLocal，每一个ThreadLocal实例拥有一个Map实例。

在JDK 8版本中，ThreadLocal的内部结构发生了演进，虽然还是使用了Map结构，但是Map结构的拥有者已经发生了变化，其拥有者为Thread（线程）实例，每一个Thread实例拥有一个Map实例。另外，Map结构的Key值也发生了变化：新的Key为ThreadLocal实例。

每一个线程在获取本地值时，**都会将ThreadLocal实例作为Key从自己拥有的ThreadLocalMap中获取值**，别的线程无法访问自己的ThreadLocalMap实例，自己也无法访问别人的ThreadLocalMap实例，达到相互隔离，互不干扰。

改动的主要区别在于：

1. 拥有者发生了变化：新版本的ThreadLocalMap拥有者为Thread，早期版本的ThreadLocalMap拥有者为ThreadLocal。
2. Key发生了变化：新版本的Key为ThreadLocal实例，早期版本的Key为Thread实例。

更新之后的版本优势在于：

1. 每个ThreadLocalMap存储的“Key-Value对”数量变少。早期版本的“Key-Value对”数量与线程个数强关联，若线程数量多，则ThreadLocalMap存储的“Key-Value对”数量也多。新版本的ThreadLocalMap的Key为ThreadLocal实例，多线程情况下ThreadLocal实例比线程数少。
2. 早期版本ThreadLocalMap的拥有者为ThreadLocal，在Thread（线程）实例销毁后，ThreadLocalMap还是存在的；新版本的ThreadLocalMap的拥有者为Thread，现在当Thread实例销毁后，ThreadLocalMap也会随之销毁，在一定程度上能减少内存的消耗。



### 12-2. ThreadLocal的使用规范

编程规范有云：ThreadLocal实例作为ThreadLocalMap的Key，针对一个线程内的所有操作是共享的，所以建议设置static修饰符，以便被所有的对象共享。

由于静态变量会在类第一次被使用时装载，只会分配一次存储空间，此类的所有实例都会共享这个存储空间，所以使用static修饰ThreadLocal就会节约内存空间。另外，为了确保ThreadLocal实例的唯一性，除了使用static修饰之外，还会使用final进行加强修饰，以防止其在使用过程中发生动态变更。

```java
// 推荐使用static final线程本地变量
private static final ThreadLocal<T> threadLocal = new ThreadLocal<T>();
```

以上代码中，为什么ThreadLocal实例除了添加static final修饰外，还常常添加private修饰呢？

主要目的是缩小使用的范围，尽可能不让他人引用。

凡事都有两面性，使用static、final修饰ThreadLocal实例也会带来副作用，使得Thread实例内部的ThreadLocalMap中Entry的Key在Thread实例的生命期内将始终保持为非null，从而导致Key所在的Entry不会被自动清空，这就会让Entry中的Value指向的对象一直存在强引用，于是Value指向的对象在线程生命期内不会被释放，最终导致内存泄漏。

所以，在使用完static、final修饰的ThreadLocal实例之后，**必须调用remove()来进行显式的释放操作**。如果使用线程池，可以定制线程池的afterExecute()方法（任务执行完成之后的钩子方法），在任务执行完成之后，调用ThreadLocal实例的remove()方法对其进行释放，从而使得其线程内部的Entry得以释放，参考的代码如下：







### 13. synchronized





### 14. synchronized的使用场景





### 15. jdk1.6之后的synchronized关键字做了哪些优化？





### 16. synchronized和ReentrantLock








### 17. synchronized和volatile







### 18. ReentrantReadWriteLock 







### 19. 乐观锁(CAS, compare and swap)

[重新认识CAS 跟 AQS 你了解乐观锁和悲观锁吗？ - Java野生程序猿 - 博客园 (cnblogs.com)](https://www.cnblogs.com/jiahaoJAVA/p/11309668.html)

















### 20. 乐观锁常用的两种实现方式

乐观锁常用的两种机制是版本号和CAS

- 版本号机制

  一般是在数据表中加入一个版本号version字段，表示数据被修改的次数，当数据被修改时，version会向上累加。当线程 A 要更新数据值时，在读取数据的同时也会读取 version 值，在提交更新时，若刚才读取到的 version 值为当前数据库中的 version 值相等时才更新，否则重试更新操作，直到更新成功。

- CAS

  Compare And Swap，是一种著名的无锁算法。

  无锁算法也就是在不使用锁的条件写实现多线程之间的变量同步，也就是在没有线程阻塞的情况下实现变量的同步。

  CAS涉及到三个主要的参数，

  1. 需要读写的内存值V
  2. 进行比较的新的数值A
  3. 将要被写入的数值B

  当且仅当，内存V的数值等于A的时候，使用数值B来进行写入。这是一个原子操作，否则不会进行任何操作，也就是一个自旋，会导致CPU空转，进行不断地重试。



### 21. CAS和synchronized

一般来说，CAS比较适用于那些写操作较少的场景（读场景较多）

synchronized适用于写比较多的情况（因为一般来说，同时写的场景容易出现冲突）

1. 对于资源竞争，线程冲突较少的情况，CAS基于硬件实现，不需要进入内核，不需要切换线程，自旋的几率会比较小；使用synchronized同步锁进行线程阻塞和唤醒切换用户态内核态的切换操作，会额外浪费CPU，这种情况会更加适用于CAS。
2. 对于资源竞争严重（线程冲突严重）的情况，如果采用CAS的话，会出现大量自旋，CPU资源被浪费，这种情况直接采用synchronized会更加合适。











### 22. 悲观锁









### 23. Java原子类









### 24. atomic的原理







### 25. 同步器AQS





### 26. AQS对于资源的共享模式







### 27. semaphore和ReenterantLock







### 28. Cyclic和CountDownLatch







### 29. Thread pool

Java创建线程的代价非常高，需要JVM和OS配合创建：

1. 因为需要为线程分配和初始化大量的内存块，其中至少包含1MB的占内存。
2. 需要进行系统调用，以便在OS中创建和注册本地线程。



Java高并发应用频繁创建和销毁线程的操作是非常低效的，而且是不被编程规范所允许的。如何降低Java线程的创建成本？必须使用到线程池。使用线程池主要有两个方面的优势。

1. **提升性能**：线程池能独立负责线程的创建、维护和分配。在执行大量异步任务时，可以不需要自己创建线程，而是将任务交给线程池去调度。线程池能尽可能使用空闲的线程去执行异步任务，最大限度地对已经创建的线程进行复用，使得性能提升明显。
2. **线程管理**：每个Java线程池会保持一些基本的线程统计信息，例如完成的任务数量、空闲时间等，以便对线程进行有效管理，使得能对所接收到的异步任务进行高效调度。



### 30. 创建线程池的常用参数







### 31. execute()和submit()







### 32. Fork和Join并行框架的理解





### 33. JDK中的并发容器







### 34. CopyOnWriteArrayList









### 35. BlockingQueue







### 36. Java内存模型





### 37. Java线程池

线程池（ThreadPool）是一种基于池化思想管理和使用线程的机制。它是将多个线程预先存储在一个“池子”内，当有任务出现时可以避免重新创建和销毁线程所带来性能开销，只需要从“池子”内取出相应的线程执行对应的任务即可。

在阿里巴巴开发规范手册中规定，线程资源必须通过线程池创建，不允许在应用中自行显式创建线程。使用线程池的好处是减少在创建和销毁线程上所花的时间以及系统资源的开销，解决资源不足的问题。如果不使用线程池，有可能造成系统创建大量同类线程而导致消耗完内存或者“过度切换”的问题。

线程池不允许使用 Executors，而是通过ThreadPoolExecutor的方式。

> 使用Executors的弊端在于，FixedThreadPool 和 SingleThread Pool会导致请求队列太长，导致OOM错误。
>
> CachedThreadPool 和 ScheduledThreadPool允许创建的线程数量太多，有可能导致OOM。





### 38. ThreadPoolExecutor

[java线程池ThreadPoolExecutor类使用详解 - bigfan - 博客园 (cnblogs.com)](https://www.cnblogs.com/dafanjoy/p/9729358.html)

[线程池详解（ThreadPoolExecutor） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/34405230)

ThreadPoolExecutor的构造函数

```java 
public ThreadPoolExecutor(int corePoolSize,
                          int maximumPoolSize,
                          long keepAliveTime,
                          TimeUnit unit,
                          BlockingQueue<Runnable> workQueue) {
    this(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue,
         Executors.defaultThreadFactory(), defaultHandler);
}
```

corePoolSize：指定了线程池中的线程数量，它的数量决定了添加的任务是开辟新的线程去执行，还是放到workQueue任务队列中去；

maximumPoolSize：指定了线程池中的最大线程数量，这个参数会根据你使用的workQueue任务队列的类型，决定线程池会开辟的最大线程数量；

keepAliveTime：当线程池中空闲线程数量超过corePoolSize时，多余的线程会在多长时间内被销毁；

unit：keepAliveTime的单位

workQueue：任务队列，被添加到线程池中，但尚未被执行的任务；它一般分为直接提交队列、有界任务队列、无界任务队列、优先任务队列几种；

threadFactory：线程工厂，用于创建线程，一般用于默认即可

handle：拒绝策略，当任务太多的时候，选择什么拒绝策略

[ThreadPoolExecutor使用详解 - WakamiyaShinobu - 博客园 (cnblogs.com)](https://www.cnblogs.com/zedosu/p/6665306.html)

```java
package com.gatsby.chapter14_concurrency;

import java.util.concurrent.*;
import java.util.concurrent.atomic.AtomicInteger;

/**
 * @author -- gatsby
 * @date -- 2022/6/8
 * @description -- 线程池测试
 */


public class ThreadPoolExecutorTest {
    private static int CORE_POOL_SIZE = 6;
    private static int MAXIMUM_POOL_SIZE = 10;
    private static int KEEP_ALIVE_TIME = 60;
    private static TimeUnit TIME_UNIT = TimeUnit.SECONDS;
    private static BlockingDeque<Runnable> BLOCKING_QUEUE = new LinkedBlockingDeque();

    /*
    创建一个线程池（完整的入参）
    核心线程数为 5
    最大线程数为 10
    存活时间为 60s
    工作队列为 LinkedBlockingDeque
    线程工厂为默认的 defaultThreadFactory
    拒绝策略为抛出异常
     */
    private static ExecutorService THREAD_POOL
            = new ThreadPoolExecutor(CORE_POOL_SIZE, MAXIMUM_POOL_SIZE, KEEP_ALIVE_TIME,
            TIME_UNIT, BLOCKING_QUEUE, Executors.defaultThreadFactory(),
            new ThreadPoolExecutor.AbortPolicy());

    public void destory() {
        if (THREAD_POOL != null) {
            THREAD_POOL.shutdown();
        }
    }

    public ExecutorService getThreadPool() {
        return THREAD_POOL;
    }

    private class CustomThreadFactory implements ThreadFactory {
        private AtomicInteger count = new AtomicInteger(0);

        @Override
        public Thread newThread(Runnable r) {
            Thread t = new Thread(r);
            String threadName = ThreadPoolExecutorTest.class.getName() + count.addAndGet(1);
            System.out.println(threadName);
            t.setName(threadName);
            return t;
        }
    }

    private class CustomRejectionExecutionHandler implements RejectedExecutionHandler {
        @Override
        public void rejectedExecution(Runnable r, ThreadPoolExecutor executor) {
            try {
                executor.getQueue().put(r);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }


    public static void main(String[] args) {
        ThreadPoolExecutorTest test = new ThreadPoolExecutorTest();
        ExecutorService pool = test.getThreadPool();
        for (int i = 0; i < 10; ++i) {
            System.out.println("Commit the " + i + "st task.");

            pool.execute(new Runnable() {
                @Override
                public void run() {
                    try {
                        System.out.println(">>>Task is running=====");
                        TimeUnit.SECONDS.sleep(1);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            });
        }
    }
}
```



### 39. 线程池的工作流程

<img src="https://pic4.zhimg.com/v2-885830dd498528ca7e277f9d930fcac3_r.jpg" alt="preview" style="zoom:50%;" />



1. 默认情况下，创建了线程池之后并不会直接创建线程，而是有任务提交的时候才会创建线程来进行处理。（除非调用prestartCoreThread或prestartAllCoreThreads方法）
2. 当线程数小于核心线程数时，每提交一个任务就创建一个线程来执行，即使当时有线程处于空闲状态，直到当前的线程数到达了核心线程数。
3. 当前线程数到达核心线程数之后，如果此时还在提交任务，这些任务会被存储到工作队列中，等到有空闲的线程再来工作队列中取得任务。
4. 当前线程数到达核心线程数并且工作队列也满了之后，如果此时还提交任务，那么会继续创建新的线程来处理任务，直到到达最大线程数。
5. 当前线程数达到最大线程数并且工作队列也已经满了，如果此时还提交任务，那么就需要采用拒绝策略，也称为饱和策略。
6. 如果某个线程的控线时间超过了keepAliveTime，那么将被标记为可回收的，并且当前线程池的当前大小超过了核心线程数时，这个线程将被终止。



### 40. 线程池的工作队列

如果新请求的到达速率超过了线程池的处理速率，那么新到来的请求将累积起来。在线程池中，这些请求会在一个由Executor管理的Runnable队列中等待，而不会像线程那样去竞争CPU资源。

常见的工作队列有以下几种，前三种用的最多。

1. ArrayBlockingQueue：列表形式的工作队列，必须要有初始队列大小，有界队列，先进先出。
2. LinkedBlockingQueue：链表形式的工作队列，可以选择设置初始队列大小，有界/无界队列，先进先出。
3. SynchronousQueue：SynchronousQueue不是一个真正的队列，而是一种在线程之间移交的机制。要将一个元素放入SynchronousQueue中，必须有另一个线程正在等待接受这个元素。如果没有线程等待，并且线程池的当前大小小于最大值，那么ThreadPoolExecutor将创建一个线程, 否则根据饱和策略，这个任务将被拒绝。使用直接移交将更高效，因为任务会直接移交给执行它的线程，而不是被首先放在队列中，然后由工作者线程从队列中提取任务。只有当线程池是无解的或者可以拒绝任务时，SynchronousQueue才有实际价值.
4. PriorityBlockingQueue：优先级队列，有界队列，根据优先级来安排任务，任务的优先级是通过自然顺序或Comparator（如果任务实现了Comparator）来定义的。
5. DelayedWorkQueue：延迟的工作队列，无界队列。



### 41. 饱和策略（拒绝策略）

当有界队列被填满后，饱和策略开始发挥作用。ThreadPoolExecutor的饱和策略可以通过调用setRejectedExecutionHandler来修改。（如果某个任务被提交到一个已被关闭的Executor时，也会用到饱和策略）。饱和策略有以下四种，一般使用默认的AbortPolicy。

1. AbortPolicy：中止策略。默认的饱和策略，抛出未检查的RejectedExecutionException。调用者可以捕获这个异常，然后根据需求编写自己的处理代码。
2. DiscardPolicy：抛弃策略。当新提交的任务无法保存到队列中等待执行时，该策略会悄悄抛弃该任务。
3. DiscardOldestPolicy：抛弃最旧的策略。当新提交的任务无法保存到队列中等待执行时，则会抛弃下一个将被执行的任务，然后尝试重新提交新的任务。（如果工作队列是一个优先队列，那么“抛弃最旧的”策略将导致抛弃优先级最高的任务，因此最好不要将“抛弃最旧的”策略和优先级队列放在一起使用）。
4. CallerRunsPolicy：调用者运行策略。该策略实现了一种调节机制，该策略既不会抛弃任务，也不会抛出异常，而是将某些任务回退到调用者（调用线程池执行任务的主线程），从而降低新任务的流程。它不会在线程池的某个线程中执行新提交的任务，而是在一个调用了execute的线程中执行该任务。当线程池的所有线程都被占用，并且工作队列被填满后，下一个任务会在调用execute时在主线程中执行（调用线程池执行任务的主线程）。由于执行任务需要一定时间，因此主线程至少在一段时间内不能提交任务，从而使得工作者线程有时间来处理完正在执行的任务。在这期间，主线程不会调用accept，因此到达的请求将被保存在TCP层的队列中。如果持续过载，那么TCP层将最终发现它的请求队列被填满，因此同样会开始抛弃请求。当服务器过载后，这种过载情况会逐渐向外蔓延开来——从线程池到工作队列到应用程序再到TCP层，最终达到客户端，导致服务器在高负载下实现一种平缓的性能降低。





### 42. 线程工厂

每当线程池需要创建一个线程时，都是通过线程工厂方法来完成的。在ThreadFactory中只定义了一个方法newThread，每当线程池需要创建一个新线程时都会调用这个方法。Executors提供的线程工厂有两种，一般使用默认的，当然如果有特殊需求，也可以自己定制。

1. DefaultThreadFactory：默认线程工厂，创建一个新的、非守护的线程，并且不包含特殊的配置信息。
2. PrivilegedThreadFactory：通过这种方式创建出来的线程，将与创建privilegedThreadFactory的线程拥有相同的访问权限、 AccessControlContext、ContextClassLoader。如果不使用privilegedThreadFactory， 线程池创建的线程将从在需要新线程时调用execute或submit的客户程序中继承访问权限。
3. 自定义线程工厂：可以自己实现ThreadFactory接口来定制自己的线程工厂方法。



























## JVM









## SSM

### 1. Spring框架的优点

1. Spring轻量级，基础包大概需要2MB
2. 控制反转IoC：Spring 通过控制反转实现了松散耦合，对象们给出它们的依赖，而不是创建或查找依赖的对象们。
3. 面向切面的编程(AOP)：Spring 支持面向切面的编程，并且把应用业务逻辑和系统服务分开。
4. 容器：Spring 包含并管理应用中对象的生命周期和配置。
5. 支持声明式事务处理：只需要通过配置就可以完成对事物的管理，而无须手动编程。
6. MVC框架：Spring 的 Web 框架是个精心设计的框架，是 Web 框架的一个很好的替代品。
7. 方便集成各种优秀框架：Spring不排斥各种优秀的开源框架，其内部提供了对各种优秀框架（如Struts、Hibernate、MyBatis、Quartz等）的直接支持。



### 2. AOP

Aspect-Oriented Programming，面向方面编程，可以说是 OOP（Object-Oriented Programing，面向对象编程）的补充和完善。

OOP 引入封装、继承和多态性等概念来建立一种对象层次结构，用以模拟公共行为的一个集合。当我们需要为分散的对象引入公共行为的时候，OOP 则显得无能为力。例如日志功能。日志代码往往水平地散布在所有对象层次中，而与它所散布到的对象的核心功能毫无关系。对于其他类型的代码，如：安全性、异常处理和透明的持续性也是如此。这种散布在各处的无关的代码被称为横切（cross-cutting）代码，在 OOP 设计中，它导致了大量代码的重复，而不利于各个模块的重用。

而 AOP 技术则恰恰相反，它利用一种称为“横切”的技术，剖解开封装的对象内部，并将那些影响了多个类的公共行为封装到一个可重用模块，并将其名为“Aspect”，即方面。所谓“方面”，简单地说，就是将那些与业务无关，却为业务模块所共同调用的逻辑或责任封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可操作性和可维护性。

AOP的基本概念：

1. 切面（Aspect）：官方的抽象定义为“一个关注点的模块化，这个关注点可能会横切多个对象”。
2. 连接点（Joinpoint）：程序执行过程中的某一行为。
3. 通知（Advice）：“切面”对于某个“连接点”所产生的动作。
4. 切入点（Pointcut）：匹配连接点的断言，在 AOP 中通知和一个切入点表达式关联。
5. 目标对象（Target Object）：被一个或者多个切面所通知的对象。
6. AOP 代理（AOP Proxy）：在 Spring AOP 中有两种代理方式，JDK 动态代理和 CGLIB 代理。



### 3. Advice

什么是Advice？

特定JoinPoint 处的Aspect 所采取的动作称为Advice。Spring AOP 使用一个Advice 作
为拦截器，在JoinPoint “周围”维护一系列的拦截器。

1. **前置通知**（Before advice）：在某连接点（JoinPoint）之前执行的通知，但这个通知不能阻止连接点前的执行。ApplicationContext 中在 < aop:aspect > 里面使用 < aop:before > 元素进行声明；
2. **后置通知**（After advice）：当某连接点退出的时候执行的通知（不论是正常返回还是异常退出）。ApplicationContext 中在 < aop:aspect > 里面使用 < aop:after > 元素进行声明。
3. **返回后通知**（After return advice ：在某连接点正常完成后执行的通知，不包括抛出异常的情况。ApplicationContext 中在 < aop:aspect > 里面使用 << after-returning >> 元素进行声明。
4. **环绕通知**（Around advice）：包围一个连接点的通知，类似 Web 中 Servlet规范中的 Filter 的 doFilter 方法。可以在方法的调用前后完成自定义的行为，也可以选择不执行。ApplicationContext 中在 < aop:aspect > 里面使用 < aop:around > 元素进行声明。
5. **抛出异常后通知**（After throwing advice）：在方法抛出异常退出时执行的通知。ApplicationContext 中在 < aop:aspect > 里面使用 < aop:after-throwing > 元素进行声明。





### 4. 为什么使用IoC和AOP

1. 假设没有IoC，用MVC的三层模型举例，DAO被Service调用，Service又被Controller调用，越是底层的模型就越是耦合，一个DAO可能被多个Service调用，一个Service又会被多个Controller调用，这是逻辑复用。

   但是我们没有做到资源复用。上层调用下一层时，必然会持有下一层的对象引用，即成员变量，目前我们每一个成员变量都会实例化一个对象，每一个链路都创建了同样的对象，造成了极大的资源浪费。本应多个 Controller 复用同一个 Service，多个 Service 复用同一个 DAO。现在变成了一个 Controller创建多个重复的 Service，多个 Service 又创建了多个重复的 DAO。

   许多组件只需要实例化一个对象就够了，创建多个没有任何意义。针对对象重复创建的问题，我们自然而然想到了单例模式。只要编写类时都将其写为单例，这样就避免了资源浪费。但是，引入设计模式必然会带来复杂性，况且还是每一个类都为单例，每一个类都会有相似的代码，其弊端不言自明。

   当然，这些问题都可以引入设计模式来解决，不过这样一来又绕回去了：设计模式本身也会带来复杂性。这就像一种死循环：传统开发模式编码复杂，要想解决这种复杂却得陷入另一种复杂。难道没有办法解决了吗？当然不是的，在讲优秀解决方案前，我们先来梳理一下目前出现的问题：

   - 创建了许多重复对象，造成大量资源浪费；
   - 更换实现类需要改动多个地方；
   - 创建和配置组件工作繁杂，给组件调用方带来极大不便。

   透过现象看本质，这些问题的出现都是同一个原因：**组件的调用方参与了组件的创建和配置工作**。

   其实调用方只需关注组件如何调用，至于这个组件如何创建和配置又与调用方有什么关系呢？就好比我去餐馆只需点菜，饭菜并不需要我亲自去做，餐馆自然会做好给我送过来。如果我们编码时，有一个「东西」能帮助我们创建和配置好那些组件，我们只负责调用该多好。这个「东西」就是容器。

   我们到底为什么要用 IoC 和 AOP - 老刘的文章 - 知乎 https://zhuanlan.zhihu.com/p/349386138

   Tomcat 是 Servlet 容器，只负责管理 Servlet。我们平常使用的组件则需要另一种容器来管理，这种容器我们称之为 **IoC 容器**。

   控制反转，是指对象的创建和配置的控制权从调用方转移给容器。好比在家自己做菜，菜的味道全部由自己控制；去餐馆吃饭，菜的味道则是交由餐馆控制。IoC 容器就担任了餐馆的角色。

   有了 IoC 容器，我们可以将对象交由容器管理，交由容器管理后的对象称之为 Bean。调用方不再负责组件的创建，要使用组件时直接获取 Bean 即可。

   调用方只需按照约定声明依赖项，所需要的 Bean 就自动配置完毕了，就好像在调用方外部注入了一个依赖项给其使用，所以这种方式称之为 依赖注入（Dependency Injection，缩写为 DI）。**控制反转和依赖注入是一体两面，都是同一种开发模式的表现形式**。

   IoC 轻而易举地解决了我们刚刚总结的问题：

   对象交由容器管理后，默认是单例的，这就解决了资源浪费问题。

   若要更换实现类，只需更改 Bean 的声明配置，即可达到无感知更换。

2. AOP

   当有重复代码出现时，可以就将其封装出来然后复用。我们通过分层、分包、分类来规划不同的逻辑和职责，就像之前讲解的三层架构。但这里的复用的都是**核心业务逻辑**，并不能复用一些**辅助逻辑**，比如：日志记录、性能统计、安全校验、事务管理，等等。但是这些业务内容往往贯穿全流程，传统的OOP无法封装。

   OOP 是至上而下的编程方式，犹如一个树状图，A调用B、B调用C，或者A继承B、B继承C。这种方式对于业务逻辑来说是合适的，通过调用或继承以复用。而辅助逻辑就像一把闸刀横向贯穿所有方法。

   横线切开，每一层都会执行相同的辅助逻辑，所以大家将这些辅助逻辑称为层面或者切面。

   AOP 不是 OOP 的对立面，它是对 OOP 的一种补充。OOP 是纵向的，AOP 是横向的，**让我们能够不修改原有代码，便能让切面逻辑在所有业务逻辑中生效**。

   我们只需声明一个切面，写上切面逻辑：

   ```java
   @Aspect // 声明一个切面
   @Component
   public class MyAspect {
       // 原业务方法执行前
       @Before("execution(public void com.rudecrab.test.service.*.doService())")
       public void methodBefore() {
           System.out.println("===AspectJ 方法执行前===");
       }
   
       // 原业务方法执行后
       @AfterReturning("execution(* com.rudecrab.test.service..doService(..))")
       public void methodAddAfterReturning() {
           System.out.println("===AspectJ 方法执行后===");
       }
   }
   ```

   无论你有一个业务方法，还是一万个业务方法，对我们开发者来说只需编写一次切面逻辑，就能让所有业务方法生效，极大提高了我们的开发效率。



### 5. JDK中的静态代理

JDK 动态代理最核心的一个接口和方法如下所示：

1. java.lang.reflect中的**InvocationHandler**

   ```java
   public interface InvocationHandler {
       public Object invoke(Object proxy, Method method, Object[] args)
           throws Throwable;
   }
   ```

   对于被代理的类的操作都会由该接口中的 invoke 方法实现，其中的参数的含义分别是：

   1. proxy：被代理的类的实例；
   2. method：调用被代理的类的方法；
   3. args：该方法需要的参数。

   测试实例：

   ```java
   public class ProxyHandler implements InvocationHandler {
       private Object object;
   
       ProxyHandler(Object object) {
           this.object = object;
       }
   
       @Override
       public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
           System.out.println("Before invoke "  + method.getName());
           method.invoke(object, args);
           System.out.println("After invoke " + method.getName());
           return null;
       }
   }
   ```

   调用Proxy

   ```java
   public class DynamicProxyTest {
       public static void main(String[] args) {
           // System.getProperties().setProperty("sun.misc.ProxyGenerator.saveGeneratedFiles", "true");
           IHello hello = new HelloImpl();
           IBye bye = new ByeImpl();
           InvocationHandler handler = new ProxyHandler(hello);
           IHello proxyHello = (IHello) Proxy.newProxyInstance(hello.getClass().getClassLoader(), hello.getClass().getInterfaces(), handler);
           proxyHello.sayHello();
           InvocationHandler handlerBye = new ProxyHandler(bye);
           IBye proxyBye = (IBye) Proxy.newProxyInstance(bye.getClass().getClassLoader(), bye.getClass().getInterfaces(), handlerBye);
           proxyBye.sayBye();
       }
   }
   ```

2. java.lang.reflect 包中的 Proxy 类中的**newProxyInstance 方法**

   ```java
   public static Object newProxyInstance(ClassLoader loader,
                                             Class<?>[] interfaces,
                                             InvocationHandler h)
           throws IllegalArgumentException{
       
   }
   ```

   其中的参数含义如下：

   1. loader：被代理的类的类加载器；
   2. interfaces：被代理类的接口数组；
   3. invocationHandler：调用处理器类的对象实例

   该方法会返回一个被修改过的类的实例，从而可以自由的调用该实例的方法。



### 6. Bean的生命周期

1. Spring启动之后，查找并加载需要被Spring管理的Bean，进行Bean的实例化。
   1. 实例化BeanFactoryPostProcessor实现类，执行postProcessBeanFactory方法。
   2. 实例化BeanPostProcessor实现类。
   3. 实例化InstantiationAwareBeanPostProcessorAdaptor实现类，执行postProcessBeforeInstantiation方法。
   4. 执行Bean的构造器，执行InstantiationAwareBeanPostProcessorAdaptor的postProcessPropertyValues方法。
2. Bean实例化之后，将Bean的引用和值注入到Bean的属性中。
   1. 调用BeanNameAware的setBeanName方法。
   2. 调用BeanFactoryAware的setBeanFactory方法。
   3. 执行BeanPostProcessor的postProcessBeforeInstantiation方法。
   4. 调用InitializationBean的afterPropertiesSet方法。
3. 调用<bean>的init-method属性指定的初始化方法。
4. 执行BeanPostProcessor的postProcessorAfterInitialization方法。
5. 容器初始化成功，执行正常调用。
6. 销毁容器。
7. 调用DisposibleBean的destory方法。
8. 调用<bean>的destory-method的指定的销毁bean的方法。

[Spring Bean的生命周期（非常详细） - Chandler Qian - 博客园 (cnblogs.com)](https://www.cnblogs.com/zrtqsk/p/3735273.html)



### 7. Bean的作用域

Spring框架支持5种作用域，有三种作用域是当开发者使用基于web的ApplicationContext的时候才生效的。下面就是Spring直接支持的作用域了，当然开发者也可以自己定制作用域。

| 作用域                   | 描述                                                         |
| ------------------------ | ------------------------------------------------------------ |
| 单例(singleton)          | （默认）每一个Spring IoC容器都拥有唯一的一个实例对象         |
| 原型(prototype)          | 一个Bean定义，任意多个对象                                   |
| 请求(request)            | 在一次 HTTP 请求中，容器会返回该 Bean 的同一个实例。而对不同的 HTTP 请求，会返回不同的实例，该作用域仅在当前 HTTP Request 内有效。 |
| 会话(session)            | 在一次 HTTP Session 中，容器会返回该 Bean 的同一个实例。而对不同的 HTTP 请求，会返回不同的实例，该作用域仅在当前 HTTP Session 内有效。 |
| 全局会话(global session) | 在一个全局的 HTTP Session 中，容器会返回该 Bean 的同一个实例。该作用域仅在使用 portlet context 时有效。 |



### 8. 单例的线程安全问题

[Spring 中的bean 是线程安全的吗？ - myseries - 博客园 (cnblogs.com)](https://www.cnblogs.com/myseries/p/11729800.html)

Spring中的Bean是否线程安全，首先容器本身并没有提供相关的安全策略，所以Spring容器中的Bean本身是不具备线程安全的，但是要根据不同的作用域进行分析。

已知Bean的作用域有五个，见上一个问题：

Spring 的 bean 作用域（scope）类型
　　1、singleton:单例，默认作用域。

　　2、prototype:原型，每次创建一个新对象。

　　3、request:请求，每次Http请求创建一个新对象，适用于WebApplicationContext环境下。

　　4、session:会话，同一个会话共享一个实例，不同会话使用不用的实例。

　　5、global-session:全局会话，所有会话共享一个实例。



线程安全这个问题，要从单例与原型Bean分别进行说明。

原型Bean
　　对于原型Bean,每次创建一个新对象，也就是线程之间并不存在Bean共享，自然是不会有线程安全的问题。

但是针对单例的Bean，其实是线程不安全的，因为所有的进程都在争用这个资源。

如果单例Bean,是一个无状态Bean，也就是线程中的操作不会对Bean的成员执行**查询**以外的操作，那么这个单例Bean是线程安全的。比如Spring mvc 的 Controller、Service、Dao等，这些Bean大多是无状态的，只关注于方法本身。

Spring中的Bean默认是单例模式的，框架并没有对bean进行多线程的封装处理。

实际上大部分时间Bean是无状态的（比如Dao） 所以说在某种程度上来说Bean其实是安全的。

但是如果Bean是有状态的 那就需要开发人员自己来进行线程安全的保证，最简单的办法就是改变bean的作用域 把 "singleton"改为’‘protopyte’ 这样每次请求Bean就相当于是 new Bean() 这样就可以保证线程的安全了。

> 有状态就是有数据存储功能
> 无状态就是不会保存数据

controller、service和dao层本身并不是线程安全的，只是如果只是调用里面的方法，而且多线程调用一个实例的方法，会在内存中复制变量，这是自己的线程的工作内存，是安全的。

所以其实任何无状态单例都是线程安全的。**Spring的根本就是通过大量这种单例构建起系统，以事务脚本的方式提供服务**



### 9. Controller、Service的线程安全

首先问@Controller @Service是不是线程安全的？
答：默认配置下不是的。为啥呢？因为默认情况下@Controller没有加上@Scope，没有加@Scope就是默认值singleton，单例的。意思就是系统只会初始化一次Controller容器，所以每次请求的都是同一个Controller容器，当然是非线程安全的。

```java
@RestController
public class TestController {

    private int var = 0;
    
    @GetMapping(value = "/test_var")
    public String test() {
        System.out.println("普通变量var:" + (++var));
        return "普通变量var:" + var ;
    }
}
```

通过请求连发三次，输出结果如下：

```java
在postman里面发三次请求，结果如下：
普通变量var:1
普通变量var:2
普通变量var:3
```

说明这不是线程安全的。

使用上文提到的方法改变作用域，@Scope，将作用域由单例变成原型，

```java 
@RestController
@Scope(value = "prototype") // 加上@Scope注解，他有2个取值：单例-singleton 多实例-prototype
public class TestController {

    private int var = 0;
    
    @GetMapping(value = "/test_var")
    public String test() {
        System.out.println("普通变量var:" + (++var));
        return "普通变量var:" + var ;
    }
}
```

这样一来，每个请求都单独创建一个Controller容器，所以各个请求之间是线程安全的，三次请求结果：

```
普通变量var:1
普通变量var:1
普通变量var:1
```

但是改了作用域之后还是存在问题，如果这个类中带有静态变量怎么办？

```java 
@RestController
@Scope(value = "prototype") // 加上@Scope注解，他有2个取值：单例-singleton 多实例-prototype
public class TestController {
    private int var = 0;
    private static int staticVar = 0;

    @GetMapping(value = "/test_var")
    public String test() {
        System.out.println("普通变量var:" + (++var)+ "---静态变量staticVar:" + (++staticVar));
        return "普通变量var:" + var + "静态变量staticVar:" + staticVar;
    }
}
```

那么创建实例是没有办法管理静态变量的。

再尝试使用ThreadLocal的方法，

```java 
@RestController
@Scope(value = "singleton") // prototype singleton
public class TestController {

    private int var = 0; // 定义一个普通变量

    private static int staticVar = 0; // 定义一个静态变量

    @Value("${test-int}")
    private int testInt; // 从配置文件中读取变量

    ThreadLocal<Integer> tl = new ThreadLocal<>(); // 用ThreadLocal来封装变量

    @Autowired
    private User user; // 注入一个对象来封装变量

    @GetMapping(value = "/test_var")
    public String test() {
        tl.set(1);
        System.out.println("先取一下user对象中的值："+user.getAge()+"===再取一下hashCode:"+user.hashCode());
        user.setAge(1);
        System.out.println("普通变量var:" + (++var) + "===静态变量staticVar:" + (++staticVar) + "===配置变量testInt:" + (++testInt)
                + "===ThreadLocal变量tl:" + tl.get()+"===注入变量user:" + user.getAge());
        return "普通变量var:" + var + ",静态变量staticVar:" + staticVar + ",配置读取变量testInt:" + testInt + ",ThreadLocal变量tl:"
                + tl.get() + "注入变量user:" + user.getAge();
    }
}
```



补充Controller以外的代码：
config里面自己定义的Bean:User

```java 
@Configuration
public class MyConfig {
    @Bean
    public User user(){
        return new User();
    }
}
```

三次请求得到的结果是：

```
先取一下user对象中的值：0===再取一下hashCode:241165852
普通变量var:1===静态变量staticVar:1===配置变量testInt:1===ThreadLocal变量tl:1===注入变量user:1
先取一下user对象中的值：1===再取一下hashCode:241165852
普通变量var:2===静态变量staticVar:2===配置变量testInt:2===ThreadLocal变量tl:1===注入变量user:1
先取一下user对象中的值：1===再取一下hashCode:241165852
普通变量var:3===静态变量staticVar:3===配置变量testInt:3===ThreadLocal变量tl:1===注入变量user:1
```

可以看到，**在单例模式下Controller中只有用ThreadLocal封装的变量是线程安全的**。

为什么这样说呢？我们可以看到3次请求结果里面只有ThreadLocal变量值每次都是从0+1=1的，其他的几个都是累加的，而user对象呢，默认值是0，第二交取值的时候就已经是1了，**关键他的hashCode是一样的，说明每次请求调用的都是同一个user对象。**

下面将TestController 上的@Scope注解的属性改一下改成多实例的：@Scope(value = "prototype")，其他都不变，再次请求，结果如下：

```
先取一下user对象中的值：0===再取一下hashCode:853315860
普通变量var:1===静态变量staticVar:1===配置变量testInt:1===ThreadLocal变量tl:1===注入变量user:1
先取一下user对象中的值：1===再取一下hashCode:853315860
普通变量var:1===静态变量staticVar:2===配置变量testInt:1===ThreadLocal变量tl:1===注入变量user:1
先取一下user对象中的值：1===再取一下hashCode:853315860
普通变量var:1===静态变量staticVar:3===配置变量testInt:1===ThreadLocal变量tl:1===注入变量user:1
```

分析这个结果发现，多实例模式下普通变量，取配置的变量还有ThreadLocal变量都是线程安全的，而静态变量和user（看他的hashCode都是一样的）对象中的变量都是非线程安全的。也就是说尽管TestController 是每次请求的时候都初始化了一个对象，但是静态变量始终是只有一份的，而且这个注入的user对象也是只有一份的。静态变量只有一份这是当然的咯，那么有没有办法让user对象可以每次都new一个新的呢？当然可以：

```java 
public class MyConfig {
    @Bean
    @Scope(value = "prototype")
    public User user(){
        return new User();
    }    
}
```

这样的话

```
先取一下user对象中的值：0===再取一下hashCode:1612967699
普通变量var:1===静态变量staticVar:1===配置变量testInt:1===ThreadLocal变量tl:1===注入变量user:1
先取一下user对象中的值：0===再取一下hashCode:985418837
普通变量var:1===静态变量staticVar:2===配置变量testInt:1===ThreadLocal变量tl:1===注入变量user:1
先取一下user对象中的值：0===再取一下hashCode:1958952789
普通变量var:1===静态变量staticVar:3===配置变量testInt:1===ThreadLocal变量tl:1===注入变量user:1
```

可以看到每次请求的user对象的hashCode都不是一样的，每次赋值前取user中的变量值也都是默认值0。

下面总结一下：

　　1、在@Controller/@Service等容器中，默认情况下，scope值是单例-singleton的，也是线程不安全的。
　　2、尽量不要在@Controller/@Service等容器中定义静态变量，不论是单例(singleton)还是多实例(prototype)他都是线程不安全的。
　　3、默认注入的Bean对象，在不设置scope的时候他也是线程不安全的。
　　4、**一定要定义变量的话，用ThreadLocal来封装，这个是线程安全的**



### 10. 对Spring中事务的理解

事务是逻辑上的一组操作，要么都执行，要么都不执行。

- 原子性：事务是最小的执行单位，不允许分割。事务的原子性确保动作要么全部完成，要么完全不起作用；
- 一致性：执行事务前后，数据保持一致；
- 隔离性：并发访问数据库时，一个用户的事物不被其他事物所干扰，各并发事务之间数据库是独立的；
- 持久性：一个事务被提交之后。它对数据库中数据的改变是持久的，即使数据库发生故障也不应该对其有任何影响。

Spring 支持编程式事务管理和声明式事务管理两种方式：

声明式事务：

```java 
作者：白苏
链接：https://zhuanlan.zhihu.com/p/260517060
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

@Service
public class PersonService {
    @Resource
    private PersonMapper personMapper;
   
    @Resource
    private CompanyMapper companyMapper;
    
    //rollbackFor：触发回滚的异常，默认是RuntimeException和Error
    //isolation: 事务的隔离级别，默认是Isolation.DEFAULT也就是数据库自身的默认隔离级别
    @Transactional(rollbackFor = {RuntimeException.class, Error.class})
    public void saveOne(Person person) {
        Company company = new Company();
        company.setName("tenmao:" + person.getName());
        companyMapper.insertOne(company);
        personMapper.insertOne(person);
    }
}
```



编程式事务：

手动开启、提交、回滚事务。

Spring 编程式事务实现需要在配置文件中配置相应的事务处理器，用 AOP、事务标签、注解方式实现事务。

```java 
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
     <property name="dataSource" ref="dataSource" />
</bean>

作者：白苏
链接：https://zhuanlan.zhihu.com/p/260517060
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```





### 11. Spring的事务传播行为

所谓事务传播特性就是多个事务方法相互调用时，事务如何在这些方法间传播。

1. PROPAGATION_REQUIRED：如果当前没有事务，就新建一个事务，如果已经存在一个事务中，加入到这个事务中。这是最常见的。 
2. PROPAGATION_SUPPORTS：支持当前事务，如果当前没有事务，就以非事务方式执行。  
3. PROPAGATION_MANDATORY：支持当前事务，如果当前没有事务，就抛出异常。 
4. PROPAGATION_REQUIRES_NEW：新建事务，如果当前存在事务，把当前事务挂起。  
5. PROPAGATION_NOT_SUPPORTED：以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。  
6. PROPAGATION_NEVER：以非事务方式执行，如果当前存在事务，则抛出异常。
7. PROPAGATION_NESTED：如果当前存在事务，则在嵌套事务内执行，如果当前没有事务，则执行1。

虽然有7种，但是常用的就第一种REQUIRED和第四种REQUIRES_NEW


为什么Spring可以让事务进行传播呢：看了源码我们就明白了

由于Spring的事务管理是通过线程相关的ThreadLocal来保存数据访问基础设施的（Connection对象），再结合IoC和AOP实现高级声明式事务的功能，所以Spring的事务天然的和线程有着千丝万缕的关系。

Spring通过ThreadLocal可以将大部分Bean无状态化（线程安全的）所以Spring中单实例Bean对线程安全问题拥有一种天然的免疫力。

所以Spring中DAO和Service都以单实例的方式存在，Spring将有状态的变量（Connection）本地线程化，达到另一个层面上的线程无关，从而实现线程无关。

总结：在相同的线程中进行相互嵌套调用的事务方法工作于相同的事务中，不同的线程中，则各自独立工作与独立的事务中。



### 12. Spring的事务隔离级别

1. ISOLATION_DEFAULT：这是个PlatfromTransactionManager 默认的隔离级别，使用数据库默认的事务隔离级别。
2. ISOLATION_READ_UNCOMMITTED：读未提交，允许另外一个事务可以看到这个事务未提交的数据。
3. ISOLATION_READ_COMMITTED：读已提交，保证一个事务修改的数据提交后才能被另一事务读取，而且能看到该事务对已有记录的更新。
4. ISOLATION_REPEATABLE_READ：可重复读，保证一个事务修改的数据提交后才能被另一事务读取，但是不能看到该事务对已有记录的更新。
5. ISOLATION_SERIALIZABLE：一个事务在执行的过程中完全看不到其他事务对数据库所做的更新。

更多关于Spring框架知识可以看教程



### 13. Spring常用的注入方法

1. 构造器依赖注入：构造器依赖注入（DI）通过容器触发一个类的构造器来实现，该类有一系列参数，每个参数代表一个其他类的依赖。
2. Setter方法注入：Setter 方法注入是容器通过调用无参构造器或无参 static 工厂方法实例化 bean 之后，调用该 bean 的 Setter 方法，即实现了基于 Setter 的依赖注入。
3. 基于注解的注入：最好的解决方案是用构造器参数实现强制依赖，Setter 方法实现可选依赖。



### 14. Spring中常用的设计模式

1. 工厂设计模式：spring使用工程模式通过BeanFactory、ApplicationContext创建Bean对象
2. 代理设计模式：Spring AOP
3. 单例设计模式：Spring中的Bean默认都是单例模式
4. 模板设计模式：Spring中的JdbcTemplate、hibernateTemplate等以Template结尾的对数据库操作的库
5. 包装器设计模式：我们的项目需要连接多个数据库，而且不同的客户在每次访问中根据需
   要会去访问不同的数据库。这种模式让我们可以根据客户的需求能够动态切换不同的数据
   源。
6. 观察者模式：Spring 事件驱动模型就是观察者模式很经典的一个应用
7. 适配器模式：Spring AOP 的增强或通知(Advice)使用到了适配器模式、spring MVC 中也是用到了适配器模式适配Controller



### 15. Spring MVC的理解

MVC是model-view-controller的简称，是一种架构模式，这种架构模式分离了表现和交互。

MVC被分为三个部件，也就是名字体现出来的部件：模型、试图、控制器。

- Model：模型是程序的主体部分，主要包括业务数据和业务逻辑，在模型层会涉及用户发布的服务，在服务中会根据不同的业务需求，跟新业务模型中的数据。
- View：视图是用户和程序交互的接口，用户会根据具体的业务需求，在视图界面输入自己的特定的业务数据，并且通过自己的界面的时间交互，将对应的输入参数提交给后台的控制器进行处理。
- Controller：控制器负责接收用户输入进来的参数，以及更新业务模型的部分。控制器中接受了用户与界面交互时传过来的数据，并且负责将用户输入的数据根据业务逻辑执行服务的调用、更新业务中的数据。



### 18. Spring MVC的核心组件

- DispatcherServlet 控制器入口 负责分发请求
- HandlerMapping 负责根据请求 找到对应的控制器
- Controller 真正处理请求的控制器
- ModelAndView 封装数据信息和视图信息的
- ViewResolver 视图处理器 通过处理找到对应的页面



1. 前端控制器：DispatcherServlet（调度程序）

   Spring的前端的入口函数。用来接受请求、响应结果相当于转发器，中央处理器，有了DS，可以大大减少其他组件之间的耦合。

2. 处理器映射器：HandlerMapping（负责找到Controller）

   根据请求的url查找Handler，HandlerMapping负责根据用户的请求找到对应的Controller，Spring MVC提供了不同的映射器实现不同的映射方式，例如：配置文件方式、实现接口方式、注解方式。

3. 处理器适配器：HandlerAdapter（负责找到Controller中对应的方法）

   按照特定的规则（HandlerAdapter所规定的规则）去执行Handler。

4. 处理器：Handler

   编写 Handler 时按照 HandlerAdapter 的要求去做，这样适配器才可以去正确执行 Handler.Handler 是继 DispatcherServlet 前端控制器的后端控制器，在 DispatcherServlet 的控制下 Handler 对具体的用户请求进行处理.由于 Handler 涉及到具体的用户业务请求，所以一般情况需要工程师根据业务需求开发Handler

5. 视图解析器：ViewResolver
   
   进行视图解析，根据逻辑视图名解析成真正的视图（View）
   



注意：处理器 Handler（也就是我们平常说的 Controller 控制器）以及视图层 View 都是需要我们自己手动开发的.其他的一些组件比如：前端控制器 DispatcherServlet、处理器映射器 HandlerMapping、处理器适配器 HandlerAdapter 等等都是框架提供给我们的，不需要自己手动开发。



### 17. Spring MVC的执行流程

首先分析Spring MVC的原理，当请求到来的时候，第一个接受这个请求的前端控制器叫DispatcherServlet，后端的控制器是Controller，前端请求和后端的控制需要通过一个映射HandlerMapping，负责业务逻辑处理的模型对象一般也是我们平常写的DAO/DTO组件。只是它最后的返回更灵活，Controller返回一个ModelAndView对象给DispatcherServlet，ModelAndView可以携带一个视图对象，也可以携带一个视图对象的逻辑名。最后，DispatcherServlet将请求分派给ModelAndView对象指定的视图对象。视图对象负责渲染返回给客户的回应。



更加具体的工作流程是：

①当用户在浏览器中点击一个链接或者提交一个表单时，那么就会产生一个请求（request）。当请求离开浏览器时，它会携带用户请求的信息（比如说请求的URL信息，用户名，密码什么的）。

②请求的第一站到达的是Spring的DispatcherServlet，它是一个前端控制器，工作是将用户的请求委托给其他的组件（这里是交给Spring MVC的控制器）去处理。这里DispatcherServlet要决定将请求传给哪一个控制器（Controller）去处理，那么这时就需要处理器映射（Handler Mapping）了。处理器映射会看请求的URL信息，然后决定将请求交给哪一个控制器去处理。比如说有两个控制器ControllerA和ControllerB，分别处理后缀名为.html和.jsp送来的请求，那么当请求者的后缀名为.html时，那么DispatcherServlet就将请求交给ControllerA进行处理。

③当选择了一个合适的控制器后，DispatcherServlet就会将请求交给这个控制器去处理。在这个控制器上，用户的请求将会将用户提交的一些信息交由控制器处理并等待。然而设计的比较好的控制器本身对信息做很少的处理或者根本不做处理，而是将业务逻辑交给一个或多个服务器对象（Model）去处理。

④当控制器对用户请求所携带的信息进行处理（或交给模型层处理）后，经常会产生一些其他的需要返回给浏览器进行显示的数据。这些原始数据直接显示出来显然是不友好的，那么就需要视图（View）来对这些数据进行显示了。控制器的最后一件事就是将模型数据打包，并且指定产生输出的视图的特定名称，然后它将模型、视图名称以及request请求一起发送给DispatcherServlet。所以控制器并没有与视图进行耦合，因为传递给DispatcherServlet的视图名称并不是某一个指定的特殊的文件名称（如后缀名一定是JSP或其他什么名称），它只要是一个可以产生输出和展示结果的逻辑名称就可以了。

⑤DispatcherServlet会向一个视图解析器（ViewResolver）进行请求，视图解析器可以将逻辑视图名称映射到一个特定的视图显示文件上面。

⑥现在DispatcherServlet知道哪一个视图文件可以显示结果了。该视图将会利用模板数据产生输出，这些输出通过response对象返回给客户端进行显示。



### 19. @RequestMapping

RequestMapping是一个用来处理请求地址映射的注解，可用于类或者方法上，用于类上的话，表示类中的所有请求都是以该地址作为父路径。

@RequestMapping有六个属性，下面把它分为三类作为说明

**value、method：**

1. value：指定请求的实际地址
2. method：定请求的method类型， GET、POST、PUT、DELETE 等

**consumes、produces：**

1. consumes：指定处理请求的提交内容类型（Content-Type），例如 application/json、text/html
2. produces：指定返回的内容类型，仅当 request 请求头中的（Accept）类型中包含该指定类型才返回

**params、header：**

1. params：指定 request 中必须包含某些参数值是，才让该方法处理
2. headers：指定 request 中必须包含某些指定的 header 值，才能让该方法处理请求



### 20. Spring Controller

Spring Controller默认都是单例模式，所以不是线程安全的。

但是如果采用同步的话，会大大影响性能。

解决方案是不要在Controller加入field字段。





## MyBatis

[MyBatis 教程_w3cschool](https://www.w3cschool.cn/mybatis/)

### 1. MyBatis概述

MyBatis是一款在持久层使用的SQL映射框架，可以将SQL语句单独写在XML配置文件中，或者使用带有注解的Mapper映射类来完成数据库记录到Java实体的映射。与另一款主流的ORM框架Hibernate不同，MyBatis属于半自动的ORM框架，它虽然不能将不同数据库的影响隔离开，仍然需要自己编写SQL语句，但是可以灵活地控制SQL语句的构造，将SQL语句的编写和程序的运行分离开，使用更加便捷。



### 2. MyBatis的优缺点

优点：

1. 基于SQL语句编程，相当灵活，不会对现有的程序或者数据库的设计造成任何影响，SQL写在XML文件中，解除了SQL语句和程序代码的耦合，便于统一管理，提供xml标签，支持编写动态SQL语句，并可以复用。
1. 消除了大量的jdbc冗余代码，包括参数设置、结果集封装，不需要手动开关连接
2. SQL语句可以控制，方便查询优化，使用更加灵活
3. 提供与IoC框架与Spring的集成
4. 引入了缓存机制，提供了与第三方缓存库的集成与支持

缺点：

1. SQL语句的编写工作量比较大，尤其当字段多，关联表比较多的时候，对于开发人员的SQL语句功底要求比较高
2. SQL语句依赖于数据库，导致数据库的可移植性比较差，不能随意更换数据库（本来也就不能随意更换数据库吧）



### 3. MyBatis和Hibernate的异同

| 功能       | MyBatis                                 | Hibernate                                                    |
| ---------- | --------------------------------------- | ------------------------------------------------------------ |
| 开发速度   | 上手容易                                | 相对掌握困难                                                 |
| 开发工作量 | 手写SQL语句                             | 也可以手写SQL语句，但破坏了Hibernate封装和简洁性             |
| 扩展性     | 因为是手写SQL，所有扩展性好，但迁移性差 | 数据库关系关联都在XML中，所以HQL对是什么数据库不关心         |
| 移植性     | 不好，针对不同的数据库写不同的SQL       | 较好                                                         |
| 一级缓存   | SQLSession                              | Session缓存                                                  |
| 二级缓存   | 在每个具体的表-对象映射中进行详细配置   | 在SessionFactory生成的配置文件中进行详细配置， 然后再在具体的表-对象映射中配置是那种缓存 |



**ORM：**是一种为了解决面向对象与关系型数据库中数据类型不匹配的技术，它通过描述Java对象与数据库表之间的映射关系，自动将java应用程序中的对象持久化到关系型数据库的表中；

使用ORM框架后，应用程序不再直接访问底层数据库，而是以面向对象的方式来操作持久化对象，而ORM框架则会通过映射关系将这些面向对象的操作转换成底层的SQL操作；



**Hibernate：**是一个全表映射的框架。通常开发者只需定义好持久化对象到数据库表的映射关系，就可以通过Hibernate提供的方法完成持久层操作。开发者并不需要熟悉地掌握SQL语句的编写，Hibernate会根据制定的存储逻辑，自动的生成对应的SQL，并调用JDBC接口来执行，所以其开发效率会高于Mybatis。然而Hibernate自身也存在着一些缺点，例如它在多表关联时，对SQL查询的支持较差；更新数据时，需要发送所有字段；不支持存储过程；不能通过优化SQL来优化性能等。这些问题导致其只适合在场景不太复杂且对性能要求不高的项目中使用。



**Mybatis：**是一个半自动映射的框架。这里所谓的”半自动"是相对于Hibernate全表映射而言，Mybatis需要手动匹配提供POJO、SQL和映射关系，而Hibernate只需要POJO和映射关系即可。与Hibernate相比，虽然使用Mybatis手动编写SQL要比使用Hibernate的工作量大，但Mybatis可以配置动态SQL并优化SQL，可以通过配置决定SQL的映射规则，它还支持存储过程等。对于一些复杂的和需要优化性能的项目来说，显然使用Mybatis更加合适。





共同点：

1. Hibernate于MyBatis都是可以**通过SessionFactoryBuilder由xml配置文件生成SessionFactory，然后由SessionFactory生成Session，Session用来执行事务和SQL语句。**其中SessionFactoryBuider，SessionFactory，Session的生命周期都是差不多的。Hibernate和MyBatis都支持JDBC和JTA事务处理。



不同点：

1. 而MyBatis的优势是MyBatis可以进行更为细致的SQL优化，可以减少查询字段，并且容易掌握。
2. Hibernate的优势是DAO层开发比MyBatis简单，Mybatis需要维护SQL和结果映射。
3. Hibernate数据库移植性很好，MyBatis的数据库移植性不好，不同的数据库需要写不同SQL。
4. Hibernate有更好的二级缓存机制，可以使用第三方缓存。MyBatis本身提供的缓存机制不佳。



总结：

- Hibernate功能强大，数据库无关性好，O/R映射能力强，如果你对Hibernate相当精通，而且对Hibernate进行了适当的封装，那么你的项目整个持久层代码会相当简单，需要写的代码很少，开发速度很快，非常爽。
- Hibernate的缺点就是学习门槛不低，要精通门槛更高，而且怎么设计O/R映射，在性能和对象模型之间如何权衡取得平衡，以及怎样用好Hibernate方面需要你的经验和能力都很强才行。
- iBATIS入门简单，即学即用，提供了数据库查询的自动对象绑定功能，而且延续了很好的SQL使用经验，对于没有那么高的对象模型要求的项目来说，相当完美。
- iBATIS的缺点就是框架还是比较简陋，功能尚有缺失，虽然简化了数据绑定代码，但是整个底层数据库查询实际还是要自己写的，工作量也比较大，而且不太容易适应快速数据库修改。



### 4. MyBatis的执行过程

MyBatis在操作数据库时的8个操作：

1. **读取Mybatis配置文件mybatis-config.xml。**mybatis-config.xml作为Mybatis的全局配置文件，配置Mybatis的运行环境等信息，其中主要内容是获取数据库连接。
2. **加载映射文件Mapper.xml。**Mapper.xml文件即SQL映射文件，该文件中配置了操作数据库的SQL语句，需要在mybatis-config.xml中加载才能执行。mybatis-config.xml可以加载多个配置文件，每个配置文件对应数据库中的一张表。
3. **构建会话工厂**。通过Mybatis的环境等配置信息构建会话工厂SqlSessionFactory。
4. **创建SqlSession对象。**由会话工厂创建SqlSession对象，该对象中包含执行SQL的所有方法。
5. **Mybat**输入参数映射。**在执行方法时，MappedStatement对象会对用户执行SQL语句的输入参数进行定义（可以定义为Map、List类型、基本类型和POJO类型），Executor执行器会通过MappedStatement对象在执行SQL前，将输入的Java对象映射到SQL语句中。这里对输入参数的映射过程就类似于JDBC编程中对preparedStatement对象设置参数的过程。is底层定义了一个Executor接口来操作数据库，**它会根据SqlSession传递的参数动态地生成需要执行的SQL语句，同时负责查询缓存的维护。
6. **在Executor接口的执行方法中，包含一个MappedStatement类型的参数，该参数对映射信息的封装，用于存储要映射的SQL语句的id、参数等。**Mapper.xml文件中一个SQL对应一个MappedStatement对象，SQL的id即是MappedStatement的id。
7. **输入结果映射。**在数据库中执行完SQL语句后，MappedStatement对象会对SQL执行输出的结果进行定义（可以定义为Map和List类型、基本类型、POJO类型），Executor执行器会通过MappedStatement对象在执行SQL语句后，**将输出结果映射至Java对象中**。这种将输出结果映射到Java对象的过程就类似于JDBC编程中对结果的解析处理过程。



### 5. MyBatis的核心对象

1. SqlSessionFactory：是单个数据库映射关系经过编译后的内存镜像，主要作用是创建SqlSession对象。SqlSessionFactory可以通过SqlSessionFactoryBuilder对象来构建，而SqlSessionFactoryBuilder则可以通过xml配置文件或者一个预先定义好的Configuration实例构建出SqlSessionFactory的实例。

   SqlSessionFactory是线程安全的，一旦被创建，在整个应用执行期间都会存在，**如果我们多次地创建同一个数据库的SqlSessionFactory，那么此数据库的资源很容易被耗尽**，为了解决此问题，通常每个数据库都只会对一个SqlSessionFactory，所以在构建SqlSessionFactory实例时，建议使用单例模式。

2. SqlSession：是应用程序与持久层之间执行交互操作的一个单线程对象，主要的功能就是执行持久化操作。SqlSession中包含了数据库中的所有SQL操作，由于底层封装了JDBC连接，所以可以直接使用其实例来执行已经映射的SQL语句。

   每一个线程都应该有一个自己的SqlSession实例，并且该实例是不能被共享的，同时SqlSession实例也是线程不安全的，因此在其适用范围最好一次请求或者一个方式中，绝不能放在类的静态字段、实例字段或任何类型的管理范围（如Servlet的HttpSession）中使用。使用完SqlSession对象之后要及时关闭，通常可以放在finally代码块中关闭。



### 6. MyBatis中的#{}和${}

**#{}是预编译处理，${}是字符串替换**

1. MyBatis在处理#{}的时候，会将SQL中的#{}替换成`?`，调用 PreparedStatement 的 set 方法来赋值；使用 #{} 可以有效的防止 SQL 注入，提高系统安全性；
2. MyBatis在处理${}的时候，只是做了字符串替换，将数值替换。



### 7. MyBatis是如何进行分页的？

>1.物理分页
>物理分页依赖的是某一物理实体，这个物理实体就是数据库，比如MySQL数据库提供了limit关键字，程序员只需要编写带有limit关键字的SQL语句，数据库返回的就是分页结果。
>
>2.逻辑分页
>逻辑分页依赖的是程序员编写的代码。数据库返回的不是分页结果，而是全部数据，然后再由程序员通过代码获取分页数据，常用的操作是一次性从数据库中查询出全部数据并存储到List集合中，因为List集合有序，再根据索引获取指定范围的数据。

MyBatis使用RowBounds对象进行分页，它是针对 ResultSet 结果集执行的内存分页，而非物理分页。可以在 SQL 内直接书写带有物理分页的参数来完成物理分页功能，也可以使用分页插件来完成物理分页。

分页插件的基本原理是使用 MyBatis 提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的 SQL，然后重写 SQL，根据 dialect 方言，添加对应的物理分页语句和物理分页参数。



### 8. MyBatis的四种分页方式

1. 数组分页

   通俗来说，就是由Service分页，由SQL查出所有数据，然后再List中截取需要的部分。

   ```java 
   List<Student> queryStudentsByArray();
   ```

   xml配置文件

   ```xml
   <select id="queryStudentsByArray"  resultMap="studentmapper">
           select * from student
   </select>
   ```

   @Service

   ```java 
   接口
   List<Student> queryStudentsByArray(int currPage, int pageSize);
   实现接口
   @Override
   public List<Student> queryStudentsByArray(int currPage, int pageSize) {
       //查询全部数据
       List<Student> students = studentMapper.queryStudentsByArray();
       //从第几条数据开始
       int firstIndex = (currPage - 1) * pageSize;
       //到第几条数据结束
       int lastIndex = currPage * pageSize;
       return students.subList(firstIndex, lastIndex); //直接在list中截取
   }
   ```

   @Controller

   ```java
   @ResponseBody
   @RequestMapping("/student/array/{currPage}/{pageSize}")
   public List<Student> getStudentByArray(@PathVariable("currPage") int currPage, @PathVariable("pageSize") int pageSize) {
       List<Student> student = StuServiceIml.queryStudentsByArray(currPage, pageSize);
       return student;
   }
   ```

   

2. SQL分页

   MyBatis接口

   ```java
   List<Student> queryStudentsBySql(Map<String,Object> data);
   ```

   xml文件

   ```xml
   <select id="queryStudentsBySql" parameterType="map" resultMap="studentmapper">
           select * from student limit #{currIndex} , #{pageSize}
   </select>
   ```

   @Service

   ```java
   接口
   List<Student> queryStudentsBySql(int currPage, int pageSize);
   实现类
   public List<Student> queryStudentsBySql(int currPage, int pageSize) {
       Map<String, Object> data = new HashedMap();
       data.put("currIndex", (currPage-1)*pageSize);
       data.put("pageSize", pageSize);
       return studentMapper.queryStudentsBySql(data);
   }
   ```

   

3. 拦截器分页

   [Mybatis四种分页方式_w3cschool](https://www.w3cschool.cn/mybatis/mybatis-ypsj3bpi.html)

4. RowBounds分页

   数据量小时，RowBounds不失为一种好办法。但是数据量大时，实现拦截器就很有必要了。

   mybatis接口加入RowBounds参数

   ```java
   public List<UserBean> queryUsersByPage(String userName, RowBounds rowBounds);
   ```

   @Service

   ```java
   @Override
   @Transactional(isolation = Isolation.READ_COMMITTED, propagation = Propagation.SUPPORTS)
   public List<RoleBean> queryRolesByPage(String roleName, int start, int limit) {
       return roleDao.queryRolesByPage(roleName, new RowBounds(start, limit));
   }
   ```




### 9. Mybatis的缓存

缓存将数据保存在内存中，主要目的是为了增快读写速度。

目前常用的缓存系统有：MongoDB、Redis、Ehcache。缓存由于直接保存在内存上，读取的时候不用再从磁盘读写，使用更加快速。

[Mybatis一级缓存和二级缓存](https://blog.csdn.net/qq_37574623/article/details/119132525)

和大多数持久化框架一样，MyBatis提供了一级缓存和二级缓存的功能，默认情况下，MyBatis只开启一级缓存。

1. 一级缓存

   一级缓存基于PerpetualCache的HashMap本地缓存，作用范围在session域内，当session flush或者close之后，缓存就会被清除。

   缓存的Map中的数据结构是

   - key：MapperID+offset+limit+Sql+所有的入参
   - value：查询的结果对象

   其原理是：

   **在参数和 SQL 完全一样的情况下，我们使用同一个 SqlSession 对象调用同一个 mapper 的方法，往往只执行一次 SQL。**因为使用 SqlSession 第一次查询后，MyBatis 会将其放在缓存中，再次查询时，如果没有刷新，并且缓存没有超时的情况下，SqlSession 会取出当前缓存的数据，而不会再次发送 SQL 到数据库。

2. 二级缓存是全局缓存，作用域超出 session 范围之外，可以被所有 SqlSession 共享。

   一级缓存缓存的是 SQL 语句，二级缓存缓存的是结果对象。

​	

MyBatis判断两次查询是完全相同的查询的依据是：

1. 传入的statementId
2. 查询时要求的结果集中的结果范围
3. 这次查询所产生的最终要传递给JDBC java.sql.Preparedstatement的Sql语句字符串（boundSql.getSql() ）
4. 传递给java.sql.Statement要设置的参数值

**脏读**

MyBatis缓存中存在读脏数据的可能，假如SqlSession1查询数据，并且做了一级缓存，然后SqlSession2增删改了数据库中的数据，那么SqlSession1查询数据使用缓存的数据就会造成脏读。

因此此时的缓存应该使用二级缓存，因为一级缓存只得作用域是SqlSession，但是如果多个SqlSession之间共享缓存，那么需要二级缓存。

二级缓存的共享级别是mapper，同样的mapper都是一个命名空间。二级缓存默认是不开启的，二级缓存的开启需要进行配置，实现二级缓存的时候，MyBatis要求返回的POJO必须是可序列化的。

如果我们开启了二级缓存就意味着：

- 映射语句文件中的所有select语句将会被缓存。

- 映射语句文件中的所欲insert、update和delete语句会刷新缓存。

- 缓存会使用默认的Least Recently Used（LRU，最近最少使用的）算法来收回。

- 根据时间表，比如No Flush Interval,（CNFI没有刷新间隔），缓存不会以任何时间顺序来刷新。

- 缓存会存储列表集合或对象(无论查询方法返回什么)的1024个引用

- 缓存会被视为是read/write(可读/可写)的缓存，意味着对象检索不是共享的，而且可以安全的被调用者修改，不干扰其他调用者或线程所做的潜在修改。



### 10. MyBatis的执行器

MyBatis的执行器有三种基本执行器：

1. SimpleExecutor：每执行一次 update 或 select，就开启一个 Statement 对象，用完立刻关闭 Statement 对象；
2. ReuseExecutor：执行 update 或 select，以 SQL 作为 key 查找 Statement 对象，存在就使用，不存在就创建，用完后，不关闭 Statement 对象，而是放置于 Map 内，供下一次使用。简言之，就是重复使用 Statement 对象；
3. BatchExecutor：执行update（不支持select，jdbc批处理不支持select），使用addBatch()将所有的SQL都添加到批处理中，等待execBatch()方法统一执行，缓存了多个Statement对象。





## MySQL

[MySQL 三万字精华总结 + 面试100 问，和面试官扯皮绰绰有余（收藏系列） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/164519371)

### 1. MySQL架构

大体来说，MySQL主要分为Server和存储引擎两个部分。

**Server层包括，连接器、查询缓存、分析器、优化器、执行器等等**，涵盖了MySQL提供的绝大多数核心服务功能，以及所有的内置函数（日期、时间、数学、加密函数），所有跨存储引擎的功能都在server层实现。

存储引擎包括数据的存储和提取。

![preview](https://pic2.zhimg.com/v2-4ce9c07e160c6c3e48387bc98d5295e1_r.jpg)



### 2. 一条SQL语句的执行过程

1. MySQL应用程序客户端（Java、Python、Golang等语言的数据客户端）将SQL发送给Server
2. 首先查询缓存，如果缓存是打开的，服务器在接收到SQL之后，就不会直接去查询数据库，而是现在缓存中查询是否有响应的查询数据，如果存在，则直接返回给客户端。如果缓存没有打开，或者缓存中没有找到响应的数据，那么进行以下的操作。
3. 查询优化处理，生成执行计划：解析SQL、预处理、优化SQL执行计划
4. MySQL根据相应的执行计划完成整个查询
5. 将查询结果返回给客户端。



详细的过程分为以下步骤：

![img](https://img-blog.csdnimg.cn/20190109110912557.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Bjd2wxMjA2,size_16,color_FFFFFF,t_70)

1. 首先连接上数据库，使用的是连接器，连接器负责和客户端建立连接，获取权限，维护正常的连接。

   > **数据库的连接分为长连接和短连接，长连接是指，如果客户端长时间有持续的请求的话，就会一直使用一个连接而不断开，短连接是指如果客户端持续有请求的话，一次连接之后，会断开，下一次再发起请求申请会重新再建立一个连接。**
   >
   > 但是如果连接都使用长连接的话，你可能会发现，有时候 MySQL 占用内存涨的特别快，这是因为 MySQL 在执行过程中临时使用的内存是管理在连接对象里面的。这些资源会在连接断开的时候才会释放。所以如果长连接累积下来，可能导致内存占用太大，被系统强行杀掉（OOM），从现象上看就是 MySQL 异常重启了。
   >
   > 如何解决这个问题，有两种思路：
   >
   > 1. 定期断开长连接，使用了一段时间之后，或者该连接已经产生了较大的内存之后，就将这个连接断开，释放资源之后再重新连接。
   > 2. 如果你用的是 MySQL 5.7 或更新版本，可以在每次执行一个比较大的操作后，通过**执行 mysql_reset_connection 来重新初始化连接资源**。这个过程不需要重连和重新做权限验证，但是会将连接恢复到刚刚创建完时的状态。

2. 查询缓存

   建立连接之后就可以开始执行SQL语句，

   MySQL拿到SQL语句之后，会先进入缓存查询是否执行过这条SQL语句，在此之前执行的SQL-ResultSet都会被当作Key-value的形式保存进内存，如果在缓存中可以直接找到这个缓存语句，那么就将结果集直接返回给客户端。

   如果命中缓存的话，效率会很高。

   **但是大多数情况下都不建议使用缓存查询。**

   查询缓存的失效非常频繁，一旦表更新了，这个表中的所有查询缓存都会失效。

   对于更新压力比较大的表，也就是增删改的概率比较大的表，查询缓存的命中率非常低，除非业务属性导致这张表几乎就是一张静态表，那么这个表才比较适合用来做缓存查询。

   > 需要说明的是MySQL8以上的版本已经将缓存查询功能删除。

3. 分析器

   如果没有命中缓存，才是开始真正执行SQL语句的时候，首先MySQL需要直到语句执行的目的，这一步是对SQL语句的解析。

   分析器同时也兼顾了语法的校验，首先将SELECT等操作字符解析出来确定是哪种SQL操作，然后开始分析语法是否符合MySQL的语法规范。

4. 优化器

   优化器是在表里有多个索引的时候，决定使用哪个索引；或者在一个语句有多表关联（join）的时候，决定各个表的连接顺序。

5. 执行器

   分析器分析、优化器优化，然后到执行器的执行阶段。开始执行之前，首先要校验该用户是否有执行这个SQL的权限，如果没有权限就会返回没有权限的错误。

   如果有权限，就打开表继续执行。打开表的时候，执行器就会根据表的引擎定义，去使用这个引擎提供的接口。

   比如我们这个例子中的表 T 中，ID 字段没有索引，那么执行流程是这样的：

   1. 调用 InnoDB 引擎接口取这个表的第一行，判断 ID 值是不是 10，如果不是则跳过，如果是则将这行存在结果集中；

   2. 调用引擎接口取“下一行”，重复相同的判断逻辑，直到取到这个表的最后一行；

   3. 执行器将上述遍历过程中所有满足条件的行组成的记录集作为结果集返回给客户端。

   至此，这个语句就执行完成了。

   对于有索引的表，执行的逻辑也差不多。第一次调用的是“取满足条件的第一行”这个接口，之后循环取“满足条件的下一行”这个接口，这些接口都是引擎中已经定义好的。

   你会在数据库的慢查询日志中看到一个 rows_examined 的字段，表示这个语句执行过程中扫描了多少行。这个值就是在执行器每次调用引擎获取数据行的时候累加的。

   **在有些场景下，执行器调用一次，在引擎内部则扫描了多行，因此引擎扫描行数跟 rows_examined 并不是完全相同的。**



### 3. 数据库设计的三大范式

第一范式：列具有原子性， 数据库表中的每一个列都应该是不可再分的原子数据项。

第二范式：实体的属性要求完全依赖与主关键字，完全依赖是指，不能存在只依赖主关键字的一部分属性的情况

第三范式：任何非主属性不依赖于其他非主属性



### 4. varchar和char

char(n)：固定长度类型，例如char(10)，如果输入”abc“，那么三个字母占用三个char位置，但是剩下的七个char位置还是会保留，是空字节。

char的优点：效率高

char的缺点：占用空间

使用场景：文件的md5值，固定长度的存储数据，用固定长度的数据类型很合适



varchar(n)：可变长度数据类型，存储的结构是：数据类型+记录长度的字节。

从空间上选择varchar比较合适，从效率上选择char更好。



### 5. varchar(10)和varchar(20)

从MySQL4.1开始，varchar (N)中的N指的是该字段最多能存储多少个字符(characters)，不是字节数。

**不管是一个中英文字符或者数字、或者一个汉字，都当做一个字符。在4.1之前，N表示的是最大存储的字节数(bytes)。**

MySQL建立索引时如果没有限制索引的大小，索引长度会默认采用的该字段的长度，也就是说varchar(20)和varchar(255)对应的索引长度分别为20\*3(utf-8)(+2+1),255\*3(utf-8)(+2+1)，其中"+2"用来存储长度信息，“+1”用来标记是否为空，加载索引信息时用varchar(255)类型会占用更多的内存； (备注：当字段定义为非空的时候，是否为空的标记将不占用字节)

通常情况下使用varchar(20)和varchar(255)保持'hello'占用的空间都是一样的，但使用长度较短的列却有巨大的优势。较大的列使用更多的内存，因为MySQL通常会分配固定大小的内存块来保存值，这对排序或使用基于内存的临时表尤其不好。同样的事情也会发生在使用文件排序或者基于磁盘的临时表的时候。



### 6. 索引

索引的出现是为了提高数据的查询效率，就像书的目录一样。一本500页的书，如果你想快速找到其中的某一个知识点，在不借助目录的情况下，那我估计你可得找一会儿。同样，对于数据库的表而言，索引其实就是它的“目录”。

同样索引也会带来很多负面影响：创建索引和维护索引需要耗费时间，这个时间随着数据量的增加而增加；索引需要占用物理空间，不光是表需要占用数据空间，每个索引也需要占用物理空间；当对表进行增、删、改、的时候索引也要动态维护，这样就降低了数据的维护速度。

**建立索引的原则**：

1. 选择唯一性索引

   唯一性索引的值是唯一的，可以更快速的通过该索引来确定某条记录。例如，学生表中学号是具有唯一性的字段。为该字段建立唯一性索引可以很快的确定某个学生的信息。如果使用姓名的话，可能存在同名现象，从而降低查询速度。

2. 为经常需要排序、分组和联合操作的字段建立索引

   经常需要ORDER BY、GROUP BY、DISTINCT和UNION等操作的字段，排序操作会浪费很多时间。如果为其建立索引，可以有效地避免排序操作。

3. 为常作为查询条件的字段建立索引

   如果某个字段经常用来做查询条件，那么该字段的查询速度会影响整个表的查询速度。因此，为这样的字段建立索引，可以提高整个表的查询速度。

4. 限制索引的数目

   索引的数目不是越多越好。每个索引都需要占用磁盘空间，索引越多，需要的磁盘空间就越大。修改表时，对索引的重构和更新很麻烦。越多的索引，会使更新表变得很浪费时间。

5. 尽量使用数据量少的索引

   如果索引的值很长，那么查询的速度会受到影响。例如，对一个CHAR(100)类型的字段进行全文检索需要的时间肯定要比对CHAR(10)类型的字段需要的时间要多。

6. 尽量使用前缀来索引

   如果索引字段的值很长，最好使用值的前缀来索引。例如，TEXT和BLOG类型的字段，进行全文检索会很浪费时间。如果只检索字段的前面的若干个字符，这样可以提高检索速度。

7. 最左前缀匹配原则，非常重要的原则

   mysql会一直向右匹配直到遇到范围查询(>、<、between、like)就停止匹配，比如a 1=”” and=”” b=”2” c=”“> 3 and d = 4 如果建立(a,b,c,d)顺序的索引，d是用不到索引的，如果建立(a,b,d,c)的索引则都可以用到，a,b,d的顺序可以任意调整。

8. =和in可以乱序

   比如a = 1 and b = 2 and c = 3 建立(a,b,c)索引可以任意顺序，mysql的查询优化器会帮你优化成索引可以识别的形式

9. 尽量选择区分度高的列作为索引

   区分度的公式是count(distinct col)/count(*)，表示字段不重复的比例，比例越大我们扫描的记录数越少，唯一键的区分度是1，而一些状态、性别字段可能在大数据面前区分度就 是0，那可能有人会问，这个比例有什么经验值吗？使用场景不同，这个值也很难确定，一般需要join的字段我们都要求是0.1以上，即平均1条扫描10条记录

10. 索引列不能参与计算，保持列“干净”

    比如from_unixtime(create_time) = ’2014-05-29’就不能使用到索引，原因很简单，b+树中存的都是数据表中的字段值，但进行检索时，需要把所有元素都应用函数才能比较，显然成本 太大。所以语句应该写成create_time = unix_timestamp(’2014-05-29’);

11. 尽量的扩展索引，不要新建索引

    比如表中已经有a的索引，现在要加(a,b)的索引，那么只需要修改原来的索引即可

12. 当单个索引字段查询数据很多，区分度都不是很大时，则需要考虑建立联合索引来提高查询效率

不适合建立索引的情况：

1. 对于查询中**很少涉及的列或者重复值比较多**的列，不宜建立索引
2. 对于一些特殊数据结构不适合建立索引，例如txt



### 7. MySQL建立索引

索引分为聚簇索引和非聚簇索引，聚簇索引是根据存储的物理位置为顺序，非聚簇索引则不一样。

聚簇索引可以加快多行检索的速度，而非聚簇索引对于单行的检索速度更快。

1. 创建普通索引

   最基础的索引类型，没有任何限制，直接创建索引：

   ```SQL
   CREATE <index_name> ON <table_name> (<column_name> [<length>] [ASC | DESC])
   ```

   语法说明如下：

   - `<索引名>`：指定索引名。一个表可以创建多个索引，但每个索引在该表中的名称是唯一的。
   - `<表名>`：指定要创建索引的表名。
   - `<列名>`：指定要创建索引的列名。通常可以考虑将查询语句中在 JOIN 子句和 WHERE 子句里经常出现的列作为索引列。
   - `<长度>`：可选项。指定使用列前的 length 个字符来创建索引。使用列的一部分创建索引有利于减小索引文件的大小，节省索引列所占的空间。在某些情况下，只能对列的前缀进行索引。索引列的长度有一个最大上限 255 个字节（MyISAM 和 InnoDB 表的最大上限为 1000 个字节），如果索引列的长度超过了这个上限，就只能用列的前缀进行索引。另外，BLOB 或 TEXT 类型的列也必须使用前缀索引。
   - `ASC|DESC`：可选项。`ASC`指定索引按照升序来排列，`DESC`指定索引按照降序来排列，默认为`ASC`。

2. 创建表的时候创建索引

   ```SQL
   CREATE <index_name> ON <> (<列名> [<长度>] [ ASC | DESC])
   ```

   索引也可以在创建表（CREATE TABLE）的同时创建。在 CREATE TABLE 语句中添加以下语句。语法格式：

   ```sql
   CONSTRAINT PRIMARY KEY [索引类型] (<列名>,…)
   ```

   在 CREATE TABLE 语句中添加此语句，表示在创建新表的同时创建该表的主键。

   语法格式：

   ```sql
   KEY | INDEX [<索引名>] [<索引类型>] (<列名>,…)
   ```

   在 CREATE TABLE 语句中添加此语句，表示在创建新表的同时创建该表的索引。

   语法格式：

   ```sql
   UNIQUE [ INDEX | KEY] [<索引名>] [<索引类型>] (<列名>,…)
   ```

   在 CREATE TABLE 语句中添加此语句，表示在创建新表的同时创建该表的唯一性索引。

   语法格式：

   ```sql
   FOREIGN KEY <索引名> <列名>
   ```

   在 CREATE TABLE 语句中添加此语句，表示在创建新表的同时创建该表的外键。

   在使用 CREATE TABLE 语句定义列选项的时候，可以通过直接在某个列定义后面添加 PRIMARY KEY 的方式创建主键。而当主键是由多个列组成的多列索引时，则不能使用这种方法，只能用在语句的最后加上一个 PRIMARY KRY(<列名>，…) 子句的方式来实现。

3. 修改表并添加索引

   CREATE INDEX 语句可以在一个已有的表上创建索引，ALTER TABLE 语句也可以在一个已有的表上创建索引。在使用 ALTER TABLE 语句修改表的同时，可以向已有的表添加索引。具体的做法是在 ALTER TABLE 语句中添加以下语法成分的某一项或几项。

   语法格式：

   ```sql
   ADD INDEX [<索引名>] [<索引类型>] (<列名>,…)
   ```

   在 ALTER TABLE 语句中添加此语法成分，表示在修改表的同时为该表添加索引。

   语法格式：

   ```sql
   ADD PRIMARY KEY [<索引类型>] (<列名>,…)
   ```

   在 ALTER TABLE 语句中添加此语法成分，表示在修改表的同时为该表添加主键。

   语法格式：

   ```sql
   ADD UNIQUE [ INDEX | KEY] [<索引名>] [<索引类型>] (<列名>,…)
   ```

   在 ALTER TABLE 语句中添加此语法成分，表示在修改表的同时为该表添加唯一性索引。

   语法格式：

   ```sql
   ADD FOREIGN KEY [<索引名>] (<列名>,…)
   ```

   在 ALTER TABLE 语句中添加此语法成分，表示在修改表的同时为该表添加外键。



### 8. 索引的底层数据结构

<img src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9jZG4ubmxhcmsuY29tL3l1cXVlLzAvMjAyMC9wbmcvNDM3NjQyLzE1ODIyNjMxMjA4MjMtYjgxMDIwNDUtYzZiNy00M2FiLTkwYjYtNjI0ODk4NWJlZmQzLnBuZw?x-oss-process=image/format,png" alt="img" style="zoom: 67%;" />

**不加索引**

不加索引的情况下，SQL： select * from t where t.col2=89 ，需要从表的第一行一行遍历，比对col2的值是否等于89，这样需要比对6次才能查到。这只是只有几行记录的表，那如果是百万级千万级的表呢就比较的次数就O(n)增长。

**加索引**

如果col2这列加了索引，mysql内部会维护一个数据结构。假设mysql用的数据结构是红黑树（右子树的元素大于根节点，左子树的元素小于根节点）的数据结构建立索引，就像上图右边那样。这样的话，刚才的那条SQL是不是只需要2次磁盘IO就查到了（先加载根节点进行比较，大于根节点的值，则遍历右子树进行比较），快了很多。

**索引可选的数据结构**

- 二叉树
- 红黑树
- Hash
- B-Tree



**二叉树**

**二叉树在某些情况下退化成了链表**，比如：递增递减。当二叉树退化成链表之后，效率和没有加索引并没有太大的区别。当二叉树像上图一样退化成链表后，我们去查col1=6的记录就需要从二叉树的根节点依次遍历，遍历6次才能查到，和不加索引从表里一行行的遍历没太大差别。这是二叉树作为索引底层数据结构的弊端之一。



**红黑树**

1. 所有结点要么是红色要么是黑色
2. 根结点和叶子节点（NIL）必须是黑色
3. 红结点的两个子结点必须是黑色
4. 任意一结点到每个叶子结点的路径都包含数量相同的黑结点

但是红黑树仍然存在一个问题，**也就是树的高度太高导致查询效率变慢。**

那么就可以采用横向发展的思想，每个节点不止两个子节点，而是3个、4个、5个...这样下来横向存储就会更大，树的高度就被减小了。这就是B树。



**B-Tree**

- 阶数：一个节点最多有多少个孩子节点。(一般用字母m表示)
- 关键字：节点上的数值就是关键字
- 度：一个节点拥有的子节点的数量。



1. 所有的叶子节点都在同一个层级上，并且不带信息。
2. 一个结点包含的关键字个数有一个区间，这个区间和磁盘块大小t有关，t≥2。除根节点以外的节点，至少有t-1个关键字，但是小于2t-1个关键字，也就是区间[t-1, 2t-1]。
3. 一个包含x个关键字的节点有x+1个子节点，这个很好理解，对一个区间切x刀，可以将区间划成x+1个区间。
4. 一个节点中的所有关键字都按照升序排列，因此k1关键字和k2关键字之间的所有子节点的关键字都在(k1, k2)区间。
5. 与二叉排序树不同， B-Tree的搜索是从根结点开始，根据结点的孩子树做多路分支选择，而二叉排序树做的是二路分支选择，每一次判断都会进行一次磁盘 I/O操作。
6. 与其他平很二叉树类似，B-Tree查找、插入和删除操作的时间复杂度为O(logN)量级。

[图解：什么是B树？一文读懂B-树 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/146252512)



**B+Tree**

B+Tree是由B-Tree衍生而来，也是一种多路搜索树。

一棵m阶的B+树主要有这些特点：

- 每个结点至多有m个子女;
- 非根节点关键值个数范围：⌈m/2⌉ - 1 <= k <= m-1
- 相邻叶子节点是通过指针连起来的，并且是关键字大小排序的。

B+Tree和B-Tree的主要区别在于：

- B-树内部节点是保存数据的；而B+树内部节点是不保存数据的，只作索引作用，它的叶子节点才保存数据。
- B+树相邻的叶子节点之间是通过链表指针连起来的，B-树没有。
- 在查找过程中，B-树在找到具体的数值以后就结束，而B+树则需要通过索引找到叶子结点中的数据才结束。
- B-树中任何一个关键字出现且只出现在一个结点中，而B+树可以出现多次。

[B树和B+树的插入、删除图文详解 - nullzx - 博客园 (cnblogs.com)](https://www.cnblogs.com/nullzx/p/8729425.html)

[MySQL索引底层：B+树详解 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/351240279)



**InnoDB和MyISAM的一些不同**

MyISAM是使用非聚簇索引，索引文件和数据文件是分离的；

InnoDB是使用聚簇索引

- 表数据文件本身就是按B+Tree组织的一个索引结构文件
- 聚集索引-叶子节点包含了完整的数据记录
- 为什么InnoDB表必须有主键，并且推荐使用整型的自增主键？
- 为什么非主键索引结构叶子节点存储的是主键值？（一致性和节省存储空间）

[MySQL索引底层数据结构及原理深入分析](https://blog.csdn.net/ibigboy/article/details/104494719)// 写得非常好



### 9. MySQL为什么采用B+Tree

1. B+树的磁盘读写代价更低：B+树的内部节点并没有指向关键字具体信息的指针，因此其内部节点相对B树更小，如果把所有同一内部节点的关键字存放在同一盘块中，那么盘块所能容纳的关键字数量也越多，一次性读入内存的需要查找的关键字也就越多，相对IO读写次数就降低了。
2. B+树的查询效率更加稳定：由于非终结点并不是最终指向文件内容的结点，而只是叶子结点中关键字的索引。所以任何关键字的查找必须走一条从根结点到叶子结点的路。所有关键字查询的路径长度相同，导致每一个数据的查询效率相当。
3. 由于B+树的数据都存储在叶子结点中，分支结点均为索引，方便扫库，只需要扫一遍叶子结点即可，但是B树因为其分支结点同样存储着数据，我们要找到具体的数据，需要进行一次中序遍历按序来扫，所以B+树更加适合在区间查询的情况，所以通常B+树用于数据库索引。

>今天看了几篇文章，自己总结一下。
>
>数据库使用B+树肯定是为了提升查找效率。
>
>但是具体如何提升查找效率呢？
>
>查找数据，最简单的方式是顺序查找。但是对于几十万上百万，甚至上亿的数据库查询就很慢了。
>
>所以要对查找的方式进行优化，熟悉的二分查找，二叉树可以把速度提升到O(log(n,2))，查询的瓶颈在于树的深度，最坏的情况要查找到二叉树的最深层，由于，每查找深一层，就要访问更深一层的索引文件。在多达数G的索引文件中，这将是很大的开销。所以，尽量把数据结构设计的更为‘矮胖’一点就可以减少访问的层数。在众多的解决方案中，B/B+树很好的适合。B树定义具体可以查阅，简而言之就是中间节点可以多余两个子节点，而且中间的元素可以是一个域。相比B树，B+树的父节点也必须存在于子节点中，是其中最大或者最小元素，B+树的节点只存储索引key值，具体信息的地址存在于叶子节点的地址中。这就使以页为单位的索引中可以存放更多的节点。减少更多的I/O支出。因此，B+树成为了数据库比较优秀的数据结构。
>
>MySQL中MyIsAM和InnoDB都是采用的B+树结构。不同的是前者是非聚集索引，后者主键是聚集索引，所谓聚集索引是物理地址连续存放的索引，在取区间的时候，查找速度非常快，但同样的，插入的速度也会受到影响而降低。聚集索引的物理位置使用链表来进行存储。



### 10. 聚簇索引和非聚簇索引

[聚簇索引与非聚簇索引（也叫二级索引）--最清楚的一篇讲解 - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1541265#:~:text=通俗点讲. 聚簇索引：将数据存储与索引放到了一块，找到索引也就找到了数据. 非聚簇索引：将数据存储于索引分开结构，索引结构的叶子节点指向了数据的对应行，myisam通过key_buffer把索引先缓存到内存中，当需要访问数据时（通过索引访问数据），在内存中直接搜索索引，然后通过索引找到磁盘相应数据，这也就是为什么索引不在key,buffer命中时，速度慢的原因. 澄清一个概念 ：innodb中，在聚簇索引之上创建的索引称之为辅助索引，辅助索引访问数据总是需要二次查找，非聚簇索引都是辅助索引，像复合索引、前缀索引、唯一索引，辅助索引叶子节点存储的不再是行的物理位置，而是主键值。.)

- 聚簇索引：将数据存储与索引放到了一块，找到索引也就找到了数据
- 非聚簇索引：将数据存储于索引分开结构，索引结构的叶子节点指向了数据的对应行，MyISAM通过key_buffer把索引先缓存到内存中，当需要访问数据时（通过索引访问数据），在内存中直接搜索索引，然后通过索引找到磁盘相应数据，这也就是为什么索引不在key buffer命中时，速度慢的原因

**聚簇索引的叶子节点就是数据节点，而非聚簇索引的叶子节点仍然是索引节点，只不过有指向对应数据块的指针。**

使用聚簇索引和非聚簇索引的场景

| 场景                       | 使用聚簇索引 | 使用非聚簇索引 |
| -------------------------- | ------------ | -------------- |
| 列经常被分组排序           | 应该         | 应该           |
| 返回某个范围区间的数据结果 | 应该         | 不应该         |
| 几乎没有不同值             | 不应该       | 不应该         |
| 小量的不同值               | 应该         | 不应该         |
| 大量的不同值               | 不应该       | 应该           |
| 频繁更新的列               | 不应该       | 应该           |
| 外键列                     | 应该         | 应该           |
| 主键列                     | 应该         | 应该           |
| 频繁修改索引列             | 不应该       | 应该           |



### 11. 哈希索引

哈希索引可以将时间复杂度减小到O(1)，但是Hash没有办法做到有序。无法用于排序和分组，只支持精确查找，无法用于部分查找和范围查找。

InnoDB有一个特殊的功能，叫做“自适应哈希索引”，当某个索引值被频繁使用，会在B+Tree的索引之上再建立一个哈希索引，以缩短这些频繁精确查找的索引时间。



### 12. 最左前缀原则

MySQL使用联合索引时，需要满足最左前缀原则，

假设，对一个一个 2 列的索引 (name, age)，对 (name)、(name, age) 上建立了索引；

此外，对一个 3 列的索引 (name, age, sex)，对 (name)、(name, age)、(name, age, sex) 上建立了索引。

> 一般来说，不对sex建立索引，因为sex存在大量重复值，因此更换为location。

此外，对一个 3 列的索引 (name, age, location)，对 (name)、(name, age)、(name, age, location) 上建立了索引。

1. B+Tree的数据项是一个复合数据结构，比如：name、age、sex，B+Tree是按照从左到右的顺序来建立搜索树的，比如：当(小明，22，男)这样的数据来检索的时候，B+Tree会优先比较name，再确定下一步的搜索方向，如果name相同再从左向右依次比较age、name，得到检索结果。但是对于（22，上海）这样的检索数据结构，是没有办法索引的，因为建立索引的时候，name就已经是第一个比较因子，必须要先根据name的索引结果再确定下一步去哪里查询。
2. 当 (小明, 男) 这样的数据来检索时，B+ 树可以用 name 来指定搜索方向，但下一个字段 age 的缺失，所以只能把名字等于小明的数据都找到，然后再匹配性别是男的数据了， 这个是非常重要的性质，即索引的最左匹配特性。

最左前缀原则的补充：

1. 最左前缀匹配原则会一直向右匹配，直到出现范围查询（>、<、between、like）就停止索引。比如：a = 1 and b = 2 and c > 3 and d = 4 如果建立 (a, b, c, d) 顺序的索引，d 是用不到索引的。如果建立 (a, b, d, c) 的索引则都可以用到，a、b、d 的顺序可以任意调整。
2. = 和 in 可以乱序，比如：a = 1 and b = 2 and c = 3 建立 (a, b ,c) 索引可以任意顺序，MySQL 的优化器会优化成索引可以识别的形式。



### 13. 什么情况索引失效？

索引失效也即不走索引

1. 索引列参与表达式计算

   ```SQL
   SELECT 'sname' FROM 'stu' WHERE 'age' + 10 = 30;
   ```

2. 函数运算

   ```sql
   SELECT 'sname' FROM 'stu' WHERE LEFT('date',4) < 1990; 
   ```

3. 模糊查询-%temp%

   ```sql
   SELECT * FROM 'manong' WHERE `uname` LIKE '码农%' -- 走索引 
   SELECT * FROM 'manong' WHERE `uname` LIKE '%码农%' -- 不走索引 
   ```

4. 字符串与数字比较

   ```mysql
   CREATE TABLE 'a' ('a' char(10));
   EXPLAIN SELECT * FROM 'a' WHERE 'a'="1" — 走索引，这是字符串比较
   EXPLAIN SELECT * FROM 'a' WHERE 'a'= 1 — 不走索引，同样也是使用了函数运算，这是字符串和数字比较
   ```

5. 查询条件中有 or ，即使其中有条件带索引也不会使用

6. 正则表达式不使用索引

7. MySQL内部优化器会对SQL进行优化，如果优化器预估全表扫描比索引还快，就不使用索引



### 14. 有哪些查询的优化方法？

**减少请求的数据量**

1. 只返回必要的列：尽量不要使用SELECT *
2. 只返回必要的行：使用LIMIT语句
3. 缓存经常需要查询且不轻易改动的数据：使用缓存可以避免在数据库中进行查询，特别在要查询的数据经常被重复查询时，缓存带来的查询性能提升将会是非常明显的。



**减少服务器扫描的行数**

1. 最有效的方式是使用索引来覆盖查询



### 15. InnoDB和MyISAM

1. 事务：InnoDB支持事务，MyISAM不支持事务

2. 全文索引：InnoDB 5.6之后都支持全文索引

3. count()：InnoDB会一行一行扫描，而MyISAM会直接返回结果。[MySQL count()函数 - MySQL教程™ (yiibai.com)](https://www.yiibai.com/mysql/count.html)

   InnoDB 不保存表的具体行数，执行select count(*) from table 时需要全表扫描。而 MyISAM用一个变量保存了整个表的行数，执行上述语句时只需要读出该变量即可，速度很快；

4. 外键：InnoDB支持外键，MyISAM不支持外键

5. 锁：InnoDB支持行锁，MyISAM只支持表锁

6. 索引类型：InnoDB是聚簇索引，InnoDB是聚集索引，使用B+Tree作为索引结构，数据文件是和（主键）索引绑在一起的（表数据文件本身就是按B+Tree组织的一个索引结构），必须要有主键，通过主键索引效率很高。但是辅助索引需要两次查询，先查询到主键，然后再通过主键查询到数据。因此，主键不应该过大，因为主键太大，其他索引也都会很大。

   MyISAM是非聚簇索引，索引和数据文件是分离的，索引保存的是数据文件的指针。主键索引和辅助索引是独立的。



### 16. 数据库的水平拆分和垂直拆分

- **水平切分**

水平切分是将同一个表中的记录**拆分到多个结构相同**的表中。当一个表的数据不断增多时，水平切分是必然的选择，它可以将数据分布到集群的不同节点上，从而缓存单个数据库的压力。

- **垂直切分**

垂直切分是**将一张表按列切分成多个表**，通常是按照列的关系密集程度进行切分，也可以利用垂直切分将经常被使用的列和不经常被使用的列切分到不同的表中。例如：将原来的电商数据库垂直切分成商品数据库、用户数据库等。



### 17. 主从复制

在实际的生产中，为了解决Mysql的单点故障已经提高MySQL的整体服务性能，一般都会采用主从复制。

比如：在复杂的业务系统中，有一句sql执行后导致锁表，并且这条sql的的执行时间有比较长，那么此sql执行的期间导致服务不可用，这样就会严重影响用户的体验度。

主从复制分为**主服务器写入**，**从服务器读取**，MySQL的主从复制是一个异步的过程，这样读写分离的过程能够是整体的服务性能提高，即使写操作时间比较长，也不影响读操作的进行。

Mysql的主从复制中主要有**三个线程**：`master（binlog dump thread）、slave（I/O thread 、SQL thread）`，Master一条线程和Slave中的两条线程。

`master（binlog dump thread）`主要负责Master库中有数据更新的时候，会按照`binlog`格式，将更新的事件类型写入到主库的`binlog`文件中。

并且，Master会创建`log dump`线程通知Slave主库中存在数据更新，这就是为什么主库的binlog日志一定要开启的原因。

`I/O thread`线程在Slave中创建，该线程用于请求Master，Master会返回binlog的名称以及当前数据更新的位置、binlog文件位置的副本。

SQL线程也是在Slave中创建的，当Slave检测到中继日志有更新，就会将更新的内容同步到Slave数据库中，这样就保证了主从的数据的同步。



[小白都能懂的Mysql主从复制原理（原理+实操） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/164518315)



**主从复制的延迟问题**

主从复制产生延迟的原因主要来自于

如果一个Master开放给很多Slave，这样会产生大并发的更新操作，但是服务器里读取binlog的线程只有一个，当某个 SQL 在从服务器上执行的时间稍长或者由于某个 SQL 要进行锁表就会导致主服务器的 SQL 大量积压，未被同步到从服务器里。这就导致了主从不一致， 也就是主从延迟。

其实主从复制的延迟问题没有特别好的解决办法，因为所有的 SQL 必须都要在从服务器里面执行一遍，但是主服务器如果不断的有更新操作源源不断的写入，那么一旦有延迟产生，那么延迟加重的可能性就会原来越大。

我们知道因为主服务器要负责更新操作， 它对安全性的要求比从服务器高，所有有些设置可以修改，比如sync_binlog=1，innodb_flush_log_at_trx_commit = 1 之类的设置，而 slave 则不需要这么高的数据安全，完全可以将 sync_binlog 设置为 0 或者关闭 binlog、innodb_flushlog、innodb_flush_log_at_trx_commit 也 可以设置为 0 来提高 SQL 的执行效率。

增加从服务器，这个目的还是分散读的压力， 从而降低服务器负载。



### 18. 事务

ACID

1. 原子性*[atomicity](javascript:;)*事务是最小的执行单位，不允许分割。事务的原子性确保动作要么全部完成，要么完全不起作用；
2. 一致性*[consistency](javascript:;)*执行事务前后，数据库从一个一致性状态转换到另一个一致性状态。
3. 隔离性*[isolation](javascript:;)*并发访问数据库时，一个用户的事物不被其他事务所干扰，各并发事务之间数据库是独立的；
4. 持久性*[durability](javascript:;)*一个事务被提交之后。它对数据库中数据的改变是持久的，即使数据库 发生故障也不应该对其有任何影响。





















