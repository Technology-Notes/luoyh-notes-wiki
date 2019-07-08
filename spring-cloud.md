```
68240 kafka.Kafka /opt/app/kafka/config/server.properties -Xmx1G -Xms1G -XX:+UseG1GC -XX:MaxGCPauseMillis=20 -XX:InitiatingHeapOccupancyPercent=35 -XX:+ExplicitGCInvokesConcurrent -Djava.awt.headless=true -Xloggc:/opt/app/kafka/bin/../logs/kafkaServer-gc.log -verbose:gc -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:+PrintGCTimeStamps -XX:+UseGCLogFileRotation -XX:NumberOfGCLogFiles=10 -XX:GCLogFileSize=100M -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.ssl=false -Dkafka.logs.dir=/opt/app/kafka/bin/../logs -Dlog4j.configuration=file:/opt/app/kafka/bin/../config/log4j.properties
71424 /opt/app/demo/demo-2.1.0.jar --spring.profiles.active=prod --spring.config.location=/opt/app/demo/demo.yml --server.port=8044
71476 /opt/app/test/test-2.1.0.jar --spring.profiles.active=prod --eureka.client.serviceUrl.defaultZone=http://e0:6975/eureka --server.port=6060
69463 eureka-server-1.0.0.jar --spring.profiles.active=prod --eureka.client.serviceUrl.defaultZone=http://e1:6976/eureka
69464 eureka-server-1.0.0.jar --spring.profiles.active=prod2 --eureka.client.serviceUrl.defaultZone=http://e1:6975/eureka
9993 sun.tools.jps.Jps -lVvm -Dapplication.home=/usr/java/jdk1.8.0_144 -Xms8m
70203 config-server-1.0.0.jar --eureka.client.serviceUrl.defaultZone=http://e0:6975/eureka --spring.profiles.active=native --spring.cloud.stream.kafka.binder.brokers=e0:9092 --eureka.instance.hostname=e0 --spring.cloud.stream.kafka.binder.zkNodes=e0:2181
68239 org.apache.zookeeper.server.quorum.QuorumPeerMain /opt/app/kafka/config/zookeeper.properties -Xmx512M -Xms512M -XX:+UseG1GC -XX:MaxGCPauseMillis=20 -XX:InitiatingHeapOccupancyPercent=35 -XX:+ExplicitGCInvokesConcurrent -Djava.awt.headless=true -Xloggc:/opt/app/kafka/bin/../logs/zookeeper-gc.log -verbose:gc -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:+PrintGCTimeStamps -XX:+UseGCLogFileRotation -XX:NumberOfGCLogFiles=10 -XX:GCLogFileSize=100M -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.ssl=false -Dkafka.logs.dir=/opt/app/kafka/bin/../logs -Dlog4j.configuration=file:/opt/app/kafka/bin/../config/log4j.properties
71070 /opt/app/run/run-2.1.0.jar --spring.profiles.active=prod --eureka.client.serviceUrl.defaultZone=http://e1:6975/eureka --server.port=8066

```





### eureka

| 请求名称                   | 请求方式 | HTTP地址                                                 | 请求描述                                                     |
| -------------------------- | -------- | -------------------------------------------------------- | ------------------------------------------------------------ |
| 注册新服务                 | POST     | /eureka/apps/`{appID}`                                   | 传递JSON或者XML格式参数内容，HTTP code为204时表示成功        |
| 取消注册服务               | DELETE   | /eureka/apps/`{appID}`/`{instanceID}`                    | HTTP code为200时表示成功                                     |
| 发送服务心跳               | PUT      | /eureka/apps/`{appID}`/`{instanceID}`                    | HTTP code为200时表示成功                                     |
| 查询所有服务               | GET      | /eureka/apps                                             | HTTP code为200时表示成功，返回XML/JSON数据内容               |
| 查询指定appID的服务列表    | GET      | /eureka/apps/`{appID}`                                   | HTTP code为200时表示成功，返回XML/JSON数据内容               |
| 查询指定appID&instanceID   | GET      | /eureka/apps/`{appID}`/`{instanceID}`                    | 获取指定appID以及InstanceId的服务信息，HTTP code为200时表示成功，返回XML/JSON数据内容 |
| 查询指定instanceID服务列表 | GET      | /eureka/apps/instances/`{instanceID}`                    | 获取指定instanceID的服务列表，HTTP code为200时表示成功，返回XML/JSON数据内容 |
| 变更服务状态               | PUT      | /eureka/apps/`{appID}`/`{instanceID}`/status?value=DOWN  | 服务上线、服务下线等状态变动，HTTP code为200时表示成功       |
| 变更元数据                 | PUT      | /eureka/apps/`{appID}`/`{instanceID}`/metadata?key=value | HTTP code为200时表示成功                                     |
| 查询指定IP下的服务列表     | GET      | /eureka/vips/`{vipAddress}`                              | HTTP code为200时表示成功                                     |
| 查询指定安全IP下的服务列表 | GET      | /eureka/svips/`{svipAddress}`                            | HTTP code为200时表示成功                                     |



- `{appID}`：服务名称，对应`spring.application.name`参数值
- `{instanceID}`：实例名称，如果已经自定义`instanceId`则对应`eureka.instance.instance-id`参数值





```
curl -v -X PUT http://localhost:10000/eureka/apps/HENGBOY-SPRING-CLOUD-EUREKA-PROVIDER/hengboy-spring-cloud-eureka-provider:20000:v1.0/status\?value\=DOWN

```


```
--spring.cloud.inetutils.use-only-site-local-interfaces=true
```

