# Message Queue

### 六个场景对比适合RabbitMQ还是Kafka

#### 消息的顺序

有这样一个需求：当订单状态变化的时候，把订单状态变化的消息发送给所有关心订单变化的系统。

1. 订单会有创建成功、待付款、已支付、已发货的状态，状态之间是单向流动的。

   <img src="https://pic1.zhimg.com/v2-9ff9beb78cfea1f2d402abaeb83d19f6_r.jpg?source=1940ef5c" alt="img" style="zoom:50%;" />

2. 更新了状态之后，现在我们把订单状态变化消息要发送给所有关心订单状态的系统上去，实现方式就是用消息队列。

   <img src="https://pic1.zhimg.com/v2-98d5385101e2922e302141b53186b904_r.jpg?source=1940ef5c" alt="img" style="zoom:50%;" />

3. 在这种业务下，我们最需要的其实是两点：

   1. 消息的顺序：对于同一笔订单而言，状态的变化都是有严格的先后顺序的。
   2. 吞吐量：像订单的业务，我们自然希望越多越好，订单越多，吞吐量就越多。

4. 在这种场景下，RabbitMQ的做法是，**首先，对于发消息，并广播给多个消费者这种情况，RabbitMQ 会为每个消费者建立一个对应的队列。也就是说，如果有 10 个消费者，RabbitMQ 会建立 10 个对应的队列。然后，当一条消息被发出后，RabbitMQ 会把这条消息复制 10 份放到这 10 个队列里。**<img src="https://pic1.zhimg.com/v2-6abd470247cd617f59a7099d513aa602_r.jpg?source=1940ef5c" alt="img" style="zoom:50%;" />

   当 RabbitMQ 把消息放入到对应的队列后，我们紧接着面临的问题就是，我们应该在系统内部启动多少线程去从消息队列中获取消息。RabbitMQ 有这么个特性，它在官方文档就声明了自己是不保证多线程消费同一个队列的消息，一定保证顺序的。而不保证的原因，是因为多线程时，当一个线程消费消息报错的时候，RabbitMQ 会把消费失败的消息再入队，此时就可能出现乱序的情况。<img src="https://pic1.zhimg.com/v2-f6e27271afcd729fc494e8fc7debd89e_r.jpg?source=1940ef5c" alt="img" style="zoom: 67%;" />

   T0 时刻，队列中有四条消息 A1、B1、B2、A2。其中 A1、A2 表示订单 A 的两个状态：待付款、已付款。B1、B2 也同理，是订单 B 的待付款、已付款。

   到了 T1 时刻，消息 A1 被线程 1 收到，消息 B1 被线程 2 收到。此时，一切都还正常。

   到了 T3 时刻，B1 消费出错了，同时呢，由于线程 1 处理速度快，又从消息队列中获取到了 B2。此时，问题开始出现。

   到了 T4 时刻，由于 RabbitMQ 线程消费出错，可以把消息重新入队的特性，此时 B1 会被重新放到队列头部。所以，如果不凑巧，线程 1 获取到了 B1，就出现了乱序情况，B2 状态明明是 B1 的后续状态，却被提前处理了。

   所以，可以看到了，这个场景用 RabbitMQ，出现了三个问题：

   1. 为了实现发布订阅功能，从而使用的消息复制，会降低性能并耗费更多资源
   2. 多个消费者无法严格保证消息顺序
   3. 大量的订单集中在一个队列，吞吐量受到了限制

5. 在这种场景下，Kafka在这几点的场景下会比RabbitMQ好很多。

   1. 首先，Kafka的发布订阅并不是通过复制消息实现的。Kafka的发布订阅是消费者直接去获取被Kafka保存在日志文件中的消息，所以无论多少消费者，都是通过主动的方式去获取消息在文件中的位置。
   2. 其次，Kafka不会出现消费者出错之后，将消息重新入队的问题。
   3. 最后，Kafka 可以对订单进行分区，把不同订单分到多个分区中保存，这样，吞吐量能更好。

   所以在这种场景下，Kafka要更加合适。



#### 消息的匹配

假设存在一个营销系统，这套系统中有一个很显著的特点，就是非常复杂非常灵活的匹配规则。

比如，要根据推广内容去匹配不同的方式做宣传。又比如，要根据不同的活动去匹配不同的渠道去做分发。在这套系统中，存在很多种匹配规则。

<img src="https://pic1.zhimg.com/v2-d467d9e2759cc49fcb1d09cab3ad6fe3_r.jpg?source=1940ef5c" alt="img" style="zoom:50%;" />

首先，先看看 RabbitMQ，你会发现 RabbitMQ 是允许在消息中添加 routing_key 或者自定义消息头，然后通过一些特殊的 Exchange，很简单的就实现了消息匹配分发。开发几乎不用成本。

而 Kafka 呢？如果你要实现消息匹配，开发成本高多了。

首先，通过简单的配置去自动匹配和分发到合适的消费者端这件事是不可能的。

其次，消费者端必须先把所有消息不管需要不需要，都取出来。然后，再根据业务需求，自己去实现各种精准和模糊匹配。可能因为过度的复杂性，还要引入规则引擎。

所以在这种场景下，使用Kafka比RabbitMQ要复杂很多。





#### 消息的超时

在电商体系中，有一个很重要的需求是：下单十五分钟之后是否完成了支付，如果没有支付就取消订单，释放资源。

首先解释一下为什么这么个定时任务需要用到消息队列。

在普通的SOA或者微服务架构中，可能就直接一个定时任务就可以结束，但是在微服务架构中，很多的服务都需要获得这个支付结果的状态。如果每一个服务都要单独实现一套自己的定时逻辑，设计的复杂度会大很多。

在这种场景中，我们往往会做一层抽象，把要执行的任务封装成消息，当时间到了，直接将消息丢尽消息队列中，消息的订阅者收到消息之后，可以直接执行这个消息。**这种希望把消息延迟一会再执行的场景叫做延迟队列。**

对于延迟队列这种场景，我们就会在创建订单的时候，同时将一个包含了执行任务信息的消息加到延迟队列中，指定15分钟之后，订阅了这个队列的各个消费者可以收到这个消息，随后各个消费者所在的系统就可以执行相关的检查订单的任务了。

![img](https://pic1.zhimg.com/v2-4737535155eb366b7f91f8d8ef85183c_r.jpg?source=1940ef5c)

RabbitMQ在这种情况中的分析：

1. RabbitMQ首先自带计时。消息中有一个TTL字段，这个字段用于标记该消息在队列中存放的时间，如果超过TTL会被移动到死信队列。所以，延迟队列 RabbitMQ 最简单的实现方式就是设置 TTL，然后一个消费者去监听死信队列，当消息超时了，监听死信队列的消费者就收到消息了。

2. 但是假设队列中有多条信息，并且过期时间不相同，

   > 例如：我们先往队列放入一条过期时间是 10 秒的 A 消息，再放入一条过期时间是 5 秒的 B 消息。 那么问题来了，B 消息会先于 A 消息进入死信队列吗？
   >
   > 答案是否定的。B 消息会优先遵守队列的先进先出规则，在 A 消息过期后，和其一起进入死信队列被消费者消费。
   >
   > 在 RabbitMQ 的 3.5.8 版本以后，官方推荐的 rabbitmq delayed message exchange 插件可以解决这个问题。
   >
   > - 用了这个插件，我们在发送消息的时候，把消息发往一个特殊的 Exchange。
   > - 同时，在消息头里指定要延迟的时间。
   > - 收到消息的 Exchange 并不会立即把消息放到队列里，而是在消息延迟时间到达后，才会把消息放入。

<img src="https://pic1.zhimg.com/v2-3386e85e7dddbeeaf4ad80452fc55d25_r.jpg?source=1940ef5c" alt="img" style="zoom:50%;" />

Kafka本身没有自带延迟队列，自定义实现延迟队列很麻烦。



#### 消息的保持

涉及到消息持久化的时候，基本都是Kafka更占优势，

在微服务里，事件溯源是经常用到的。如果想用消息队列实现，一般是把事件当成消息，依次发送到消息队列中。

事件溯源有个最经典的场景，就是事件的重放。简单来讲就是把系统中某段时间发生的事件依次取出来再处理。而且，根据业务场景不同，这些事件重放很可能不是一次，更可能是重复 N 次。

假设，我们现在需要一批在线事件重放，去排查一些问题。

**RabbitMQ 此时就真的不行了，因为消息被人取出来就被删除了**。想再次被重复消费？对不起。

**而 Kafka 呢，消息会被持久化一个专门的日志文件里**。不会因为被消费了就被删除。

所以，对消息不离不弃的 Kafka 相对用过就抛的 RabbitMQ，请选择 Kafka。



#### 消息的错误处理

很多时候，在做记录数据相关业务的时候，Kafka 一般是不二选择。不过，有时候在记录数据吞吐量不大时，我自己倒是更喜欢用 RabbitMQ。

原因就是 Kafka 有一个我很不喜欢的[设计原则](https://www.zhihu.com/search?q=设计原则&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2335229719})：

当单个分区中的消息一旦出现消费失败，就只能停止而不是跳过这条失败的消息继续消费后面的消息。即不允许消息空洞。

只要消息出现失败，不管是 Kafka 自身消息格式的损坏，还是消费者处理出现异常，是不允许跳过消费失败的消息继续往后消费的。

所以，在**数据统计不要求十分精确**的场景下选了 Kafka，一旦出现了消息消费问题，就会发生项目不可用的情况。这真是徒增烦恼。

而 RabbitMQ 呢，它由于会在消息出问题或者消费错误的时候，可以重新入队或者移动消息到死信队列，继续消费后面的，会省心很多。

坏消息就像群众中的坏蛋那样，Kafka 处理这种坏蛋太过残暴，非得把坏蛋揪出来不行。相对来说，RabbitMQ 就温柔多了，群众是群众，坏蛋是坏蛋，分开处理嘛。



#### 消息的吞吐量

**Kafka 是每秒几十万条消息吞吐，而 RabbitMQ 的吞吐量是每秒几万条消息，Kafka的吞吐量更大。**







## RabbitMQ架构

[RabbitMQ](https://so.csdn.net/so/search?q=RabbitMQ&spm=1001.2101.3001.7020)是采用Erlang语言实现AMQP（Advanced Message Queuing Protocol,高级消息队列协议）的消息中间件。































## Kafka

**Kafka** 是一个消息系统，原本开发自 LinkedIn，用作 LinkedIn 的活动流（Activity Stream）和运营数据处理管道（Pipeline）的基础。现在它已被作为多种类型的数据管道和消息系统使用。

### **Kafka Intro**

Kafka 是一种分布式的，基于发布 / 订阅的消息系统。主要设计目标如下：

- 以时间复杂度为 O(1) 的方式提供消息持久化能力，即使对 TB 级以上数据也能保证常数时间复杂度的访问性能。
- 高吞吐率。即使在非常廉价的商用机器上也能做到单机支持每秒 100K 条以上消息的传输。
- 支持 Kafka Server 间的消息分区，及分布式消费，同时保证每个 Partition 内的消息顺序传输。
- 同时支持离线数据处理和实时数据处理。
- Scale out：支持在线水平扩展。



### Kafka Concepts

#### Producer

对于 Kafka 来说客户端有两种基本类型：**生产者（Producer）**和**消费者（Consumer）**。除此之外，还有用来做数据集成的 Kafka Connect API 和流式处理的 Kafka Streams 等高阶客户端，但这些高阶客户端底层仍然是生产者和消费者API，它们只不过是在上层做了封装。

Producer：生产者，也就是发送消息的一方。生产者负责创建消息，然后将其投递到Kafka中。

#### Consumer

Consumer：消费者，也就是接收消息的一方。消费者连接到Kafka上并接收消息，进而进行相应的业务逻辑处理。



#### Topic

Kafka中的消息以主题为单位进行归类，生产者负责将消息发送到特定的主题（发送到Kafka集群中的每一条消息都要指定一个主题），而消费者负责订阅主题并进行消费。

- 每个 `topic` 是一个发布-订阅消息的通道。它可以看作是一个队列，用于存储生产者发送的消息。
- 多个 `partition` 可以组成一个 `topic`，每个 `partition` 是一个独立的有序消息队列。
- 每个 `topic` 可以有不同的分区，这些分区可以分布在不同的 Kafka 服务器（broker）上。

#### Partition

每个 `topic` 可以有多个 `partition`，用于将消息分散存储在不同的队列中。

- `partition` 中的消息是有序的，它们按照生产者发送的顺序存储。
- `partition` 中的消息是独立的，一个 `partition` 中的消息不会影响其他 `partition` 中的消息。
- `partition` 的数量可以通过创建 `topic` 时指定，或者在创建后通过 Kafka 管理工具修改。

主题是一个逻辑上的概念，它还可以细分为多个分区，一个分区只属于单个主题，很多时候也会把分区称为主题分区（Topic-Partition）。同一主题下的不同分区包含的消息是不同的，分区在存储层面可以看作一个可追加的日志（Log）文件，消息在被追加到分区日志文件的时候都会分配一个特定的偏移量（offset）。offset是消息在分区中的唯一标识，Kafka通过它来保证消息在分区内的顺序性，不过offset并不跨越分区，也就是说，Kafka保证的是分区有序而不是主题有序。

如图 1-2 所示，主题中有 4 个分区，消息被顺序追加到每个分区日志文件的尾部。Kafka中的分区可以分布在不同的服务器（broker）上，也就是说，一个主题可以横跨多个broker，以此来提供比单个broker更强大的性能。

![image-20240423104810852](C:\Users\Gatsby\AppData\Roaming\Typora\typora-user-images\image-20240423104810852.png)

每一条消息被发送到broker之前，会根据分区规则选择存储到哪个具体的分区。如果分区规则设定得合理，所有的消息都可以均匀地分配到不同的分区中。如果一个主题只对应一个文件，那么这个文件所在的机器 I/O 将会成为这个主题的性能瓶颈，而分区解决了这个问题。在创建主题的时候可以通过指定的参数来设置分区的个数，当然也可以在主题创建完成之后去修改分区的数量，通过增加分区的数量可以实现水平扩展。

> Topic和Partition的关系：
>
> - 一个 `topic` 可以有多个 `partition`，每个 `partition` 是 `topic` 的一个物理分割。
> - `partition` 存储在不同的 `broker` 上，每个 `broker` 存储 `topic` 的一个或多个 `partition`。
> - 生产者将消息发送到 `topic`，而消费者可以从 `topic` 的一个或多个 `partition` 中读取消息。
>
> 通过将一个 `topic` 拆分为多个 `partition`，Kafka 能够提供更高的吞吐量，因为生产者和消费者可以并行处理不同的 `partition`。此外，`partition` 的设计还允许 Kafka 实现容错和高可用性，因为即使某些 `broker` 发生故障，其他 `broker` 仍然可以处理 `topic` 的其他 `partition`。



在 Kafka 中，虽然每个 `partition` 内部的消息是有序的，但是 `topic` 中的不同 `partition` 之间并没有保证有序性。每个 `partition` 都是独立的队列，它们存储在不同的 `broker` 上，并且可以独立地进行读写操作。

Kafka 的设计哲学是允许 `topic` 中的不同 `partition` 之间消息的顺序性由应用逻辑来控制。以下是一些方法，可以帮助你在应用程序层面保证 `topic` 中不同 `partition` 之间的消息顺序：

1. **Key-Based Partitioning**：在生产者端，可以为消息指定一个键（key），这个键可以是消息内容的哈希值或者是一个顺序编号。Kafka 服务器会根据键的哈希值将消息发送到不同的 `partition`。如果生产者确保具有相同键的消息被发送到同一个 `partition`，那么这些消息在 `topic` 中的顺序性就可以得到保证。
2. **Consumer Group**：在消费者端，可以将多个消费者组织成一个消费者组。每个消费者组内的消费者可以订阅同一个 `topic` 的不同 `partition`。如果消费者组内的所有消费者都按顺序消费，那么它们可以看到整个 `topic` 中消息的顺序。
3. **事务性消息**：Kafka 支持事务性消息，即可以保证一系列消息作为一个原子性的操作来执行。这可以用于确保跨 `partition` 的消息有序性。
4. **应用逻辑控制**：在应用程序中，可以通过控制生产者和消费者的逻辑来保证不同 `partition` 之间的消息顺序。例如，生产者可以在发送消息之前检查其他 `partition` 中的消息顺序，或者消费者在消费消息时按照一定的顺序来处理不同 `partition` 中的消息。

需要注意的是，Kafka 并不提供内置的机制来保证 `topic` 中不同 `partition` 之间的消息顺序，这需要通过应用程序的逻辑来实现。如果你需要在不同 `partition` 之间保证消息的顺序性，那么你需要在生产者和消费者端都采取相应的措施。



#### Replica

Kafka 为分区引入了多副本（Replica）机制，通过增加副本数量可以提升容灾能力。同一分区的不同副本中保存的是相同的消息（在同一时刻，副本之间并非完全一样），副本之间是“一主多从”的关系，其中leader副本负责处理读写请求，follower副本只负责与leader副本的消息同步。副本处于不同的broker中，当leader副本出现故障时，从follower副本中重新选举新的leader副本对外提供服务。Kafka通过多副本机制实现了故障的自动转移，当Kafka集群中某个broker失效时仍然能保证服务可用。

如图1-3所示，Kafka集群中有4个broker，某个主题中有3个分区，且副本因子（即副本个数）也为3，如此每个分区便有1个leader副本和2个follower副本。生产者和消费者只与leader副本进行交互，而follower副本只负责消息的同步，很多时候follower副本中的消息相对leader副本而言会有一定的滞后。

![image-20240423111824095](C:\Users\Gatsby\AppData\Roaming\Typora\typora-user-images\image-20240423111824095.png)



- 分区中的所有副本统称为AR（Assigned Replicas）。
- 所有与leader副本保持一定程度同步的副本（包括leader副本在内）组成ISR（In-Sync Replicas），ISR集合是AR集合中的一个子集。
- 消息会先发送到leader副本，然后follower副本才能从leader副本中拉取消息进行同步，同步期间内follower副本相对于leader副本而言会有一定程度的滞后。前面所说的“一定程度的同步”是指可忍受的滞后范围，这个范围可以通过参数进行配置。
- 与leader副本同步滞后过多的副本（不包括leader副本）组成OSR（Out-of-Sync Replicas），由此可见，AR=ISR+OSR。
- 在正常情况下，所有的follower 副本都应该与 leader 副本保持一定程度的同步，即 AR=ISR，OSR集合为空。



#### Leader Follower

leader副本负责维护和跟踪ISR集合中所有follower副本的滞后状态，当follower副本落后太多或失效时，leader副本会把它从ISR集合中剔除。如果OSR集合中有follower副本“追上”了leader副本，那么leader副本会把它从OSR集合转移至ISR集合。默认情况下，当leader副本发生故障时，只有在ISR集合中的副本才有资格被选举为新的leader，而在OSR集合中的副本则没有任何机会（不过这个原则也可以通过修改相应的参数配置来改变）。



#### High Watermark

HW是High Watermark的缩写，俗称高水位，它标识了一个特定的消息偏移量（offset），消费者只能拉取到这个offset之前的消息。



#### LEO/LSO

![image-20240423141721547](C:\Users\Gatsby\AppData\Roaming\Typora\typora-user-images\image-20240423141721547.png)



如图 1-4 所示，它代表一个日志文件，这个日志文件中有 9 条消息，第一条消息的 offset（LogStartOffset）为0，最后一条消息的offset为8，offset为9的消息用虚线框表示，代表下一条待写入的消息。日志文件的HW为6，表示消费者只能拉取到offset在0至5之间的消息，而offset为6的消息对消费者而言是不可见的。

LEO是Log End Offset的缩写，它标识当前日志文件中下一条待写入消息的offset，图1-4中offset为9的位置即为当前日志文件的LEO，LEO的大小相当于当前日志分区中最后一条消息的offset值加1。分区ISR集合中的每个副本都会维护自身的LEO，而ISR集合中最小的LEO即为分区的HW，对消费者而言只能消费HW之前的消息。

> 注意要点：很多资料中误将图1-4中的offset为5的位置看作HW，而把offset为8的位置看作LEO，这显然是不对的。



![image-20240423143954438](C:\Users\Gatsby\AppData\Roaming\Typora\typora-user-images\image-20240423143954438.png)

在消息写入leader副本之后，follower副本会发送拉取请求来拉取消息3和消息4以进行消息同步。在同步过程中，不同的 follower 副本的同步效率也不尽相同。如图 1-7 所示，在某一时刻follower1完全跟上了leader副本而follower2只同步了消息3，如此leader副本的LEO为5，follower1的LEO为5，follower2的LEO为4，那么当前分区的HW取最小值4，此时消费者可以消费到offset为0至3之间的消息。

![image-20240423144028146](C:\Users\Gatsby\AppData\Roaming\Typora\typora-user-images\image-20240423144028146.png)



![image-20240423144101756](C:\Users\Gatsby\AppData\Roaming\Typora\typora-user-images\image-20240423144101756.png)



> 由此可见，Kafka 的复制机制既不是完全的同步复制，也不是单纯的异步复制。事实上，同步复制要求所有能工作的 follower 副本都复制完，这条消息才会被确认为已成功提交，这种复制方式极大地影响了性能。而在异步复制方式下，follower副本异步地从leader副本中复制数据，数据只要被leader副本写入就被认为已经成功提交。在这种情况下，如果follower副本都还没有复制完而落后于leader副本，突然leader副本宕机，则会造成数据丢失。Kafka使用的这种ISR的方式则有效地权衡了数据可靠性和性能之间的关系。



#### Broker

一个 Kafka 服务器也称为 Broker，它接受生产者发送的消息并存入磁盘；Broker  同时服务消费者拉取分区消息的请求，返回目前已经提交的消息。使用特定的机器硬件，一个 Broker  每秒可以处理成千上万的分区和百万量级的消息。

#### Cluster

若干个 Broker 组成一个集群（Cluster），其中集群内某个 Broker 会成为集群控制器（Cluster  Controller），它负责管理集群，包括分配分区到 Broker、监控 Broker 故障等。在集群内，一个分区由一个 Broker  负责，这个 Broker 也称为这个分区的 Leader；当然一个分区可以被复制到多个 Broker 上来实现冗余，这样当存在 Broker  故障时可以将其分区重新分配到其他 Broker 来负责。下图是一个样例：![img](https://pic2.zhimg.com/v2-9c8de1bed82a54799c4ef2cbfeedab61_r.jpg)

**Kafka 的一个关键性质是日志保留（retention）**，我们可以配置主题的消息保留策略，譬如只保留一段时间的日志或者只保留特定大小的日志。当超过这些限制时，老的消息会被删除。我们也可以针对某个主题单独设置消息过期策略，这样对于不同应用可以实现个性化。



#### 多集群

随着业务的发展，我们往往需要多集群，通常处于下面几个原因：

- 基于数据的隔离；
- 基于安全的隔离；
- 多数据中心（容灾）

当构建多个数据中心时，往往需要实现消息互通。举个例子，假如用户修改了个人资料，那么后续的请求无论被哪个数据中心处理，这个更新需要反映出来。又或者，多个数据中心的数据需要汇总到一个总控中心来做数据分析。

上面说的分区复制冗余机制只适用于同一个 Kafka 集群内部，对于多个 Kafka 集群消息同步可以使用 Kafka 提供的 MirrorMaker  工具。本质上来说，MirrorMaker 只是一个 Kafka  消费者和生产者，并使用一个队列连接起来而已。它从一个集群中消费消息，然后往另一个集群生产消息。







### kafka Install & Config

#### Kafka 版本演进





#### Zookeeper

windows环境下启动zookeeper

1. 安装ZooKeeper的第一步也是下载相应的安装包，安装包可以从官网中获得，示例中使用的安装包是zookeeper-3.4.12.tar.gz，同样将其复制到安装目录下，然后解压缩
2. 修改配置文件。将zoo_sample.cfg文件修改为zoo.cfg，并且配置其中的dataDir和dataLogDir。clientPort根据情况自己设定。
3. 在dataDir目录下创建一个myid文件，并写入一个数字，比如0。这就是服务器的编号。
4. Windows启动服务只需要双击zkServer.cmd。



#### Kafka start

1. 从官网下载安装包，解压缩。

2. 修改/conf/server.properties，修改listeners=PLAINTEXT://localhost:9092，log.dirs=D:/data/kafka

3. 如果是单机模式，已经可以启动服务。

   ```shell
   .\bin\windows\kafka-server-start.bat .\config\server.properties
   ```

   

#### Topic

```shell
.\windows\kafka-topics.bat --zookeeper localhost:2181 --create --topic topic-demo --replication-factor 1 --partitions 4
```



```shell
PS D:\kafka_2.13-2.7.2\bin> .\windows\kafka-topics.bat --zookeeper localhost:2181 --describe --topic topic-demo
Topic: topic-demo       PartitionCount: 4       ReplicationFactor: 1    Configs:
        Topic: topic-demo       Partition: 0    Leader: 0       Replicas: 0     Isr: 0
        Topic: topic-demo       Partition: 1    Leader: 0       Replicas: 0     Isr: 0
        Topic: topic-demo       Partition: 2    Leader: 0       Replicas: 0     Isr: 0
        Topic: topic-demo       Partition: 3    Leader: 0       Replicas: 0     Isr: 0
```







#### Consumer

```shell
PS D:\kafka_2.13-2.7.2\bin> .\windows\kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic topic-demo
```

出现文本框，开始等待接收生产者推送的数据。



#### Producer

```shell
PS D:\kafka_2.13-2.7.2\bin> .\windows\kafka-console-producer.bat --bootstrap-server localhost:9092 --topic topic-demo
```

出现文本输入框，输入的内容将会推送到消费者的文本框。

### 











