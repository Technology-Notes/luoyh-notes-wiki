## mvn download jar and source, docs.

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

## install jar to local:

```
mvn install:install-file -DgroupId=com.oracle -DartifactId=ojdbc14 -Dversion=10.2.0.2.0 -Dpackaging=jar -Dfile=ojdbc14-10.2.0.2.0.jar
```


```



单独构建某个模块，mvn有支持的选项：


-pl, --projects
        Build specified reactor projects instead of all projects
-am, --also-make
        If project list is specified, also build projects required by the list
-amd, --also-make-dependents
        If project list is specified, also build projects that depend on projects on the list
首先切换到工程的根目录，

单独构建模块 pingjuan-web，同时会构建 pingjuan-web 模块依赖的其他模块

$ mvn install -pl pingjuan-web -am
单独构建模块 pingjuan-common，同时构建依赖模块 pingjuan-common 的其他模块

$ mvn install -pl pingjuan-common -am -amd


多模块工程的打包命令参考：
-am --also-make 同时构建所列模块的依赖模块；
-amd -also-make-dependents 同时构建依赖于所列模块的模块；
-pl --projects <arg> 构建制定的模块，模块间用逗号分隔；
-rf -resume-from <arg> 从指定的模块恢复反应堆。
看英文的更助于理解：
-am,--also-make
If project list is specified, also build projects required by the list
-amd,--also-make-dependents
If project list is specified, also build projects that depend on projects
 on the list
-pl,--projects <arg>
Comma-delimited list of specified reactor projects to build instead of
 all projects.A project can be specified by [groupId]:
artifactId or by its relative path.
-rf,--resume-frome <arg>
Resume reactor from specified project
按需打包:
mvn -pl A -am install
上述命令的意思是指定构建Module A, 同时依据依赖树的路径，构建A的依赖（无论是直接还是间接的）。注意
这里的命令是install, 而不是package.



1、常用命令

　　　　1）创建一个Project

 

mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false -DarchetypeCatalog=local -DarchetypeCatalog=internal

　　mvn archetype:generate　　固定格式

　　-DgroupId　　　　　　　　　组织标识（包名）

　　-DartifactId　　　　　　　　项目名称

　　-DarchetypeArtifactId　　  指定ArchetypeId，maven-archetype-quickstart，创建一个Java Project；maven-archetype-webapp，创建一个Web Project

　　-DinteractiveMode　　　　　　是否使用交互模式

　　　　2）编译源代码

mvn compile
 　　　3）编译测试代码

mvn test-compile
 　　　4）清空

mvn clean
 　　　5）运行测试

mvn test
 　　　6）生产站点目录并打包

mvn site-deploy
 　　　7）安装当前工程的输出文件到本地仓库

mvn install
 　　　8）打包

mvn package
 　　　9）先清除再打包

mvn clean package
 　　　10）打成jar包

mvn jar:jar
　　　 11）生成eclipse项目　　

mvn eclipse:eclipse
　　　 12）查看帮助信息

mvn help:help
　　　13）查看maven有哪些项目类型分类

mvn archetype:generate -DarchetypeCatalog=intrenal

```

> spring-boot maven profile replace properties


```
<profiles>
		<profile>
			<id>dev</id>
			<activation>
				<activeByDefault>true</activeByDefault>
			</activation>
			<properties>
				<spring.profile.active>dev</spring.profile.active>
			</properties>
		</profile>
		<profile>
			<id>ga</id>
			<properties>
				<spring.profile.active>ga</spring.profile.active>
			</properties>
		</profile>
	</profiles>
	<build>
		<pluginManagement>
			<plugins>
				<plugin>
					<artifactId>maven-resources-plugin</artifactId>
					<configuration>
						<encoding>utf-8</encoding>
						<useDefaultDelimiters>true</useDefaultDelimiters>
					</configuration>
				</plugin>
			</plugins>
		</pluginManagement>
		<resources>
			<resource>
				<directory>src/main/resources</directory>
				<filtering>true</filtering>
			</resource>
		</resources>
	</build>
```

## multi module change version 

> mvn source:jar install -Dmaven.test.skip=true

```
Use versions:set from the versions-maven plugin:

mvn versions:set -DnewVersion=2.50.1-SNAPSHOT
It will adjust all pom versions, parent versions and dependency versions in a multi-module project.

If you made a mistake, do

mvn versions:revert
afterwards, or

mvn versions:commit
if you're happy with the results.




```

