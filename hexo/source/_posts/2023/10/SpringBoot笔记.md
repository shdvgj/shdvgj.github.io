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

### [常见SpringBoot注解](https://www.upgrad.com/blog/spring-boot-annotations/)

- `@Bean` : The @Bean annotations are used at the method level and indicate that a method produces a bean that is to be managed by the Spring container. It is an alternative to the XML<bean> tag. 
- `@Service` : It is used at the class level. It shows that the annotated class is a service class, such as business basic logic, and call external APIs.
- `@Repository` : It is a Data Access Object (DAO) that accesses the database directly. It indicates that the annotated class is a repository. 
- `@Configuration` : It is used as a source of bean definitions. It is a class-level annotation.
- `@Controller` : The annotation is used to indicate that the class is a web request handler. It is often used to present web pages. It is most commonly used with `@RequestMapping` annotation. 
- `@RequestMapping` : RequestMapping is used to map the HTTP request. It is used with the class as well as the method. It has many other optional elements like consumes, name, method, request, path, etc.
- `@Autowired` : This annotation is used to auto-wire spring bean on setter methods, constructor and instance variable. It injects object dependency implicitly. When we use this annotation, the spring container auto-wires the bean by its matching data type.
- `@Component` : It is a class-level annotation that turns the class into Spring bean at the auto-scan time.
- `@SpringBootApplication` : It consists of @Configuration, @ComponentScan, and `@EnabeAutoConfiguration`. The class annotated with @SpringBootApplication is kept in the base package. This annotation does the component scan. However, only the sub-packages are scanned. 
- `@EnableAutoConfiguration` : It is placed on the main application class. Based on classpath settings, other beans, and various property settings, this annotation instructs SpringBoot to start adding beans.
- `@ComponetScan` : It is used to scan a package of beans. It is used with the annotation `@Configuration` to allow Spring to know the packages to be scanned for annotated components. This annotation is also used to specify base packages.
- `@Required` : This annotation is applied to bean setter methods. It indicates that the required property must be filled at the configuration time in the affected bean, or else it throws an exception: BeanInitializationException.
- `@Qualifier` : It is used along with `@Autowired` annotation. It is used when more control is required over the dependency injection process. Individual constructor arguments or method parameters can be specified by using this annotation. 
- `@CookieValue` : It is used at the method parameter level as an argument of the request mapping method. For a given cookie name, the HTTP cookie is bound to a @CookieValue parameter.
- `@Lazy` : It is used in the component class. At startup, all auto-wired dependencies are created and configured. But a `@Lazy` annotation can be created if a bean is to be initialized lazily.

### [Restful风格写法](https://spring.io/guides/tutorials/rest/)

- Restful是一种Web编程风格，以资源为中心，进行GET/POST/PUT/DELETE/PATCH
- 使用超文本链接（HATEOAS），来表示资源之间的关系，以便客户端可以通过动态导航发现和访问相关资源
- 在SpringBoot中增加包`spring-boot-starter-hateoas`
- 代码中增加`linkTo`关系
```java
@GetMapping("/employees/{id}")
EntityModel<Employee> one(@PathVariable Long id) {

    Employee employee = employeeRepository.findById(id) //
            .orElseThrow(() -> new EmployeeNotFoundException(id));

    return EntityModel.of(employee, //
            linkTo(methodOn(EmployeeController.class).one(id)).withSelfRel(),
            linkTo(methodOn(EmployeeController.class).all()).withRel("employees"));
}
```
- 返回的结果中会自带关联的资源uri
```json
{
    "id": 1,
    "name": "Bilbo Baggins",
    "role": "burglar",
    "_links": {
        "self": {
            "href": "http://localhost:8080/employees/1"
        },
        "employees": {
            "href": "http://localhost:8080/employees"
        }
    }
}
```


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
- 统一错误页面
  - 在templates目录下增加一个error目录
  - 目录中写入对应错误代码为标题的页面，如404.html，403.html

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

### 国际化

- i18n，根据请求头中的content-language中的zh_CN、en_US来判断
  - 在resources目录下增加国际化语言的配置文件，根据标准化的[国家标识](https://www.ibm.com/docs/zh/spectrum-protect/8.1.10?topic=options-language)来做后缀，如下图
  ![](i18n_1.png)
  - 在各个配置文件中写入各国语言的翻译信息，如`message_zh_CN.properties`中写入`message1=你好世界`，`message_en_US`中写入`message1=helloworld`
  - 前端的thymeleaf页面中写入`<p th:text="|#{message1}!|" />`即可引用properties中的变量
  - 在`application.yaml`中新增配置`spring.messages.basename: i18n.message`
  - [Springboot实现i18n](https://lokalise.com/blog/spring-boot-internationalization/)
- DNS的方式，在DNS服务器上进行翻译
> 要通过DNS服务器实现国际化，可以使用国际化域名（Internationalized Domain Name，简称IDN）。IDN允许在域名中使用非ASCII字符，包括特定语言的字符和符号。
> 以下是实现国际化的一般步骤：
> - 选择一个支持IDN的DNS注册商：确保你选择的DNS注册商支持IDN，并且能够提供针对特定语言和字符集的域名注册服务。
> - 注册国际化域名：使用支持IDN的DNS注册商注册你想要的国际化域名。这些域名可以包含特定语言的字符和符号，例如中文、俄文、阿拉伯文等。
> - 配置DNS解析：一旦你注册了国际化域名，你需要配置DNS解析，将域名指向你的服务器或网站。这通常涉及到在DNS服务器上创建相应的记录，如A记录、CNAME记录或MX记录等。
> - 测试和验证：完成DNS解析配置后，你可以进行测试和验证，确保国际化域名能够正确解析到相应的服务器或网站。
> 需要注意的是，不是所有的DNS服务器和应用程序都完全支持IDN。因此，在实施国际化时，确保你的目标受众和使用环境支持IDN，并进行相应的测试和验证。
> 此外，还需要注意国际化域名的兼容性和安全性。一些恶意攻击可能会利用国际化域名的特殊字符来进行钓鱼或欺诈行为。因此，确保你选择的DNS注册商和DNS服务器有适当的安全措施来保护你的域名和用户的安全。
> 总结起来，要通过DNS服务器实现国际化，你需要选择支持IDN的DNS注册商，注册国际化域名，并配置DNS解析将域名指向你的服务器或网站。记得测试和验证确保一切正常，并注意兼容性和安全性的问题。

### 登录拦截器

- 自定义拦截器
- 在自定义的MyMvcConfig去重写并写入

### [SpringSecurity简单接入](https://docs.spring.io/spring-security/reference/servlet/authentication/passwords/index.html)

- SpringSecurity可以进行少量配置就实现强大的安全管理，涉及几个类，完成认证和授权
  - WebSecurityConfigureAdapter：自定义安全策略
  - AuthenticationManagerBuilder：自定义认证策略
  - @EnableWebSecurity：开启security模式
- 利用github来做认证
  - 注册一个github app，具体步骤如下
    - 进入[github app注册页面](https://github.com/settings/developers)，点击`New OAuth App`
    - 输入项目名，和项目首页地址，本地测试可以输入`http://localhost:8080`
    - `Authorization callback URL` 这一栏输入 `http://localhost:8080/login/oauth2/code/github`
    - 点击`Register Application`
    - 在`application.yaml`文件中增加security配置，clientId和clientSecret替换为自己的GitHub上对应的值
    ```yaml
    spring:
        security:
            oauth2:
                client:
                    registration:
                        github:
                            clientId: github-client-id
                            clientSecret: github-client-secret
    ```
    - 此时启动项目，并访问首页，将会看到页面指向github的授权页面
    ![](oauth1.png)
    - SpringSecurity将github返回的token存入到cookie中，`JSESSIONID=XXXXXXXXXXXX`
- 内存用户名密码登录
  - 配置WebSecurity类
  ```java
    @Configuration
    @EnableWebSecurity
    public class WebSecurityConfig {
        @Bean
        public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
            http
                    .authorizeHttpRequests((requests) -> requests
                            .requestMatchers("/", "/home").permitAll()
                            // 设置页面角色权限
                            .requestMatchers("/hello").hasRole("ADMIN")
                            .requestMatchers("/user").hasRole("USER")
                            .anyRequest().authenticated()
                    )
                    .formLogin((form) -> form
                            // 定制登录页面
                            .loginPage("/login")
                            // 声明页面username参数名
                            .usernameParameter("uname")
                            // 声明页面password参数名
                            .passwordParameter("pwd")
                    )
                    .logout((logout) -> logout.permitAll());

            return http.build();
        }
        /**
         * 设置用户名密码和角色
         */
        @Bean
        public UserDetailsService userDetailsService() {
            UserDetails user =
                    User.withDefaultPasswordEncoder()
                            .username("user")
                            .password("password")
                            .roles("USER")
                            .build();
            return new InMemoryUserDetailsManager(user);
        }
    }
  ```
- 绑定Thymeleaf
  - 用法
  ```html
    <div class="right menu">
        <div sec:authorize="!isAuthenticated()">
            <a class="item" th:href="@{/toLogin}">
            <i class="address card icon"></i> 登录
            </a>
        </div>
        <a class="item" th:href="@{/logout}">
            <i class="address cara icon"></i> 注销
        </a>
    </div>
  ```
  - 可用属性
    - `sec:authorize="isAuthenticated()"` 进行登录状态的判断
    - `sec;authentication="name"` 获取当前用户名
    - `sec:authorize="hasRole(vip1)"` 判读用户是否拥有该权限
    - `sec:authentication="principal.authorities"` 获得当前用户角色
- SpringSecurity连接数据库登录
  - 写好登录用户类和Repository类，其中用户的username和password是必须的
  - 继承UserDetailService类
  ```java
    @Service
    public class EmployeeService implements UserDetailsService {
        // repository类
        private final EmployeeRepository employeeRepository;

        public EmployeeService(EmployeeRepository employeeRepository) {
            this.employeeRepository = employeeRepository;
        }

        @Override
        public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
            Employee employee = Optional.of(employeeRepository.findTopByFirstName(username))
                    .orElseThrow(() -> new UsernameNotFoundException("User not found"));
            return org.springframework.security.core.userdetails.User
                    .withUsername(employee.getFirstName())
                    .password(employee.getPassword())
                    .roles(employee.getRole())
                    .build();
        }
    }
  ```
  - SecurityConfig类中无需额外设置，新版的SpringSecurity会自动找到UserService，[旧版配置需要继承WebSecurityConfigurerAdapter](https://spring.io/blog/2022/02/21/spring-security-without-the-websecurityconfigureradapter)
  ```java
    @Configuration
    @EnableWebSecurity
    public class WebSecurityConfig {

        @Bean
        public PasswordEncoder passwordEncoder() {
            return new BCryptPasswordEncoder();
        }
        @Bean
        public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
            http
                    .authorizeHttpRequests((requests) -> requests
                            .requestMatchers("/", "/home").permitAll()
                            // 设置页面角色权限
                            .requestMatchers("/hello").hasRole("ADMIN")
                            .requestMatchers("/user").hasRole("USER")
                            .anyRequest().authenticated()
                    )
                    .formLogin((form) -> form
                            // 定制登录页面
                            .loginPage("/login")
                            // 声明页面username参数名
                            .usernameParameter("uname")
                            // 声明页面password参数名
                            .passwordParameter("pwd")
                            .permitAll()
                    )
                    .logout((logout) -> logout.permitAll())
                    .csrf().disable();

            return http.build();
        }
    }
  ```
  - 注意，存入数据库的密码需要用[Bcrpt加密](https://www.bejson.com/encrypt/bcrpyt_encode/)，否则会报错`Encoded password does not look like BCrypt`


### 连接mysql数据库

- 导入mysql包：`implementation 'com.mysql:mysql-connector-j:8.2.0'`
- `application.yaml`中增加配置
```yaml
spring:
  jpa:
    hibernate:
      # 设置为create的话会自动创建表
      ddl-auto: update
    show-sql: true
    properties:
      hibernate:
        dialect: org.hibernate.dialect.MySQLDialect
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/db_example?userUnicode=true&characterEncoding=UTF-8
    username: springuser
    password: ThePassword
```
- 其中spring.jpa.hibernate.ddl-auto的说明如下
  - none: The default for MySQL. No change is made to the database structure.
  - update: Hibernate changes the database according to the given entity structures.
  - create: Creates the database every time but does not drop it on close.
  - create-drop: Creates the database and drops it when SessionFactory closes.
- 创建entity
```java
@Entity
public class Employee {
    private @Id @GeneratedValue Long id;
    private String firstName;
    private String lastName;
    private String role;

    public Employee() {
    }

    public String getName() {
        return this.firstName + " " + this.lastName;
    }

    public void setName(String name) {
        String[] parts = name.split(" ");
        this.firstName = parts[0];
        this.lastName = parts[1];
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getFirstName() {
        return firstName;
    }

    public void setFirstName(String firstName) {
        this.firstName = firstName;
    }

    public String getLastName() {
        return lastName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }

    public String getRole() {
        return role;
    }

    public void setRole(String role) {
        this.role = role;
    }

}
```
- 增加一个repository，自动包含了employee所需的crud方法
```java
public interface EmployeeRepository extends JpaRepository<Employee, Long> {
}
```

### 集成[Druid连接池](https://github.com/alibaba/druid)

#### SpringBoot项目直接集成druid
- 连接池初始化的时候按照initialSize创建这么多个连接
- 有DB操作访问的时候，就从里面取一个
- 如果当前正在使用的连接=maxActve（默认8）,就会进入等待，没有到maxActive拿一个空闲连接，没有空闲就创建个新连接，等待超过maxWait则会报错
使用完毕还回去等待其它人用
- 每一个connection在连接池里都是有空闲时长的，允许最大空闲时长: minEvictableidleTimeMillis（默认30分钟），多久检测一次:timeBetweenEvictionRunsMillis（默认10秒）
- [配置参数列表](https://github.com/alibaba/druid/wiki/DruidDataSource%E9%85%8D%E7%BD%AE%E5%B1%9E%E6%80%A7%E5%88%97%E8%A1%A8)
- maxActive如何配置：设置成理论上的最大并发数
  - 最大并发如何评估：假设每分钟活跃用户1000，每个用户访问5个页面，按照1000*5/10=500个并发数
- 前端拿到连接后，设置连接池在达到一定时长后也会自动回收连接，**建议关闭**
  - removeAbandoned: true （默认false）
  - removeAbandonedTimeoutMillis: 60 （单位分钟）
  - logAbandoned: true
- [SpringBoot引入](https://github.com/alibaba/druid/tree/master/druid-spring-boot-starter)
  - SpringBoot2导入包`druid-spring-boot-starter`，SpringBoot3导入包`druid-spring-boot-3-starter`
  - 增加配置 `spring.datasrouce.type: com.alibaba.druid.pool.DruidDataSource`
  - 增加druid配置
  ```yaml
  spring:
    datasource:
        driver-class-name: com.mysql.cj.jdbc.Driver
        url: jdbc:mysql://192.168.2.200:3306/db_example?userUnicode=true&characterEncoding=UTF-8
        username: springuser
        password: ThePassword
        type: com.alibaba.druid.pool.DruidDataSource
        druid:
            initial-size: 10 # 初始化连接数
            max-active: 20 # 最大活跃连接数
            min-idle: 5 # 最小连接数
            max-wait: 60000 # 最大等待时间
            test-on-borrow: false # 申请连接时执行validationQuery检测连接是否有效，做了这个配置会降低性能。
            test-on-return: false # 归还连接时执行validationQuery检测连接是否有效，做了这个配置会降低性能。
            pool-prepared-statements: false
            use-global-data-source-stat: true
            # 有两个含义：
            # 1) Destroy线程会检测连接的间隔时间，如果连接空闲时间大于等于minEvictableIdleTimeMillis则关闭物理连接。
            # 2) testWhileIdle的判断依据，详细看testWhileIdle属性的说明
            time-between-eviction-runs-millis: 60000
            min-evictable-idle-time-millis: 300000 # 连接保持空闲而不被驱逐的最小时间
            validation-query: SELECT 1 FROM DUAL # 用来检测连接是否有效的sql，要求是一个查询语句
            # 建议配置为true，不影响性能，并且保证安全性。
            # 申请连接的时候检测，如果空闲时间大于timeBetweenEvictionRunsMillis，
            # 执行validationQuery检测连接是否有效。
            test-while-idle: true
            # 每个连接PScache的最大数量
            # 要启用PSCache，必须配置大于0，当大于0时，poolPreparedStatements自动触发修改为true。
            # 在Druid中，不会存在Oracle下PSCache占用内存过多的问题，可以把这个数值配置大一些，比如说100
            max-pool-prepared-statement-per-connection-size: 20
            ## 属性类型是字符串，通过别名的方式配置扩展插件，常用的插件有：
            # 监控统计用的filter:stat
            # 日志用的filter:log4j
            # 防御sql注入的filter:wall
            filters: stat,wall,log4j
            ## 打开mergesql功能，慢查询时间5s
            connection-properties: druid.stat.mergesql=true;druid.stat.slowSqlMills=5000
            ##### 监控相关配置 StatViewServlet配置，说明请参考Druid Wiki，配置_StatViewServlet配置
            stat-view-servlet:
                enabled: true #是否启用StatViewServlet（监控页面）默认值为false（考虑到安全问题默认并未启动，如需启用建议设置密码或白名单以保障安全）
                login-username: druidWatcher
                login-password: druidPassword
                reset-enable: false # 是否启用reset功能
  ```
#### docker部署druid进行全局监控

TODO

### 接入Shiro

- 增加shiro-springboot-start包
```xml
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-spring-boot-web-starter</artifactId>
    <version>1.13.0</version>
</dependency>
```
- 创建Shiro的配置类：创建一个类，用于配置Shiro的相关设置，如Realm、Session管理器等。
```java
@Configuration
public class ShiroConfig implements ShiroWebMvcConfigurationAware, ShiroWebSecurityManagerConfigurationAware,
        ShiroWebFilterConfigurationAware {

    @Override
    public void setShiroWebMvcConfiguration(ShiroWebMvcConfiguration configuration) {

    }

    @Override
    public void setShiroWebSecurityManagerConfiguration(ShiroWebSecurityManagerConfiguration configuration) {

    }

    @Override
    public void setShiroWebFilterConfiguration(ShiroWebFilterConfiguration configuration) {

    }

    @Bean
    public Realm realm() {
        // 创建自定义的Realm实现类
        // 可根据需要进行配置，如设置身份验证器、授权器等
        return new MyRealm();
    }

    @Bean
    public SessionManager sessionManager() {
        DefaultWebSessionManager sessionManager = new DefaultWebSessionManager();
        // 配置SessionManager，如设置超时时间、Cookie等
        return sessionManager;
    }

    @Bean
    public SecurityManager securityManager(Realm realm, SessionManager sessionManager) {
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        securityManager.setRealm(realm);
        securityManager.setSessionManager(sessionManager);
        // 配置SecurityManager，如设置RememberMe管理器、Cache管理器等
        return securityManager;
    }

    @Bean
    public ShiroFilterChainDefinition shiroFilterChainDefinition() {
        DefaultShiroFilterChainDefinition chainDefinition = new DefaultShiroFilterChainDefinition();
        // 配置URL的访问权限，如设置登录URL、注销URL等
        return chainDefinition;
    }
}
```
- 创建自定义的Realm实现类：创建一个类，继承自org.apache.shiro.realm.AuthorizingRealm，并实现相关的认证和授权逻辑。
```java
public class MyRealm extends AuthorizingRealm {

    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        // 处理身份验证逻辑，如从数据库中获取用户信息进行验证
        UsernamePasswordToken usernamePasswordToken = (UsernamePasswordToken) token;
        String username = usernamePasswordToken.getUsername();
        String password = new String(usernamePasswordToken.getPassword());

        // 根据用户名查询用户信息
        // ...

        // 返回身份验证信息
        return new SimpleAuthenticationInfo(username, password, getName());
    }

    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
        // 处理授权逻辑，如从数据库中获取用户的角色和权限信息
        // ...

        // 返回授权信息
        return null;
    }
}
```
- 配置Shiro的过滤器：在Spring Boot应用程序的配置类中添加ShiroFilterFactoryBean，用于配置Shiro的过滤器链。
```java
@Configuration
public class ShiroFilterConfig {

    @Bean
    public ShiroFilterFactoryBean shiroFilterFactoryBean(SecurityManager securityManager,
                                                         ShiroFilterChainDefinition shiroFilterChainDefinition) {
        ShiroFilterFactoryBean filterFactoryBean = new ShiroFilterFactoryBean();
        filterFactoryBean.setSecurityManager(securityManager);
        filterFactoryBean.setFilterChainDefinitionMap(shiroFilterChainDefinition.getFilterChainMap());
        // 配置登录URL和未授权URL等
        filterFactoryBean.setLoginUrl("/login");
        filterFactoryBean.setUnauthorizedUrl("/unauthorized");
        return filterFactoryBean;
    }
}
```
- 创建登录和注销的Controller：创建一个Controller类，用于处理登录和注销请求。
```java
@Controller
public class AuthController {

    @PostMapping("/login")
    public String login(String username, String password) {
        Subject subject = SecurityUtils.getSubject();
        UsernamePasswordToken token = new UsernamePasswordToken(username, password);
        try {
            subject.login(token);
            return "redirect:/home";
        } catch (AuthenticationException e) {
            // 处理登录失败的逻辑
            return "redirect:/login?error";
        }
    }

    @RequestMapping("/logout")
    public String logout() {
        SecurityUtils.getSubject().logout();
        return "redirect:/login";
    }
}
```

### 异步任务

- 在Spring Boot应用程序的配置类上添加@EnableAsync注解
```java
@Configuration
@EnableAsync
public class AsyncConfig {

}
```
- 创建一个包含异步方法的service类
```java
@Service
public class MyService {

    @Async
    public void asyncMethod() {
        // 异步执行的任务逻辑
        // ...
    }
}
```
- 即可在其他组件中注入`MyService`调用异步方法

### Spring WebFlux流式返回

- 添加包依赖
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-webflux</artifactId>
</dependency>
```
- Controller中通过`Mono`类返回
```java
@RestController
public class HelloController {

    @GetMapping("/hello")
    public Mono<String> hello() {
        return Mono.just("Hello, World!");
    }
}
```

### Swagger接入 

- 介绍
  - Swagger是一个用于设计、构建、文档化和使用RESTful风格的Web服务的开源工具集。它提供了一组工具和约定，可以帮助开发人员和团队更轻松地构建和维护API。
  - 架构：
    - Swagger规范（Swagger Specification）：它是一种用于描述和定义RESTful API的规范，通常使用JSON或YAML格式表示。Swagger规范定义了API的端点、参数、响应、错误处理等信息，以及与API相关的元数据。
    - Swagger UI：它是一个交互式的API文档工具，可以根据Swagger规范自动生成漂亮且易于浏览的API文档界面。Swagger UI提供了一个可视化的界面，展示API的端点、参数、示例请求和响应等信息，同时还支持在界面中进行API的调试和测试。
    - Swagger Codegen：它是一个用于根据Swagger规范生成API客户端代码的工具。Swagger Codegen可以根据Swagger规范自动生成多种编程语言的客户端代码模板，开发人员可以直接使用生成的代码来调用API，减少了手动编写API调用代码的工作量。
  - 功能：
    - API文档自动生成：Swagger可以根据API的定义自动生成详细的API文档，包括请求和响应的参数、示例、错误处理等信息，使得API的使用和理解更加方便。
    - 可视化API调试：Swagger UI提供了一个交互式的界面，开发人员可以在界面中直接测试和调试API，减少了使用其他工具或编写测试代码的需要。
    - 代码生成：Swagger Codegen可以根据Swagger规范自动生成API客户端代码，减少了手动编写API调用代码的工作量，提高了开发效率。
    - 接口约定：Swagger规范定义了API的结构和约定，可以帮助团队统一接口设计和开发规范，提高了团队协作的效率。


- 通过Springfox在SpringBoot中接入Swagger **【已过时，和SpringBoot3有兼容问题】**
  - pom.xml中增加swagger包
  ```xml
  <dependency>
      <groupId>io.springfox</groupId>
      <artifactId>springfox-swagger2</artifactId>
      <version>3.0.0</version>
  </dependency>
  <dependency>
      <groupId>io.springfox</groupId>
      <artifactId>springfox-swagger-ui</artifactId>
      <version>3.0.0</version>
  </dependency>
  ```
  - 增加swagger配置类
  ```java
  @Configuration
  @EnableSwagger2
  public class SwaggerConfig {
      @Bean
      public Docket api() {
          return new Docket(DocumentationType.SWAGGER_2)
                  .select()
                  .apis(RequestHandlerSelectors.basePackage("com.rickydemo.payroll")) // 指定扫描的包路径
                  .paths(PathSelectors.any())
                  .build()
                  .apiInfo(apiInfo());
      }

      private ApiInfo apiInfo() {
          return new ApiInfoBuilder()
                  .title("API文档") // 设置文档标题
                  .description("这是一个示例API文档") // 设置文档描述
                  .version("1.0.0") // 设置文档版本
                  .build();
      }
  }
  ```

- 通过SpringDoc在SpringBoot中接入Swagger【推荐】
  - [SpringDoc文档](https://springdoc.org/#Introduction)
  - 增加包依赖
  ```xml
  <!-- springdoc来整合swagger -->
  <dependency>
      <groupId>org.springdoc</groupId>
      <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
      <version>2.2.0</version>
  </dependency>
  ```
  - yaml增加配置
  ```yaml
  #swagger配置
  springdoc:
    swagger-ui:
      ### 指定路径
      path: /swagger-ui.html
  ```
  - 增加这两个配置即可通过`http://localhost:8080/swagger-ui.html`访问swagger页面
  ![](swagger1.png)
  - Controller注解说明
  ```java
  /**
   * @Tag 表示总目录
   * @Operation 方法的说明
   * @ApiResponses 返回类型
   */
  @Tag(name = "员工管理")
  @RestController
  public class EmployeeController {
      @Operation(summary = "查找单个员工")
      @ApiResponses(value = { @ApiResponse(responseCode = "400", description = "Invalid ID supplied"),
              @ApiResponse(responseCode = "404", description = "Order not found") })
      @GetMapping("/employees/{id}")
      public EntityModel<Employee> one(@PathVariable Long id) {

          Employee employee = employeeRepository.findById(id) //
                  .orElseThrow(() -> new EmployeeNotFoundException(id));

          return employeeModelAssembler.toModel(employee);
      }
  }
  ```


[SpringBoot笔记1.pdf](SpringBoot笔记1.pdf)
[SpringBoot笔记2.pdf](SpringBoot笔记2.pdf)