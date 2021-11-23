---
title: 解决maven库中无法引入Oracle_jdbc_driver
date: 2019-11-04 14:02:10
tags: 
- solved
comments: true
categories: 
- 学习
- 搭建相关
---

1.在maven库中直接引入oracle jdbc driver是不能下载到本地的，原因是oracle的jdbc工具包不是开放给公众下载的，需要用户是oracle的注册用户才能在oracle官网下载到。

2.解决方式：目前没有发现更简便的方法，只能从oracle官网上下载jdbc driver的包，然后从本地upload到本地仓库。

- 进入JDBC[下载网站](https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html)，选择自己需要的JDBC driver版本，点击进入。
![](1.png)

- 下载需要的JDBC driver的jar包

![](2.png)

- 如果还没有登陆oracle账号，需要你登陆以后才能下载，没有注册过的注册一个账号，这里不再赘述。

- 下载完成后，进入下载完成的文件夹。在这里需要手动执行命令，在该文件夹打开git窗口，如何在windows安装git请自行百度。执行如下命令。
<!-- more -->
  ```bash
  mvn deploy:deploy-file -Dmaven.test.skip=true -Dfile=ojdbc6.jar -DgroupId=com.oracle -DartifactId=ojdbc6 -Dversion=11.0.0.7 -Dpackaging=jar -DrepositoryId=localmaven -Durl=http://localhost/nexus/content/repositories/releases
  ```

  各个参数含义如下：

  -Dmaven.test.skip ：是否跳过jar包检测。这种官方的包，我们不需要对其进行测试，设置为true即可。

  -Dfile：需要上传的jar包名。建议直接在jar包所在目录执行上述命令，可以避免再输入jar包的地址。

  -DgroupId，-DartifactId，-Dversion：jar包在本地maven库中的地址。理论上这三个值都是自己定义的，只要自己能够区分出来即可。建议地址不要和官方的maven地址完全一致，改一个版本号，或者artifactId都行，这样可以避免自动从官方maven库下载jar包，导致还是不能引入。

  -Dpackaging：打包方式。一般选择下载的是jar包，这里也写jar就行。

  -DrepositoryId：部署的仓库ID。这个ID配置在maven的setings.xml中，参考如下：

![](3.png)

  自行写入本地仓库的用户名和密码。

  -Durl：部署的本地仓库的地址。

- 执行完成后，显示如下页面，就表示上传成功。

![](4.png)

- 在项目的pom.xml文件下需要做如下配置即可：

  ```xml
  <!--增加本地仓库地址-->
  <repositories>
      <repository>
          <id>java.net2</id>
          <url>http://localhost/nexus/content/groups/public/</url>
      </repository>
  </repositories>
  <!--引入oracle包-->
  <dependencies>
      <!--oracle-->
      <dependency>
          <groupId>com.oracle</groupId>
          <artifactId>ojdbc6</artifactId>
          <version>11.0.0.7</version>
      </dependency>
  </dependencies>
  ```