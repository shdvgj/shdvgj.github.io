---
title: Java面试八股文知识点随笔
date: 2022-06-02 11:14:41
comments: true
tags:
- 面试
categories: 
- 学习
---

## 复习关键字

- [ ] new对象的过程
- [ ] 类加载过程
    - [ ] 双亲委派机制
    - [ ] 类加载器
- [ ] Synchronized，Reentrantlock和AQS
- [ ] Java新版本特性
- [ ] 集合源码
    - [ ] 红黑树
- [ ] 两种代理模式
- [ ] IO模型，三种javaIO模型
- [ ] 数据库主从分离方式
    - [ ] 代理方式
    - [ ] 组件方式

## 框架了解

- [ ] 注册中心 [Zookeeper](https://github.com/apache/zookeeper), [Consul](https://github.com/hashicorp/consul), [Eureka](https://github.com/Netflix/eureka), [Nacos](https://github.com/alibaba/nacos)
- [ ] 客户端/进程内负载均衡器 [Ribbon](https://github.com/Netflix/ribbon), [Nginx](https://github.com/nginx/nginx)
- [ ] Rpc调用 [OpenFeign](https://github.com/spring-cloud/spring-cloud-openfeign), [Dubbo](https://github.com/apache/dubbo), [Grpc](https://github.com/grpc/grpc)
- [ ] 熔断降级 [Hystrix](https://github.com/Netflix/Hystrix), [Sentinel](https://github.com/alibaba/Sentinel)
- [ ] 微服务网关 [Zuul](https://github.com/Netflix/zuul), [Spring Cloud Gateway](https://github.com/spring-cloud/spring-cloud-gateway), [Kong](https://github.com/Kong/kong), **[APISIX](https://github.com/apache/apisix)**, [Shenyu](https://github.com/apache/incubator-shenyu)
- [ ] 配置中心 [Spring Cloud Config](https://github.com/spring-cloud/spring-cloud-config) + [Spring Cloud Bus](https://github.com/spring-cloud/spring-cloud-bus), [Apollo](https://github.com/apolloconfig/apollo)
- [ ] 开源式分布式NoSQL数据库 [Cassandra](https://github.com/apache/cassandra)
- [ ] 分布式ID [Tinyid](https://github.com/didi/tinyid/), Snowflake(twitter的开源库已删，有很多替代方案如美团的[Leaf](https://github.com/Meituan-Dianping/Leaf)，百度的UidGenerator)
- [ ] 分布式Redis [Codis](https://github.com/CodisLabs/codis/)
- [ ] MySQL主从分离中间件 [MySQL Router](https://github.com/mysql/mysql-router)（官方）、[Atlas](https://github.com/Qihoo360/Atlas)（基于 MySQL Proxy）、[Maxscale](https://github.com/mariadb-corporation/MaxScale)、[MyCat](https://github.com/MyCATApache/Mycat-Server)
- [ ] 分库分表 [ShardingSphere](https://github.com/apache/shardingsphere)

## 算法

- KMP算法
- variable-precision SWAR算法
- RAFT算法


<!-- more -->

## [使用 try-with-resources 代替try-catch-finally](https://javaguide.cn/java/basis/java-basic-questions-03.html#%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8-try-with-resources-%E4%BB%A3%E6%9B%BFtry-catch-finally)

适用于java7之后的特性，针对字节流和网络连接之类的对象，不再需要每次在finally中进行close

try-catch-finally风格
```java
//读取文本文件的内容
Scanner scanner = null;
try {
    scanner = new Scanner(new File("D://read.txt"));
    while (scanner.hasNext()) {
        System.out.println(scanner.nextLine());
    }
} catch (FileNotFoundException e) {
    e.printStackTrace();
} finally {
    if (scanner != null) {
        scanner.close();
    }
}
```

try-with-resources风格
```java
try (BufferedInputStream bin = new BufferedInputStream(new FileInputStream(new File("test.txt")));
     BufferedOutputStream bout = new BufferedOutputStream(new FileOutputStream(new File("out.txt")))) {
    int b;
    while ((b = bin.read()) != -1) {
        bout.write(b);
    }
}
catch (IOException e) {
    e.printStackTrace();
}
```

在 Java 9 之后，在 try-with-resources 语句中可以使用 effectively-final 变量。

```java
// effectively-final简单说就是被初始化后从未变更的变量，如下用final修饰符的scanner和未被final修饰的writer，都未曾改变，因此就是effectively-final变量
final Scanner scanner = new Scanner(new File("testRead.txt"));
PrintWriter writer = new PrintWriter(new File("testWrite.txt"))
try (scanner;writer) {
    // omitted
}
```

## [静态方法为什么不能调用非静态成员?](https://javaguide.cn/java/basis/java-basic-questions-01.html#%E9%9D%99%E6%80%81%E6%96%B9%E6%B3%95%E4%B8%BA%E4%BB%80%E4%B9%88%E4%B8%8D%E8%83%BD%E8%B0%83%E7%94%A8%E9%9D%9E%E9%9D%99%E6%80%81%E6%88%90%E5%91%98)

简单来说静态方法初始化在类加载时，非静态成员初始化于new 实例对象时

## [深拷贝和浅拷贝区别了解吗？什么是引用拷贝？](https://javaguide.cn/java/basis/java-basic-questions-02.html#%E6%B7%B1%E6%8B%B7%E8%B4%9D%E5%92%8C%E6%B5%85%E6%8B%B7%E8%B4%9D%E5%8C%BA%E5%88%AB%E4%BA%86%E8%A7%A3%E5%90%97-%E4%BB%80%E4%B9%88%E6%98%AF%E5%BC%95%E7%94%A8%E6%8B%B7%E8%B4%9D)
![](javaGuide1.png)


## [为什么重写 equals() 时必须重写 hashCode() 方法？](https://javaguide.cn/java/basis/java-basic-questions-02.html#%E4%B8%BA%E4%BB%80%E4%B9%88%E9%87%8D%E5%86%99-equals-%E6%97%B6%E5%BF%85%E9%A1%BB%E9%87%8D%E5%86%99-hashcode-%E6%96%B9%E6%B3%95)

hashcode相等时，两对象不一定相等。但如果两对象相等，那hashcode必定相等。即hashcode是两对象相等的**必要不充分**条件。

## [为什么 Java 中只有值传递](https://javaguide.cn/java/basis/why-there-only-value-passing-in-java.html)

还可以参见[Java 到底是值传递还是引用传递？ - Hollis的回答 - 知乎](https://www.zhihu.com/question/31203609/answer/576030121)

## [Java 序列化详解](https://javaguide.cn/java/basis/serialization.html)

序列化一般用于数据-文件转换和网络传输
序列化方式
JDK自带 性能较差，一般不用
kyro  高性能
Protobuf  高性能，跨语言，略烦琐，google在用
ProtoStuff  Protobuf升级版，更简洁易用
hessian 较老，跨语言，dubbo在使用hessian2

## [BigDecimal 详解](https://javaguide.cn/java/basis/bigdecimal.html)

![](javaGuide2.png)

## [集合总结](https://javaguide.cn/java/collection/java-collection-questions-01.html#%E9%9B%86%E5%90%88%E6%A6%82%E8%BF%B0)

![](javaGuide3.png)

- List(对付顺序的好帮手): 存储的元素是有序的、可重复的。
- Set(注重独一无二的性质): 存储的元素是无序的、不可重复的。
- Queue(实现排队功能的叫号机): 按特定的排队规则来确定先后顺序，存储的元素是有序的、可重复的。
- Map(用 key 来搜索的专家): 使用键值对（key-value）存储，类似于数学上的函数 y=f(x)，"x" 代表 key，"y" 代表 value，key 是无序的、不可重复的，value 是无序的、可重复的，每个键最多映射到一个值。
- **虽然理论上linkedList的头尾插入和删除比ArrayList更快，但是实际一般都会在中间位置插入，这个时候差别并不大，而ArrayList的其他性能几乎都更好，因此实践中几乎不会用到LinkedList**
- ArrayList 实现了 RandomAccess 接口，而 LinkedList 没有实现。RandomAccess更多是一个标示，其接口内容是空的，本质上是因为ArrayList底层实现是数组，因此支持随机访问，而不是实现了RandomAccess才支持随机访问。

## [HashMap 的长度为什么是 2 的幂次方](https://javaguide.cn/java/collection/java-collection-questions-02.html#hashmap-%E7%9A%84%E9%95%BF%E5%BA%A6%E4%B8%BA%E4%BB%80%E4%B9%88%E6%98%AF-2-%E7%9A%84%E5%B9%82%E6%AC%A1%E6%96%B9)

hash值通过对数组的长度进行取模运算得到在数组中的位置
**取余(%)操作中如果除数是 2 的幂次则等价于与其除数减一的与(&)操作（也就是说 hash%length==hash&(length-1)的前提是 length 是 2 的 n 次方；）**


## [ArrayList中ensurecapacity方法](https://javaguide.cn/java/collection/arraylist-source-code.html#_3-4-ensurecapacity%E6%96%B9%E6%B3%95)

**最好在 add 大量元素之前用 ensureCapacity 方法，以减少增量重新分配的次数**

## [说说线程的生命周期和状态](https://javaguide.cn/java/concurrent/java-concurrent-questions-01.html#%E8%AF%B4%E8%AF%B4%E7%BA%BF%E7%A8%8B%E7%9A%84%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E5%92%8C%E7%8A%B6%E6%80%81)

线程有六种状态：NEW，RUNNABLE，BLOCKED，WAITING，TIME_WAITING，TERMINATED
其中RUNNABLE状态包含READY和RUNNING两种状态，由于切换时间很短，因此并未做区分
![](javaGuide4.png)

## [为什么我们调用 start() 方法时会执行 run() 方法，为什么我们不能直接调用 run() 方法](https://javaguide.cn/java/concurrent/java-concurrent-questions-01.html#%E4%B8%BA%E4%BB%80%E4%B9%88%E6%88%91%E4%BB%AC%E8%B0%83%E7%94%A8-start-%E6%96%B9%E6%B3%95%E6%97%B6%E4%BC%9A%E6%89%A7%E8%A1%8C-run-%E6%96%B9%E6%B3%95-%E4%B8%BA%E4%BB%80%E4%B9%88%E6%88%91%E4%BB%AC%E4%B8%8D%E8%83%BD%E7%9B%B4%E6%8E%A5%E8%B0%83%E7%94%A8-run-%E6%96%B9%E6%B3%95)

调用 start() 方法方可启动线程并使线程进入就绪状态，直接执行 run() 方法的话不会以多线程的方式执行。

## [AQS介绍](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#aqs)

AQS 的全称为（AbstractQueuedSynchronizer），是一个用来构建锁和同步器的框架，使用 AQS 能简单且高效地构造出大量应用广泛的同步器，比如我们提到的 ReentrantLock，Semaphore，其他的诸如 ReentrantReadWriteLock，SynchronousQueue，FutureTask 等等皆是基于 AQS 的。




