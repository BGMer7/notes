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











## Java Basics

### Serializable












### Iterable









## Code Rules

### RESTful APIs











## Docker

### Docker Compose







## Kafka









## Nacos







## Dubbo











## Zookeeper







## Jenkins











