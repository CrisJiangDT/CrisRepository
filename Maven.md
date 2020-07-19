<link href="http://cdn.bootcss.com/highlight.js/8.0/styles/monokai_sublime.min.css" rel="stylesheet">  
<script src="http://cdn.bootcss.com/highlight.js/8.0/highlight.min.js"></script>  
<script>hljs.initHighlightingOnLoad();</script>

# Maven

- Maven是一款服务于Java平台的**自动化构建工具**

### Maven的必要性

- 不使用Maven前，项目存在的问题：
 - 一个项目就是一个工程，不利于实现分工协作**（Maven可以将一个项目拆分成多个工程）**
 - 项目中所需要的jar包需要手动添加到Web-INF/lib目录下，不同项目中用到相同的jar包时，浪费存储空间**（Maven将jar包存储在“仓库”中，项目需要用到jar包时只需“引用”这个jar包的接口，而不需要真的把jar包复制过来）**
 - jar包需要自己准备**（所有知名框架或第三方jar包都已经以统一的规范存放在Maven的中央仓库中）**
 - jar包可能有多个依赖jar包，也需要自己准备**（Maven会自动导入依赖jar包）**

### Maven自动化构建

- Maven自动化构建的环节：
 1. 清理：将以前编译得到的旧的class字节码文件删除，为下一次编译做准备
 2. 编译：将Java源程序编译成class字节码文件
 3. 测试：自动测试，自动调用junit
 4. 报告：报告测试结果
 5. 打包：Java项目打jar包，JavaWeb项目打war包
 6. 安装：将打包得到的文件复制到“仓库”中的指定位置，是Maven特定的概念
 7. 部署：将JavaWeb项目生成的war包复制到Servlet容器的指定目录下，使其可以运行

### Maven工程约定的目录结构

- Maven约定的目录结构：
```
Hello（Maven工程名）
|--->src（存放源码）
|---|--->>main（存放主程序）
|---|---|--->>>java（存放源码）
|---|---|--->>>resources（存放框架或其他工具的配置文件）
|---|--->>test（存放测试程序）
|---|---|--->>>java（存放源码）
|---|---|--->>>resources（存放框架或其他工具的配置文件）
|--->pom.xml（Maven工程的核心配置文件）
```

### Maven的常用命令

- Maven的常用命令：
 - `mvn clean` 清理（删除之前编译得到的target目录）
 - `mvn compile` 编译主程序（编译主程序，生成target目录）
 - `mvn test-compile` 编译测试程序（编译主程序和测试程序，生成target目录）
 - `mvn test` 执行测试程序
 - `mvn packet` 打包（生成jar包或war包或pom文件）
 - `mvn install` 安装
 - `mvn site` 生成站点

### POM

- **POM的含义：Project Object Model，项目对象模型**
- pom.xml是Maven工程的核心配置文件

### Maven中的坐标

- **Maven中的坐标（GAV）**：使用下面三个向量来**唯一地定位一个Maven工程**：
 1. **groupid**：公司或组织域名倒序+项目名，如`<groupid>com.atguigu.maven</groupid>`
 2. **artifactid**：模块名，如`<artifactid>Hello</artifactid>`
 3. **version**：版本，如`<version>1.0.1</version>`
- Maven工程的坐标与仓库中的路径之间有对应关系，例如坐标为：
```
<groupid>org.springframework</groupid>
<artifactid>spring-core</artifactid>
<version>4.0.0.RELEASE<version>
```
则在仓库中的路径为：`org/springframework/spring-core/4.0.0.RELEASE/spring-core-4.0.0.RELEASE.jar`

### Maven中的仓库

- Maven中的仓库分类：
 - 本地仓库：在当前电脑上部署的仓库目录，为当前电脑上所有的Maven工程服务
 - 远程仓库：
 1. 私服：搭建在局域网中，为局域网范围内所有的Maven工程服务
 2. 中央仓库：搭建在Internet上，为全世界所有的Maven工程服务
 3. 中央仓库镜像：分担中央仓库的流量
- 仓库中保存的内容：Maven工程，如：
 - Maven自身所需的插件
 - 第三方框架或工具的jar包
 - 我们自己开发的Maven工程

### Maven中的依赖

- **Maven解析依赖信息时，会到本地仓库中查找被依赖的jar包**
- 要依赖我们自己开发的Maven工程，需要使用`mvn install`命令将其安装到本地仓库中
- Maven中的依赖范围：
 - **compile范围的依赖：对主程序、测试程序都有效，且参与打包和部署**
 - **test范围的依赖：**仅针对测试，**对主程序无效，对测试程序有效，且不参与打包和部署**（典型的例子是junit）
 - **provided范围的依赖：**只在开发阶段需要，部署后由Servlet容器（如Tomtact）提供，**对主程序、测试程序都有效，但不参与打包和部署**（典型的例子是servlet-api.jar）
 - **runtime范围的依赖：**对主程序无效，对测试程序有效，且参与打包和部署**（典型的例子是JDBC驱动包）**
- Maven中的依赖需要在pom.xml中进行配置，如：
```
<dependencies>
	<dependency>
		<groupId>junit</groupId>
		<artifactId>junit</artifactId>
		<version>4.9</version>
		<scope>test</scope>
	</dependency>
</dependencies>
```

##### Maven中的依赖的传递性

- **Maven中的依赖具有传递性**，例如：C依赖B，B依赖A，则在C中只需要声明依赖B，就会自动间接依赖A
- **注意：非compile范围的依赖不能传递**
- 依赖的传递性的好处：可以传递的依赖不必在每个模块中的都声明，在“最底层”的模块中声明一次即可

##### Maven中的依赖的排除

- 例如C依赖B，B依赖A，但C不想间接依赖A（可能A是不稳定jar包，不希望加入），则可以设置依赖的排除，在pom.xml的`<dependency>`中配置：
```
<exclusions>
	<exclusion>
		<groupId>...A</groupId>
		<artifactId>...A</artifactId>
	</exclusion>
</exclusions>
```

##### Maven中的依赖的原则

- jar包冲突问题：
 1. 路径不同时，**就近原则**：若C依赖B，B依赖A和xxx.2，A依赖xxx.1，则C依赖xxx.2，而不依赖xxx.1
 2. 路径相同时，**先声明者优先原则**：若C依赖A和B，A依赖xxx.1，B依赖xxx.2，则在C的pom.xml的`<dependencies>`中，若A先声明，C就依赖xxx.1，若B先声明，C就依赖xxx.2

##### 统一管理依赖的版本号

- 例如当前项目的Spring的各个jar包的版本都是4.0.0，要想全都升级为4.1.1，在pom.xml中一个个手动改不靠谱，可以在pom.xml中**在properties标签中进行宏定义**，如：
```
<properties>
	<spring-version>4.1.1-RELEASE<spring-version>	<!--以后升级版本号只需要改这里即可-->
</properties>
...
...
...
<dependencies>
	...
	<dependency>
		<groupId>...</groupId>
		<artifactId>...</artifactId>
		<version>${spring-version}</version>
	</dependency>
	...
<dependencies>
...
```

### Maven的继承

- 现状：非compile范围的依赖（例如junit）是不能传递的，因此依赖信息会散落在各个工程中，若版本号不一致，将会产生不必要的麻烦，修改也较为麻烦
- 解决：可以定义一个父工程（父工程只需要pom.xml，因此打包为pom即可），在父工程的pom.xml中定义junit的版本号，然后在所有子工程的pom.xml中声明父工程，同时不用再声明junit的版本号，会默认与父工程相同，修改时也只需要修改父工程中声明的版本号

### Maven的聚合

- 聚合可以实现一键安装，将聚合的所有工程一起install，而不用一个个去install，例如在工程Parent中配置聚合工程Hello，则install工程Parent时会自动install工程Hello

父工程Parent的pom.xml：
```
    <groupId>org.example</groupId>
    <artifactId>Parent</artifactId>
    <version>1.0-SNAPSHOT</version>
    <!-- 父工程的打包方式为pom -->
    <packaging>pom</packaging>

    <!-- 配置聚合 -->
    <modules>
        <!-- 配置每个要聚合的工程的相对路径 -->
        <module>../Hello</module>
        <module>../Introduce</module>
    </modules>

    <!-- 在父工程中统一配置依赖的管理 -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>4.12</version>
                <scope>test</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

子工程Hello的pom.xml：
```
  <!-- groupId相对父工程是重复的，可以删除 -->
<!--    <groupId>org.example</groupId>-->
  <artifactId>Hello</artifactId>
  <version>1.0-SNAPSHOT</version>

  <!-- 子工程中要声明父工程 -->
  <parent>
    <!-- 引用父工程的GAV坐标 -->
    <groupId>org.example</groupId>
    <artifactId>Parent</artifactId>
    <version>1.0-SNAPSHOT</version>

    <!-- 最好加上子工程pom.xml到父工程pom.xml的相对路径 -->
    <relativePath>../Parent/pom.xml</relativePath>
  </parent>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <!-- 删除junit的版本声明，让父工程来统一管理 -->
<!--      <version>4.11</version>-->
      <scope>test</scope>
    </dependency>
  </dependencies>
```

### Maven的生命周期

- 构建的过程中，各个环节执行的顺序不能打乱（如清理、编译、测试、报告...），这就是Maven的生命周期
- Maven的核心程序为了更好地实现自动化构建，不论执行生命周期的哪一阶段，都会从最初的清理工作开始逐步执行（如`mvn compile`命令，会先执行清理，再执行编译）

### 查找Maven依赖信息的网站

- [https://mvnrepository.com/](https://mvnrepository.com/)

---

#注意事项

- 如果用Maven创建Web工程，无法新建Servlet，要在pom.xml中添加Servlet的依赖，如：
```
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.1.0</version> <!-- 注意：Servlet3.0之后才可以使用注解！ -->
      <scope>provided</scope>
    </dependency>
```
JSP同理：
```
    <dependency>
      <groupId>javax.servlet.jsp</groupId>
      <artifactId>jsp-api</artifactId>
      <version>2.0</version>
      <scope>provided</scope>
    </dependency>
```

- 若不进行配置，则Maven默认使用Tomcat6，会有许多不支持，可以在pom.xml中修改，例如：
```
  <build>
       <plugins>    
          <!-- 配置Tomcat插件 ,用于启动项目 -->
        <plugin>
            <groupId>org.apache.tomcat.maven</groupId>
            <artifactId>tomcat7-maven-plugin</artifactId>
            <!--如果不设置，则默认为猫的自定义端口，项目路径为http://localhost:默认端口/项目名  -->
            <configuration>
            <!--如果端口号改为8088：那么访问的时候路径的端口就要写成8088  -->
                <port>8088</port>
                <!-- 如果设置为/，则项目了路径为http://localhost:端口号 /-->
                <!-- 如果设置为/lw，则项目了路径为http://localhost:端口号/lw -->
                <path>/</path>
            </configuration>
        </plugin>      
      </plugins>
  </build> 
```
使用此插件后，maven build 时的命令为：`tomcat7:run`，此时启动的tomcat版本为7.x.x而不是默认的6.x.x