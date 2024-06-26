## Database

### 最基础的SQL命令

SELECT
DELETE
UPDATE
INSERT INTO
CREATE DATABASE
ALTER DATABASE
CREATE TABLE
DROP TABLE 
CREATE INDEX
DROP INDEX

SELECT DISTINCT 
SELECT * FROM table_name WHERE …

ORDER BY ASC|DESC
ORDER BY column_name1, column_name2

INSERT INTO TABLE_NAME(column_name1, column_name2…)
VALUE (‘value1’, ‘value2’, …)

UPDATE table_name



### 索引

[MySQL索引面试题（2021最新版） - SegmentFault 思否](https://segmentfault.com/a/1190000040177994?sort=votes)

**索引是一种特殊的文件(InnoDB数据表上的索引是表空间的一个组成部分)，它们包含着对数据表里所有记录的引用指针。**

索引是一种数据结构。数据库索引，是数据库管理系统中一个排序的数据结构，以协助快速查询、更新数据库表中数据。索引的实现通常使用B树及其变种B+树。更通俗的说，索引就相当于目录。为了方便查找书中的内容，通过对内容建立索引形成目录。而且索引是一个文件，它是要占据物理空间的。

索引是在存储引擎中实现的，也就是说不同的存储引擎，会使用不同的索引。MyISAM和InnoDB存储引擎：只支持BTREE索引，也就是说默认使用BTREE，不能够更换。MEMORY/HEAP存储引擎：支持HASH和BTREE索引。



### 索引的优点与缺点

**索引的优点**

- 可以大大加快检索的速度，这是创建索引的最主要的原因。

- 通过使用索引，可以在查询的过程中，使用优化隐藏器，提高系统的性能。

**索引的缺点**

- 创建索引和维护索引都需要花费时间，索引可以大大缩短查询的时间，但是会增加增删改的时间，因为多余的时间都要用来维护索引
- 索引也是文件，要占用存储空间



### 索引有哪些种类

普通索引：一个索引只包含单个列，一个表可以有多个单列索引

唯一索引：索引的值允许为空，但是索引值必须唯一

复合索引：多列值组成一个索引，专门用于组合搜索，其效率大于索引合并

聚集索引：并不是一种单独的索引类型，而是一种数据存储方式。具体细节取决于不同的实现，InnoDB的聚簇索引其实就是在同一个结构中保存了B-Tree索引(技术上来说是B+Tree)和数据行

非聚集索引：不是聚集索引就是非聚集索引



### 数据库索引的底层数据结构

[我以为我对数据库索引很了解，直到我遇到了阿里面试官 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/107487215)

1. 哈希

   哈希索引是基于哈希表实现的，对于每一行数据，存储引擎会对索引列通过哈希算法进行哈希计算得到哈希码，并且哈希算法要尽量保证不同的列值计算出的哈希码值是不同的，将哈希码的值作为哈希表的key值，将指向数据行的指针作为哈希表的value值。这样查找一个数据的时间复杂度就是o(1)，一般多用于精确查找。

2. B+树

   二叉树是有序的，所以是支持范围查询的。

   但是他的时间复杂度是O(log(N))，为了维持这个时间复杂度，更新的时间复杂度也得是O(log(N))，那就得保持这棵树是完全平衡二叉树了。索引也不只是在内存里面存储的，还是要落盘持久化的，如果数据多了，树高会很高，查询的成本就会随着树高的增加而增加。可以发现同样的元素，B树的表示要比完全平衡二叉树要“矮”，**原因在于B树中的一个节点可以存储多个元素**。

   同样的元素，B+树的表示要比B树要“胖”，原因在于**B+树中的非叶子节点会冗余一份在叶子节点中，并且叶子节点之间用指针相连**。

   最开始的Hash不支持范围查询，二叉树树高很高，只有B树跟B+有的一比。

   B树一个节点可以存储多个元素，相对于完全平衡二叉树整体的树高降低了，磁盘IO效率提高了。

   而B+树是B树的升级版，只是把非叶子节点冗余一下，这么做的好处是**为了提高范围查找的效率**。

   B+树具有有序性，并且所有的数据都存放在叶子节点，所以查找的效率非常高，并且支持排序和范围查找。



### 哈希冲突的解决方式

1. 开放地址法

   前提是散列表的长度大于等于所要存放的元素

   发生哈希冲突后，按照某一次序找到下一个空闲的单元，把冲突的元素放入。

   - 线性探查法：
     从发生冲突的单元开始探查，依次查看下一个单元是否为空，如果到了最后一个单元还是空，那么再从表首依次判断。如此执行直到碰到了空闲的单元或者已经探查完所有单元。

   - 平方探查法
     从发生冲突的单元加上1^2,2^2,3^2,...,n^2，直到遇到空闲的单元

   - 双散列函数探查法
     定义两个散列函数，分别为s1和s2，s1的算法和前面一致，s2取一个1～m-1之间并和m互为素数的数。s2作为步长。

2. 再哈希法

   用哈希出来的值，再次哈希出一个值用于插入。

3. 建立公共溢出区

   把哈希表分为公共表和溢出表，如果发生了溢出，溢出的数据全部放在溢出区。

4. 链地址法

   将哈希值相同的元素构成一个链表，head放在散列表中。一般链表长度超过了8就转为红黑树，长度少于6个就变为链表。 



### 为什么默认使用B+树索引结构，而不是B树

B树和B+树最主要的区别主要有两点：

- B树中的内部节点和叶子节点均存放键和值，而B+树的内部节点只有键没有值，叶子节点存放所有的键和值。
- B＋树的叶子节点是通过相连在一起的，方便顺序检索。
  两者的结构图如下。

B+树：

<img src="https://pic1.zhimg.com/v2-2a93f5ee5d6c1372cb449f40d9a82dfc_r.jpg" alt="preview" style="zoom:67%;" />

B树：

<img src="https://pic4.zhimg.com/v2-9d1dbadfd53ceb05b371375606c0c33b_r.jpg" alt="preview" style="zoom:67%;" />

- B树适合随机检索，而B+树对于顺序检索和随即检索都适用。
- B+树的空间利用率更高，因为B树每个节点要存储键和值，而B+树的内部节点只存储键，这样B+树的一个节点就可以存储更多的索引，从而使树的高度变低，减少了I/O次数，使得数据检索速度更快。
- B+树的叶子节点都是通过链表连接在一起的，所以范围查找，顺序查找更加方便。
- B+树的性能更加稳定，因为在B+树中，每次查询都是从根节点到叶子节点，而在B树中，要查询的值可能不在叶子节点，在内部节点就已经找到。

但是特殊情况下，如果是只有极个别常用的键值频繁访问，可以把这几个值放在离节点近的地方。这样提高的查询效率，但是普遍情况来说，还是B+树的性能更优秀。



### 聚簇索引和非聚簇索引

聚簇索引和非聚簇索引最主要的区别是**数据和索引是否分开存储**。



[数据库技术面经 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/128546246)

### 数据库事务

数据库事务Transaction是将数据库的操作作为一个整体，要么全部执行要么全不执行，是作为一个不可分割的工作单位。事务由事务开始到事务结束之间执行的全部数据库操作组成。

数据库的事务有三个特点：ACID(酸)

1. 原子性Atomicity：事务作为一个整体执行，要么全部都执行，要不全部都不执行。
2. 一致性Consistency：事务应该确保数据库的状态由一个一致状态转变为另一个一致状态。一致状态的含义是指数据库的完整性应该满足完整性约束。
3. 隔离性Isolation：多个事务并发执行的时候，一个事务的执行不应影响其他事务的执行。
4. 持久性Duration：已经被提交的事务对数据库的修改应该永久保存在数据库中。



### 事务的四个隔离级别

1. 读未提交：一个事务还未提交，其他的事务就已经可以看到它做的更改
2. 读已提交：绝大多数的数据库的默认的隔离级别，只有当一个事务提交了之后，其余的事务才可以读取到它的更改
3. 可重复读：MySQL的默认隔离级别，一个事务在启动和提交之间读取到的数据是一致的，在它未提交的时候，其他事务无法看到它做出的更改
4. 串行化：对于同一个记录，写会加写锁，读会加读锁，当读写锁冲突的时候，后面的事务必须要等待上一个事务结束才可以继续执行



### 脏读、幻读、重复读

脏读是指读取到其他事务没有提交的数据。

幻读是指在一个事务中，前后的数据读取总量不一致。

不可重复读指在一个事务中前后读取数据不一致，即读到其他事务已提交的数据，针对update操作



### 主键

主键就是一条记录中的唯一能标识的一条记录。

平时建表的时候都会为表加上主键，在某些关系数据库中，如果建表时不指定主键，数据库会拒绝建表的语句执行。事实上，一个加了主键的表，并不能称之为表。一个没有加主键的表，数据无序存放在磁盘上，一行一行很整齐地排列，反而能叫做表。如果给表加上了主键，那么表在磁盘上地存储结构就由整齐排列地顺序结构变成了树状结构，变成了平衡树。换句话说，就是这个表变成了 一个索引。这也就是聚集索引。



### 数据库范式

为了建立冗余较小，结构合理的数据库，设计数据库的时候必须遵循一定的规则。在关系型数据库中，这种规则称之为范式。范式是符合某一种设计要求的总结。要想设计一个结构合理的关系型数据库，必须满足一定的范式。

**第一范式：确保每列保持原子性**

如果数据库表中的所有字段值都是不可分解的原子数值，就说明该数据库表满足了第一范式。

**第二范式：确保表中的每列都和主键有关**

在第一范式的基础上需要保证数据库中的每一列都与主键有关，而不能至于主键的某一部分有关。也就是说一个数据库表中，只能保存一种数据，不可以把多种数据保存在同一张表中。

**第三范式：确保每一列都是与主键列直接相关，而不是间接相关**



### 数据库日志的类型和功能

错误日志、查询日志、慢查询日志、事务日志、二进制日志、中继日志。

错误日志：记录mysql实例运行中产生的错误或者警告信息，以及每次mysql实例启动和停止的时间，必须开启。

查询日志：记录mysql运行的所有sql操作，无论成功与否。

慢查询日志：记录超过指定时间显示查询结果的sql命令，只会记录执行成功的语句。

二进制日志：记录的是除了查询之外的所有命令。



### InnoDB和MyISAM

[MyISAM与InnoDB 的区别（9个不同点）_张花生的博客-CSDN博客_innodb和myisam的区别](https://blog.csdn.net/qq_35642036/article/details/82820178)

1. **InnoDB支持事务，MyISAM不支持，对于InnoDB每一条SQL语言都默认封装成事务，自动提交，这样会影响速度，所以最好把多条SQL语言放在begin和commit之间，组成一个事务；** 

2. InnoDB支持外键，而MyISAM不支持。

3. **InnoDB是聚集索引**，采用B+树，数据文件和主键索引绑在一起。必须要有主键，用主键索引的效率会很高，但是辅助索引需要两次查询，先查询到主键，然后再通过主键查询到数据，因此主键不应该过大，因为主键过大的话其他索引都会变的很大。**MyISAM是非聚集索引**，也是使用B+Tree作为索引结构，索引和数据文件是分离的，索引保存的是数据文件的指针。主键索引和辅助索引是独立的。

   ![img](https://img-blog.csdn.net/20180923094753230?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1NjQyMDM2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

   ![img](https://img-blog.csdn.net/20180923094753224?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1NjQyMDM2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

4. **InnoDB不保存表的具体行数，执行select count(\*) from table时需要全表扫描。而MyISAM用一个变量保存了整个表的行数，执行上述语句时只需要读出该变量即可，速度很快（注意不能加有任何WHERE条件）；**

5. **Innodb不支持全文索引，而MyISAM支持全文索引，在涉及全文索引领域的查询效率上MyISAM速度更快高；PS：5.7以后的InnoDB支持全文索引了**

6. **MyISAM表格可以被压缩后进行查询操作**

7. **InnoDB支持表、行(默认)级锁，而MyISAM支持表级锁**

8. **InnoDB表必须有唯一索引（如主键）（用户没有指定的话会自己找/生产一个隐藏列Row_id来充当默认主键），而MyISAM可以没有**

9. **Innodb存储文件有frm、ibd，而Myisam是frm、MYD、MYI**

   **Innodb：frm是表定义文件，ibd是数据文件**

   **Myisam：frm是表定义文件，myd是数据文件，myi是索引文件**

InnoDB和MyISAM各自适应的场景：

1. 如果需要支持事务操作，那么就只能选择InnoDB，如果不需要的话可以考虑MyISAM；
2. 如果表中的绝大多数都是读查询，可以考虑MyISAM，如果有大量写，选择InnoDB；
3. 系统崩溃的话，MyISAM需要大约一天时间来恢复，而InnoDB只需要几分钟；
4. 遇事不决选InnoDB。



### InnoDB

InnoDB的自增ID可以保证每次插入B+索引的时候B+都是从右边扩展，可以避免B+树的频繁合并和分裂。如果使用字符串主键和随机主键，会使得数据随即插入，效率比较低。

InnoDB的四大特性：

1. 插入缓冲(insert buffer)
2. 二次写(double write)
3. 自适应哈希索引(adaptive hash index)
4. 预读(read ahead)



### 乐观锁和悲观锁

悲观锁基于一种悲观的态度来防止一切数据冲突，他是以一种预防的态度，在修改之前就把数据全部锁住，然后再对数据进行读写，在锁释放之前不能对其进行操作。只有当前一个用户把锁释放掉，下一个用户才可以加锁，才可以操作。一般数据库的实现都是通过悲观锁的机制进行实现的。 // DB的行锁表锁页锁，适用于数据一致性要求比较高的场景

乐观锁拿数据的时候认为别的用户并不会修改这个数据，所以不会上锁。但是在更新或者提交的时候会和原来的数据进行对比，判断在此期间有没有人动过这个数据。// 版本号或者时间戳控制，适用于读多写少的场景

悲观锁的使用场景：一旦通过悲观锁锁定一个资源，那么其他需要操作该资源的使用方，只能等待直到锁被释放，好处在于可以减少并发，但是当并发量非常大的时候，由于锁消耗资源，并且可能锁定时间过长，容易导致系统性能下降，资源消耗严重。因此一般我们可以在并发量不是很大，并且出现并发情况导致的异常用户和系统都很难以接受的情况下，会选择悲观锁进行。

乐观锁的使用场景：资源提交冲突，其他使用方需要重新读取资源，会增加读的次数，但是可以面对高并发场景，前提是如果出现提交失败，用户是可以接受的。因此一般乐观锁只用在高并发、多读少写的场景。
 其中：GIT,SVN,CVS等代码版本控制管理器，就是一个乐观锁使用很好的场景，例如：A、B程序员，同时从SVN服务器上下载了code.html文件，当A完成提交后，此时B再提交，那么会报版本冲突，此时需要B进行版本处理合并后，再提交到服务器。这其实就是乐观锁的实现全过程。如果此时使用的是悲观锁，那么意味者所有程序员都必须一个一个等待操作提交完，才能访问文件，这是难以接受的。



### synchronized和CAS

[Java：CAS(乐观锁) - 简书 (jianshu.com)](https://www.jianshu.com/p/ae25eb3cfb5d)

- synchronized是悲观锁，这种线程一旦得到锁，其他需要锁的线程就挂起的情况就是悲观锁。

- CAS操作的就是乐观锁，每次不加锁而是假设没有冲突而去完成某项操作，如果因为冲突失败就重试，直到成功为止。

首先是一段线程不安全的代码：

```java
 private static int count = 0;

    public static void main(String[] args) {
        for (int i = 0; i < 2; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    try {
                        Thread.sleep(10);
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                    //每个线程让count自增100次
                    for (int i = 0; i < 100; i++) {
                        count++;
                    }
                }
            }).start();
        }

        try{
            Thread.sleep(2000);
        }catch (Exception e){
            e.printStackTrace();
        }
        System.out.println(count);
    }
```

因为这里是全局变量，所以每次操作没有锁上的话，会造成结果的count最终数值不是200。

我们可以使用synchronized同步锁，只需要在count++的地方加上同步锁。

```java
private static int count = 0;

    public static void main(String[] args) {
        for (int i = 0; i < 2; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    try {
                        Thread.sleep(10);
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                    //每个线程让count自增100次
                    for (int i = 0; i < 100; i++) {
                        synchronized (ThreadCas.class){
                            count++;
                        }
                    }
                }
            }).start();
        }

        try{
            Thread.sleep(2000);
        }catch (Exception e){
            e.printStackTrace();
        }
        System.out.println(count);
    }
```

加了同步锁之后，count自增的操作变成了原子性操作，所以最终的输出一定是count=200，代码实现了线程安全。

但是加上锁之后，虽然确保了线程的安全，但是性能上不是最优的解，synchronized会让没有得到资源的线程进入blocked状态，而在得到资源之后进入runnable状态，这个过程中涉及到操作系统用户模式和内核模式的转换，会产生一定的开销。

那么就可以采用原子操作，在java.util.concurrent.atomic包下，一系列以Atomic开头的包装类。例如AtomicInteger、AtomicBoolean、AtomicLong。

```java

    private static AtomicInteger count = new AtomicInteger(0);

    public static void main(String[] args) {
        for (int i = 0; i < 2; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    try {
                        Thread.sleep(10);
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                    //每个线程让count自增100次
                    for (int i = 0; i < 100; i++) {
                        count.incrementAndGet();
                    }
                }
            }).start();
        }

        try{
            Thread.sleep(2000);
        }catch (Exception e){
            e.printStackTrace();
        }
        System.out.println(count);
    }
```

使用AtomicInteger之后，最终的输出结果同样可以保证是200。并且在某些情况下，代码的性能会比Synchronized更好。

Atomic的底层实现原理就是CAS，也即campare and swap.

CAS机制当中使用了3个基本操作数：内存地址V，旧的预期值A，要修改的新值B。

更新一个变量的时候，只有当变量的预期值A和内存地址V当中的实际值相同时，才会将内存地址V对应的值修改为B。

CAS的缺点：

1.CPU开销较大
 在并发量比较高的情况下，如果许多线程反复尝试更新某一个变量，却又一直更新不成功，循环往复，会给CPU带来很大的压力。

2.不能保证代码块的原子性
 CAS机制所保证的只是一个变量的原子性操作，而不能保证整个代码块的原子性。比如需要保证3个变量共同进行原子性的更新，就不得不使用Synchronized了。



### 左连接右连接

![preview](https://pic1.zhimg.com/v2-0f8b6e26d4ceb1440fb569922732dc1c_r.jpg)



左连接，左外连接：返回包括左表中的所有记录和右表中连接字段相等的记录。

右连接，右外连接：返回包括右表中的所有记录和左表中连接字段相等的记录。

内连接：只返回两个表中连接字段相等的行。

全外连接：返回左右表中所有的记录和左右表中连接字段相等的记录。



### SQL语言的执行过程

数据库引擎检查语法、语义，对查询对象加锁，访问权限核对，确定最终执行计划。





### MySQL Explain

[mysql explain详解 - 腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1093229)

通过 explain 我们可以知道以下信息：**表的读取顺序，数据读取操作的类型，哪些索引可以使用，哪些索引实际使用了，表之间的引用，每张表有多少行被优化器查询**等信息。



下面是使用 explain 的例子： 

在 select 语句之前增加 explain 关键字，[MySQL](https://cloud.tencent.com/product/cdb?from=10680) 会在查询上设置一个标记，执行查询时，会返回执行计划的信息，而不是执行这条SQL（如果 from 中包含子查询，仍会执行该子查询，将结果放入临时表中）。

```sql
mysql> explain select * from actor;
+----+-------------+-------+------+---------------+------+---------+------+------+-------+
| id | select_type | table | type | possible_keys | key  | key_len | ref  | rows | Extra |
+----+-------------+-------+------+---------------+------+---------+------+------+-------+
|  1 | SIMPLE      | actor | ALL  | NULL          | NULL | NULL    | NULL |    2 | NULL  |
+----+-------------+-------+------+---------------+------+---------+------+------+-------+
```



**select_type代表查询的类型：**

主要有几种关键的类型：

- **SIMPLLE**：简单查询，该查询不包含 UNION 或子查询
- **PRIMARY**：如果查询包含UNION 或子查询，则**最外层的查询**被标识为PRIMARY
- UNION：表示此查询是 UNION 中的第二个或者随后的查询
- DEPENDENT：UNION 满足 UNION 中的第二个或者随后的查询，其次取决于外面的查询
- UNION RESULT：UNION 的结果
- **SUBQUERY**：子查询中的第一个select语句(该子查询不在from子句中)
- DEPENDENT SUBQUERY：子查询中的 第一个 select，同时取决于外面的查询
- **DERIVED**：包含在from子句中子查询(也称为派生表)
- UNCACHEABLE SUBQUERY：满足是子查询中的第一个 select 语句，同时意味着 select 中的某些特性阻止结果被缓存于一个 Item_cache 中
- UNCACHEABLE UNION：满足此查询是 UNION 中的第二个或者随后的查询，同时意味着 select 中的某些特性阻止结果被缓存于一个 Item_cache 中



**table代表正在查询的表名：**



**type：**该列称为**关联类型或者访问类型**，它指明了MySQL决定如何查找表中符合条件的行，同时**是我们判断查询是否高效的重要依据**。

以下为常见的取值

- ALL：**全表扫描**，这个类型是性能最差的查询之一。通常来说，我们的查询不应该出现 ALL 类型，因为这样的查询，在数据量最大的情况下，对数据库的性能是巨大的灾难。
- index：**全索引扫描**，和 ALL 类型类似，只不过 ALL 类型是全表扫描，而 index 类型是扫描全部的索引，主要优点是避免了排序，但是开销仍然非常大。如果在 Extra 列看到 Using index，说明正在使用覆盖索引，只扫描索引的数据，它比按索引次序全表扫描的开销要少很多。
- range：**范围扫描**，就是一个有限制的索引扫描，它开始于索引里的某一点，返回匹配这个值域的行。这个类型通常出现在 `=、<>、>、>=、<、<=、IS NULL、<=>、BETWEEN、IN()` 的操作中，key 列显示使用了哪个索引，当 type 为该值时，则输出的 ref 列为 NULL，并且 key_len 列是此次查询中使用到的索引最长的那个。
- ref：一种索引访问，也称索引查找，它返回所有匹配某个单个值的行。此类型通常出现在多表的 join 查询, 针对于非唯一或非主键索引, 或者是使用了最左前缀规则索引的查询。
- eq_ref：使用这种索引查找，最多只返回一条符合条件的记录。在使用唯一性索引或主键查找时会出现该值，非常高效。
- const、system：该表至多有一个匹配行，在查询开始时读取，或者该表是系统表，只有一行匹配。其中 const 用于在和 primary key 或 unique 索引中有固定值比较的情形。
- NULL：在执行阶段不需要访问表。



[面试前必须知道的MySQL命令【explain】 - Java3y - 博客园 (cnblogs.com)](https://www.cnblogs.com/Java3y/p/10075578.html)









## MySQL的大数据场景

### NO SELECT *

1. 非必要的IO

   使用Select *将会返回大量我们并不需要的字段，这些查询过程对于数据库来说是浪费IO。

2. 非必要的网络开销

   非必要的数据量导致了非必要的网络带宽开销。

3. 非必要的内存开销

   同上的理由

4. 不可预知的返回顺序

   Select *会导致返回的顺序改变，原先的1、2、3，有可能返回顺序是2、1、3。

5. 连接查询时，无法使用缓冲池

   MySQL中连接查询的原理是先对驱动表进行查询，然后再用从驱动表中得到的数据作为比较，逐条得到被驱动表进行查询。**每次驱动表加载一条数据到内存中，都需要被驱动表中的数据也往内存中加载一次进行比较。所以MySQL中指定一个缓冲池，缓冲池大的话可以同时加载多个表的数据进行比较。存放的数据越多，就可以在内存中比较的条数就越多，所需要的IO操作就越少，性能就越好。**所以，如果此时使用SELECT *放一些无用的列，只会白白的占用缓冲空间。浪费本可以提高性能的机会。



### 联合索引

https://www.cnblogs.com/MrYuChen-Blog/p/13936680.html

mysql索引比较简单的是单列索引（b+tree）。遇到多条件查询时，不可避免会使用到多列索引。**联合索引又叫复合索引**

> b+tree结构如下图
>  每一个磁盘块在mysql中是一个页，页大小是固定的，**mysql innodb的默认的页大小是16k**，每个索引会分配在页上的数量是由字段的大小决定。当字段值的长度越长，每一页上的数量就会越少，**因此在一定数据量的情况下，索引的深度会越深，影响索引的查找效率。**
>
> ![image-20220719111022733](C:\Users\Caijinyang\AppData\Roaming\Typora\typora-user-images\image-20220719111022733.png)
>
> **对于复合索引（多列B+树，使用多列值组合而成的B+树索引），遵循最左原则，从左到右使用索引中的字段，一个查询可以只使用索引中的一部分，但是只能是最左侧部分，例如索引是key index(a, b, c)，可以支持(a), (a, b), (a, b, c)三种组合进行查找，但是不支持(b, c)这种组合查找**，当使用最左侧字段的时候，就会十分有效。

**创建表test如下：**

```css

create table test(
a int,
b int,
c int,
KEY a(a,b,c));
```

- 查（a, b, c)的时候，b+树是按照从左到右的顺序来建立搜索树的，比如当(a=? and b=? and  c=?)这样的数据来检索的时候，b+树会优先比较a列来确定下一步的所搜方向，如果a列相同再依次比较b列和c列，最后得到检索的数据；

  但当(b=?  and  c=?)这样的没有a列的数据来的时候，b+树就不知道下一步该查哪个节点，**因为建立搜索树的时候a列就是第一个比较因子**，**必须要先根据a列来搜索才能知道下一步去哪里查询**。比如当(a=? and c=?)这样的数据来检索时，b+树可以用a列来指定搜索方向，但下一个字段b列的缺失，所以只能把a列的数据找到，然后再匹配c列的数据了， 这个是非常重要的性质，即索引的最左匹配特性。以下通过例子分析索引的使用情况，以便于更好的理解联合索引的查询方式和使用范围。

**多列索引在and中的查询应用**

```sql
select * from test where a=? and b=? and c=?；查询效率最高，索引全覆盖。

select * from test where a=? and b=?；索引覆盖a和b。

select * from test where b=? and a=?；经过mysql的查询分析器的优化，索引覆盖a和b。

select * from test where a=?；索引覆盖a。

select * from test where b=? and c=?；没有a列，不走索引，索引失效。

select * from test where c=?；没有a列，不走索引，索引失效。

```

**多列索引在范围查询中的应用**

```sql
select * from test where a=? and b between ? and ? and c=?；索引覆盖a和b，因b列是范围查询，因此c列不能走索引。

select * from test where a between ? and ? and b=?；a列走索引，因a列是范围查询，因此b列是无法使用索引。

select * from test where a between ? and ? and b between ? and ? and c=?；a列走索引，因a列是范围查询，b列是范围查询也不能使用索引。

```



**多列索引在排序中的应用**

```sql
select * from test where a=? and b=? order by c；a、b、c三列全覆盖索引，查询效率最高。

select * from test where a=? and b between ? and ? order by c；a、b列使用索引查找，因b列是范围查询，因此c列不能使用索引，会出现file sort。

```



**联合索引的优势**

1. 减少开销

   建立一个联合索引(a, b, c)相当于建立了三个索引(a) (a, b) (a, b, c)三个索引，每多一个索引，都会增加写操作的开销和磁盘空间的开销。对于大量数据的表，使用联合索引会大大的减少开销

2. 覆盖索引

   对联合索引 (a,b,c)，如果有如下 sql 的，

   ```sql
   SELECT a,b,c from table where a='xx' and b = 'xx';
   ```

   那么 MySQL 可以直接通过遍历索引取得数据，而无需回表，这减少了很多的随机 io 操作。减少 io 操作，特别是随机 io 其实是 DBA 主要的优化策略。所以，在真正的实际应用中，覆盖索引是主要的提升性能的优化手段之一。

   > [什么是 MySQL 的“回表”？ - 文章详情 (itpub.net)](https://z.itpub.net/article/detail/A89812AB1494E1D51731ADF0606CC0F3)
   >
   > 对于主键索引和非主键索引，使用的数据结构都是 B+Tree，唯一的区别在于叶子结点中存储的内容不同：
   >
   > - 主键索引的叶子结点存储的是一行完整的数据。
   > - 非主键索引的叶子结点存储的则是主键值。
   >
   > 这就是两者最大的区别。
   >
   > 所以，当我们需要查询的时候：
   >
   > 1. 如果是通过主键索引来查询数据，例如 `select * from user where id=100`，那么此时只需要搜索主键索引的 B+Tree 就可以找到数据。
   > 2. 如果是通过非主键索引来查询数据，例如 `select * from user where username='javaboy'`，那么此时需要先搜索 username 这一列索引的 B+Tree，搜索完成后得到主键的值，然后再去搜索主键索引的 B+Tree，就可以获取到一行完整的数据。
   >
   > 对于第二种查询方式而言，一共搜索了两棵 B+Tree，**第一次搜索 B+Tree 拿到主键值后再去搜索主键索引的 B+Tree，这个过程就是所谓的回表。**

3. 效率高

   每一次索引匹配都可以筛选掉一部分的数据，多次联合索引的筛选效果是指数级的。



**索引是建的越多越好吗**
答案自然是否定的
数据量小的表不需要建立索引，建立会增加额外的索引开销

不经常引用的列不要建立索引，因为不常用，即使建立了索引也没有多大意义

经常频繁更新的列不要建立索引，因为肯定会影响插入或更新的效率

数据重复且分布平均的字段，因此他建立索引就没有太大的效果（例如性别字段，只有男女，不适合建立索引）

数据变更需要维护索引，意味着索引越多维护成本越高。

更多的索引也需要更多的存储空间



### 如何优化MySQL

1. SQL语句以及索引的优化

   > 优化insert：一次插入多个值
   >
   > 应尽量避免在where子句中使用!=或者<>或者null值判断，否则引擎会放弃索引而是使用全表扫描
   >
   > 优化嵌套查询：子查询可以使用join实现
   >
   > 很多时候使用exists代替in是一个好的选择

2. 数据表结构的优化

   > 选择合适的数据类型
   >
   > 表的范式优化
   >
   > 表的垂直拆分
   >
   > 表的水平拆分

3. 系统配置的优化

4. 硬件的优化



### 海量数据库分库分表

[数据库分库分表，何时分？怎样分？详细解读，一篇就够了_azhuyangjun的博客-CSDN博客_数据库分库分表](https://blog.csdn.net/azhuyangjun/article/details/86976514)

关系型数据库本身比较容易成为系统的性能瓶颈，单机存储容量、连接数、处理能力都有限。当表的大小到达1000W或者100GB之后，由于查询的维度比较多，一边添加主从库进行优化，做很多操作的时候性能仍然会下降严重。这个时候需要对数据库表进行切分，切分的目的在于减少数据库的负担，缩短查询的时间。

数据库分布式的核心就在于数据切分，以及对切分之后的数据的整合、定位。数据库分库分片的思想就是将数据分散存储在多个数据库中，使得单一数据库的数据量减小，通过扩充主机数量缓解单一数据库的性能问题，从而达到数据库操作性能的提升。 数据切分根据切分类型主要分为水平切分和垂直切分。

#### 垂直切分

垂直切分有分为垂直分库和垂直分表。垂直切分就是根据业务耦合性，将关联度低的不同表存储在不同的数据库。做法和大系统拆成小系统的做法类似，类似于每个微服务使用一个单独的数据库。

垂直分表是基于数据库中的column来分的，某个表的字段太多较多，可以新建一张扩张表，将不经常用或者字段长度较大的字段分出去建立扩展表。在字段比较多的表中（例如某张大表有100多个字段），通过大表拆小表，更易于维护和开发，同时也可以避免跨页的问题。

避免跨页问题的原理是：MySQL的底层都是由页实现的，如果一条记录占用的空间过大会导致跨页，造成额外的性能开销。

此外，由于内存的容量是一定的，每一行记录的字段长度越大的话，能够存储的记录数量就越少，那么内存的命中的概率就会更小，减少了磁盘的IO，从而提升了数据库的性能。

**垂直切分的优点：**

- 能够解决业务逻辑的的耦合，业务清晰
- 与微服务的治理类似，可以对不同业务的数据进行分级管理、维护、监控、扩展。
- 高并发的场景下，垂直切分一定程度的提升IO、数据库连接数、单机硬件资源的瓶颈

**垂直切分的缺点：**

- 部分表不能Join，只能通过接口聚合的方式解决，提升了开发的复杂度
- 分布式事务处理复杂
- 依然存在单表的数据量过大的问题

#### 水平切分

当一个业务的细粒度已经几乎无法再分，也就是无法再垂直切分，或者切分后数据行数巨大，存在单库读写、存储性能瓶颈，这时候就需要进行水平切分。

水平切分分为库内分表和分库分表，是根据表内数据内在的逻辑关系，将同一个表按不同的条件分散到多个数据库或多个表中，每个表中只包含一部分数据，从而使得单个表的数据量变小，达到分布式的效果。

库内分表也就是将同一个数据库拆成多个表，但是实际上并没有解决减轻数据库压力的问题，因为所有的数据库还是在争用同一个物理机的资源，CPU和内存的资源仍然是有限的。所以最优的解决方案是通过分库分表。

**水平切分的优点：**

- 不会存在单库的数据量巨大、高并发的瓶颈，提升系统的稳定性和负载能力
- 应用端几乎不需要做更改，不需要拆分业务模块

**水平切分的缺点：**

- 快分片的事务难以保证一致性
- 跨库join的性能比较差
- 数据多次扩展难度和维护量比较大

















### Linux(CentOS 7)安装MySQL

[centos7系统安装mysql8.0完整步骤 - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1849475)

1. 解压安装包

   ```shell
   tar -xvf mysql-8.0.26-1.el7.x86_64.rpm-bundle.tar
   ```

2. 安装顺序：common->lib->lib-compat->client->server

   ```shell
   rpm -ivh mysql-community-common-8.0.26-1.el7.x86_64.rpm --nodeps --force
   rpm -ivh mysql-community-libs-8.0.26-1.el7.x86_64.rpm --nodeps --force
   rpm -ivh mysql-community-client-8.0.26-1.el7.x86_64.rpm --nodeps --force
   rpm -ivh mysql-community-server-8.0.26-1.el7.x86_64.rpm --nodeps --force
   ```

3. 通过 rpm -qa | grep mysql 命令查看 mysql 的安装包

4. 初始化MySQL

   ```shell
   mysqld --initialize;
   chown mysql:mysql /var/lib/mysql -R;
   systemctl start mysqld.service;
   systemctl enable mysqld;
   ```

5. 查看数据库默认的随机密码

   ```shell
   cat /var/log/mysqld.log | grep password
   ```

   将默认的密码记录下来

6. 登录MySQL

   ```shell
   mysql -uroot -p
   ```

   输入刚刚查到的密码，进行数据库的登陆，复制粘贴就行，MySQL 的登陆密码也是不显示的

7. 修改登录密码

   ```sql
   ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '新的密码';
   ```

8. 退出MySQL

9. 授权远程访问

   ```sql 
   create user 'root'@'%' identified with mysql_native_password by 'root';
   grant all privileges on *.* to 'root'@'%' with grant option;
   flush privileges;
   ```

   ```sql
   // mysql 5.7
   mysql> select host,user from user;
   +-----------+---------------+
   | host      | user          |
   +-----------+---------------+
   | %         | root          |
   | localhost | mysql.session |
   | localhost | mysql.sys     |
   | localhost | root          |
   +-----------+---------------+
   4 rows in set (0.00 sec)
   
   mysql> create user 'rock'@'%' identified by 'cfsc@2021';
   Query OK, 0 rows affected (0.00 sec)
   
   mysql> select host,user from user;
   +-----------+---------------+
   | host      | user          |
   +-----------+---------------+
   | %         | rock          |
   | %         | root          |
   | localhost | mysql.session |
   | localhost | mysql.sys     |
   | localhost | root          |
   +-----------+---------------+
   5 rows in set (0.00 sec)
   
   mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'cfsc@2021';
   Query OK, 0 rows affected, 1 warning (0.00 sec)
   
   mysql> flush privileges;
   Query OK, 0 rows affected (0.00 sec)
   
   mysql> select host,user from user;
   +-----------+---------------+
   | host      | user          |
   +-----------+---------------+
   | %         | rock          |
   | %         | root          |
   | localhost | mysql.session |
   | localhost | mysql.sys     |
   | localhost | root          |
   +-----------+---------------+
   5 rows in set (0.00 sec)
   
   // mtsql 8
   mysql> GRANT ALL ON *.* TO 'root'@'%';
   
   // 权限设置完之后需要systemctl restart mysqld
   ```


10. 修改加密规则

    ```sql
    ALTER USER 'root'@'localhost' IDENTIFIED BY 'root' PASSWORD EXPIRE NEVER;
    ```

11. 刷新权限

    ```sql
    flush privileges;
    ```

    

### MySQL设置大小写不敏感

[mysql 设置大小写不敏感_Hehuyi_In的博客-CSDN博客_mysql设置大小写不敏感](https://blog.csdn.net/Hehuyi_In/article/details/95354014)



### MySQL修改密码

[Linux下Mysql-8.0修改密码 - 暖影子 - 博客园 (cnblogs.com)](https://www.cnblogs.com/nuanyingzi/p/13432465.html)

**Linux下mysql升级到8.0版本了，以往mysql5.7设置密码的方法已经失效。此处提供的是8.0之后的版本。**



假设仍然记得原始密码，并且已经登录进入MySQL。

1. 先将原先的密码清空

   ```SQL
   update user set authentication_string='' where  user = 'root';
   ```

2. 修改密码

   ```sql
   ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '你的密码'; 
   ```

3. 刷新权限

   ```sql
   FLUSH privileges;
   ```

   

假设忘记了登录密码，无法登录进入MySQL，需要先在启动项中添加skip-grant-tables。





### MySQL设置密码永不过期

[MySQL 密码过期的修改方法 | 小决的专栏 (jueee.github.io)](https://jueee.github.io/2021/07/2021-07-23-MySQL密码过期的修改方法/)

```mysql
mysql> set global default_password_lifetime=0;
```

```shell
vim /etc/my.cnf

[mysqld]
default_password_lifetime=0
```

```mysql
mysql> show global variables like 'default_password_lifetime';
+---------------------------+-------+
| Variable_name             | Value |
+---------------------------+-------+
| default_password_lifetime | 0     |
+---------------------------+-------+
1 row in set (0.01 sec)
```
