# Java Features

## Java 8 Features

### Lambda expression

#### Introduction

`Java 8` 早已经在 2014 年 3 月 18 日发布，毫无疑问 `Java 8` 对 Java 来说绝对算得上是一次重大版本更新，它包含了十多项语言、库、工具、JVM 等方面的十多项新特性。比如提供了语言级的匿名函数，也就是被官方称为 `Lambda` 的表达式语法（外界也称为闭包，`Lambda` 的引入也让流式操作成为可能，减少了代码编写的复杂性），比如函数式接口，方法引用，重复注解。再比如 `Optional` 预防空指针，`Stearm` 流式操作，`LocalDateTime` 时间操作等。



上面说了这次是介绍 `Lambda` 表达式，为什么要介绍函数接口呢？其实 Java 中的函数接口在使用时，可以隐式的转换成 `Lambda` 表达式，在 `Java 8` 中已经有很多接口已经声明为函数接口，如 Runnable、Callable、Comparator 等。

函数接口的例子可以看下 `Java 8` 中的 `Runnable` 源码（去掉了注释）。

```java 
package java.lang;

@FunctionalInterface
public interface Runnable {
    public abstract void run();
}

```





#### Function Interface

对于什么样子的接口才是函数接口，有一个很简单的定义，也就是只有`一个抽象函数`的接口，函数接口使用注解 `@FunctionalInterface` 进行声明（注解声明不是必须的，如果没有注解，也是只有一个抽象函数，依旧会被认为是函数接口）。多一个或者少一个抽象函数都不能定义为函数接口，如果使用了函数接口注解又不止一个抽象函数，那么编译器会拒绝编译。函数接口在使用时候可以隐式的转换成 Lambda 表达式。



`Java 8` 中很多有很多不同功能的函数接口定义，都放在了 `Java 8` 新增的 `java.util.function` 包内。下面是一些关于 `Java 8` 中函数接口功能的描述。

| 序号                     | 接口 & 描述                                                  |
| :----------------------- | ------------------------------------------------------------ |
| **BiConsumer**           | 代表了一个接受两个输入参数的操作，并且不返回任何结果         |
| **BiFunction**           | 代表了一个接受两个输入参数的方法，并且返回一个结果           |
| **BinaryOperator**       | 代表了一个作用于于两个同类型操作符的操作，并且返回了操作符同类型的结果 |
| **BiPredicate**          | 代表了一个两个参数的 boolean 值方法                          |
| **BooleanSupplier**      | 代表了 boolean 值结果的提供方                                |
| **Consumer**             | 代表了接受一个输入参数并且无返回的操作                       |
| **DoubleBinaryOperator** | 代表了作用于两个 double 值操作符的操作，并且返回了一个 double 值的结果。 |
| **DoubleConsumer**       | 代表一个接受 double 值参数的操作，并且不返回结果。           |
| **DoubleFunction**       | 代表接受一个 double 值参数的方法，并且返回结果               |
| **DoublePredicate**      | 代表一个拥有 double 值参数的 boolean 值方法                  |
| **DoubleSupplier**       | 代表一个 double 值结构的提供方                               |
| **DoubleToIntFunction**  | 接受一个 double 类型输入，返回一个 int 类型结果。            |
| **DoubleToLongFunction** | 接受一个 double 类型输入，返回一个 long 类型结果             |
| **DoubleUnaryOperator**  | 接受一个参数同为类型 double, 返回值类型也为 double 。        |
| **Function**             | 接受一个输入参数，返回一个结果。                             |
| **IntBinaryOperator**    | 接受两个参数同为类型 int, 返回值类型也为 int 。              |
| **IntConsumer**          | 接受一个 int 类型的输入参数，无返回值 。                     |
| **IntFunction**          | 接受一个 int 类型输入参数，返回一个结果 。                   |
| **IntPredicate**         | 接受一个 int 输入参数，返回一个布尔值的结果。                |
| **IntSupplier**          | 无参数，返回一个 int 类型结果。                              |
| **IntToDoubleFunction**  | 接受一个 int 类型输入，返回一个 double 类型结果 。           |
| **IntToLongFunction**    | 接受一个 int 类型输入，返回一个 long 类型结果。              |
| **IntUnaryOperator**     | 接受一个参数同为类型 int, 返回值类型也为 int 。              |
| **LongBinaryOperator**   | 接受两个参数同为类型 long, 返回值类型也为 long。             |
| **LongConsumer**         | 接受一个 long 类型的输入参数，无返回值。                     |
| **LongFunction**         | 接受一个 long 类型输入参数，返回一个结果。                   |
| **LongPredicate**        | 接受一个 long 输入参数，返回一个布尔值类型结果。             |
| **LongSupplier**         | 无参数，返回一个结果 long 类型的值。                         |
| **LongToDoubleFunction** | 接受一个 long 类型输入，返回一个 double 类型结果。           |
| **LongToIntFunction**    | 接受一个 long 类型输入，返回一个 int 类型结果。              |
| **LongUnaryOperator**    | 接受一个参数同为类型 long, 返回值类型也为 long。             |
| **ObjDoubleConsumer**    | 接受一个 object 类型和一个 double 类型的输入参数，无返回值。 |
| **ObjIntConsumer**       | 接受一个 object 类型和一个 int 类型的输入参数，无返回值。    |
| **ObjLongConsumer**      | 接受一个 object 类型和一个 long 类型的输入参数，无返回值。   |
| **Predicate**            | 接受一个输入参数，返回一个布尔值结果。                       |
| **Supplier**             | 无参数，返回一个结果。                                       |
| **ToDoubleBiFunction**   | 接受两个输入参数，返回一个 double 类型结果                   |
| **ToDoubleFunction**     | 接受一个输入参数，返回一个 double 类型结果                   |
| **ToIntBiFunction**      | 接受两个输入参数，返回一个 int 类型结果。                    |
| **ToIntFunction**        | 接受一个输入参数，返回一个 int 类型结果。                    |
| **ToLongBiFunction**     | 接受两个输入参数，返回一个 long 类型结果。                   |
| **ToLongFunction**       | 接受一个输入参数，返回一个 long 类型结果。                   |
| **UnaryOperator**        | 接受一个参数为类型 T, 返回值类型也为 T。                     |



示例：

```java 
package com.gatsby;

import java.util.ArrayList;
import java.util.List;
import java.util.function.Consumer;

/**
 * @author caijinyang
 * @classname LambdaTest
 * @description
 * @date 2023/3/31
 **/

public class LambdaTest {

    public static void main(String[] args) {
        List<Integer> oddList = new ArrayList<>();
        List<Integer> evenList = new ArrayList<>();

        // 此处的Consumer<Integer>可以直接替换为IntConsumer
        Consumer<Integer> storeNumber = n -> {
            if (n % 2 == 0) {
                evenList.add(n);
            } else {
                oddList.add(n);
            }
        };

        Consumer<List<Integer>> printList = list -> list.forEach(n -> System.out.println(n));

        storeNumber.accept(10);
        storeNumber.accept(15);
        storeNumber.accept(25);
        storeNumber.accept(30);

        System.out.println("--- Odd number ---");

        printList.accept(oddList);

        System.out.println("--- Even number ---");

        printList.accept(evenList);
    }
}

```



将Consumer作为入参：

```java 
private static void printArray(String[] array,
                               Consumer<String> name,
                               Consumer<String> gender) {
    for (String info : array) {
        name.andThen(gender).accept(info);
    }
}

public static void testConsumerArray() {
    String[] array = {"大雄，男", "静香，女", "胖虎，男"};

    printArray(array,
               s -> System.out.print("姓名：" + s.split("，")[0] + "，"),
               s -> System.out.println("性别：" + s.split("，")[1] + "。"));

}
```



#### Grammar

Lambda 的语法主要是下面几种。

1. (params) -> expression
2. (params) -> {statements;}



Lambda 的语法特性。

1. 使用 `->` 分割 Lambda 参数和处理语句。
2. 类型可选，可以不指定参数类型，编译器可以自动判断。
3. 圆括号可选，如果只有一个参数，可以不需要圆括号，多个参数必须要圆括号。
4. 花括号可选，一个语句可以不用花括号，多个参数则花括号必须。
5. 返回值可选，如果只有一个表达式，可以自动返回，不需要 return 语句；花括号中需要 return 语法。 
6. Lambda 中引用的外部变量必须为 final 类型，内部声明的变量不可修改，内部声明的变量名称不能与外部变量名相同。



```java 
// 1.不需要参数，没有返回值，输出 hello
()->System.out.pritnln("hello");

// 2.不需要参数，返回 hello
()->"hello";

// 3. 接受2个参数(数字),返回两数之和 
(x, y) -> x + y  
  
// 4. 接受2个数字参数,返回两数之和 
(int x, int y) -> x + y  
  
// 5. 两个数字参数，如果都大于10，返回和，如果都小于10，返回差
(int x,int y) ->{
  if( x > 10 && y > 10){
    return x + y;
  }
  if( x < 10 && y < 10){
    return Math.abs(x-y);
  }
};
```



#### Usage

1. 对于函数接口

   从上面的介绍中已经知道了 Runnable 接口已经是函数接口了，它可以隐式的转换为 Lambda 表达式进行使用，通过下面的创建线程并运行的例子看下 `Java 8` 中 Lambda 表达式的具体使用方式。

   ```java
   /**
    * Lambda 的使用，使用 Runnable 例子
    * @throws InterruptedException
    */
   @Test
   public void createLambda() throws InterruptedException {
       // 使用 Lambda 之前
       Runnable runnable = new Runnable() {
           @Override
           public void run() {
               System.out.println("JDK8 之前的线程创建");
           }
       };
      new Thread(runnable).start();
      // 使用 Lambda 之后
      Runnable runnable1Jdk8 = () -> System.out.println("JDK8 之后的线程创建");
      new Thread(runnable1Jdk8).start();
      // 更加紧凑的方式
      new Thread(() -> System.out.println("JDK8 之后的线程创建")).start();
   }
   ```

   

   可以发现 `Java 8` 中的 `Lambda` 碰到了函数接口 Runnable，自动推断了要运行的 run 方法，不仅省去了 run 方法的编写，也代码变得更加紧凑。

   运行得到结果如下。

   ```shell
   JDK8 之前的线程创建
   JDK8 之后的线程创建
   JDK8 之后的线程创建
   ```

   

   上面的 Runnable 函数接口里的 run 方法是没有参数的情况，如果是有参数的，那么怎么使用呢？我们编写一个函数接口，写一个 `say` 方法接受两个参数。

   ```java
   /**
    * 定义函数接口
    */
   @FunctionalInterface
   public interface FunctionInterfaceDemo {
       void say(String name, int age);
   } 
   ```

   

   编写一个测试类。

   ```java
    /**
     * 函数接口，Lambda 测试
     */
    @Test
    public void functionLambdaTest() {
        FunctionInterfaceDemo demo = (name, age) -> System.out.println("我叫" + name + "，我今年" + age + "岁了");
        demo.say("金庸", 99);
    }
   ```

   

   输出结果。

   ```java
   我叫金庸，我今年99岁了。
   ```

2. 对于方法引用

   方法引用这个概念前面还没有介绍过，方法引用可以让我们直接访问类的实例或者方法，在 Lambda 只是执行一个方法的时候，就可以不用 `Lambda` 的编写方式，而用方法引用的方式：`实例/类::方法`。这样不仅代码更加的紧凑，而且可以增加代码的可读性。

   通过一个例子查看方法引用。

   通过一个例子查看方法引用。

   ```java
   @Getter
   @Setter
   @ToString
   @AllArgsConstructor
   static class User {
       private String name;
       private Integer age;
   }
   public static List<User> userList = new ArrayList<User>();
   static {
       userList.add(new User("A", 26));
       userList.add(new User("B", 18));
       userList.add(new User("C", 23));
       userList.add(new User("D", 19));
   }
   /**
    * 测试方法引用
    */
   @Test
   public void methodRef() {
       User[] userArr = new User[userList.size()];
       userList.toArray(userArr);
       // User::getAge 调用 getAge 方法
       Arrays.sort(userArr, Comparator.comparing(User::getAge));
       for (User user : userArr) {
           System.out.println(user);
       }
   }
   ```

   

   得到输出结果：

   ```text
   Jdk8Lambda.User(name=B, age=18) Jdk8Lambda.User(name=D, age=19) Jdk8Lambda.User(name=C, age=23) Jdk8Lambda.User(name=A, age=26
   ```

3. 对于遍历方式

   Lambda 带来了新的遍历方式，`Java 8` 为集合增加了 `foreach` 方法，它可以接受函数接口进行操作。下面看一下 `Lambda` 的集合遍历方式。

   ```java
   /**
    * 新的遍历方式
    */
   @Test
   public void foreachTest() {
       List<String> skills = Arrays.asList("java", "golang", "c++", "c", "python");
       // 使用 Lambda 之前
       for (String skill : skills) {
           System.out.print(skill+",");
       }
       System.out.println();
       // 使用 Lambda 之后
       // 方式1,forEach+lambda
       skills.forEach((skill) -> System.out.print(skill+","));
       System.out.println();
       // 方式2，forEach+方法引用
       skills.forEach(System.out::print);
   }
   ```

   

   运行得到输出。

   ```shell
   java,golang,c++,c,python,
   java,golang,c++,c,python,
   javagolangc++cpython
   ```

4. Stream操作

   得益于 `Lambda` 的引入，让 `Java 8` 中的流式操作成为可能，`Java 8` 提供了 stream 类用于获取数据流，它专注对数据集合进行各种高效便利操作，提高了编程效率，且同时支持串行和并行的两种模式汇聚计算。能充分的利用多核优势。

   流式操作如此强大， `Lambda` 在流式操作中怎么使用呢？下面来感受流操作带来的方便与高效。

   

   流式操作一切从这里开始。

   ```java
   // 为集合创建串行流
   stream()
   // 为集合创建并行流
   parallelStream()
   ```

   流式操作的去重 `distinct` 和过滤 `filter`。

   ```java
   @Test
   public void streamTest() {
       List<String> skills = Arrays.asList("java", "golang", "c++", "c", "python", "java");
       // Jdk8 之前
       for (String skill : skills) {
           System.out.print(skill + ",");
       }
       System.out.println();
       // Jdk8 之后-去重遍历
       skills.stream().distinct().forEach(skill -> System.out.print(skill + ","));
       System.out.println();
       // Jdk8 之后-去重遍历
       skills.stream().distinct().forEach(System.out::print);
       System.out.println();
       // Jdk8 之后-去重，过滤掉 ptyhon 再遍历
       skills.stream().distinct().filter(skill -> skill != "python").forEach(skill -> System.out.print(skill + ","));
       System.out.println();
       // Jdk8 之后转字符串
       String skillString = String.join(",", skills);
       System.out.println(skillString);
   }
   ```

   运行得到结果。

   ```shell
   java,golang,c++,c,python,java,
   java,golang,c++,c,python,
   javagolangc++cpython
   java,golang,c++,c,
   java,golang,c++,c,python,java
   ```

   流式操作的数据转换（也称映射）`map`。

   ```java
    /**
     * 数据转换
     */
    @Test
    public void mapTest() {
        List<Integer> numList = Arrays.asList(1, 2, 3, 4, 5);
        // 数据转换
        numList.stream().map(num -> num * num).forEach(num -> System.out.print(num + ","));
   
        System.out.println();
   
        // 数据收集
        Set<Integer> numSet = numList.stream().map(num -> num * num).collect(Collectors.toSet());
        numSet.forEach(num -> System.out.print(num + ","));
    }
   ```

   运行得到结果。

   ```shell
   1,4,9,16,25,
   16,1,4,9,25,
   ```

   流式操作的数学计算。

   ```java
   /**
    * 数学计算测试
    */
   @Test
   public void mapMathTest() {
       List<Integer> list = Arrays.asList(1, 2, 3, 4, 5);
       IntSummaryStatistics stats = list.stream().mapToInt(x -> x).summaryStatistics();
       System.out.println("最小值：" + stats.getMin());
       System.out.println("最大值：" + stats.getMax());
       System.out.println("个数：" + stats.getCount());
       System.out.println("和：" + stats.getSum());
       System.out.println("平均数：" + stats.getAverage());
       // 求和的另一种方式
       Integer integer = list.stream().reduce((sum, cost) -> sum + cost).get();
       System.out.println(integer);
   }
   ```

   运行得到结果。

   ```shell
   得到输出
   最小值：1
   最大值：5
   个数：5
   和：15
   平均数：3.0
   15
   ```

   



### Comparator Sort

#### Comparator

Comparator 是 Java 8 之前常用的排序方式，下面是排序 Person 类中的 `age` 字段的示例。

```java
public static void main(String[] args) {
    List<Person> list = new ArrayList<>();
    list.add(new Person("Chris", 20));
    list.add(new Person("Linda", 10));
    list.add(new Person("Jack", 30));
    Collections.sort(list, new Comparator<Person>() {
    		@Override
    		public int compare(Person o1, Person o2) {
    		    return o1.getAge() - o2.getAge();
    		}
		});
    for (Person person : list) {
        System.out.println(person);
    }
}
```

输出：

```text
Person{name='Linda', age=10}
Person{name='Chris', age=20}
Person{name='Jack', age=30}
```

这里是一个匿名内部类方式的实现，为了一行排序代码写的代码是不是有点多了？

#### Comparator+Lambda+Stream

```java
package com.gatsby;

import lombok.Data;

import java.util.ArrayList;
import java.util.Comparator;
import java.util.List;

/**
 * @author caijinyang
 * @classname ComparatorTest
 * @description
 * @date 2023/4/3
 **/

public class ComparatorTest {
    @Data
    public static class Person {
        private String name;
        private Integer age;

        public Person(String name, Integer age) {
            this.name = name;
            this.age = age;
        }
    }


    private static void testComparatorAndLambda(List<Person> list) {
        Comparator<Person> comparator = (Person p1, Person p2) -> p2.getAge().compareTo(p1.getAge());
        list.sort(comparator);
        list.forEach(System.out::println);
        list.sort(comparator.reversed());
        list.forEach(System.out::println);
    }

    private static void testComparator(List<Person> list) {
        list.sort(Comparator.comparing(Person::getAge));
        list.forEach(System.out::println);
    }

    private static void testStream(List<Person> list) {
        Comparator<Person> comparator = (Person p1, Person p2) -> p2.getAge().compareTo(p1.getAge());
        list.stream().sorted(comparator).forEach(System.out::println);
    }

    public static void main(String[] args) {
        List<Person> list = new ArrayList<>();
        list.add(new Person("Jack", 12));
        list.add(new Person("Simon", 19));
        list.add(new Person("Lemon", 9));
        list.add(new Person("Maron", 89));
        list.add(new Person("Tony", 30));
        testComparator(list);
        System.out.println("---------------");
        testComparatorAndLambda(list);
        System.out.println("---------------");
        testStream(list);

    }
}
```



### LocalDate LocalDateTime

常用的时间类：

常用的类有以下几个类。

| 时间相关类        | 介绍                       |
| ----------------- | -------------------------- |
| LocalDateTime     | 时间处理类，最高精确到纳秒 |
| LocalDate         | 时间处理类，最高精确到天   |
| DateTimeFormatter | 时间格式化                 |
| ZoneId            | 时区设置类                 |



#### Acquire Time

```java 
package com.gatsby.Java8Features;

import java.time.LocalDate;
import java.time.LocalDateTime;

/**
 * @author caijinyang
 * @classname LocalDateTest
 * @description
 * @date 2023/4/4
 **/

public class LocalDateTest {
    private static void nowTimeTest() {
        LocalDate date = LocalDate.now();

        System.out.println(date);
        System.out.println(date.getEra());
        System.out.println(date.getYear());
        System.out.println(date.getMonthValue());
        System.out.println(date.getDayOfMonth());
        System.out.println(date.getDayOfWeek());

        System.out.println("-------------------");

        LocalDateTime time = LocalDateTime.now();

        System.out.println(time);
        System.out.println(time.getHour());
        System.out.println(time.getMinute());
        System.out.println(time.getSecond());

    }

    public static void main(String[] args) {
        nowTimeTest();
    }

}

```



#### Create Time

```java 
private static void createTimeTest() {
    LocalDateTime time = LocalDateTime.of(2023, 4, 4, 16, 10);

    System.out.println(time);

    LocalDate date = LocalDate.of(2023, 4, 4);

    System.out.println(date);

    LocalTime localTime = LocalTime.of(16, 11);

    System.out.println(localTime);

}
```



#### Convert Time

```java
/**
* 日期转换
*/
@Test
public void convertTimeTest() {
    LocalDateTime parseTime = LocalDateTime.parse("2019-10-01T22:22:22.222");
    System.out.println("字符串时间转换：" + parseTime);

    LocalDate formatted = LocalDate.parse("20190101", DateTimeFormatter.BASIC_ISO_DATE);
    System.out.println("字符串时间转换-指定格式：" + formatted);

    // Date 转换成 LocalDateTime
    Date date = new Date();
    ZoneId zoneId = ZoneId.systemDefault();
    System.out.println("Date 转换成 LocalDateTime：" + LocalDateTime.ofInstant(date.toInstant(), zoneId));

    // LocalDateTime 转换成 Date
    LocalDateTime localDateTime = LocalDateTime.now();
    Date toDate = Date.from(localDateTime.atZone(ZoneId.systemDefault()).toInstant());
    System.out.println("LocalDateTime 转换成 Date：" + toDate);\
        
    // 当前时间转时间戳
    long epochMilli = LocalDateTime.now().toInstant(ZoneOffset.of("+8")).toEpochMilli();
    System.out.println("当前时间转时间戳：" + epochMilli);
    // 时间戳转换成时间
    LocalDateTime epochMilliTime = LocalDateTime.ofInstant(Instant.ofEpochMilli(epochMilli), ZoneId.systemDefault());
    System.out.println("时间戳转换成时间：" + epochMilliTime);
}

```



#### Format time

```java
/**
 * 日期格式化
 */
@Test
public void formatTest() {
    LocalDateTime now = LocalDateTime.now();
    System.out.println("当前时间：" + now);
    System.out.println("格式化后：" + now.format(DateTimeFormatter.ISO_LOCAL_DATE_TIME));
    System.out.println("格式化后：" + now.format(DateTimeFormatter.ISO_LOCAL_DATE));
    System.out.println("格式化后：" + now.format(DateTimeFormatter.ISO_LOCAL_TIME));
    System.out.println("格式化后：" + now.format(DateTimeFormatter.ofPattern("YYYY-MM-dd hh:mm:ss")));
}

```



#### Compare Time

```java 
/**
 * 时间比较
 */
@Test
public void diffTest() {
    LocalDateTime now = LocalDateTime.now();
    LocalDateTime yestory = now.minusDays(1);
    System.out.println(now + "在" + yestory + "之后吗?" + now.isAfter(yestory));
    System.out.println(now + "在" + yestory + "之前吗?" + now.isBefore(yestory));

    // 时间差
    long day = yestory.until(now, ChronoUnit.DAYS);
    long month = yestory.until(now, ChronoUnit.MONTHS);
    long hours = yestory.until(now, ChronoUnit.HOURS);
    long minutes = yestory.until(now, ChronoUnit.MINUTES);
    System.out.println("相差月份" + month);
    System.out.println("相差天数" + day);
    System.out.println("相差小时" + hours);
    System.out.println("相差分钟" + minutes);

    // 距离JDK 14 发布还有多少天？
    LocalDate jdk14 = LocalDate.of(2020, 3, 17);
    LocalDate nowDate = LocalDate.now();
    System.out.println("距离JDK 14 发布还有：" + nowDate.until(jdk14, ChronoUnit.DAYS) + "天");
}

```



#### Extend Time

```java
private static void extendTime() {
    LocalDateTime now = LocalDateTime.now();
    System.out.println("当前时间：" + now);

    // 第一天
    LocalDateTime firstDay = now.withDayOfMonth(1);
    System.out.println("本月第一天：" + firstDay);

    // 当天最后一秒
    LocalDateTime lastSecondOfDay = now.withHour(23).withMinute(59).withSecond(59);
    System.out.println("当天最后一秒：" + lastSecondOfDay);

    // 最后一天
    LocalDateTime lastDay = now.with(TemporalAdjusters.lastDayOfMonth());
    System.out.println("本月最后一天:" + lastDay);

    // 是否闰年
    System.out.println("今年是否闰年：" + Year.isLeap(now.getYear()));

}

```



## Java 16 Features

### Record

以前我们定义类都是用`class`关键词，但从Java 16开始，我们将多一个关键词`record`，它也可以用来定义类。`record`关键词的引入，主要是为了提供一种更为简洁、紧凑的`final`类的定义方式。

Java `Record` 是一种不变类，类似于 String，Integer。让我们看一个简单的例子。

```java
public record Data( int x, int y)
```

我们使用 record 关键字来创建记录类，它的等价类写法如下：

```java
public final class Data {

    final private int x;
    final private int y;
    public Data( int x, int y){
        this.x = x;
        this.y = y;
    }

    public boolean equals(Object o) {
        ...
    }

    public int hashCode() {
       ...
    }

    public String toString() {
        ...
    }
}

```

当我们声明一个没有任何构造函数的普通类时，编译器会提供一个没有参数的缺省构造函数。对于记类录，也会隐式创建构造函数。

```java
public record Data(int x, int y) {

    public Data {
        if (x >y) {
            throw new IllegalArgumentException();
        }
        x+=100;
        y+=100;
    }
}

```



记录类不能继承，也不支持继承，唯一的父类是java.lang.Record

记录类可以实现接口，无论它是单个接口还是多个接口。

```java
public record Data( int x, int y) implements Runnable, Serializable
```

可以在记录类里定义方法，包括`accessor、equals、hashcode` 方法。然而，您需要确保不会打破记录类的不可变性。

当我们只想在函数中临时保存不可变数据时，记录类会非常好用。

```java
public List<Person> sortPeopleByAge(List<Person> people) {
    
    record Data(Person person, int age){};

    return people.stream()
            .map(person -> new Data(person, computeAge(person)))
            .sorted((d1, d2) -> Double.compare(d2.age(), d1.age()))
            .map(Data::person)
            .collect(toList());
}

```







