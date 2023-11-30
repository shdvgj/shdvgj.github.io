---
title: dubbo以及zookeeper分布式锁
date: 2023-11-20 11:16:13
categories: 
- 学习
tags:
- 分布式
- SpringBoot
- dubbo
- zookeeper
---

### dubbo序列化
Dubbo支持多种序列化方式，用于在网络传输中将对象进行序列化和反序列化。下面介绍Dubbo中常用的几种序列化方式：

1. Hessian：Hessian是一种基于二进制的序列化协议，它的优点是序列化后的数据体积相对较小，网络传输效率较高。Dubbo默认使用Hessian进行序列化。
2. Java原生序列化：Dubbo也支持使用Java原生的序列化方式（java.io.Serializable）。这种方式简单易用，但序列化后的数据体积较大，传输效率较低。
3. JSON：Dubbo可以使用JSON作为序列化方式。JSON是一种文本格式的序列化协议，可以在不同语言之间进行数据交换。它的优点是可读性好，方便调试和跨语言通信。但相比于二进制序列化方式，JSON的序列化和反序列化效率较低。
4. Protobuf：Protobuf（Protocol Buffers）是Google开发的一种二进制序列化协议，具有高效、紧凑和跨语言等特点。Dubbo支持使用Protobuf进行序列化，可以在数据传输中节省带宽和存储空间。
5. FST：FST（Fast Serialization）是一种快速的Java序列化/反序列化库，它提供了高性能和紧凑的序列化格式。FST在Dubbo中也被用作一种可选的序列化方式。
6. Kryo：Kryo是另一种高性能的二进制序列化库，它被广泛用于Java领域。Dubbo也支持使用Kryo进行序列化。

<!-- more -->
自定义序列化：除了Dubbo支持的默认序列化方式之外，你还可以通过实现Dubbo的Serialization接口来开发自定义的序列化方式。通过自定义序列化，你可以根据业务需求进行优化，例如选择特定的序列化库、自定义序列化算法等。

性能比较：不同的序列化方式在性能上可能会有所差异。一般来说，基于二进制的序列化方式（如Hessian、Protobuf、FST、Kryo）在性能上往往优于基于文本的序列化方式（如JSON、Java原生序列化）。但具体的性能表现还会受到多个因素的影响，比如对象复杂度、序列化和反序列化的速度、数据体积等。

在Dubbo中，可以通过配置来选择使用哪种序列化方式。例如，在服务提供者和消费者的Dubbo配置中，可以通过`serialization`属性来指定序列化方式，如：
```xml
<dubbo:protocol name="dubbo" serialization="hessian2" />
```
上述配置将使用Hessian进行序列化。

通过选择合适的序列化方式，可以根据具体的应用场景和需求来平衡传输效率、数据体积和跨语言交互的需求。

### Springboot整合步骤

要在Spring Boot中整合Dubbo，可以按照以下步骤进行操作：

1. 创建Spring Boot项目：使用Spring Initializr或其他方式创建一个基于Spring Boot的项目。
2. 引入Dubbo依赖：在项目的pom.xml文件中添加Dubbo依赖，例如：
   ````xml
   <dependency>
       <groupId>org.apache.dubbo</groupId>
       <artifactId>dubbo-spring-boot-starter</artifactId>
       <version>2.7.12</version>
   </dependency>
   ```
3. 创建Dubbo服务提供者：在项目中创建一个Dubbo服务提供者，即提供具体的服务实现。可以使用Dubbo的`@Service`注解标记服务实现类。
4. 配置Dubbo服务提供者：在application.properties或application.yml中配置Dubbo服务提供者的相关信息，例如：
   ````yaml
   # Dubbo服务提供者配置
   dubbo.application.name=my-provider
   dubbo.registry.address=zookeeper://localhost:2181
   dubbo.protocol.name=dubbo
   dubbo.protocol.port=20880
   ```
5. 创建Dubbo服务消费者：在项目中创建一个Dubbo服务消费者，即使用Dubbo调用远程服务。可以使用Dubbo的@Reference注解注入服务接口。
6. 配置Dubbo服务消费者：在application.properties或application.yml中配置Dubbo服务消费者的相关信息，例如：
   ````yaml
   # Dubbo服务消费者配置
   dubbo.application.name=my-consumer
   dubbo.registry.address=zookeeper://localhost:2181
   ```
7. 运行项目：启动Spring Boot应用程序，Dubbo将自动完成服务的注册与发现，并实现远程服务的调用。

需要注意的是，上述示例中使用ZooKeeper作为注册中心，你也可以选择其他的注册中心，如Nacos、Consul等。此外，还可以根据需要配置Dubbo的更多属性，如负载均衡策略、超时设置等。

### dubbo服务降级配置

在Dubbo中，可以通过实现服务降级来应对服务不可用或异常情况，以提高系统的可用性和稳定性。以下是在Dubbo上实现服务降级的一般步骤：

1. 定义服务降级逻辑：在服务提供者端实现服务降级逻辑。当服务出现异常或不可用时，可以返回一个默认的响应结果，或者执行备用逻辑来代替原始的服务实现。
2. 服务降级配置：
   - 在服务提供者端，可以通过Dubbo的`@Service`注解中的`mock`属性来配置服务降级逻辑的实现类。例如：
     ```java
     @Service(interfaceClass = UserService.class, mock = "com.example.UserServiceMock")
     public class UserServiceImpl implements UserService {
         // 实际的服务实现
     }
     ```
     在上述示例中，`UserServiceMock`是实现了服务降级逻辑的类。
   
   - 在服务消费者端，可以通过Dubbo的`@Reference`注解中的`mock`属性来配置服务降级的实现类。例如：
     ```java
     @Reference(interfaceClass = UserService.class, mock = "com.example.UserServiceMock")
     private UserService userService;
     ```
     在上述示例中，`UserServiceMock`是实现了服务降级逻辑的类。
3. 实现服务降级逻辑：创建一个实现了服务接口的服务降级类，并实现相应的降级逻辑。该类需要实现服务接口并提供默认的响应或备用逻辑。

   ````java
   public class UserServiceMock implements UserService {
       @Override
       public User getUserById(String userId) {
           // 服务降级逻辑：返回默认的响应结果或执行备用逻辑
           return new User();
       }
   }
   ```

通过以上步骤，服务提供者和服务消费者都可以配置相应的服务降级逻辑。当服务不可用或异常时，Dubbo将自动调用降级逻辑，返回默认的响应结果或执行备用逻辑，以保证系统的可用性。

需要注意的是，服务降级只是一种应对异常情况的手段，应该根据具体的业务需求和场景来选择合适的降级策略。在实际应用中，可以根据业务需求选择不同的降级方式，如返回默认值、返回缓存数据、调用备用接口等，以确保系统的可用性和用户体验。


### 分布式锁需要满足的条件

要实现分布式锁，需要满足以下条件：

1. 可靠的共享存储：分布式锁需要一个可靠的共享存储来存储锁的状态信息。这个共享存储可以是分布式缓存（如Redis、Memcached）、分布式文件系统或数据库等。所有参与分布式锁的节点都能够访问和共享这个存储。
2. 原子性操作：获取锁和释放锁的操作必须是原子性的，即在一个节点成功获取锁之后，其他节点就不能再获取到该锁，直到锁被释放。原子性操作可以通过共享存储的原子性操作来实现。
3. 互斥性：分布式锁需要保证在任何时候只有一个节点能够获取到锁。这意味着当一个节点成功获取到锁时，其他节点无法获取该锁。
4. 高可用性：分布式锁应该是高可用的，即使在节点故障或网络分区的情况下，锁仍然能够正常工作。这可以通过使用分布式存储系统来实现数据的冗余和复制，以及使用选举算法来选择新的锁持有者。
5. 死锁避免：分布式锁需要能够避免死锁情况的发生。死锁是指多个节点相互等待对方释放锁的情况，导致程序无法继续执行。为了避免死锁，可以使用超时机制、锁续租机制或者基于时间戳的锁等。
6. 性能和效率：分布式锁需要具备良好的性能和效率，以便在高并发情况下能够快速地获取和释放锁，避免对系统性能造成过大的影响。

实现分布式锁并满足以上条件是一个复杂的任务，需要考虑分布式系统的各种情况和并发访问的竞争条件。因此，通常建议使用已有的分布式锁实现，如基于Redis的Redlock、ZooKeeper的分布式锁等，它们已经考虑了上述条件并提供了可靠的分布式锁解决方案。

#### Redis设置分布式锁

- 使用SETNX命令：SETNX（SET if Not eXists）命令可以在键不存在的情况下设置键的值。分布式锁可以使用一个特定的键作为锁的标识，当一个客户端尝试使用SETNX命令设置该键时，如果返回值为1，则表示该客户端成功获取到了锁，可以执行后续操作；如果返回值为0，则表示锁已经被其他客户端持有，当前客户端需要等待或执行其他逻辑。在操作完成后，客户端可以使用DEL命令来释放锁。
- 使用SET命令结合EXPIRE命令：SET命令可以设置键的值，并且可以通过EXPIRE命令设置键的过期时间。分布式锁可以使用SET命令设置一个带有过期时间的键，当一个客户端成功设置该键时，表示获取到了锁，其他客户端可以通过GET命令检查该键是否存在以判断锁的状态。当锁不再需要时，客户端可以使用DEL命令来释放锁。
- 使用RedLock算法：RedLock是由Redis官方提出的一种分布式锁算法。它通过在多个Redis实例之间协调来实现分布式锁的可靠性和高可用性。RedLock算法要求至少需要多数（majority）的Redis节点达成一致才能获得锁。具体实现上，客户端会尝试在多个Redis实例上使用SET命令设置同一个键，并设置相同的过期时间。如果成功在大多数实例上设置了该键，则表示获取到了锁。释放锁时，客户端需要在所有实例上执行DEL命令来删除该键。
- 在业务执行的时候就增加一个守护进程监控锁的过期时间，如果锁即将过期，就将锁的时间延长，防止出现业务执行时间过长导致锁自动到期释放的问题。

#### Zookeeper实现分布式锁

当在Spring Boot中使用ZooKeeper，你需要添加相应的依赖以及编写配置和代码来连接和使用ZooKeeper。以下是一个简单的Spring Boot中使用ZooKeeper的示例：

1. 添加依赖

在你的Maven或Gradle配置文件中添加ZooKeeper依赖：

Maven:
```xml
<dependency>
    <groupId>org.apache.zookeeper</groupId>
    <artifactId>zookeeper</artifactId>
    <version>3.6.3</version>
</dependency>
```

Gradle:
```groovy
implementation 'org.apache.zookeeper:zookeeper:3.6.3'
```

2. 配置ZooKeeper连接

在`application.properties`或`application.yml`中添加ZooKeeper连接配置：

application.properties:
```properties
zookeeper.connectString=localhost:2181
zookeeper.sessionTimeout=5000
```

application.yml:
```yaml
zookeeper:
  connectString: localhost:2181
  sessionTimeout: 5000
```

3. 编写ZooKeeper客户端

创建一个ZooKeeper客户端类，用于连接ZooKeeper，并提供相应的方法来操作ZooKeeper：

```java
import org.apache.zookeeper.WatchedEvent;
import org.apache.zookeeper.Watcher;
import org.apache.zookeeper.ZooKeeper;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

import javax.annotation.PostConstruct;
import java.io.IOException;
import java.util.concurrent.CountDownLatch;

@Component
public class ZooKeeperClient {
    @Value("${zookeeper.connectString}")
    private String connectString;

    @Value("${zookeeper.sessionTimeout}")
    private int sessionTimeout;

    private ZooKeeper zooKeeper;
    
    // 用于确保链接完成后进行后续操作
    private CountDownLatch connectedLatch;

    @PostConstruct
    public void connect() throws IOException, InterruptedException {
        connectedLatch = new CountDownLatch(1);
        zooKeeper = new ZooKeeper(connectString, sessionTimeout, new Watcher() {
            @Override
            public void process(WatchedEvent event) {
                if (event.getState() == Event.KeeperState.SyncConnected) {
                    connectedLatch.countDown();
                }
            }
        });
        connectedLatch.await();
    }

    public boolean createNode(String path, byte[] data) {
        try {
            zooKeeper.create(path, data, null, null);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    public boolean deleteNode(String path) {
        try {
            zooKeeper.delete(path, -1);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }
}
```

4. 使用ZooKeeper分布式锁

在需要使用分布式锁的地方，可以注入`ZooKeeperClient`，并使用它的方法来创建和删除ZooKeeper节点来实现分布式锁的获取和释放：

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class DistributedLockService {
    private final static String LOCK_PATH = "/my-lock";

    @Autowired
    private ZooKeeperClient zooKeeperClient;

    public boolean acquireLock() {
        return zooKeeperClient.createNode(LOCK_PATH, null);
    }

    public boolean releaseLock() {
        return zooKeeperClient.deleteNode(LOCK_PATH);
    }
}
```