### 介绍

框架是工具而不是生活信条，我们一定要带着怀疑的态度审视每一个框架。是的，采用框架可能会很有帮助，但采用它们的成本呢？我们一定要懂得权衡如何使用一个框架，如何保护自己。


#### 一.服务级别
* 服务与服务
* 服务与Web


#### 二.代码级别
##### 1.思路
![](../img/dir_model.jpg ':size=706x181')


##### 2.目录

```text
src
└── main
    └── java
        ├── acl（防腐层，请求其他服务）
        ├── controller（接口层，响应其他服务）
        └── domain
            ├── service
            └── manager
        └── kit
            ├── config
            ├── exception
            ├── gateway
            ├── log
            └── utils
        └── model
            ├── enums（业务枚举）
            ├── po（实体类）
            ├── req（请求数据）
            └── vo（响应数据） 
        ├── Application
        └── Config（程序相关）
     └── resources
         ├── application.yml（环境配置）
         └── smart-doc.json（接口文档配置）
├── test
└── pom.xml
```

##### 3.状态码
* 2XX

200，响应数据以JSON形式放到Body里面，例如：{} 或者 []。

* 4XX

400，1.仅提示，例如：
```json
{"code":4000, "msg":"提示性错误，与用户操作相关"}
```
2.有操作，例如：
```json
{"code":4100, "msg":"需要用户根据code提示做操作"}
```

* 5XX

500
```json
{"code":5000, "msg":"提示性错误，服务器错误"}
```

##### 4.异常
* 400

业务处理异常：BusinessException。

* 500

请求短信服务、邮件服务：ExternalException；微服务之间内部调用：InnerException。

##### 5.日志
* 日志流程

日志输出到控制台，fluent-bit采集日志，存储到loki，Grafana查询

* 日志格式

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--debug：默认为false；若设置为true，打印出logback内部日志信息-->
<!--scan：默认值为true，若设置为true，配置文件如果发生改变，将会被重新加载-->
<!--scanPeriod：与scan配合使用，当scan为true时，此属性生效，默认的时间间隔为1分钟-->
<configuration>
    <!--默认上下文名称是default，用于标识应用-->
    <contextName>default</contextName>
    <!--定义属性-->
    <!--<property name="path" value="./log" />-->
    <!--定义属性，读取application.properties-->
    <!--<springProperty scope="context" name="log.path" source="catalina.base"/>-->
    <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
        <encoder charset="utf-8">
            <!--定义打印值：时间，接口名，用户唯一标识，线程，日志级别，类信息，业务信息 -->
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} %X{X_API_NAME} %X{X_USER_ID} [%thread] %-5level %logger{50} - %msg%n</pattern>
        </encoder>
    </appender>

    <root level="info">
        <appender-ref ref="console"/>
    </root>
</configuration>
```

```text
2023-06-23 20:40:29.401  INFO 47503 --- [nio-8080-exec-1] t.flint.archetype.kit.gateway.Gateway    : afterCompletion
2023-06-23 20:40:58.278  INFO 47503 --- [nio-8080-exec-2] t.flint.archetype.kit.gateway.Gateway    : preHandle
2023-06-23 20:41:03.973  INFO 47503 --- [nio-8080-exec-2] t.flint.archetype.kit.gateway.Gateway    : preHandlePOST
2023-06-23 20:41:03.976  INFO 47503 --- [nio-8080-exec-2] t.f.archetype.controller.DemoController  : query 18698581176
2023-06-23 20:41:03.977 ERROR 47503 --- [nio-8080-exec-2] t.f.a.k.g.ControllerExceptionAdvice      : 1==1


2023-06-23 20:46:06.048   INFO  [http-nio-8080-exec-1] team.flint.archetype.kit.gateway.Gateway - preHandle
2023-06-23 20:46:06.048   INFO  [http-nio-8080-exec-1] team.flint.archetype.kit.gateway.Gateway - preHandlePOST
2023-06-23 20:46:06.169   INFO  [http-nio-8080-exec-1] team.flint.archetype.controller.DemoController - query 18698581176
2023-06-23 20:46:10.520   ERROR [http-nio-8080-exec-1] t.f.a.kit.gateway.ControllerExceptionAdvice - 1==1
java.lang.RuntimeException: 1==1
```

```text
1. Filter1 (Before)
2. Filter2 (Before)
3. ...
4. HandlerInterceptor1 (preHandle)
5. HandlerInterceptor2 (preHandle)
6. ...
7. Controller's Method
8. ...
9. HandlerInterceptor2 (postHandle)
10. HandlerInterceptor1 (postHandle)
11. ...
12. ControllerAdvice (Exception Handling)
13. HandlerInterceptor2 (afterCompletion)
14. HandlerInterceptor1 (afterCompletion)
15. Filter2 (After)
16. Filter1 (After)

```

##### 6.性能



