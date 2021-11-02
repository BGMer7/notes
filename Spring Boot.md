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

### @Configuration、@Bean、@EnableAutoConfiguration、@SpringBootConfiguration

Spring的@Bean注解用于告诉方法，产生一个Bean对象，然后这个Bean对象交给Spring管理。产生这个Bean对象的方法Spring只会调用一次，随后这个Spring将会将这个Bean对象放在自己的IOC容器中。

SpringIOC 容器管理一个或者多个bean，这些bean都需要在@Configuration注解下进行创建，在一个方法上使用@Bean注解就表明这个方法需要交给Spring进行管理。

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













### @Controller、@Service、@Repository、@Componet

@Controller: 表明一个注解的类是一个"Controller"，也就是控制器，可以把它理解为MVC 模式的Controller 这个角色。这个注解是一个特殊的@Component，允许实现类通过类路径的扫描扫描到。它通常与@RequestMapping 注解一起使用。
@Service: 表明这个带注解的类是一个"Service"，也就是服务层，可以把它理解为MVC 模式中的Service层这个角色，这个注解也是一个特殊的@Component，允许实现类通过类路径的扫描扫描到
@Repository: 表明这个注解的类是一个"Repository",团队实现了JavaEE 模式中像是作为"Data Access Object" 可能作为DAO来使用，当与 PersistenceExceptionTranslationPostProcessor 结合使用时，这样注释的类有资格获得Spring转换的目的。这个注解也是@Component 的一个特殊实现，允许实现类能够被自动扫描到
@Component: 表明这个注释的类是一个组件，当使用基于注释的配置和类路径扫描时，这些类被视为自动检测的候选者。





### @Resource、@Autowired

[@Autowired的这些骚操作，你都知道吗？ - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1867131)

[使用@Autowired注入父类对象时会报错，找到多个bean对象处理 - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1770166)

@Resource是jdk提供的注解，它默认使用byName进行装配，byName无法装配则使用byType；

 @Autowired是spring提供的注解，@Autowired默认使用byType进行装配，byType无法装配则使用byName，如果接口有多个实现类，需要配合@Qualifier注解使用。
————————————————
版权声明：本文为CSDN博主「_云卷云舒_」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/Alexshi5/article/details/84000678

[Spring的byType、byName的自动装配逻辑以及@Autowired和@Resource的使用示例_云卷云舒的架构师之路-CSDN博客](https://blog.csdn.net/Alexshi5/article/details/84000678)







### @EnableSwagger2

[Swagger2简单使用教程_M丶Rock-CSDN博客](https://blog.csdn.net/qq_37131913/article/details/111151379)





### @RestController、@Controller、@ResponseBody

在处理JSON的时候需要用到spring框架特有的注解@ResponseBody或者@RestController注解，这两个注解都会处理返回的数据格式，使用了该类型注解后返回的不再是视图，不会进行转跳，而是返回JSON或xml数据格式，输出在页面上。



@RestController，一般是使用在类上的，其实就是结合了@Controller和@ResponseBody两个注解。

@ResponseBody，一般是使用在单独的方法上的，需要哪个方法返回json数据格式，就在哪个方法上使用，具有针对性。

如果哪个类下的所有方法需要返回json数据格式的，就在哪个类上使用该注解，具有统一性；需要注意的是，使用了@RestController注解之后，其本质相当于在该类的所有方法上都统一使用了@ResponseBody注解，所以该类下的所有方法都会返回json数据格式，输出在页面上，而不会再返回视图。
采用@Contoller注解的方式，可以使接口的定义更加简单，将@Controller标记在某个类上，配合@RequestMapping注解，可以在一个类中定义多个接口，这样使用起来更加灵活。



### @RequestMapping

[@RequestMapping注解的用法-阿里云开发者社区 (aliyun.com)](https://developer.aliyun.com/article/651661)

```java
@RequestMapping(value="/{id}", method=RequestMethod.PUT)
```







### @Insert、@Delete、@Update、@Select







### @ApiOperation







### @Entry







### @Data











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











## theory

