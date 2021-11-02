## MVC







## Maven



## 反射





## AOP





## IoC

控制反转(Inversion of Control, IoC)是OO编程用于削弱计算机程序耦合性的一个重要手段，也是Spring框架的核心。

[Java IoC_远有青山-CSDN博客](https://blog.csdn.net/holandstone/article/details/23654853)

在传统编程中，我们使用new关键字来实现2个组件之间的互相关联，但是这种方式会造成组件之间的耦合，IoC结局的就是耦合的问题，IoC将组件之间的关系提到外部容器来解决。IoC的核心目标是通过简单的机制解决组件依赖的问题（常常是引用一个合作对象），并且在依赖的生命周期内对他进行管理。

IoC的优势：

- 每一个模块只专注于它自己的任务
- 每一个模块的实现都假定与其他的模块无关，屏蔽了其他的模块的实现过程
- 更换某个模块并不会影响到当前的模块

IoC一般分为两种方式，依赖注入(Dependency Injection)和依赖查找(Dependency Lookup)

**Dependency Lookup：容器中的对象通过容器的API来查找自己所需要的资源和协作对象。**

**Dependency Injection：容器全权负责组件的装配，负责将对象传递给需要的对象。**

实际上，绝大多数都在使用依赖注入，因为耦合性更低，而且容器不会用到某个容器的特定API，因此可以脱离容器使用。

有六种常用的方式实现IoC：

[Spring学习笔记1—依赖注入（构造器注入、set注入和注解注入） - 大象踢足球 - 博客园 (cnblogs.com)](https://www.cnblogs.com/Jason-Xiang/p/5345342.html)

1. DI的工厂模式factory pattern，通过工厂传入参数，返回具体的实例。
2. DI的构造注入constructor injection，通过构造函数建立依赖关系，容器可以通过调用类的构造函数来注入其中。
3. DI的设值注入setter injection，使用最为广泛，通过属性来表达自己所依赖的对象和所需要的数值。
4. DI的服务器定位模式service locator pattern，将所有的服务访问都包装到对象中，对这些访问进行封装隔离，用一个类来控制所有的访问。
5. DI的接口注入interface injection，为了将调用者和实现者在编译期分离，我们动态加载实现类，并通过接口强制转型后使用。
6. DL的Dependency pull(DP)和Contextualized dependency lookup(CDL)



### 





## 依赖注入-构造器注入

Spring通过DI(依赖注入)实现IOC(控制反转)，常用的注入方式有三种：构造方法注入、setter注入、基于注解的注入。

构造器注入保证一些必要的属性在Bean实例化的时候就得到设置，并且确保Bean实例化之后就可以使用。





### 实例化

Demo demo = new Demo()

通常把这条语句称之为创建一个对象，也就是Java的实例化。

其中包含了四个过程

1. 右边的new Demo()是以Demo类为模板，在堆空间中创建一个Demo类的对象，也就是Demo对象。
2. 结尾的()代表调用类的构造函数，对刚生成的构造函数进行初始化，如果没有自定义的构造函数，Java的编译器会自动加上一个默认的构造函数。
3. 左边的Demo demo创建一个Demo类引用变量，所谓类引用，就是一个指向Demo类的对象引用。
4. 中间的=将左边的这个引用指向右边的对象。





## 依赖注入-setter注入







## 依赖注入-注解注入







## 抽象和接口









## pom.xml和application.yml







## IDEA+Git





## 





## 单例模式和工厂模式





