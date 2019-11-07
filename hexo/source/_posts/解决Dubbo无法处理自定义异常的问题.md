---
title: 解决Dubbo无法处理自定义异常的问题
date: 2019-11-04 16:05:58
tags: 
- solved
comments: true
categories: 
- 学习
- 搭建相关
---

### 问题描述

Dubbo有一个比较奇怪的问题，目前不知道Apache和Alibaba公司出于什么样的考虑，貌似一直都没有一个比较合适的解决方案，问题如下：

- 项目搭建中你需要自定义一个本地的Exception，命名为比如BusinessException。比较一般的书写代码如下：

  ```java
  /**
   * @Author linqiang
   * @Date 2019/10/24 16:20
   * @Version 1.0
   * @Description 业务异常类
   **/
  public class BusinessException extends RuntimeException {
      private Integer code;
      private String msg;
  
      public BusinessException(Integer code, String msg) {
          this.code = code;
          this.msg = msg;
      }
  
      public Integer getCode() {
          return code;
      }
  
      public String getMsg() {
          return msg;
      }
  }
  ```

- 通常这个BusinessException是要能够跨模块使用的，一般放在commons或者core模块中，同时别的模块的pom.xml文件引入这些模块，使得整个项目都可以使用这个BusinessException。

- 问题来了，如果在A模块调用了B模块，B模块抛出了一个BusinessException，这时A模块接收到的不是BusinessException，而是一个RuntimeException，而且关于BusinessException的细节已经完全丢失，只会剩下一个类名的描述。

### 问题原因

关于该问题出现的原因，参考[这篇文章](https://blog.csdn.net/qq_25673113/article/details/78574514)，归纳一下，就是在Dubbo的传输信息过程中，类ExceptionFilter.java会对Exception做一个过滤，其过滤器的关键代码如下：

```java
// directly throw if it's checked exception
if (!(exception instanceof RuntimeException) && (exception instanceof Exception)) {
    return;
}
// directly throw if the exception appears in the signature
try {
    Method method = invoker.getInterface().getMethod(invocation.getMethodName(), invocation.getParameterTypes());
    Class<?>[] exceptionClassses = method.getExceptionTypes();
    for (Class<?> exceptionClass : exceptionClassses) {
        if (exception.getClass().equals(exceptionClass)) {
            return;
        }
    }
} catch (NoSuchMethodException e) {
    return;
}

// for the exception not found in method's signature, print ERROR message in server's log.
logger.error("Got unchecked and undeclared exception which called by " + RpcContext.getContext().getRemoteHost() + ". service: " + invoker.getInterface().getName() + ", method: " + invocation.getMethodName() + ", exception: " + exception.getClass().getName() + ": " + exception.getMessage(), exception);

// directly throw if exception class and interface class are in the same jar file.
String serviceFile = ReflectUtils.getCodeBase(invoker.getInterface());
String exceptionFile = ReflectUtils.getCodeBase(exception.getClass());
if (serviceFile == null || exceptionFile == null || serviceFile.equals(exceptionFile)) {
    return;
}
// directly throw if it's JDK exception
String className = exception.getClass().getName();
if (className.startsWith("java.") || className.startsWith("javax.")) {
    return;
}

// directly throw if it's dubbo exception
if (exception instanceof RpcException) {
    return;
}

// otherwise, wrap with RuntimeException and throw back to the client
appResponse.setException(new RuntimeException(StringUtils.toString(exception)));
return;
```

即Dubbo在遇到异常时会这样处理：

- 非RuntimeException不处理，直接返回
- 抛出的是方法上注明的异常，直接返回
- 如果异常类和接口类在同一jar包，直接返回
- java或者javax目录下的异常类，直接返回
- Dubbo自带的RpcException，直接返回
- 其他的异常，会被封装为RuntimeException返回

### 解决方式

根据以上的分析，那么很显然，自定义异常是被直接封装为RuntimeException返回了，而且只带了自定义异常的类名信息，丢失了别的细节。

那么我们想要自定义异常进行正常返回，那只有满足这个FIlter所写的上述条件。我们可以分析一下：

- 不继承RuntimeException，以检查时异常抛出。**不推荐，正常的业务异常应该是运行时异常。**

- 在接口方法上要写上throws BusinessException，如下：

  ```java
  public interface DemoService {
  
      DemoUser getUserInfo(Long userID) throws BusinessException;
  
  }
  ```

  **不推荐，不符合接口设计原则，且这样是把运行时异常作为检查时异常处理。**

- 把自定义异常类和接口放在同一个包目录下。**不推荐，毕竟这样相当于绑定了异常类的目录，耦合性变高。**

- 改包名，以“java.”或者“javax.”来开头。**不推荐，违反了类命名原则。**

- 继承Dubbo的RpcException。RpcException也是继承了RuntimeException，因此能够以RuntimeException的方式进行处理。**不推荐，相当于自定义异常属于Dubbo的RpcException，这在程序设计上不合理。**

我们发现，想要满足Dubbo的过滤器直接返回异常的条件，我们就必须做出一些违反程序设计的操作，如果一定要从这些方法中选择一种的话，相对来说，**自定义异常类和接口放在同一目录下，以及继承RpcException**是对于程序侵入性更小的方式。

### 其他解决方式

参考 [这篇文章](https://blog.csdn.net/yangzaizi/article/details/80638306)，提供了两种解决方式：

1.在配置文件中配置如下，效果是：关闭ExceptionFIlter，使所有异常绕过该过滤器直接返回。**不推荐，Dubbo既然设置了这个异常过滤类，一定是出于安全和功能上的考虑，直接禁用可能会引发别的问题。**

```yml
dubbo:
  provider:
    filter: -exception
```

2.修改Dubbo源文件ExceptionFilter，使其遇到BusinessException也能直接返回。**不推荐，相当于定制了本地的Dubbo包，是一个后续很容易被人忽略的大坑。**

### 总结

Dubbo在处理自定义异常时，会直接返回RuntimeException，且抹去自定义异常的所有细节，导致无法处理。

本文写下的时候，Dubbo版本为2.7.3，该问题还没有非常完美的解决方案，相对来说，把**自定义异常和接口类放在同一目录下**是侵入性最小的方案。