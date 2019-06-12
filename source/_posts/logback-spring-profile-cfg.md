---
title: logback spring boot profile config
date: 2018-11-29 11:49:11
tags: dev
categories: dev
keywords: spring, spring boot, logback
---

logback-spring.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration scan="true" scanPeriod="30 seconds">

    <property name="LOG_FILE" value="test.log" />
    <springProfile name="dev">
        <property name="LOG_HOME" value="${user.dir}/logs" />
    </springProfile>
    <springProfile name="test">
        <property name="LOG_HOME" value="/root/app/apache-tomcat-8.5.30/logs" />
    </springProfile>
    <springProfile name="prod">
        <property name="LOG_HOME" value="I:/apache-tomcat-8.5.28/logs" />
    </springProfile>

    <!-- 彩色日志 -->
    <!-- 彩色日志依赖的渲染类 -->
    <conversionRule conversionWord="clr" converterClass="org.springframework.boot.logging.logback.ColorConverter" />
    <conversionRule conversionWord="wex" converterClass="org.springframework.boot.logging.logback.WhitespaceThrowableProxyConverter" />
    <conversionRule conversionWord="wEx" converterClass="org.springframework.boot.logging.logback.ExtendedWhitespaceThrowableProxyConverter" />
    <!-- 彩色日志格式 -->
    <property name="CONSOLE_LOG_PATTERN" value="${CONSOLE_LOG_PATTERN:-%clr(%d{yyyy-MM-dd HH:mm:ss.SSS}){faint} %clr(${LOG_LEVEL_PATTERN:-%5p}) %clr(${PID:- }){magenta} %clr(---){faint} %clr([%15.15t]){faint} %clr(%-40.40logger{39}){cyan} %clr(:){faint} %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}}" />
    
    <!-- 控制台输出 -->
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
        	<charset>utf8</charset>
        	<pattern>${CONSOLE_LOG_PATTERN}</pattern>
        </encoder>
    </appender>

    <!-- 按照每天生成日志文件 -->
    <appender name="FILE"  class="ch.qos.logback.core.rolling.RollingFileAppender">
        <File>${LOG_HOME}/${LOG_FILE}</File>
        <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
            <!-- rollover daily -->
            <fileNamePattern>${LOG_HOME}/${LOG_FILE}.%d{yyyy-MM-dd}.log.%i.gz</fileNamePattern>
            <!-- each file should be at most 100MB, keep 60 days worth of history, but at most 20GB -->
            <maxFileSize>100MB</maxFileSize>
            <maxHistory>60</maxHistory>
            <totalSizeCap>20GB</totalSizeCap>
            <!--日志文件保留天数-->
            <MaxHistory>30</MaxHistory>
        </rollingPolicy>

        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
        	<charset>utf8</charset>
            <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符-->
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
        </encoder>
    </appender>


    <logger name="jdbc.sqlonly" level="WARN"/>  
    <logger name="jdbc.sqltiming" level="INFO"/>  
    <logger name="jdbc.resultsettable" level="INFO"/>  
    <logger name="jdbc.resultset" level="WARN"/>  
    <logger name="jdbc.connection" level="WARN"/>  
    <logger name="jdbc.audit" level="WARN"/>
    <logger name="druid.sql.Statement" level="debug" />
    
    <root level="INFO">
        <appender-ref ref="STDOUT" />
        <appender-ref ref="FILE" />
    </root>
</configuration>

```
<!--more-->
