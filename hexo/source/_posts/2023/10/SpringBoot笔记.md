---
title: SpringBoot笔记
date: 2023-10-30 11:35:13
tags:
---

### application.properties 常用配置

```shell

#服务器端口配置：

server.port=8080
#用于指定应用程序监听的端口号。

#数据库连接配置：

spring.datasource.url=jdbc:mysql://localhost:3306/mydatabase
spring.datasource.username=root
spring.datasource.password=secret
#用于配置数据库连接的URL、用户名和密码。

#日志配置：

logging.level.root=INFO
logging.file=/path/to/application.log
#用于配置日志级别和日志文件的路径。

#Spring MVC配置：

spring.mvc.view.prefix=/WEB-INF/views/
spring.mvc.view.suffix=.jsp
#用于配置视图解析器的前缀和后缀，以便正确解析和渲染视图。

#静态资源配置：

spring.resources.static-locations=classpath:/static/
#用于配置静态资源文件的位置，比如CSS、JavaScript和图片等。

#国际化配置：

spring.messages.basename=messages
spring.messages.encoding=UTF-8
#用于配置国际化资源文件的基名和编码方式。

#缓存配置：

spring.cache.type=redis
spring.redis.host=localhost
spring.redis.port=6379
#用于配置缓存的类型和相关的参数，比如Redis缓存的主机和端口。
```

<!-- more -->

### 通过ConfigurationProperties注入值

配置文件yaml中写入值
```yaml
student:
  name: Alice
  age: 18
```
在java中写入注解
```java
@Component
@ConfigurationProperties(prefix = "student") // 注入yaml中的配置内容
public class Student {
    private String name;
    private Integer age;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

### JSR303校验

- 支持的[JSR303注解](https://docs.jboss.org/hibernate/stable/validator/reference/en-US/html_single/#section-builtin-constraints)

- 对象类中增加校验注解，比如`@Email`，`NotNull`，`Size(min=2, max=30)`

```java
import javax.validation.Valid;
@Data
public class ArticleDTO {
    @NotNull(message = "文章id不能为空")
    @Min(value = 1,message = "文章ID不能为负数")
    private Integer id;
    @NotBlank(message = "文章内容不能为空")
    private String content;
    @NotBlank(message = "作者Id不能为空")
    private String authorId;
    @Future(message = "提交时间不能为过去时间")
    private Date submitTime;
}
```
- Controller类中在传入的参数前增加注解`@Valid`，以及增加一个`BindingResult`的参数接收错误校验的结果。

```java
@PostMapping("/personName")
public String person(@Valid @RequestBody Student student, BindingResult bindingResult) {
    if (bindingResult.hasErrors()) {
        List<ObjectError> errors = bindingResult.getAllErrors();
        for (ObjectError error : errors) {
            System.out.println(error.getDefaultMessage());
        }
    }
    return "Hello World";
}
```

### 配置文件

- 推荐使用yaml配置方式
- [配置文件寻找路径](https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.external-config.files)
    - classpath下
      - classpath根目录
      - classpath目录下/config目录
    - 当前目录
    - 当前目录下的/config子目录
- 多个环境的配置文件
  - properties方式
    - application-prod.properties
    - application-dev.properties
    - application-test.properties
    - 通过制定主配置文件application.properties中的配置项`spring.profiles.active=test`
  - yaml方式
    - 在一个文件中配置多个配置环境
    ```yaml
    spring:
        profiles:
            active: prod

    ---
    server:
        port: 8081
    spring:
        config:
            activate:
                on-profile: dev

    ---
    server:
        port: 8082
    spring:
        config:
            activate:
                on-profile: prod
    ```

### webjars

- webjars将前端资源打包成jar包，就类似于后端jar包的形式，这样就能更方便的集中管理和版本管理
- 如在gradle中加入`implementation 'org.webjars.bower:jquery:3.7.1'`
- 就可在web应用打开时直接访问到jquery资源包
- **用的极少，可以忽略**

### 静态资源导入

- sprintboot默认读取`{"classpath:/META-INF/resources/","classpath:/resources/","classpath:/static/","classpath:/public/"}`下的资源文件
- 优先级：resources，static，public
    - public：放公共的js、css等
    - static：放静态资源
    - resources：上传的资源⽂文件
- 首页默认读取资源路径下的index.html

### Thymeleaf模版引擎

- /templates目录下放thymeleaf模版文件
- SpringBoot增加Thymeleaf支持
   - gradle增加`implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'`
   - 增加Controller类
    ```java
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RequestParam;

    @Controller
    public class ThymeleafController {

        @RequestMapping("/thymeHello")
        public String thymeHello(@RequestParam(name="name", required=false,
                defaultValue="World") String name, Model model) {
            model.addAttribute("name", name);
            return "ThymeHello";
        }
    }

    ```
  - 增加html文件，放到templates目录下
    ```html
    <!DOCTYPE HTML>
    <html xmlns:th="http://www.thymeleaf.org">
    <head>
        <title>Getting Started: Serving Web Content</title>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    </head>
    <body>
    <p th:text="|Hello, ${name}!|" />
    </body>
    </html>
    ```
  - 可用的[thymeleaf前端语法](https://www.thymeleaf.org/doc/tutorials/3.1/usingthymeleaf.html#setting-value-to-specific-attributes)


### SpringBoot中的MVC配置

- 新增类继承`WebMvcConfigure`
- 重写接口方法来实现新的配置功能
```java
@Configuration
public class MyMvcConfig implements WebMvcConfigurer {
    @Bean
    public ViewResolver myViewResolver() {
        return new MyViewResolver();
    }
    //⾃自⼰己定义了了⼀一个视图解析器器
    public static class MyViewResolver implements ViewResolver {
        @Override
        public View resolveViewName(String s, Locale locale) throws Exception {
            return null;
        }

    }
}
```
- 在spring启动时就会自动装载这个自定义配置


[SpringBoot笔记1.pdf](SpringBoot笔记1.pdf)
[SpringBoot笔记2.pdf](SpringBoot笔记2.pdf)