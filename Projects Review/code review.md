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












### Iterable





#### interface extends









## Code Rules

### RESTful APIs





## Spring Boot

### pom.xml

#### dependency











#### exclusions











#### artifactId











### DTO























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











