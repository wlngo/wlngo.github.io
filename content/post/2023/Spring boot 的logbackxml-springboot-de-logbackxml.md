---
title: Spring boot 的logback.xml
date: 2023-03-15 15:46:24.022
updated: 2023-10-30 10:35:22.979
url: /archives/springboot-de-logbackxml
categories: 
tags: 
---

官方配置日志格式
```
<property name="CONSOLE_LOG_PATTERN" value="${CONSOLE_LOG_PATTERN:-%clr(%d{${LOG_DATEFORMAT_PATTERN:-yyyy-MM-dd HH:mm:ss.SSS}}){faint} %clr(${LOG_LEVEL_PATTERN:-%5p}) %clr(${PID:- }){magenta} %clr(---){faint} %clr([%15.15t]){faint} %clr(%-40.40logger{39}){cyan} %clr(:){faint} %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}}"/>
```
https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot/src/main/resources/org/springframework/boot/logging/logback/defaults.xml

正常模板 适用于spring boot 2
```
<?xml version="1.0" encoding="UTF-8"?>
<!-- 日志级别从低到高分为TRACE < DEBUG < INFO < WARN < ERROR < FATAL，如果设置为WARN，则低于WARN的信息都不会输出 -->
<!-- scan:当此属性设置为true时，配置文件如果发生改变，将会被重新加载，默认值为true -->
<!-- scanPeriod:设置监测配置文件是否有修改的时间间隔，如果没有给出时间单位，默认单位是毫秒。当scan为true时，此属性生效。默认的时间间隔为1分钟。 -->
<!-- debug:当此属性设置为true时，将打印出logback内部日志信息，实时查看logback运行状态。默认值为false。 -->
<configuration debug="false" scan="true" scanPeriod="1000 seconds">
    <contextName>logback</contextName>
    <!-- name的值是变量的名称，value的值时变量定义的值。通过定义的值会被插入到logger上下文中。定义变量后，可以使“${}”来使用变量。 -->
    <property name="log.path" value="./logs"/>
    <springProperty scope="context" name="spring.application.name" source="spring.application.name" defaultValue="app"/>
    <!-- 彩色日志 -->
    <!-- 彩色日志依赖的渲染类 -->
    <conversionRule conversionWord="clr" converterClass="org.springframework.boot.logging.logback.ColorConverter"/>
    <conversionRule conversionWord="wex"
                    converterClass="org.springframework.boot.logging.logback.WhitespaceThrowableProxyConverter"/>
    <conversionRule conversionWord="wEx"
                    converterClass="org.springframework.boot.logging.logback.ExtendedWhitespaceThrowableProxyConverter"/>
    <property name="CONSOLE_LOG_PATTERN"
              value="${CONSOLE_LOG_PATTERN:-%clr(%d{${LOG_DATEFORMAT_PATTERN:-yyyy-MM-dd HH:mm:ss.SSS}}){faint} %clr([%X{tid}]){faint} %clr(${LOG_LEVEL_PATTERN:-%5p}) %clr(${PID:- }){magenta} %clr(---){faint} %clr([%15.15t]){faint} %clr(%-40.40logger{39}){cyan} %clr(:){faint} %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}}"/>
    <property name="CONSOLE_LOG_CHARSET" value="${CONSOLE_LOG_CHARSET:-${file.encoding:-UTF-8}}"/>
    <property name="CONSOLE_LOG_THRESHOLD" value="${CONSOLE_LOG_THRESHOLD:-TRACE}"/>
    <property name="FILE_LOG_PATTERN"
              value="${FILE_LOG_PATTERN:-%d{${LOG_DATEFORMAT_PATTERN:-yyyy-MM-dd HH:mm:ss.SSS}} [%X{tid}] ${LOG_LEVEL_PATTERN:-%5p} ${PID:- } --- [%t] %-40.40logger{39} : %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}}"/>
    <property name="FILE_LOG_CHARSET" value="${FILE_LOG_CHARSET:-${file.encoding:-UTF-8}}"/>
    <property name="FILE_LOG_THRESHOLD" value="${FILE_LOG_THRESHOLD:-TRACE}"/>

    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>trace</level>
        </filter>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <charset>${CONSOLE_LOG_CHARSET}</charset>
            <Pattern>${CONSOLE_LOG_PATTERN}</Pattern>
        </encoder>
    </appender>

    <appender name="LOG_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 正在记录的日志文件的路径及文件名 -->
        <file>${log.path}/${spring.application.name}.log</file>
        <!--日志文件输出格式-->
        <encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
            <charset>${FILE_LOG_CHARSET}</charset>
        </encoder>
        <layout class="ch.qos.logback.classic.PatternLayout">
            <pattern>${FILE_LOG_PATTERN}</pattern>
        </layout>
        <!-- 日志记录器的滚动策略，按日期，按大小记录 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- 每天日志归档路径以及格式 -->
            <fileNamePattern>${log.path}/${spring.application.name}-%d{yyyy-MM-dd}.%i.log.zip</fileNamePattern>
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>100MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
            <!--日志文件保留天数-->
            <maxHistory>365</maxHistory>
        </rollingPolicy>

        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>trace</level>
        </filter>
    </appender>
    <!-- 不丢失日志.默认的,如果队列的80%已满,则会丢弃TRACT、DEBUG、INFO级别的日志 -->
    <!-- 更改默认的队列的深度,该值会影响性能.默认值为256 -->
    <!-- 添加附加的appender,最多只能添加一个 -->
    <appender name="LOG_ASYNC" class="ch.qos.logback.classic.AsyncAppender">
        <discardingThreshold>0</discardingThreshold>
        <queueSize>256</queueSize>
        <includeCallerData>true</includeCallerData>
        <appender-ref ref="LOG_FILE"/>
    </appender>


    <!--    additivity是否传递-->
    <!--    mybatis自动生成文件日志 -->
    <logger name="com.baomidou.mybatisplus.generator" level="debug" additivity="true">
    </logger>
    <logger name="top.wei.yhproprietaryinteresth5pre" level="info" additivity="false">
        <!-- 指定输出的appender -->
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="LOG_ASYNC"/>
    </logger>
    <root level="info">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="LOG_ASYNC"/>
    </root>
</configuration>
```
skywalking模板 适用于spring boot 2
```
<?xml version="1.0" encoding="UTF-8"?>
<!-- 日志级别从低到高分为TRACE < DEBUG < INFO < WARN < ERROR < FATAL，如果设置为WARN，则低于WARN的信息都不会输出 -->
<!-- scan:当此属性设置为true时，配置文件如果发生改变，将会被重新加载，默认值为true -->
<!-- scanPeriod:设置监测配置文件是否有修改的时间间隔，如果没有给出时间单位，默认单位是毫秒。当scan为true时，此属性生效。默认的时间间隔为1分钟。 -->
<!-- debug:当此属性设置为true时，将打印出logback内部日志信息，实时查看logback运行状态。默认值为false。 -->
<configuration debug="false" scan="true" scanPeriod="1000 seconds">
    <contextName>logback</contextName>
    <!-- name的值是变量的名称，value的值时变量定义的值。通过定义的值会被插入到logger上下文中。定义变量后，可以使“${}”来使用变量。 -->
    <property name="log.path" value="./logs"/>
    <springProperty scope="context" name="spring.application.name" source="spring.application.name" defaultValue="app"/>
    <!-- 彩色日志 -->
    <!-- 彩色日志依赖的渲染类 -->
    <conversionRule conversionWord="clr" converterClass="org.springframework.boot.logging.logback.ColorConverter"/>
    <conversionRule conversionWord="wex"
                    converterClass="org.springframework.boot.logging.logback.WhitespaceThrowableProxyConverter"/>
    <conversionRule conversionWord="wEx"
                    converterClass="org.springframework.boot.logging.logback.ExtendedWhitespaceThrowableProxyConverter"/>
    <property name="CONSOLE_LOG_PATTERN"
              value="${CONSOLE_LOG_PATTERN:-%clr(%d{${LOG_DATEFORMAT_PATTERN:-yyyy-MM-dd HH:mm:ss.SSS}}){faint} %clr([%X{tid}]){faint} %clr(${LOG_LEVEL_PATTERN:-%5p}) %clr(${PID:- }){magenta} %clr(---){faint} %clr([%15.15t]){faint} %clr(%-40.40logger{39}){cyan} %clr(:){faint} %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}}"/>
    <property name="CONSOLE_LOG_CHARSET" value="${CONSOLE_LOG_CHARSET:-${file.encoding:-UTF-8}}"/>
    <property name="CONSOLE_LOG_THRESHOLD" value="${CONSOLE_LOG_THRESHOLD:-TRACE}"/>
    <property name="FILE_LOG_PATTERN"
              value="${FILE_LOG_PATTERN:-%d{${LOG_DATEFORMAT_PATTERN:-yyyy-MM-dd HH:mm:ss.SSS}} [%X{tid}] ${LOG_LEVEL_PATTERN:-%5p} ${PID:- } --- [%t] %-40.40logger{39} : %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}}"/>
    <property name="FILE_LOG_CHARSET" value="${FILE_LOG_CHARSET:-${file.encoding:-UTF-8}}"/>
    <property name="FILE_LOG_THRESHOLD" value="${FILE_LOG_THRESHOLD:-TRACE}"/>

    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>trace</level>
        </filter>
        <encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
            <charset>${CONSOLE_LOG_CHARSET}</charset>
        </encoder>
        <!--        mdc接入skywalking-->
        <layout class="org.apache.skywalking.apm.toolkit.log.logback.v1.x.mdc.TraceIdMDCPatternLogbackLayout">
            <Pattern>${CONSOLE_LOG_PATTERN}</Pattern>
        </layout>
    </appender>

    <appender name="LOG_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 正在记录的日志文件的路径及文件名 -->
        <file>${log.path}/${spring.application.name}.log</file>
        <!--日志文件输出格式-->
        <encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
            <charset>${FILE_LOG_CHARSET}</charset>
        </encoder>
        <layout class="org.apache.skywalking.apm.toolkit.log.logback.v1.x.mdc.TraceIdMDCPatternLogbackLayout">
            <pattern>${FILE_LOG_PATTERN}</pattern>
        </layout>
        <!-- 日志记录器的滚动策略，按日期，按大小记录 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- 每天日志归档路径以及格式 -->
            <fileNamePattern>${log.path}/${spring.application.name}-%d{yyyy-MM-dd}.%i.log.zip</fileNamePattern>
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>100MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
            <!--日志文件保留天数-->
            <maxHistory>365</maxHistory>
        </rollingPolicy>

        <!-- 此日志文件只记录info级别的 此配置不适用skywalking 因为onMatch  onMismatch 两个字段-->
        <!--        <filter class="ch.qos.logback.classic.filter.LevelFilter">-->
        <!--            <level>info</level>-->
        <!--            <onMatch>ACCEPT</onMatch>-->
        <!--            <onMismatch>DENY</onMismatch>-->
        <!--        </filter>-->
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>trace</level>
        </filter>
    </appender>
    <!-- 不丢失日志.默认的,如果队列的80%已满,则会丢弃TRACT、DEBUG、INFO级别的日志 -->
    <!-- 更改默认的队列的深度,该值会影响性能.默认值为256 -->
    <!-- 添加附加的appender,最多只能添加一个 -->
    <appender name="LOG_ASYNC" class="ch.qos.logback.classic.AsyncAppender">
        <discardingThreshold>0</discardingThreshold>
        <queueSize>256</queueSize>
        <includeCallerData>true</includeCallerData>
        <appender-ref ref="LOG_FILE"/>
    </appender>

    <!--  skyWalking日志采集  -->
    <appender name="APM_LOG" class="org.apache.skywalking.apm.toolkit.log.logback.v1.x.log.GRPCLogClientAppender">
        <encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
            <charset>${FILE_LOG_CHARSET}</charset>
        </encoder>
        <!--        mdc接入skywalking-->
        <layout class="org.apache.skywalking.apm.toolkit.log.logback.v1.x.mdc.TraceIdMDCPatternLogbackLayout">
            <Pattern>${FILE_LOG_PATTERN}</Pattern>
        </layout>
        <!-- 此日志文件只记录info级别的 此配置不适用skywalking 因为onMatch  onMismatch 两个字段-->
        <!--        <filter class="ch.qos.logback.classic.filter.LevelFilter">-->
        <!--            <level>info</level>-->
        <!--            <onMatch>ACCEPT</onMatch>-->
        <!--            <onMismatch>DENY</onMismatch>-->
        <!--        </filter>-->
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>trace</level>
        </filter>
    </appender>

    <!--    additivity是否传递-->
    <!--    mybatis自动生成文件日志 -->
    <logger name="com.baomidou.mybatisplus.generator" level="debug" additivity="true">
    </logger>
    <logger name="top.wei" level="info" additivity="false">
        <!-- 指定输出的appender -->
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="LOG_ASYNC"/>
        <appender-ref ref="APM_LOG"/>
    </logger>
    <root level="info">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="LOG_ASYNC"/>
        <appender-ref ref="APM_LOG"/>
    </root>
</configuration>
```
正常模板 适用于spring boot 3
```
<?xml version="1.0" encoding="UTF-8"?>
<!-- 日志级别从低到高分为TRACE < DEBUG < INFO < WARN < ERROR < FATAL，如果设置为WARN，则低于WARN的信息都不会输出 -->
<!-- scan:当此属性设置为true时，配置文件如果发生改变，将会被重新加载，默认值为true -->
<!-- scanPeriod:设置监测配置文件是否有修改的时间间隔，如果没有给出时间单位，默认单位是毫秒。当scan为true时，此属性生效。默认的时间间隔为1分钟。 -->
<!-- debug:当此属性设置为true时，将打印出logback内部日志信息，实时查看logback运行状态。默认值为false。 -->
<configuration debug="false" scan="true" scanPeriod="1000 seconds">
    <contextName>logback</contextName>
    <!-- name的值是变量的名称，value的值时变量定义的值。通过定义的值会被插入到logger上下文中。定义变量后，可以使“${}”来使用变量。 -->
    <property name="log.path" value="./logs"/>
    <springProperty scope="context" name="spring.application.name" source="spring.application.name" defaultValue="app"/>
    <!-- 彩色日志 -->
    <!-- 彩色日志依赖的渲染类 -->
    <conversionRule conversionWord="clr" converterClass="org.springframework.boot.logging.logback.ColorConverter"/>
    <conversionRule conversionWord="wex"
                    converterClass="org.springframework.boot.logging.logback.WhitespaceThrowableProxyConverter"/>
    <conversionRule conversionWord="wEx"
                    converterClass="org.springframework.boot.logging.logback.ExtendedWhitespaceThrowableProxyConverter"/>
    <property name="CONSOLE_LOG_PATTERN"
              value="${CONSOLE_LOG_PATTERN:-%clr(%d{${LOG_DATEFORMAT_PATTERN:-yyyy-MM-dd HH:mm:ss.SSS}}){faint} %clr([%X{tid}]){faint} %clr(${LOG_LEVEL_PATTERN:-%5p}) %clr(${PID:- }){magenta} %clr(---){faint} %clr([%15.15t]){faint} %clr(%-40.40logger{39}){cyan} %clr(:){faint} %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}}"/>
    <property name="CONSOLE_LOG_CHARSET" value="${CONSOLE_LOG_CHARSET:-${file.encoding:-UTF-8}}"/>
    <property name="CONSOLE_LOG_THRESHOLD" value="${CONSOLE_LOG_THRESHOLD:-TRACE}"/>
    <property name="FILE_LOG_PATTERN"
              value="${FILE_LOG_PATTERN:-%d{${LOG_DATEFORMAT_PATTERN:-yyyy-MM-dd HH:mm:ss.SSS}} [%X{tid}] ${LOG_LEVEL_PATTERN:-%5p} ${PID:- } --- [%t] %-40.40logger{39} : %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}}"/>
    <property name="FILE_LOG_CHARSET" value="${FILE_LOG_CHARSET:-${file.encoding:-UTF-8}}"/>
    <property name="FILE_LOG_THRESHOLD" value="${FILE_LOG_THRESHOLD:-TRACE}"/>

    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>trace</level>
        </filter>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <charset>${CONSOLE_LOG_CHARSET}</charset>
            <Pattern>${CONSOLE_LOG_PATTERN}</Pattern>
        </encoder>
    </appender>

    <appender name="LOG_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 正在记录的日志文件的路径及文件名 -->
        <file>${log.path}/${spring.application.name}.log</file>
        <!--日志文件输出格式-->
        <encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
            <charset>${FILE_LOG_CHARSET}</charset>
        </encoder>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>${FILE_LOG_PATTERN}</pattern>
        </encoder>
        <!--         日志记录器的滚动策略，按日期，按大小记录 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
            <fileNamePattern>${log.path}/${spring.application.name}-%d{yyyy-MM-dd}.%i.zip</fileNamePattern>
            <maxFileSize>100mb</maxFileSize>
            <!--            默认情况下，maxHistory设置为零，即默认情况下不删除存档-->
            <maxHistory>0</maxHistory>
        </rollingPolicy>

        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>trace</level>
        </filter>
    </appender>
    <!-- 不丢失日志.默认的,如果队列的80%已满,则会丢弃TRACT、DEBUG、INFO级别的日志 -->
    <!-- 更改默认的队列的深度,该值会影响性能.默认值为256 -->
    <!-- 添加附加的appender,最多只能添加一个 -->
    <appender name="LOG_ASYNC" class="ch.qos.logback.classic.AsyncAppender">
        <discardingThreshold>0</discardingThreshold>
        <queueSize>256</queueSize>
        <includeCallerData>true</includeCallerData>
        <appender-ref ref="LOG_FILE"/>
    </appender>

    <!--    additivity是否传递-->
    <logger name="top.wei.oauth2server" level="info" additivity="false">
        <!-- 指定输出的appender -->
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="LOG_ASYNC"/>
    </logger>

    <logger name="org.springframework.security" level="trace" additivity="false">
        <!-- 指定输出的appender -->
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="LOG_ASYNC"/>
    </logger>

    <!--    sql日志-->
    <logger name="top.wei.oauth2server.mapper" level="debug" additivity="false">
        <!-- 指定输出的appender -->
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="LOG_ASYNC"/>
    </logger>

    <root level="info">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="LOG_ASYNC"/>
    </root>
</configuration>
```
skywalking模板 适用于spring boot 3
```
<?xml version="1.0" encoding="UTF-8"?>
<!-- 日志级别从低到高分为TRACE < DEBUG < INFO < WARN < ERROR < FATAL，如果设置为WARN，则低于WARN的信息都不会输出 -->
<!-- scan:当此属性设置为true时，配置文件如果发生改变，将会被重新加载，默认值为true -->
<!-- scanPeriod:设置监测配置文件是否有修改的时间间隔，如果没有给出时间单位，默认单位是毫秒。当scan为true时，此属性生效。默认的时间间隔为1分钟。 -->
<!-- debug:当此属性设置为true时，将打印出logback内部日志信息，实时查看logback运行状态。默认值为false。 -->
<configuration debug="false" scan="true" scanPeriod="1000 seconds">
    <contextName>logback</contextName>
    <!-- name的值是变量的名称，value的值时变量定义的值。通过定义的值会被插入到logger上下文中。定义变量后，可以使“${}”来使用变量。 -->
    <property name="log.path" value="./logs"/>
    <springProperty scope="context" name="spring.application.name" source="spring.application.name" defaultValue="app"/>
    <!-- 彩色日志 -->
    <!-- 彩色日志依赖的渲染类 -->
    <conversionRule conversionWord="clr" converterClass="org.springframework.boot.logging.logback.ColorConverter"/>
    <conversionRule conversionWord="wex"
                    converterClass="org.springframework.boot.logging.logback.WhitespaceThrowableProxyConverter"/>
    <conversionRule conversionWord="wEx"
                    converterClass="org.springframework.boot.logging.logback.ExtendedWhitespaceThrowableProxyConverter"/>
    <property name="CONSOLE_LOG_PATTERN"
              value="${CONSOLE_LOG_PATTERN:-%clr(%d{${LOG_DATEFORMAT_PATTERN:-yyyy-MM-dd HH:mm:ss.SSS}}){faint} %clr([%X{tid}]){faint} %clr(${LOG_LEVEL_PATTERN:-%5p}) %clr(${PID:- }){magenta} %clr(---){faint} %clr([%15.15t]){faint} %clr(%-40.40logger{39}){cyan} %clr(:){faint} %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}}"/>
    <property name="CONSOLE_LOG_CHARSET" value="${CONSOLE_LOG_CHARSET:-${file.encoding:-UTF-8}}"/>
    <property name="CONSOLE_LOG_THRESHOLD" value="${CONSOLE_LOG_THRESHOLD:-TRACE}"/>
    <property name="FILE_LOG_PATTERN"
              value="${FILE_LOG_PATTERN:-%d{${LOG_DATEFORMAT_PATTERN:-yyyy-MM-dd HH:mm:ss.SSS}} [%X{tid}] ${LOG_LEVEL_PATTERN:-%5p} ${PID:- } --- [%t] %-40.40logger{39} : %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}}"/>
    <property name="FILE_LOG_CHARSET" value="${FILE_LOG_CHARSET:-${file.encoding:-UTF-8}}"/>
    <property name="FILE_LOG_THRESHOLD" value="${FILE_LOG_THRESHOLD:-TRACE}"/>

    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>trace</level>
        </filter>
        <encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
            <charset>${CONSOLE_LOG_CHARSET}</charset>
            <layout class="org.apache.skywalking.apm.toolkit.log.logback.v1.x.mdc.TraceIdMDCPatternLogbackLayout">
                <Pattern>${CONSOLE_LOG_PATTERN}</Pattern>
            </layout>
        </encoder>
    </appender>

    <appender name="LOG_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 正在记录的日志文件的路径及文件名 -->
        <file>${log.path}/${spring.application.name}.log</file>
        <!--日志文件输出格式-->
        <encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
            <charset>${FILE_LOG_CHARSET}</charset>
            <layout class="org.apache.skywalking.apm.toolkit.log.logback.v1.x.mdc.TraceIdMDCPatternLogbackLayout">
                <pattern>${FILE_LOG_PATTERN}</pattern>
            </layout>
        </encoder>
        <!--         日志记录器的滚动策略，按日期，按大小记录 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
            <fileNamePattern>${log.path}/${spring.application.name}-%d{yyyy-MM-dd}.%i.zip</fileNamePattern>
            <maxFileSize>100mb</maxFileSize>
            <!--            默认情况下，maxHistory设置为零，即默认情况下不删除存档-->
            <maxHistory>0</maxHistory>
        </rollingPolicy>

        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>trace</level>
        </filter>
    </appender>
    <!-- 不丢失日志.默认的,如果队列的80%已满,则会丢弃TRACT、DEBUG、INFO级别的日志 -->
    <!-- 更改默认的队列的深度,该值会影响性能.默认值为256 -->
    <!-- 添加附加的appender,最多只能添加一个 -->
    <appender name="LOG_ASYNC" class="ch.qos.logback.classic.AsyncAppender">
        <discardingThreshold>0</discardingThreshold>
        <queueSize>256</queueSize>
        <includeCallerData>true</includeCallerData>
        <appender-ref ref="LOG_FILE"/>
    </appender>

    <!--  skyWalking日志采集  -->
    <appender name="APM_LOG" class="org.apache.skywalking.apm.toolkit.log.logback.v1.x.log.GRPCLogClientAppender">
        <encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
            <charset>${FILE_LOG_CHARSET}</charset>
            <layout class="org.apache.skywalking.apm.toolkit.log.logback.v1.x.mdc.TraceIdMDCPatternLogbackLayout">
                <Pattern>${FILE_LOG_PATTERN}</Pattern>
            </layout>
        </encoder>
        <!--        mdc接入skywalking-->

        <!-- 此日志文件只记录info级别的 此配置不适用skywalking 因为onMatch  onMismatch 两个字段-->
        <!--        <filter class="ch.qos.logback.classic.filter.LevelFilter">-->
        <!--            <level>info</level>-->
        <!--            <onMatch>ACCEPT</onMatch>-->
        <!--            <onMismatch>DENY</onMismatch>-->
        <!--        </filter>-->
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>trace</level>
        </filter>
    </appender>

    <!--    additivity是否传递-->
    <logger name="top.wei" level="info" additivity="false">
        <!-- 指定输出的appender -->
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="LOG_ASYNC"/>
        <appender-ref ref="APM_LOG"/>
    </logger>


<!--    &lt;!&ndash;    sql日志&ndash;&gt;-->
<!--    <logger name="top.wei.oauth2server.mapper" level="debug" additivity="false">-->
<!--        &lt;!&ndash; 指定输出的appender &ndash;&gt;-->
<!--        <appender-ref ref="CONSOLE"/>-->
<!--        <appender-ref ref="LOG_ASYNC"/>-->
<!--        <appender-ref ref="APM_LOG"/>-->
<!--    </logger>-->

    <root level="info">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="LOG_ASYNC"/>
        <appender-ref ref="APM_LOG"/>
    </root>
</configuration>
```