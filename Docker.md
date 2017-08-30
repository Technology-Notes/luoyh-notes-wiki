some command:

```
# pull
docker pull xxx

# run with background
docker run -dti -p 80:80 -p 81:81 -v host_path:image_path [--rm] image_id

# in container bash
docker exec -it container_id bash

# copy resource in container
docker cp host_path container_id:container_path

# inspect
docker inspect container_id

docker stop/start

docker ps [-a]

docker rm container_id

```

> build tomcat images

> dockerfile
```

FROM hub.c.163.com/library/centos
MAINTAINER luoyh
ADD jre-8u131.tar.gz /usr/local/
ADD tomcat.tar.gz /usr/local/

ENV JAVA_HOME /usr/local/jre1.8.0_131
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.0.M21
ENV CATALINA_BASE /usr/local/apache-tomcat-9.0.0.M21
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin

VOLUME ["/root/datas/luoyhtomcat"]

ENTRYPOINT /usr/local/apache-tomcat-9.0.0.M21/bin/startup.sh && tail -f /usr/local/apache-tomcat-9.0.0.M21/logs/catalina.out

EXPOSE 8080

```
> docker build -t luoyh/tomcat --rm=true .

> run command
```
docker run -dti \
-p 8080:8080 \
-v /root/datas/tomcat/webapps:/usr/local/apache-tomcat-9.0.0.M21/webapps \
-v /root/datas/tomcat/logs:/usr/local/apache-tomcat-9.0.0.M21/logs \
luoyh/tomcat
```

### Redis On Docker

```
docker pull redis
docker run -dti --name redis -p 6379:6379 -v /var/dd/redis:/data redis 
```
> Operator Redis With Docker redis-cli

```
docker run --rm -it --link redis:redis redis /bin/bash
xxx@abccd:/data# redis-cli -h redis -p 6379
redis:6379> ...
```


