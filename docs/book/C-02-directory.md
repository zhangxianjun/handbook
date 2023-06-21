# /

## /acl（防腐层，请求其他服务）
* Redis操作类
* 数据库操作类（mapper新建module）？
* 发送短信类
## /controller（接口层，响应其他服务）
* 根据业务量，可再次划分目录。
## /domain（业务实现层）
### /service
* 直接操作数据库
### /manager
* 大于1个service，需要调用一组数据库操作。
## /kit（支持层）
### /common
#### /log
#### /gateway
#### /response
#### /exception
### /utils（与业务无关的工具类）
* 时间处理类
* 网络请求库封装类

## /model（数据模型）
### /entity
* 数据库表结构一一对应，（MySQL、Redis、MongoDB）
### /enums
* 枚举类
### /req
* request body
### /vo
* response body


## Application（启动类）

## 配置文件
### 环境配置（application.yml）
* 数据库地址、用户名、密码等

### 程序相关（Config）
* redis的key，消息队列的主题
* 方法需要读取application.yml中的配置，先加载到Config文件中

### 业务相关
* 写入数据库

