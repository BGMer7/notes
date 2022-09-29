# Code Review

## MyBatis

### 动态SQL

#### if

使用动态 SQL 最常见情景是根据条件包含 where 子句的一部分。比如：

```xml
<select id="findActiveBlogWithTitleLike"
     resultType="Blog">
  SELECT * FROM BLOG
  WHERE state = ‘ACTIVE’
  <if test="title != null">
    AND title like #{title}
  </if>
</select>
```

这条语句提供了可选的查找文本功能。如果不传入 “title”，那么所有处于 “ACTIVE” 状态的 BLOG 都会返回；如果传入了 “title” 参数，那么就会对 “title” 一列进行模糊查找并返回对应的 BLOG 结果（细心的读者可能会发现，“title” 的参数值需要包含查找掩码或通配符字符）。

如果希望通过 “title” 和 “author” 两个参数进行可选搜索该怎么办呢？首先，我想先将语句名称修改成更名副其实的名称；接下来，只需要加入另一个条件即可。

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <if test="title != null">
    AND title like #{title}
  </if>
  <if test="author != null and author.name != null">
    AND author_name like #{author.name}
  </if>
</select>
```



```xml
<sql id="Base_Column_List">
    id, location, article_id, begin_time, end_time, icon, is_del, creater, create_time, updater, update_time, is_default_headlines_icon, title, url
</sql>
```



### SQL

```xml
begin_time <![CDATA[ >= ]]> #{currentTime,jdbcType=VARCHAR}
```



### Example

```java
Example example = new Example(HeadlineArticle.class);

```



Mapper.updateByExample





### Pagehelper

仅讨论后端分页的解决方案，场景主要分为两种，一种是传统的PC的电梯分页，一种是针对移动端的流式分页。

分页方法主要有以下几种：

1. 直接使用MySQL的limit进行分页

   前端会传来page、pageSize表示第page页显示第pageSize条数据，所以本身就需要满足SQL的语法。

   >```sql
   >select _column, _column from _table [limit N][offset M]
   >```
   >
   >- **select ** : 返回记录
   >- **limit N** : 返回 N 条记录
   >- **offset M** : 跳过 M 条记录, 默认 M=0, 单独使用似乎不起作用
   >- **limit N,M** : 相当于 **limit M offset N** , 从第 N 条记录开始, 返回 M 条记录

   那么经过计算，实际上往SQL中传入的page参数应该是`page = (page-1)*pageSize`，

   然后在我们查询sql的最后加上limit语句即可

   ```sql
   select ......     limit #{page},#{pageSize}
   ```

2. 使用MyBatis的pagehelper来分页

   如果是使用的springboot的话，可以引入这个依赖代替上面的。该依赖会自动加入mybatis的依赖

   ```xml
   <dependency>
          <groupId>com.github.pagehelper</groupId>
           <artifactId>pagehelper-spring-boot-starter</artifactId>
            <version>1.2.10</version>      
   </dependency>
   ```

   pagehelper的原理是，等我们在dao层查出list之后，过滤之后再分页。

   第一步：设置分页

   ```java 
   //注意这里的page不需要进行计算，直接使用前端传过来的数据
   PageHelper.startPage(page,pageSize);
   ```
   
   第二步：从`dao`层获取`list`列表数据并自行过滤
   
   ```java
   List<User> list=userMapper.getUserByAge(int age);
   ```
   
   第三步：分页封装
   
   ```java
   PageInfo<User> pageInfo=new PageInfo<>(list);
   ```
   
   之后，`pageInfo`里面的属性就可以供我们使用了。具体可以使用的数据如下：
   
   ```java
   	//当前页
       private int pageNum;
       //每页的数量
       private int pageSize;
       //当前页的数量
       private int size;
       //当前页面第一个元素在数据库中的行号
       private int startRow;
       //当前页面最后一个元素在数据库中的行号
       private int endRow;
       //总记录数
       private long total;
       //总页数
       private int pages;
       //结果集
       private List<T> list;
       //前一页
       private int prePage;
       //下一页
       private int nextPage;
       //是否为第一页
       private boolean isFirstPage = false;
       //是否为最后一页
       private boolean isLastPage = false;
       //是否有前一页
       private boolean hasPreviousPage = false;
       //是否有下一页
       private boolean hasNextPage = false;
       //导航页码数
       private int navigatePages;
       //所有导航页号
       private int[] navigatepageNums;
       //导航条上的第一页
       private int navigateFirstPage;
       //导航条上的最后一页
       private int navigateLastPage;
   ```
   
   
   
3. 在实际生产中，会将pagehelper再做一层封装，作为一个通用的utils工具

4. MyBatisPlus

   导入MP的依赖

   ```xml
   <dependency>
          <groupId>com.baomidou</groupId>
          <artifactId>mybatis-plus-generator</artifactId>
          <version>3.4.1</version>
   </dependency>
   ```

   在config包下添加一个MP的拦截器

   ```java
   @Configuration
   //@MapperScan("com.example.demo.mapper")
   public class MybatisPlusConfig {
   
       /**
        * 新增分页拦截器，并设置数据库类型为mysql
        * @return
        */
       @Bean
       public MybatisPlusInterceptor mybatisPlusInterceptor() {
           MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
           interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.MYSQL));
           return interceptor;
       }
   
   }
   ```

   service层

   ```java
   //分页参数
   Page<SchoolStudent> rowPage = new Page(page, pageSize);
   
   //queryWrapper组装查询where条件
   LambdaQueryWrapper<SchoolStudent> queryWrapper = new LambdaQueryWrapper<>();
   rowPage = this.baseMapper.selectPage(rowPage, queryWrapper);
   return rowPage;
   ```

[mybatis-plus分页查询三种方法_李长渊哦的博客-CSDN博客](https://blog.csdn.net/weixin_46146718/article/details/126962110#:~:text=1、mybatis-plus中分页接口需要包含一个IPage类型的参数。,2、多个实体参数，需要添加%40Param参数注解，方便在xml中配置sql时获取参数值。)









### Mapper

#### BaseMapper





#### ExampleMapper





#### RowBoundsMapper





#### Marker





## Java Basics

### Serializable

主要问题在于，为什么要实现Serializable接口？

从原理上来说，一个Java的类只有实现了Serializable接口，它的对象才是可序列化的。

如果要序列化某些类的对象，这些类就必须实现Serializable接口。Serializable是一个空接口，没有什么具体内容，它的目的只是简单的标识一个类的对象可以被序列化。

**为什么要进实现Serializable接口：**为了保存在内存中的各种对象的状态（也就是实例变量，不是方法），并且可以把保存的对象状态再读出来，这是java中的提供的保存对象状态的机制—序列化。

**在什么情况下需要使用到Serializable接口呢？**

1. 当想把的内存中的对象写入到硬盘的时候；
2. 当想用套接字在网络上传送对象的时候（另一个说法就是当我们需要把对象的状态信息通过网络进行传输，或者需要将对象的状态信息持久化，以便将来使用时都需要把对象进行序列化。）；
3. 当想通过RMI传输对象的时候；

这三个场景的具体解释是：

1. 比如内存不够用了，那计算机就要将内存里面的一部分对象暂时的保存到硬盘中，等到要用的时候再读入到内存中，硬盘的那部分存储空间就是所谓的虚拟内存。在比如过你要将某个特定的对象保存到文件中，我隔几天在把它拿出来用，那么这时候就要实现Serializable接口；
2. 在进行java的Socket编程的时候，你有时候可能要传输某一类的对象，那么也就要实现Serializable接口；最常见的你传输一个字符串，它是JDK里面的类，也实现了Serializable接口，所以可以在网络上传输。
3. 如果要通过远程的方法调用（RMI）去调用一个远程对象的方法，如在计算机A中调用另一台计算机B的对象的方法，那么你需要通过JNDI服务获取计算机B目标对象的引用，将对象从B传送到A，就需要实现序列化接口。



比较常见的需要序列化的场景就是实体类：

1. 首先，直观的看：

   实体类就是一个拥有Set和Get方法的类。实体类通常总是和数据库之类的（所谓持久层数据）联系在一起。这种联系是借由框架（如Hibernate）来建立的。

2. 其次说定义（比较生涩难懂）：

   实体类主要是作为数据管理和业务逻辑处理层面上存在的类别； **它们主要在分析阶段区分 实体类的主要职责是存储和管理系统内部的信息**，它也可以有行为，甚至很复杂的行为，但这些行为必须与它所代表的实体对象密切相关。

3. 实体类的作用

   实体类就是一个载体。

   现在的设计差不多都是一张表就等于业务里面的一个类。一条记录（一般一行数据）是一个对象，一行中的一列就是这个对象的一个属性。

   所以我们在操作某个表时（比如更改这个表的信息），我们就可以在前台定义一个这样的对象，然后将其对应的属性赋值，然后传到后台。
   
   这样后台就可以拿到这个对象的所有值了——不用一个一个属性当参数传过来，只要传一个这个类的对象就好了，也就是说只要一个参数就好了。好处不言而喻。
   而这种前台对象到后台数据库的联系，我们是借由框架、配置文件来配置实现的，很方便快捷。并不需要自己手动编程实现。

> 实体类的经验与规范：
>
> 1. 实体类的名字应该尽量与数据库的表的名字相对应
> 2. 实体类应该实现Serializable接口
> 3. 实体类应该有个无参构造函数
> 4. 实体类应该有个有参构造函数（所有参数的构造函数）
> 5. 实体类的属性和方法，属性对应数据库中的字段，方法主要是setter和getter
> 6. 实体类的属性是private类型，方法是public类型，对于数据库自动生成的ID字段对应的
>    属性的setter方法为private。
> 7. 实体类还应该有个属性serialVersionUID。
>         例如：private static final long serialVersionUID = -6125297654796395674L;



> serialVersionUID
>
> 序列化ID的作用：  
>
> 其实，这个序列化ID起着关键的作用，它决定着是否能够成功反序列化！简单来说，java的序列化机制是通过在运行时判断类的serialVersionUID来验证版本一致性的。在进行反序列化时，JVM会把传来的字节流中的serialVersionUID与本地实体类中的serialVersionUID进行比较，如果相同则认为是一致的，便可以进行反序列化，否则就会报序列化版本不一致的异常。等会我们可以通过代码验证一下。
>
> 序列化ID如何产生：当我们一个实体类中没有显示的定义一个名为“serialVersionUID”、类型为long的变量时，Java序列化机制会根据编译时的class自动生成一个serialVersionUID作为序列化版本比较，这种情况下，只有同一次编译生成的class才会生成相同的serialVersionUID。
>
> 譬如，当我们编写一个类时，随着时间的推移，我们因为需求改动，需要在本地类中添加其他的字段，这个时候再反序列化时便会出现serialVersionUID不一致，导致反序列化失败。那么如何解决呢？便是在本地类中添加一个“serialVersionUID”变量，值保持不变，便可以进行序列化和反序列化。


> 序列化的实现：
>
> 为了防止反序列失败，该对象需提供一个默认的serialVersionUID（该值在反序列化的时候会进行校验校验失败并抛出InvalidClassException异常）。

但是通过源码查看Serializable 接口是一个空的接口，那么是如何起作用的？

它其实起到一个标识/标志的作用，Java虚拟机看到这个类实现了这个接口，

可能会对这个类进行特殊待遇，Java虚拟机看到这个标志，会为该类自动生成一个序列化版本号。






### Iterable





#### interface extends



### getInstance()

一般常在单例模式中使用getInstance创建对象，但并非私有构造方法+对外通过getInstance提供实例的情况就一定是单例模式。

> 单例模式：
>
> 一个私有的构造方法
>
> 一个私有的该类型的变量
>
> 必须有一个public的方法，且返回类型为该变量，用于对外提供这个私有变量的接口。

单例模式实例：

- Declaring all constructors of the class to be private.
- Providing a static method that returns a reference to the instance. The lazy  initialization concept is used to write the static methods.
- The instance is stored as a private static variable.

```java 
// Class 1
// Helper class
class Singleton {
    // Static variable reference of single_instance
    // of type Singleton
    private static Singleton single_instance = null;
  
    // Declaring a variable of type String
    public String s;
  
    // Constructor
    // Here we will be creating private constructor
    // restricted to this class itself
    private Singleton()
    {
        s = "Hello I am a string part of Singleton class";
    }
  
    // Static method
    // Static method to create instance of Singleton class
    public static Singleton getInstance()
    {
        if (single_instance == null)
            single_instance = new Singleton();
  
        return single_instance;
    }
}
  
// Class 2
// Main class
class GFG {
    // Main driver method
    public static void main(String args[])
    {
        // Instantiating Singleton class with variable x
        Singleton x = Singleton.getInstance();
  
        // Instantiating Singleton class with variable y
        Singleton y = Singleton.getInstance();
  
        // Instantiating Singleton class with variable z
        Singleton z = Singleton.getInstance();
  
        // Printing the hash code for above variable as
        // declared
        System.out.println("Hashcode of x is "
                           + x.hashCode());
        System.out.println("Hashcode of y is "
                           + y.hashCode());
        System.out.println("Hashcode of z is "
                           + z.hashCode());
  
        // Condition check
        if (x == y && y == z) {
  
            // Print statement
            System.out.println(
                "Three objects point to the same memory location on the heap i.e, to the same object");
        }
  
        else {
            // Print statement
            System.out.println(
                "Three objects DO NOT point to the same memory location on the heap");
        }
    }
}

// 双重检查锁double check lock，线程安全
public class Singleton{  
    private static Singleton uniqueInstance;  
    // 使用private Consructor 確保類別Singleton 的物件(Object)只能透過 API – method : getInstance()。
private Singleton()
{
	// 這裡可以有很多code，所以建立物件(Object)可能需要很多資源/時間。
}
	// 因為Constructor已經private，所以需要以下方法讓其他程式調用這個類別。
    public static Singleton getInstance() {  
		// 使用 synchronized 關鍵字避免多於一個Thread 進入
        if(instance == null){
            synchronized(Singleton.class){
                if(instance == null){
                    instance = new Singleton();
                }    
            }
        }         return uniqueInstance;  
    }  
}  
```



getInstance方法的主要作用，主要





### AOP

#### @interface

自定义注解

[ @interface 与aop_我本半山人的博客-CSDN博客](https://blog.csdn.net/qq_38895905/article/details/110448742)

自定义注解需要了解四个元注解，元注解就是在注解之上的注解，通俗来说就是注解的注解。

@Retention

定义注解的保留策略，有三个策略

```java
@Retention(RetentionPolicy.SOURCE)	//注解仅存在于源码中，在class字节码文件中不包含
@Retention(RetentionPolicy.CLASS)  	//默认的保留策略，注解会在class字节码文件中存在，但运行时无法获得，
@Retention(RetentionPolicy.RUNTIME)	//注解会在class字节码文件中存在，在运行时可以通过反射获取到
```




@Target

```java
@Target(ElementType.TYPE)                      //接口、类
@Target(ElementType.FIELD)                     //属性
@Target(ElementType.METHOD)                    //方法
@Target(ElementType.PARAMETER)                 //方法参数
@Target(ElementType.CONSTRUCTOR)               //构造函数
@Target(ElementType.LOCAL_VARIABLE)            //局部变量
@Target(ElementType.ANNOTATION_TYPE)           //注解
@Target(ElementType.PACKAGE)                   //包 
```



@Inherited

指定被修饰的Annotation将具有继承性



@Documented

指定被修饰的该Annotation可以被javadoc工具提取成文档



注解的分类

根据@Annotation是否包含成员变量，可以把Annotation分为两类。

标记注解和元注解

**标记注解：注解本身没有成员变量，这种注解仅利用自身的存在与否提供信息。**

**元数据注解：包含成员变量的注解，可以接受或者提供更多数据。**



使用@interface关键字, 其定义过程与定义接口非常类似, 需要注意的是:

Annotation的成员变量在Annotation定义中是以无参的方法形式来声明的, 其方法名和返回值类型定义了该成员变量的名字和类型，

而且我们还可以使用default关键字为这个成员变量设定默认值；



**AOP的自定义注解**

```java
@Target({ElementType.METHOD, ElementType.ANNOTATION_TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
public @interface Log() {
    String logname() default "";
}
```

Target注解，一共有以下几种：

```java
public enum ElementType {
    /** 类, 接口 (包括注释类型), 或 枚举 声明 */
    TYPE,
 
    /** 字段声明（包括枚举常量） */
    FIELD,
 
    /** 方法声明(Method declaration) */
    METHOD,
 
    /** 正式的参数声明 */
    PARAMETER,
 
    /** 构造函数声明 */
    CONSTRUCTOR,
 
    /** 局部变量声明 */
    LOCAL_VARIABLE,
 
    /** 注释类型声明 */
    ANNOTATION_TYPE,
 
    /** 包声明 */
    PACKAGE,
 
    /**
     * 类型参数声明
     *
     * @since 1.8
     */
    TYPE_PARAMETER,
 
    /**
     * 使用的类型
     *
     * @since 1.8
     */
    TYPE_USE
}

```

@Retention注解，一共有以下几种

```java
public enum RetentionPolicy {
    /**
     *被编译器忽略
     */
    SOURCE,

    /**
     * 注解将会被保留在Class文件中，但在运行时并不会被VM保留。这是默认行为，所有没有      ＊ 用Retention注解的注解，都会采用这种策略。
     */
    CLASS,

    /**
     * 保留至运行时。所以我们可以通过反射去获取注解信息
     */
    RUNTIME
}
```



编写完自定义注释之后可以开始自定义切面。

```java
/**
 * web日志切面
 *
 */
//标识这是一个切面
@Aspect
//交给spring容器管理
@Component
public class WebLogAspect {

	private final static Logger logger = LoggerFactory.getLogger(WebLogAspect.class);

	// 以自定义 @WebLog 注解为切点
	@Pointcut("@annotation(com.qqxhb.mybatis.annotation.WebLog)")
	public void webLog() {
	}

	/**
	 * 切点之前
	 * 
	 * @param joinPoint
	 * @throws Throwable
	 */
	@Before("webLog()")
	public void before(JoinPoint joinPoint) throws Throwable {
		// 得到 HttpServletRequest
		ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
		HttpServletRequest request = attributes.getRequest();
		logger.info("============ before ==========");
		// 获取WebLog注解信息
		String info = getWebLogInfo(joinPoint);
		logger.info("Point Info    : {}", info);
		// 请求地址URL
		logger.info("URL	: {}", request.getRequestURL().toString());
		// 请求方法
		logger.info("HTTP Method : {}", request.getMethod());
		// 具体切入执行方法
		logger.info("Class Method : {}.{}", joinPoint.getSignature().getDeclaringTypeName(),
				joinPoint.getSignature().getName());
		// 请求IP
		logger.info("IP	: {}", request.getRemoteAddr());// 打印描述信息
		// 请求参数
		logger.info("Input Parameter : {}", Arrays.asList(joinPoint.getArgs()));
	}

	/**
	 * 切点之后
	 * 
	 * @throws Throwable
	 */
	@After("webLog()")
	public void after() throws Throwable {
		logger.info("============ after ==========");
	}

	/**
	 * 切点返回内容后
	 * 
	 * @throws Throwable
	 */
	@AfterReturning("webLog()")
	public void afterReturning() throws Throwable {
		logger.info("============ afterReturning ==========");
	}

	/**
	 * 切点抛出异常后
	 * 
	 * @throws Throwable
	 */
	@AfterThrowing("webLog()")
	public void afterThrowing() throws Throwable {
		logger.info("============ afterThrowing ==========");
	}

	/**
	 * 环绕
	 * 
	 * @param proceedingJoinPoint
	 * @return
	 * @throws Throwable
	 */
	@Around("webLog()")
	public Object doAround(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
		logger.info("============ doAround ==========");
		long startTime = System.currentTimeMillis();
		Object result = proceedingJoinPoint.proceed();
		// 打印出参
		logger.info("Output Parameter : {}", result);
		// 执行时间
		logger.info("Execution Time : {} ms", System.currentTimeMillis() - startTime);
		return result;
	}

	/**
	 * 获取web日志注解信息
	 * 
	 * @param joinPoint
	 * @return
	 * @throws Exception
	 */
	public String getWebLogInfo(JoinPoint joinPoint) throws Exception {
		// 获取切入点的目标类
		String targetName = joinPoint.getTarget().getClass().getName();
		Class<?> targetClass = Class.forName(targetName);
		// 获取切入方法名
		String methodName = joinPoint.getSignature().getName();
		// 获取切入方法参数
		Object[] arguments = joinPoint.getArgs();
		// 获取目标类的所有方法
		Method[] methods = targetClass.getMethods();
		for (Method method : methods) {
			// 方法名相同、包含目标注解、方法参数个数相同（避免有重载）
			if (method.getName().equals(methodName) && method.isAnnotationPresent(WebLog.class)
					&& method.getParameterTypes().length == arguments.length) {
				return method.getAnnotation(WebLog.class).value();
			}
		}
		return "";
	}
}
```

补充：

1. 可以指定特定的有效环境

   可以在切面类上加入@Profile注解运行环境

   spring在确定那个profile处于激活状态的时，需要依赖两个独立的属性：spring.profiles.active和spring.profile.default。如果设置了spring.profiles.active属性，那么它的值就会用来确定那个profile是激活的。如果没有设置spring.profiles.active属性的话，那spring将会查找spring.profiles.default的值。

   ```java
   // 指定在开发环境还是测试环境
   @Profile({"dev", "test"})
   
   // 此时需要设置spring.profiles.active的值为dev或者test切面才生效
   ```

2. 切点关键字

   有以下几种切点关键字，execution、within、this、target、args、@target、@args、@within、@annotation。

   1. execution是一种使用频率很高的主要切点指示符，用来匹配方法签名，方法签名使用全限定名，包括访问修饰符(public/private/protected)，返回类型，包名，类名，方法名，参数，其中返回类型，包名，类名，方法，参数是必须的，如下面代码片段所示：

      ```JAVA
      @Pointcut("execution(public String org.baeldung.dao.FooDao.findById(Long))")
      ```

      这样写的代码的表达式精确地匹配到FooDao类里的findById(Long)方法，但是这看起来不是很灵活。假设我们要匹配FooDao类的所有方法，这些方法可能会有不同的方法名，不同的返回值，不同的参数列表，为了达到这种效果，我们可以使用通配符。如下代码片段所示：

      ```java
      @Pointcut("execution(* org.baeldung.dao.FooDao.*(..))")
      ```

      第一个通配符匹配所有返回值类型，第二个匹配这个类里的所有方法，（）括号表示参数列表，括号里的用两个点号表示匹配任意个参数，包括0个
      within
      使用within切点批示符可以达到上面例子一样的效果，within用来限定连接点属于某个确定类型的类，这个类里的所有方法都会被匹配。如下面代码的效果与上面的例子是一样的：

      ```java
      @Pointcut("within(org.baeldung.dao.FooDao)")
      ```

      我们也可以使用within指示符来匹配某个包下面所有类的方法（包括子包下面的所有类方法），如下代码所示：

      ```java
      @Pointcut("within(org.baeldung..*)")
      ```

   2. this和target

      this用来匹配的连接点所属的对象引用是某个特定类型的实例，target用来匹配的连接点所属目标对象必须是指定类型的实例；那么这两个有什么区别呢？原来AspectJ在实现代理时有两种方式：

      1. 如果当前对象引用的类型没有实现自接口时，spring aop使用生成一个基于CGLIB的代理类实现切面编程

      2. 如果当前对象引用实现了某个接口时，Spring aop使用JDK的动态代理机制来实现切面编程

         **this指示符就是用来匹配基于CGLIB的代理类，通俗的来讲就是，如果当前要代理的类对象没有实现某个接口的话，则使用this；target指示符用于基于JDK动态代理的代理类，通俗的来讲就是如果当前要代理的目标对象有实现了某个接口的话，则使用target**

      ```java
      public class FooDao implements BarDao {
          ...
      }
      ```

      在以上代码中，由于FooDao已经实现了BarDao这个接口，spring aop将使用jdk的动态代理来实现切面编程，在编写匹配这类型的目标对象的连接点表达式时要使用target指示符， 如下所示：

      ```java
      @Pointcut("target(org.baeldung.dao.BarDao)")
      ```

      如果FooDao类没有实现任何接口，或者在spring aop配置属性：proxyTargetClass设为true时，Spring Aop会使用基于CGLIB的动态字节码技为目标对象生成一个子类将为代理类，这时应该使用this指示器：

      ```java
   @Pointcut("this(org.baeldung.dao.FooDao)")
      ```

   3. @Target

      这个指示器匹配指定连接点，这个连接点所属的目标对象的类有一个指定的注解:

      ```java
      @Pointcut("@target(org.springframework.stereotype.Repository)")
      ```
   
   4. @args
      这个指示符是用来匹配连接点的参数的，@args指出连接点在运行时传过来的参数的类必须要有指定的注解，假设我们希望切入所有在运行时接受是@Entity注解的bean对象的方法：

      ```java
      @Pointcut("@args(org.baeldung.aop.annotations.Entity)")
      ```

   5. @within
      这个指示器，指定匹配必须包括某个注解的的类里的所有连接点：
   
      ```java
      @Pointcut("@within(org.springframework.stereotype.Repository)")
      ```
   

      上面的切点跟以下这个切点是等效的：

      ```java
      @Pointcut("within(@org.springframework.stereotype.Repository *)")
      ```
   
   6. @annotation
   
      这个指示器匹配那些有指定注解的连接点，比如，我们可以新建一个这样的注解@Loggable:
   
      ```java
      @Pointcut("@annotation(org.baeldung.aop.annotations.Loggable)")
      ```
   
      **切点表达式 可以使用&&、||、!、三种运算符来组合切点表达式，表示与或非的关系。**
   
      ```java
      @Pointcut("@target(org.springframework.stereotype.Repository)")
      public void repositoryMethods() {}
      
      @Pointcut("execution(* *..create*(Long,..))")
      public void firstLongParamMethods() {}
      
      @Pointcut("repositoryMethods() && firstLongParamMethods()")
      public void entityCreationMethods() {}
      ```
   
3. 使用注解切面
   
   在需要使用该切面的方法上添加自定义的注解即可。
   
   ```java
   @GetMapping
   @WebLog("查询列表接口")
   public List<News> getNews(String title, @RequestParam(defaultValue = "1") int pageIndex, @RequestParam(defaultValue = "5") int pageSize) {
       return newsServiceImpl.selectNews(title, pageIndex, pageSize);
   }   
   ```
   





#### JoinPoint & ProceedingJoinPoint

 我们平时在使用springAop的时候，经常是将某一个方法定义为一个切入点，用来做前置，后置或环绕增强，**但如果想得到切入点方法的参数**，以及它的返回值就需要做一些特定的配置。

```java
@Aspect
@Component
public class UserInfoAspect {
    @Pointcut("execution(* com.*.test(*))")
    public void test() {}
    
    //使用JoinPoint 对象可以接收到切入点方法的参数
    @AfterReturning(value = "test()")
    public void logMethodCall(JoinPoint jp) throws Throwable {

        System.out.println("进入后置增强了！");
        String name = jp.getSignature().getName();
        System.out.println(name);

        Object[] args = jp.getArgs();
        for (Object arg : args) {
            System.out.println("参数：" + arg);
        }
    }
 }
```



#### return value

returnValue即为该方法返回值。

```java
@AfterReturning(value = "buyPoint()", returning = "returnValue")
public void AfterBuying(JoinPoint jp, Object returnValue) throws Throwable {

    System.out.println("支付已完成。");
    String name = jp.getSignature().getName();
    System.out.println("此切面的方法名为：" + name);

    Object[] args = jp.getArgs();
    for (Object arg : args) {
        System.out.println("参数值：" + arg);
    }

    System.out.println("方法返回值的类型为：" + returnValue.getClass().getName());
}


支付已完成。
此切面的方法名为：buy
方法返回值的类型为：java.lang.String
    
此切面的方法名为：addToCart
参数：包
方法返回值的类型为：java.lang.String
```









## Code Rules

### RESTful APIs







## Design patterns

### Facade pattern



## Spring Boot

#### maven

groupId，artfactId，version，type，classifier，scope，systemPath，exclusions，optional 是 maven的9种依赖属性，

其中groupId，artfactId，version是三个基本的依赖坐标，不可缺少。

groupId------包名（一般为域名的反写）。

artfactId------项目名。

version-------版本。









### DTO

DTO（Data Transfer Object）：数据传输对象，这个概念来源于J2EE的设计模式，原来的目的是为了EJB的分布式应用提供粗粒度的数据实体，以减少分布式调用的次数，从而提高分布式调用的性能和降低网络负载，但在这里，我泛指用于**展示层与服务层之间的数据传输对象**。

DO（Domain Object）：领域对象，就是从现实世界中抽象出来的有形或无形的**业务实体**。

PO（Persistent Object）：**持久化对象，它跟持久层（通常是关系型数据库）的数据结构形成一一对应的映射关系，如果持久层是关系型数据库，那么，数据表中的每个字段（或若干个）就对应PO的一个（或若干个）属性。**

用户发出请求（可能是填写表单），表单的数据在展示层被匹配为VO。
展示层把VO转换为服务层对应方法所要求的DTO，传送给服务层。
服务层首先根据DTO的数据构造（或重建）一个DO，调用DO的业务方法完成具体业务。
服务层把DO转换为持久层对应的PO（可以使用ORM工具，也可以不用），调用持久层的持久化方法，把PO传递给它，完成持久化操作。
对于一个逆向操作，如读取数据，也是用类似的方式转换和传递。















## Docker

### Docker Compose









## Git

### Merge

Merge命令会将对独立分支上的代码库所做的任何更改作为一个新的commit合并到当前分支中。

```shell
git merge BRANCH-NAME
```

> 如果在当前分支中存在uncommited的内容，git是不允许合并分支的。对于这种情况可以
>
> 1. 创建一个新的分支，这个新的分支用来提交
>
>    ```shell
>    git checkout -b new-branch-name
>    git add .
>    git commit -m "<your commit message>"
>    ```
>
> 2. 将这些修改保存为一个stash作为临时存储
>
>    ```shell
>    git stash				# add the changes to stash
>    git merge new-features	# do the merge
>    git stash pop			# get the changes back into your working tree
>    ```
>
> 3. 如果修改都是不需要的临时修改，可以全部删除
>
>    ```shell
>    git reset --hard		# remove all the pending changes
>    ```
>
>    



### Rebase

在 Git 中rebase是一种将整个分支移动到working tree中的另一个分支节点的方法。最简单的例子是在树中将分支进一步向上移动。假设我们有一个分支在点 A 处偏离主分支：

```text
        /o-----o---o--o-----o--------- branch
--o-o--A--o---o---o---o----o--o-o-o--- master
```

当我们rebase这个分支的时候，分支情况会变成这样：

```text
                                   /o-----o---o--o-----o------ branch
--o-o--A--o---o---o---o----o--o-o-o master
```

在rebase之前，需要确保已经包所有想要的更改都已经commit，check out到想要rebase的分支，然后输入想要重新base的分支名：

```shell
git rebase master
```



### Merge Or Rebase?

- merge 是一个合并操作，会将两个分支的修改合并在一起，默认操作的情况下会提交合并中修改的内容
- merge 的提交历史忠实地记录了实际发生过什么，关注点在真实的提交历史上面，每一次commit或者merge都会留下记录，但是会产生大量交织
- rebase 并没有进行合并操作，只是提取了当前分支的修改，将其复制在了目标分支的最新提交后面
- rebase 的提交历史反映了项目过程中发生了什么，关注点在开发过程上面，只是将实际的改动内容贴到了分支的后面
- merge 与 rebase 都是非常强大的分支整合命令，没有优劣之分，使用哪一个应由项目和团队的开发需求决定
- merge 和 rebase 还有很多强大的选项，可以使用 `git help <command>` 查看



## Redis







## Kafka









## Nacos







## Dubbo











## Zookeeper







## Jenkins











