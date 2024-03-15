---
title: maven使用教程
tags:
  - maven
categories:
  - maven
keywords: maven
index_img: /img/mvn/maven.png
excerpt: 一个开源的项目管理和构建自动化工具，用于管理Java项目的依赖、构建和部署，它通过标准化的项目结构和配置文件，帮助开发者更轻松地构建、测试和发布Java应用程序，提高了项目的可维护性和可扩展性。
abbrlink: cc2d81c9
date: 2019-11-07 19:46:24
---

# 下载maven

1.进入[官网](https://maven.apache.org/download.cgi)下载需要的版本 

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151402306.png)

# 安装maven

将下载的压缩包解压到**非中文、没有空格**的目录 , 在解压目录中，我们需要着重关注Maven的核心配置文件：conf/settings.xml

# 创建本地仓库

本地仓库这个目录，我们手动创建一个空的目录即可。

# 指定本地仓库

本地仓库默认值：用户家目录/.m2/repository。由于本地仓库的默认位置是在用户的家目录下，而家目录往往是在C盘，也就是系统盘。将来Maven仓库中jar包越来越多，仓库体积越来越大，可能会拖慢C盘运行速度，影响系统性能。所以建议将Maven的本地仓库放在其他盘符下。

配置方式如下：  
打开maven里的conf文件，打开setings.xml文件，配置本地仓库文件

```xml
<!-- localRepository
| The path to the local repository maven will use to store artifacts.
|
| Default: ${user.home}/.m2/repository
<localRepository>/path/to/local/repo</localRepository>
-->
<localRepository>D:\mavenRepository</localRepository>
<!--mavenRepository是自己创建的仓库名-->
```

**记住**：一定要把localRepository标签**从注释中拿出来**。

**注意**：本地仓库本身也需要使用一个**非中文、没有空格**的目录。

# 配置镜像仓库

> 为什么要配置阿里云提供的镜像仓库？

Maven下载jar包默认访问境外的中央仓库，速度很慢。改成阿里云提供的镜像仓库，访问国内网站，可以让Maven下载jar包的时候速度更快。

配置的方式是：

打开maven里的conf文件，打开setings.xml文件，

将下面mirror标签整体复制到mirrors标签的内部。

```xml
<mirrors>
    <mirror>
        <id>nexus-aliyun</id>
        <mirrorOf>central</mirrorOf>
        <name>Nexus aliyun</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public</url>
    </mirror>
</mirrors>
```

# 配置maven工厂的基础JDK版本

> 如果按照默认配置运行，Java工程使用的JDK版本是1.5。

配置的方式是：

打开maven里的conf文件，打开setings.xml文件，

将profile标签整个复制到profiles标签内。

```xml
<profiles>
    <profile>
      <id>jdk-1.8</id>
      <activation>
        <activeByDefault>true</activeByDefault>
        <jdk>1.8</jdk>
      </activation>
      <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
      </properties>
    </profile>
</profiles>
```

# 配置环境变量

> Maven是一个用Java语言开发的程序，它必须基于JDK来运行，需要通过JAVA_HOME来找到JDK的安装位置。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151405660.png)

可以使用下面的命令验证：

```text
C:\Users\Administrator>echo %JAVA_HOME%
D:\software\Java
C:\Users\Administrator>java -version
java version "1.8.0_141"
Java(TM) SE Runtime Environment (build 1.8.0_141-b15)
Java HotSpot(TM) 64-Bit Server VM (build 25.141-b15, mixed mode)
```

# 配置MAVEN_HOME

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151406492.png)

配置环境变量的规律：

XXX_HOME指向的是bin目录的上一级

PATH指向的是bin目录

**配置path：**

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151406834.png)

**验证：**

```text
C:\Users\Administrator>mvn -v
Apache Maven 3.5.4 (1edded0938998edf8bf061f1ceb3cfdeccf443fe; 2018-06-18T02:33:14+08:00)
Maven home: D:\software\apache-maven-3.5.4\bin\..
Java version: 1.8.0_141, vendor: Oracle Corporation, runtime: D:\software\Java\jre
Default locale: zh_CN, platform encoding: GBK
OS name: "windows 10", version: "10.0", arch: "amd64", family: "windows"
```

# 仓库和坐标

## maven仓库

maven的仓库就是maven存放jar包的位置，主要分为如下三类仓库

|仓库名称|作用|
|---|---|
|本地仓库|相当于缓存，工程第一次会从远程仓库（互联网）去下载jar 包，将jar包存在本地仓库（在程序员的电脑上）。第二次不需要从远程仓库去下载。先从本地仓库找，如果找不到才会去远程仓库找。|
|中央仓库|仓库中jar由专业团队（maven团队）统一维护。中央仓库的地址：[https://repo1.maven.org/maven2/](https://link.zhihu.com/?target=https%3A//repo1.maven.org/maven2/)|
|远程仓库|在公司内部架设一台私服，其它公司架设一台仓库，对外公开。|

## maven坐标

Maven的一个核心的作用就是管理项目的依赖，引入我们所需的各种jar包等。为了能自动化的解析任何一个Java构件，Maven必须将这些Jar包或者其他资源进行唯一标识，这是管理项目的依赖的基础，也就是我们要说的坐标。包括我们自己开发的项目，也是要通过坐标进行唯一标识的，这样才能在其它项目中进行依赖引用。坐标的定义元素如下：

- groupId:项目组织唯一的标识符，实际对应JAVA的包的结构 (一般写公司的组织名称 eg:com.itee,com.alibaba)
- artifactId: 项目的名称
- version：定义项目的当前版本

例如：要引入druid，只需要在pom.xml配置文件中配置引入druid的坐标即可：

```xml
<dependecies>
  <!--druid连接池-->
  <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>    
      <version>1.0.9</version>
  </dependency>
    <dependency>
      <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.6</version>
    </dependency>
    <dependency>
        <groupId>com.alibaba</groupId>
      <artifactId>fastjson</artifactId>    
      <version>1.1.39</version>
    </dependency>
</dependecies>
```

# 在idea中集成maven

**1. 配置maven软件的安装路径和本地仓库路径**

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151408008.png)

**配置Maven的Runner参数**

参数一: -DarchetypeCatalog=internal 解决使用骨架构建Maven项目时候会卡住的问题

参数二: -Dfile.encoding=GB2312 解决Maven工程中控制台输出中文乱码的问题

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151408832.png)

**注意事项:**

上述配置如果是在settings中进行配置则只会对当前Project生效，后续创建的新的Project不会生效

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151409913.png)

如果是在New Projects Setup里面的setting for new projects中配置，则会对后续创建的所有新的Project都生效，但是无法对当前项目生效。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151409245.png)

# maven构建命令

执行Maven构建命令有两种方式:

1. 如果是自己使用IDEA创建的Maven工程，可以自己直接在IDEA的Maven面板上执行相应的构建命令
2. 如果是该项目是从其它地方拷贝过来的，并且没有在IDEA上打开，则可以使用命令行来进行Maven构建

**注意事项:**

运行Maven中和构建操作相关的命令时，必须进入到pom.xml所在的目录。如果没有在pom.xml所在的目录运行Maven的构建命令，那么会看到下面的错误信息：

```xml
The goal you specified requires a project to execute but there is no POM in this directory
```
## 清理 clean

mvn clean命令是清除项目的编译内容，具体的效果是删除target目录

## 编译 compile

compile命令是对工程进行编译，具体效果是产生target目录，而编译又分为: 编译主程序和编译测试程序

### 编译主程序

mvn compile 命令是编译主程序，主程序编译之后的文件会生成在 target/classes 目录中

### 编译测试程序

mvn test-compile 命令是编译测试程序，测试程序编译之后的文件会生成在 target/test-classes 目录中

## 执行测试 test

mvn test会执行测试程序中的所有测试用例，并且将生成的测试报告存放在target/surefire-reports目录下。

在执行mvn test命令的过程中，会自动先执行mvn clean、mvn compile、mvn test-compile，然后再执行mvn test进行测试

## 打包 package

mvn package命令会对程序进行打包，如果是javase工程会打成jar包，而javaweb工程则会打成war包，打包得到的结果会生成在target目录中

在执行mvn package命令的过程中，会自动先执行mvn clean、mvn compile、mvn test-compile、mvn test，然后再执行mvn package进行打包

## 安装 install

mvn install 命令会将程序打成的包安装到本地仓库(针对jar包，war包安装到本地仓库意义不大)

在执行mvn install命令的过程中，会自动先执行mvn clean、mvn compile、mvn test-compile、mvn test、mvn package，然后再执行mvn package进行打包

安装的效果是将本地构建过程中生成的jar包存入Maven本地仓库。这个jar包在Maven仓库中的路径是根据它的坐标生成的。

坐标信息如下：

```xml
<groupId>com.fm.maven</groupId>
<artifactId>pro01-maven-java</artifactId>
<version>1.0-SNAPSHOT</version>
```

在Maven仓库中生成的路径如下：

```text
D:\mavenRepository\com\fm\maven\pro01-maven-java\1.0-SNAPSHOT\pro01-maven-java-1.0-SNAPSHOT.jar
```

另外，安装操作还会将pom.xml文件转换为XXX.pom文件一起存入本地仓库。所以我们在Maven的本地仓库中想看一个jar包原始的pom.xml文件时，查看对应XXX.pom文件即可，它们是名字发生了改变，本质上是同一个文件。

# 依赖

## 引入依赖

在maven项目中，我们只需要通过jar包的maven坐标去引入jar包就可以了，而不需要像之前一样手动导入jar包。至于每一个jar包的坐标到底怎么写，我们不需要记忆，写代码的时候可以直接复制，如果需要查找则可以到[http://mvnrepository.com/](https://link.zhihu.com/?target=http%3A//mvnrepository.com/)网站查找即可

例如，引入单元测试的依赖:

```xml
<dependencies>  
  <dependency>    
    <groupId>junit</groupId>    
    <artifactId>junit</artifactId>      
    <version>4.12</version>       
    <scope>test</scope>    
  </dependency>
  <dependencies>
```

当前项目的所有依赖都是放在`<dependencies>`标签里面，而具体每一个jar包的坐标则是放在一个`<dependency>`标签中

## 依赖范围

`<scope>`标签表示依赖的范围，它的位置是在dependencies/dependency/scope，该标签中的值可以是compile、test、provided、runtime这四个值中的一个，如果没有手动指定依赖范围，那么依赖范围默认是compile

**四种依赖范围的对比**

|依赖范围|main目录（空间）|test目录（空间）|编译阶段（时间）|运行阶段（时间）|
|---|---|---|---|---|
|compile|有效|有效|有效|有效|
|test|无效|有效|有效|无效|
|provided|有效|有效|有效|无效|
|runtime|无效|无效|无效|有效|

1. compile: 该范围的依赖既可以在main目录使用，又可以在test目录使用，即在编译阶段使用，又在实际运行阶段使用(会打到war/jar包中)，通常使用的第三方框架的jar包这样在项目实际运行时真正要用到的jar包都是以compile范围进行依赖的。比如SSM框架所需jar包。
2. test: 该范围的依赖只能在test目录中使用，不能在main目录中使用，只能在编译阶段使用，无法在项目运行阶段使用(不会打到war/jar包中)，引入junit单元测试依赖的时候会使用该范围
3. provided: 该范围的依赖即可在main目录使用，又可以在test目录使用，但是它只能在编译阶段使用，无法在项目运行阶段使用(不会打到war/jar包中)。比如说Tomcat服务器中内置有servlet-api、jsp-api等等依赖，所以我们在项目中引入这些依赖的时候的目标仅仅是**让我们的代码编译通过**， 为了避免和服务器上已有的同类jar包产生冲突，同时减轻服务器的负担我们**不会选择将这些依赖打到war包中**。说白了就是：“服务器上已经有了，你就别带啦！”
4. runtime: 该范围的依赖既不可在main目录使用，又不可在test目录使用，说白了就是它无法在编译阶段使用，只能在项目运行阶段使用(会打到war/jar包中)。比如说MySQL驱动的依赖，我们在开发(写代码)的过程中根本不需要使用MySQL驱动中的任何类(因为都是直接用的JDBC接口)，只在运行阶段需要MySQL驱动的依赖，所以我们就可以将MySQL驱动的范围指定为runtime这样能有助于提升编译效率

## 依赖的传递性和排除

### 依赖的传递性

依赖的传递性指的是: A依赖B，B依赖C，那么在A没有配置对C的依赖的情况下，A里面能不能直接使用C。 在A依赖B，B依赖C的前提下，C是否能够传递到A，取决于B依赖C时使用的依赖范围。

- B依赖C时使用compile范围：可以传递
- B依赖C时使用test或provided范围：不能传递，所以需要这样的jar包时，就必须在需要的地方明确配置依赖才可以。

### 依赖的排除

当A依赖B，B依赖C而且C可以传递到A的时候，但是A不想要C，需要在A里面把C排除掉。而往往这种情况都是为了避免jar包之间的冲突。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151412051.png)

所以配置依赖的排除其实就是阻止某些jar包的传递。因为这样的jar包传递过来会和其他jar包冲突。

**如何排除**

```xml
<dependency>  
  <groupId>org.springframework</groupId>
  <artifactId>spring-beans</artifactId> 
  <version>4.2.4.RELEASE</version>  
  <!--直接排除-->  
  <exclusions>  
    <exclusion>     
      <groupId>org.springframework</groupId>   
      <artifactId>spring-core</artifactId>     
    </exclusion>   
  </exclusions>
</dependency>
```

# 自定义属性

在pom.xml文件中的`<properties>`标签中添加子标签进行自定义属性，子标签的标签名就是属性名，子标签的标签体的内容就是属性值，例如:

```xml
<properties> 
  <fm.username>tom</fm.username>
</properties>
```

那么我们在pom.xml中就可以使用${属性名}来获取属性值

# 版本锁定

因为父工程在管理项目依赖的时候，需要管理非常多的依赖，而有很多依赖的版本都应该是相同的，比如spring框架的相关依赖都需要统一版本。

```xml
<dependencyManagement>   
  <dependencies>     
    <dependency>     
      <groupId>org.springframework</groupId> 
      <artifactId>spring-core</artifactId>    
      <version>4.0.0.RELEASE</version>   
    </dependency>    
    <dependency>      
      <groupId>org.springframework</groupId>    
      <artifactId>spring-beans</artifactId>     
      <version>4.0.0.RELEASE</version>   
    </dependency>      
    <dependency>        
      <groupId>org.springframework</groupId>     
      <artifactId>spring-context</artifactId>      
      <version>4.0.0.RELEASE</version>     
    </dependency>   
    <dependency>       
      <groupId>org.springframework</groupId>     
      <artifactId>spring-expression</artifactId>     
      <version>4.0.0.RELEASE</version>   
    </dependency>    
    <dependency>    
      <groupId>org.springframework</groupId>    
      <artifactId>spring-aop</artifactId>   
      <version>4.0.0.RELEASE</version>   
    </dependency>    
  </dependencies>
</dependencyManagement>
```

如果未进行版本锁定的话，我们修改spring框架的版本的时候，就需要手动将每一个依赖的版本都进行修改，这无疑是非常麻烦而且容易出错的操作

所谓版本锁定就是自定义一个属性，属性值就是要统一的版本，然后在`<version>`标签体中引入该属性值就行了。

```xml
<properties>
    <fm.spring.version>4.0.0.RELEASE</fm.username>
</properties>
<dependencyManagement>
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>${fm.spring.version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-beans</artifactId>
        <version>${fm.spring.version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>${fm.spring.version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-expression</artifactId>
        <version>${fm.spring.version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aop</artifactId>
        <version>${fm.spring.version}</version>
    </dependency>
</dependencies>
</dependencyManagement>
<!--进行版本锁定之后，如果有修改依赖版本的需求，则只需要取修改自定义属性的值就可以了-->
```