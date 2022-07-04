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
<!-- more -->
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

## Java9

### 接口私有方法
```java
public interface MyInterface {
    private void methodPrivate(){
    }
}
```

### JShell

JShell 是 Java 9 新增的一个实用工具。为 Java 提供了类似于 Python 的实时命令行交互工具。目前能想到的最大的作用**就是可以用于验证一些代码中的小问题**。
```jshelllanguage
jshell > System.out.print("HelloWorld");
```

### 集合新增of() 方法，用于快速创建集合对象

```java
List.of("Java", "C++");
Set.of("Java", "C++");
Map.of("Java", 1, "C++", 2);
```

### String由char[]数组改为byte[]数组存储
```java
public final class String implements java.io.Serializable,Comparable<String>, CharSequence {
    // @Stable 注解表示变量最多被修改一次，称为“稳定的”。
    @Stable
    private final byte[] value;
}
```


### 模块化系统

可以看成是大于package小于jar的一种结构，用于代码之间更高效的调用。在每个模块中加入一个module-info.java文件，写入下列描述，用于声明暴露和依赖的模块。
```java
module modular.demo.persistent {
    exports com.hanmc.example.modulardemo.persistent.domain;
    exports com.hanmc.example.modulardemo.persistent.dao;
​
    //声明需要依赖的模块
    requires modular.demo.common;
    requires mybatis.plus;
    requires mybatis.plus.core;
    requires mybatis.plus.annotation;
}
```
目录结构如下：
![](java-new1.jpg)

### 引入孵化器模块jdk.incubator

java9开始新特性不再是一定保留，而是会在版本中进行迭代，这些新特性就放在孵化器中，如果随着迭代得以保留就会从孵化器中移出成为正式的特性，不合适的就会直接移除。这大大增强了java新特性开发的灵活性。

### G1 成为默认垃圾回收器

### Stream & Optional 增强

### 进程API

Java 9 增加了 java.lang.ProcessHandle 接口来实现对原生进程进行管理，尤其适合于管理长时间运行的进程。
```java
// 获取当前正在运行的 JVM 的进程
ProcessHandle currentProcess = ProcessHandle.current();
// 输出进程的 id
System.out.println(currentProcess.pid());
// 输出进程的信息
System.out.println(currentProcess.info());
```

## Java10

### 局部变量类型推断(var)

var 并不会改变 Java 是一门静态类型语言的事实，编译器负责推断出类型。

```java
var id = 0;
var codefx = new URL("https://mp.weixin.qq.com/");
var list = new ArrayList<>();
var list = List.of(1, 2, 3);
var map = new HashMap<String, String>();
var p = Paths.of("src/test/java/Java9FeaturesTest.java");
var numbers = List.of("a", "b", "c");
for (var n : list)
    System.out.print(n+ " ");

var count=null; //❌编译不通过，不能声明为 null
var r = () -> Math.random();//❌编译不通过,不能声明为 Lambda表达式
var array = {1,2,3};//❌编译不通过,不能声明数组
```
### 若干性能的提升

#### G1 并行 Full GC
从java9的单线程标记-清除-压缩算法转为并行算法，从而有效的减少stop-the-world的时间

#### [Application Class-Data Sharing 程序class数据文件共享](https://www.baeldung.com/java-10-performance-improvements)
AppCDS原本是商业特性，现在免费开源了。
我的理解，简单来说是将类加载器中的类生成一份共享档案文件，从而在jvm启动时直接读取内存中的对应类，可以加快jvm的启动速度。

#### [Experimental Java-Based JIT Compiler 实验性的基于 Java 的 JIT 编译器](https://www.baeldung.com/java-10-performance-improvements#experimental-java-based-jit-compiler)

Graal 是一种java编写的动态编译器，可实现更高的性能和拓展性。同时也是java9中介绍过的Ahead-of-Time（AOT）编译器的基础。
加入以下参数开启Graal虚拟机
```java
-XX:+UnlockExperimentalVMOptions -XX:+UseJVMCICompiler
```


### 不可变集合

```java
// 新增copyOf，拷贝一个不可变的集合
@Test(expected = UnsupportedOperationException.class)
public void whenModifyCopyOfList_thenThrowsException() {
    List<Integer> copyList = List.copyOf(someIntList);
    copyList.add(4);
}

// Collectors新增toUnmodifiableList方法将集合变为不可变
@Test(expected = UnsupportedOperationException.class)
public void whenModifyToUnmodifiableList_thenThrowsException() {
    List<Integer> evenList = someIntList.stream()
      .filter(i -> i % 2 == 0)
      .collect(Collectors.toUnmodifiableList());
    evenList.add(4);
}
```

### Optional增加orElseThrow() 方法

### 容器意识
现在jvm能够知道自己在容器中运行，增加了几种容器中的jvm参数
```java
-XX:-UseContainerSupport
-XX:ActiveProcessorCount=count
-XX:InitialRAMPercentage
-XX:MaxRAMPercentage
-XX:MinRAMPercentage
```

### 根证书

现在java的证书密钥库包含了一些根证书，可用于对若干安全协议建立可信的证书连接。此功能被oracle从商业特性改为开源特性。

### 基于时间发布版本

Java10开始，oracle开始周期性的发布新版本：
1.每六个月发布一个java新版本。
2.功能性的发布只会支持6个月。
3.长期支持的版本会标记为LTS。该版本会支持3年。
4.java版本号现在会包含一个时间标示。
```java
$ java -version
openjdk version "10" 2018-03-20
OpenJDK Runtime Environment 18.3 (build 10+46)
OpenJDK 64-Bit Server VM 18.3 (build 10+46, mixed mode)
```

## Java11
Java11是Java8之后第一个长期支持的版本（LTS）。同时Oracle不再提供免费的Oracle JDK版本，但是依然对免费的Open JDK提供支持。

### 新的String方法

新增 isBlank, lines, strip, stripLeading, stripTrailing, and repeat
```java
// 对于多行文本的操作
String multilineString = "Baeldung helps \n \n developers \n explore Java.";
List<String> lines = multilineString.lines()
  .filter(line -> !line.isBlank())
  .map(String::strip)
  .collect(Collectors.toList());
assertThat(lines).containsExactly("Baeldung helps", "developers", "explore Java.");
```

### 新的Files操作方法

Files类新增writeString和readString静态方法，现在更容易从文件中操作String。
```java
Path filePath = Files.writeString(Files.createTempFile(tempDir, "demo", ".txt"), "Sample text");
String fileContent = Files.readString(filePath);
assertThat(fileContent).isEqualTo("Sample text");
```

### 集合到数组
java.util.Collection接口新增默认方法toArray
```java
List sampleList = Arrays.asList("Java", "Kotlin");
String[] sampleArray = sampleList.toArray(String[]::new);
assertThat(sampleArray).containsExactly("Java", "Kotlin");
```
### The Not Predicate Method

```java
List<String> sampleList = Arrays.asList("Java", "\n \n", "Kotlin", " ");
List withoutBlanks = sampleList.stream()
  .filter(Predicate.not(String::isBlank))
  .collect(Collectors.toList());
assertThat(withoutBlanks).containsExactly("Java", "Kotlin");
```

### Lamda表达式中的本地变量语法
```java
List<String> sampleList = Arrays.asList("Java", "Kotlin");
String resultString = sampleList.stream()
  .map((@Nonnull var x) -> x.toUpperCase())
  .collect(Collectors.joining(", "));
assertThat(resultString).isEqualTo("JAVA, KOTLIN");
```

### HTTP Client

新的HTTP API全面提升了性能并且将HTTP的支持从HTTP/1.1提升到了HTTP/2
```java
HttpClient httpClient = HttpClient.newBuilder()
  .version(HttpClient.Version.HTTP_2)
  .connectTimeout(Duration.ofSeconds(20))
  .build();
HttpRequest httpRequest = HttpRequest.newBuilder()
  .GET()
  .uri(URI.create("http://localhost:" + port))
  .build();
HttpResponse httpResponse = httpClient.send(httpRequest, HttpResponse.BodyHandlers.ofString());
assertThat(httpResponse.body()).isEqualTo("Hello from the server!");
```

### [嵌套类访问控制](https://www.baeldung.com/java-11-new-features#7-nest-based-access-control)

### 可直接运行java文件
```java
$ java HelloWorld.java
Hello Java 11!
```

### Dynamic Class-File Constants

个人理解是动态常量池

### 新增一个消极的垃圾收集器 Epsilon

不会去进行GC，一般不用于正式的java项目，可用于测试和生命周期极短的项目。
启用方式：
```java
-XX:+UnlockExperimentalVMOptions -XX:+UseEpsilonGC
```

### 飞行记录器

Java Flight Recorder (JFR) 现在在OpenJDK开源，使用如下命令运行监控

```java
-XX:StartFlightRecording=duration=120s,settings=profile,filename=java-demo-app.jfr
```
会生成一个JFR文件，该文件要使用JDK Mission Control (JMC)进行分析。

## Java12

### String增加两个方法indent() 和 transform()

### File增加文件对比方法
可以通过mismatch(Path path, Path path2) 方法来比较两个文件的内容差异
```java
@Test
public void givenDifferentFiles_thenShouldFindMismatch() {
    Path filePath3 = Files.createTempFile("file3", ".txt");
    Path filePath4 = Files.createTempFile("file4", ".txt");
    Files.writeString(filePath3, "Java 12 Article");
    Files.writeString(filePath4, "Java 12 Tutorial");

    long mismatch = Files.mismatch(filePath3, filePath4);
    assertEquals(8, mismatch);
}
```

### Collector新增teeing方法
这是一个数学方法，典型用法是第一个参数得出总数，第二个参数计算个数，第三个参数将取到前两个参数的值进行平均数计算。
```java
Collector<T, ?, R> teeing(Collector<? super T, ?, R1> downstream1,
  Collector<? super T, ?, R2> downstream2, BiFunction<? super R1, ? super R2, R> merger)

@Test
public void givenSetOfNumbers_thenCalculateAverage() {
    double mean = Stream.of(1, 2, 3, 4, 5)
      .collect(Collectors.teeing(Collectors.summingDouble(i -> i), 
        Collectors.counting(), (sum, count) -> sum / count));
    assertEquals(3.0, mean);
}
```

### 数字格式化

可以将数字进行格式化，按照某种语言习惯进行转换，比如1000在英语中可以转为1k。

```java
@Test
public void givenNumber_thenCompactValues() {
    NumberFormat likesShort = 
      NumberFormat.getCompactNumberInstance(new Locale("en", "US"), NumberFormat.Style.SHORT);
    likesShort.setMaximumFractionDigits(2);
    assertEquals("2.59K", likesShort.format(2592));

    NumberFormat likesLong = 
      NumberFormat.getCompactNumberInstance(new Locale("en", "US"), NumberFormat.Style.LONG);
    likesLong.setMaximumFractionDigits(2);
    assertEquals("2.59 thousand", likesLong.format(2592));
}

```


### 预览新特性

可以输入如下参数开启新特性的使用
```java
javac -Xlint:preview --enable-preview -source 12 src/main/java/File.java
```

#### Switch 语法增强

以前的switch写法
```java
DayOfWeek dayOfWeek = LocalDate.now().getDayOfWeek();
String typeOfDay = "";
switch (dayOfWeek) {
    case MONDAY:
    case TUESDAY:
    case WEDNESDAY:
    case THURSDAY:
    case FRIDAY:
        typeOfDay = "Working Day";
        break;
    case SATURDAY:
    case SUNDAY:
        typeOfDay = "Day Off";
}
```
现在的switch语法
```java
// 可在一个case中判断多个字段，同时移除break语法，
typeOfDay = switch (dayOfWeek) {
    case MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY -> "Working Day";
    case SATURDAY, SUNDAY -> "Day Off";
}

// 可在case中运行代码
switch (dayOfWeek) {
    case MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY -> System.out.println("Working Day");
    case SATURDAY, SUNDAY -> System.out.println("Day Off");
}

// 运行多行代码
case MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY -> {
    // more logic
    System.out.println("Working Day")
}
```

#### instanceof的类型匹配

以前在instanceof中进行类型判断和使用需要像这样
```java
Object obj = "Hello World!";
if (obj instanceof String) {
    String s = (String) obj;
    int length = s.length();
}
``` 
现在
```java
if (obj instanceof String s) {
    int length = s.length();
}
```

### JVM的修改

#### Shenandoah 一个致力于暂停时间短的垃圾收集器

#### Microbenchmark Suite 微基准套件
Java12介绍了一组针对Java源码的约100个的微基准测试套件，这些套件可以测试Jvm的性能并帮助开发者在Jvm上进行开发。

#### CDS 文档变为默认功能
CDS用于减少JVM启动时间，在Java12中成为默认开启的功能，通过如下命令进行关闭
```java
java -Xshare:off HelloWorld.java
```

## Java13

### 新特性预览，需要手动开启
```java
javac -Xlint:preview --enable-preview -source 12 src/main/java/File.java
```

#### Switch表达式增强，新增yield语法用于返回值
用法:
```java
@Test
@SuppressWarnings("preview")
public void whenSwitchingOnOperationSquareMe_thenWillReturnSquare() {
    var me = 4;
    var operation = "squareMe";
    var result = switch (operation) {
        case "doubleMe" -> {
            yield me * 2;
        }
        case "squareMe" -> {
            yield me * me;
        }
        default -> me;
    };

    assertEquals(16, result);
}
```

#### 多行文本块
现在可以更方便的对String进行多行文本的赋值，比如 JSON, XML, HTML 等文本。
```java
String JSON_STRING 
  = "{\r\n" + "\"name\" : \"Baeldung\",\r\n" + "\"website\" : \"https://www.%s.com/\"\r\n" + "}";

String TEXT_BLOCK_JSON = """
{
    "name" : "Baeldung",
    "website" : "https://www.%s.com/"
}
""";

assertThat(TEXT_BLOCK_JSON.contains("Baeldung")).isTrue();
assertThat(TEXT_BLOCK_JSON.indexOf("www")).isGreaterThan(0);
assertThat(TEXT_BLOCK_JSON.length()).isGreaterThan(0);
```

### 动态CDS归档
CDS从java10发布，到java12成为默认开启的功能。然而该功能的使用略显繁杂。开发者需要先试运行他们的程序以生成class列表，然后再将其转为归档文件。这之后，这些归档文件才能在虚拟机之间进行数据共享。
在Java13中，简化了归档文件的生成步骤。现在我们可以在程序创建的时候就生成共享归档文件。减少了试运行的那一步。我们需要在启动参数中加入-XX:ArchiveClassesAtExit并指定归档文件名来开启该功能。
```java
java -XX:ArchiveClassesAtExit=<archive filename> -cp <app jar> AppName
```
然后我们就可以在运行相同应用的时候直接使用共享文档
```java
java -XX:SharedArchiveFile=<archive filename> -cp <app jar> AppName
```

### ZGC释放未使用内存
在该版本之前，ZGC虽然可以做到STW时间始终低于10ms，但是不像其他垃圾收集器会将未使用的堆内存释放。现在Java13拥有了该功能，并且该功能是默认开启的。并且ZGC现在将最大支持内存从4TB增加到了16TB。

### Socket API重构
Socket API发布以来，20年未曾更改，在Java13终于进行了调整。

### 其他更改
- java.nio – method FileSystems.newFileSystem(Path, Map<String, ?>) added
- java.time – new official Japanese era name added
- javax.crypto – support for MS Cryptography Next Generation (CNG)
- javax.security – property jdk.sasl.disabledMechanisms added to disable SASL mechanisms
- javax.xml.crypto – new String constants introduced to represent Canonical XML 1.1 URIs
- javax.xml.parsers – new methods added to instantiate DOM and SAX factories with namespaces support
- Unicode support upgraded to version 12.1
- Support added for Kerberos principal name canonicalization and cross-realm referrals

## Java14

### Switch新语法和文本块语法实装
```java
boolean isTodayHoliday = switch (day) {
    case "MONDAY", "TUESDAY", "WEDNESDAY", "THURSDAY", "FRIDAY" -> false;
    case "SATURDAY", "SUNDAY" -> true;
    default -> throw new IllegalArgumentException("What's a " + day);
};
```
```java
String multiline = """
    A quick brown fox jumps over a lazy dog; \
    the lazy dog howls loudly.""";
```

### Records 关键字（预览特性）
可以通过定一个Records类，来减少类中多余的模版代码，Records相当于自动帮你生成了get，equals, hashCode, 构造方法和toString方法。可以看成简化版的lombok。
```java
public record User(int id, String password) { };

private User user1 = new User(0, "UserOne");

@Test
public void givenRecord_whenObjInitialized_thenValuesCanBeFetchedWithGetters() {
    assertEquals(0, user1.id());
    assertEquals("UserOne", user1.password());
}

@Test
public void whenRecord_thenEqualsImplemented() {
    User user2 = user1;
    assertTrue(user1, user2);
}

@Test
public void whenRecord_thenToStringImplemented() {
    assertTrue(user1.toString().contains("UserOne"));
}
```

### NPE 报错增强，现在会准确提示报错在哪一行以及哪一个对象为空。
```java
java.lang.NullPointerException: Cannot store to int array because "a" is null
```

### 孵化特性，这些特性放在模块jdk.incubator.中

#### 访问外部内存的API
现在允许java程序直接访问外部内存，比如计算机本地内存。

#### 打包工具
现在可以通过jlink工具打包，生成在windows中exe文件或者macos中的dmg文件，以实现更方便的双击运行。

### ZGC现在可以在Windows和MacOS上使用-实验特性

### 为G1 增加 NUMA 内存收集机制

### JFR 事件流，增强了JFR的监控能力，现在可以通过消费者的方式直接获取监控结果

## Java15

### records关键字实装

### sealed，non-sealed，permits关键字
这些关键字提供了更精细的类之间的访问控制。
```java
public abstract sealed class Person
    permits Employee, Manager {
 
    //...
}
```

以上语法的效果是Person类将只能被Employee, Manager类继承。
注意：继承sealed类的子类必须用sealed, non-sealed, 或 final进行修饰，这样可以保证类层次有限，同时被JVM正确识别。

```java
public final class Employee extends Person {
}

public non-sealed class Manager extends Person {
}
```

### 隐藏类
大部分程序员在日常开发中用不到隐藏类，JVM开发者会发现它的好处。隐藏类的目的是在程序运行时创建无法被发现的类。这种类无法被引用，被反射，同时具有较短的生命周期。

### 模式匹配中的类型检查
该特性在java14中已经介绍过，代码如下。
```java
if (person instanceof Employee) {
    Employee employee = (Employee) person;
    Date hireDate = employee.getHireDate();
    //...
}
```
在java15中该特性得到增强，在类型判断成功后，jvm会自动将该变量赋值为对应类型进行后续操作，如下person对象自动赋值到了employee对象中，可以进行后续的变量操作：
```java
if (person instanceof Employee employee && employee.getYearsOfService() > 5) {
    //...
}
```


### 外部内存API
JAVA14中已经介绍过访问外部内存的API，在java15中增加若干功能。
- A new VarHandle API, to customize memory access var handles
- Support for parallel processing of a memory segment using the Spliterator interface
- Enhanced support for mapped memory segments
- Ability to manipulate and dereference addresses coming from things like native calls

### ZGC和Shenandoah成为可进行选择配置的垃圾收集器，G1依然是默认的垃圾收集器

### Java15中移除偏向锁，RMI，Solaris/SPARC

## Java16

### 动态代理现在可以反射接口的默认方法
```java
Object proxy = Proxy.newProxyInstance(getSystemClassLoader(), new Class<?>[] { HelloWorld.class },
    (prox, method, args) -> {
        if (method.isDefault()) {
            return InvocationHandler.invokeDefault(prox, method, args);
        }
        // ...
    }
);
Method method = proxy.getClass().getMethod("hello");
assertThat(method.invoke(proxy)).isEqualTo("world");
```

### 日期格式的支持
通过一个标识符“B”来进行日期格式的转换。更多详细用法[在此](https://www.baeldung.com/java-datetimeformatter)
```java
LocalTime date = LocalTime.parse("15:25:08.690791");
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("h B");
assertThat(date.format(formatter)).isEqualTo("3 in the afternoon");
```

### Stream 类增加toList方法
```java
List<String> integersAsString = Arrays.asList("1", "2", "3");
List<Integer> ints = integersAsString.stream().map(Integer::parseInt).collect(Collectors.toList());
List<Integer> intsEquivalent = integersAsString.stream().map(Integer::parseInt).toList();
```

### Vector API Incubator

### Records关键字功能增强

### instanceof模式匹配和Sealed关键字实装

## Java17

### 浮点型计算将保证在不同的平台保持严格相同的结果

### 伪随机数生成器增强
java17为伪随机数生成器新增了几个接口和实现。将更容易使用不同的算法并更好的支持stream为基础的编程。
```java
public IntStream getPseudoInts(String algorithm, int streamSize) {
    // returns an IntStream with size @streamSize of random numbers generated using the @algorithm
    // where the lower bound is 0 and the upper is 100 (exclusive)
    return RandomGeneratorFactory.of(algorithm)
            .create()
            .ints(streamSize, 0,100);
}
```

### Applet API 被移除

### 对JDK的内部类更严格的封装，比如sun.misc.Unsafe，将保证开发者无法访问到此类方法

### switch中的模式匹配
之前已经介绍过的instanceOf中的模式匹配，该功能进一步增强了switch的语法能力，如下，obj的类型转换直接发生在case关键字中，减少了很多冗余代码。
```java
static record Human (String name, int age, String profession) {}

public String checkObject(Object obj) {
    return switch (obj) {
        case Human h -> "Name: %s, age: %s and profession: %s".formatted(h.name(), h.age(), h.profession());
        case Circle c -> "This is a circle";
        case Shape s -> "It is just a shape";
        case null -> "It is null";
        default -> "It is an object";
    };
}

public String checkShape(Shape shape) {
    return switch (shape) {
        case Triangle t && (t.getNumberOfSides() != 3) -> "This is a weird triangle";
        case Circle c && (c.getNumberOfSides() != 0) -> "This is a weird circle";
        default -> "Just a normal shape";
    };
}
```

### RMI 功能被移除

### sealed关键字

### 移除Ahead-Of-Time (AOT) 编译 和 Just-In-Time (JIT) 编译方式
在JDK9和10中被介绍的特性，在经历了多个版本迭代后，其维护成本过高，因此被移除。

### 移除 Security Manager

### 外部方法和外部内存的API（孵化器特性）
外部方法和的API允许开发者访问JVM虚拟机之外的代码，外部内存的API可以管理堆外内存。其设计目的是为了取代JNI API，并提示其安全性和性能。
调用C语言库：
```java
private static final SymbolLookup libLookup;

static {
    // loads a particular C library
    var path = JEP412.class.getResource("/print_name.so").getPath();
    System.load(path);
    libLookup = SymbolLookup.loaderLookup();
}
```

### Vector API（二次孵化）
Vector API 处理 SIMD（单指令，多数据）类型的操作，这意味着并行执行的各种指令集。 它利用支持向量指令的专用 CPU 硬件，并允许执行流水线等指令。

```java
public void newVectorComputation(float[] a, float[] b, float[] c) {
    for (var i = 0; i < a.length; i += SPECIES.length()) {
        var m = SPECIES.indexInRange(i, a.length);
        var va = FloatVector.fromArray(SPECIES, a, i, m);
        var vb = FloatVector.fromArray(SPECIES, b, i, m);
        var vc = va.mul(vb);
        vc.intoArray(c, i, m);
    }
}

public void commonVectorComputation(float[] a, float[] b, float[] c) {
    for (var i = 0; i < a.length; i ++) {
        c[i] = a[i] * b[i];
    }
}
```

### 反序列化过滤器
用于验证从不可信任来源发送来的序列化数据，这个过程发生在jvm虚拟机中，提升了安全性和健壮性。

### 新增一个半年周期-特性的模块
这个模块帮助开发者减轻开发一个新功能的压力，一个功能首先在该模块中进行试用，之后再发布到LTS模块中。

### 新增LTS模块



> 参考资料 
https://javaguide.cn/java/new-features/java8-common-new-features.html
https://javaguide.cn/java/new-features/java9.html
https://javaguide.cn/java/new-features/java10.html
https://javaguide.cn/java/new-features/java11.html
https://javaguide.cn/java/new-features/java12-13.html
https://javaguide.cn/java/new-features/java14-15.html
https://javaguide.cn/java/new-features/java16.html
https://www.baeldung.com/java-8-new-features
https://www.baeldung.com/new-java-9
https://www.baeldung.com/java-10-overview
https://www.baeldung.com/java-11-new-features
https://www.baeldung.com/java-12-new-features
https://www.baeldung.com/java-13-new-features
https://www.baeldung.com/java-14-new-features
https://www.baeldung.com/java-15-new
https://www.baeldung.com/java-16-new-features
https://www.baeldung.com/java-17-new-features

          

