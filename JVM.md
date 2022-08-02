# JVM
## JVM的内存空间
JVM的内存空间分为5个部分

- 程序计数器
- Java虚拟机栈

- 本地方法栈
- 堆
- 方法区

![image-20220715143229321](C:\Users\Caijinyang\AppData\Roaming\Typora\typora-user-images\image-20220715143229321.png)

>JDK 1.8 同 JDK 1.7 比，最大的差别就是：**元数据区取代了永久代**。元空间的本质和永久代类似，都是对 JVM 规范中方法区的实现。不过元空间与永久代之间最大的区别在于：元数据空间并不在虚拟机中，而是使用本地内存。



### 程序计数器（PC寄存器）

**程序计数器的定义**

程序计数器是一块较小的内存空间，是线程正在执行的字节码指令的地址。

如果线程正在执行的是一个本地方法，那么此时的程序计数器为undefined。



**程序计数器的作用**

- 字节码解释器通过改变程序计数器来一次读取指令，从而实现代码的流程控制。
- 在多线程情况下，程序计数器执行的是当前线程执行的位置，当线程切换回来的时候，就知道上次的线程执行到哪儿。



**程序计数器的特点**

- 是一块空间较小的**内存**空间
- 线程私有，每个线程都有一个独立的程序计数器
- 随着线程的生命周期创建和销毁
- 是唯一不会出现OOM错误的区域





### Java虚拟机栈（Java栈）

**Java虚拟机栈的定义**

Java虚拟机栈的是描述Java方法运行的内存模型。

Java虚拟机会为每个即将运行的Java方法分配一块内存，叫做**栈帧**，这个内存空间用于放置该方法运行中的一些信息：

- 局部变量表
- 操作数栈
- 动态链接
- 方法出口信息

<img src="C:\Users\Caijinyang\AppData\Roaming\Typora\typora-user-images\image-20220715154838322.png" alt="image-20220715154838322" style="zoom: 80%;" />

**压栈出栈过程**

当方法过程中需要创建局部变量的时候，就将局部变量的值存放在栈帧的局部变量表中。

**Java 虚拟机栈的栈顶的栈帧是当前正在执行的活动栈，也就是当前正在执行的方法，PC 寄存器也会指向这个地址。**

**只有活动栈的本地变量可以被操作数栈调用。当在这个栈帧中调用另一个方法， 与之对应的新的栈帧又会被创建，新创建的栈帧压入栈顶，变为当前的活动栈帧。**

**方法结束之后，该方法出栈，栈帧的返回值变成新的活动栈帧中操作数栈的一个操作数。如果没有返回值，那么新的活动栈中的操作数不会有任何变化。**

> 由于 Java 虚拟机栈是与线程对应的，数据不是线程共享的（也就是线程私有的)，因此不用关心数据一致性问题，也不会存在同步锁的问题。



**局部变量表**

定义为一个数字数组，主要用于存储方法参数，定义在方法体内部的局部变量，数据类型（包括各种类型的基本数据、对象引用），以及return address类型。

局部变量表的大小是在编译期确定下来的。最基本的存储单位是slot，32位类型占用一个slot，64位类型（long、double）占用两个slot。

> 对于slot的理解：
>
> - JVM会为每一个slot分配一个访问索引，通过这个索引可以直接访问到slot，获取slot地址中的值。
> - 如果当前帧是由构造方法或者实例方法创建的，那么该对象引用this，会存放在index为0的slot处，其余的参数表顺序继续排列。
> - 栈帧中的局部变量表中的槽位是可以重复的，**如果一个局部变量过了其作用域，那么其作用域之后申明的新的局部变量就有可能会复用过期局部变量的槽位**，从而达到节省资源的目的。

**在栈帧中，与性能调优关系最密切的部分，就是局部变量表**，方法执行时，虚拟机使用局部变量表完成方法的传递局部变量表中的变量也是重要的垃圾回收根节点，**只要被局部变量表中直接或间接引用的对象都不会被回收**。



**操作数栈**

- **栈顶缓存**：栈顶元素全部放到CPU的cache中，这样可以降低在内存中频繁读写的时间消耗，提升执行引擎的执行效率。
- 每一个操作数栈都有一个明确的栈深度，用于存储数值，最大深度在编译器就已经规定好，32bit 类型占用一个栈单位深度，64bit 类型占用两个栈单位深度操作数栈。
- 并非采用访问索引方式进行数据访问，而是只能通过标准的入栈、出栈操作完成一次数据访问。



**方法的调用**

- 静态链接：当一个字节码文件被装载进 JVM 内部时，如果被调用的目标方法在编译期可知，且运行时期间保持不变，这种情况下降调用方的符号引用转为直接引用的过程称为静态链接。
- 动态链接：如果被调用的方法无法在编译期被确定下来，只能在运行期将调用的方法的符号引用转为直接引用，这种引用转换过程具备动态性，因此被称为动态链接。
- 方法绑定：
  - 早期绑定：被调用的目标方法如果**在编译期可知**，且运行期保持不变。
  - 晚期绑定：被调用的方法**在编译期无法被确定**，只能够在程序运行期根据实际的类型绑定相关的方法。
- 静态方法：如果方法在编译期就确定了具体的调用版本，则这个版本在运行时是不可变的。这样的方法称为非虚方法静态方法，私有方法，final 方法，实例构造器，父类方法都是非虚方法，除了这些以外都是虚方法。
- 虚方法表：面向对象的编程中，会很频繁的使用动态分配，如果每次动态分配的过程都要重新在类的方法元数据中搜索合适的目标的话，就可能影响到执行效率，因此为了提高性能，JVM 采用在类的方法区建立一个虚方法表，使用索引表来代替查找。
  - **每个类都有一个虚方法表，表中存放着各个方法的实际入口。**
  - 虚方法表会在类加载的链接阶段被创建，并开始初始化，类的变量初始值准备完成之后，JVM 会把该类的方法也初始化完毕。

- 方法重写的本质
  - 找到操作数栈顶的第一个元素所执行的对象的实际类型，记做 C。如果在类型 C 中找到与常量池中描述符和简单名称都相符的方法，则进行访问权限校验。
  - 如果通过则返回这个方法的直接引用，查找过程结束；如果不通过，则返回 java.lang.IllegalAccessError 异常。
  - 否则，按照继承关系从下往上依次对 C 的各个父类进行上一步的搜索和验证过程。
  - 如果始终没有找到合适的方法，则抛出 java.lang.AbstractMethodError 异常。

**Java 中任何一个普通方法都具备虚函数的特征（运行期确认，具备晚期绑定的特点），C++ 中则使用关键字 virtual 来显式定义。如果在 Java 程序中，不希望某个方法拥有虚函数的特征，则可以使用关键字 final 来标记这个方法。**



Java虚拟机栈的特点

- 运行速度快，仅次于PC寄存器。
- 局部变量表随着栈帧的创建而创建，它的大小在编译时确定，创建时只需分配事先规定的大小即可。在方法运行过程中，局部变量表的大小不会发生改变。
- Java虚拟机栈会产生两种错误：StackOverFlowError和OOM。
  - StackOverFlowError：**若 Java 虚拟机栈的大小不允许动态扩展，那么当线程请求栈的深度超过当前 Java 虚拟机栈的最大深度时，抛出 StackOverFlowError 异常。**
  - OutOfMemoryError：若允许动态扩展，那么当**线程请求栈时分配给这个进程的物理内存用完了**，无法再动态扩展时，抛出 OutOfMemoryError 异常。
- **Java 虚拟机栈也是线程私有，随着线程创建而创建，随着线程的结束而销毁。**
- 出现 StackOverFlowError 时，内存空间可能还有很多。



常见的运行时异常有：

- NullPointerException - 空指针引用异常
- ClassCastException - 类型强制转换异
- IllegalArgumentException - 传递非法参数异常
- ArithmeticException - 算术运算异常
- ArrayStoreException - 向数组中存放与声明类型不兼容对象异常
- IndexOutOfBoundsException - 下标越界异常
- NegativeArraySizeException - 创建一个大小为负数的数组错误异常
- NumberFormatException - 数字格式异常
- SecurityException - 安全异常
- UnsupportedOperationException - 不支持的操作异常



### 本地方法栈（C栈）

**本地方法栈的定义**

本地方法栈是为 JVM 运行 Native 方法准备的空间，由于很多 Native 方法都是用 C 语言实现的，所以它通常又叫 C 栈。它与 Java 虚拟机栈实现的功能类似，只不过本地方法栈是描述本地方法运行过程的内存模型。

**栈帧变化过程**

本地方法被执行时，在本地方法栈也会创建一块栈帧，用于存放该方法的局部变量表、操作数栈、动态链接、方法出口信息等。

方法执行结束后，相应的栈帧也会出栈，并释放内存空间。也会抛出 StackOverFlowError 和 OutOfMemoryError 异常。

> 如果 Java 虚拟机本身不支持 Native 方法，或是本身不依赖于传统栈，那么可以不提供本地方法栈。如果支持本地方法栈，那么这个栈一般会在线程创建的时候按线程分配。



### 堆

**堆的定义**

堆用来存放对象的内存空间，几乎所有的对象都存放在堆中。

**堆的特点**

- 线程共享，所有的线程都共享同一个堆，整个Java虚拟机只有一个堆。与之相比较，程序计数器、虚拟机栈、本地方法栈都是每个线程独有的。
- 在虚拟机启动的时候就创建堆。
- 是垃圾回收工作的主要场所。
- 堆可分为新生代（Eden 区：`From Survior`，`To Survivor`）、老年代。
- Java 虚拟机规范规定，**堆可以处于物理上不连续的内存空间中，但在逻辑上它应该被视为连续的。**

堆的不同区域存放着不同生命周期的对象内存，这样可以根据不同的区域使用不同的垃圾回收算法，更加具有针对性。

堆的大小既可以固定也可以扩展，但是对于主流的虚拟机，堆的大小是可以扩展的。当堆的大小已经是最大，并且内存也已经全部分配，此时会抛出OOM异常。



**新生代和老年代**

- 老年代的生命周期比新生代长。
- 新生代与老年代空间默认比例为1: 2 ---- JVM 调参数，`XX:NewRatio=2`，表示新生代占 1，老年代占 2，新生代占整个堆的 1/3。
- HotSpot中，Eden空间和另外两个Survivor空间所缺省的比例是8:1:1。
- 几乎所有的Java对象都是在Eden区被new出来的，Eden存放不了大的对象，会直接进入老年代。



**对象的分配过程**

- new的对象先放在Eden区，大小有限制
- 如果创建新对象，Eden空间填满了，就会触发Minor GC，将Eden不再被引用的对象销毁，再加载新的对象到Eden区，特别注意的是 Survivor 区满了是不会触发 Minor GC 的，而是 Eden 空间填满了，Minor GC 才顺便清理 Survivor 区
- 将Eden中剩余的对象移到Survivor区
- 再次触发垃圾回收，此时上次 Survivor 下来的，放在 Survivor0 区的，如果没有回收，就会放到 Survivor1 区
- 再次经历垃圾回收，又会将幸存者重新放回 Survivor0 区，依次类推

- 默认是 15 次的循环，超过 15 次，则会将幸存者区幸存下来的转去老年区 jvm 参数设置次数 : -XX:MaxTenuringThreshold=N 进行设置
- 频繁在新生区收集，很少在养老区收集，几乎不在永久区/元空间搜集



**Full GC & Major GC触发的条件**

- 显式调用`System.gc()`，老年代的空间不够，方法区的空间不够等都会触发 Full GC，同时对新生代和老年代回收，FUll GC 的 STW 的时间最长，应该要避免
- 在出现 Major GC 之前，会先触发 Minor GC，如果老年代的空间还是不够就会触发 Major GC，STW 的时间长于 Minor GC



**四种引用方式**

[Java的四种引用方式](https://blog.csdn.net/linzhiqiang0316/article/details/88591907)

- 强引用：创建一个对象，并且将整个对象赋给一个引用变量，普通new出来的对象的变量引用都是强引用，有引用变量指向的时候永远不会被GC，JVM宁愿抛出OOM。当一个方法运行结束之后，指向的对象不存在了，JVM会将其回收。如果想中断强引用和某个对象之间的关联，可以显示地将引用赋值为null，这样一来的话，JVM在合适的时间就会回收该对象。

- 软引用：如果一个对象具有软引用，内存空间还足够，GC就不会回收它，如果内存空间不足了，就会回收这些对象的内存，只要垃圾回收器没有回收它，该对象就可以被程序回收。

  ```java 
  MyObject aRef = new  MyObject();  
  SoftReference aSoftRef=new SoftReference(aRef);  
  ```

  此时，对于这个MyObject对象，有两个引用路径，一个是来自SoftReference对象的软引用，一个来自变量aReference的强引用，所以这个MyObject对象是强可及对象。

  随即，我们可以结束aReference对这个MyObject实例的强引用:

  ```java 
  aRef = null;
  ```

  此后，这个MyObject对象成为了软引用对象。如果垃圾收集线程进行内存垃圾收集，**并不会因为有一个SoftReference对该对象的引用而始终保留该对象。**

  Java虚拟机的垃圾收集线程对软可及对象和其他一般Java对象进行了区别对待:软可及对象的清理是由垃圾收集线程根据其特定算法按照内存需求决定的。
  也就是说，垃圾收集线程会在虚拟机抛出OutOfMemoryError之前回收软可及对象，而且虚拟机会尽可能优先回收长时间闲置不用的软可及对象，对那些刚刚构建的或刚刚使用过的“新”软可反对象会被虚拟机尽可能保留。在回收这些对象之前，我们可以通过:

  ```java 
  MyObject anotherRef=(MyObject)aSoftRef.get();  
  ```

  重新获得对该实例的强引用。而回收之后，调用get()方法就只能得到null了。

- 弱引用：弱引用也是用来描述非必需对象的，当JVM进行垃圾回收时，无论内存是否充足，都会回收被弱引用关联的对象。在java中，用java.lang.ref.WeakReference类来表示。下面是使用示例：

  第二个输出结果是null，这说明只要JVM进行垃圾回收，被弱引用关联的对象必定会被回收掉。不过要注意的是，这里所说的被弱引用关联的对象是指只有弱引用与之关联，如果存在强引用同时与之关联，则进行垃圾回收时也不会回收该对象（软引用也是如此）。

- 虚引用：虚引用和前面的软引用、弱引用不同，它并不影响对象的生命周期。在java中用java.lang.ref.PhantomReference类表示。如果一个对象与虚引用关联，则跟没有引用与之关联一样，在任何时候都可能被垃圾回收器回收。



### 方法区

**方法区的定义**

Java虚拟机中的方法区是堆的一个逻辑部分，方法区存放以下信息：

- 已经被虚拟机加载的信息
- 常量
- 静态变量
- 即时编译器编译后的代码



**方法区的特点**

- 线程共享。方法区是堆的一个逻辑部分，因此和堆一样，都是线程共享的。整个虚拟机中只有一个方法区。
- 方法区是永久代。方法区中的信息一般需要长期存在，而且它又是堆的逻辑分区，因此用堆的划分方法，把方法区称为“永久代”。
- 内存回收率低。方法区中的信息一般需要长期存在，回收一遍之后可能只有少量信息无效。主要回收目标是：对常量池的回收；对类型的卸载。
- Java虚拟机对于方法区的要求比较宽松。和堆一样，允许固定大小也允许动态扩展，还允许不GC。



**运行常量池**

方法区中存放：类信息、常量、静态变量、即时编译器编译后的代码，常量就存放在运行时的常量池中。

当类被 Java 虚拟机加载后， .class 文件中的常量就存放在方法区的运行时常量池中。而且在运行期间，可以向常量池中添加新的常量。如 String 类的 `intern()` 方法就能在运行期间向常量池中添加字符串常量。



### 直接内存（堆外内存）

直接内存是除了Java虚拟机之外的内存，但也可能被Java使用。

**操作直接内存**

Java在NIO中引入了一种基于通道和缓冲的 IO 方式。它可以通过调用本地方法直接分配 Java 虚拟机之外的内存，然后通过一个存储在堆中的`DirectByteBuffer`对象直接操作该内存，而无须先将外部内存中的数据复制到堆中再进行操作，从而提高了数据操作的效率。

直接内存的大小不受 Java 虚拟机控制，但既然是内存，当内存不足时就会抛出 OutOfMemoryError 异常。



**直接内存与堆内存比较**

- 直接内存申请空间耗费更高的性能
- 直接内存读取 IO 的性能要优于普通的堆内存
- 直接内存作用链： 本地 IO -> 直接内存 -> 本地 IO
- 堆内存作用链：本地 IO -> 直接内存 -> 非直接内存 -> 直接内存 -> 本地 IO

> 服务器管理员在配置虚拟机参数时，会根据实际内存设置`-Xmx`等参数信息，但经常忽略直接内存，使得各个内存区域总和大于物理内存限制，从而导致动态扩展时出现`OutOfMemoryError`异常。





## Hotspot虚拟机对象

### 对象的内存布局

在Hotspot虚拟机中，对象的内存布局分为以下3块区域：

- 对象头Header
- 示例数据Instance Data
- 对其填充Padding



### 对象头

对象头记录对象在运行阶段的一些数据：

- 哈希值
- GC分代年龄
- 锁状态标志
- 线程持有的锁
- 偏向线程ID
- 偏向时间戳

对象头可能包含类型指针，通过该指针能确定对象属于哪个类。如果对象是一个数组，那么对象头还会包括数组长度。

### 实例数据

实例数据部分就是成员变量的值，其中包括父类成员变量和本类成员变量。

### 对齐填充

用于确保对象的总长度为 8 字节的整数倍。

HotSpot VM 的自动内存管理系统要求对象的大小必须是 8 字节的整数倍。而对象头部分正好是 8 字节的倍数（1 倍或 2 倍），因此，当对象实例数据部分没有对齐时，就需要通过对齐填充来补全。

> 对齐填充并不是必然存在，也没有特别的含义，它仅仅起着占位符的作用。



### 对象的创建过程

#### 类加载检查

虚拟机在解析一个.class文件的时候，如果遇到一个new指令，首先会去检查常量池中是否有这个类的符号引用，并且检查这个符号引用所代表的类是否已经被加载、解析、初始化过。如果没有，再进行类加载。



#### 为新生对象分配内存

对象所需要的内存在类加载完之后就可以确定下来，接下来从堆中划分一块对应大小的内存空间给新的对象。

分配堆中的内存有两种方式：

- **指针碰撞：**如果 Java **堆中内存绝对规整**（说明采用的是“**复制算法**”或“**标记整理法**”），空闲内存和已使用内存中间放着一个指针作为分界点指示器，那么分配内存时只需要把指针向空闲内存挪动一段与对象大小一样的距离，这种分配方式称为“**指针碰撞**”。
- **空闲列表**：如果 Java **堆中内存并不规整**，已使用的内存和空闲内存交错（说明采用的是**标记-清除法**，有碎片），此时没法简单进行指针碰撞， VM 必须维护一个列表，记录其中哪些内存块空闲可用。分配之时从空闲列表中找到一块足够大的内存空间划分给对象实例。这种方式称为“**空闲列表**”。



#### 初始化

分配完内存后，为对象中的成员变量赋上初始值，设置对象头信息，调用对象的构造函数方法进行初始化。

至此，整个对象的创建过程就完成了。



### 对象的访问方式

所有对象的存储空间都是在堆中分配的，但是找个对象的引用确实在堆栈中分配的，也就是说建立一个对象时，两个地方都需要分配内存，在堆中分配的内存是用来实际建立对象，栈中的内存只是一个指向找个堆内存中对象的指针（引用），那么根据引用存放的地址类型的不同，对象的访问方式不同。

#### 句柄访问方式

堆中需要有一块叫做“句柄池”的内存空间，句柄中包含了对象实例数据与类型数据各自的具体地址信息。

引用类型的变量存放的是该对象的句柄地址（reference）。访问对象时，首先需要通过引用类型的变量找到该对象的句柄，然后根据句柄中对象的地址找到对象。

![handle-access](https://camo.githubusercontent.com/d80786b4279beaef408e13f354894bf073fdf2325ad5fb71391db775194c5e67/68747470733a2f2f63646e2e6a7364656c6976722e6e65742f67682f646f6f63732f6a766d406d61696e2f696d616765732f68616e646c652d6163636573732e6a7067)

#### 直接指针访问方式

引用类型的变量直接存放对象的地址，从而不需要句柄池，通过引用能够直接访问对象。但对象所在的内存空间需要额外的策略存储对象所属的类信息的地址。

![direct-pointer](https://camo.githubusercontent.com/08c8395c4927f7aa25d7df63912df8145c5a876cdabf66675398148629b4f086/68747470733a2f2f63646e2e6a7364656c6976722e6e65742f67682f646f6f63732f6a766d406d61696e2f696d616765732f6469726563742d706f696e7465722e6a7067)

需要说明的是，HotSpot 采用第二种方式，即直接指针方式来访问对象，只需要一次寻址操作，所以在性能上比句柄访问方式快一倍。但像上面所说，它需要**额外的策略**来存储对象在方法区中类信息的地址。



## GC

程序计数器、虚拟机栈、本地方法栈随线程而生，也随线程而灭；

栈帧随着方法的开始而入栈，随着方法的结束而出栈。

这几个区域的内存分配和回收都具有确定性，在这几个区域内不需要过多考虑回收的问题，**因为方法结束或者线程结束时，内存自然就跟随着回收了。**



**垃圾回收主要是针对对象而言。**



### 判断对象是否存活

若一个对象不被任何对象或变量引用，那么它就是无效对象，需要被回收。

#### 引用计数

在对象头维护着一个 counter 计数器，对象被引用一次则计数器 +1；若引用失效则计数器 -1。当计数器为 0 时，就认为该对象无效了。

在多线程环境下，这个counter需要耗费大量的资源加锁，



#### 可达性分析

所有和 GC Roots 直接或间接关联的对象都是有效对象，和 GC Roots 没有关联的对象就是无效对象。

**GC Roots 是指：**

- **Java 虚拟机栈（栈帧中的本地变量表）中引用的对象**
- **本地方法栈中引用的对象**
- **方法区中常量引用的对象**
- **方法区中类静态属性引用的对象**

GC Roots 并不包括堆中对象所引用的对象，这样就不会有循环引用的问题。**（GC roots包含虚拟机栈、本地方法栈、方法区中静态属性、常量）引用的对象，之所以不包括堆中的对象，是为了防止循环引用。**



#### 四种引用 TBD

强引用：类似 "`Object obj = new Object()`" 这类的引用，就是强引用，只要强引用存在，垃圾收集器永远不会回收被引用的对象。但是，如果我们**错误地保持了强引用**，比如：赋值给了 static 变量，那么对象在很长一段时间内不会被回收，会产生内存泄漏。

软引用：软引用是一种相对强引用弱化一些的引用，可以让对象豁免一些垃圾收集，只有当 JVM 认为内存不足时，才会去试图回收软引用指向的对象。JVM 会确保在抛出 OutOfMemoryError 之前，清理软引用指向的对象。软引用通常用来**实现内存敏感的缓存**，如果还有空闲内存，就可以暂时保留缓存，当内存不足时清理掉，这样就保证了使用缓存的同时，不会耗尽内存。

弱引用：弱引用的**强度比软引用更弱**一些。当 JVM 进行垃圾回收时，**无论内存是否充足，都会回收**只被弱引用关联的对象。

虚引用：虚引用也称幽灵引用或者幻影引用，它是**最弱**的一种引用关系。一个对象是否有虚引用的存在，完全不会对其生存时间构成影响。它仅仅是提供了一种确保对象被 finalize 以后，做某些事情的机制，比如，通常用来做所谓的 Post-Mortem 清理机制。





### GC算法

学会了如何判定无效对象、无用类、废弃常量之后，剩余工作就是回收这些垃圾。常见的垃圾收集算法有以下几个：

#### 标记-清除

**标记**的过程是：遍历所有的 `GC Roots`，然后将所有 `GC Roots` 可达的对象**标记为存活的对象**。

**清除**的过程将遍历堆中所有的对象，将没有标记的对象全部清除掉。与此同时，清除那些被标记过的对象的标记，以便下次的垃圾回收。

这种方法有两个**不足**：

- 效率问题：标记和清除两个过程的效率都不高。
- 空间问题：标记清除之后会产生大量不连续的内存碎片，碎片太多可能导致以后需要分配较大对象时，无法找到足够的连续内存而不得不提前触发另一次垃圾收集动作。



#### 复制算法（新生代）

为了解决效率问题，复制收集算法出现，将内存按照容量划分成大小相等的两块，每次只使用其中的一块，当这一块内存用完了需要进行垃圾收集时，就将存活者的对象全部复制到另一块上，将第一块的内存全部清除。

- 优点：不会有内存碎片的问题
- 缺点：真正在使用的内存缩小为原来的一半，浪费了很多空间

为了解决空间利用率问题，可以将内存分为三块： **Eden、From Survivor、To Survivor，比例是  8:1:1，**每次使用 Eden 和其中一块 Survivor。回收时，将 Eden 和 Survivor 中还存活的对象一次性复制到另外一块  Survivor 空间上，最后清理掉 Eden 和刚才使用的 Survivor 空间。这样只有 10% 的内存被浪费。

但是我们无法保证每次回收都只有不多于 10% 的对象存活，当 Survivor 空间不够，需要依赖其他内存（指老年代）进行分配担保。

##### 分配担保

为对象分配内存空间时，如果 Eden+Survivor 中空闲区域无法装下该对象，会触发 MinorGC  进行垃圾收集。但如果 Minor GC 过后依然有超过 10% 的对象存活，这样存活的对象直接通过分配担保机制进入老年代，然后再将新对象存入  Eden 区。



#### 标记-整理算法（老年代）

**标记**：它的第一个阶段与**标记-清除算法**是一模一样的，均是遍历 `GC Roots`，然后将存活的对象标记。

**整理**：移动所有**存活的对象**，且按照内存地址次序依次排列，然后将末端内存地址以后的内存全部回收。因此，第二阶段才称为整理阶段。

这是一种老年代的垃圾收集算法。老年代的对象一般寿命比较长，因此每次垃圾回收会有大量对象存活，如果采用复制算法，每次需要复制大量存活的对象，效率很低。



#### 分代收集算法

根据对象存活周期的不同，将内存划分为几块。一般是把 Java 堆分为新生代和老年代，针对各个年代的特点采用最适当的收集算法。

- 新生代：复制算法
- 老年代：标记-清除算法、标记-整理算法



## hotspot GC TBD







## JVM cmds

Sun JDK监控和故障处理命令有jps jstat jmap jhat jstack jinfo下面做一一介绍

```shell
[root@rock1 ~]# jps -l
26304 gateway.jar
1937 sun.tools.jps.Jps
[root@rock1 ~]# jps -m
26304 jar
1986 Jps -m
[root@rock1 ~]# jps -q
26304
2027
[root@rock1 ~]# jps -v
26304 jar -Xms2048m -Xmx4096m
2054 Jps -Denv.class.path=.:/data/java/jdk/lib:/data/java/jdk/jre/lib: -Dapplication.home=/data/java/jdk -Xms8m

[root@rock1 ~]# jstack gc  26304
Attaching to core 26304 from executable gc, please wait...
Error attaching to core file: cannot open binary file
sun.jvm.hotspot.debugger.DebuggerException: cannot open binary file
        at sun.jvm.hotspot.debugger.linux.LinuxDebuggerLocal.attach0(Native Method)
        at sun.jvm.hotspot.debugger.linux.LinuxDebuggerLocal.attach(LinuxDebuggerLocal.java:286)
        at sun.jvm.hotspot.HotSpotAgent.attachDebugger(HotSpotAgent.java:673)
        at sun.jvm.hotspot.HotSpotAgent.setupDebuggerLinux(HotSpotAgent.java:611)
        at sun.jvm.hotspot.HotSpotAgent.setupDebugger(HotSpotAgent.java:337)
        at sun.jvm.hotspot.HotSpotAgent.go(HotSpotAgent.java:304)
        at sun.jvm.hotspot.HotSpotAgent.attach(HotSpotAgent.java:156)
        at sun.jvm.hotspot.tools.Tool.start(Tool.java:191)
        at sun.jvm.hotspot.tools.Tool.execute(Tool.java:118)
        at sun.jvm.hotspot.tools.JStack.main(JStack.java:92)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at sun.tools.jstack.JStack.runJStackTool(JStack.java:140)
        at sun.tools.jstack.JStack.main(JStack.java:106)

[root@rock1 ~]# jstack 26304
2022-07-31 22:17:03
Full thread dump Java HotSpot(TM) 64-Bit Server VM (25.202-b08 mixed mode):

"Attach Listener" #15234 daemon prio=9 os_prio=0 tid=0x00007f09f0002000 nid=0x95c waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"Keep-Alive-Timer" #15233 daemon prio=8 os_prio=0 tid=0x00007f09a4003800 nid=0x54a waiting on condition [0x00007f09e4173000]
   java.lang.Thread.State: TIMED_WAITING (sleeping)
        at java.lang.Thread.sleep(Native Method)
        at sun.net.www.http.KeepAliveCache.run(KeepAliveCache.java:172)
        at java.lang.Thread.run(Thread.java:748)

"boundedElastic-86" #13412 daemon prio=5 os_prio=0 tid=0x00007f09c851e000 nid=0x1377 waiting on condition [0x00007f09806f0000]
   java.lang.Thread.State: WAITING (parking)
        at sun.misc.Unsafe.park(Native Method)
        - parking to wait for  <0x00000006cc315ae0> (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
        at java.util.concurrent.locks.LockSupport.park(LockSupport.java:175)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.await(AbstractQueuedSynchronizer.java:2039)
        at java.util.concurrent.ScheduledThreadPoolExecutor$DelayedWorkQueue.take(ScheduledThreadPoolExecutor.java:1081)
        at java.util.concurrent.ScheduledThreadPoolExecutor$DelayedWorkQueue.take(ScheduledThreadPoolExecutor.java:809)
        at java.util.concurrent.ThreadPoolExecutor.getTask(ThreadPoolExecutor.java:1074)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1134)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
        at java.lang.Thread.run(Thread.java:748)

"boundedElastic-90" #13413 daemon prio=5 os_prio=0 tid=0x00007f09cc01f800 nid=0x1376 waiting on condition [0x00007f09807f1000]
   java.lang.Thread.State: WAITING (parking)
        at sun.misc.Unsafe.park(Native Method)
        - parking to wait for  <0x00000006cc3160c8> (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
        at java.util.concurrent.locks.LockSupport.park(LockSupport.java:175)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.await(AbstractQueuedSynchronizer.java:2039)
        at java.util.concurrent.ScheduledThreadPoolExecutor$DelayedWorkQueue.take(ScheduledThreadPoolExecutor.java:1081)
        at java.util.concurrent.ScheduledThreadPoolExecutor$DelayedWorkQueue.take(ScheduledThreadPoolExecutor.java:809)
        at java.util.concurrent.ThreadPoolExecutor.getTask(ThreadPoolExecutor.java:1074)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1134)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
        at java.lang.Thread.run(Thread.java:748)

"boundedElastic-88" #13411 daemon prio=5 os_prio=0 tid=0x00007f09b853e800 nid=0x1375 waiting on condition [0x00007f09808f2000]
   java.lang.Thread.State: WAITING (parking)
        at sun.misc.Unsafe.park(Native Method)
        - parking to wait for  <0x00000006cc315508> (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
        at java.util.concurrent.locks.LockSupport.park(LockSupport.java:175)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.await(AbstractQueuedSynchronizer.java:2039)
        at java.util.concurrent.ScheduledThreadPoolExecutor$DelayedWorkQueue.take(ScheduledThreadPoolExecutor.java:1081)
        at java.util.concurrent.ScheduledThreadPoolExecutor$DelayedWorkQueue.take(ScheduledThreadPoolExecutor.java:809)
        at java.util.concurrent.ThreadPoolExecutor.getTask(ThreadPoolExecutor.java:1074)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1134)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
        at java.lang.Thread.run(Thread.java:748)

"boundedElastic-89" #13410 daemon prio=5 os_prio=0 tid=0x00007f09a0017800 nid=0x1374 waiting on condition [0x00007f09e4375000]
   java.lang.Thread.State: WAITING (parking)
        at sun.misc.Unsafe.park(Native Method)
        - parking to wait for  <0x00000006cc3166b0> (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
        at java.util.concurrent.locks.LockSupport.park(LockSupport.java:175)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.await(AbstractQueuedSynchronizer.java:2039)
        at java.util.concurrent.ScheduledThreadPoolExecutor$DelayedWorkQueue.take(ScheduledThreadPoolExecutor.java:1081)
        at java.util.concurrent.ScheduledThreadPoolExecutor$DelayedWorkQueue.take(ScheduledThreadPoolExecutor.java:809)
        at java.util.concurrent.ThreadPoolExecutor.getTask(ThreadPoolExecutor.java:1074)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1134)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
        at java.lang.Thread.run(Thread.java:748)

"boundedElastic-87" #13409 daemon prio=5 os_prio=0 tid=0x00007f09cc01e800 nid=0x1373 waiting on condition [0x00007f09e4274000]
   java.lang.Thread.State: WAITING (parking)
        at sun.misc.Unsafe.park(Native Method)
        - parking to wait for  <0x00000006cc314f20> (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
        at java.util.concurrent.locks.LockSupport.park(LockSupport.java:175)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.await(AbstractQueuedSynchronizer.java:2039)
        at java.util.concurrent.ScheduledThreadPoolExecutor$DelayedWorkQueue.take(ScheduledThreadPoolExecutor.java:1081)
        at java.util.concurrent.ScheduledThreadPoolExecutor$DelayedWorkQueue.take(ScheduledThreadPoolExecutor.java:809)
        at java.util.concurrent.ThreadPoolExecutor.getTask(ThreadPoolExecutor.java:1074)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1134)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
        at java.lang.Thread.run(Thread.java:748)

"parallel-4" #58 daemon prio=5 os_prio=0 tid=0x00007f09c8162800 nid=0x6867 waiting on condition [0x00007f098fffe000]
   java.lang.Thread.State: WAITING (parking)
        at sun.misc.Unsafe.park(Native Method)
        - parking to wait for  <0x00000006c0e54f88> (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
        at java.util.concurrent.locks.LockSupport.park(LockSupport.java:175)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.await(AbstractQueuedSynchronizer.java:2039)
        at java.util.concurrent.ScheduledThreadPoolExecutor$DelayedWorkQueue.take(ScheduledThreadPoolExecutor.java:1081)
        at java.util.concurrent.ScheduledThreadPoolExecutor$DelayedWorkQueue.take(ScheduledThreadPoolExecutor.java:809)
        at java.util.concurrent.ThreadPoolExecutor.getTask(ThreadPoolExecutor.java:1074)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1134)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
        at java.lang.Thread.run(Thread.java:748)

"parallel-3" #57 daemon prio=5 os_prio=0 tid=0x00007f09b8348000 nid=0x6772 waiting on condition [0x00007f098c6e7000]
   java.lang.Thread.State: WAITING (parking)
        at sun.misc.Unsafe.park(Native Method)
        - parking to wait for  <0x00000006c0e55458> (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
        at java.util.concurrent.locks.LockSupport.park(LockSupport.java:175)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.await(AbstractQueuedSynchronizer.java:2039)
        at java.util.concurrent.ScheduledThreadPoolExecutor$DelayedWorkQueue.take(ScheduledThreadPoolExecutor.java:1081)
        at java.util.concurrent.ScheduledThreadPoolExecutor$DelayedWorkQueue.take(ScheduledThreadPoolExecutor.java:809)
        at java.util.concurrent.ThreadPoolExecutor.getTask(ThreadPoolExecutor.java:1074)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1134)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
        at java.lang.Thread.run(Thread.java:748)

"com.alibaba.nacos.client.Worker.longPolling.fixed-10.189.66.196_8848-dev" #56 daemon prio=5 os_prio=0 tid=0x00007f09b0005800 nid=0x6755 runnable [0x00007f098c7e8000]
   java.lang.Thread.State: RUNNABLE
        at java.net.SocketInputStream.socketRead0(Native Method)
        at java.net.SocketInputStream.socketRead(SocketInputStream.java:116)
        at java.net.SocketInputStream.read(SocketInputStream.java:171)
        at java.net.SocketInputStream.read(SocketInputStream.java:141)
        at java.io.BufferedInputStream.fill(BufferedInputStream.java:246)
        at java.io.BufferedInputStream.read1(BufferedInputStream.java:286)
        at java.io.BufferedInputStream.read(BufferedInputStream.java:345)
        - locked <0x0000000791a15018> (a java.io.BufferedInputStream)
        at sun.net.www.http.HttpClient.parseHTTPHeader(HttpClient.java:735)
        at sun.net.www.http.HttpClient.parseHTTP(HttpClient.java:678)
        at sun.net.www.protocol.http.HttpURLConnection.getInputStream0(HttpURLConnection.java:1587)
        - locked <0x0000000791a0fd88> (a sun.net.www.protocol.http.HttpURLConnection)
        at sun.net.www.protocol.http.HttpURLConnection.getInputStream(HttpURLConnection.java:1492)
        - locked <0x0000000791a0fd88> (a sun.net.www.protocol.http.HttpURLConnection)
        at java.net.HttpURLConnection.getResponseCode(HttpURLConnection.java:480)
        at com.alibaba.nacos.common.http.client.response.JdkHttpClientResponse.getStatusCode(JdkHttpClientResponse.java:75)
        at com.alibaba.nacos.common.http.client.handler.AbstractResponseHandler.handle(AbstractResponseHandler.java:43)
        at com.alibaba.nacos.common.http.client.NacosRestTemplate.execute(NacosRestTemplate.java:483)
        at com.alibaba.nacos.common.http.client.NacosRestTemplate.postForm(NacosRestTemplate.java:407)
        at com.alibaba.nacos.client.config.http.ServerHttpAgent.httpPost(ServerHttpAgent.java:155)
        at com.alibaba.nacos.client.config.http.MetricsHttpAgent.httpPost(MetricsHttpAgent.java:68)
        at com.alibaba.nacos.client.config.impl.ClientWorker.checkUpdateConfigStr(ClientWorker.java:441)
        at com.alibaba.nacos.client.config.impl.ClientWorker.checkUpdateDataIds(ClientWorker.java:408)
        at com.alibaba.nacos.client.config.impl.ClientWorker$LongPollingRunnable.run(ClientWorker.java:596)
        at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:511)
        at java.util.concurrent.FutureTask.run(FutureTask.java:266)
        at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.access$201(ScheduledThreadPoolExecutor.java:180)
        at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.run(ScheduledThreadPoolExecutor.java:293)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
        at java.lang.Thread.run(Thread.java:748)

"parallel-2" #55 daemon prio=5 os_prio=0 tid=0x00007f09cc023000 nid=0x6738 waiting on condition [0x00007f098c8e9000]
   java.lang.Thread.State: WAITING (parking)
        at sun.misc.Unsafe.park(Native Method)
        - parking to wait for  <0x00000006c0e55908> (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
        at java.util.concurrent.locks.LockSupport.park(LockSupport.java:175)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.await(AbstractQueuedSynchronizer.java:2039)
        at java.util.concurrent.ScheduledThreadPoolExecutor$DelayedWorkQueue.take(ScheduledThreadPoolExecutor.java:1081)
        at java.util.concurrent.ScheduledThreadPoolExecutor$DelayedWorkQueue.take(ScheduledThreadPoolExecutor.java:809)
        at java.util.concurrent.ThreadPoolExecutor.getTask(ThreadPoolExecutor.java:1074)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1134)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
        at java.lang.Thread.run(Thread.java:748)

"com.alibaba.nacos.client.Worker.longPolling.fixed-10.189.66.196_8848-dev" #54 daemon prio=5 os_prio=0 tid=0x00007f09b4004000 nid=0x672f waiting on condition [0x00007f098c9ea000]
   java.lang.Thread.State: WAITING (parking)
        at sun.misc.Unsafe.park(Native Method)
        - parking to wait for  <0x00000006c057e3e0> (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
        at java.util.concurrent.locks.LockSupport.park(LockSupport.java:175)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.await(AbstractQueuedSynchronizer.java:2039)
        at java.util.concurrent.ScheduledThreadPoolExecutor$DelayedWorkQueue.take(ScheduledThreadPoolExecutor.java:1081)
        at java.util.concurrent.ScheduledThreadPoolExecutor$DelayedWorkQueue.take(ScheduledThreadPoolExecutor.java:809)
        at java.util.concurrent.ThreadPoolExecutor.getTask(ThreadPoolExecutor.java:1074)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1134)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
        at java.lang.Thread.run(Thread.java:748)

"reactor-http-epoll-4" #53 daemon prio=5 os_prio=0 tid=0x00007f0988056000 nid=0x670c runnable [0x00007f098daec000]
   java.lang.Thread.State: RUNNABLE
        at io.netty.channel.epoll.Native.epollWait(Native Method)
        at io.netty.channel.epoll.Native.epollWait(Native.java:193)
        at io.netty.channel.epoll.Native.epollWait(Native.java:186)
        at io.netty.channel.epoll.EpollEventLoop.epollWaitNoTimerChange(EpollEventLoop.java:290)
        at io.netty.channel.epoll.EpollEventLoop.run(EpollEventLoop.java:347)
        at io.netty.util.concurrent.SingleThreadEventExecutor$4.run(SingleThreadEventExecutor.java:986)
        at io.netty.util.internal.ThreadExecutorMap$2.run(ThreadExecutorMap.java:74)
        at io.netty.util.concurrent.FastThreadLocalRunnable.run(FastThreadLocalRunnable.java:30)
        at java.lang.Thread.run(Thread.java:748)

"reactor-http-epoll-3" #52 daemon prio=5 os_prio=0 tid=0x00007f0988055000 nid=0x670b runnable [0x00007f098dbed000]
   java.lang.Thread.State: RUNNABLE
        at io.netty.channel.epoll.Native.epollWait(Native Method)
        at io.netty.channel.epoll.Native.epollWait(Native.java:193)
        at io.netty.channel.epoll.Native.epollWait(Native.java:186)
        at io.netty.channel.epoll.EpollEventLoop.epollWaitNoTimerChange(EpollEventLoop.java:290)
        at io.netty.channel.epoll.EpollEventLoop.run(EpollEventLoop.java:347)
        at io.netty.util.concurrent.SingleThreadEventExecutor$4.run(SingleThreadEventExecutor.java:986)
        at io.netty.util.internal.ThreadExecutorMap$2.run(ThreadExecutorMap.java:74)
        at io.netty.util.concurrent.FastThreadLocalRunnable.run(FastThreadLocalRunnable.java:30)
        at java.lang.Thread.run(Thread.java:748)

"pool-5-thread-1" #51 daemon prio=5 os_prio=0 tid=0x00007f098800e800 nid=0x670a waiting on condition [0x00007f098deee000]
   java.lang.Thread.State: WAITING (parking)
        at sun.misc.Unsafe.park(Native Method)
        - parking to wait for  <0x00000006c0d00e90> (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
        at java.util.concurrent.locks.LockSupport.park(LockSupport.java:175)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.await(AbstractQueuedSynchronizer.java:2039)
        at java.util.concurrent.ScheduledThreadPoolExecutor$DelayedWorkQueue.take(ScheduledThreadPoolExecutor.java:1081)
        at java.util.concurrent.ScheduledThreadPoolExecutor$DelayedWorkQueue.take(ScheduledThreadPoolExecutor.java:809)
        at java.util.concurrent.ThreadPoolExecutor.getTask(ThreadPoolExecutor.java:1074)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1134)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
        at java.lang.Thread.run(Thread.java:748)

run(ThreadPoolExecutor.java:624)
        at java.lang.Thread.run(Thread.java:748)



"DestroyJavaVM" #45 prio=5 os_prio=0 tid=0x00007f0a28009800 nid=0x66c1 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE


[root@rock1 ~]# jmap 26304
Attaching to process ID 26304, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 25.202-b08
0x0000000000400000      8K      /data/java/jdk/bin/java
0x00007f09e4889000      91K     /data/java/jdk/jre/lib/amd64/libnio.so
0x00007f0a116c5000      50K     /data/java/jdk/jre/lib/amd64/libmanagement.so
0x00007f0a11bd0000      114K    /data/java/jdk/jre/lib/amd64/libnet.so
0x00007f0a2cff6000      124K    /data/java/jdk/jre/lib/amd64/libzip.so
0x00007f0a2d212000      60K     /usr/lib64/libnss_files-2.17.so
0x00007f0a2d425000      222K    /data/java/jdk/jre/lib/amd64/libjava.so
0x00007f0a2d653000      64K     /data/java/jdk/jre/lib/amd64/libverify.so
0x00007f0a2d862000      42K     /usr/lib64/librt-2.17.so
0x00007f0a2da6a000      1110K   /usr/lib64/libm-2.17.so
0x00007f0a2dd6c000      16645K  /data/java/jdk/jre/lib/amd64/server/libjvm.so
0x00007f0a2ed55000      2106K   /usr/lib64/libc-2.17.so
0x00007f0a2f123000      18K     /usr/lib64/libdl-2.17.so
0x00007f0a2f327000      106K    /data/java/jdk/lib/amd64/jli/libjli.so
0x00007f0a2f53f000      138K    /usr/lib64/libpthread-2.17.so
0x00007f0a2f75b000      159K    /usr/lib64/ld-2.17.so
[root@rock1 ~]# jstat -gc
invalid argument count
Usage: jstat -help|-options
       jstat -<option> [-t] [-h<lines>] <vmid> [<interval> [<count>]]

Definitions:
  <option>      An option reported by the -options option
  <vmid>        Virtual Machine Identifier. A vmid takes the following form:
                     <lvmid>[@<hostname>[:<port>]]
                Where <lvmid> is the local vm identifier for the target
                Java virtual machine, typically a process id; <hostname> is
                the name of the host running the target Java virtual machine;
                and <port> is the port number for the rmiregistry on the
                target host. See the jvmstat documentation for a more complete
                description of the Virtual Machine Identifier.
  <lines>       Number of samples between header lines.
  <interval>    Sampling interval. The following forms are allowed:
                    <n>["ms"|"s"]
                Where <n> is an integer and the suffix specifies the units as
                milliseconds("ms") or seconds("s"). The default units are "ms".
  <count>       Number of samples to take before terminating.
  -J<flag>      Pass <flag> directly to the runtime system.
[root@rock1 ~]# jstat -gc 26304
 S0C    S1C    S0U    S1U      EC       EU        OC         OU       MC     MU    CCSC   CCSU   YGC     YGCT    FGC    FGCT     GCT
1024.0 1024.0 544.0   0.0   696832.0 15234.8  1651712.0   202184.5  57984.0 54181.7 7552.0 6809.2   1502    9.077   2      0.120    9.197
[root@rock1 ~]# jstat -gccapcity 26304
Unknown option: -gccapcity
Usage: jstat -help|-options
       jstat -<option> [-t] [-h<lines>] <vmid> [<interval> [<count>]]

Definitions:
  <option>      An option reported by the -options option
  <vmid>        Virtual Machine Identifier. A vmid takes the following form:
                     <lvmid>[@<hostname>[:<port>]]
                Where <lvmid> is the local vm identifier for the target
                Java virtual machine, typically a process id; <hostname> is
                the name of the host running the target Java virtual machine;
                and <port> is the port number for the rmiregistry on the
                target host. See the jvmstat documentation for a more complete
                description of the Virtual Machine Identifier.
  <lines>       Number of samples between header lines.
  <interval>    Sampling interval. The following forms are allowed:
                    <n>["ms"|"s"]
                Where <n> is an integer and the suffix specifies the units as
                milliseconds("ms") or seconds("s"). The default units are "ms".
  <count>       Number of samples to take before terminating.
  -J<flag>      Pass <flag> directly to the runtime system.
[root@rock1 ~]# jstat -gccapacity 26304
 NGCMN    NGCMX     NGC     S0C   S1C       EC      OGCMN      OGCMX       OGC         OC       MCMN     MCMX      MC     CCSMN    CCSMX     CCSC    YGC    FGC
698880.0 1397760.0 698880.0 1024.0 1024.0 696832.0  1398272.0  2796544.0  1651712.0  1651712.0      0.0 1099776.0  57984.0      0.0 1048576.0   7552.0   1502     2
[root@rock1 ~]# jstat -printcompilation 26304
Compiled  Size  Type Method
   15430     21    1 reactor/core/publisher/Operators$MultiSubscriptionSubscriber onError
[root@rock1 ~]# jstat -gcmetacapacity 26304
   MCMN       MCMX        MC       CCSMN      CCSMX       CCSC     YGC   FGC    FGCT     GCT
       0.0  1099776.0    57984.0        0.0  1048576.0     7552.0  1502     2    0.120    9.197
[root@rock1 ~]# jinfo 26304
Attaching to process ID 26304, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 25.202-b08
Java System Properties:

java.runtime.name = Java(TM) SE Runtime Environment
java.vm.version = 25.202-b08
sun.boot.library.path = /data/java/jdk/jre/lib/amd64
java.protocol.handler.pkgs = org.springframework.boot.loader
java.vendor.url = http://java.oracle.com/
java.vm.vendor = Oracle Corporation
path.separator = :
file.encoding.pkg = sun.io
java.vm.name = Java HotSpot(TM) 64-Bit Server VM
sun.os.patch.level = unknown
sun.java.launcher = SUN_STANDARD
user.country = US
user.dir = /home/app/gateway
java.vm.specification.name = Java Virtual Machine Specification
PID = 26304
java.runtime.version = 1.8.0_202-b08
java.awt.graphicsenv = sun.awt.X11GraphicsEnvironment
JM.LOG.PATH = /root/logs
os.arch = amd64
java.endorsed.dirs = /data/java/jdk/jre/lib/endorsed
CONSOLE_LOG_CHARSET = UTF-8
line.separator =

java.io.tmpdir = /tmp
java.vm.specification.vendor = Oracle Corporation
os.name = Linux
FILE_LOG_CHARSET = UTF-8
sun.jnu.encoding = UTF-8
java.library.path = /usr/java/packages/lib/amd64:/usr/lib64:/lib64:/lib:/usr/lib
spring.beaninfo.ignore = true
java.specification.name = Java Platform API Specification
java.class.version = 52.0
sun.management.compiler = HotSpot 64-Bit Tiered Compilers
os.version = 3.10.0-862.el7.x86_64
user.home = /root
user.timezone = America/New_York
java.awt.printerjob = sun.print.PSPrinterJob
file.encoding = UTF-8
java.specification.version = 1.8
user.name = root
java.class.path = gateway.jar
java.vm.specification.version = 1.8
sun.arch.data.model = 64
sun.java.command = gateway.jar
java.home = /data/java/jdk/jre
user.language = en
java.specification.vendor = Oracle Corporation
awt.toolkit = sun.awt.X11.XToolkit
java.vm.info = mixed mode
java.version = 1.8.0_202
java.ext.dirs = /data/java/jdk/jre/lib/ext:/usr/java/packages/lib/ext
sun.boot.class.path = /data/java/jdk/jre/lib/resources.jar:/data/java/jdk/jre/lib/rt.jar:/data/java/jdk/jre/lib/sunrsasign.jar:/data/java/jdk/jre/lib/jsse.jar:/data/java/jdk/jre/lib/jce.jar:/data/java/jdk/jre/lib/charsets.jar:/data/java/jdk/jre/lib/jfr.jar:/data/java/jdk/jre/classes
java.awt.headless = true
java.vendor = Oracle Corporation
file.separator = /
java.vendor.url.bug = http://bugreport.sun.com/bugreport/
sun.io.unicode.encoding = UnicodeLittle
sun.cpu.endian = little
sun.cpu.isalist =

VM Flags:
Non-default VM flags: -XX:CICompilerCount=3 -XX:InitialHeapSize=2147483648 -XX:MaxHeapSize=4294967296 -XX:MaxNewSize=1431306240 -XX:MinHeapDeltaBytes=524288 -XX:NewSize=715653120 -XX:OldSize=1431830528 -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseFastUnorderedTimeStamps -XX:+UseParallelGC
Command line:  -Xms2048m -Xmx4096m

[root@rock1 ~]# ps -ef | grep java
root      5821  1665  0 10:39 pts/1    00:00:00 grep --color=auto java
root     26304     1  0 Feb16 ?        05:37:38 java -jar -Xms2048m -Xmx4096m gateway.jar
[root@rock1 ~]# ps -aux | grep java
root      5835  0.0  0.0 112812   976 pts/1    S+   10:39   0:00 grep --color=auto java
root     26304  0.1 14.4 8112368 1284168 ?     Sl   Feb16 337:38 java -jar -Xms2048m -Xmx4096m gateway.jar
```









































[JVM命令大全_yesirwu的博客-CSDN博客](https://blog.csdn.net/yesirwu/article/details/104064304)

[JAVA 线上故障排查完整套路！牛掰！ - 腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1633434)

























