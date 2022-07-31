# Redis

[Redis](https://www.iamshuaidi.com/redis-interview)

### Redis定义

Redis（全称：Remote Dictionary Server 远程字典服务）是一个开源的使用 ANSI C 语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value 数据库，并提供多种语言的 API。



### Redis的使用场景

1. 缓存：Redis作为缓存，减轻MySQL的查询压力，提升系统性能；
2. 排序：利用Redis的SortSet（有序集合）实现排序；
3. Session 共享：Session 是保存在服务器的文件中，如果是集群服务，同一个用户过来可能落在不同机器上，这就会导致用户频繁登陆；采用 Redis 保存 Session 后，无论用户落在那台机器上都能够获取到对应的 Session 信息；
4. 计数器/限速器：利用 Redis 中原子性的自增操作，我们可以统计类似用户点赞数、用户访问数等。这类操作如果用 MySQL，频繁的读写会带来相当大的压力；限速器比较典型的使用场景是限制某个用户访问某个 API 的频率，常用的有抢购时，防止用户疯狂点击带来不必要的压力；
5. 好友关系：利用集合的一些命令，比如求交集、并集、差集等。可以方便解决一些共同好友、共同爱好之类的功能；
6. 消息队列：除了 Redis 自身的发布/订阅模式，我们也可以利用 List 来实现一个队列机制，比如：到货通知、邮件发送之类的需求，不需要高可靠，但是会带来非常大的 DB 压力，完全可以用 List 来完成异步解耦；



**Redis 不适合的场景**

数据量太大、数据访问频率非常低的业务都不适合使用 Redis，数据太大会增加成本，访问频率太低，保存在内存中纯属浪费资源。





### Redis的功能

[redis有哪些功能 - java面试站 - 博客园 (cnblogs.com)](https://www.cnblogs.com/javamianshizhan/p/13476071.html#:~:text=redis有哪些功能 1 服务端的Redis 2,持久化（Persistence） 3 哨兵（Sentinel）和复制（Replication） 4 集群（Cluster）)

1. **基于本机内存的缓存**

   为了解决调用API依然需要2秒的问题，经过排查，其主要原因在于使用SQL获取热点新闻的过程中消耗了将近2秒的时间，于是乎，我们又想到了一个简单粗暴的解决方案，**即把SQL查询的结果直接缓存在当前api服务器的内存中（设置缓存有效时间为1分钟）**。后续1分钟内的请求直接读缓存，不再花费2秒去执行SQL了。

   假如这个api每秒接收到的请求时100个，那么一分钟就是6000个，也就是只有前2秒拥挤过来的请求会耗时2秒，后续的58秒中的所有请求都可以做到即使响应，而无需再等2秒的时间。

2. **基于服务端的Redis**

   当应用服务器的内存本身就很有限的时候，直接使用本机内存的方案就显得有些捉襟见肘。此时就会有个新的方案，我们**使用一台专门的服务器，用来运行Redis，这台服务器的内存分配很大，并且只用来运行redis**，这样就解决了应用服务器内存吃紧的问题。

3. **持久化（Persistence）**

   单一服务器仍然具有一定的可能性会出现故障，导致所有的缓存都丢失。虽然可以把Redis服务器重新上线，但是由于内存的数据丢失，造成了缓存雪崩，API服务器和数据库的压力还是一下子就上来了。

   所以这个时候Redis的持久化功能就派上用场了，可以缓解一下缓存雪崩带来的影响。**redis的持久化指的是redis会把内存的中的数据写入到硬盘中，在redis重新启动的时候加载这些数据，从而最大限度的降低缓存丢失带来的影响。**

4. **哨兵（Sentinel）和复制（Replication）**

   如果某一台redis服务器挂了，如何切换，如何保证备份的机器是原始服务器的完整备份呢？这时候就需要Sentinel和Replication出场了。

   **Sentinel可以管理多个Redis服务器，它提供了监控，提醒以及自动的故障转移的功能**；**Replication则是负责让一个Redis服务器可以配备多个备份的服务器。**Redis也是利用这两个功能来保证Redis的高可用的。此外，Sentinel功能则是对Redis的发布和订阅功能的一个利用。

5. **集群（Cluster）**

   单台服务器资源的总是有上限的，CPU资源和IO资源我们可以通过主从复制，进行读写分离，把一部分CPU和IO的压力转移到从服务器上。但是内存资源怎么办，主从模式做到的只是相同数据的备份，并不能横向扩充内存；单台机器的内存也只能进行加大处理，但是总有上限的。所以我们就需要一种解决方案，可以让我们横向扩展。最终的目的既是把每台服务器只负责其中的一部分，让这些所有的服务器构成一个整体，对外界的消费者而言，这一组分布式的服务器就像是一个集中式的服务器一样。







### Redis的五种数据结构

1. **String**

   字符串类型是 Redis 最基础的数据结构，首先键是字符串类型，而且其他几种结构都是在字符串类型基础上构建的。字符串类型实际上可以是字符串：简单的字符串、XML、JSON；数字：整数、浮点数；二进制：图片、音频、视频。

   使用场景：缓存、计数器、共享 Session、限速。

2. **Hash**

   在 Redis中哈希类型是指键本身是一种键值对结构，如 value={{field1,value1},……{fieldN,valueN}}

   使用场景：哈希结构相对于字符串序列化缓存信息更加直观，并且在更新操作上更加便捷。所以常常用于用户信息等管理，但是哈希类型和关系型数据库有所不同，哈希类型是稀疏的，而关系型数据库是完全结构化的，关系型数据库可以做复杂的关系查询，而 Redis 去模拟关系型复杂查询开发困难且维护成本高。

3. **List**

   列表类型是用来储存多个有序的字符串，列表中的每个字符串成为元素，一个列表最多可以储存 2 ^ 32 – 1 个元素，在 Redis 中，可以队列表两端插入和弹出，还可以获取指定范围的元素列表、获取指定索引下的元素等，列表是一种比较灵活的数据结构，它可以充当栈和队列的角色。

4. **Set**

   集合类型也是用来保存多个字符串的元素，但和列表不同的是集合中不允许有重复的元素，并且集合中的元素是无序的，不能通过索引下标获取元素，Redis 除了支持集合内的增删改查，同时还支持多个集合取交集、并集、差集。合理的使用好集合类型，能在实际开发中解决很多实际问题。

   使用场景：如：一个用户对娱乐、体育比较感兴趣，另一个可能对新闻感兴趣，这些兴趣就是标签，有了这些数据就可以得到同一标签的人，以及用户的共同爱好的标签，这些数据对于用户体验以及曾强用户粘度比较重要。

5. **Zset**

   有序集合和集合有着必然的联系，它保留了集合不能有重复成员的特性，但不同得是，有序集合中的元素是可以排序的，但是它和列表的使用索引下标作为排序依据不同的是：它给每个元素设置一个分数，作为排序的依据。

   使用场景：排行榜是有序集合经典的使用场景。例如：视频网站需要对用户上传的文件做排行榜，榜单维护可能是多方面：按照时间、按照播放量、按照获得的赞数等。

   

### Redis的性能

1. Redis基于内存，读取速度要超过在硬盘上持久化的数据结构。
2. 数据结构很简单，读取也很快。
3. Redis是单线程，避免了不必要的上下文切换和竞争条件，也不存在多进程或者多线程导致的切换而消耗 CPU，不用去考虑各种锁的问题，不存在加锁释放锁操作，没有因为可能出现死锁而导致的性能消耗；
4. 使用多路 I/O 复用模型，非阻塞 IO；
5. 使用底层模型不同，它们之间底层实现方式以及与客户端之间通信的应用协议不一样，Redis直接自己构建了VM 机制 ，因为一般的系统调用系统函数的话，会浪费一定的时间去移动和请求；

>多路I/O复用模型是利用 select、poll、epoll 可以同时监察多个流的 I/O 事件的能力，在空闲的时候，会把当前线程阻塞掉，当有一个或多个流有 I/O 事件时，就从阻塞态中唤醒，于是程序就会轮询一遍所有的流（epoll 是只轮询那些真正发出了事件的流），并且只依次顺序的处理就绪的流，这种做法就避免了大量的无用操作。
>
>这里“多路”指的是多个网络连接，“复用”指的是复用同一个线程。采用多路 I/O 复用技术可以让单个线程高效的处理多个连接请求（尽量减少网络 IO 的时间消耗），且 Redis 在内存中操作数据的速度非常快，也就是说内存内的操作不会成为影响Redis性能的瓶颈，主要由以上几点造就了 Redis 具有很高的吞吐量。





### 缓存穿透 | 缓存击穿 | 缓存雪崩

[【高并发】面试官：讲讲什么是缓存穿透？击穿？雪崩？如何解决？_缓存_冰河_InfoQ写作社区](https://xie.infoq.cn/article/39495c2d568aca1d6db5c9c50)

#### 缓存穿透

缓存穿透问题在一定程度上与缓存命中率有关。如果我们的缓存设计的不合理，缓存的命中率非常低，那么，这个缓存就形同虚设，数据访问的绝大部分压力都会集中在后端数据库层面。

如果在请求数据时，在缓存层和数据库层都没有找到符合条件的数据，也就是说，在缓存层和数据库层都没有命中数据，那么，这种情况就叫作缓存穿透。

<img src="https://static001.geekbang.org/infoq/a5/a58ddda2d741fecc2b51129e9a1db458.png" alt="img" style="zoom:67%;" />

造成缓存穿透的主要原因就是：**查询某个 Key 对应的数据，Redis 缓存中没有相应的数据，则直接到数据库中查询。数据库中也不存在要查询的数据，则数据库会返回空，而 Redis 也不会缓存这个空结果。这就造成每次通过这样的 Key 去查询数据都会直接到数据库中查询，Redis 不会缓存空结果。**这就造成了缓存穿透的问题。



**解决缓存穿透的方法**

第一种解决方案：**就是把空对象缓存起来。**当第一次从数据库中查询出来的结果为空时，我们就将这个空对象加载到缓存，并设置合理的过期时间，这样，就能够在一定程度上保障后端数据库的安全。

第二种解决方案：就是使用布隆过滤器，布隆过滤器可以针对大数据量的、有规律的键值进行处理。**一条记录是不是存在，本质上是一个 Bool 值，只需要使用 1bit 就可以存储。**我们可以使用布隆过滤器将这种表示是、否等操作，压缩到一个数据结构中。比如，我们最熟悉的用户性别这种数据，就非常适合使用布隆过滤器来处理。



#### 缓存击穿

[如何防止缓存穿透、缓存击穿、缓存雪崩 - 简书 (jianshu.com)](https://www.jianshu.com/p/62725919db21)

缓存击穿是一个热点的Key，有大并发集中对其进行访问，突然间这个Key失效了，导致大并发全部打在数据库上，导致数据库压力剧增。这种现象就叫做缓存击穿。

> 如果缓存中的数据在某个时刻批量过期，导致大部分用户的请求都会直接落在数据库上，这种现象就叫作缓存击穿。

造成缓存击穿的主要原因就是：我们为缓存中的数据设置了过期时间。如果在某个时刻从数据库获取了大量的数据，并设置了相同的过期时间，这些缓存的数据就会在同一时刻失效，造成缓存击穿问题。

![img](https://static001.geekbang.org/infoq/1f/1f307ba02e1c1c04c73b877e167d58e6.png)



**解决缓存击穿的方法**

**对于比较热点的数据，我们可以在缓存中设置这些数据永不过期**；也可以在访问数据的时候，在缓存中更新这些数据的过期时间；如果是批量入库的缓存项，我们可以为这些缓存项分配比较合理的过期时间，避免同一时刻失效。

还有一种解决方案就是：**使用互斥锁。如果缓存失效的情况，只有拿到锁才可以查询数据库**，降低了在同一时刻打在数据库上的请求，防止数据库打死。当然这样会导致系统的性能变差。



#### 缓存雪崩

请求的不同的数据没有命中缓存但是命中数据库，当大量的请求在极短的时间段内一起请求不同数据的缓存并都未命中（如：大批量的数据缓存到期，热点数据没有预热等），这些请求会继续作用到数据库上，造成DB压力剧增，甚至宕机。

缓存击穿是指并发查询某一条数据，缓存雪崩是指不同数据都过期，查询这些数据时都查不到。



![img](https://static001.geekbang.org/infoq/df/df9de5602fe8cc0f07a4b54a0da99151.png)



**解决缓存雪崩的方法**

解决缓存雪崩问题最常用的一种方案就是保证 Redis 的高可用，将 Redis 缓存部署成高可用集群（必要时候做成异地多活），可以有效的防止缓存雪崩问题的发生。

1. 为了缓解大并发流量，我们也可以使用限流降级的方式防止缓存雪崩。例如，**在缓存失效后，通过加锁或者使用队列来控制读数据库写缓存的线程数量**。具体点就是设置某些 Key 只允许一个线程查询数据和写缓存，其他线程等待。则能够有效的缓解大并发流量对数据库打来的巨大冲击。

2. 另外，我们也可以**通过数据预热的方式将可能大量访问的数据加载到缓存，在即将发生大并发访问的时候，提前手动触发加载不同的数据到缓存中。**

3. 为数据**设置不同的过期时间**，让缓存失效的时间点尽量分散，使用随机数设定过期时间，不至于在同一时刻全部失效。
4. **做二级缓存，或者双缓存策略**：Cache1 为原始缓存，Cache2 为拷贝缓存，Cache1 失效时，可以访问 Cache2，Cache1 缓存失效时间设置为短期，Cache2 设置为长期。



### 缓存和数据库的一致性

分布式系统中的一致性分为强一致性、弱一致性和最终一致性。

强一致性：如果系统要求是强一致性的高并发场景，那么建议不要使用缓存。

弱一致性：只能尽可能快地完成一致。

最终一致性：系统保证在一定的时间内能够达到数据一致的状态，这里之所以将最终一致性单独提出来，是因为它是弱一致性中非常推崇的一种一致性模型，也是业界在大型分布式系统的数据一致性上比较推崇的模型。一般情况下，高可用只确保最终一致性，不确保强一致性。

强一致性，读请求和写请求会串行化，串到一个内存队列里去，这样会大大增加系统的处理效率，吞吐量也会大大降低。



理论上来说，如果存在绝对理想的缓存过期时间，我们就能保证缓存中的数据是和数据库中的数据是一致。

因为只要缓存数据过期了，就会被删除。随后读的时候，因为缓存里没有，就可以查数据库的数据，然后将数据库查出来的数据写入到缓存中。除了设置过期时间，我们还需要做更多的措施来尽量避免数据库与缓存处于不一致的情况发生。

但是实际上这种理想情况不存在，因为没有办法预测用户会在什么时候进行写入操作。

因此保持缓存和数据库一致性的关键就在于：新增、更改、删除数据库操作时同步更新 Redis，可以使用事务机制来保证数据的一致性。

一般有如下四种方案：

1. 先更新数据库，后更新缓存
2. 先更新缓存，后更新数据库
3. 先删除缓存，后更新数据库
4. 先更新数据库，后删除缓存

四种方案中，

方案一在并发情况下会出现脏读数据，因为这并不是一个原子操作。有可能出现先更新了两次数据库，之后再更新两次缓存。

第二种方案存在的问题是：如果先更新缓存成功，但是数据库更新失败，则肯定会造成数据不一致。



主要的方案有两种：

1. 延时双删：

   基本思路：在对数据库写操作的时候进行删除缓存，并且设置合理的超时时间

   基本步骤：先删除缓存，在写入数据库，休眠一段时间，再删除缓存

   注：休眠的时间是根据自己的项目的读数据业务逻辑的耗时来确定的。这样做主要是为了保证在写请求之前确保读请求结束，写请求可以删除读请求造成的缓存脏数据。

   该方案的弊端： 集合双删策略+缓存超时策略设置，这样最差的结果就是在超时时间内数据存在不一致，又增加了写请求的耗时。

2. 一步更新缓存（基于订阅binlog的同步机制）：

   基本思路：MySQL binlog增强订阅消费+消息队列+增强数据更新到redis，读redis，热数据基本都在redis，写MySQL

   增删改都是通过操作MySQL，然后MySQL更新binlog，binlog来更新redis

   数据操作主要分为两大块：
   一个是全量，将全部数据写去redis；另一个就是增量（update、insert、delete），实时更新。

   读取binlog后分析 ，利用消息队列,推送更新各台的redis缓存数据。
   这样一旦MySQL中产生了新的写入、更新、删除等操作，就可以把binlog相关的消息推送至Redis，Redis再根据binlog中的记录，对Redis进行更新。
   其实这种机制，很类似MySQL的主从备份机制，因为MySQL的主备也是通过binlog来实现的数据一致性。

   这里可以结合使用canal(阿里的一款开源框架)，通过该框架可以对MySQL的binlog进行订阅，而canal正是模仿了mysql的slave数据库的备份请求，使得Redis的数据更新达到了相同的效果。

   当然，这里的消息推送工具你也可以采用别的第三方：kafka、rabbitMQ等来实现推送更新Redis。







### Redis持久化的方案

#### RDB

[完全掌握Redis持久化：RDB和AOF-Redis-PHP中文网](https://www.php.cn/redis/492972.html)

持久化的方案就是把内存中的数据写入到磁盘中去，主要是为了防止服务器出现异常之后，在内存中的数据丢失，缓存失效。

Redis提供两种持久化的方案：RDB和AOF。

Redis的默认持久化方案是RDB，RDB就是Redis Datebase的缩写，按照一定的时间将快照保存在硬盘中，对应产生的数据库文件为dump.db。它保存的是某一时刻的数据并不关注过程。**RDB保存redis某一时刻的数据的快照。**RDB持久化可以手动触发，也可以自动触发。

**触发快照的方式**

1. 符合自定义配置的快照规则；
2. 执行save或者bgsave命令；
3. 执行flushall命令；
4. 执行主从复制操作 (第一次)。



**RDB执行备份的方式**

redis会单独创建（fork）一个子进程来进行持久化，会先将数据写入到临时文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好了的文件。**整个过程中，主进程是不进行任何IO操作的**，这就确保了极高的性能。如果需要进行大规模数据的恢复，且对数据的恢复完整性不是非常敏感，那么RDB方式要比AOF方式更加的高效。RDB的缺点是最后一次持久化的数据可能丢失。



**手动触发**：save和bgsave

1. **`save`**
   执行`save`命令会手动触发RDB持久化，**但是`save`命令会阻塞Redis服务**，直到RDB持久化完成。当Redis服务储存大量数据时，会造成较长时间的阻塞，不建议使用。

2. **`bgsave`**
   执行`bgsave`命令也会手动触发RDB持久化，**和`save`命令不同是：Redis服务一般不会阻塞**。Redis进程会执行fork操作创建子进程，RDB持久化由子进程负责，不会阻塞Redis服务进程。Redis服务的阻塞只发生在fork阶段，一般情况时间很短。

   bgsave的流程如下：

   1、执行`bgsave`命令，Redis进程**先判断当前是否存在正在执行的RDB或AOF子线程**，如果存在就是直接结束。
   2、Redis进程执行fork操作创建子线程，在fork操作的过程中Redis进程会被阻塞。
   3、Redis进程fork完成后，`bgsave`命令就结束了，自此Redis进程不会被阻塞，可以响应其他命令。
   4、子进程根据Redis进程的内存生成快照文件，并替换原有的RDB文件。
   5、同时发送信号给主进程，通知主进程rdb持久化完成，主进程更新相关的统计信息（info Persitence下的rdb_*相关选项）。



**自动触发**

除了执行以上命令手动触发以外，Redis内部可以自动触发RDB持久化。自动触发的RDB持久化都是采用`bgsave`的方式，减少Redis进程的阻塞。

1. 在配置文件中设置了`save`的相关配置，如`sava m n`，它表示在m秒内数据被修改过n次时，自动触发`bgsave`操作。
2. 当从节点做全量复制时，主节点会自动执行`bgsave`操作，并且把生成的RDB文件发送给从节点。
3. 执行`debug reload`命令时，也会自动触发`bgsave`操作。
4. 执行`shutdown`命令时，如果没有开启AOF持久化也会自动触发`bgsave`操作。



**dump.rdb中配置RDB**

**快照周期**：内存快照虽然可以通过技术人员手动执行`SAVE`或`BGSAVE`命令来进行，但生产环境下多数情况都会设置其周期性执行条件。

- **Redis中默认的周期新设置**

```shell
# 周期性执行条件的设置格式为
save <seconds> <changes>
# 默认的设置为：
save 900 1
save 300 10
save 60 10000
# 以下设置方式为关闭RDB快照功能
save ""
```

以上三项默认信息设置代表的意义是：

- 如果900秒内有1条Key信息发生变化，则进行快照；
- 如果300秒内有10条Key信息发生变化，则进行快照；
- 如果60秒内有10000条Key信息发生变化，则进行快照。读者可以按照这个规则，根据自己的实际请求压力进行设置调整。



**在进行快照操作的这段时间，如果发生服务崩溃怎么办？**
很简单，在没有将数据全部写入到磁盘前，这次快照操作都不算成功。如果出现了服务崩溃的情况，将以上一次完整的RDB快照文件作为恢复内存数据的参考。也就是说，在快照操作过程中不能影响上一次的备份数据。Redis服务会在磁盘上创建一个临时文件进行数据操作，待操作成功后才会用这个临时文件替换掉上一次的备份。

**可以每秒做一次快照吗？**
对于快照来说，所谓“连拍”就是指连续地做快照。这样一来，快照的间隔时间变得很短，即使某一时刻发生宕机了，因为上一时刻快照刚执行，丢失的数据也不会太多。但是，这其中的快照间隔时间就很关键了。

针对RDB不适合实时持久化的问题，Redis提供了AOF持久化方式来解决。



#### AOF

AOF（Append Only File）持久化是把每次写命令追加写入日志中，当需要恢复数据时重新执行AOF文件中的命令就可以了。AOF解决了数据持久化的实时性，也是目前主流的Redis持久化方式。

> Redis是“写后”日志，Redis先执行命令，把数据写入内存，然后才记录日志。日志里记录的是Redis收到的每一条命令，这些命令是以文本形式保存。
>
> PS: 大多数的数据库采用的是写前日志（WAL），例如MySQL，通过写前日志和两阶段提交，实现数据和逻辑的一致性。

而AOF日志采用写后日志，即**先写内存，后写日志**。

<img src="https://img.php.cn/upload/article/000/000/067/532282aad9aa59277bd836f3a3f290e0-4.png" alt="在这里插入图片描述" style="zoom: 25%;" />

**为什么采用写后日志？**
Redis要求高性能，采用写后日志有两方面好处：

- **避免额外的检查开销**：Redis 在向 AOF 里面记录日志的时候，并不会先去对这些命令进行语法检查。所以，如果先记日志再执行命令的话，日志中就有可能记录了错误的命令，Redis 在使用日志恢复数据时，就可能会出错。
- 不会阻塞当前的写操作

但这种方式存在潜在风险：

- 如果命令执行完成，写日志之前宕机了，会丢失数据。
- 主线程写磁盘压力大，导致写盘慢，阻塞后续操作。



**AOF的实现**

AOF日志记录Redis的每个写命令，步骤分为：命令追加（append）、文件写入（write）和文件同步（sync）。

- **命令追加** 当AOF持久化功能打开了，服务器在执行完一个写命令之后，会以协议格式将被执行的写命令追加到服务器的 aof_buf 缓冲区。
- **文件写入和同步** 关于何时将 aof_buf 缓冲区的内容写入AOF文件中，Redis提供了三种写回策略：

- <img src="https://img.php.cn/upload/article/000/000/067/66727a89e41a7e9789eb52006d878576-5.png" alt="在这里插入图片描述" style="zoom:33%;" />
- Always，同步写回：每个写命令执行完，立马同步地将日志写回磁盘；
- Everysec，每秒写回：每个写命令执行完，只是先把日志写到AOF文件的内存缓冲区，每隔一秒把缓冲区中的内容写入磁盘；
- No，操作系统控制的写回：每个写命令执行完，只是先把日志写到AOF文件的内存缓冲区，由操作系统决定何时将缓冲区内容写回磁盘。

**redis.conf中配置AOF**

默认情况下，Redis是没有开启AOF的，可以通过配置redis.conf文件来开启AOF持久化，关于AOF的配置如下：

```shell
# appendonly参数开启AOF持久化
appendonly no

# AOF持久化的文件名，默认是appendonly.aof
appendfilename "appendonly.aof"

# AOF文件的保存位置和RDB文件的位置相同，都是通过dir参数设置的
dir ./

# 同步策略
# appendfsync always
appendfsync everysec
# appendfsync no

# aof重写期间是否同步
no-appendfsync-on-rewrite no

# 重写触发配置
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb

# 加载aof出错如何处理
aof-load-truncated yes

# 文件重写策略
aof-rewrite-incremental-fsync yes
```



**AOF重写**

> AOF会记录每个写命令到AOF文件，随着时间越来越长，AOF文件会变得越来越大。如果不加以控制，会对Redis服务器，甚至对操作系统造成影响，而且AOF文件越大，数据恢复也越慢。为了解决AOF文件体积膨胀的问题，Redis提供AOF文件重写机制来对AOF文件进行“瘦身”。

<img src="https://img.php.cn/upload/article/000/000/067/66727a89e41a7e9789eb52006d878576-6.png" alt="在这里插入图片描述" style="zoom:50%;" />

***AOF重写会阻塞吗？***
AOF重写过程是由后台进程bgrewriteaof来完成的。主线程fork出后台的bgrewriteaof子进程，fork会把主线程的内存拷贝一份给bgrewriteaof子进程，这里面就包含了数据库的最新数据。然后，bgrewriteaof子进程就可以在不影响主线程的情况下，逐一把拷贝的数据写成操作，记入重写日志。所以aof在重写时，**在fork进程时是会阻塞住主线程的**。

***AOF日志何时会重写？***
有两个配置项控制AOF重写的触发：
**auto-aof-rewrite-min-size**：**表示运行AOF重写时文件的最小大小，默认为64MB**。
**auto-aof-rewrite-percentage**:这个值的计算方式是，当前aof文件大小和上一次重写后aof文件大小的差值，再除以上一次重写后aof文件大小。也就是当前aof文件比上一次重写后aof文件的增量大小，和上一次重写后aof文件大小的比值。

***重写日志时，有新数据写入咋整？***
重写过程总结为：“一个拷贝，两处日志”。在fork出子进程时的拷贝，以及在重写时，如果有新数据写入，主线程就会将命令记录到两个aof日志内存缓冲区中。如果AOF写回策略配置的是always，则直接将命令写回旧的日志文件，并且保存一份命令至AOF重写缓冲区，这些操作对新的日志文件是不存在影响的。（旧的日志文件：主线程使用的日志文件，新的日志文件：bgrewriteaof进程使用的日志文件）

而在bgrewriteaof子进程完成日志文件的重写操作后，会提示主线程已经完成重写操作，主线程会将AOF重写缓冲中的命令追加到新的日志文件后面。这时候在高并发的情况下，AOF重写缓冲区积累可能会很大，这样就会造成阻塞，Redis后来通过Linux管道技术让aof重写期间就能同时进行回放，这样aof重写结束后只需回放少量剩余的数据即可。最后通过修改文件名的方式，保证文件切换的原子性。

在AOF重写日志期间发生宕机的话，因为日志文件还没切换，所以恢复数据时，用的还是旧的日志文件。



#### RDB和AOF混合方式（4.0版本)

> **Redis 4.0 中提出了一个混合使用 AOF 日志和内存快照的方法。简单来说，内存快照以一定的频率执行，在两次快照之间，使用 AOF 日志记录这期间的所有命令操作。**

这样一来，快照不用很频繁地执行，这就避免了频繁 fork 对主线程的影响。而且，AOF 日志也只用记录两次快照间的操作，也就是说，不需要记录所有操作了，因此，就不会出现文件过大的情况了，也可以避免重写开销。



#### 从持久化中恢复数据

> 数据的备份、持久化做完了，我们如何从这些持久化文件中恢复数据呢？如果一台服务器上有既有RDB文件，又有AOF文件，该加载谁呢？

- redis重启时判断是否开启aof，如果开启了aof，那么就优先加载aof文件；
- 如果aof存在，那么就去加载aof文件，加载成功的话redis重启成功，如果aof文件加载失败，那么会打印日志表示启动失败，此时可以去修复aof文件后重新启动；
- 若aof文件不存在，那么redis就会转而去加载rdb文件，如果rdb文件不存在，redis直接启动成功；
- 如果rdb文件存在就会去加载rdb文件恢复数据，如加载失败则打印日志提示启动失败，如加载成功，那么redis重启成功，且使用rdb文件恢复数据；

***那么为什么会优先加载AOF呢？因为AOF保存的数据更完整，通过上面的分析我们知道AOF基本上最多损失1s的数据。***



### Redis的内存淘汰策略

1. volatile-lru：从已设置过期时间的数据集（server. db[i]. expires）中挑选最近最少使用的数据淘汰；
2. volatile-ttl：从已设置过期时间的数据集（server. db[i]. expires）中挑选将要过期的数据淘汰。
3. volatile-random：从已设置过期时间的数据集（server. db[i]. expires）中任意选择数据淘汰。
4. allkeys-lru：从数据集（server. db[i]. dict）中挑选最近最少使用的数据淘汰。
5. allkeys-random：从数据集（server. db[i]. dict）中任意选择数据淘汰。
6. no-enviction（驱逐）：禁止驱逐数据。



### Redis的三种过期删除策略

- 定时过期：每个设置过期时间的key都需要创建一个定时器，到过期时间就会立即清除。该策略可以立即清除过期的数据，对内存很友好；**但是会占用大量的CPU资源去处理过期的数据**，从而影响缓存的响应时间和吞吐量。
- 惰性过期：只有当访问一个key时，才会判断该key是否已过期，过期则清除。该策略**可以最大化地节省CPU资源，却对内存非常不友好**。极端情况可能出现大量的过期key没有再次被访问，从而不会被清除，占用大量内存。
- 定期清除：每隔一定的时间，会扫描一定数量的数据库的expires字典中一定数量的key，并清除其中已过期的key。该策略是前两者的一个折中方案。通过调整定时扫描的时间间隔和每次扫描的限定耗时，可以在不同情况下使得CPU和内存资源达到最优的平衡效果。(expires字典会保存所有设置了过期时间的key的过期时间数据，其中，key是指向键空间中的某个键的指针，value是该键的毫秒精度的UNIX时间戳表示的过期时间。键空间是指该Redis集群中保存的所有键。)

Redis中同时使用了惰性过期和定期过期两种过期策略。



### Redis线程模型

这个问题下讨论Redis的单线程、多线程网络模型，以及多线程异步任务模型。

#### 单线程模型（网络线程模型）

在6.0以前的版本都是Redis的核心网络模型选择用单线程来实现。

正如redis官网上说，对于一个 DB 来说，CPU 通常不会是瓶颈，因为大多数请求不会是 CPU 密集型的，而是 I/O 密集型。**具体到 Redis 的话，如果不考虑 RDB/AOF 等持久化方案，Redis 是完全的纯内存操作，执行速度是非常快的，因此这部分操作通常不会是性能瓶颈，Redis 真正的性能瓶颈在于网络 I/O，也就是客户端和服务端之间的网络传输延迟**，因此 Redis 6.0版本前选择了**单线程的 I/O 多路复用**来实现它的核心网络模型。

##### 使用单线程网络模型的优势

- **避免过多的上下文切换开销**
  多线程调度过程中必然需要在 CPU 之间切换线程上下文 context，而上下文的切换是有开销的。单线程则可以规避进程内频繁的线程切换开销，因为程序始终运行在进程中单个线程内，没有多线程切换的场景。
- **避免同步机制的开销**
  如果 Redis 选择多线程模型，势必涉及到底层数据同步的问题，必然会引入某些同步机制，比如锁，而我们知道 Redis 不仅仅提供了简单的 key-value 数据结构，还有 list、set 和 hash 等等其他丰富的数据结构，而不同的数据结构对同步访问的加锁粒度又不尽相同，可能会导致在操作数据过程中带来很多加锁解锁的开销，增加程序复杂度的同时还会降低性能。
- **简单可维护**
  Redis 的作者 Salvatore Sanfilippo (别称 antirez) 对 Redis 的设计和代码有着近乎偏执的简洁性理念。因此代码的简单可维护性必然是 Redis 早期的核心准则之一，而引入多线程必然会导致代码的复杂度上升和可维护性下降。前面我们提到引入多线程必须的同步机制，如果 Redis 使用多线程模式，那么所有的底层数据结构都必须实现成线程安全的，这无疑又使得 Redis 的实现变得更加复杂。总而言之，Redis 选择单线程可以说是多方博弈之后的一种权衡：在保证足够的性能表现之下，使用单线程保持代码的简单和可维护性。

在 v6.0 版本之前，Redis 的核心网络模型一直是一个典型的单 Reactor 模型：**利用 epoll/select/kqueue 等多路复用技术，在单线程的事件循环中不断去处理事件（客户端请求），最后回写响应数据到客户端**	

[Redis线程模型 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/462925793)

下面我们描绘一下 客户端client 与 Redis server 建立连接、发起请求到接收到返回的整个过程：

- 首先Redis 服务器启动，开启主线程事件循环 aeMain，注册 acceptTcpHandler 连接应答处理器到用户配置的监听端口对应的文件描述符，等待新连接到来；
- 客户端和服务端建立网络连接，acceptTcpHandler 被调用，主线程将 readQueryFromClient 命令读取处理器绑定到新连接对应的文件描述符上作为对应事件发生时的回调函数，并初始化一个 client 绑定这个客户端连接；
- 客户端发送请求命令，触发读就绪事件，主线程调用 readQueryFromClient 通过 socket 读取客户端发送过来的命令存入 client->querybuf 读入缓冲区；
- 接着调用 processInputBuffer，在其中使用 processInlineBuffer 或者 processMultibulkBuffer 根据 Redis 协议解析命令，最后调用 processCommand 执行命令；
- 根据请求命令的类型（SET, GET, DEL, EXEC 等），分配相应的命令执行器去执行，最后调用 addReply 函数族的一系列函数将响应数据写入到对应 client 的写出缓冲区：client->buf 或者 client->reply ，client->buf 是首选的写出缓冲区，固定大小 16KB，一般来说可以缓冲足够多的响应数据，但是如果客户端在时间窗口内需要响应的数据非常大，那么则会自动切换到 client->reply链表上去，使用链表理论上能够保存无限大的数据（受限于机器的物理内存），最后把 client 添加进一个 LIFO 队列 clients_pending_write；
- 在事件循环 aeMain 中，主线程执行 beforeSleep --> handleClientsWithPendingWrites，遍历 clients_pending_write 队列，调用 writeToClient 把 client 的写出缓冲区里的数据回写到客户端，如果写出缓冲区还有数据遗留，则注册 sendReplyToClient 命令回复处理器到该连接的写就绪事件，等待客户端可写时在事件循环中再继续回写残余的响应数据。



#### Redis6.0之后的多线程模型

<img src="https://pic4.zhimg.com/v2-c286c18ded02b276adbe6e2431d46ddf_r.jpg" alt="preview" style="zoom:50%;" />

这种模式不再是单线程的事件循环，而是有多个线程（IO Thread）各自维护一个独立的事件循环。整体模型是由 Main 线程负责接收新连接，并分发给 IO Thread 去独立处理（解析请求命令），但是具体命令的执行还是使用main 线程来执行，最后使用IO 线程回写响应给客户端。

IO线程轮训socket列表读事件，然后解析为redis命令，并把解析好的命令放到全局待执行队列，然后主线程从全局待执行队列读取命令然后具体执行命令，最后把响应结果分配到不同IO线程，由IO线程来具体执行把响应结果写回客户端。

**也就是具体命令执行还是由main线程所在的事件循环单线程处理，只是读写socket事件由IO线程来处理。**

虽然多线程方案能提升1倍以上的性能，但整个方案仍然比较粗糙：

- 首先所有命令的执行仍然在主线程中进行，仍然存在性能瓶颈。
- 另外IO 读写为批处理读写，即所有 IO 线程先读取完请求数据并且解析为redis命令后，主线程才开始执行解析的命令；然后待主线程执行完所有的redis命令后，才让所有 IO 线程再一起回复所有响应；也就是说不同请求需要相互等待，效率不高。
- 最后在 IO 批处理读写和主线程处理时，使用线程自旋检测等待（如下代码），效率更是低下，即便任务很少，也很容易把 CPU 打满。



#### Redis任务多线程模型（异步任务，非网络线程模型）

Redis 在 v4.0 版本的时就已经引入了的多线程来做一些异步操作，这主要是为了解决一些非常耗时的命令，通过将这些命令的执行进行异步化，避免阻塞单线程网络模型的事件循环。

Redis 启动时，会创建三个任务队列，并对应构建 3 个 BIO 线程，三个 BIO 线程与 3 个任务队列之间一一对应。BIO 线程分别处理如下 3 种任务。

1. close 关闭文件任务。rewriteaof 完成后，主线程需要关闭旧的 AOF 文件，就向 close 队列插入一个旧 AOF 文件的关闭任务。由 close 线程来处理。
2. fysnc 任务。Redis 将 AOF 数据缓冲写入文件内核缓冲后，需要定期将系统内核缓冲数据写入磁盘，此时可以向 fsync 队列写入一个同步文件缓冲的任务，由 fsync 线程来处理。
3. lazyfree 任务。Redis 在需要淘汰元素数大于 64 的聚合类数据类型时，如列表、集合、哈希等，就往延迟清理队列中写入待回收的对象，由 lazyfree 线程后续进行异步回收。

BIO 线程的整个处理流程如图所示。当主线程有慢任务需要异步处理时。就会向对应的任务队列提交任务。提交任务时，首先申请内存空间，构建 BIO 任务。然后对队列锁进行加锁，在队列尾部追加新的 BIO 任务，最后尝试唤醒正在等待任务的 BIO 线程。

BIO 线程启动时或持续处理完所有任务，发现任务队列为空后，就会阻塞，并等待新任务的到来。当主线程有新任务后，主线程会提交任务，并唤醒 BIO 线程。BIO 线程随后开始轮询获取新任务，并进行处理。当处理完所有 BIO 任务后，则再次进入阻塞，等待下一轮唤醒。

**总结**

在Redis6.0版本前，其提供单线程网络模型，使用单线程来处理socket的读写事件、命令解析、命令执行工作。

在Redis6.0版本后，提供了多线程模型逻辑，其中socket的读写事件、命令解析使用IO线程来处理，但是具体命令的执行还是使用单线程事件循环来进行处理。但是其实现并不优雅。

最后无论是单线程还是多线程网络模型，命令的具体执行还是靠单线程事件循环来执行的，如果要执行的命令非常耗时，则会阻塞事件循环的执行，使得其他命令得不到及时执行，所以Redis4.0时开始提供异步多线程任务来解决耗时比较长的命令的执行，将其异步化执行，使得主事件循环线程可以及时得到释放。





### Redis分区

Redis 分区技术（又称 Redis Partition）指的是将 Redis 中的数据进行拆分，然后把拆分后的数据分散到多个不同的 Redis 实例（即服务器）中，每个实例仅存储数据集的某一部分（一个子集），我们把这个过程称之为 Redis 分区操作。

> Redis 实例指的是一台安装了 Redis 服务器的计算机。

分区（Partition）不仅是 Redis 中的概念，几乎所有数据库管理系统都会涉及到“分区”的应用。

**Redis 分区技术有两个方面的优势，一是提升服务器的性能，二是提高了服务器的数据存储能力。**

一方面，单台机器的 Redis 服务器，其网络 IO 能力和计算资源都是非常有限的，但是如果我们将请求分散到多台机器上，那么就能充分利用多台计算机的算力和网络带宽，从而整体上提升 Redis 服务器的性能。另一方面，随着存储数据的不断增加，单台机器的存储容量会达到极限，若将数据分散存储到多台 Redis 服务器上，其存储能力也将得到大幅度提升。

> 注意，Redis 分区技术可以利用多台计算机的内存总和，从而创建出大型的 Redis 数据库。

#### 范围分区

范围分区是最简单、最有效的分区方法之一。所谓范围分区指的是将特定范围的 key 映射到指定的 Redis 实例上。key 的命名格式如下：

```
object_name:<id>
```

比如：user:1、user:2 ...用来表示不同 id 的用户。下面通过一个示例了解范围分区的具体流程：

假设现在共有 3000 个用户，您可以把 id 从 0 到 1000 的用户映射到实例 R0 上，id 从 1001 到 2000 的用户映射到实例 R1 上，以此类推，将 id 从 2001 到 3000 的用户映射到实例 R2 上。

范围分区不仅简单，而且实用，适合许多的特定场景。但当存储的 key 不能按照范围划分时，那么范围分区就不再适用了，比如 key 是一组 uuid（通用唯一识别码）。如下所示：

```
9eb4d81b-31ec-4c69-a721-c7e1771413dd
```

此时范围分区就不再适用，就要用到另外一种分区方式——哈希分区。

#### 哈希分区

哈希分区与范围分区相比，它最显著的优势是适合任何形式的 key。哈希分区方法并不复杂比，id 表达式如下所示：

```
id=hash(key)%N
```

这里的 id 指的是 Redis 实例的编号，而 N 表示共有多少个 Redis 实例。

首先调用一个 crc32() 哈希函数，它可以将 key 转换为一个整数。 如下所示：

crc32(key)

假如转换后的整数是 93024922，此时共有 4 个 Redis 实例，对整数与实例的数量进行取模运算，就会得到一个 0 到 3 之间的整数，如下所示：

```
93024922 % 4 = 2
```

上述计算结果为 2 ，我们就把这个 key 映射到 R2 实例中，如果为 3 就映射到 R3实例中。以此类推，通过这种方式可以将所有的 key 分散到 4 个不同的 Redis 实例中。



#### Redis分区的缺陷

- 同时操作多个key,则不能使用Redis事务
- 当使用分区的时候，数据处理会非常复杂，例如为了备份你必须从不同的Redis实例和主机同时收集RDB / AOF文件
- 分区使用的粒度是key，不能使用一个非常长的排序key存储一个数据集
- 涉及多个key的操作通常不会被支持。例如你不能对两个集合求交集，因为他们可能被存储到不同的Redis实例（实际上这种情况也有办法，但是不能直接使用交集指令）。
- 分区时动态扩容或缩容可能非常复杂。Redis集群在运行时增加或者删除Redis节点，能做到最大程度对用户透明地数据再平衡，但其他一些客户端分区或者代理分区方法则不支持这种特性。然而，有一种预分片的技术也可以较好的解决这个问题



### Redis缓存预热

缓存预热是指系统上线后，提前将相关的缓存数据加载到缓存系统。避免在用户请求的时候，先查询数据库，然后再将数据缓存的问题，用户直接查询事先被预热的缓存数据。

如果不进行预热，那么Redis初始状态数据为空，系统上线初期，对于高并发的流量，都会访问到数据库中， 对数据库造成流量的压力。

缓存预热解决方案：

- 数据量不大的时候，工程启动的时候进行加载缓存动作；
- 数据量大的时候，设置一个定时任务脚本，进行缓存的刷新；
- 数据量太大的时候，优先保证热点数据进行提前加载到缓存。





### Redis单副本

Redis单副本，采用单个Redis节点部署架构，没有备用节点实时同步数据，不提供数据持久化和备份策略，适用于数据可靠性要求不高的纯缓存业务场景。

**优点：**

- 架构简单，部署方便；
- 高性价比：缓存使用时无需备用节点（单实例可用性可以用supervisor或crontab保证），当然为了满足业务的高可用性，也可以牺牲一个备用节点，但同时刻只有一个实例对外提供服务；
- 高性能。

**缺点：**

- 不保证数据的可靠性；
- 在缓存使用，进程重启后，数据丢失，即使有备用的节点解决高可用性，但是仍然不能解决缓存预热问题，因此不适用于数据可靠性要求高的业务；
- 高性能受限于单核CPU的处理能力（Redis是单线程机制），CPU为主要瓶颈，所以适合操作命令简单，排序、计算较少的场景。也可以考虑用Memcached替代。



### Redis多副本

Redis多副本，采用主从（replication）部署结构，相较于单副本而言最大的特点就是主从实例间数据实时同步，并且提供数据持久化和备份策略。主从实例部署在不同的物理服务器上，根据公司的基础环境配置，可以实现同时对外提供服务和读写分离策略。

**优点：**

- 高可靠性：一方面，采用双机主备架构，能够在主库出现故障时自动进行主备切换，从库提升为主库提供服务，保证服务平稳运行；另一方面，开启数据持久化功能和配置合理的备份策略，能有效的解决数据误操作和数据异常丢失的问题；
- 读写分离策略：从节点可以扩展主库节点的读能力，有效应对大并发量的读操作。

**缺点：**

- 故障恢复复杂，如果没有RedisHA系统（需要开发），当主库节点出现故障时，需要手动将一个从节点晋升为主节点，同时需要通知业务方变更配置，并且需要让其它从库节点去复制新主库节点，整个过程需要人为干预，比较繁琐；
- 主库的写能力受到单机的限制，可以考虑分片；
- 主库的存储能力受到单机的限制，可以考虑Pika；
- 原生复制的弊端在早期的版本中也会比较突出，如：Redis复制中断后，Slave会发起psync，此时如果同步不成功，则会进行全量同步，主库执行全量备份的同时可能会造成毫秒或秒级的卡顿；又由于COW机制，导致极端情况下的主库内存溢出，程序异常退出或宕机；主库节点生成备份文件导致服务器磁盘IO和CPU（压缩）资源消耗；发送数GB大小的备份文件导致服务器出口带宽暴增，阻塞请求，建议升级到最新版本。



### Redis主从模式

主从复制模式中包含一个主数据库实例（master）与一个或多个从数据库实例（slave），如下图





![img](https://pic4.zhimg.com/80/v2-8c5ff51b6aa9f933231e4c3f50cfdd6f_720w.jpg)





客户端可对主数据库进行读写操作，对从数据库进行读操作，主数据库写入的数据会实时自动同步给从数据库。

具体工作机制为：

- **slave启动后，向master发送SYNC命令**，master接收到SYNC命令后**通过bgsave保存快照**（即上文所介绍的RDB持久化），并使用缓冲区记录保存快照这段时间内执行的写命令
- master将保存的快照文件发送给slave，并继续记录执行的写命令
- slave接收到快照文件后，加载快照文件，载入数据
- master快照发送完后开始向slave发送缓冲区的写命令，slave接收命令并执行，完成复制初始化
- **此后master每次执行一个写命令都会同步发送给slave，保持master与slave之间数据的一致性**





### TBD Redis哨兵Sentinel





### Redis Cluster

通过Docker实现：

docker network create redis --subnet 172.28.0.0/16









### TBD Redis主从复制







### Redis实现分布式锁

[Redis：Redisson分布式锁的使用（推荐使用）](https://blog.csdn.net/chuanchengdabing/article/details/121210426)

分布式锁可以使用MySQL缓存、ZooKeeper、Redis缓存。

Redisson是架设在Redis基础上的一个Java驻内存数据网格（In-Memory Data Grid）。
Redisson在**基于NIO的Netty框架**上，生产环境使用分布式锁。

```xml
<dependency>
    <groupId>org.redisson</groupId>
    <artifactId>redisson</artifactId>
    <version>2.7.0</version>
</dependency>
```

配置redisson管理类：

```java
public class RedissonManager {
  private static Config config = new Config();
  //声明redisso对象
  private static Redisson redisson = null;
  
   //实例化redisson
	static{
	  config.useClusterServers()
	  // 集群状态扫描间隔时间，单位是毫秒
	 .setScanInterval(2000)
	  //cluster方式至少6个节点(3主3从，3主做sharding，3从用来保证主宕机后可以高可用)
	 .addNodeAddress("redis://127.0.0.1:6379" )
	 .addNodeAddress("redis://127.0.0.1:6380")
	 .addNodeAddress("redis://127.0.0.1:6381")
	 .addNodeAddress("redis://127.0.0.1:6382")
	 .addNodeAddress("redis://127.0.0.1:6383")
	 .addNodeAddress("redis://127.0.0.1:6384");
	 
	  //得到redisson对象
	  redisson = (Redisson) Redisson.create(config);
	}
	
	  //获取redisson对象的方法
	  public static Redisson getRedisson(){
	    return redisson;
	 }
}
————————————————
版权声明：本文为CSDN博主「穿城大饼」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/chuanchengdabing/article/details/121210426
```

加锁与释放锁：

```java
public class DistributedRedisLock {
  //从配置类中获取redisson对象
  private static Redisson redisson = RedissonManager.getRedisson();
  private static final String LOCK_TITLE = "redisLock_";
  
  //加锁
  public static boolean acquire(String lockName){
    //声明key对象
    String key = LOCK_TITLE + lockName;
    //获取锁对象
    RLock mylock = redisson.getLock(key);
    //加锁，并且设置锁过期时间3秒，防止死锁的产生  uuid+threadId
    mylock.lock(2,3,TimeUtil.SECOND);
    //加锁成功
    return  true;
  }
  
  //锁的释放
  public static void release(String lockName){
    //必须是和加锁时的同一个key
    String key = LOCK_TITLE + lockName;
    //获取所对象
    RLock mylock = redisson.getLock(key);
    //释放锁（解锁）
    mylock.unlock();
  
  }
}
```

具体业务逻辑中使用分布式锁：

```xml
public String discount() throws IOException{
    String key = "lock001";
    //加锁
    DistributedRedisLock.acquire(key);
    //执行具体业务逻辑
    dosomething
    //释放锁
    DistributedRedisLock.release(key);
    //返回结果
    return dosomething;
 }

```















## Spring Boot/Cloud + Redis

[SpringBoot整合Mysql、Redis_路上的追梦人的博客-CSDN博客_springboot配置mysql和redis](https://blog.csdn.net/Jiangtagong/article/details/122960915)

Redis的实际配置基于Mybatis-starter项目的MySQL服务。https://github.com/BGMer7/ORM-starter/tree/master/MyBatisPlus-starter



**application.properties**

```properties
# Redis配置项，以spring.redis为前缀
# 数据库索引（默认为0）
spring.redis.database=0
# 服务器地址
spring.redis.host=127.0.0.1
# 端口
spring.redis.port=6379
# 密码（默认为空，为空时不设置该属性）
spring.redis.password=
# 超时时间（毫秒）
spring.redis.timeout=30000
# 建议使用lettuce 可以换成jedis，spring默认集成lettuce
spring.redis.client-type=lettuce
# 如果使用jedis客户端，则下面定义的内容需要将lettuce换成jedis
# 连接池最大连接数（使用负值表示没有限制）
spring.redis.lettuce.pool.max-active=10
# 最大阻塞等待时间（使用负值表示没有限制）
spring.redis.lettuce.pool.max-wait=-2
# 最大空闲连接数
spring.redis.lettuce.pool.max-idle=10
# 最小空闲连接
spring.redis.lettuce.pool.min-idle=0
# mysql 配置
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/java_mysql?serverTimezone=Asia/Shanghai
spring.datasource.username=root
spring.datasource.password=123456
mybatis.mapper-locations=classpath:mapper/*.xml
# 端口参数
server.port=7010

```



**pom.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.7.0</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.gatsby</groupId>
    <artifactId>Redis-starter</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>Redis-starter</name>
    <description>Demo project for Spring Boot</description>
    <properties>
        <java.version>1.8</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>

        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.2.2</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.19</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>

```



**RedisConfig.java**

spring中为redis定义了专门的RedisAutoConfiguration自动配置类
 * 这个类在 package org.springframework.boot.autoconfigure.data.redis 中
 * 其中定义创建泛型为<Object, Object>的RedisTemplate对象。
 * 为了允许自定义配置Bean，自动配置类使用@ConditionalOnMissingBean注解，
 * 表示当其他地方定义一个RedisTemplate的Bean时，会替代自动配置中的结果

```java
package com.gatsby.redisstarter.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.lettuce.LettuceConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.GenericJackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.StringRedisSerializer;

import java.io.Serializable;

/**
 * @author -- gatsby
 * @date -- 2022/6/15
 * @description --
 * spring中为redis定义了专门的RedisAutoConfiguration自动配置类
 * 这个类在 package org.springframework.boot.autoconfigure.data.redis 中
 * 其中定义创建泛型为<Object, Object>的RedisTemplate对象。
 * 为了允许自定义配置Bean，自动配置类使用@ConditionalOnMissingBean注解，
 * 表示当其他地方定义一个RedisTemplate的Bean时，会替代自动配置中的结果。
 */


// 自定义RedisConfig
@Configuration
public class RedisConfig {

    @Bean
    public RedisTemplate<String, Serializable> redisTemplate(LettuceConnectionFactory lettuceConnectionFactory) {
        RedisTemplate<String, Serializable> redisTemplate = new RedisTemplate<>();
        // StringRedisSerializer：序列化为String
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        // GenericJackson2JsonRedisSerializer：
        // 序列化为JSON,同时在json中加入@class属性，类的全路径包名，方便反系列化
        redisTemplate.setValueSerializer(new GenericJackson2JsonRedisSerializer());
        redisTemplate.setHashKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashValueSerializer(new GenericJackson2JsonRedisSerializer());
        //设置连接工厂
        redisTemplate.setConnectionFactory(lettuceConnectionFactory);
        return redisTemplate;
    }
}

```



**RedisUtil.java**

```java
package com.gatsby.redisstarter.util;

import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Component;
import org.springframework.util.CollectionUtils;

import javax.annotation.Resource;
import java.util.Collection;
import java.util.List;
import java.util.Map;
import java.util.Set;
import java.util.concurrent.TimeUnit;

/**
 * @author -- gatsby
 * @date -- 2022/6/15
 * @description --
 */


@Component
public class RedisUtil {
    @Resource
    private RedisTemplate<String, Object> redisTemplate;

    public void setRedisTemplate(RedisTemplate<String, Object> redisTemplate) {
        this.redisTemplate = redisTemplate;
    }

    // 指定缓存失效时间
    public boolean expire(String key, long time) {
        try {
            if (time > 0) {
                redisTemplate.expire(key, time, TimeUnit.SECONDS);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    // 根据key获取过期时间
    public long getExpire(String key) {
        return redisTemplate.getExpire(key, TimeUnit.SECONDS);
    }

    // 判断key是否存在
    public boolean hasKey(String key) {
        try {
            return redisTemplate.hasKey(key);
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    // 删除缓存
    public void del(String... key) {
        if (key != null && key.length > 0) {
            if (key.length == 1) {
                redisTemplate.delete(key[0]);
            } else {
                redisTemplate.delete(
                        (Collection<String>) CollectionUtils.arrayToList(key));
            }
        }
    }

    // 普通缓存获取
    public Object get(String key) {
        return key == null ? null : redisTemplate.opsForValue().get(key);
    }

    // 普通缓存放入
    public boolean set(String key, Object value) {
        try {
            redisTemplate.opsForValue().set(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }

    }

    // 普通缓存放入并设置时间
    public boolean set(String key, Object value, long time) {
        try {
            if (time > 0) {
                redisTemplate.opsForValue()
                        .set(key, value, time, TimeUnit.SECONDS);
            } else {
                set(key, value);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    // 递增
    public long incr(String key, long delta) {
        if (delta < 0) {
            throw new RuntimeException("递增因子必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, delta);
    }


    // 递减
    public long decr(String key, long delta) {
        if (delta < 0) {
            throw new RuntimeException("递减因子必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, -delta);
    }


    // HashGet
    public Object hget(String key, String item) {
        return redisTemplate.opsForHash().get(key, item);
    }

    // 获取hashKey对应的所有键值
    public Map<Object, Object> hmget(String key) {
        return redisTemplate.opsForHash().entries(key);
    }


    // HashSet
    public boolean hmset(String key, Map<String, Object> map) {
        try {
            redisTemplate.opsForHash().putAll(key, map);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    // HashSet 并设置时间
    public boolean hmset(String key, Map<String, Object> map, long time) {
        try {
            redisTemplate.opsForHash().putAll(key, map);
            if (time > 0) {
                expire(key, time);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    // 向一张hash表中放入数据,如果不存在将创建
    public boolean hset(String key, String item, Object value) {
        try {
            redisTemplate.opsForHash().put(key, item, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    // 向一张hash表中放入数据,如果不存在将创建
    public boolean hset(String key, String item, Object value, long time) {
        try {
            redisTemplate.opsForHash().put(key, item, value);
            if (time > 0) {
                expire(key, time);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    // 删除hash表中的值
    public void hdel(String key, Object... item) {
        redisTemplate.opsForHash().delete(key, item);
    }

    // 判断hash表中是否有该项的值
    public boolean hHasKey(String key, String item) {
        return redisTemplate.opsForHash().hasKey(key, item);
    }

    // hash递增 如果不存在,就会创建一个 并把新增后的值返回
    public double hincr(String key, String item, double by) {
        return redisTemplate.opsForHash().increment(key, item, by);
    }

    // hash递减
    public double hdecr(String key, String item, double by) {
        return redisTemplate.opsForHash().increment(key, item, -by);
    }


    // 根据key获取Set中的所有值
    public Set<Object> sGet(String key) {
        try {
            return redisTemplate.opsForSet().members(key);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }

    // 根据value从一个set中查询,是否存在
    public boolean sHasKey(String key, Object value) {
        try {
            return redisTemplate.opsForSet().isMember(key, value);
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    // 将数据放入set缓存
    public long sSet(String key, Object... values) {
        try {
            return redisTemplate.opsForSet().add(key, values);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }

    // 将set数据放入缓存
    public long sSetAndTime(String key, long time, Object... values) {
        try {
            Long count = redisTemplate.opsForSet().add(key, values);
            if (time > 0) expire(key, time);
            return count;
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }


    // 获取set缓存的长度
    public long sGetSetSize(String key) {
        try {
            return redisTemplate.opsForSet().size(key);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }

    // 移除值为value的
    public long setRemove(String key, Object... values) {
        try {
            Long count = redisTemplate.opsForSet().remove(key, values);
            return count;
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }


    // 获取list缓存的内容
    public List<Object> lGet(String key, long start, long end) {
        try {
            return redisTemplate.opsForList().range(key, start, end);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }

    // 获取list缓存的长度
    public long lGetListSize(String key) {
        try {
            return redisTemplate.opsForList().size(key);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }

    // 通过索引 获取list中的值
    public Object lGetIndex(String key, long index) {
        try {
            return redisTemplate.opsForList().index(key, index);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }

    // 将list放入缓存
    public boolean lSet(String key, Object value) {
        try {
            redisTemplate.opsForList().rightPush(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    // 将list放入缓存
    public boolean lSet(String key, Object value, long time) {
        try {
            redisTemplate.opsForList().rightPush(key, value);
            if (time > 0) expire(key, time);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    // 将list放入缓存
    public boolean lSet(String key, List<Object> value) {
        try {
            redisTemplate.opsForList().rightPushAll(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    // 将list放入缓存
    public boolean lSet(String key, List<Object> value, long time) {
        try {
            redisTemplate.opsForList().rightPushAll(key, value);
            if (time > 0) expire(key, time);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    // 根据索引修改list中的某条数据
    public boolean lUpdateIndex(String key, long index, Object value) {
        try {
            redisTemplate.opsForList().set(key, index, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    // 移除N个值为value
    public long lRemove(String key, long count, Object value) {
        try {
            Long remove = redisTemplate.opsForList().remove(key, count, value);
            return remove;
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }
}
```



**RedisService.java**

```java
package com.gatsby.redisstarter.service;

import com.gatsby.redisstarter.entity.Employee;

import java.util.List;

/**
 * @author -- gatsby
 * @date -- 2022/6/15
 * @description --
 */


public interface EmployeeRedisService {
    List<Employee> getAllEmployees();

    void updateEmployeeSalary();

    void deleteAllUserKey();

    boolean hasKey(String key);
}
```



**RedisServiceImpls.java**

```java
package com.gatsby.redisstarter.service.impl;

import com.gatsby.redisstarter.constant.RedisConstant;
import com.gatsby.redisstarter.entity.Employee;
import com.gatsby.redisstarter.mapper.EmployeeMapper;
import com.gatsby.redisstarter.service.EmployeeRedisService;
import com.gatsby.redisstarter.service.EmployeeService;
import com.gatsby.redisstarter.util.RedisUtil;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Service;
import org.springframework.util.CollectionUtils;

import javax.annotation.Resource;
import java.util.List;

/**
 * @author -- gatsby
 * @date -- 2022/6/15
 * @description --
 */

@Slf4j
@Service
public class EmployeeRedisServiceImpl implements EmployeeRedisService {
    @Resource
    private RedisUtil redisUtil;

    @Resource
    private EmployeeMapper employeeMapper;

    @Override
    public List<Employee> getAllEmployees() {
        log.info("get all employee");
        List<Employee> employeeList = (List<Employee>) redisUtil.get(RedisConstant.ALL_USER_KEY);
        if (CollectionUtils.isEmpty(employeeList)) {
            employeeList = employeeMapper.queryAll();
            redisUtil.set(RedisConstant.ALL_USER_KEY, employeeList, 30);
        }
        return employeeList;
    }

    @Override
    public void updateEmployeeSalary() {
        log.info("update employee salary");
    }

    @Override
    public void deleteAllUserKey() {
        redisUtil.del(RedisConstant.ALL_USER_KEY);
    }

    @Override
    public boolean hasKey(String key) {
        return redisUtil.hasKey(key);
    }
}
```



**RedisController.java**

```java
package com.gatsby.redisstarter.controller;

import com.gatsby.redisstarter.entity.Employee;
import com.gatsby.redisstarter.service.EmployeeRedisService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;
import java.util.List;

/**
 * @author -- gatsby
 * @date -- 2022/6/15
 * @description --
 */


@Slf4j
@RestController
@RequestMapping("/redis")
public class EmployeeRedisController {
    private EmployeeRedisService employeeRedisService;

    public EmployeeRedisController(EmployeeRedisService employeeRedisService) {
        this.employeeRedisService = employeeRedisService;
    }

    @GetMapping("/query-all")
    public List<Employee> queryAll() {
        return employeeRedisService.getAllEmployees();
    }

    @GetMapping("/delete-all-user-key")
    public void deleteAllUserKey() {
        employeeRedisService.deleteAllUserKey();
    }

    @GetMapping("/has-key")
    public boolean deleteAllUserKey(@RequestParam String key) {
        return employeeRedisService.hasKey(key);
    }
}
```



































