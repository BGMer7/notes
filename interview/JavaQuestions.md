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







### 13. synchronized





### 14. synchronized的使用场景





### 15. jdk1.6之后的synchronized关键字做了哪些优化？





### 16. synchronized和ReenTrantLock








### 17. synchronized和volatile







### 18. ReentrantReadWriteLock 







### 19. 乐观锁(CAS, compare and swap)

[重新认识CAS 跟 AQS 你了解乐观锁和悲观锁吗？ - Java野生程序猿 - 博客园 (cnblogs.com)](https://www.cnblogs.com/jiahaoJAVA/p/11309668.html)

















### 20. 乐观锁常用的两种实现方式





### 21. CAS和synchronized







### 22. 悲观锁









### 23. Java原子类









### 24. atomic的原理







### 25. 同步器AQS





### 26. AQS对于资源的共享模式







### 27. semaphore和ReenterantLock







### 28. Cyclic和CountDownLatch







### 29. Thread pool







### 30. 创建线程池的常用参数







### 31. execute()和submit()







### 32. Fork和Join并行框架的理解





### 33. JDK中的并发容器







### 34. CopyOnWriteArrayList









### 35. BlockingQueue







### 36. Java内存模型









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



























