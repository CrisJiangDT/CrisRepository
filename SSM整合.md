<link href="http://cdn.bootcss.com/highlight.js/8.0/styles/monokai_sublime.min.css" rel="stylesheet">  
<script src="http://cdn.bootcss.com/highlight.js/8.0/highlight.min.js"></script>  
<script>hljs.initHighlightingOnLoad();</script>

# SSM整合

- SSM：
![](.\MyPic\SSM.bmp)
- SSM整合：**用Spring来整合SpringMVC和MyBatis**，注意在各自测试成功后再进行整合

---

## 第一步：搭建环境

- 创建Maven工程，完善目录结构，在pom.xml中导入以下依赖：spring-web、spring-webmvc、spring-context、spring-jdbc、spring-tx、aspectjweaver、javax.annotation-api、mybatis、**mybatis-spring（用于整合Spring和MyBatis）**、log4j、mysql-connector-java、servlet-api、jsp-api、jstl、junit：
```
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.encoding>UTF-8</maven.compiler.encoding>
    <java.version>11</java.version>
    <maven.compiler.source>11</maven.compiler.source>
    <maven.compiler.target>11</maven.compiler.target>
    <spring.version>5.2.7.RELEASE</spring.version>
  </properties>

  <dependencies>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-web</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-tx</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>org.aspectj</groupId>
      <artifactId>aspectjweaver</artifactId>
      <version>1.9.5</version>
    </dependency>

    <dependency>
      <groupId>javax.annotation</groupId>
      <artifactId>javax.annotation-api</artifactId>
      <version>1.3.1</version>
    </dependency>

    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.1</version>
    </dependency>

    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis-spring</artifactId>
      <version>2.0.5</version>
    </dependency>

    <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>1.2.12</version>
    </dependency>

    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>8.0.20</version>
    </dependency>

    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>servlet-api</artifactId>
      <version>2.5</version>
    </dependency>

    <dependency>
      <groupId>javax.servlet.jsp</groupId>
      <artifactId>jsp-api</artifactId>
      <version>2.0</version>
    </dependency>

    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>jstl</artifactId>
      <version>1.2</version>
    </dependency>

    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
```
- 在resources目录下创建log4j.properties，用于输出日志：
```
log4j.rootLogger=DEBUG, Console
#Console
log4j.appender.Console=org.apache.log4j.ConsoleAppender
log4j.appender.Console.layout=org.apache.log4j.PatternLayout
log4j.appender.Console.layout.ConversionPattern=%d [%t] %-5p [%c] - %m%n
log4j.logger.java.sql.ResultSet=INFO
log4j.logger.org.apache=INFO
log4j.logger.java.sql.Connection=DEBUG
log4j.logger.java.sql.Statement=DEBUG
log4j.logger.java.sql.PreparedStatement=DEBUG
```
- 创建Account类，包含id、money、uid属性
- 创建三层架构（并加上Spring的注解将其配置到IoC核心容器中）：
**DAO层**：IAccountDao，**不用写实现类，因为MyBatis可以用配置的方式自动创建DAO实现类（将`@Repository`注解直接写在DAO接口上即可）**
```
@Repository("accountDao")
public interface IAccountDao {
    /**
     * 查询所有账户信息
     */
    List<Account> findAll();

    /**
     * 保存账户信息
     */
    void saveAccount(Account account);
}
```
Service层：IAccountService、AccountServiceImpl
```
public interface IAccountService {
    /**
     * 查询所有账户信息
     */
    List<Account> findAll();

    /**
     * 保存账户信息
     */
    void saveAccount(Account account);
}
```
```
@Service("accountService")
public class AccountServiceImpl implements IAccountService {
    @Override
    public List<Account> findAll() {
        System.out.println("service层执行查询所有账户信息的操作");
        return null;
    }

    @Override
    public void saveAccount(Account account) {
        System.out.println("service层执行保存账户信息的操作");
    }
}
```
Controller层：AccountController
```
@Controller("accountController")
public class AccountController {
}
```

## 第二步：编写Spring框架

- 在resouce目录下创建Spring配置文件applicationContext.xml：
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
   https://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <!-- Spring的配置文件 -->

    <!-- 配置要扫描注解的包，不扫描Controller（Controller已在SpringMVC配置文件springmvc.xml加载时扫描过） -->
    <context:component-scan base-package="pers.cris">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>
</beans>
```
- 在上面已经用注解将Service层实现类配置到IoC核心容器中，进行测试：
```
    /**
     * 测试Spring框架
     */
    @Test
    public void testSpring() {
        ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
        IAccountService accountService = ac.getBean("accountService", IAccountService.class);
        accountService.findAll();
    }
```
输出`accountService.saveAccount();`，说明Spring框架编写成功

## 第三步：编写SpringMVC框架

- 在web.xml中配置前端控制器和解决中文乱码的过滤器：
```
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >
<web-app>

  <display-name>Archetype Created Web Application</display-name>

  <!-- 配置解决中文乱码的过滤器 -->
  <filter>
    <filter-name>encodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
  </filter>
  <filter-mapping>
    <filter-name>encodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>

  <!-- 配置前端控制器 -->
  <servlet>
    <servlet-name>dispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!-- 指定SpringMVC配置文件位置 -->
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:springmvc.xml</param-value>
    </init-param>
    <!-- 配置开启服务器时立即加载前端控制器 -->
    <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
    <servlet-name>dispatcherServlet</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>
</web-app>
```
- 在resouce目录下创建SpringMVC的配置文件springmvc.xml：
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="
   http://www.springframework.org/schema/beans
   https://www.springframework.org/schema/beans/spring-beans.xsd
   http://www.springframework.org/schema/context
   https://www.springframework.org/schema/context/spring-context.xsd
   http://www.springframework.org/schema/mvc
   https://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!-- SpringMVC的配置文件 -->

    <!-- 配置要扫描注解的包，只扫描Controller -->
    <context:component-scan base-package="pers.cris.controller"/>

    <!-- 配置前端控制器不拦截的静态资源 -->
    <mvc:resources mapping="/js/**" location="/js/"/>
    <mvc:resources mapping="/css/**" location="/css/"/>
    <mvc:resources mapping="/images/**" location="/images/"/>

    <!-- 开启SpringMVC注解支持 -->
    <mvc:annotation-driven/>

    <!-- 视图解析器 -->
    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/pages/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
</beans>
```
- 给Controller的方法加上`@RequestMapping`注解来指定访问路径，并跳转到list.jsp页面：
```
@Controller("accountController")
@RequestMapping("/account")
public class AccountController {
    /**
     * 查询所有账户信息
     */
    @RequestMapping("/findAll")
    public String findAll() {
        System.out.println("controller层执行查询所有账户信息的操作");
        return "list";
    }

    /**
     * 查询所有账户信息
     */
    @RequestMapping("/saveAccount")
    public String saveAccount() {
        System.out.println("controller层执行保存账户信息的操作");
        return "list";
    }
}
```
- index.jsp：
```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
    <head>
        <title>SSM整合</title>
    </head>
    <body>
        <a href="account/findAll">查询所有账户信息</a>

        <hr/>

        <form action="account/saveAccount" method="post">
            用户ID：<input type="text" name="uid"> <br/>
            余额：<input type="text" name="money"> <br/>
            <input type="submit" value="保存账户信息">
        </form>
    </body>
</html>
```
- list.jsp：
```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
    <head>
        <title>显示所有账户信息的页面</title>
    </head>
    <body>
        <h3>以下是查询到的所有账户信息：</h3>
    </body>
</html>
```
- 测试：开启Tomcat服务器，点击index.jsp上的请求超链接，输出`controller层执行查询所有账户信息的操作`，并跳转到list.jsp，则说明SpringMVC框架编写成功

## 第四步：Spring整合SpringMVC

- **Spring整合SpringMVC的目的就是使得Controller层可以调用Service层的方法**
 - 目前，只在前端控制器中配置加载了SpringMVC的配置文件springmvc.xml，而**没有加载Spring的配置文件applicationContext.xml，因此Service还没有被创建并添加到IoC核心容器中**，Controller层无法依赖注入得到Service层实现类对象，
 - **解决方案：在web.xml中配置ServletContext的监听器，用于在服务器开启时，加载Spring的配置文件applicationContext.xml：**（若不配置，虽然Controller层仍能注入Service层实现类对象，但后续Spring整合MyBatis过程中，Service层需要注入MyBatis通过代理创建的DAO代理对象时，会出错）

```
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >
<web-app>
  <display-name>Archetype Created Web Application</display-name>

  <!-- 指定Spring配置文件applicationContext.xml的位置，监听器会对其进行加载 -->
  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext.xml</param-value>
  </context-param>

  <!-- 配置解决中文乱码的过滤器 -->
  <filter>
    <filter-name>encodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
  </filter>
  <filter-mapping>
    <filter-name>encodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>

  <!-- 配置监听器，用来加载Spring的配置文件applicationContext.mxl，默认只加载WEB-INF下的applicationContext.xml -->
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>

  <!-- 配置前端控制器 -->
  <servlet>
    <servlet-name>dispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!-- 指定SpringMVC配置文件位置 -->
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:springmvc.xml</param-value>
    </init-param>
    <!-- 配置开启服务器时立即加载前端控制器 -->
    <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
    <servlet-name>dispatcherServlet</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>

</web-app>
```
- 给Controller注入Service层实现类，并在Controller的方法中调用Service的方法：
```
@Controller("accountController")
@RequestMapping("/account")
public class AccountController {
    @Resource(name = "accountService")
    private IAccountService accountService;

    /**
     * 查询所有账户信息
     */
    @RequestMapping("/findAll")
    public String findAll() {
        System.out.println("controller层执行查询所有账户信息的操作");
        accountService.findAll();
        return "list";
    }

    /**
     * 查询所有账户信息
     */
    @RequestMapping("/saveAccount")
    public String saveAccount(Account account) {
        System.out.println("controller层执行保存账户信息的操作");
        accountService.saveAccount(account);
        return "list";
    }
}
```
- 测试：重新部署后，点击index.jsp上的超链接，输出为：
```
controller层执行查询所有账户信息的操作
service层执行查询所有账户信息的操作
```
并跳转到list.jsp页面，表示Spring框架整合SpringMVC框架成功

## 第五步：编写MyBatis框架

- 在resource目录下创建保存着JDBC四大参数的配置文件jdbcConfig.properties：
```
driver = com.mysql.jdbc.Driver
url = jdbc:mysql://localhost:3306/mydb?serverTimezone=Asia/Shanghai
username = Cris
password = 123
```
- 在resource目录下创建MyBatis的配置文件sqlMapConfig.xml：
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!-- MyBatis的配置文件 -->
<configuration>

    <!-- 从外部引入JDBC四大参数的配置信息 -->
    <properties resource="jdbcConfig.properties"/>

    <!-- 配置环境 -->
    <environments default="mysql">
        <environment id="mysql">
            <transactionManager type="jdbc"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>

    <!-- 配置映射信息 -->
    <mappers>
<!--        <mapper class="pers.cris.dao.IAccountDao"/>-->
        <package name="pers.cris.dao"/>
    </mappers>
</configuration>
```
- 在DAO的接口IAccountDao中给每个方法添加注解来指定要执行的SQL语句：
```
@Repository("accountDao")
public interface IAccountDao {
    /**
     * 查询所有账户信息
     */
    @Select("SELECT * FROM tb_account")
    List<Account> findAll();

    /**
     * 保存账户信息
     */
    @Insert("INSERT INTO tb_account(money, uid) VALUES(#{money}, #{uid})")
    void saveAccount(Account account);
}
```
- 测试MyBatis的findAll()方法：
```
    /**
     * 测试MyBatis框架
     */
    @Test
    public void testMyBatis() throws IOException {
        // 获取SqlSession对象
        InputStream in = Resources.getResourceAsStream("sqlMapConfig.xml");
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(in);
        SqlSession sqlSession = factory.openSession();

        // 通过SqlSession对象，获取DAO实现类对象，并执行findAll()方法
        IAccountDao accountDao = sqlSession.getMapper(IAccountDao.class);
        List<Account> accounts = accountDao.findAll();
        for (Account account : accounts)
            System.out.println(account);

        // 释放资源
        sqlSession.close();
        in.close();
    }
```
- 测试MyBatis的saveAccount()方法：
```
    /**
     * 测试MyBatis框架的保存账户操作
     */
    @Test
    public void testSaveAccount() throws IOException {
        // 获取SqlSession对象
        InputStream in = Resources.getResourceAsStream("sqlMapConfig.xml");
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(in);
        SqlSession sqlSession = factory.openSession();

        // 通过SqlSession对象，获取DAO实现类对象，并执行saveAccount()方法
        IAccountDao accountDao = sqlSession.getMapper(IAccountDao.class);
        accountDao.saveAccount(new Account(1000d, 7));

        // 释放资源
        sqlSession.commit();    // 注意：增删改时，sqlSession需要commit
        in.close();
    }
```

## 第六步：Spring整合MyBatis

- **Spring整合MyBatis的目的就是使得Service层可以调用DAO层的方法**
 - **关键就是要想办法把MyBatis通过代理生成的DAO代理对象配置到IoC核心容器中（其实就是要将MyBatis配置文件sqlMapConfig.xml中的内容转移到Spring配置文件applicationContext.xml中）**
- 在Spring配置文件applicationContext.xml中，添加如下内容来进行Spring和MyBatis的整合（需要导入mybatis-spring的依赖）：
```
    <!-- Spring整合MyBatis -->
    <!-- 1. 配置连接池（数据源），选用Spring提供的DriverManagerDataSource -->
    <bean id="ds" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver" />
        <property name="url" value="jdbc:mysql://localhost:3306/mydb?serverTimezone=Asia/Shanghai" />
        <property name="username" value="Cris" />
        <property name="password" value="123" />
    </bean>

    <!-- 2. 配置SqlSession的工厂 -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="ds"/>
    </bean>

    <!-- 3. 配置DAO接口所在的包 -->
    <bean id="mapperScanner" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="pers.cris.dao"/>
    </bean>
```
其实就是将MyBatis配置文件sqlMapConfig.xml中的内容搬到Spring配置文件applicationContext.xml中，然后就可以删除MyBatis配置文件sqlMapConfig.xml
- 给Service层注入DAO层实现类，并在Service层的方法中调用DAO的方法：
```
@Service("accountService")
public class AccountServiceImpl implements IAccountService {
    @Resource(name = "accountDao")
    private IAccountDao accountDao;

    @Override
    public List<Account> findAll() {
        System.out.println("service层执行查询所有账户信息的操作");
        return accountDao.findAll();
    }

    @Override
    public void saveAccount(Account account) {
        System.out.println("service层执行保存账户信息的操作");
        accountDao.saveAccount(account);
    }
}
```
- 在Controller的findAll()方法中，利用Model将调用Service查询到的所有账户信息存到request域中，然后在list.jsp中进行显示；在Controller的saveAccount()方法中，调用Service保存账户信息后，重定向访问findAll()方法，来查询所有账户信息并在list.jsp中进行显示：
```
@Controller("accountController")
@RequestMapping("/account")
public class AccountController {
    @Resource(name = "accountService")
    private IAccountService accountService;

    /**
     * 查询所有账户信息
     */
    @RequestMapping("/findAll")
    public String findAll(Model model) {
        System.out.println("controller层执行查询所有账户信息的操作");
        List<Account> accounts = accountService.findAll();
        // 通过Model将查询到的所有账户信息存到request域中，转发到list.jsp中进行显示
        model.addAttribute("accounts", accounts);
        return "list";
    }

    /**
     * 查询所有账户信息
     */
    @RequestMapping("/saveAccount")
    public void saveAccount(Account account, HttpServletRequest request, HttpServletResponse response) throws IOException {
        System.out.println("controller层执行保存账户信息的操作");
        accountService.saveAccount(account);
        // 保存账户信息后，重定向请求findAll()方法，findAll()方法会查询所有账户信息并转发到list.jsp中进行显示
        response.sendRedirect(request.getContextPath() + "/account/findAll");
    }
}
```
- list.jsp：
```
<%@ page contentType="text/html;charset=UTF-8" language="java" isELIgnored="false" %>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
    <head>
        <title>显示所有账户信息的页面</title>
    </head>
    <body>
        <h3>以下是查询到的所有账户信息</h3>
        <table border="1" cellspacing="0w">
            <tbody>
                <tr>
                    <th>账户ID</th><th>账户余额</th><th>户主ID</th>
                </tr>
                <c:forEach items="${accounts}" var="account">
                    <tr>
                        <td>${account.id}</td><td>${account.money}</td><td>${account.uid}</td>
                    </tr>
                </c:forEach>
            </tbody>
        </table>
    </body>
</html>
```
- 测试：点击index.jsp上查询所有账户信息的超链接，会在list.jsp中显示所有账户信息；在index.jsp填写保存账户信息的表单并提交，会将所填写的账户信息保存到数据库中，并在list.jsp中显示所有账户信息

## 第七步：Spring进行事务控制


- 在Spring配置文件applicationContext.xml中配置**事务管理器**（此时**名称必须是transactionManager**），并**开启Spring对注解声明式事务的支持**：
```
    <!-- 配置Spring的声明式事务支持 -->
    <!-- 1.配置事务管理器（此时名称必须是transactionManager）  -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="ds"/>
    </bean>

    <!-- 2. 开启Spring对注解声明式事务的支持 -->
    <tx:annotation-driven/>
```
- 给DAO添加findById()方法和updateAccount()方法：
```
@Repository("accountDao")
public interface IAccountDao {
    /**
     * 通过账户ID查询账户信息
     */
    @Select("SELECT * FROM tb_account WHERE id = #{id}")
    Account findById(Integer id);

    /**
     * 更新账户信息
     */
    @Update("UPDATE tb_account SET money = #{money}, uid = #{uid} WHERE id = #{id}")
    void updateAccount(Account account);
}
```
- 在Service添加transfer()方法，实现转账操作，并抛出异常，同时在实现类上**添加`@Transactional`注解**，来开启对其的事务：
```
@Service("accountService")
@Transactional
public class AccountServiceImpl implements IAccountService {
    @Resource(name = "accountDao")
    private IAccountDao accountDao;

    @Override
    public void transfer(Integer sourceId, Integer targetId, Double money) {
        System.out.println("service层执行转账的操作");
        Account sourceAccount = accountDao.findById(sourceId);
        Account targetAccount = accountDao.findById(targetId);
        sourceAccount.setMoney(sourceAccount.getMoney() - money);
        targetAccount.setMoney(targetAccount.getMoney() + money);
        accountDao.updateAccount(sourceAccount);
        int i = 1 / 0;  // 抛出异常
        accountDao.updateAccount(targetAccount);
    }
}
```
- **注意：Spring配置文件applicationContext.xml中注解不扫描Controller，SpringMVC配置文件springmvc.xml中注解只扫描Controller（若SpringMVC配置文件springmvc.xml中注解扫描了Service，此时Spring配置文件applicationContext.xml还未加载，还未给Service添加事务的增强，会将未增强的Service添加到IoC核心容器中，导致事务不起作用！！）**
 - SpringMVC配置文件springmvc.xml：
```
    <!-- 配置要扫描注解的包，只扫描Controller -->
    <context:component-scan base-package="pers.cris.controller"/>
```
 - Spring配置文件applicationContext.xml：
```
    <!-- 配置要扫描注解的包，不扫描Controller（Controller已在SpringMVC配置文件springmvc.xml加载时扫描过） -->
    <context:component-scan base-package="pers.cris">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>
```
- 在Controller添加transfer()方法：
```
@Controller("accountController")
@RequestMapping("/account")
public class AccountController {
    @Resource(name = "accountService")
    private IAccountService accountService;

    /**
     * 转账
     */
    @RequestMapping("/transfer")
    public void transfer(Integer sourceId, Integer targetId, Double money,
                         HttpServletRequest request, HttpServletResponse response) throws IOException {
        System.out.println("controller层执行转账的操作");
        accountService.transfer(sourceId, targetId, money);
        // 转账后，重定向请求findAll()方法，findAll()方法会查询所有账户信息并转发到list.jsp中进行显示
        response.sendRedirect(request.getContextPath() + "/account/findAll");
    }
}
```
- 给index.jsp添加转账的表单：
```
        <form action="account/transfer" method="post">
            转出账户ID：<input type="text" name="sourceId"> <br/>
            转入账户ID：<input type="text" name="targetId"> <br/>
            转账金额：<input type="text" name="money"> <br/>
            <input type="submit" value="开始转帐">
        </form>
```
- 在"/WEB-INF/pages"目录下创建错误页面error.jsp：
```
<%@ page contentType="text/html;charset=UTF-8" language="java" isELIgnored="false" %>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
    <head>
        <title>错误页面</title>
    </head>
    <body>
        ${errorMsg}
    </body>
</html>
```
- 自定义异常处理器MyExceptionResolver，实现HandlerExceptionResolver接口，将异常信息保存到request域中，并转发到error.jsp：
```
public class MyExceptionResolver implements HandlerExceptionResolver {
    @Override
    public ModelAndView resolveException(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) {
        ModelAndView mv = new ModelAndView();
        mv.addObject("errorMsg", "不好意思，出错啦~");
        mv.setViewName("error");
        return mv;
    }
}
```
- 在SpringMVC配置文件springmvc.xml中配置自定义的异常处理器MyExceptionResolver：
```
    <!-- 自定义的异常处理器 -->
    <bean id="myExceptionResolver" class="pers.cris.exception.MyExceptionResolver"/>
```
- 测试：
```
    /**
     * 测试Spring的事务控制
     */
    @Test
    public void testTransaction() {
        ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
        IAccountService accountService = ac.getBean("accountService", IAccountService.class);
        accountService.transfer(1, 2, 100d);
    }
```
查询账户信息发现数据正常，事务回滚成功
- 测试：提交index.jsp的转账表单，跳转到error.jsp，显示用户友好的异常信息，再次查询账户信息发现数据正常，事务回滚成功