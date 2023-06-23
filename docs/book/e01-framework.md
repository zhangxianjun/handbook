### 介绍
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



