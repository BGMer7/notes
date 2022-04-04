# Design Patterns

## Singleton Pattern

单例模式是一种简单的对象创建模式，**单例模式保证一个类只有一个实例，并且提供一个访问它的全局访问点。**

要做到单例模式，最基础的需要做到几点：

- 将构造方法私有化，杜绝使用构造器Constructor创建实例
- 创建一个实例，提供一个全局访问点



### 懒汉式

懒汉式直接使用`synchronized`关键字进行加锁，保证了线程安全。

只有当第一次调用的时候才进行初始化，所以节省了内存。

但是直接使用`synchronized`关键字会直接对获取实例的方法加锁，导致性能大大下降。

```java
package com.design.pattern;

/**
 * @ClassName: SingletonLazy
 * @Description: 单例模式懒汉式
 * @author: Gatsby
 * @date: 2022/3/20 14:31
 */

public class SingletonLazy {
    /**
     * @MethodName:
     * @Parameter:
     * @Return
     * @Description: 私有实例
     * @author: Gatsby
     * @date: 2022/3/20 14:33
     */
    private static SingletonLazy singletonLazy;

    /**
     * @MethodName: SingletonLazy
     * @Parameter: []
     * @Return
     * @Description: 私有构造方法
     * @author: Gatsby
     * @date: 2022/3/20 14:34
     */
    private SingletonLazy() {
    }

    /**
     * @MethodName:  getInstance
     * @Parameter: []
     * @Return com.design.pattern.SingletonLazy
     * @Description: 提供一个全局接口，返回实例以供使用
     * @author: Gatsby
     * @date:  2022/3/20 14:36
     */
    public static synchronized SingletonLazy getInstance() {
        if (singletonLazy == null) {
            singletonLazy = new SingletonLazy();
        }
        return singletonLazy;
    }
}
```



### 饿汉式

饿汉式是利用类加载机制来避免多线程的同步问题，所以是线程安全的。

由于没有使用加锁，所以性能更好。

但是由于是在类加载的时候就进行实例化，会造成内存浪费。

如果对内存性能的要求不高，可以考虑饿汉式单例模式。

```java
package com.design.pattern;

/**
 * @ClassName: SingletonEager
 * @Description: 单例模式饿汉式
 * @author: Gatsby
 * @date: 2022/3/20 14:50
 */

public class SingletonEager {
    /**
     * @MethodName:
     * @Parameter:
     * @Return
     * @Description: 私有实例，静态变量会在类加载的时候初始化，是线程安全的
     * @author: Gatsby
     * @date: 2022/3/20 14:53
     */
    private static final SingletonEager singletonEager = new SingletonEager();

    /**
     * @MethodName: SingletonEager
     * @Parameter: []
     * @Return
     * @Description: 私有构造方法
     * @author: Gatsby
     * @date: 2022/3/20 14:58
     */
    private SingletonEager() {
    }


    /**
     * @MethodName:  getInstance
     * @Parameter: []
     * @Return com.design.pattern.SingletonEager
     * @Description: 返回一个全局调用的接口
     * @author: Gatsby
     * @date:  2022/3/20 14:59
     */
    public static SingletonEager getInstance() {
        return singletonEager;
    }
}
```



### 双重校验锁DCL

利用`volatile`的线程可见性（被一个线程修改之后，其他的线程会理解可见，因为volatile修饰的变量是存放在主存中），既保证了懒加载，又保证了线程的性能。

```Java
package com.design.pattern;

/**
 * @ClassName: SingletonDcl
 * @Description: 单例模式double check lock双重校验锁
 * @author: Gatsby
 * @date: 2022/3/20 15:06
 */

public class SingletonDcl {

    /**
     * @MethodName:
     * @Parameter: 
     * @Return 
     * @Description: volatile针对线程可见性，一旦有一个线程使用了这个实例，
     * @author: Gatsby
     * @date:  2022/3/20 15:21
     */
    private static volatile SingletonDcl singletonDcl;

    private SingletonDcl() {

    }

    public static SingletonDcl getInstance() {
        // 第一次校验
        if (singletonDcl == null) {
            synchronized (SingletonDcl.class) {
                // 第二次校验
                if (singletonDcl == null) {
                    singletonDcl = new SingletonDcl();
                }
            }
        }
        return singletonDcl;
    }

}
```

这样执行的顺序就变成了

1. 检查变量是否被初始化(不去获得锁)，如果已被初始化则立即返回。
2. 获取锁。
3. 再次检查变量是否已经被初始化，如果还没被初始化就初始化一个对象。

执行双重检查是因为，如果多个线程同时了通过了第一次检查，并且其中一个线程首先通过了第二次检查并实例化了对象，那么剩余通过了第一次检查的线程就不会再去实例化对象。总体来说是由于多线程的执行无序性导致的，所以在创建之前需要再次检测一次，也是因为此，所以叫做double check lock。

[Java中的双重检查锁（double checked locking） - Decouple - 博客园 (cnblogs.com)](https://www.cnblogs.com/xz816111/p/8470048.html)

DCL的要素

1. volatile
2. double check



### 静态内部类

该模式利用了静态内部类延迟初始化的特性，来达到与双重校验锁方式一样的功能。由于需要借助辅助类，并不常用。

```java 
package com.design.pattern;

/**
 * @ClassName: SingletonInnerClass
 * @Description: 单例模式静态内部类
 * @author: Gatsby
 * @date: 2022/3/20 17:39
 */

public class SingletonInnerClass {
    /**
     * @MethodName:
     * @Parameter:
     * @Return
     * @Description: 私有一个内部静态类
     * @author: Gatsby
     * @date: 2022/3/20 17:41
     */
    private static class LazyHolder {
        private static final SingletonInnerClass INSTANCE = new SingletonInnerClass();
    }

    /**
     * @MethodName: SingletonInnerClass
     * @Parameter: []
     * @Return
     * @Description: 私有构造方法
     * @author: Gatsby
     * @date: 2022/3/20 17:43
     */
    private SingletonInnerClass() {
    }

    /**
     * @MethodName: getInstance
     * @Parameter: []
     * @Return com.design.pattern.SingletonInnerClass
     * @Description: 获取内部静态类中的唯一实例，并返回
     * @author: Gatsby
     * @date: 2022/3/20 17:43
     */
    public static SingletonInnerClass getInstance() {
        return LazyHolder.INSTANCE;
    }
}
```



### 枚举类Enum

该方式利用了枚举类的特性，不仅能避免线程同步问题，还防止反序列化重新创建新的对象。这种方式是 Effective Java 作者 Josh Bloch 提倡的方式。

但由于这种编码方式还不能适应，所以实际工作中很少使用。

```java
package com.design.pattern;

public enum SingletonEnum {
    /*
     * @param null:
     * @return null
     * @description: 单例实例
     * @date 2022/3/20 2022/3/20
     */
    INSTANCE;

    public void method() {
        System.out.println("枚举类中定义方法");
    }
}
```

测试一下是不是单例

```java
package com.design.pattern;

/**
 * @ClassName: SingletonEnumTest
 * @Description: 单例模式枚举类测试类
 * @author: Gatsby
 * @date: 2022/3/20 17:56
 */

public class SingletonEnumTest {
    public static void main(String[] args) {
        SingletonEnum instance1 = SingletonEnum.INSTANCE;
        SingletonEnum instance2 = SingletonEnum.INSTANCE;
        instance1.method();
        // 枚举类中定义方法
        instance2.method();
        // 枚举类中定义方法

        System.out.println(instance1 == instance2);
        // true
        System.out.println(instance1.hashCode());
        // 495053715
        System.out.println(instance2.hashCode());
        // 495053715
    }
}
```























