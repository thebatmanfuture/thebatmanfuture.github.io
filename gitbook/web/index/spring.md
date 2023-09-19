#### 如何发现Spring Boot框架

1.网站的icon文件是一个绿色的树叶：

[![bqDIpD.png](https://s1.ax1x.com/2022/03/13/bqDIpD.png)](https://imgtu.com/i/bqDIpD)

2.页面存在特有的报错信息：

[![bqDT6H.png](https://s1.ax1x.com/2022/03/13/bqDT6H.png)](https://imgtu.com/i/bqDT6H)

#### 敏感信息泄露所影响的版本

`Spring Boot < 1.5`： 默认未授权访问所有端点。
`Spring Boot >= 1.5`： 默认只允许访问`/health`和`/info`端点，但是此安全性通常被应用程序开发人员禁用

~~~
路径					描述					默认启用
auditevents	显示当前应用程序的审计事件信息	Yes
beans	显示一个应用中所有Spring Beans的完整列表	Yes
conditions	显示配置类和自动配置类(configuration and auto-configuration	classes)的状态及它们被应用或未被应用的原因
configprops	显示一个所有@ConfigurationProperties的集合列表	Yes
env	显示来自Spring的 ConfigurableEnvironment的属性	Yes
flyway	显示数据库迁移路径，如果有的话	Yes
health	显示应用的健康信息（当使用一个未认证连接访问时显示一个简单	的’status’，使用认证连接访问则显示全部信息详情）
info	显示任意的应用信息	Yes
liquibase	展示任何Liquibase数据库迁移路径，如果有的话	Yes
metrics	展示当前应用的metrics信息	Yes
mappings	显示一个所有@RequestMapping路径的集合列表	Yes
scheduledtasks	显示应用程序中的计划任务	Yes
sessions	允许从Spring会话支持的会话存储中检索和删除(retrieval and deletion)	用户会话。使用Spring Session对反应性Web应用程序的支持时不可用。
shutdown	允许应用以优雅的方式关闭（默认情况下不启用）	No
threaddump	执行一个线程dump	Yes
heapdump	返回一个GZip压缩的hprof堆dump文件	Yes
jolokia	通过HTTP暴露JMX beans（当Jolokia在类路径上时，WebFlux不可用）	Yes
logfile	返回日志文件内容（如果设置了logging.file或logging.path属性的话），支持使用HTTP Range头接收日志文件内容的部分信息	Yes
prometheus	以可以被Prometheus服务器抓取的格式显示metrics信息	Yes
~~~

up有幸用awvs扫出来过一个Spring Boot Actuator v2配置不当，等下写

访问/actuator
如果网站设置了`management.endpoints.web.exposure.include`为`*`，那么我们可以在`/actuator`看到所有存在的断点：

[![bqrm3F.png](https://s1.ax1x.com/2022/03/13/bqrm3F.png)](https://imgtu.com/i/bqrm3F)

访问/actuator/version

[![bqrnc4.png](https://s1.ax1x.com/2022/03/13/bqrnc4.png)](https://imgtu.com/i/bqrnc4)

访问/env或者/actuator/env
获取环境属性
其中redis账户和信息泄露

##### 字典

~~~
%20/swagger-ui.html
actuator
actuator/auditevents
actuator/beans
actuator/conditions
actuator/configprops
actuator/env
actuator/health
actuator/heapdump
actuator/httptrace
actuator/hystrix.stream
actuator/info
actuator/jolokia
actuator/logfile
actuator/loggers
actuator/mappings
actuator/metrics
actuator/scheduledtasks
actuator/swagger-ui.html
actuator/threaddump
actuator/trace
api.html
api/index.html
api/swagger-ui.html
api/v2/api-docs
api-docs
auditevents
autoconfig
beans
caches
cloudfoundryapplication
conditions
configprops
distv2/index.html
docs
druid/index.html
druid/login.html
druid/websession.html
dubbo-provider/distv2/index.html
dump
entity/all
env
env/(name)
eureka
flyway
gateway/actuator
gateway/actuator/auditevents
gateway/actuator/beans
gateway/actuator/conditions
gateway/actuator/configprops
gateway/actuator/env
gateway/actuator/health
gateway/actuator/heapdump
gateway/actuator/httptrace
gateway/actuator/hystrix.stream
gateway/actuator/info
gateway/actuator/jolokia
gateway/actuator/logfile
gateway/actuator/loggers
gateway/actuator/mappings
gateway/actuator/metrics
gateway/actuator/scheduledtasks
gateway/actuator/swagger-ui.html
gateway/actuator/threaddump
gateway/actuator/trace
health
heapdump
heapdump.json
httptrace
hystrix
hystrix.stream
info
intergrationgraph
jolokia
jolokia/list
liquibase
logfile
loggers
mappings
metrics
monitor
prometheus
refresh
scheduledtasks
sessions
shutdown
spring-security-oauth-resource/swagger-ui.html
spring-security-rest/api/swagger-ui.html
static/swagger.json
sw/swagger-ui.html
swagger
swagger/codes
swagger/index.html
swagger/static/index.html
swagger/swagger-ui.html
swagger-dubbo/api-docs
swagger-ui
swagger-ui.html
swagger-ui/html
swagger-ui/index.html
system/druid/index.html
template/swagger-ui.html
threaddump
trace
user/swagger-ui.html
v1.1/swagger-ui.html
v1.2/swagger-ui.html
v1.3/swagger-ui.html
v1.4/swagger-ui.html
v1.5/swagger-ui.html
v1.6/swagger-ui.html
v1.7/swagger-ui.html
/v1.8/swagger-ui.html
/v1.9/swagger-ui.html
/v2.0/swagger-ui.html
v2.1/swagger-ui.html
v2.2/swagger-ui.html
v2.3/swagger-ui.html
v2/swagger.json
webpage/system/druid/index.html
~~~





**这是up对某银行系统测试出的漏洞，如图**

[![bqrs4f.png](https://s1.ax1x.com/2022/03/13/bqrs4f.png)](https://imgtu.com/i/bqrs4f)







































