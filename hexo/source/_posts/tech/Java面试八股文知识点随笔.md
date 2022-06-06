---
title: Java面试八股文知识点随笔
date: 2022-06-02 11:14:41
tags:
---

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



