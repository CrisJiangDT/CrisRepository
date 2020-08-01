<link href="http://cdn.bootcss.com/highlight.js/8.0/styles/monokai_sublime.min.css" rel="stylesheet">  
<script src="http://cdn.bootcss.com/highlight.js/8.0/highlight.min.js"></script>  
<script>hljs.initHighlightingOnLoad();</script>

# SpringMVC

- **SpringMVC**是一种基于Java的实现**MVC设计模型**的请求驱动类型的轻量级**Web框架**
- **MVC设计模型**：
 - **Model（模型）：用于封装数据，即JavaBean**
 - **View（视图）：用于展示数据，如JSP、HTML等**
 - **Controller（控制器）：用于处理逻辑，如Servlet**
- SSM框架：
![](.\MyPic\SSM.bmp)

###### SpringMVC与Struts2的比较

- SpringMVC与Struts2的共同点：
 - 都是表现层框架，都**基于MVC设计模型**
 - 底层都**离不开Servlet API**
 - 处理请求的机制都是一个**核心控制器**
- SpringMVC与Struts2的区别：
 - SpringMVC的入口是一个Servlet，而Struts2的入口是一个Filter
 - SpringMVC是**基于方法**设计的（较快），而Struts2是基于类设计的（每次执行都会创建一个动作类，因此较慢）
 - SpringMVC使用更加简洁，处理ajax的请求更加方便
 - Struts2的OGNL表达式使页面开发效率比SpringMVC高一些，但执行效率没有SpringMVC的JSTL高

---

#### SpringMVC入门案例

- 环境搭建步骤：
 1. 创建Maven工程，选择maven-archetype-webapp，将目录补全
 2. 在pom.xml中引入**spring-web、spring-webmvc、servlet-api、jsp-api、jstl**的依赖，并进行版本锁定：
```
  <!-- 版本锁定 -->
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
  </dependencies>
```
 3. 在resource目录下创建SpringMVC的**配置文件springmvc.xml**（注意要导入有mvc标签和context标签的名称空间和XML约束），**配置要扫描注解的包，配置视图解析器InternalResourceViewResolver，并用`<mvc:annotation-driven/>`标签开启SpringMVC注解支持**：
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
    <!-- 注意要导入有mvc标签和context标签的名称空间和XML约束 -->

    <!-- 配置要扫描注解的包 -->
    <context:component-scan base-package="pers.cris"/>

    <!-- 配置视图解析器InternalResourceViewResolver -->
    <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/pages/"/>   <!-- 前缀，即页面资源的目录 -->
        <property name="suffix" value=".jsp"/>  <!-- 后缀，即页面资源的后缀名 -->
    </bean>

    <!-- 开启SpringMVC注解支持 -->
    <mvc:annotation-driven/>
</beans>
```
 4. **在web.xml中配置前端控制器DispatcherServlet**，并通过**初始化参数contextConfigLocation指定**SpringMVC的配置文件**springmvc.xml**的位置，同时**`<load-on-startup>1</load-on-startup>`指定一开启服务器就加载**该Servlet（**前端控制器**）：
```
  <!-- 配置前端控制器DispatcherServlet -->
  <servlet>
    <servlet-name>dispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!-- 通过初始化参数指定SpringMVC的配置文件springmvc.xml -->
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:springmvc.xml</param-value>
    </init-param>
    <!-- 配置一开启服务器就加载该Servlet（前端控制器） -->
    <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
    <servlet-name>dispatcherServlet</servlet-name>
    <url-pattern>/</url-pattern>  <!-- 配置所有请求都会经过该前端控制器 -->
  </servlet-mapping>
```
 5. 在index.jsp中编写一个超链接（相对路径写法，不能加"/"），用来调用入门程序：
```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
    <head>
        <title>SpringMVC入门案例</title>
    </head>
    <body>
        <h3>SpringMVC入门</h3>
        <a href="hello">点击执行SpringMVC入门程序</a>   <!-- href中是相对路径的写法，不能加"/" -->
    </body>
</html>
```
 6. 在`WEB-INF > pages`目录下编写一个成功页面success.jsp，用于入门程序执行完后的跳转：
```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
    <head>
        <title>SpringMVC入门成功</title>
    </head>
    <body>
        <h3>恭喜，SpringMVC入门成功~</h3>
    </body>
</html>
```
 7. 编写一个**控制器类**HelloController（入门程序），并**添加到IoC容器中**；控制器类里编写一个**方法，返回值为要跳转页面的名称，并加上`@RequestMapping`注解，`path`属性（和`value`属性等价）指定该方法的请求路径**（index.jsp的超链接中的路径）
```
@Controller("helloController")
public class HelloController {
    @RequestMapping(path = "hello")    // 指定该响应方法的请求路径
    public String sayHello() {
        System.out.println("Hello SpringMVC~");
        return "success";   // 返回值就是要跳转页面的名称，会到视图解析器中解析
    }
}
```
 8. 配置并启动Tomcat服务器

- 注意：在**web.xml中配置了Servlet后**，开启服务器，在JSP页面**点击浏览器图标，会打开页面源码**而不是页面，原因不明，需要让IDEA自动弹出页面，或在浏览器地址栏输入URL进行访问

###### SpringMVC入门案例的分析

**服务器开启时：**
1. **前端控制器DispatcherServlet会立即被创建**（配置了`<load-on-startup>1</load-on-startup>`），同时通过初始化参数可以**加载配置文件springmvc.xml**
2. 在**配置文件springmvc.xml中：**
 1. **创建IoC核心容器**，并指定要扫描注解的包
 2. **扫描注解**时，会**创建控制器HelloController对象，添加到IoC核心容器中**，并**给**控制器HelloController的**sayHello()方法指定访问路径**）
 3. **创建视图解析器InternalResourceViewResolver对象，添加到IoC核心容器中**（指定了页面资源的前后缀）
 4. **开启SpringMVC注解支持**（sayHello()方法的**`@RequestMapping`注解生效**）

**请求发送时:**
1. 点击index.jsp的超链接，**请求会到达前端控制器DispatcherServlet（任何请求都会经过前端控制器）**
2. **前端控制器**根据路径`hello`**找到HelloController的sayHello()方法，执行之**
3. HelloController的sayHello()方法的**返回“success”给前端控制器**
4. **前端控制器去找视图解析器InternalResourceViewResolver**
5. **视图解析器**给“success”添加前后缀，**找到success.jsp页面**
5. **success.jsp将页面信息返回给前端控制器，响应到客户端**


#### SpringMVC详细分析

- **SpringMVC三大组件：处理器映射器（HandlerMapping）、处理器适配器（HandlerAdapter）、视图解析器（View Resolver）**
- **SpringMVC**框架**基于组件**方式来执行流程：
 - **前端控制器（DispatcherServlet）**:整个流程的**“指挥中心”**
 - **处理器映射器（HandlerMapping）**：负责**根据用户的请求找到处理器Handler**
 - **处理器（Handler）**：业务具体的**控制器**，需要自己编写
 - **处理器适配器（HandlerAdapter）**：通过处理器适配器HandlerAdapter对处理器Handler进行执行，可以**处理多种类型的处理器**
 - **视图解析器（View Resolver）**：负责**将处理结果生成视图View**，然后通过页面展示给用户
 - **`<mvc:annotation-driven/>`标签会自动加载处理器映射器（HandlerMapping）和处理器适配器（HandlerAdapter）**

###### 入门案例更详细的流程图
![](.\MyPic\springmvc_flowchart.bmp)

---

###### `@RequestMapping`注解

- **`@RequestMapping`注解的作用：用于建立请求URL和处理请求的方法之间的对应关系**
- `@RequestMapping`注解既可以写在类上，也可以写在方法上
 - 当类上有`@RequestMapping`注解时，访问类中方法的请求URL需要以类上的注解为父级路径，以方法上的注解为子级路径，例如：
```
@Controller("helloController")
@RequestMapping(path = "helloController")
public class HelloController {
    @RequestMapping(path = "testRequestMapping")
    public String testRequestMapping() {
        System.out.println("测试@RequestMapping注解~");
        return "success";
    }
}
```
则调用该方法的超链接为：`<a href="helloController/testRequestMapping">测试@RequestMapping注解</a>`
- `@RequestMapping`注解的属性：
 - `value`属性和`path`属性等价，用于指定该类或方法的请求URL
 - `method`属性用于指定能接受的请求方式，如`@RequestMapping(path = "hello", method = {RequestMethod.GET, RequestMethod.POST})`表示能接受GET请求和POST请求
 - `params`属性用于指定请求时需要带的参数和参数值，若有指定则请求时必须一致，如`@RequestMapping(path = "hello", params = {"username"}`表示请求时必须带有名为“username”的参数，`@RequestMapping(path = "hello", params = {"username=zhangSan"})`表示请求时必须带有名为“username”且值为“zhangSan”的参数
 - `headers`属性用于指定请求时必须包含的请求头，如`@RequestMapping(path = "hello", headers = {"Accept"})`表示请求时必须有“Accept”请求头

---

#### 请求参数绑定

###### 参数类型为基本数据类型

- **请求中的参数名和响应方法中的参数名相同**时，会自动绑定，例如响应方法中有username和password参数：
```
@Controller("paramsController")
@RequestMapping(path = "params")
public class ParamsController {
    /**
     * 测试请求参数绑定，参数类型为基本数据类型
     */
    @RequestMapping(path = "testParamsBinding1")
    public String testParamsBinding1(String username, String password) {
        System.out.println("testParamsBinding1()方法执行了~");
        System.out.println("username = " + username);
        System.out.println("password = " + password);
        return "success";
    }
}
```
超链接请求的URL为：`<a href="params/testParamsBinding1?username=zhangSan&password=123">测试请求参数绑定</a>`，则输出为：
```
testParamsBinding1()方法执行了~
username = zhangSan
password = 123
```
即将请求中的参数值赋给了响应方法中的同名参数

###### 参数类型为JavaBean类型

- 使用表单来进行封装
 - 在响应方法的参数列表中指定要封装的JavaBean类型
 - **表单`<input>`标签的`name`属性指定**要用set方法来进行赋值的**属性名**
 - 当JavaBean中有其他JavaBean属性时，表单`<input>`标签的`name`属性可以用`.`来进行多层的set操作

 例如：
User类有uid、username、age属性，且提供了set方法
Account类有id、money、user属性，且提供了set方法

 Controller控制器：
```
@Controller("paramsController")
@RequestMapping(path = "params")
public class ParamsController {
    /**
     * 测试请求参数绑定，参数类型为JavaBean类型
     */
    @RequestMapping(path = "testParamsBinding2")
    public String testParamsBinding2(Account account) {
        System.out.println("testParamsBinding2()方法执行了~");
        System.out.println("account = " + account);
        return "success";
    }
}
```

 请求表单：
```
		<%-- 请求参数类型为JavaBean --%>
        <form action="params/testParamsBinding2" method="post">
            <!-- name属性值为JavaBean中的属性名（在响应方法的参数列表中指定了JavaBean类型），会调用set方法进行封装 -->
            账户ID：<input type="text" name="id"> <br/>
            账户金额：<input type="text" name="money"> <br/>
            <!-- 对于JavaBean中的引用数据类型的封装，用"."来连续调用set方法 -->
            用户ID：<input type="text" name="user.uid"> <br/>
            用户名：<input type="text" name="user.username"> <br/>
            用户年龄：<input type="text" name="user.age"> <br/>
            <input type="submit" value="提交">
        </form>
```
 
 结果：参数封装和绑定成功，输出为
```
testParamsBinding2()方法执行了~
account = Account{id=1, money=1000.6, user=User{uid=1, username='zhangSan', age=18}}
```

###### 参数类型为JavaBean中有集合数据类型

 例如：
User类有id、username、age属性，且提供了set方法
Group类有users（类型为`List<User>`）、userMap（类型为`Map<String, User>`）属性，且提供了set方法

 Controller控制器：
```
@Controller("paramsController")
@RequestMapping(path = "params")
public class ParamsController {
    /**
     * 测试请求参数绑定，参数类型为JavaBean中有集合数据类型
     */
    @RequestMapping(path = "testParamsBinding3")
    public String testParamsBinding3(Group group) {
        System.out.println("testParamsBinding3()方法执行了~");
        System.out.println("group = " + group);
        return "success";
    }
}
```

 请求表单：
```
		<%-- 请求参数类型为JavaBean中有集合类型属性 --%>
        <form action="params/testParamsBinding3" method="post">
            <!-- 将数据封装成User，添加到Group的users中（类型为List<User>） -->
            用户ID：<input type="text" name="users[0].uid"> <br/>
            用户名：<input type="text" name="users[0].username"> <br/>
            用户年龄：<input type="text" name="users[0].age"> <br/>
            <!-- 将数据封装成User，添加到Group的userMap中（类型为Map<String, User>） -->
            用户ID：<input type="text" name="userMap['xxx'].uid"> <br/>
            用户名：<input type="text" name="userMap['xxx'].username"> <br/>
            用户年龄：<input type="text" name="userMap['xxx'].age"> <br/>
            <input type="submit" value="提交">
        </form>
```

 结果：参数封装和绑定成功，输出为
```
testParamsBinding3()方法执行了~
group = Group{users=[User{uid=1, username='张三', age=19}], userMap={xxx=User{uid=2, username='李四', age=18}}}
```

---

###### 配置解决中文乱码的过滤器CharacterEncodingFilter

- 在上面的表单中，若提交中文参数，会乱码，可以在web.xml中配置一个处理乱码问题的过滤器CharacterEncodingFilter来解决中文乱码问题

在web.xml中添加：
```
  <!-- 配置解决中文乱码的过滤器CharacterEncodingFilter -->
  <filter>
    <filter-name>characterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <!-- 用初始化参数配置编码为UTF-8 -->
    <init-param>
      <param-name>encoding</param-name>
      <param-value>UTF-8</param-value>
    </init-param>
  </filter>
  <filter-mapping>
    <filter-name>characterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern> <!-- 所有请求都拦截 -->
  </filter-mapping>
```

---

###### 自定义类型转换器

- **发送请求时的参数都是字符串**
 - 在允许的情况下，SpringMVC会自动将其转换为需要的类型，例如上面int类型的age也可以被封装，这是因为SpringMVC已经提供了许多**类型转换器，用于将字符串形式的请求参数转换成需要的类型**
 - 但是有时候却无法自动转换，例如请求参数为Date类型时，只有输入`yyyy/MM/dd`形式才能被自动转换成Date类型，而如果输入`yyyy-MM-dd`形式时，会在类型转换时就报错，这就需要我们**自定义类型转换器，实现Converter接口**，并进行类型的转换，然后在配置文件**springmvc.xml中对其进行配置**
- 步骤：
 1. **自定义类型转换器String2DateConverter，实现Convert接口**，将字符串转换成Date：
```
public class String2DateConverter implements Converter<String, Date> {
    @Override
    public Date convert(String s) {
        try {
            DateFormat df = new SimpleDateFormat("yyyy-MM-dd");
            return df.parse(s);
        } catch (Exception e) {
            throw new RuntimeException("转换错误~");
        }
    }
}
```
 2. 在配置文件**springmvc.xml中配置类型转换服务conversionService**，并**将自定义类型转换器String2DateConverter注册进去**：
```
    <!-- 配置conversionService，并注册自定义类型转换器 -->
    <bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
        <property name="converters">
            <set>
                <bean class="pers.cris.utils.String2DateConverter"/>    <!-- 注册自定义类型转换器String2DateConverter -->
            </set>
        </property>
    </bean>
```
 3. 在配置文件**springmvc.xml中`<mvc:annotation-driven/>`标签的`conversion-service`属性指定类型转换服务，使得上面配置的类型转换服务生效**：
```
<!-- 开启SpringMVC注解支持，并让自定义类型转换器生效 -->
    <mvc:annotation-driven conversion-service="conversionService"/>
```
 4. Controller控制器：
```
@Controller("paramsController")
@RequestMapping(path = "params")
public class ParamsController {
    /**
     * 自定义类型转换器
     */
    @RequestMapping(path = "testParamsBinding4")
    public String testParamsBinding4(Person person) {
        System.out.println("testParamsBinding4()方法执行了~");
        System.out.println("person = " + person);
        return "success";
    }
}
```
 5. 表单：
```
        <%-- 测试自定义类型转换器 --%>
        <form action="params/testParamsBinding4" method="post">
            姓名：<input type="text" name="name"> <br/>
            生日：<input type="text" name="birthday"> <br/>
            <input type="submit" value="提交">
        </form>
```
 6. 结果：生日输入`yyyy-MM-dd`形式时，成功封装，不会报错

---

###### 在响应方法中获取Servlet API

- 在响应方法中，若想获取Servlet API（如request、response、session、servletContext等对象），只需要在参数列表中写上request和response即可

 例如：
 Controller控制类：
```
@Controller("paramsController")
@RequestMapping(path = "params")
public class ParamsController {
    /**
     * 获取Servlet API，如request、response、session、servletContext等
     */
    @RequestMapping(path = "testServletAPI")
    public String testServletAPI(HttpServletRequest request, HttpServletResponse response) {
        System.out.println("testServletAPI()方法执行了~");

        System.out.println("request = " + request);
        System.out.println("response = " + response);

        HttpSession session = request.getSession();
        System.out.println("session = " + session);

        ServletContext servletContext = session.getServletContext();
        System.out.println("servletContext = " + servletContext);

        return "success";
    }
}
```
 请求该响应方法的超链接：`<a href="params/testServletAPI">测试获取Servlet API</a>`
 结果：成功拿到request、response、session、servletContext

---

#### SpringMVC的常用注解

###### `@RequestParam`注解

- 之前，当请求参数名和响应方法的形参名不一致时，无法赋值，而**`@RequestParam`注解可以用于给方法形参指定请求参数名**，实现参数名称不同时的赋值，例如：
```
@Controller("annotationController")
@RequestMapping(path = "/annotation")
public class AnnotationController {
    /**
     * 测试 @RequestParam 注解
     */
    @RequestMapping(path = "/testRequestParam")
    public String testRequestParam(@RequestParam(name = "name") String username) {
        System.out.println("testRequestParam()方法执行了~");
        System.out.println("username = " + username);
        return "success";
    }
}
```
则`<a href="annotation/testRequestParam?name=张三">测试@RequestParam注解</a>`，请求参数`name=张三`会被赋给方法形参`username`
需要注意的是，**给形参添加`@RequestParam`注解后，对该方法的请求中就必须包含一个该参数**（例如上面的超链接中若没有名为“name”的参数，就会报错），**否则会报错，除非将该注解的`required`属性置为false（默认为true）**

###### `@RequestBody`注解

- **`@RequestBody`注解用于获取请求体的内容**（形式为"key1=value1&key2=value2..."），它**对GET请求不适用（GET请求没有请求体）**

 例如：
 Controller：
```
@Controller("annotationController")
@RequestMapping(path = "/annotation")
public class AnnotationController {
    /**
     * 测试 @RequestBody 注解
     */
    @RequestMapping(path = "/testRequestBody")
    public String testRequestBody(@RequestBody String body) {
        System.out.println("testRequestBody()方法执行了~");
        System.out.println("body = " + body);
        return "success";
    }
}
```

 请求表单（**必须要POST请求**）：
```
        <%-- 测试@RequestBody注解 --%>
        <form action="annotation/testRequestBody" method="post">
            用户名：<input type="text" name="username"> <br/>
            密码：<input type="password" name="password"> <br/>
            <input type="submit" value="提交">
        </form>
```

 结果：获取到POST请求的请求体，输出为：
```
testRequestBody()方法执行了~
body = username=jdt&password=123123
```

###### `@PathVariable`注解

- **`@PathVariable`注解用于绑定URL中的占位符**，例如URL`/testPathVariable/{sid}`中的`{sid}`就是占位符

 例如：
 Controller：
```
@Controller("annotationController")
@RequestMapping(path = "/annotation")
public class AnnotationController {
    /**
     * 测试 @PathVariable 注解
     */
    @RequestMapping(path = "/testPathVariable/{sid}")
    public String testPathVariable(@PathVariable("sid") String id) {
        System.out.println("testPathVariable()方法执行了~");
        System.out.println("id = " + id);
        return "success";
    }
}
```
 请求超链接：`<a href="annotation/testPathVariable/7">测试@PathVariable注解</a>`
 输出为：
```
testPathVariable()方法执行了~
id = 7
```
也就是说，**在形参上添加`@PathVariable`注解后，会将URL中指定占位符的值赋给形参**

###### `@RequestHeader`注解

- **`@RequestHeader`注解用于获取请求头的值**

 例如：
 Controller：
```
@Controller("annotationController")
@RequestMapping(path = "/annotation")
public class AnnotationController {
    /**
     * 测试 @RequestHeader 注解
     */
    @RequestMapping(path = "/testRequestHeader")
    public String testRequestHeader(@RequestHeader("Accept") String header) {
        System.out.println("testRequestHeader()方法执行了~");
        System.out.println("header = " + header);
        return "success";
    }
}
```
 请求超链接为：`<a href="annotation/testRequestHeader">测试@RequestHeader注解</a>`
 结果：响应方法中的形参header成功获取到“Accept”请求头的值，输出为：
```
testRequestHeader()方法执行了~
header = text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
```

###### `@CookieValue`注解

- **`@CookieValue`注解用于获取指定Cookie的值**

 例如：
 Controller：
```
@Controller("annotationController")
@RequestMapping(path = "/annotation")
public class AnnotationController {
    /**
     * 测试 @CookieValue 注解
     */
    @RequestMapping(path = "/testCookieValue")
    public String testCookieValue(@CookieValue("JSESSIONID") String cookieVal) {
        System.out.println("testCookieValue()方法执行了~");
        System.out.println("cookieVal = " + cookieVal);
        return "success";
    }
}
```
 请求超链接为：`<a href="annotation/testCookieValue">测试@CookieValue注解</a>`
 结果：响应方法中的形参cookieVal成功获取到名为“JSESSIONID”的Cookie的值，输出为：
```
testCookieValue()方法执行了~
cookieVal = 8ECD8CB6B47178AD319F1A3C6E0F0343
```

###### `@ModelAttribute`注解

- `@ModelAttribute`注解用在方法上时，表示当前方法会在Controller的方法执行之前先执行，它可以修饰没有返回值的方法，也可以修饰有返回值的方法（两种写法）
- `@ModelAttribute`注解用在参数上时，获取指定的数据给参数赋值
- **`@ModelAttribute`注解的应用场景：当表单提交的数据不完整时，可以保证将缺失的数据用数据库中原有的数据来填充**

1. **`@ModelAttribute`注解修饰有返回值的方法**
 提交表单中只提供了User的uid和username属性，而没有提供age属性，用`@ModelAttribute`，先拿到uid，并从数据库中获取该User的所有信息，封装到user中，并将user返回，然后，Controller的方法才执行，会用表单提供的数据对user的某些属性进行覆盖

 Controller：
```
@Controller("annotationController")
@RequestMapping(path = "/annotation")
public class AnnotationController {
    /**
     * 测试 @ModelAttribute 注解 之 修饰有返回值的方法
     * 提交表单中只提供了User的uid和username属性，而没有提供age属性
     */
    @RequestMapping(path = "/testModelAttribute1")
    public String testModelAttribute1(User user) {
        System.out.println("testModelAttribute1()方法执行了~");
        System.out.println("user = " + user);
        return "success";
    }

    /**
     * 测试 @ModelAttribute 注解 之 修饰有返回值的方法
     * 通过参数中的uid，查询数据库（模拟），查询到数据库中原有的信息
     * 然后再执行Controller的方法，表单提供的内容将会覆盖从数据库中查询到的数据，而表单未提供的内容仍然是从数据库中查询到的数据
     */
    @ModelAttribute
    public User fillUser1(int uid) {
        System.out.println("fillUser1()方法执行了~");

        // 通过参数中的uid，查询数据库（模拟），查询到数据库中原有的信息
        User user = new User();
        user.setUid(uid);
        user.setUsername("zhangSan");
        user.setAge(18);

        // 将数据库中查询到的User对象返回
        return user;
    }
}
```
 提交表单：
```
		<%-- 测试@ModelAttribute注解 之 修饰有返回值的方法 --%>
        <form action="annotation/testModelAttribute1" method="post">
            用户ID：<input type="text" name="uid"> <br/>
            用户名：<input type="text" name="username"> <br/>
            <input type="submit" value="提交">
        </form>
```
 结果：虽然提交表单中没有提供age属性，但通过fillUser1()方法，模拟从数据库中通过uid取出原有数据进行填充，并返回User对象，因此Controller中得到的User是有age属性值的，更新提供了的属性值，保留未提供的属性值，输出为：
```
fillUser1()方法执行了~
testModelAttribute1()方法执行了~
user = User{uid=1, username='jdt', age=18}
```

2. **`@ModelAttribute`属性修饰没有返回值的方法**
 提交表单中只提供了User的uid和username属性，而没有提供age属性，用`@ModelAttribute`，先拿到uid，并从数据库中获取该User的所有信息，封装到user中，并将user存到Map中，然后，Controller的方法才执行，从Map中获取user，然后会用表单提供的数据对user的某些属性进行覆盖
 Controller：
```
@Controller("annotationController")
@RequestMapping(path = "/annotation")
public class AnnotationController {
    /**
     * 测试 @ModelAttribute 注解 之 修饰没有返回值的方法
     * 通过@ModelAttribute注解，从map中将填充了数据库原有数据的user取出
     */
    @RequestMapping(path = "/testModelAttribute2")
    public String testModelAttribute2(@ModelAttribute("aaa") User user) {
        System.out.println("testModelAttribute2()方法执行了~");
        System.out.println("user = " + user);
        return "success";
    }

    /**
     * 测试 @ModelAttribute 注解 之 修饰没有返回值的方法
     * 通过参数中的uid，查询数据库（模拟），查询到数据库中原有的信息，存到map中
     * 然后再执行Controller的方法，从map中取出user，再用表单提供的数据对user进行更新
     */
    @ModelAttribute
    public void fillUser2(int uid, Map<String, User> map) {
        System.out.println("fillUser2()方法执行了~");

        // 通过参数中的uid，查询数据库（模拟），查询到数据库中原有的信息
        User user = new User();
        user.setUid(uid);
        user.setUsername("zhangSan");
        user.setAge(18);

        // 将数据库中查询到的User对象存放到map中
        map.put("aaa", user);
    }
}
```
 请求表单：
```
    <%-- 测试@ModelAttribute注解 之 修饰没有返回值的方法 --%>
    <form action="annotation/testModelAttribute2" method="post">
        用户ID：<input type="text" name="uid"> <br/>
        用户名：<input type="text" name="username"> <br/>
        <input type="submit" value="提交">
    </form>
```
 结果：虽然提交表单中没有提供age属性，但通过fillUser2()方法，模拟从数据库中通过uid取出原有数据进行填充，并存到map中，因此Controller从map中得到的User是有age属性值的，更新提供了的属性值，保留未提供的属性值，输出为：
```
fillUser2()方法执行了~
testModelAttribute2()方法执行了~
user = User{uid=1, username='jdt', age=18}
```

###### `@SessionAttributes`注解

- **`@SessionAttributes`注解用于多次执行Controller控制器方法间的参数共享**
- **`@SessionAttributes`注解只能写在类上，用于将request域中的某个键值对保存到session域中**
- **可以用Model来进行request域的存取**（若直接用request存，耦合高，没有Servlet API的情况下编译器就报错）
- **可以用SessionStatus来进行session域的删除**

 例如：
 Controller：
```
@Controller("annotationController")
@RequestMapping(path = "/annotation")
@SessionAttributes(value = {"msg"}) // 将request域中"msg"所对应的键值对存到session域中
public class AnnotationController {

    /**
     * 测试 @SessionAttributes 注解
     * 用Model将键值对存到request域中，再通过Controller类上的@SessionAttributes注解将request域中的键值对存到session域中
     */
    @RequestMapping(path = "/testSessionAttributes")
    public String testSessionAttributes(Model model) {
        System.out.println("testSessionAttributes()方法执行了~");

        // 用Model来将键值对存到request域中（若直接用request存，耦合高，没有Servlet API的情况下编译器就报错）
        model.addAttribute("msg", "哈哈");

        return "success";
    }

    /**
     * 从session域中取值
     */
    @RequestMapping(path = "getSessionAttributes")
    public String getSessionAttributes(Model model) {
        System.out.println("getSessionAttributes()方法执行了~");

        // 通过Model从session域中取值（由于是新的请求，request域中不可能有msg，所以只能是从session域中取的）
        String msg = (String) model.getAttribute("msg");
        System.out.println("msg = " + msg);

        return "success";
    }

    /**
     * 从session域中删除值
     */
    @RequestMapping(path = "delSessionAttributes")
    public String delSessionAttributes(SessionStatus status) {
        System.out.println("delSessionAttributes()方法执行了~");

        // 通过SessionStatus从session域中删除
        status.setComplete();

        return "success";
    }
}
```
请求超链接：
```
    <a href="annotation/testSessionAttributes">测试@SessionAtttributes注解</a>
    <a href="annotation/getSessionAttributes">从Session域中取出键值对</a>
    <a href="annotation/delSessionAttributes">从Session域中删除键值对</a>
```
success.jsp中输出request域和session域中的信息（注意要设置`isELIgnored="false"`）：
```
<%@ page contentType="text/html;charset=UTF-8" language="java" isELIgnored="false" %>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
    <head>
        <title>SpringMVC入门成功</title>
    </head>
    <body>
        <h3>恭喜，SpringMVC入门成功~</h3>

        ${requestScope.msg}
        ${sessionScope}
    </body>
</html>
```
 结果：点击第一个超链接往session域中存键值对后，点击第二个超链接可以从session域中取出值输出到控制台。点击第三个超链接从session域中删除键值对后，点击第二个超链接从session域中获取不到值

---

## SpringMVC 之 响应

#### 响应 之 返回值是String

- 和上面一样，在配置文件springmvc.xml中配置视图解析器后，响应方法直接返回页面的名称（如"success"），则视图解析器会找到相应目录下的页面进行响应

#### 响应 之 返回值是void

- 当返回值是void时，可以：
 - 什么都不做，默认是会按照视图解析器中配置的目录+当前访问路径进行查找jsp（例如视图解析器配置的目录是"/WEB-INF/pages/"，控制器及其方法的请求路径是"user/testVoid"，则会找"/WEB-INF/pages/user/testVoid.jsp"，若找不到则报404）
 - 拿到request对象进行转发
 - 拿到response对象进行重定向
 - 拿到response对象并直接对页面进行响应

 例如：
 Controller：
```
@Controller("userController")
@RequestMapping("/user")
public class UserController {
    /**
     * 响应 之 返回值为void类型
     */
    @RequestMapping("/testVoid")
    public void testVoid(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("testVoid()方法执行了~");

        /* 1. 什么都不做时，默认会转发到"/WEB-INF/pages/user/testVoid.jsp"
        return;
         */

        /* 2. 拿到request对象进行转发，不会经过视图解析器，因此要写页面的完整路径
        request.getRequestDispatcher("/WEB-INF/pages/success.jsp").forward(request, response);
         */

        /* 3. 拿到response对象进行重定向（重定向是新的请求，需要加上项目名，且无法访问到WEB-INF目录，只能访问到webapp目录）
        response.sendRedirect(request.getContextPath() + "/redirect.jsp");
         */

        /* 4. 拿到response对象，直接进行响应 */
        response.setCharacterEncoding("UTF-8");
        response.setContentType("text/html;character=UTF-8");
        response.getWriter().println("你好~");
    }
}
```
 请求超链接：`<a href="user/testVoid">测试：响应为void类型</a> <br/>`
 结果：什么都不做时，会自动转发到"/WEB-INF/pages/user/testVoid.jsp"（由视图解析器配置的页面目录和响应方法的请求路径共同决定，若没有该页面则报404）；**转发时，不需要写项目路径，但不会经过视图解析器，因此需要写页面的完整路径**，成功转发到"/WEB-INF/pages/success.jsp"；**重定向时，是新的请求，因此需要加上项目名，且无法访问WEB-INF目录**，成功重定向到webapp目录下的redirect.jsp页面；直接响应时，要注意处理中文的编码问题

#### 响应 之 返回值是ModelAndView（常用）

- **ModelAndView**是SpringMVC提供的，**可以同时存Model（JavaBean，会用Model将其存到request域中）和View（要跳转的页面，会经过视图解析器）**，上面返回值是String类型时，底层也会使用ModelAndView对象，因此它们之间只是写法不同

 例如：
 Controller：
```
@Controller("userController")
@RequestMapping("/user")
public class UserController {
    /**
     * 响应 之 返回值为ModelAndView类型
     */
    @RequestMapping("/testModelAndView")
    public ModelAndView testModelAndView() {
        System.out.println("testModelAndView()方法执行了~");

        // 模拟从数据库中取出User对象
        User user = new User();
        user.setUsername("zhangSan");
        user.setPassword("123");
        user.setAge(18);

        // 创建ModelAndView对象，将User存到request域中，并指定要跳转的页面
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.addObject("user", user);
        modelAndView.setViewName("success");    // 会经过视图解析器，只写页面名即可

        return modelAndView;
    }
}
```
 请求超链接：`<a href="user/testModelAndView">测试：响应为ModelAndView类型</a>`
 结果：成功将user存到request域中，并转发都success.jsp页面

#### 响应 之 使用关键字进行转发或重定向

- 可以通过String类型的返回值，加上`forward`或`redirect`关键字来进行转发或重定向

 例如：
 Controller：
```
@Controller("userController")
@RequestMapping("/user")
public class UserController {
    /**
     * 响应 之 使用关键字进行转发或重定向
     */
    @RequestMapping("/testForwardAndRedirect")
    public String testForwardAndRedirect() {
        System.out.println("testForwardAndRedirect()方法执行了~");

        /* 1. 用forward关键字进行转发
        return "forward:/WEB-INF/pages/success.jsp";
        */

        /* 2. 用redirect关键字进行重定向（此时不用加项目名，框架会为我们加上项目名） */
        return "redirect:/redirect.jsp";
    }
}
```
 请求超链接：`<a href="user/testForwardAndRedirect">测试：使用关键字进行转发或重定向</a>`
 结果：成功转发，成功重定向

---

## 接收和响应JSON数据

#### 要先解决的静态资源过滤问题

先试图在页面上添加一个按钮，点击后弹出通知框：
 1. 要用到jquery，在webapp目录下创建静态资源的文件夹：css、js（并导入jquery.js）、images文件夹
 2. 在webapp目录下的response.jsp页面中导入jquery.js，并给按钮添加点击事件：
```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
    <head>
        <title>No Title</title>

        <script src="js/jquery-3.5.1.min.js"></script>

        <script type="text/javascript">
            // 页面加载时，给btn绑定点击事件
            $(function() {
                $("#btn").click(function() {
                    alert("Hello~")
                })
            })
        </script>
    </head>
    <body>
        <button id="btn">发送AJAX请求</button>
    </body>
</html>
```
 3. 开启服务器，点击按钮，发现无反应

这是由于之前配置了前端控制器DispatcherServlet，会拦截所有请求，而导入静态资源jquery.js时也被拦截了，因此需要**配置前端控制器不需要拦截的静态资源**，在**配置文件springmvc.xml中用`<mvc:resources>`标签进行配置**：
```
    <!-- 配置前端控制器不拦截的静态资源 -->
    <mvc:resources mapping="/js/**" location="/js/"/>
    <mvc:resources mapping="/css/**" location="/css/"/>
    <mvc:resources mapping="/images/**" location="/images/"/>
```
配置好并重新部署后，按钮生效

#### 接收和响应JSON串

- 客户端给服务器发送一个AJAX请求，请求内容为JSON串，包含User的username和password信息，服务器拿到后将JSON串转换成User对象，模拟从数据库中查询age并设置给User对象，然后返回该User对象所对应的JSON串，客户端拿到后响应在浏览器上
- 步骤：
 1. JSP页面的按钮绑定点击事件，发送AJAX请求，包含User的username和password信息，并将服务器的响应的JSON串弹出：
```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
    <head>
        <title>No Title</title>

        <script src="js/jquery-3.5.1.min.js"></script>

        <script type="text/javascript">
            // 页面加载时，给btn绑定点击事件
            $(function() {
                $("#btn").click(function() {
                    // alert("Hello~")

                    // 发送AJAX请求
                    $.ajax({
                        url : "user/testAjax",
                        type : "post",
                        contentType : "application/json;charset=UTF-8",
                        data : '{"username" : "zhangSan", "password" : "123"}',   // 发送给服务器的JSON串
                        data_type : "json",
                        success : function(data) {
                            // data是服务器响应的JSON数据
                            alert(data);
                            alert(data.username);
                            alert(data.password);
                            alert(data.age);
                        }
                    })
                })
            })
        </script>
    </head>
    <body>
        <button id="btn">发送AJAX请求</button>
    </body>
</html>
```
 2. 在响应方法中，可以通过**`@RequestBody`注解将请求体中的JSON串拿到**，且在pom.xml中导入**jackson-databind**的依赖后，**可以直接将JSON串封装成**User**对象**，然后模拟从数据库中查询age并赋给User对象，再在响应方法的返回值类型前加上**`@ResponseBody`注解，将返回值**User**对象变成JSON串进行返回**：
```
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.11.0</version>
    </dependency>
```
```
@Controller("userController")
@RequestMapping("/user")
public class UserController {
    /**
     * 响应 之 JSON
     */
    @RequestMapping("/testAjax")
    public @ResponseBody User testAjax(@RequestBody User user) {
        System.out.println("testAjax()方法执行了~");
        System.out.println("user = " + user);
        // 模拟从数据库中查询age并赋值给user
        user.setAge(18);
        return user;
    }
}
```
 3. 重新部署后，点击按钮，服务器端成功将客户端发送的JSON串封装到User对象中，也成功将User对象转换成JSON串发回给客户端

---

## 文件上传

- 文件上传的必要前提：
 1. **表单的enctype取值必须是`multipart/form-data`**（默认值是`application/x-www-form-urlencoded`）（enctype表示表单请求正文的类型）
 2. 表单的**method必须是`post`**
 3. 表单要提供一个**文本选择器`<input type="file"/>`**
 4. 在pom.xml中导入**commons-fileupload**的依赖

#### 之前JavaWeb传统的文件上传操作

- 之前**JavaWeb传统的文件上传操作**时，需要**自己创建解析器ServletFileUpload，来对request进行解析**，并判断是否为上传文件项

 例如：
 在pom.xml中导入commons-fileupload的依赖：
```
    <dependency>
      <groupId>commons-fileupload</groupId>
      <artifactId>commons-fileupload</artifactId>
      <version>1.3.3</version>
    </dependency>
```
 Controller：
```
@Controller("fileUploadController")
@RequestMapping("/fileUpload")
public class FileUploadController {
    /**
     * 之前JavaWeb传统的文件上传操作
     */
    @RequestMapping("/testFileUpload1")
    public String testFileUpload1(HttpServletRequest request) throws Exception {
        System.out.println("testFileUpload1()方法执行了~");

        // 设置编码，ServletFileUpload会自动获取该编码，否则上传文件的中文字符会乱码
        request.setCharacterEncoding("UTF-8");

        // 上传文件的保存目录（若不存在，需要创建）
        String dirName = request.getSession().getServletContext().getRealPath("/uploads/");
        File dir = new File(dirName);
        if (!dir.exists())
            dir.mkdir();

        // 得到解析器ServletFileUpload，并解析request
        DiskFileItemFactory factory = new DiskFileItemFactory();
        ServletFileUpload sfu = new ServletFileUpload(factory);
        List<FileItem> fileItems = sfu.parseRequest(request);

        for (FileItem fileItem : fileItems) {
            if (fileItem.isFormField()) {   // 是普通表单项
                System.out.println(fileItem.getFieldName() + ":" + fileItem.getString("UTF-8"));
            } else {    // 是文件表单项
                String fileName = fileItem.getName();
                // 给文件名添加唯一前缀
                fileName = UUID.randomUUID().toString().replaceAll("-","") + "_" + fileName;
                File file = new File(dir, fileName);
                fileItem.write(file);
                System.out.println("已保存到：" + file);
                // 删除临时文件
                fileItem.delete();
            }
        }

        return "success";
    }
}
```
 上传表单：
```
        <h3>文件上传</h3>
		<%-- 之前JavaWeb传统的文件上传操作 --%>
        <form action="fileUpload/testFileUpload1" method="post" enctype="multipart/form-data">
            标题：<input type="text" name="title"> <br/>
            <input type="file" name="照片"> <br/>
            <input type="submit" value="上传">
        </form>
```

#### SpringMVC的文件上传操作（文件解析器）

- **SpringMVC的文件上传操作**，不需要自己来解析request，只需要**在配置文件springmvc.xml中配置文件解析器CommonsMultipartResolver**，然后通过响应方法的**形参**列表`xxx(HttpRequest request, MultipartFile upload)`中的MultipartFile**拿到上传文件项**即可，但需要注意：
 - **上传表单中`<input type="file" name="upload">`的name属性必须和响应方法的形参列表中**`xxx(HttpRequest request, MultipartFile upload)`的**MultipartFile的形参名称相同**
 - **配置文件解析器CommonsMultipartResolver时，其id必须是`multipartResolver`**

 例如：
 在pom.xml中导入commons-fileupload的依赖：
```
    <dependency>
      <groupId>commons-fileupload</groupId>
      <artifactId>commons-fileupload</artifactId>
      <version>1.3.3</version>
    </dependency>
```
 在配置文件springmvc.xml中配置文件解析器CommonsMultipartResolver（其id必须是multipartResolver），并**配置编码**等：
```
    <!-- 文件解析器CommonsMultipartResolver，其id必须是multipartResolver -->
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <!-- 可以在文件解析器中配置编码、上传文件的大小限制等 -->
        <property name="defaultEncoding" value="UTF-8"/>
        <property name="maxUploadSizePerFile" value="10485760"/>    <!-- 配置单个上传文件不能超过10M -->
    </bean>
```
 Controller：
```
@Controller("fileUploadController")
@RequestMapping("/fileUpload")
public class FileUploadController {
    /**
     * SpringMVC的上传操作
     * 注意：形参中的uploadFile必须和上传表单项中<input type="file" name="uploadFile">中的name属性值相同
     */
    @RequestMapping("/testFileUpload2")
    public String testFileUpload2(HttpServletRequest request, MultipartFile uploadFile) throws IOException {
        System.out.println("testFileUpload2()方法执行了~");

        // 上传文件的保存目录
        String dirName = request.getSession().getServletContext().getRealPath("/uploads/");
        File dir = new File(dirName);
        if (!dir.exists())
            dir.mkdir();

        String fileName = uploadFile.getOriginalFilename(); // 获取文件名
        fileName = UUID.randomUUID().toString().replaceAll("-","") + "_" + fileName;

        File file = new File(dir, fileName);
        uploadFile.transferTo(file);    // 保存上传文件到指定位置

        System.out.println("已保存到：" + file);
        return "success";
    }
}
```
 上传表单：
```
        <%-- SpringMVC的文件上传操作 --%>
        <form action="fileUpload/testFileUpload2" method="post" enctype="multipart/form-data">
            <input type="file" name="uploadFile"> <br/> <!-- 注意：name属性必须和响应方法MultipartFile的形参一致 -->
            <input type="submit" value="上传">
        </form>
```

- 注意：如果Tomcat的Deployment中**部署的是war**，则**会保存到Tomcat目录下**，每次重新部署或**重启Tomcat**时，都**会被覆盖**掉；而如果Tomcat的Deployment中**部署的是war exploded**，则**会保存到工程项目中**，重新部署或重启Tomcat也**不会被覆盖**

#### 跨服务器的文件上传操作

- 可能会有专门的文件上传服务器，这就需要我们在接收到上传文件时，将其保存到文件上传服务器中
- 步骤：
 1. 新创建一个模块FileUploadServer，并在webapp目录下创建一个uploads目录，专门用于存储上传的文件
 2. 新创建一个Tomcat服务器作为文件上传服务器，将其端口号修改为9090（避免和当前服务器的8080冲突），并部署上面新创建的模块FileUploadServer
 2. 在当前模块的pom.xml中导入commons-fileupload、jersey-core、jersey-client的依赖：
```
    <dependency>
      <groupId>commons-fileupload</groupId>
      <artifactId>commons-fileupload</artifactId>
      <version>1.3.3</version>
    </dependency>

    <dependency>
      <groupId>com.sun.jersey</groupId>
      <artifactId>jersey-core</artifactId>
      <version>1.9</version>
    </dependency>

    <dependency>
      <groupId>com.sun.jersey</groupId>
      <artifactId>jersey-client</artifactId>
      <version>1.9</version>
    </dependency>
```
 3. Controller：
```
@Controller("fileUploadController")
@RequestMapping("/fileUpload")
public class FileUploadController {
    /**
     * 跨服务器上传文件（新创建一个Tomcat服务器作为文件上传服务器，将其端口设置为9090，并在WEB-INF下创建uploads目录）
     */
    @RequestMapping("/testFileUpload3")
    public String testFileUpload3(MultipartFile uploadFile) throws IOException {
        System.out.println("testFileUpload3()方法执行了~");

        String fileName = uploadFile.getOriginalFilename();
        fileName = UUID.randomUUID().toString().replaceAll("-","") + "_" + fileName;

        // 创建客户端
        Client client = Client.create();
        // 将客户端与文件上传服务器建立连接
        String url = "http://localhost:9090/FileUploadServer/uploads/";
        WebResource webResource = client.resource(url + fileName);
        // 上传文件
        webResource.put(uploadFile.getBytes());

        return "success";
    }
}
```
 4. 上传表单（注意：上传文件的文件名不能包含中文，否则会报400错误，解决方案还不明确）：
```
        <%-- 跨服务器上传文件操作 --%>
        <form action="fileUpload/testFileUpload3" method="post" enctype="multipart/form-data">
            <input type="file" name="uploadFile"> <br/>
            <input type="submit" value="上传">
        </form>
```
 5. 在`Tomcat目录 > conf > web.xml`的DefaultServlet中加上readonly为false的初始化参数（否则会报405错误），然后重启两个服务器，（亲测重新部署没用，需要重启）：
```
    <servlet>
        <servlet-name>default</servlet-name>
        <servlet-class>org.apache.catalina.servlets.DefaultServlet</servlet-class>
        <init-param>
            <param-name>debug</param-name>
            <param-value>0</param-value>
        </init-param>
        <init-param>
            <param-name>listings</param-name>
            <param-value>false</param-value>
        </init-param>
		<init-param>
			<param-name>readonly</param-name>
            <param-value>false</param-value>
		</init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
```
 7. 测试：上传表单访问当前服务器，成功将文件上传到另一台服务器（文件上传服务器），注意文件名不能包含中文

---

## SpringMVC 之 异常处理（异常处理器）

- 一般来说，DAO层会将异常抛给Service层，Service层会将异常抛给Web层（Controller层），Web层会将异常抛给前端控制器（DispatcherServlet），**如果不加处理，前端控制器又会将异常直接抛给浏览器显示，这样对普通用户很不友好**
- 在SpringMVC中，可以**配置异常处理器（实现HandlerExceptionResolver接口），则前端控制器在捕获到异常后，会调用异常处理器来进行处理**，在异常处理器中可以**将错误信息保存到request域中**，并**转发到**用户友好的**错误页面**进行显示
- 步骤：
 1. 自定义异常类MyException：
```
public class MyException extends Exception {
    private String message;

    public String getMessage() { return message; }
    public void setMessage(String message) { this.message = message; }

    public MyException(String message) { this.message = message; }
    public MyException() { }
}
```
 2. 自定义异常处理器MyExceptionResolver，实现HandlerExceptionResolver接口：
```
public class MyExceptionResolver implements HandlerExceptionResolver {
    @Override
    public ModelAndView resolveException(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) {
        // 将异常类Exception转换成自定义异常类MyException
        MyException me = null;
        if (e instanceof MyException)
            me = (MyException) e;
        else
            me = new MyException("系统正在维护中~");

        // 创建ModelAndView，将错误信息保存到request域中，并指定要转发的页面
        ModelAndView mv = new ModelAndView();
        mv.addObject("errorMsg", me.getMessage());
        mv.setViewName("error");

        return mv;
    }
}
```
 3. 在springmvc.xml中，配置视图解析器和自定义的异常处理器MyExceptionResolver（也可以直接在自定义异常类上用`@Component`注解将其配置到IoC核心容器中）：
```
    <context:component-scan base-package="pers.cris"/>

    <mvc:resources mapping="/js/**" location="/js/"/>
    <mvc:resources mapping="/css/**" location="/css/"/>
    <mvc:resources mapping="/images/**" location="/images/"/>

    <mvc:annotation-driven/>

    <!-- 视图解析器 -->
    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/pages/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

    <!-- 配置自定义的异常处理器MyExceptionResolver（也可以用注解配） -->
    <bean id="myExceptionResolver" class="pers.cris.exception.MyExceptionResolver"/>
```
 3. 错误页面error.jsp，由于显示对用户友好的错误信息：
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
 3. Controller，模拟捕获到Service层抛出的异常：
```
@Controller("exceptionController")
@RequestMapping("/exception")
public class ExceptionController {
    @RequestMapping("/testExceptionResolver")
    public String testExceptionResolver() throws MyException {
        System.out.println("testExceptionResolver()方法执行了~");
        try {
            // 模拟调用Service层操作并捕获到异常
            int i = 1 / 0;
        } catch (Exception e) {
            e.printStackTrace();
            throw new MyException("出错啦~~~");
        }
        return "success";
    }
}
```
 5. 请求超链接：`<a href="exception/testExceptionResolver">测试异常处理</a>`
 6. 结果：点击超链接后，成功跳转到错误页面，显示错误信息“出错啦~~~”（而不是显示普通用户看不懂的错误信息），并在控制台输出了错误信息

---

## SpringMVC 之 拦截器

- SpringMVC的处理器**拦截器类似于**Servlet开发中的**过滤器Filter，用于对处理器进行预处理和后处理**
- 拦截器和过滤器的区别：
 - **过滤器**是Servlet规范的一部分，**所有JavaWeb工程都可以使用**；**拦截器**是SpringMVC框架的一部分，**只有SpringMVC框架的工程才能使用**
 - **过滤器**的url-pattern配置成`/*`后，**可以对所有要访问的资源进行拦截**；**拦截器只拦截Controller控制器的方法**，而不会拦截对jsp、html、css、images、js等资源的访问
 - 也就是说，过滤器能做的事，拦截器不一定能做，拦截器能做的事，过滤器全都能做
- **自定义拦截器，需要实现HandlerInterceptor接口，然后在配置文件springmvc.xml中用`<mvc:interceptors>`标签配置自定义的拦截器**，HandlerInterceptor接口的默认方法：
 - **preHandler()方法：在Controller方法执行之前执行**（也可以用request直接跳转到其他页面），**`return true`表示放行，`return false`表示不放行**
 - **postHandler()方法：在Controller方法执行之后，跳转页面执行之前执行**（也可以用request直接跳转到其他页面）
 - **afterCompletion()方法：在跳转页面执行之后执行**

 例如：
 Controller，跳转到success.jsp：
```
@Controller("interceptorController")
@RequestMapping("/intercept")
public class InterceptorController {
    /**
     * 测试拦截器
     */
    @RequestMapping("/testInterceptor")
    public String testInterceptor() {
        System.out.println("testInterceptor()方法执行了~");
        return "success";
    }
}
```
 成功页面success.jsp，执行该页面时会输出“success.jsp页面执行了~”：
```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
    <head>
        <title>成功页面</title>
    </head>
    <body>
        <h3>恭喜你，成功啦~</h3>
        <% System.out.println("success.jsp页面执行了~"); %>
    </body>
</html>
```
 自定义拦截器MyInterceptor1和MyInterceptor2，实现HandlerInterceptor接口（均放行）：
```
public class MyInterceptor1 implements HandlerInterceptor {
    /**
     * 预处理，在Controller方法执行前执行
     * return false 表示不放行
     * return true  表示放行
     * 也可以用request转发到其他页面
     */
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("MyInterceptor1的preHandler()方法执行了~");
        return true;
    }

    /**
     * 后处理，在Controller方法执行后，跳转页面success.jsp页面执行前执行
     * 也可以用request转发到其他页面
     */
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("MyInterceptor1的postHandler()方法执行了~");
    }

    /**
     * 最后处理，在跳转页面success.jsp页面执行后执行
     */
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("MyInterceptor1的afterCompletion()方法执行了~");
    }
}
```
```
public class MyInterceptor2 implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("MyInterceptor2的preHandler()方法执行了~");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("MyInterceptor2的postHandler()方法执行了~");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("MyInterceptor2的afterCompletion()方法执行了~");
    }
}
```
 在配置文件springmvc.xml中配置视图解析器和自定义的拦截器：
```
    <!-- 视图解析器 -->
    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/pages/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

    <!-- 配置拦截器 -->
    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/intercept/*"/>  <!-- 配置要拦截的方法为InterceptorController的所有方法 -->
<!--            <mvc:exclude-mapping path=""/>  也可以配置不拦截的方法-->
            <bean class="pers.cris.interceptor.MyInterceptor1"/>
        </mvc:interceptor>

        <mvc:interceptor>
            <mvc:mapping path="/**"/>   <!-- 拦截所有Controller的所有方法 -->
            <bean class="pers.cris.interceptor.MyInterceptor2"/>
        </mvc:interceptor>
    </mvc:interceptors>
```
 请求超链接：`<a href="intercept/testInterceptor">测试拦截器</a>`
 输出为：
```
MyInterceptor1的preHandler()方法执行了~
MyInterceptor2的preHandler()方法执行了~
testInterceptor()方法执行了~
MyInterceptor2的postHandler()方法执行了~
MyInterceptor1的postHandler()方法执行了~
success.jsp页面执行了~
MyInterceptor2的afterCompletion()方法执行了~
MyInterceptor1的afterCompletion()方法执行了~
```

- **如果在拦截器的preHandler()中利用request转发到了其他页面（如error.jsp），然后再放行，则浏览器会显示error.jsp，但success.jsp会按计划执行**，即：
```
public class MyInterceptor1 implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("MyInterceptor1的preHandler()方法执行了~");
        request.getRequestDispatcher("/WEB-INF/pages/error.jsp").forward(request, response);
        return true;
    }
}
```
则浏览器显示error.jsp，但输出显示success.jsp按计划被执行：
```
MyInterceptor1的preHandler()方法执行了~
testInterceptor()方法执行了~
MyInterceptor1的postHandler()方法执行了~
success.jsp页面执行了~
MyInterceptor1的afterCompletion()方法执行了~
```
- **如果在拦截器的postHandler()中利用request转发到了其他页面（如error.jsp），浏览器会显示error.jsp，但success.jsp也会按计划执行**，即：
```
public class MyInterceptor1 implements HandlerInterceptor {
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("MyInterceptor1的postHandler()方法执行了~");
        request.getRequestDispatcher("/WEB-INF/pages/error.jsp").forward(request, response);
    }
}
```
则浏览器显示error.jsp，但输出显示success.jsp按计划被执行：
```
MyInterceptor1的preHandler()方法执行了~
testInterceptor()方法执行了~
MyInterceptor1的postHandler()方法执行了~
success.jsp页面执行了~
MyInterceptor1的afterCompletion()方法执行了~
```