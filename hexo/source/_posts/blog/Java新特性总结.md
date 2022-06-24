---
title: Java新特性总结
date: 2022-06-20 14:09:18
comments: true
tags:
- 面试
categories: 
- 学习
---

## Java8

### Lambda 表达式

让 java 也能支持简单的函数式编程
```java
/** Runnable 接口 **/
new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("The runable now is using!");
            }
}).start();
//用lambda
new Thread(() -> System.out.println("It's a lambda function!")).start();

/**Comparator 接口**/
List<Integer> strings = Arrays.asList(1, 2, 3);

Collections.sort(strings, new Comparator<Integer>() {
@Override
public int compare(Integer o1, Integer o2) {
    return o1 - o2;}
});

//Lambda
Collections.sort(strings, (Integer o1, Integer o2) -> o1 - o2);
//分解开
Comparator<Integer> comperator = (Integer o1, Integer o2) -> o1 - o2;
Collections.sort(strings, comperator);
```

### Stream

Stream依然不存储数据，不同的是它可以检索(Retrieve)和逻辑处理集合数据、包括筛选、排序、统计、计数等。**可以想象成是 Sql 语句**。

它的源数据可以是 Collection、Array 等。由于它的方法参数都是函数式接口类型，所以一般和 Lambda 配合使用。

```java
@Test
public void test() {
  List<String> strings = Arrays.asList("abc", "def", "gkh", "abc");
    //返回符合条件的stream
    Stream<String> stringStream = strings.stream().filter(s -> "abc".equals(s));
    //计算流符合条件的流的数量
    long count = stringStream.count();

    //forEach遍历->打印元素
    strings.stream().forEach(System.out::println);

    //limit 获取到1个元素的stream
    Stream<String> limit = strings.stream().limit(1);
    //toArray 比如我们想看这个limitStream里面是什么，比如转换成String[],比如循环
    String[] array = limit.toArray(String[]::new);

    //map 对每个元素进行操作返回新流
    Stream<String> map = strings.stream().map(s -> s + "22");

    //sorted 排序并打印
    strings.stream().sorted().forEach(System.out::println);

    //Collectors collect 把abc放入容器中
    List<String> collect = strings.stream().filter(string -> "abc".equals(string)).collect(Collectors.toList());
    //把list转为string，各元素用，号隔开
    String mergedString = strings.stream().filter(string -> !string.isEmpty()).collect(Collectors.joining(","));

    //对数组的统计，比如用
    List<Integer> number = Arrays.asList(1, 2, 5, 4);

    IntSummaryStatistics statistics = number.stream().mapToInt((x) -> x).summaryStatistics();
    System.out.println("列表中最大的数 : "+statistics.getMax());
    System.out.println("列表中最小的数 : "+statistics.getMin());
    System.out.println("平均数 : "+statistics.getAverage());
    System.out.println("所有数之和 : "+statistics.getSum());

    //concat 合并流
    List<String> strings2 = Arrays.asList("xyz", "jqx");
    Stream.concat(strings2.stream(),strings.stream()).count();

    //注意 一个Stream只能操作一次，不能断开，否则会报错。
    Stream stream = strings.stream();
    //第一次使用
    stream.limit(2);
    //第二次使用
    stream.forEach(System.out::println);
    //报错 java.lang.IllegalStateException: stream has already been operated upon or closed

    //但是可以这样, 连续使用
    stream.limit(2).forEach(System.out::println);
}

```

### Optional表达式

主要用于解决NPE问题

```java
//原来的方式
Zoo zoo = getZoo();
if(zoo != null){
   Dog dog = zoo.getDog();
   if(dog != null){
      int age = dog.getAge();
      System.out.println(age);
   }
}

//Optional方式
Optional.ofNullable(zoo).map(o -> o.getDog()).map(d -> d.getAge()).ifPresent(age ->
    System.out.println(age)
);

```

### Interface新增默认方法，可以用default 或 static修饰

目的：避免接口新增方法时，所有实现的类也必须跟着修改
```java
public interface InterfaceNew {
    static void sm() {
        System.out.println("interface提供的方式实现");
    }
    static void sm2() {
        System.out.println("interface提供的方式实现");
    }

    default void def() {
        System.out.println("interface default方法");
    }
    default void def2() {
        System.out.println("interface default2方法");
    }
    //须要实现类重写
    void f();
}
```

### Date-Time API

```java
public void newFormat(){
    //format yyyy-MM-dd
    LocalDate date = LocalDate.now();
    System.out.println(String.format("date format : %s", date));

    //format HH:mm:ss
    LocalTime time = LocalTime.now().withNano(0);
    System.out.println(String.format("time format : %s", time));

    //format yyyy-MM-dd HH:mm:ss
    LocalDateTime dateTime = LocalDateTime.now();
    DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
    String dateTimeStr = dateTime.format(dateTimeFormatter);
    System.out.println(String.format("dateTime format : %s", dateTimeStr));
    
    LocalDate date = LocalDate.of(2021, 1, 26);
    LocalDate.parse("2021-01-26");
    
    LocalDateTime dateTime = LocalDateTime.of(2021, 1, 26, 12, 12, 22);
    LocalDateTime.parse("2021-01-26 12:12:22");
    
    LocalTime time = LocalTime.of(12, 12, 22);
    LocalTime.parse("12:12:22");

}

```
