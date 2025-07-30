---
title: SpringBoot
tags:
  - Java
categories:
  - Java
  - Spring
  - Spring Boot
cover: https://image.runtimes.cc/202404051534895.png
abbrlink: 50077
date: 2023-07-15 14:58:26
updated: 2024-04-12 16:18:56
---

# 定时器
定时器的数值放到配置文件,如果使用`@Scheduled(fixedDelay = 类中的变量)`这种方式试过不行

```java
@Scheduled(fixedDelayString = "${fixedDelayString}")
public void open() {
}
```

```properties
fixedDelayString=30000
```

# RocketMQ

下载源码或者二进制包

```shell
# 源码安装
# 解压文件
unzip rocketmq-all-5.2.0-source-release.zip
cd rocketmq-all-5.2.0-source-release/
# 编译
mvn -Prelease-all -DskipTests -Dspotbugs.skip=true clean install -U
# 编译后的二进制的目录,配置文件在这里
cd ./distribution
```

修改配置文件

```shell
# 修改配置文件
# 修改内存配置,虚拟机的内存太小了
# vim ./bin/runserver.sh
JAVA_OPT="${JAVA_OPT} -server -Xms512m -Xmx521m -Xmn256g -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=320m"

# vim ./bin/runbroker.sh
JAVA_OPT="${JAVA_OPT} -server -Xms512m -Xmx512m"
```

启动

```shell
# 第一步.启动Name Server
sh bin/mqnamesrv
The Name Server boot success...
# 第二步.启动Broker
sh bin/mqbroker -n localhost:9876
 The broker[%s, 172.30.30.233:10911] boot success...
```

测试发送消息

```
export NAMESRV_ADDR=localhost:9876
# 发送消息
sh bin/tools.sh org.apache.rocketmq.example.quickstart.Producer
# 接收消息
sh bin/tools.sh org.apache.rocketmq.example.quickstart.Consumer
```

## 关闭服务

```
sh bin/mqshutdown broker
sh bin/mqshutdown namesrv
```

## 监控

```shell
# 记得checkout release-rocketmq-console-1.0.0分支
git clone https://github.com/apache/rocketmq-externals/tree/release-rocketmq-console-1.0.0

# 修改配置文件里的rocketMQ远程地址
NameSvrAddrList = 192.168.254.124:9876
# 或者
rocketmq.config.namesrvAddr=192.168.254.124:9876
```

## 代码

### 单个生产者消费者

```java
public class Provider {
    public static void main(String[] args) throws Exception {
        DefaultMQProducer producer = new DefaultMQProducer("group1");
        producer.setNamesrvAddr("192.168.254.190:9876");
        producer.start();
        String msg = "什么鬼,咋莫名其妙就收到了";
        Message message = new Message("topic1", "tag1", msg.getBytes());
        SendResult sendResult = producer.send(message);
        System.out.printf("%s%n", sendResult);
        producer.shutdown();
    }
}

public class Consumer {
    public static void main(String[] args) throws MQClientException {
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("group1");
        consumer.setNamesrvAddr("192.168.254.190:9876");
        consumer.subscribe("topic1", "*");
        consumer.registerMessageListener((MessageListenerConcurrently) (list, consumeConcurrentlyContext) -> {
            // 业务逻辑
            list.forEach(one->{
                byte[] body = one.getBody();
                System.out.println(new String(body));
            });
            return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
        });

        consumer.start();
        System.out.println("消费者启起来了");
    }
}
```

## 单个生产者多个消费者

### 相同的Topic,相同的Group-负载均衡

默认就是负载均衡,多个消费者,每个人收一条信息,不重复

![](https://image.runtimes.cc/202404051459670.png)

```java
public class Provider {

    public static void main(String[] args) throws Exception {
        DefaultMQProducer producer = new DefaultMQProducer("group2");
        producer.setNamesrvAddr("192.168.254.190:9876");
        producer.start();

        for (int i = 0; i < 10; i++) {
            String msg = "默认接受者是负载均衡"+i;
            Message message = new Message("topic2", "tag1", msg.getBytes());
            SendResult sendResult = producer.send(message);
            System.out.printf("%s%n", sendResult);
        }
        producer.shutdown();
    }
}

public class Consumer {
    public static void main(String[] args) throws MQClientException {
        // 谁来收
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("group2");
        // 从哪里收
        consumer.setNamesrvAddr("192.168.254.190:9876");
        // 监听队列
        consumer.subscribe("topic2", "*");
        // 监听器
        consumer.registerMessageListener((MessageListenerConcurrently) (list, consumeConcurrentlyContext) -> {
            // 业务逻辑
            list.forEach(one->{
                byte[] body = one.getBody();
                System.out.println(new String(body));
            });
            return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
        });

        consumer.start();
        System.out.println("消费者启起来了");
    }
}

// 第一个消费者
默认接受者是负载均衡0
默认接受者是负载均衡1
默认接受者是负载均衡4
默认接受者是负载均衡5
默认接受者是负载均衡8
默认接受者是负载均衡9

// 第二个消费者
默认接受者是负载均衡2
默认接受者是负载均衡3
默认接受者是负载均衡6
默认接受者是负载均衡7
```

### 相同的Topic,相同的Group-轮训

只是添加了消费者的一段代码:`consumer.setMessageModel(MessageModel.BROADCASTING);`

![](https://image.runtimes.cc/202404051459646.png)

```java
public class Provider {

    public static void main(String[] args) throws Exception {
        // 谁来发
        DefaultMQProducer producer = new DefaultMQProducer("group3");
        // 发给谁
        producer.setNamesrvAddr("192.168.254.190:9876");
        producer.start();

        for (int i = 0; i < 10; i++) {
            String msg = "默认接受者是负载均衡"+i;
            Message message = new Message("topic3", "tag1", msg.getBytes());
            SendResult sendResult = producer.send(message);
            System.out.printf("%s%n", sendResult);
        }
        producer.shutdown();
    }
}

public class Consumer {
    public static void main(String[] args) throws MQClientException {
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("group3");
        consumer.setNamesrvAddr("192.168.254.190:9876");
        consumer.subscribe("topic3", "*");
        consumer.setMessageModel(MessageModel.BROADCASTING);
        // 监听器
        consumer.registerMessageListener((MessageListenerConcurrently) (list, consumeConcurrentlyContext) -> {
            // 业务逻辑
            list.forEach(one->{
                byte[] body = one.getBody();
                System.out.println(new String(body));
            });
            return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
        });

        consumer.start();
        System.out.println("消费者启起来了");
    }
}

// 两个消费者都是收到相同的消息
默认接受者是负载均衡0
默认接受者是负载均衡1
默认接受者是负载均衡2
默认接受者是负载均衡3
默认接受者是负载均衡4
默认接受者是负载均衡5
默认接受者是负载均衡6
默认接受者是负载均衡7
默认接受者是负载均衡8
默认接受者是负载均衡9
```

### 不同的Topic,不同的Group

![](https://image.runtimes.cc/202404051459641.png)

```java
// 消费者1
public class Consumer {
    public static void main(String[] args) throws MQClientException {
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("group4");
        consumer.setNamesrvAddr("192.168.254.190:9876");
        consumer.subscribe("topic4", "*");
        // 监听器
        consumer.registerMessageListener((MessageListenerConcurrently) (list, consumeConcurrentlyContext) -> {
            // 业务逻辑
            list.forEach(one->{
                byte[] body = one.getBody();
                System.out.println(new String(body));
            });
            return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
        });

        consumer.start();
        System.out.println("消费者启起来了");
    }
}

// 消费者2
public class Consumer {
    public static void main(String[] args) throws MQClientException {
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("group4");
        consumer.setNamesrvAddr("192.168.254.190:9876");
        consumer.subscribe("topic4", "*");
        // 监听器
        consumer.registerMessageListener((MessageListenerConcurrently) (list, consumeConcurrentlyContext) -> {
            // 业务逻辑
            list.forEach(one->{
                byte[] body = one.getBody();
                System.out.println(new String(body));
            });
            return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
        });

        consumer.start();
        System.out.println("消费者启起来了");
    }
}

// 消费者3
public class Consumer {
    public static void main(String[] args) throws MQClientException {
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("group4-1");
        consumer.setNamesrvAddr("192.168.254.190:9876");
        consumer.subscribe("topic4", "*");
        // 监听器
        consumer.registerMessageListener((MessageListenerConcurrently) (list, consumeConcurrentlyContext) -> {
            // 业务逻辑
            list.forEach(one->{
                byte[] body = one.getBody();
                System.out.println(new String(body));
            });
            return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
        });

        consumer.start();
        System.out.println("消费者启起来了");
    }
}

// 消费者1
默认接受者是负载均衡2
默认接受者是负载均衡3
默认接受者是负载均衡6
默认接受者是负载均衡7

// 消费者2
默认接受者是负载均衡0
默认接受者是负载均衡1
默认接受者是负载均衡4
默认接受者是负载均衡5
默认接受者是负载均衡8
默认接受者是负载均衡9

// 消费者3
默认接受者是负载均衡0
默认接受者是负载均衡1
默认接受者是负载均衡2
默认接受者是负载均衡3
默认接受者是负载均衡4
默认接受者是负载均衡5
默认接受者是负载均衡6
默认接受者是负载均衡7
默认接受者是负载均衡8
默认接受者是负载均衡9
```



# Swagger
```xml
<!-- 引入Swagger3依赖 -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-boot-starter</artifactId>
    <version>3.0.0</version>
</dependency>
```

```java
import io.swagger.annotations.ApiOperation;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.oas.annotations.EnableOpenApi;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.service.Contact;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
/**
 * Swagger配置类
 */
@EnableOpenApi
@Configuration
public class SwaggerConfig {
    @Bean
    public Docket docket(){
        return new Docket(DocumentationType.OAS_30)
                .apiInfo(apiInfo()).enable(true)
                .select()
                //apis： 添加swagger接口提取范围
                .apis(RequestHandlerSelectors.basePackage("com.example.study.controller"))
                .apis(RequestHandlerSelectors.withMethodAnnotation(ApiOperation.class))
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo(){
        return new ApiInfoBuilder()
                .title("XX项目接口文档")
                .description("XX项目描述")
                .contact(new Contact("作者", "作者URL", "作者Email"))
                .version("1.0")
                .build();
    }
}
```

```powershell
# 原因是在springboot 2.6.0中将SpringMVC 默认路径匹配策略从AntPathMatcher
# 更改为PathPatternParser，导致出错，解决办法是切换回原先的AntPathMatcher
spring.mvc.pathmatch.matching-strategy=ant_path_matcher
```

# knife4j

```xml
<dependency>
    <groupId>com.github.xiaoymin</groupId>
    <artifactId>knife4j-spring-boot-starter</artifactId>
    <version>3.0.3</version>
</dependency>
```

application.properties

```xml
spring.mvc.pathmatch.matching-strategy=ant_path_matcher
knife4j.enable: true
```

```java
@RestController
@RequestMapping("/test")
@Api(tags = "测试2")
// 排序
@ApiSort(value = 1)
public class TestController2 {

    @GetMapping("/3")
    @ApiOperation("3")
    public String method(){
        return "hello wolrd";
    }

    @GetMapping("/4")
    @ApiOperation("4")
    public String method2(){
        return "hello wolrd";
    }
}
```

# Log4j2

## Log4j2

日志所有级别级别从上到下 ,级别从低到高,报错(更重要)这种,级别最高,info,debug相对不怎么重要

* all
* trace
* debug
* info
* warn
* error
* fail

Log4j2.xml配置例子

{% tabs Log4j2 %}

<!-- tab 抄网上的示例 -->

```
info级别输出到info.log
warn级别输出warn.log
error级别输出到error.log
debug级别输出到debug.log
```


```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
    <!--<Configuration status="WARN" monitorInterval="30"> -->
    <properties>
        <property name="LOG_HOME">./service-logs</property>
    </properties>
    <Appenders>
        <!--*********************控制台日志***********************-->
        <Console name="consoleAppender" target="SYSTEM_OUT">
            <!--设置日志格式及颜色-->
            <PatternLayout
                    pattern="%style{%d{ISO8601}}{bright,green} %highlight{%-5level} [%style{%t}{bright,blue}] %style{%C{}}{bright,yellow}: %msg%n%style{%throwable}{red}"
                    disableAnsi="false" noConsoleNoAnsi="false"/>
        </Console>

        <!--*********************文件日志***********************-->
        <!--all级别日志-->
        <RollingFile name="allFileAppender"
                     fileName="${LOG_HOME}/all.log"
                     filePattern="${LOG_HOME}/$${date:yyyy-MM}/all-%d{yyyy-MM-dd}-%i.log.gz">
            <!--设置日志格式-->
            <PatternLayout>
                <pattern>%d %p %C{} [%t] %m%n</pattern>
            </PatternLayout>
            <Policies>
                <!-- 设置日志文件切分参数 -->
                <!--<OnStartupTriggeringPolicy/>-->
                <!--设置日志基础文件大小，超过该大小就触发日志文件滚动更新-->
                <SizeBasedTriggeringPolicy size="100 MB"/>
                <!--设置日志文件滚动更新的时间，依赖于文件命名filePattern的设置-->
                <TimeBasedTriggeringPolicy/>
            </Policies>
            <!--设置日志的文件个数上限，不设置默认为7个，超过大小后会被覆盖；依赖于filePattern中的%i-->
            <DefaultRolloverStrategy max="100"/>
        </RollingFile>

        <!--debug级别日志-->
        <RollingFile name="debugFileAppender"
                     fileName="${LOG_HOME}/debug.log"
                     filePattern="${LOG_HOME}/$${date:yyyy-MM}/debug-%d{yyyy-MM-dd}-%i.log.gz">
            <Filters>
                <!--过滤掉info及更高级别日志-->
                <ThresholdFilter level="info" onMatch="DENY" onMismatch="NEUTRAL"/>
            </Filters>
            <!--设置日志格式-->
            <PatternLayout>
                <pattern>%d %p %C{} [%t] %m%n</pattern>
            </PatternLayout>
            <Policies>
                <!-- 设置日志文件切分参数 -->
                <!--<OnStartupTriggeringPolicy/>-->
                <!--设置日志基础文件大小，超过该大小就触发日志文件滚动更新-->
                <SizeBasedTriggeringPolicy size="100 MB"/>
                <!--设置日志文件滚动更新的时间，依赖于文件命名filePattern的设置-->
                <TimeBasedTriggeringPolicy/>
            </Policies>
            <!--设置日志的文件个数上限，不设置默认为7个，超过大小后会被覆盖；依赖于filePattern中的%i-->
            <DefaultRolloverStrategy max="100"/>
        </RollingFile>

        <!--info级别日志-->
        <RollingFile name="infoFileAppender"
                     fileName="${LOG_HOME}/info.log"
                     filePattern="${LOG_HOME}/$${date:yyyy-MM}/info-%d{yyyy-MM-dd}-%i.log.gz">
            <Filters>
                <!--过滤掉warn及更高级别日志-->
                <ThresholdFilter level="warn" onMatch="DENY" onMismatch="NEUTRAL"/>
            </Filters>
            <!--设置日志格式-->
            <PatternLayout>
                <pattern>%d %p %C{} [%t] %m%n</pattern>
            </PatternLayout>
            <Policies>
            <!-- 设置日志文件切分参数 -->
            <!--<OnStartupTriggeringPolicy/>-->
            <!--设置日志基础文件大小，超过该大小就触发日志文件滚动更新-->
            <SizeBasedTriggeringPolicy size="100 MB"/>
            <!--设置日志文件滚动更新的时间，依赖于文件命名filePattern的设置-->
            <TimeBasedTriggeringPolicy interval="1" modulate="true" />
            </Policies>
            <!--设置日志的文件个数上限，不设置默认为7个，超过大小后会被覆盖；依赖于filePattern中的%i-->
            <!--<DefaultRolloverStrategy max="100"/>-->
        </RollingFile>

        <!--warn级别日志-->
        <RollingFile name="warnFileAppender"
                     fileName="${LOG_HOME}/warn.log"
                     filePattern="${LOG_HOME}/$${date:yyyy-MM}/warn-%d{yyyy-MM-dd}-%i.log.gz">
            <Filters>
                <!--过滤掉error及更高级别日志-->
                <ThresholdFilter level="error" onMatch="DENY" onMismatch="NEUTRAL"/>
            </Filters>
            <!--设置日志格式-->
            <PatternLayout>
                <pattern>%d %p %C{} [%t] %m%n</pattern>
            </PatternLayout>
            <Policies>
                <!-- 设置日志文件切分参数 -->
                <!--<OnStartupTriggeringPolicy/>-->
                <!--设置日志基础文件大小，超过该大小就触发日志文件滚动更新-->
                <SizeBasedTriggeringPolicy size="100 MB"/>
                <!--设置日志文件滚动更新的时间，依赖于文件命名filePattern的设置-->
                <TimeBasedTriggeringPolicy/>
            </Policies>
            <!--设置日志的文件个数上限，不设置默认为7个，超过大小后会被覆盖；依赖于filePattern中的%i-->
            <DefaultRolloverStrategy max="100"/>
        </RollingFile>

        <!--error及更高级别日志-->
        <RollingFile name="errorFileAppender"
                     fileName="${LOG_HOME}/error.log"
                     filePattern="${LOG_HOME}/$${date:yyyy-MM}/error-%d{yyyy-MM-dd}-%i.log.gz">
            <!--设置日志格式-->
            <PatternLayout>
                <pattern>%d %p %C{} [%t] %m%n</pattern>
            </PatternLayout>
            <Policies>
                <!-- 设置日志文件切分参数 -->
                <!--<OnStartupTriggeringPolicy/>-->
                <!--设置日志基础文件大小，超过该大小就触发日志文件滚动更新-->
                <SizeBasedTriggeringPolicy size="100 MB"/>
                <!--设置日志文件滚动更新的时间，依赖于文件命名filePattern的设置-->
                <TimeBasedTriggeringPolicy/>
            </Policies>
            <!--设置日志的文件个数上限，不设置默认为7个，超过大小后会被覆盖；依赖于filePattern中的%i-->
            <DefaultRolloverStrategy max="100"/>
        </RollingFile>

        <!--json格式error级别日志-->
        <RollingFile name="errorJsonAppender"
                     fileName="${LOG_HOME}/error-json.log"
                     filePattern="${LOG_HOME}/error-json-%d{yyyy-MM-dd}-%i.log.gz">
            <JSONLayout compact="true" eventEol="true" locationInfo="true"/>
            <Policies>
                <SizeBasedTriggeringPolicy size="100 MB"/>
                <TimeBasedTriggeringPolicy interval="1" modulate="true"/>
            </Policies>
        </RollingFile>
    </Appenders>

    <Loggers>
        <!-- 根日志设置 -->
        <Root level="debug">
            <AppenderRef ref="allFileAppender" level="all"/>
            <AppenderRef ref="consoleAppender" level="debug"/>
            <AppenderRef ref="debugFileAppender" level="debug"/>
            <AppenderRef ref="infoFileAppender" level="info"/>
            <AppenderRef ref="warnFileAppender" level="warn"/>
            <AppenderRef ref="errorFileAppender" level="error"/>
            <AppenderRef ref="errorJsonAppender" level="error"/>
        </Root>

        <!--spring日志-->
        <Logger name="org.springframework" level="debug"/>
        <!--druid数据源日志-->
        <Logger name="druid.sql.Statement" level="warn"/>
        <!-- mybatis日志 -->
        <Logger name="com.mybatis" level="warn"/>
        <Logger name="org.hibernate" level="warn"/>
        <Logger name="com.zaxxer.hikari" level="info"/>
        <Logger name="org.quartz" level="info"/>
        <Logger name="com.andya.demo" level="debug"/>
    </Loggers>
</Configuration>
```
<!-- endtab -->

<!-- tab 实际开发用的-->
```
error级别 打印error.log
info级别 打印info,error 日志到info.log
debug级别 打印info,error,debug日志到debug.log
```

<!-- endtab -->
{% endtabs %}

基本结构是:

* Configuration
  * properties  属性配置,可以在这里配置全局变量,可以在xml别的地方引入
  * Appenders  具体配置日志框架该如何收集日志的动作
    * Console 控制台输出
    * RollingFile 滚动日志文件
  * Loggers
    * Root 理论上只有一个Root,level属性,是全局日志级别,如果AppenderRef没有配置level,就使用全局级别
      * AppenderRef  引用具体配置的动作,level 没有的话,就使用全局级别,就像`CSS`属性那样,标签里的属性优先级最高
    * Logger 单独配置一些类,level 没有的话,就使用全局级别,就像`CSS`属性那样,标签里的属性优先级最高

过滤器的具体使用方法

```xml
<Filters>
    <!--过滤掉info及更高级别日志-->
    <ThresholdFilter level="info" onMatch="DENY" onMismatch="NEUTRAL"/>
</Filters>
```

`level`指定日志等级的阈值。

`onMatch` 定义当日志事件等级等于或高于 `level` 属性指定的等级时的行为。
  - `ACCEPT`：接受日志事件，让它通过过滤器。
  - `DENY`：拒绝日志事件，不让它通过过滤器。
  - `NEUTRAL`：对日志事件不做决定，继续应用其他的过滤规则。

`onMismatch` 定义当日志事件等级低于 `level` 属性指定的等级时的行为。
  - `ACCEPT`：接受日志事件，让它通过过滤器。
  - `DENY`：拒绝日志事件，不让它通过过滤器。
  - `NEUTRAL`：对日志事件不做决定，继续应用其他的过滤规则。
```
if 日志的级别 >= level配置的级别
	ACCEPT  接受日志
	DENY  拒绝日志
	NEUTRAL 对日志事件不做决定，继续应用其他的过滤规则

if 日志的级别 < level配置的级别
	ACCEPT  接受日志
	DENY  拒绝日志
	NEUTRAL 对日志事件不做决定，继续应用其他的过滤规则
```

> 还是没有搞清楚 onMismatch="NEUTRAL"  的作用是怎样的
>
> 使用ChatGPT提供的demo,没有验证
>
> info级别  记录到info.log 
> error级别 记录到error.log 
> info级别和error级别  记录到dev.log
>
> ```xml
> <Configuration>
>     <Appenders>
>         <!-- Info 级别日志的 Appender -->
>         <File name="InfoAppender" fileName="logs/info.log">
>             <ThresholdFilter level="INFO" onMatch="ACCEPT" onMismatch="DENY"/>
>             <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
>         </File>
>         <!-- Error 级别日志的 Appender -->
>         <File name="ErrorAppender" fileName="logs/error.log">
>             <ThresholdFilter level="ERROR" onMatch="ACCEPT" onMismatch="DENY"/>
>             <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
>         </File>
>         <!-- 同时记录 Info 和 Error 级别日志的 Appender -->
>         <File name="DevAppender" fileName="logs/dev.log">
>             <Filters>
>                 <ThresholdFilter level="INFO" onMatch="ACCEPT" onMismatch="DENY"/>
>                 <ThresholdFilter level="ERROR" onMatch="ACCEPT" onMismatch="NEUTRAL"/>
>             </Filters>
>             <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
>         </File>
>     </Appenders>
>     <Loggers>
>         <Root level="DEBUG">
>             <AppenderRef ref="InfoAppender"/>
>             <AppenderRef ref="ErrorAppender"/>
>             <AppenderRef ref="DevAppender"/>
>         </Root>
>     </Loggers>
> </Configuration>
> ```

## 只是引入web框架

只是引入web框架

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

```java
@GetMapping("/test")
public String test(){
    // 格式化当前日期时间
    String formattedDateTime = LocalDateTime.now().format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss"));

    log.debug("当前日期时间：{}", formattedDateTime);
    log.info("当前日期时间：{}", formattedDateTime);
    log.error("当前日期时间：{}", formattedDateTime);
    log.warn("当前日期时间：{}", formattedDateTime);
    log.trace("当前日期时间：{}", formattedDateTime);
    return formattedDateTime;
}
```

```shell
# 控制台打印
INFO 64710 --- [nio-8080-exec-1] c.e.s.controller.IndexController         : 当前日期时间：2024-03-03 21:37:40
ERROR 64710 --- [nio-8080-exec-1] c.e.s.controller.IndexController         : 当前日期时间：2024-03-03 21:37:40
WARN 64710 --- [nio-8080-exec-1] c.e.s.controller.IndexController         : 当前日期时间：2024-03-03 21:37:40
# 为什么控制台没有打印 trace和debug日志
```