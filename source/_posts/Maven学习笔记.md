---
title: Maven学习笔记
date: 2017-01-16 14:13:57
tags: maven
---
# Maven 学习笔记

## 常用命令

Maven 命令基本格式
```
mvn <plugin-prefix>:<goal> -D<属性名>=<属性值>
mvn <phase1> <phase2> ...
```
<!--more-->
### archetype插件命令

- mvn archetype:generate 使用指定原型创建一个 Maven 项目。
    - 属性 interactiveMode=true/false 是否交互模式，默认true
    - 属性 groupId 
    - 属性 artifactId
    - 属性 package
    - 属性 archetypeAtifactId 指定建立项目使用的模板，如：maven-archetype-webapp
- mvn archetype:create-from-project 使用已有项目创建一个 Maven 项目。
- mvn archetype:crawl 从仓库中检索原型。


> mvn archetype:generate -DinteractiveMode=false -DgroupId=com.wentuotuo -DartifactId=testMaven -Dpackage=com.wentuotuo.testmaven

上面命令会在当前目录创建一个名叫testMaven的项目


### 编译 
> mvn compile

### 执行 
> mvn exec:java -Dexec.mainClass="com.wentuotuo.testMaven.App"

## Maven 项目目录约定
- 源代码：./src/main/java/
- 资源文件： ./src/main/resources/
- 测试代码： ./src/test/
- 编译成的class文件: ./target/classes/
- 项目整个打包的jar文件： ./target/

## Maven 生命周期
### clean生命周期
- pre-clean 在构建之前进行预清理
- clean 执行清理
- post-clean 最后清理
> mvn post-clean
该命令会清理项目编译过程中生成的文件，执行后testMaven中只留下src和pom.xml

### default生命周期
- compile 编译项目
- test 单元测试
- package 打包项目
- install 安装到本地仓库
- deploy 部署到远程仓库
当执行 mvn install 时，将会先执行install之前的3个阶段，完成后再执行install。这也是maven生命周期的重要特性。
### site生命周期
- pre-site 生成站点之前做验证
- site 生成站点
- post-site 生成站点之后做验证
- site-deploy 发布站点到远程服务器
> mvn post-site
该命令会在target目录下生成一个site子目录，打开其中index.html就可以看到生成的站点

### 生命周期绑定
pom.xml 见最下面。主要关注<build>节点中exec插件的配置。
> mvn compile
执行该条命令时，不仅仅会执行编译，还会执行exec:java -Dexec.mainClass="com.wentuotuo.testMaven.App"

## Maven 资源库 (repository)
### 本地资源库
通过 Maven 安装目录下的 conf\setting.xml 或者 用户 home 目录下的 m2\setting.xml 文件中的 <localRepository> 元素进行设置，默认本地仓库在 ${user.home}/.m2/repository。
```xml
<localRepository>E:\Config\Maven\repository</localRepository>
```
例如上面，我在我用户目录下的 m2\setting.xml 文件中设置了本地仓库地址

### 远程资源库
```xml
<!-- 远程资源库 -->
  <repositories>
        <repository>
            <id>public</id>
            <name>Public Repository</name>
            <url>http://yourhost:8081/nexus/content/groups/public/</url>
        </repository>
    </repositories>
```

**当 Maven 需要某个插件或者jar包时，总是按照 本地仓库 -> 远程仓库 -> 中央仓库的顺序去寻找。并且从远程和中央仓库下载的插件会保存在本地，下次引用的时候就不需要重新下载了。**

## pom.xml
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.wentuotuo</groupId> <!-- 项目开发者的域名 -->
  <artifactId>testMaven</artifactId> <!-- 项目名字 -->
  <packaging>jar</packaging> <!-- 指定项目打包的类型,默认jar -->
  <version>1.0-SNAPSHOT</version> <!-- 指定项目的版本 -->
  <name>mavenQs</name>
  <url>http://maven.apache.org</url>
  <!-- 定义项目使用的License -->
    <licenses>
    	<license>
    		<name>Apache 2</name>
    		<url>http://www.apache.org/licenses/LICENSE-2.0.txt</url>
    		<distribution>repo</distribution>
    		<comments>A business-freiendly OSS license</comments>
    	</license>
    </licenses>
    <!-- 声明该项目所属的组织 -->
    <organization>
    	<name>Ekimin</name>
    	<url>ekimin.github.io</url>
    </organization>
    <!-- 声明项目的开发者 -->
    <developers>
    	<developer>
    		<id>ymhe</id>
    		<name>ymhe</name>
    		<email>heyiminwork@gmail.com</email>
    		<url>ekimin.github.io</url>  		
    		<organization>Ekimin</organization>
    		<!-- 声明开发者角色 -->
    		<roles>
    			<role>developer</role>
    		</roles>
    		<timezone>+8</timezone>
    	</developer>
    </developers>
    <!-- 声明对项目有贡献的人 -->
    <contributors>
    	<contributor>
    		<name>george</name>
    		<email>georgehym@gmail.com</email>
    		<url>ekimin.github.io</url>  		
    		<organization>Ekimin</organization>
    		<roles>
    			<role>developer</role>
    		</roles>
    	</contributor>
    </contributors>
  <build>
      <plugins>
          <plugin>
              <groupId>org.codehaus.mojo</groupId>
              <artifactId>exec-maven-plugin</artifactId>
              <version>1.5.0</version>
              <executions>
              	<execution>
              		<!-- 指定绑定到compile阶段 -->
              		<phase>compile</phase>
              		<!-- 指定目标,这里goal就相当于mvn exec:java中的java -->
              		<goals>
              			<goal>java</goal>
              		</goals>
              		<!-- 指定插件参数，即是-D后面的参数 -->
              		<configuration>
              			<mainClass>com.wentuotuo.testMaven.App</mainClass>
              		</configuration>            		
              	</execution>            	
              </executions>
          </plugin>
      </plugins>
    </build>
  <!-- 远程资源库 -->
  <repositories>
        <repository>
            <id>public</id>
            <name>Public Repository</name>
            <url>http://yourhost:8081/nexus/content/groups/public/</url>
        </repository>
    </repositories>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>

```

## setting.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>

<!--
Licensed to the Apache Software Foundation (ASF) under one
or more contributor license agreements.  See the NOTICE file
distributed with this work for additional information
regarding copyright ownership.  The ASF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an
"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
KIND, either express or implied.  See the License for the
specific language governing permissions and limitations
under the License.
-->

<!--
 | This is the configuration file for Maven. It can be specified at two levels:
 |
 |  1. User Level. This settings.xml file provides configuration for a single user,
 |                 and is normally provided in ${user.home}/.m2/settings.xml.
 |
 |                 NOTE: This location can be overridden with the CLI option:
 |
 |                 -s /path/to/user/settings.xml
 |
 |  2. Global Level. This settings.xml file provides configuration for all Maven
 |                 users on a machine (assuming they're all using the same Maven
 |                 installation). It's normally provided in
 |                 ${maven.home}/conf/settings.xml.
 |
 |                 NOTE: This location can be overridden with the CLI option:
 |
 |                 -gs /path/to/global/settings.xml
 |
 | The sections in this sample file are intended to give you a running start at
 | getting the most out of your Maven installation. Where appropriate, the default
 | values (values used when the setting is not specified) are provided.
 |
 |-->
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
  <!-- localRepository
   | The path to the local repository maven will use to store artifacts.
   |
   | Default: ${user.home}/.m2/repository
   -->
  <localRepository>E:\Config\Maven\repository</localRepository>
  

  <!-- interactiveMode
   | This will determine whether maven prompts you when it needs input. If set to false,
   | maven will use a sensible default value, perhaps based on some other setting, for
   | the parameter in question.
   |
   | Default: true
  <interactiveMode>true</interactiveMode>
  -->

  <!-- offline
   | Determines whether maven should attempt to connect to the network when executing a build.
   | This will have an effect on artifact downloads, artifact deployment, and others.
   |
   | Default: false
  <offline>false</offline>
  -->

  <!-- pluginGroups
   | This is a list of additional group identifiers that will be searched when resolving plugins by their prefix, i.e.
   | when invoking a command line like "mvn prefix:goal". Maven will automatically add the group identifiers
   | "org.apache.maven.plugins" and "org.codehaus.mojo" if these are not already contained in the list.
   |-->
  <pluginGroups>
    <!-- pluginGroup
     | Specifies a further group identifier to use for plugin lookup.
    <pluginGroup>com.your.plugins</pluginGroup>
    -->
  </pluginGroups>

  <!-- proxies
   | This is a list of proxies which can be used on this machine to connect to the network.
   | Unless otherwise specified (by system property or command-line switch), the first proxy
   | specification in this list marked as active will be used.
   |-->
  <proxies>
    <!-- proxy
     | Specification for one proxy, to be used in connecting to the network.
     |
    <proxy>
      <id>optional</id>
      <active>true</active>
      <protocol>http</protocol>
      <username>proxyuser</username>
      <password>proxypass</password>
      <host>proxy.host.net</host>
      <port>80</port>
      <nonProxyHosts>local.net|some.host.com</nonProxyHosts>
    </proxy>
    -->
  </proxies>

  <!-- servers
   | This is a list of authentication profiles, keyed by the server-id used within the system.
   | Authentication profiles can be used whenever maven must make a connection to a remote server.
   |-->
  <servers>
    <!-- server
     | Specifies the authentication information to use when connecting to a particular server, identified by
     | a unique name within the system (referred to by the 'id' attribute below).
     |
     | NOTE: You should either specify username/password OR privateKey/passphrase, since these pairings are
     |       used together.
     |
     -->
    <server>
      <id>public</id>
      <username>admin</username>
      <password>admin123</password>
    </server>
    

    <!-- Another sample, using keys to authenticate.
    <server>
      <id>siteServer</id>
      <privateKey>/path/to/private/key</privateKey>
      <passphrase>optional; leave empty if not used.</passphrase>
    </server>
    -->
  </servers>

  <!-- mirrors
   | This is a list of mirrors to be used in downloading artifacts from remote repositories.
   |
   | It works like this: a POM may declare a repository to use in resolving certain artifacts.
   | However, this repository may have problems with heavy traffic at times, so people have mirrored
   | it to several places.
   |
   | That repository definition will have a unique id, so we can create a mirror reference for that
   | repository, to be used as an alternate download site. The mirror site will be the preferred
   | server for that repository.
   |-->
  <mirrors>
    <!-- mirror
     | Specifies a repository mirror site to use instead of a given repository. The repository that
     | this mirror serves has an ID that matches the mirrorOf element of this mirror. IDs are used
     | for inheritance and direct lookup purposes, and must be unique across the set of mirrors.
     |
     -->
    <mirror>
      <id>mirrorId</id>
      <mirrorOf>*</mirrorOf>
      <name>WTT mirror</name>
      <url>http://yourhost:8081/nexus/content/groups/public/</url>
    </mirror>
     
  </mirrors>

  <!-- profiles
   | This is a list of profiles which can be activated in a variety of ways, and which can modify
   | the build process. Profiles provided in the settings.xml are intended to provide local machine-
   | specific paths and repository locations which allow the build to work in the local environment.
   |
   | For example, if you have an integration testing plugin - like cactus - that needs to know where
   | your Tomcat instance is installed, you can provide a variable here such that the variable is
   | dereferenced during the build process to configure the cactus plugin.
   |
   | As noted above, profiles can be activated in a variety of ways. One way - the activeProfiles
   | section of this document (settings.xml) - will be discussed later. Another way essentially
   | relies on the detection of a system property, either matching a particular value for the property,
   | or merely testing its existence. Profiles can also be activated by JDK version prefix, where a
   | value of '1.4' might activate a profile when the build is executed on a JDK version of '1.4.2_07'.
   | Finally, the list of active profiles can be specified directly from the command line.
   |
   | NOTE: For profiles defined in the settings.xml, you are restricted to specifying only artifact
   |       repositories, plugin repositories, and free-form properties to be used as configuration
   |       variables for plugins in the POM.
   |
   |-->
  <profiles>
    <!-- profile
     | Specifies a set of introductions to the build process, to be activated using one or more of the
     | mechanisms described above. For inheritance purposes, and to activate profiles via <activatedProfiles/>
     | or the command line, profiles have to have an ID that is unique.
     |
     | An encouraged best practice for profile identification is to use a consistent naming convention
     | for profiles, such as 'env-dev', 'env-test', 'env-production', 'user-jdcasey', 'user-brett', etc.
     | This will make it more intuitive to understand what the set of introduced profiles is attempting
     | to accomplish, particularly when you only have a list of profile id's for debug.
     |
     | This profile example uses the JDK version to trigger activation, and provides a JDK-specific repo.
     -->
    <profile>
      <id>nexus</id>

     <!--  <activation>
        <jdk>1.4</jdk>
      </activation> -->

      <repositories>
        <repository>
          <id>public</id>
          <name>WTT repo</name>
          <url>http://wttrepo</url><!-- 假的地址 -->
          <layout>default</layout>          
        </repository>
      </repositories>
    </profile>
    

    <!--
     | Here is another profile, activated by the system property 'target-env' with a value of 'dev',
     | which provides a specific path to the Tomcat instance. To use this, your plugin configuration
     | might hypothetically look like:
     |
     | ...
     | <plugin>
     |   <groupId>org.myco.myplugins</groupId>
     |   <artifactId>myplugin</artifactId>
     |
     |   <configuration>
     |     <tomcatLocation>${tomcatPath}</tomcatLocation>
     |   </configuration>
     | </plugin>
     | ...
     |
     | NOTE: If you just wanted to inject this configuration whenever someone set 'target-env' to
     |       anything, you could just leave off the <value/> inside the activation-property.
     |
    <profile>
      <id>env-dev</id>

      <activation>
        <property>
          <name>target-env</name>
          <value>dev</value>
        </property>
      </activation>

      <properties>
        <tomcatPath>/path/to/tomcat/instance</tomcatPath>
      </properties>
    </profile>
    -->
  </profiles>

  <!-- activeProfiles
   | List of profiles that are active for all builds.
   |
   -->
  <activeProfiles>
    <activeProfile>nexus</activeProfile>
    <!-- <activeProfile>anotherAlwaysActiveProfile</activeProfile> -->
  </activeProfiles>
  
</settings>


```