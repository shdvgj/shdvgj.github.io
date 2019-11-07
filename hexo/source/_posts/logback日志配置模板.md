---
title: logback日志配置模板
date: 2019-11-05 16:47:55
categories: 
- 学习
- 搭建相关
---

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>

    <!-- 常量定义start -->
    <!-- 默认日志输出格式-->
    <property name="HOST_NAME" value="Project_Name"/>
    <property name="DEFAULT_PATTERN"
              value="%d{yyyy-MM-dd HH:mm:ss.SSS} ** ${HOST_NAME} ** [%thread] ** %level ** [%logger] ** - %file:%line ** [%method] - ** %msg%n"/>
    <property name="MAX_HISTORY" value="30"/>
    <property name="ASYNC_QUEUE_SIZE" value="1024"/>
    <property name="DEFAULT_CHARSET" value="UTF-8"/>
    <!-- 常量定义end -->

    <!-- 默认日志的console appender，本地使用的日志 -->
    <appender class="ch.qos.logback.core.ConsoleAppender" name="STDOUT">
        <encoder>
            <pattern>${DEFAULT_PATTERN}</pattern>
            <charset>${DEFAULT_CHARSET}</charset>
        </encoder>
    </appender>
    <appender class="ch.qos.logback.core.rolling.RollingFileAppender" name="ROLLING_FILE_DEFAULT">
        <append>true</append>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>E:/logs/${HOST_NAME}/${HOST_NAME}-%d{yyyyMMdd}.log</fileNamePattern>
            <maxHistory>${MAX_HISTORY}</maxHistory>
        </rollingPolicy>
        <encoder>
            <pattern>${DEFAULT_PATTERN}</pattern>
            <charset>${DEFAULT_CHARSET}</charset>
        </encoder>
    </appender>

    <springProfile name="!local">
        <!-- 服务器上使用的appender start -->
        <!-- 默认的file appender，按天切分日志 -->
        <appender class="ch.qos.logback.core.rolling.RollingFileAppender" name="ROLLING_FILE_DEFAULT">
            <append>true</append>
            <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
                <fileNamePattern>/home/logs/${HOSTNAME}/${HOSTNAME}-%d{yyyyMMdd}.log</fileNamePattern>
                <maxHistory>${MAX_HISTORY}</maxHistory>
            </rollingPolicy>
            <encoder>
                <pattern>${DEFAULT_PATTERN}</pattern>
                <charset>${DEFAULT_CHARSET}</charset>
            </encoder>
        </appender>

        <!-- 错误日志，按天切分 -->
        <appender class="ch.qos.logback.core.rolling.RollingFileAppender" name="ROLLING_FILE_ERROR">
            <filter class="ch.qos.logback.classic.filter.LevelFilter">
                <level>ERROR</level>
                <onMatch>ACCEPT</onMatch>
                <onMismatch>DENY</onMismatch>
            </filter>
            <append>true</append>
            <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
                <fileNamePattern>/home/logs/${HOSTNAME}/${HOSTNAME}_error-%d{yyyyMMdd}.log</fileNamePattern>
                <maxHistory>${MAX_HISTORY}</maxHistory>
            </rollingPolicy>
            <encoder>
                <pattern>${DEFAULT_PATTERN}</pattern>
                <charset>${DEFAULT_CHARSET}</charset>
            </encoder>
        </appender>

        <appender class="ch.qos.logback.classic.AsyncAppender" name="ASYNC_ROLLING_FILE_DEFAULT">
            <!-- 如果队列的80%已满,则会丢弃TRACT、DEBUG、INFO级别的日志 -->
            <discardingThreshold>-1</discardingThreshold>
            <!-- 更改默认的队列的深度,该值会影响性能.默认值为256 -->
            <queueSize>2048</queueSize>
            <includeCallerData>true</includeCallerData>
            <!-- 添加附加的appender,最多只能添加一个 -->
            <appender-ref ref="ROLLING_FILE_DEFAULT"/>
        </appender>

        <appender class="ch.qos.logback.classic.AsyncAppender" name="ASYNC_ROLLING_FILE_ERROR">
            <!-- 如果队列的80%已满,则会丢弃TRACT、DEBUG、INFO级别的日志 -->
            <discardingThreshold>-1</discardingThreshold>
            <!-- 更改默认的队列的深度,该值会影响性能.默认值为256 -->
            <queueSize>2048</queueSize>
            <includeCallerData>true</includeCallerData>
            <!-- 添加附加的appender,最多只能添加一个 -->
            <appender-ref ref="ROLLING_FILE_ERROR"/>
        </appender>

    </springProfile>

    <root level="info">
        <appender-ref ref="STDOUT"/>
        <appender-ref ref="ROLLING_FILE_DEFAULT"/>
    </root>

</configuration>

```

