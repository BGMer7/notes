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



## structure

代码层的结构：

#### 启动类Application.java

项目启动时，其当前层及子目录中所有controller会被编译，可以通过直接运行Application类来启动 Spring Boot应用。

Application中的常用注解：

@SpringBootConfiguration

@Configuration

@EnableAutoConfiguration



#### 前端控制层Controller

controller的功能为接受请求和控制响应，controller负责前后端的交互，接受前端请求，决定使用什么视图，然后控制需要准备什么数据显示，再调用service层，接收service返回的数据，最后将service返回来的数据或者页面返回到客户端。





#### 数据服务接口层Service

存放功能的业务逻辑，本质也是关于数据库的处理，但不是和数据库直接打交道。



#### 数据服务实现层Service Impl





#### 数据持久层dao

Dao层主要是和数据库进行交互，使用SQL语句向数据库发送命令，完成增删改查。

1. Dao层首先创建Dao接口，接着就在配置文件中定义该接口的实现类。
2. 在模块中调用Dao接口进行数据处理，而不需要关注这个接口的具体的实现类是什么





#### 实体类Domain/Entity

主要用于定义与数据库的对象相对应的属性，提供get/set方法，toString方法，定义有参和无参构造函数。





#### 工具类Utils







#### 配置类Config



























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











## tips

### 一键导入Maven库







### 打包部署

[SpringBoot 打包部署最佳实践 - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1884117)

修改端口



部署运行

```shell
nohup java -jar demo-0.0.1-SNAPSHOT.jar >> log_gatsby_demo.out 2> &1
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





