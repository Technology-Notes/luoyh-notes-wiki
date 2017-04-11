# mvn download jar and source, docs.

```

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
    <groupId>temp.download</groupId>
    <artifactId>temp-download</artifactId>
    <version>1.0-SNAPSHOT</version> 
 
<!-- 
call mvn -f pom.xml dependency:copy-dependencies dependency:sources
pause
-->

    <dependencies>

<!-- https://mvnrepository.com/artifact/com.auth0/java-jwt -->
<dependency>
    <groupId>com.auth0</groupId>
    <artifactId>java-jwt</artifactId>
    <version>3.1.0</version>
</dependency>

    </dependencies>
</project>

```
