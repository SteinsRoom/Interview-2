# 面试知识点总结
* 笔记向，不定期更新。
* 没有太多需要理解的东西，需要理解的部分自行学习。
* 自己总结，如果有错的地方欢迎指正。

# 数据库
1. 索引相关
    1. 引入  
    在数据库中对大量数据进行筛选的话，  
    考虑在 where 及 order by 涉及的列上建立索引。
    2. 什么是索引  
    索引是对数据库表中一列或多列的值进行排序的一种结构，使用索引可快速访问数据库表中的特定信息。
    3. 什么时候使用索引  
    总的来说就是能够提升效率的时候使用索引。  
        1. 在作为主键的列上，唯一性约束的列（不过大部分数据库会自动加索引）；
        2. 在经常用在连接的列上，这些列主要是一些外键，可以加快连接的速度；
        3. 在经常需要排序的列上创建索引，这样查询可以利用索引的排序，加快排序查询时间；
        4. 在经常使用在WHERE子句中的列上面创建索引，加快条件的判断速度。
    4. 什么时候不能使用索引  
    总的来说就是不能够提升效率的时候不使用索引，例如：该列很少进行查找或排序操作
    5. 什么时候索引会失效
        1. 查询的结果太大，性能消耗大于使用索引
        2. where里面存在!=
        3. 模糊查询的时候，通配符在第一个
        4. where查询null值
        
2. in 和 exists
    1. 结论  
    两个大小有差距的表嵌套查询，则子查询表大的用exists，子查询表小的用in
    2. 原因  
    【待补充】
3. 数据库连接方法
    1. DriverManager
    ```java
    Connection conn = DriverManager.getConnection("连接URL","用户名","密码");
    PreparedStatement ps = conn.prepareStatement(sql);
    ps.setInt(index, value);
    ps.executeUpdate()
    //或者
    Statement stmt=conn.createStatement();
    stmt.executeUpdate(sql)...
    ```
    2. 用JDBC连接池，如C3P0

1. 事务（Transaction）
    1. 事务是并发控制的基本单位。所谓的事务，它是一个操作序列，这些操作要么都执行，要么都不执行，它是一个不可分割的工作单位。事务是数据库维护数据一致性的单位，在每个事务结束时，都能保持数据一致性。
    1. 事务的四个属性 
        * 原子性 Atomic  
        就是事务应作为一个工作单元,事务处理完成，所有的工作要么都在数据库中保存下来，要么完全回滚，全部不保留 
        * 一致性 Consistent  
        事务完成或者撤销后，都应该处于一致的状态
        * 隔离性 Isolated  
        多个事务同时进行，它们之间应该互不干扰.应该防止一个事务处理其他事务也要修改的数据时，不合理的存取和不完整的读取数据 
        * 永久性 Durable  
        事务提交以后，所做的工作就被永久的保存下来 


1. 什么是存储过程？有哪些优缺点？  
存储过程是一些预编译的SQL语句。  
更加直白的理解：存储过程可以说是一个记录集，它是由一些T-SQL语句组成的代码块，这些T-SQL语句代码像一个方法一样实现一些功能（对单表或多表的增删改查），然后再给这个代码块取一个名字，在用到这个功能的时候调用他就行了。
    * 存储过程是一个预编译的代码块，执行效率比较高
    * 一个存储过程替代大量T_SQL语句 ，可以降低网络通信量，提高通信速率
    * 可以一定程度上确保数据安全

1. 简单说一说drop、delete与truncate的区别  
   SQL中的drop、delete、truncate都表示删除，但是三者有一些差别
   * delete和truncate只删除表的数据不删除表的结构
   * 速度,一般来说: drop> truncate >delete 
   * delete语句是dml,这个操作会放到rollback segement中,事务提交之后才生效;  
   如果有相应的trigger,执行的时候将被触发. truncate,drop是ddl, 操作立即生效,原数据不放到rollback segment中,不能回滚. 操作不触发trigger.   
 
   使用场景
   * 不再需要一张表的时候，用drop
   * 想删除部分数据行时候，用delete，并且带上where子句
   * 保留表而删除所有数据的时候用truncate

1. 超键、候选键、主键、外键分别是什么？
    * 超键：在关系中能唯一标识元组的属性集称为关系模式的超键。一个属性可以为作为一个超键，多个属性组合在一起也可以作为一个超键。超键包含候选键和主键。
    * 候选键：是最小超键，即没有冗余元素的超键。
    * 主键：数据库表中对储存数据对象予以唯一和完整标识的数据列或属性的组合。一个数据列只能有一个主键，且主键的取值不能缺失，即不能为空值（Null）。
    * 外键：在一个表中存在的另一个表的主键称此表的外键。

1. 什么是视图？以及视图的使用场景有哪些？  
    视图是一种虚拟的表，具有和物理表相同的功能。可以对视图进行增，改，查，操作，试图通常是有一个表或者多个表的行或列的子集。对视图的修改不影响基本表。它使得我们获取数据更容易，相比多表查询。
    * 只暴露部分字段给访问者，所以就建一个虚表，就是视图。
    * 查询的数据来源于不同的表，而查询者希望以统一的方式查询，这样也可以建立一个视图，把多个表查询结果联合起来，查询者只需要直接从视图中获取数据，不必考虑数据来源于不同表所带来的差异

1. 三大范式
    * 第一范式（1NF）：数据库表中的字段都是单一属性的，不可再分。这个单一属性由基本类型构成，包括整型、实数、字符型、逻辑型、日期型等。
    * 第二范式（2NF）：数据库表中不存在非关键字段对任一候选关键字段的部分函数依赖（部分函数依赖指的是存在组合关键字中的某些字段决定非关键字段的情况），也即所有非关键字段都完全依赖于任意一组候选关键字。
    * 第三范式（3NF）：在第二范式的基础上，数据表中如果不存在非关键字段对任一候选关键字段的传递函数依赖则符合第三范式。所谓传递函数依赖，指的是如 果存在"A → B → C"的决定关系，则C传递函数依赖于A。因此，满足第三范式的数据库表应该不存在如下依赖关系： 关键字段 → 非关键字段 x → 非关键字段y

# 项目
1. 如何保证代码质量
    1. 在写之前想好各种边界条件，想清楚细节，设计好程序的总体思路，方便后期维护
    2. 写的时候满足一定的代码规范、命名规范等，在适当的地方进行输出，方便测试的时候不仅仅是看结果也看一些主要过程和自己的预期是否一致。
    3. 写完后做好完善的单元测试
2. 如何找bug
    1. 先输出调试
    2. 再对指定地方进行单步调试
 7. 单点登录  
    【待补充】

# 框架
### Spring
1. spring 的优点？
    1. 降低了组件之间的耦合性;
    2. 提供了众多服务，如事务管理等 
    3. 容器提供单例模式支持 
    4. 容器提供了AOP技术，利用它很容易实现运行期监控等功能 
    6. spring对于主流的应用框架提供了集成支持，如hibernate等 
    7. spring属于低侵入式设计，代码的污染极低 
    8. 独立于各种应用服务器 
    9. spring的IOC机制降低了业务对象替换的复杂性 
    10. Spring的高度开放性，并不强制应用完全依赖于Spring，开发者可以自由选择spring的部分或全部 
2. IOC
在Java开发中，IoC意味着将你设计好的类交给系统去控制，而不是在你的类内部控制。  
举个例子：以前的买东西是你和商家直接交易，而IOC之后，变成了你和淘宝交易，淘宝和商家交易。  
我觉得就是在一个map里面存上类的具体路径，然后反射实现的
3. AOP
这种在运行时，动态地将代码切入到类的指定方法、指定位置上的编程思想就是面向切面的编程。一般都用于日志等地方。
4. spring 的事务管理

### Spring MVC
### Mybatis
### Hibernate

# 计算机网络
1. http
    1. http和https区别  
    HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议，要比http协议安全。  
    HTTPS和HTTP的区别主要如下：
        1. https协议需要到ca申请证书，一般免费证书较少，因而需要一定费用。
        2. http是超文本传输协议，信息是明文传输，https则是具有安全性的ssl加密传输协议。
        3. http和https使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443。
        4. http的连接很简单，是无状态的；HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议，比http协议安全。
    1. 说一下什么是Http协议？  
    对器客户端和服务器端之间数据传输的格式规范，格式简称为“超文本传输协议”。
    2. 什么是Http协议无状态协议？怎么解决Http协议无状态协议？（曾经去某创业公司问到）
        1. 无状态协议对于事务处理没有记忆能力。缺少状态意味着如果后续处理需要前面的信息
        2. 无状态协议解决办法： 通过1、Cookie 2、通过Session会话保存。
    3. 说一下Http协议中302状态  
    http协议中，返回状态码302表示重定向。这种情况下，服务器返回的头部信息中会包含一个 Location 字段，内容是重定向到的url
    4. Http协议有什么组成？
        1. 请求报文 包含三部分：
            1. 请求行：包含请求方法、URI、HTTP版本信息
            1. 请求首部字段
            1. 请求内容实体
        2. 响应报文 包含三部分：
            1. 状态行：包含HTTP版本、状态码、状态码的原因短语
            2. 响应首部字段
            3. 响应内容实体
    5. Http协议中有那些请求方式？
        1. GET： 用于请求访问已经被URI（统一资源标识符）识别的资源，可以通过URL传参给服务器
        2. POST：用于传输信息给服务器，主要功能与GET方法类似，但一般推荐使用POST方式。
        3. PUT： 传输文件，报文主体中包含文件内容，保存到对应URI位置。
        4. HEAD： 获得报文首部，与GET方法类似，只是不返回报文主体，一般用于验证URI是否有效。
        5. DELETE：删除文件，与PUT方法相反，删除对应URI位置的文件。
        6. OPTIONS：查询相应URI支持的HTTP方法。
    6. Http协议中Http1.0与1.1区别？
        1. 在http1.0中，当建立连接后，客户端发送一个请求，服务器端返回一个信息后就关闭连接，当浏览器下次请求的时候又要建立连接，显然这种不断建立连接的方式，会造成很多问题。
        2. 在http1.1中，引入了持续连接的概念，通过这种连接，浏览器可以建立一个连接之后，发送请求并得到返回信息，然后继续发送请求再次等到返回信息，也就是说客户端可以连续发送多个请求，而不用等待每一个响应的到来。
    8. get与post请求区别？
        1. get重点在从服务器上获取资源，post重点在向服务器发送数据；
        2. get传输数据是通过URL请求，post传输数据通过Http的post机制，将字段与对应值封存在请求实体中发送给服务器，这个过程对用户是不可见的；
        3. Get传输的数据量小，因为受URL长度限制，但效率较高；Post可以传输大量数据，所以上传文件时只能用Post方式；
        4. get是不安全的，因为URL是可见的，可能会泄露信息；post较get安全性较高；
    10. 常见Http协议状态？
        * 1**	信息，服务器收到请求，需要请求者继续执行操作
        * 200 - 请求成功
        * 301 - 资源（网页等）被永久转移到其它URL
        * 404 - 请求的资源（网页等）不存在
        * 500 - 内部服务器错误
    13. Http优化
        * 利用负载均衡优化和加速HTTP应用
        * 利用HTTP Cache来优化网站
2. osi七层  
    ![osi](https://raw.githubusercontent.com/llysrv/Interview/master/img/OSI.gif)

3. TCP  
三次握手  
![三次握手](https://raw.githubusercontent.com/llysrv/Interview/master/img/%E4%B8%89%E6%AC%A1%E6%8F%A1%E6%89%8B.png)  
四次断开  
因为之前存在没有传送完的数据，所以要等待。  
![四次断开](https://raw.githubusercontent.com/llysrv/Interview/master/img/%E5%9B%9B%E6%AC%A1%E6%8C%A5%E6%89%8B.jpg)  
小故事  
三次握手：  
A：喂，你听得到吗？  
B：我听得到呀，你听得到我吗？  
A：我能听到你，balabala……  
四次挥手：  
A：喂，我不说了。
B：我知道了。等下，上一句还没说完。balabala…..  
B：好了，说完了，我也不说了。  
A：我知道了。  
A等待2MSL,保证B收到了消息,否则重说一次”我知道了”(如果没收到将进行重传)  
4. TCP与UDP之间的区别
    1. 基于连接vs无连接  
    TCP是面向连接的协议，而UDP是无连接的协议。
    2. 可靠性不同  
        * TCP提供交付保证，这意味着一个使用TCP协议发送的消息是保证交付给客户端的。
        * UDP是不可靠的，它不提供任何交付的保证。一个数据报包在运输途中可能会丢失。
    3. 有序性
        * TCP保证了消息的有序性，该消息将以从服务器端发出的同样的顺序发送到客户端，
        * UDP不提供任何有序性或序列性的保证，数据包将以任何可能的顺序到达。
    4. 数据边界【不理解。。】  
        TCP不保存数据的边界，而UDP保证。
        在传输控制协议，数据以字节流的形式发送，
        并没有明显的标志表明传输信号消息（段）的边界。
        在UDP中，数据包单独发送的，只有当他们到达时，才会再次集成。包有明确的界限来哪些包已经收到，这意味着在消息发送后，在接收器接口将会有一个读操作，来生成一个完整的消息。虽然TCP也将在收集所有字节之后生成一个完整的消息，但是这些信息在传给传输给接受端之前将储存在TCP缓冲区，以确保更好的使用网络带宽
    5. 速度  
    因为TCP必须创建连接，以保证消息的可靠交付和有序性，他需要做比UDP多的多。
    6. 重量级vs轻量级  
    由于上述的开销，TCP被认为是重量级的协议，而与之相比，UDP协议则是一个轻量级的协议。因为UDP传输的信息中不承担任何间接创造连接，保证交货或秩序的的信息。这也反映在用于承载元数据的头的大小。
    7. 头大小  
    TCP具有比UDP更大的头。
    8. 流量控制  
        * TCP有流量控制。在任何用户数据可以被发送之前，TCP需要三数据包来设置一个套接字连接。TCP处理的可靠性和拥塞控制。
        * UDP不能进行流量控制。
# Java

### JVM
1. Java GC如何判断对象是否为垃圾  
根搜索算法，根：
    1. 虚拟机栈中引用的对象（本地变量表）
    2. 方法区中静态属性引用的对象
    3. 方法区中常量引用的对象
    4. 本地方法栈中引用的对象（Native对象）

16. 内存模型 为什么要设置工作内存和主内存  
    【待补充】
17. GC的过程 当发现虚拟机频繁GC时应该怎么办？  
    【待补充】  
JVM调优
### 并发
1. 乐观锁，悲观锁
    1. 乐观锁  
    乐观锁假设认为数据一般情况下不会造成冲突，所以在数据进行提交更新的时候，才会正式对数据的冲突与否进行检测，如果发现冲突了，则让返回用户错误的信息，让用户决定如何去做。
    2. 悲观锁  
    指的是对数据被外界（包括本系统当前的其他事务，以及来自外部系统的事务处理）修改持保守态度，因此，在整个数据处理过程中，将数据处于锁定状态。悲观锁的实现，往往依靠数据库提供的锁机制

15. volatile关键字的作用  
    保证变量的可见性

### 其他
1. 优化反射性能
    1. 利用缓存
    2. 对Field、Method、Constructor进行setAccessible(true);

11. 什么时候用接口、什么时候用抽象类 
    1. 表达is-a关系的时候用抽象类
    2. 表达like-a关系的时候用接口
    
12. List的实现类
    1. LinkedList 
    2. ArrayList 默认自动扩容1.5倍
    
13. Map的实现类
    1. HashMap  
    先hash，hash冲突的用链表连起来（jdk1.8 当链表长度超过8时，链表变成红黑树）
    2. TreeMap  
    红黑树
    3. LinkedHashMap  
    默认按照插入排序，  
    accessOrder为true时，记录访问顺序，最近访问的放在最后
    4. hashTable 与 concurrentHashMap 区别
        1. HashTable每次同步执行的时候都要锁住整个结构
        2. ConcurrentHashMap锁的方式是稍微细粒度的，ConcurrentHashMap里面的操作只锁当前数据所在的桶

1. Servlet的生命周期，是否是线程安全的？  
    * Servlet 生命周期可被定义为从创建直到毁灭的整个过程。以下是 Servlet 遵循的过程：
        1. Servlet 通过调用 init () 方法进行初始化。
        2. Servlet 调用 service() 方法来处理客户端的请求。  
        service() 方法检查 HTTP 请求类型（GET、POST、PUT、DELETE 等），并在适当的时候调用 doGet、doPost、doPut，doDelete 等方法。
        3. Servlet 通过调用 destroy() 方法终止（结束）。
        4. 最后，Servlet 是由 JVM 的垃圾回收器进行垃圾回收的。
    * 不是线程安全的，因为Servlet只会被实例化一次。如果Servlet中含有可变的域


# 操作系统
1. 请分别简单说一说进程和线程以及它们的区别。
    * 进程是具有一定功能的程序关于某个数据集合上的一次运行活动，进程是系统进行资源调度和分配的一个独立单位。
    * 线程是进程的实体，是CPU调度和分派的基本单位，它是比进程更小的能独立运行的基本单位。
    * 一个进程可以有多个线程，多个线程也可以并发执行
2. 线程同步的方式有哪些？
    * 互斥量：采用互斥对象机制，只有拥有互斥对象的线程才有访问公共资源的权限。因为互斥对象只有一个，所以可以保证公共资源不会被多个线程同时访问。
    * 信号量：它允许同一时刻多个线程访问同一资源，但是需要控制同一时刻访问此资源的最大线程数量。
    * 事件（信号）：通过通知操作的方式来保持多线程同步，还可以方便的实现多线程优先级的比较操作。

3. 进程的通信方式有哪些？  
主要分为：管道、系统IPC（包括消息队列、信号量、共享存储）、SOCKET  
管道主要分为：普通管道PIPE 、流管道（s_pipe）、命名管道（name_pipe）  
    * 管道是一种半双工的通信方式，数据只能单项流动，并且只能在具有亲缘关系的进程间流动，进程的亲缘关系通常是父子进程
    * 命名管道也是半双工的通信方式，它允许无亲缘关系的进程间进行通信
    * 信号量是一个计数器，用来控制多个进程对资源的访问，它通常作为一种锁机制。
    * 消息队列是消息的链表，存放在内核中并由消息队列标识符标识。
    * 信号是一种比较复杂的通信方式，用于通知接收进程某个事件已经发生。
    * 共享内存就是映射一段能被其它进程访问的内存，这段共享内存由一个进程创建，但是多个进程可以访问。

4. 什么是缓冲区溢出？有什么危害？其原因是什么？  
缓冲区溢出是指当计算机向缓冲区填充数据时超出了缓冲区本身的容量，溢出的数据覆盖在合法数据上。  
危害有以下两点：
    * 程序崩溃，导致拒绝额服务
    * 跳转并且执行一段恶意代码  
    
造成缓冲区溢出的主要原因是程序中没有仔细检查用户输入。 

5. 什么是死锁？死锁产生的条件？  
在两个或者多个并发进程中，如果每个进程持有某种资源而又等待其它进程释放它或它们现在保持着的资源，在未改变这种状态之前都不能向前推进，称这一组进程产生了死锁。通俗的讲就是两个或多个进程无限期的阻塞、相互等待的一种状态。  
死锁产生的四个条件（有一个条件不成立，则不会产生死锁）
    * 互斥条件：一个资源一次只能被一个进程使用
    * 请求与保持条件：一个进程因请求资源而阻塞时，对已获得资源保持不放
    * 不剥夺条件：进程获得的资源，在未完全使用完之前，不能强行剥夺
    * 循环等待条件：若干进程之间形成一种头尾相接的环形等待资源关系 

6. 进程有哪几种状态？
    * 就绪状态：进程已获得除处理机以外的所需资源，等待分配处理机资源
    * 运行状态：占用处理机资源运行，处于此状态的进程数小于等于CPU数
    * 阻塞状态： 进程等待某种条件，在条件满足之前无法执行 

7. 分页和分段有什么区别？
    * 段是信息的逻辑单位，它是根据用户的需要划分的，因此段对用户是可见的 ；页是信息的物理单位，是为了管理主存的方便而划分的，对用户是透明的。
    * 段的大小不固定，有它所完成的功能决定；页大大小固定，由系统决定
    * 段向用户提供二维地址空间；页向用户提供的是一维地址空间
    * 段是信息的逻辑单位，便于存储保护和信息的共享，页的保护和共享受到限制。
8. 操作系统中进程调度策略有哪几种？
    * FCFS(先来先服务)
    * 优先级
    * 时间片轮转
    * 多级反馈
9. 说一说进程同步有哪几种机制。
    * 原子操作
    * 信号量机制
    * 自旋锁管程
    * 会合
    * 分布式系统
1. 说一说死锁的处理基本策略和常用方法。  
    * 解决死锁的基本方法如下：  
    预防死锁、避免死锁、检测死锁、解除死锁   
    * 解决四多的常用策略如下：   
    鸵鸟策略、预防策略、避免策略、检测与解除死锁 
    
# 设计模式
    【待补充】

# 小算法
只总结一些我接触的少的算法。  
1. Reservoir Sampling  
    【待补充】

