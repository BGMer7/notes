# Spring boot

## initializer

GroupID是项目组织唯一的标识符，实际对应JAVA的包的结构，是main目录里Java的目录结构。

ArtifactID就是项目的唯一的标识符，实际对应项目的名称，就是项目根目录的名称。

maven选择：

>四种项目类型可供选择：
>
>1. Maven Project (Generate a Maven based project archive)
>2. Maven POM (Generate a Maven pom.xml)
>3. Gradle Project (Generate a Gradle based project archive)
>4. Gradle Config (Generate a Gradle build file)
>
>　　2和4只创建对应的配置文件(pom.xml / gradle.build)，
>
>　　1和3在创建配置文件之外还会创建Spring Boot项目中的一些常规文件（SpringBootApplication 类和对应的代码结构）



### Database

**在初始化阶段查询数据库，并将数据库返回的数据加载到环境变量中。**

```java 
package com.tzt;

import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.core.env.Environment;
import org.springframework.core.env.MutablePropertySources;
import org.springframework.core.env.PropertySource;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.datasource.DriverManagerDataSource;

/**
 * @classname ApplicationContextInitializer
 * @description:
 * @author: caijinyang
 * @create: 2023/2/23
 **/

public class ApplicationContextInitializer implements org.springframework.context.ApplicationContextInitializer<ConfigurableApplicationContext> {

    @Override
    public void initialize(ConfigurableApplicationContext applicationContext) {
        // Perform the database query here and store the result in a property
        // that can be used by your Spring beans later
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        Environment environment = applicationContext.getEnvironment();
        dataSource.setDriverClassName(environment.getProperty("spring.datasource.driver-class-name"));
        dataSource.setUrl(environment.getProperty("spring.datasource.url"));
        dataSource.setUsername(environment.getProperty("spring.datasource.username"));
        dataSource.setPassword(environment.getProperty("spring.datasource.password"));
        JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
        String sql = "SELECT value FROM `system_set_info` WHERE field_key = \"fileUploadMode\"";
        String fileUploadMode = jdbcTemplate.queryForObject(sql, String.class);
        System.out.println(fileUploadMode);

        // Store the result in the ApplicationContext
        MutablePropertySources propertySources = applicationContext.getEnvironment().getPropertySources();
        propertySources.addLast(new PropertySource<String>("fileUploadMode") {
            @Override
            public String getProperty(String name) {
                if ("fileUploadMode".equals(name)) {
                    return fileUploadMode;
                }
                return null;
            }
        });
    }
}


```



### 





## structure

代码层的结构：

### 启动类Application.java

项目启动时，其当前层及子目录中所有controller会被编译，可以通过直接运行Application类来启动 Spring Boot应用。

Application中的常用注解：

@SpringBootConfiguration

@Configuration

@EnableAutoConfiguration



### 前端控制层Controller

controller的功能为接受请求和控制响应，controller负责前后端的交互，接受前端请求，决定使用什么视图，然后控制需要准备什么数据显示，再调用service层，接收service返回的数据，最后将service返回来的数据或者页面返回到客户端。





### 数据服务接口层Service

存放功能的业务逻辑，本质也是关于数据库的处理，但不是和数据库直接打交道。



### 数据服务实现层Service Impl





### 数据持久层dao

Dao层主要是和数据库进行交互，使用SQL语句向数据库发送命令，完成增删改查。

1. Dao层首先创建Dao接口，接着就在配置文件中定义该接口的实现类。
2. 在模块中调用Dao接口进行数据处理，而不需要关注这个接口的具体的实现类是什么





### 实体类Domain/Entity

主要用于定义与数据库的对象相对应的属性，提供get/set方法，toString方法，定义有参和无参构造函数。





### 工具类Utils







### 配置类Config







## Bean

### Spring bean

传统的Java Bean有以下一些规范：

1. 这个类有一个public的无参构造方法
2. 这个类的所有属性都是private
3. private的属性通过setter和getter暴露给外界，并且方法的命名也必须遵守一些命名规范
4. 这个类应该是可序列化的（实现Serializable）

传统的Bean更多用于值传递参数

普通Java对象和Spring所管理的Bean实例化的过程是有些区别的，在普通Java环境下创建对象的简要步骤可以分为以下步骤：

1. Java源码被编译为class文件
2. 等到类被需要初始化时候（new或者反射等）
3. class文件被虚拟机装载到JVM，这一步骤由类加载器完成
4. 初始化对象供后续代码使用

简单来说，可以理解为用Class对象作为模板进而创建出具体的实例

<img src="https://pic1.zhimg.com/80/v2-b785bf02600169adb09738b87d092afc_720w.webp?source=1940ef5c" alt="img" style="zoom:50%;" />

**而Spring管理的Bean不同的是，除了class对象之外，还会使用BeanDefinition的实例来描述对象的信息，例如我们可以在spring所管理的Bean有一系列的描述，@Scope、@Lazy、@DependsOn等等，可以理解为：Class只描述了类的信息，而BeanDefinition描述了对象的信息。**



Spring在启动是需要扫描在xml、注解、JavaConfig中需要被Spring管理的Bean信息，随后将这些信息封装成BeanDefinition，最后把这个信息放到一个beanDefinitionMap中。这个map的key是beanName，value是BeanDefinition对象。到这里只是将所有定义的元数据加载起来，目前真实的对象还没有实例化。



接下来开始遍历beanDefinitionMap，执行BeanFactoryPostProcessor这个Bean工厂后置处理器的逻辑，比如说，我们平时定义的占位符信息，就是通过BeanFactoryPostProcessor的子类PropertyPlaceholderConfigurer进行注入进去。

<img src="https://pica.zhimg.com/v2-3247d0086217d6c2e5013f809210bd12_r.jpg?source=1940ef5c" alt="img" style="zoom:50%;" />

BeanFactoryPostProcessor后置处理结束之后，就到了实例化对象的步骤，在Spring里通过反射来实现这个步骤，一般来说使用合适的构造器来把对象实例化。**但是此时的实例化，只是创建了一个对象，还没有将对象的属性注入**。比如我的对象是UserService，而UserService对象依赖着SendService对象，这时候的SendService还是null的。

我们这边会抽取成一个工具类，去实现ApplicationContextAware接口，来获取ApplicationContext对象进而获取Spring Bean，Aware相关的接口处理完之后，就会到BeanPostProcessor后置处理器。





### Aware interface

> Spring中提供了广泛的Aware回调接口，让bean向容器表明它们需要某种基础设施依赖。

```java
public interface Aware {
    
}
```

Aware接口没有定义任何方法，所以这是一个标识接口。





### autowired











## annotation

### @SpringBootApplication

这个注解是 Spring Boot 项目的基石，创建 SpringBoot 项目之后会默认在主类加上。

可以把`@SpringBootApplication`当作是`@Configuration`、`@EnableAutoConfiguration`、`@ComponentScan`的集合。

- `@Configuration`：允许在 Spring 上下文中注册额外的 bean 或导入其他配置类。
- `@ComponentScan`： 扫描被`@Component` (`@Service`,`@Controller`)注解的 bean，注解默认会扫描该类所在的包下所有的类。
- `@EnableAutoConfiguration`：启用 SpringBoot 的自动配置机制。

### @Spring Bean相关

#### @Autowired、@Resource

自动导入对象（实例）到类中，被注入进的类同样要被Spring Boot容器管理，例如：Service类注入到Controller类。

[@Autowired的这些骚操作，你都知道吗？ - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1867131)

[使用@Autowired注入父类对象时会报错，找到多个bean对象处理 - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1770166)

@Resource是jdk提供的注解，它默认使用byName进行装配，byName无法装配则使用byType；

@Autowired是spring提供的注解，@Autowired默认使用byType进行装配，byType无法装配则使用byName，如果接口有多个实现类，需要配合@Qualifier注解使用。
————————————————
版权声明：本文为CSDN博主「_云卷云舒_」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/Alexshi5/article/details/84000678

[Spring的byType、byName的自动装配逻辑以及@Autowired和@Resource的使用示例_云卷云舒的架构师之路-CSDN博客](https://blog.csdn.net/Alexshi5/article/details/84000678)



#### @Component、@Repository、@Service、@Controller

如果使用@Autowired自动注入的话，需要在类上标注，让Spring容器帮助自动注入。

想要把类标识为容器可识别的类，可以使用以上注解实现。

- `@Component` ：通用的注解，可标注任意类为 `Spring` 组件。如果一个 Bean 不知道属于哪个层，可以使用`@Component` 注解标注。
- `@Repository` : 对应持久层即 Dao 层，主要用于数据库相关操作。
- `@Service` : 对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao 层。
- `@Controller` : 对应 Spring MVC 控制层，主要用户接受用户请求并调用 Service 层返回数据给前端页面。



#### @RestController

`@RestController`注解是`@Controller和`@`ResponseBody`的合集，表示这是个控制器 bean，并且是将函数的返回值直接填入 HTTP 响应体中，是 REST 风格的控制器。

如果没有使用@RestController，只是使用@Controller并且不加@ResponseBody的话，一般返回的是一个视图。

这种做法适用于比较传统的Spring MVC而不是前后端分离的架构设计，对于前后端不分离的情况，@Controller+@ResponseBody返回JSON或者XML形式数据。

[@RestController vs @Controller (qq.com)](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247485544&idx=1&sn=3cc95b88979e28fe3bfe539eb421c6d8&chksm=cea247a3f9d5ceb5e324ff4b8697adc3e828ecf71a3468445e70221cce768d1e722085359907&token=1725092312&lang=zh_CN#rd)

@RestController、@Controller、@ResponseBody

在处理JSON的时候需要用到spring框架特有的注解@ResponseBody或者@RestController注解，这两个注解都会处理返回的数据格式，使用了该类型注解后返回的不再是视图，不会进行转跳，而是返回JSON或xml数据格式，输出在页面上。

@RestController，一般是使用在类上的，其实就是结合了@Controller和@ResponseBody两个注解。

@ResponseBody，一般是使用在单独的方法上的，需要哪个方法返回json数据格式，就在哪个方法上使用，具有针对性。

如果哪个类下的所有方法需要返回json数据格式的，就在哪个类上使用该注解，具有统一性；需要注意的是，使用了@RestController注解之后，其本质相当于在该类的所有方法上都统一使用了@ResponseBody注解，所以该类下的所有方法都会返回json数据格式，输出在页面上，而不会再返回视图。
采用@Contoller注解的方式，可以使接口的定义更加简单，将@Controller标记在某个类上，配合@RequestMapping注解，可以在一个类中定义多个接口，这样使用起来更加灵活。







#### @Scope

@Scope声明 Spring Bean 的作用域，也就是四种常见的作用域：

- singleton : 唯一 bean 实例，Spring 中的 bean 默认都是单例的。
- prototype : 每次请求都会创建一个新的 bean 实例。
- request : 每一次 HTTP 请求都会产生一个新的 bean，该 bean 仅在当前 HTTP request 内有效。
- session : 每一次 HTTP 请求都会产生一个新的 bean，该 bean 仅在当前 HTTP session 内有效。



#### @Configuration

一般用来声明配置类，可以使用 `@Component`注解替代，不过使用`Configuration`注解声明配置类更加语义化。



[Spring Boot 2从入门到入坟 | 底层注解篇：@Configuration详解_李阿昀的博客-CSDN博客](https://blog.csdn.net/yerenyuan_pku/article/details/116201120)

user类：

```java
package com.gatsby.entity;


/**
 * @ClassName: DemoUser
 * @Description: a demo entity class called DemoUser
 * @author: Gatsby
 * @date: 2021/9/9 9:24
 */

public class DemoUser {
    private String name;
    private Integer age;

    public DemoUser() {
    }

    public DemoUser(String name, Integer age) {
        this.name = name;
        this.age = age;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getName() {
        return this.name;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public Integer getAge() {
        return this.age;
    }

    @Override
    public String toString() {
        return "DemoUser " + this.name + " is " + this.age + " years old.";
    }
}
```



pet类：

```java
package com.gatsby.entity;

/**
 * @ClassName: DemoPet
 * @Description: a demo entity called DemoPet
 * @author: Gatsby
 * @date: 2021/9/9 9:39
 */

public class DemoPet {
    private String name;

    public DemoPet() {}

    public DemoPet(String name) {
        this.name = name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getName() {
        return this.name;
    }

    @Override
    public String toString() {
        return "DemoPet " + this.name;
    }
}
```



master类：字段中有一个pet，因此产生了依赖。

```java
package com.gatsby.entity;

/**
 * @ClassName: DemoMaster
 * @Description: a demo entity who owns a pet
 * @author: Gatsby
 * @date: 2021/9/9 14:25
 */

public class DemoMaster {
    private String name;
    private Integer age;
    private DemoPet pet;

    public DemoMaster() {
    }

    public DemoMaster(String name, Integer age) {
        this.name = name;
        this.age = age;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getName() {
        return this.name;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public Integer getAge() {
        return this.age;
    }

    public void setPet(DemoPet pet) {
        this.pet = pet;
    }

    public DemoPet getPet() {
        return this.pet;
    }

    @Override
    public String toString() {
        return "DemoUser " + this.name + " is " + this.age + " years old.";
    }
}
```



main：

```java
package com.gatsby;

import com.gatsby.config.DemoConfig;
import com.gatsby.entity.DemoMaster;
import com.gatsby.entity.DemoPet;
import com.gatsby.entity.DemoUser;
import lombok.extern.slf4j.Slf4j;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.annotation.Bean;

import java.util.Arrays;

@Slf4j
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {

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
        // 注册的组件默认就是单实例的，就算获取无限次组件，获取到的也是一样的。
        log.info("UserA and UserB are the same: " + (userA == userB));

        DemoConfig myBean = run.getBean(DemoConfig.class);
        System.out.println(myBean);

        DemoMaster masterA = myBean.demoMaster_bean();
        DemoMaster masterB = myBean.demoMaster_bean();
        // 通过bean引用
        log.info("masterA and masterB are the same: " + (masterA == masterB));

        DemoMaster keeper = run.getBean("keeper", DemoMaster.class);
        DemoPet pet = run.getBean("demoPet_ioc", DemoPet.class);
        log.info("keeper's pet and pet are the same: " + (keeper.getPet() == pet));

    }
}
```



### 前后端传值

#### @PathVariable

用于获取路径参数。



#### @RequestParam

用于获取查询参数。

假设某个方法的代码如下

```java 
@GetMapping("/class/{classId}/teachers")
public list<Teacher> getClassTeachers(@PathVariable("classId") Long classId, 
    								@RequestParam(value="type", required="false") String type){
    return...
}
```

如果我们前端给后端发送的请求url是`/class/{123}/teachers?type=Maths`，那么这里的参数classId就会自动赋值123，type赋值Maths。



#### @RequestBody

用于读取 Request 请求（可能是 POST,PUT,DELETE,GET 请求）的 body 部分并且**Content-Type 为 application/json** 格式的数据，接收到数据之后会自动将数据绑定到 Java 对象上去。

系统会使用`HttpMessageConverter`或者自定义的`HttpMessageConverter`将请求的 body 中的 json 字符串转换为 java 对象。

假设有一个`UserRegisterRequest`对象：

```java 
@Data
public class UserRegisterRequest {
    private String userName;
    private String password;
    private String fullName;
}
```

此时有一个注册的接口：

```java 
@PostMapping
public ResponseEntity signUp(@RequestBody UserRegisterRequest userRegisterRequest) {
    return...
}
```

如果发送的请求中的参数是这样的：

```json
{	
    "userName":"coder",
    "fullName":"shuangkou",
    "password":"123456"
}
```

这样我们的后端就可以直接把 json 格式的数据映射到我们的 `UserRegisterRequest` 类上。

> 需要注意的是：**一个请求方法只可以有一个`@RequestBody`，但是可以有多个`@RequestParam`和`@PathVariable`**。 如果你的方法必须要用两个 `@RequestBody`来接受数据的话，大概率是你的数据库设计或者系统设计出问题了！



#### @RequestMapping

[@RequestMapping注解的用法-阿里云开发者社区 (aliyun.com)](https://developer.aliyun.com/article/651661)

```java
@RequestMapping(value="/{id}", method=RequestMethod.PUT)
```



### 读取配置信息

假设我们的数据源配置文件`application.yml`内容如下：

```yml
wuhan2020: 2020年初武汉爆发了新型冠状病毒，疫情严重，但是，我相信一切都会过去！武汉加油！中国加油！

my-profile:
  name: Guide哥
  email: koushuangbwcx@163.com

library:
  location: 湖北武汉
  books:
    - name: 天才基本法
      description: 二十二岁的林朝夕在父亲确诊阿尔茨海默病这天
    - name: 时间的秩序
      description: 为什么我们记得过去，而非未来？
    - name: 了不起的我
      description: 如何养成一个新习惯？
```

#### @Value

```java 
@Value("${wuhan2020}")
String wuhan;
```

wuhan = "2020年初武汉爆发了新型冠状病毒，疫情严重，但是，我相信一切都会过去！武汉加油！中国加油！"

#### @ConfigurationProperties

通过`@ConfigurationProperties`读取配置信息并与 bean 绑定。

``` {
@Component
@ConfigurationProperties(prefix="library")
class LibraryProperties {
	private String location;
	private List<Book> books;
}
```

像使用普通的 Spring bean 一样，将其注入到类中使用。



### 字段校验

- `@NotEmpty` 被注释的字符串的不能为 null 也不能为空
- `@NotBlank` 被注释的字符串非 null，并且必须包含一个非空白字符
- `@Null` 被注释的元素必须为 null
- `@NotNull` 被注释的元素必须不为 null
- `@AssertTrue` 被注释的元素必须为 true
- `@AssertFalse` 被注释的元素必须为 false
- `@Pattern(regex=,flag=)`被注释的元素必须符合指定的正则表达式
- `@Email` 被注释的元素必须是 Email 格式。
- `@Min(value)`被注释的元素必须是一个数字，其值必须大于等于指定的最小值
- `@Max(value)`被注释的元素必须是一个数字，其值必须小于等于指定的最大值
- `@DecimalMin(value)`被注释的元素必须是一个数字，其值必须大于等于指定的最小值
- `@DecimalMax(value)` 被注释的元素必须是一个数字，其值必须小于等于指定的最大值
- `@Size(max=, min=)`被注释的元素的大小必须在指定的范围内
- `@Digits (integer, fraction)`被注释的元素必须是一个数字，其值必须在可接受的范围内
- `@Past`被注释的元素必须是一个过去的日期
- `@Future` 被注释的元素必须是一个将来的日期











### lombok tag

[lombok标签之@Data @AllArgsConstructor @@NoArgsConstructor -如何去除get，set方法。@Data注解和如何使用，lombok - aspirant - 博客园 (cnblogs.com)](https://www.cnblogs.com/aspirant/p/10298752.html)

#### @Expose





#### @Getter/Setter

注解在类上, 为类提供读写属性



#### @ToString

注解在类上, 为类提供 toString() 方法



#### @AllArgsConstructor

会生成一个包含所有变量，同时如果变量使用了@NotNull的注解，会进行是否为空的校验

全部参数的构造函数的自动生成，该注解的作用域也只是在实体类上，参数的顺序与属性的定义顺序一致。



#### @NoArgsConstructor

自动生成无参构造函数。



#### @RequiredArgsConstructor

会生成一个包含常量（final），和标识了@NotNull的变量的构造方法。



#### @Data

注解在类上, 为类提供读写属性, 此外还提供了 equals()、hashCode()、toString() 方法

主要作用是提高代码的简洁性，去除一些机械性和重复的getter和setter方法



#### @Log4j

注解在类上，为类提供一个属性名为log的log4j日志对象



#### @Slf4j

注解在类上，为类提供一个属性名为log的slf4j日志对象



### @Conditional

@Conditional是Spring4新提供的注解，它的作用是按照一定的条件进行判断，满足条件给容器注册bean。

@Conditional的定义：

```java
//此注解可以标注在类和方法上
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME) 
@Documented
public @interface Conditional {
    Class<? extends Condition>[] value();
}
```

从代码中可以看到，需要传入一个Class数组，并且需要继承Condition接口：

```java
public interface Condition {
    boolean matches(ConditionContext var1, AnnotatedTypeMetadata var2);
}
```

Condition是个接口，需要实现matches方法，返回true则注入bean，false则不注入。







### integration

#### RestTemplate

[RestTemplate几种常用方法说明](https://blog.csdn.net/keehom/article/details/80721599)

##### Get请求

**第一种：getForEntity**

在RestTemplate中，发送一个GET请求，我们可以通过如下两种方式

getForEntity方法的返回值是一个`ResponseEntity<T>`，`ResponseEntity<T>`是Spring对HTTP请求响应的封装，包括了几个重要的元素，如响应码、contentType、contentLength、响应消息体等。

```java
@RequestMapping("/gethello")
public String getHello() {
    ResponseEntity<String> responseEntity = restTemplate.getForEntity("http://HELLO-SERVICE/hello", String.class);
    String body = responseEntity.getBody();
    HttpStatus statusCode = responseEntity.getStatusCode();
    int statusCodeValue = responseEntity.getStatusCodeValue();
    HttpHeaders headers = responseEntity.getHeaders();
    StringBuffer result = new StringBuffer();
    result.append("responseEntity.getBody()：").append(body).append("<hr>")
          .append("responseEntity.getStatusCode()：").append(statusCode).append("<hr>")
          .append("responseEntity.getStatusCodeValue()：").append(statusCodeValue).append("<hr>")
          .append("responseEntity.getHeaders()：").append(headers).append("<hr>");
    return result.toString();
}
```

- getForEntity的第一个参数为我要调用的服务的地址，这里我调用了服务提供者提供的/hello接口，注意这里是通过服务名调用而不是服务地址，如果写成服务地址就没法实现客户端负载均衡了。
- getForEntity第二个参数String.class表示我希望返回的body类型是String
- 拿到返回结果之后，将返回结果遍历打印出来



有时候我在调用服务提供者提供的接口时，可能需要传递参数，有两种不同的方式，如下

```java
@RequestMapping("/sayhello")
public String sayHello() {
    ResponseEntity<String> responseEntity = restTemplate.getForEntity("http://HELLO-SERVICE/sayhello?name={1}", String.class, "张三");
    return responseEntity.getBody();
}

@RequestMapping("/sayhello2")
public String sayHello2() {
    Map<String, String> map = new HashMap<>();
    map.put("name", "李四");
    ResponseEntity<String> responseEntity = restTemplate.getForEntity("http://HELLO-SERVICE/sayhello?name={name}", String.class, map);
    return responseEntity.getBody();
}
```

- 以用一个数字做占位符，最后是一个可变长度的参数，来一一替换前面的占位符
- 也可以前面使用name={name}这种形式，最后一个参数是一个map，map的key即为前边占位符的名字，map的value为参数值



第一个调用地址也可以是一个URI而不是字符串，这个时候我们构建一个URI即可，参数都包含在URI中了，如下

```java
@RequestMapping("/sayhello3")
public String sayHello3() {
    UriComponents uriComponents = UriComponentsBuilder.fromUriString("http://HELLO-SERVICE/sayhello?name={name}").build().expand("王五").encode();
    URI uri = uriComponents.toUri();
    ResponseEntity<String> responseEntity = restTemplate.getForEntity(uri, String.class);
    return responseEntity.getBody();
}
```



服务提供者不光可以返回一个String，也可以返回一个自定义类型的对象，比如提供如下方法：

```java
@RequestMapping(value = "/getbook", method = RequestMethod.GET)
public Book book() {
    return new Book("三国演义", 90, "罗贯中", "花城出版社");
}
```

对于这个方法，调用的时候可以这样调用

```java
@RequestMapping("/book1")
public Book book1() {
    ResponseEntity<Book> responseEntity = restTemplate.getForEntity("http://HELLO-SERVICE/getbook1", Book.class);
    return responseEntity.getBody();
}
```

返回的结果如下：

```
{
	"name": "三国演义",
	"price": 90,
	"author": "罗贯中",
	"publish": "花城出版社"
}
```



**getForObject**

getForObject函数实际上是对getForEntity函数的进一步封装，如果你只关注返回的消息体的内容，对其他信息都不关注，此时可以使用getForObject。

```java
@RequestMapping("/book2")
public Book book2() {
    Book book = restTemplate.getForObject("http://HELLO-SERVICE/getbook1", Book.class);
    return book;
}
```



##### Post请求

在RestTemplate中，POST请求可以通过如下三个方法来发起

**postForEntity**

```java 
@RequestMapping(value = "/getbook2", method = RequestMethod.POST)
public Book book2(@RequestBody Book book) {
    System.out.println(book.getName());
    book.setPrice(33);
    book.setAuthor("曹雪芹");
    book.setPublisher("人民文学出版社");
    return book;
}
```

我这里创建了一个Book对象，这个Book对象只有name属性有值，将之传递到服务提供者那里去，服务提供者代码如下

```java
@RequestMapping("/book3")
public Book book3() {
    Book book = new Book();
    book.setName("红楼梦");
    ResponseEntity<Book> responseEntity = restTemplate.postForEntity("http://HELLO-SERVICE/getbook2", book, Book.class);
    return responseEntity.getBody();
}
```

postForEntity的第一个参数表示调用的服务的地址，第二个参数表示上传的参数，第三个参数表示返回的消息体的数据类型。



**postForObject**

如果你只关注，返回的消息体，可以直接使用postForObject。用法和getForObject一致。



**postForLocation**

postForLocation也是提交新资源，提交成功之后，返回新资源的URI，postForLocation的参数和前面两种的参数基本一致，只不过该方法的返回值为Uri，这个只需要服务提供者返回一个Uri即可，该Uri表示新资源的位置。



##### Put请求

在RestTemplate中，PUT请求可以通过put方法调用，put方法的参数和前面介绍的postForEntity方法的参数基本一致，只是put方法没有返回值而已。举一个简单的例子，如下：

```java
@RequestMapping("/put")
public void put() {
    Book book = new Book();
    book.setName("红楼梦");
    restTemplate.put("http://HELLO-SERVICE/getbook3/{1}", book, 99);
}
```



##### **Delete请求**

delete请求我们可以通过delete方法调用来实现，如下例子

```java
@RequestMapping("/delete")
public void delete() {
    restTemplate.delete("http://HELLO-SERVICE/getbook4/{1}", 100);
}
```



#### 





## interceptor

interceptor和过滤器一样，都是面向切面编程----AOP的具体实现。

interceptor可以用来执行某些任务，例如在Controller处理之前编写日志，添加或者更新配置。

在Spring中，当请求发送到Controller时，在被Controller处理之前，必须经过多个Interceptor（0个或者多个）



### functions

常见的interceptor的功能：

- 日志记录：记录请求信息的日志，以便进行信息监控，信息统计，计算PV（Page View）等等。
- 权限检查：例如登录检查，进入Controller之前先检查用户是否已经登录。
- 性能监控：通过拦截器在进入Controller之前先记录时间，在处理结束之后记录结束的时间，从而得到有效的处理时间。
- 通用行为：读取Cookie得到用户的信息，并将用户对象放入请求，从而方便后续流程。



Spring MVC提供了interceptor的拦截机制，用于请求的预处理的后处理。

在开发一个网站时可能有这样的需求：某些页面只希望几个特定的用户浏览。 对于这样的访问权限控制，应该如何实现呢？拦截器就可以实现上述需求。在 Struts2 框架中，拦截器是其重要的组成部分，Spring MVC 框架也提供了拦截器功能。

Spring MVC 的拦截器（Interceptor）与 Java Servlet 的过滤器（Filter）类似，它主要用于拦截用户的请求并做相应的处理，通常应用在权限验证、记录请求信息的日志、判断用户是否登录等功能上。

### override

在 Spring MVC 框架中定义一个拦截器需要对拦截器进行定义和配置，主要有以下 2 种方式。

- 通过实现HandleInterceptor接口，或者继承HandleInterceptor的实现类，例如HandleInterceptorAdapter来定义自己的业务逻辑；
- 通过实现WebRequestInterceptor接口或者继承WebRequestInterceptor接口的实现类

一般来说通过HandleInterceptor来实现自定义的拦截器功能，需要实现HandleInterceptor的三个方法，分别是：

`preHandle()`：该方法在Controller的处理请求方法执行之前执行，其返回值表示是否中断后续操作。返回true则表示继续向下执行，返回false表示中断后续操作。

`postHandle()`：该方法表示在Controller的处理方法调用之后、解析视图之前，可以通过此方法对请求域中的模型和视图做进一步的修改。

`afterCompletion()`：该方法在Controller的处理请求方法之后执行，即视图渲染之后执行，可以通过此方法实现一些资源清理、记录日志记录的工作。

controller

```java 
@RestController
public class LoginController {
    @RequestMapping("/index")
    public String index() {
        return "index";
    }
    
    @RequestMapping("/admin/login")
    public String login(POJO pojo) {
        return "login";
    }
}
```

实现`HandlerInterceptor`接口

```java
@Component
public class LoginInterceptor implements HandlerInterceptor {
    /***
     * 在请求处理之前进行调用(Controller方法调用之前)
     */
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("执行了拦截器的preHandle方法");
        try {
            HttpSession session = request.getSession();
            //统一拦截（查询当前session是否存在user）(这里user会在每次登录成功后，写入session)
            User user = (User) session.getAttribute("user");
            if (user != null) {
                return true;
            }
            response.sendRedirect(request.getContextPath() + "login");
        } catch (Exception e) {
            e.printStackTrace();
        }
        return false;
        //如果设置为false时，被请求时，拦截器执行到此处将不会继续操作
        //如果设置为true时，请求将会继续执行后面的操作
    }
    
    /***
     * 请求处理之后进行调用，但是在视图被渲染之前（Controller方法调用之后）
     */
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("执行了拦截器的postHandle方法");
    }
    
    /***
     * 整个请求结束之后被调用，也就是在DispatchServlet渲染了对应的视图之后执行（主要用于进行资源清理工作）
     */
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("执行了拦截器的afterCompletion方法");
    }
}
```

实现`WebMvcConfigurer`接口，注册拦截器

```java
@Configuration
public class LoginConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        //注册TestInterceptor拦截器
        InterceptorRegistration registration = registry.addInterceptor(new UserLoginInterceptor());
        registration.addPathPatterns("/**"); //所有路径都被拦截
        registration.excludePathPatterns(    //添加不拦截路径
                "/login",                    //登录路径
                "/static"                    //css静态资源 
        );
    }
}

```

[SpringBoot 实现登录拦截器（实战版） - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1860615)

保持登录状态

只需一次登录，如果登录过，下一次再访问的时候就无需再次进行登录拦截，可以直接访问网站里面的内容了。

在正确登录之后，就将`user`保存到`session`中，再次访问页面的时候，登录拦截器就可以找到这个`user`对象，就不需要再次拦截到登录界面了.

```java
@RequestMapping(value = {"", "/", "/index"}, method = RequestMethod.GET)
public String index(Model model, HttpServletRequest request) {
    User user = (User) request.getSession().getAttribute("user");
    model.addAttribute("user", user);
    return "users/index";
}

@RequestMapping(value = {"/login"}, method = RequestMethod.GET)
public String loginIndex() {
    return "users/login";
}

@RequestMapping(value = {"/login"}, method = RequestMethod.POST)
public String login(@RequestParam(name = "username")String username, @RequestParam(name = "password")String password,
                    Model model, HttpServletRequest request) {
    User user = userService.getPwdByUsername(username);
    String pwd = user.getPassword();
    String password1 = MD5Utils.md5Code(password).toUpperCase();
    String password2 = MD5Utils.md5Code(password1).toUpperCase();
    if (pwd.equals(password2)) {
        model.addAttribute("user", user);
        request.getSession().setAttribute("user", user);
        return "redirect:/index";
    } else {
        return "users/failed";
    }
}

```

















## MyBatis

![image-20220430152944280](C:\Users\CaiJinyang\AppData\Roaming\Typora\typora-user-images\image-20220430152944280.png)

### MyBatis工作原理

MyBatis在操作数据库的时候，大体经历8个步骤：

1. 读取MyBatis配置文件mybatis-config.xml。mybatis-config.xml是MyBatis的全局配置文件，配置了MyBatis的运行环境等信息，其中主要的内容是获取数据库的连接。
2. 加载映射文件Mapper.xml。Mapper.xml是SQL映射文件，该文件配置了操作数据库的SQL语句，需要在MyBatis-config.xml中加载才能执行。mybatis-config.xml可以加载多个配置文件，每个配置文件对应数据库中的一张表。
3. 构建会话工厂。通过MyBatis的环境等配置信息构建会话工厂SqlSessionFactory。
4. 创建SqlSession，由会话工厂创建SqlSession对象，该对象中包含了执行SQL的所有方法。
5. MyBatis底层定义了一个Executor接口来操作数据库，它会根据SqlSession传递的参数动态地生成需要执行的SQL语句，同时负责查询缓存的维护。
6. 在Executor接口中，包含一个MappedStatement类型的参数，该参数是对映射参数的封装，用于存储映射的SQL语句的id、参数等等，Mapper.xml文件中一个SQL对应一个MappedStatement对象，SQL的id也就是MappedStatement的id。
7. 输入参数映射。在执行的时候，MappedStatement会对于用户执行SQL语句的输入参数进行定义，可以定义为List、Map、基本类型、POJO类型，Executor执行器会通过MappedStatement对象在执行SQL之前，将输入的Java对象映射到SQL语句中，这里对输入参数的映射过程就类似于JDBC编程中对preparedStatement对象设置参数的过程。
8. 输出参数映射。在数据库执行完SQL之后，MappedStatement对象会对SQL执行输出的结果进行定义，同理，可以定义为List、Map、基本类型、POJO类型，Executor执行器会通过MappedStatement对象在执行SQL语句后，将输出结果映射至Java对象中。这种将输出结果映射到Java对象的过程就类似于JDBC编程中对结果的解析处理过程。



### config

在实际开发中，为了避免Spring配置文件中的信息过于臃肿，通常会将配置文件中不同的部分按照不同的功能分散在多个配置文件中。

例如可以将事务文件放在application-transaction.properties中，将数据源放在application-db.properties中，这样一来，加载xml的时候，只需要通过application*.properties的方式就可以将所有的配置文件都导入进来。



### cache

1. mybatis-config.xml

   ```xml
   <settings>
   <!--因为cacheEnabled的取值默认就为true，所以这一步可以省略不配置。
   为true代表开启二级缓存；为false代表不开启二级缓存。-->
   <setting name="cacheEnabled" value="true"/>
   </settings>
   ```

2. Mapper上加上@CacheNamespace

3. 在mapper.xml的namespace下加上

   ```xml
       <cache eviction="LRU" flushInterval="180000" size="1024" readOnly="true"/>
   ```



## default class

### ApplicationListener

```java
public class ApplicationListenerEnvironmentPrepared implements ApplicationListener<ApplicationEnvironmentPreparedEvent> {
    @Override
    public void onApplicationEvent(ApplicationEnvironmentPreparedEvent event) {
        System.out.println(getClass().getSimpleName());
    }
}
```





### CurdRepository







### JpaRepository









### ResponseEntity

@ResponseEntity和@ResponseBody

首先如果在Controller的某个方法上加上@ResponseBody注解，就可以拿到Json格式的数据。

**其将方法的返回值通过适当的转换器转换为指定的格式之后，写入到 response 对象的 body 区，通常用来给客户端返回 JSON 数据或者是 XML 数据，当方法上面没有写 ResponseBody 时，底层会将方法的返回值封装为 ModelAndView 对象；需要注意的是，在使用此注解之后不会再走试图处理器，而是直接将数据写入到输入流中，他的效果等同于通过 response 对象输出指定格式的数据。**

简单来说：@RestController=Controller+ResponseBody

但是对于一个完整的Http响应来说，ResponseBody只是响应体，Http的响应除了响应体之外，还有状态码，放在注解中就是@ResponseStatus。

[@ResponseBody ResponseEntity - niceyoo - 博客园 (cnblogs.com)](https://www.cnblogs.com/niceyoo/p/10092826.html)

ResponseEntity继承的是HttpEntity的类，可以添加状态码，被用于RestTemplate和Controller。

```java 
@GetMapping("list")
public ResponseEntity<List<Category>> queryCategoryListByPid(@RequestParam("pid")Long pid){
   return ResponseEntity.ok( mCategoryService.queryCategoryListByPid(pid) );
}
```

`ResponseEntity<T> `，泛型T 表示要设置的返回的 **响应体**，而 ResponseEntity.ok() 表示设置的状态码 200，而 ResponseEntity.ok() 其实是一种快捷的写法，如果不这样写的话：

```java
@GetMapping("list")
 public ResponseEntity<List<Category>> queryCategoryListByPid(@RequestParam("pid")Long pid){
    //return  ResponseEntity.status(200).body(mCategoryService.queryCategoryListByPid(pid));
    //或者
    return ResponseEntity.status(HttpStatus.OK).body(mCategoryService.queryCategoryListByPid(pid));
 }
```

总结下来[ResponseEntity和@ResponseBody以及@ResponseStatus区别_roy的专栏](https://blog.csdn.net/deanwq/article/details/102907212)

@ResponseBody一般与@Controller组合使用，用来返回JSON字符串：

- @Controller
- @ResponseBody

而@ResponseStatus一般与RestController组合使用：

- @RestController
- @ResponseStatus



ResponseEntity是一种泛型，返回的类型并非一定要是Json格式，可以使用任何类型响应主体。

```java
@Controller
public class XXXController{
 
    @GetMapping("/hello")
    public ResponseEntity<String> hello() {
       return new ResponseEntity<>("Hello World!", HttpStatus.OK);
    }
}
```

这里的“Hello World!"就是一个字符串返回。

还可以设置Http的标头：

```java 
@GetMapping("/customHeader")
public ResponseEntity<String> customHeader() {
   HttpHeaders headers = new HttpHeaders();
   headers.add("Custom-Header", "foo");
 
   return new ResponseEntity<>("Custom header set", headers, HttpStatus.OK);
}
```

甚至返回一个对象：

```java
@GetMapping("/hello")
public ResponseEntity<String> hello() {
    //返回是JSON字符串：[{"name": "jdon"}]
    return new ResponseEntity<>(new User("jdon"), HttpStatus.OK);
}
```

但是在一般情况下我们只是想返回一个带有数据的正常响应，那么只要使用@注解即可，在类级别使用@Controller标注情况下， @ResponseBody注解告诉返回的对象将自动序列化为JSON，并通过回控制器的HttpResponse对象。



如果直接在类上面注解了@RestController，就不需要使用@ResponseBody了，可以直接返回对象，或者使用@ResponseStatus返回状态码。

```java 
@RestController
public class XXXController{
 
    @ResponseStatus(HttpStatus.FOUND)
    public User postResponseController() {
        return new User("Thanks For Posting!!!");
    }
}
```











## rules

### RESTful统一规范响应数据格式

**REST是Representational State Transfer**的缩写，是在2000年被Roy Thomas Fielding提出的，Fielding是一个很厉害的人物，他是HTTP协议的主要设计者。REST是他对互联网软件构架的原则。**REST是一种针对网络应用设计和软件开发方式，降低了开发的复杂性，提高了系统的可伸缩性**。

我们在开发过程中需要有一个统一的数据返回格式，这样可以使得所有开发人员返回结果风格统一，减少前后端开发人员的沟通时间。REST只是一种标准化的开发约定，下面我们提供一个通过返回结果的实现。

一个常用的Result类·

```Java
@Data
public class Result<T> {
    // 调用成功或者失败
    private Integer code = 0;
    // 需要传递的信息，例如错误信息
    private String msg;
    private T data;
    
   	public Result() {}
    
    public Result(Integer code, String msg, T data) {
        this.code = code;
        this.msg = msg;
        this.data = data;
    }
    
    public String toString() {
        return "code = " + code + ", msg = " + msg + ", data = " + data.toString();
    }
}
```

一个enum枚举类，定义

```java 
public enum ResultEnum {
    //这里是可以自己定义的，方便与前端交互即可
    UNKNOWN_ERROR(-1,"未知错误"),
    SUCCESS(10000,"成功"),
    USER_NOT_EXIST(1,"用户不存在"),
    USER_IS_EXISTS(2,"用户已存在"),
    DATA_IS_NULL(3,"数据为空"),
    
    private Integer code;
    private String msg;
 
    ResultEnum(Integer code, String msg) {
        this.code = code;
        this.msg = msg;
    }
 
    public Integer getCode() {
        return code;
    }
 
    public String getMsg() {
        return msg;
    }
}
```

以及一个常用的ResultUtil

```java 
public class ResultUtil {
 
    /**成功且带数据**/
    public static Result success(Object object){
        Result result = new Result();
        result.setCode(ResultEnum.SUCCESS.getCode());
        result.setMsg(ResultEnum.SUCCESS.getMsg());
        result.setData(object);
        return result;
    }
    /**成功但不带数据**/
    public static Result success(){
 
        return success(null);
    }
    /**失败**/
    public static Result error(Integer code,String msg){
        Result result = new Result();
        result.setCode(code);
        result.setMsg(msg);
        return result;
    }
}
```

在controller中加入返回Result的方法

```java @GetMapping("/test")
@ResponseBody
public Result test() throws Exception{
    String str = "程序猿最帅";
    return ResultUtil.success(str);
}
```



### VO、DTO、DO、PO

<img src="https://pic1.zhimg.com/v2-24e3ed681c02b6434681719753c53b40_r.jpg" alt="img" style="zoom: 67%;" />

DTO















## tips

### 一键导入Maven库







### 打包部署

[SpringBoot 打包部署最佳实践 - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1884117)

修改端口



部署运行

```shell
nohup java -jar demo-0.0.1-SNAPSHOT.jar >> log_gatsby_demo.out 2>&1 &
```

以及

在生产环境下启jar包时，发现原来好好的nohup信息输出到指定文件中的功能，突然出问题了。现象是控制台输出的信息一部分输出到了我指定的文件，另一部分却输出到了nohup.out，而我是不想让它产生nohup.out文件，不知道是什么原因。

我的启动命令是这样的：
 nohup java -jar eureka-server.jar  > ../logs/eureka-server.out  &
现在指行这个命令，会给我产生两个文件，一个中logs/myserver.out，另一个是nohup.out文件。
怎样才能让它不产生nohup.out文件呢。
网上搜了半天，据说好象可以把后面的 “&” 改成 “2>&1 &”，于是把启动命令改成如下：
 nohup java -jar eureka-server.jar  > ../logs/eureka-server.out  2>&1 &
 再次执行，问题解决。

```shell
2> &1
1> &2
2> stderr.log
1> stdout.log
```

解释如下：

2>
表示把标准错误(stderr)重定向，标准输出(stdout)是1。

尖括号后面可以跟文件名，或者是&1, &2，分别表示重定向到标准输出和标准错误.



### install私服Maven







### 多环境配置

[Spring-Boot application.yml 文件拆分，实现 maven 多环境动态启用 Profiles - 掘金 (juejin.cn)](https://juejin.cn/post/6844903873530822670)

[SpringBoot多环境配置详解(application-dev.yml、application-test.yml、application-prod.yml)_Wingkin的博客-程序员宅基地 - 程序员宅基地 (cxyzjd.com)](https://www.cxyzjd.com/article/Wing_kin666/111992800)

[springboot开发笔记-(7)-Profile多环境配置文件加载 - SegmentFault 思否](https://segmentfault.com/a/1190000023514649)

[你所不知道的spring boot多环境配置 - 简书 (jianshu.com)](https://www.jianshu.com/p/c0eaf1f8c529)

除了application.yml之外，还可以创建创建application-dev.yml，application-test.yml，application-prod.yml，存放在和application.yml同一个目录下，分别用来配置本地开发环境、测试环境、生产环境。

其中application.yml存放公共配置，可通过**修改active切换读取的配置文件**，比如`active: dev`改成`active: test`就是将读取application-dev.yml改为application-test.yml，环境也从本地开发变成了测试环境。

```yml
spring:
  profiles:
    active: dev
  application:
    name: data-transceivers #当前服务的名称
```

spring.profiles.active: dev表示当前加载测试环境配置application-dev.yml

spring.profiles.active: prod表示加载生产环境的配置文件application-prod.yml



也可以使用启动参数的方式

```shell
java -jar xxx.jar --spring.profiles.active=dev
```



使用Maven在打包时只加载出特定的Profiles配置文件

打包时使用`mvn package -P dev`，仍然可以使用`java -jar xxx.jar --spring.profiles.active=dev`来运行。



[idea配置多环境启动，dev、test、prod - 简书 (jianshu.com)](https://www.jianshu.com/p/b4c6d5ce3291)

(完整版配置)





