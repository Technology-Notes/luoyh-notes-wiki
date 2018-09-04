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

And you can set dont create backup file. Use [generateBackupPoms=false]:

mvn versions:set -DnewVersion=2.33.1 -DgenerateBackupPoms=false 

It will adjust all pom versions, parent versions and dependency versions in a multi-module project.

If you made a mistake, do

mvn versions:revert
afterwards, or

mvn versions:commit
if you're happy with the results.




```


## deploy to repository

```
// in pom.xml addition:

<distributionManagement>
    <repository>
        <id>nexus-releases</id>
        <name>Nexus Snapshots Repository</name>
        <url>http://localhost:8081/nexus/content/repositories/releases/</url>
    </repository>
    <snapshotRepository>
        <id>nexus-snapshots</id>
        <name>Nexus Snapshots Repository</name>
        <url>http://localhost:8081/nexus/content/repositories/snapshots/</url>
    </snapshotRepository>
</distributionManagement>

// in settings.xml

<servers>
    <server>
        <id>nexus-releases</id>
        <username>deployment</username>
        <password>deployment01</password>
    </server>
    <server> 
        <id>nexus-snapshots</id>
        <username>deployment</username>
        <password>deployment02</password>
    </server>
</servers>

# server.id = repository.id


mvn deploy --settings /local/maven/conf/local.xml

```


```
java的-D命令行参数 mvn -D参数
java的-D命令行参数
我们会用mvn启动一个应用,如下的命令行:

MAVEN_OPTS="-XX:PermSize=256m -XX:MaxPermSize=512m" mvn spring-boot:run -Drun.jvmArguments="-Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=8000"
我们来解析一下这些命令行的意义.

-Dxxxx是java运行参数的语法
作用是配置一些环境变量，具体有哪些参数,参数有什么效果,和你用的实际环境、项目架构都有关
对，让程序知道log_path代表什么，具体程序怎么用，那是程序的事了

System.getProperty(“propertyName”) 比如你的就是System.getProperty(“log_path”)就可以拿到了
java的main函数都具有String[] 参数。这个参数可以通过-d来传递。
log_path这个会被存放在System.getProperty()中，
Property 是继承 hashtable的。可以通过System.getProperty(“log_path”)取得。

Java启动参数
Java启动参数共分为三类；
其一是标准参数（-），所有的JVM实现都必须实现这些参数的功能，而且向后兼容；
其二是非标准参数（-X），默认jvm实现这些参数的功能，但是并不保证所有jvm实现都满足，且不保证向后兼容；
其三是非Stable参数（-XX），此类参数各个jvm实现会有所不同，将来可能会随时取消，需要慎重使用；

mvn 命令行参数
mvn -v, –version 显示版本信息;

mvn -V, –show-version 显示版本信息后继续执行Maven其他目标;

mvn -h, –help 显示帮助信息; mvn -e, –errors 控制Maven的日志级别,产生执行错误相关消息;

mvn -X, –debug 控制Maven的日志级别,产生执行调试信息;

mvn -q, –quiet 控制Maven的日志级别,仅仅显示错误;

mvn -Pxxx 激活 id 为 xxx的profile (如有多个，用逗号隔开);

mvn -Dxxx=yyy 指定Java全局属性;

mvn -o , –offline 运行offline模式,不联网更新依赖;

mvn -N, –non-recursive 仅在当前项目模块执行命令,不构建子模块;

mvn -pl, –module_name 在指定模块上执行命令;

mvn -ff, –fail-fast 遇到构建失败就直接退出;

mvn -fn, –fail-never 无论项目结果如何,构建从不失败;

mvn -fae, –fail-at-end 仅影响构建结果,允许不受影响的构建继续;

mvn -C, –strict-checksums 如果校验码不匹配的话,构建失败;

mvn -c, –lax-checksums 如果校验码不匹配的话,产生告警;

mvn -U 强制更新snapshot类型的插件或依赖库(否则maven一天只会更新一次snapshot依赖);

mvn -npu, –no-plugin-updates 对任何相关的注册插件,不进行最新检查(使用该选项使Maven表现出稳定行为，该稳定行为基于本地仓库当前可用的所有插件版本);

mvn -cpu, –check-plugin-updates 对任何相关的注册插件,强制进行最新检查(即使项目POM里明确规定了Maven插件版本,还是会强制更新);

mvn -up, –update-plugins [mvn -cpu]的同义词;

mvn -B, –batch-mode 在非交互（批处理）模式下运行(该模式下,当Mven需要输入时,它不会停下来接受用户的输入,而是使用合理的默认值);

mvn -f, –file 强制使用备用的POM文件; mvn -s, –settings 用户配置文件的备用路径;

mvn -gs, –global-settings 全局配置文件的备用路径;

mvn -emp, –encrypt-master-password 加密主安全密码,存储到Maven settings文件里;

mvn -ep, –encrypt-password 加密服务器密码,存储到Maven settings文件里;

mvn -npr, –no-plugin-registry 对插件版本不使用~/.m2/plugin-registry.xml(插件注册表)里的配置

```
