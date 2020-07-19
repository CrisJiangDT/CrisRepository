<link href="http://cdn.bootcss.com/highlight.js/8.0/styles/monokai_sublime.min.css" rel="stylesheet">  
<script src="http://cdn.bootcss.com/highlight.js/8.0/highlight.min.js"></script>  
<script>hljs.initHighlightingOnLoad();</script>

# Spring #

- Spring是分层的Java SE/EE应用**全栈**轻量级开源框架，**以IoC（Inverse of Control，反转控制）和AOP（Aspect Oriented Programing，面向切面编程）为内核**，同时，Spring也不排斥其他框架，方便集成各种优秀的框架，因此受到欢迎
- Spring的优势：
 - 方便解耦，简化开发
 - AOP编程的支持
 - 声明式事务的支持
 - 方便程序的测试
 - 方便集成各种优秀的框架

#### 传统JDBC的耦合问题 ####

- 注册驱动时，`DriverManager.registerDriver(new com.mysql.jdbc.Driver());`
 - 存在的问题是如果没有驱动类相关的jar包，在编译器就会报错，即依赖关系很强
 - 实际开发中，**应该做到：编译期不依赖，运行时才依赖**
 - **解耦思路是使用反射来创建对象，从而避免使用`new`关键字**，因此后来改成`Class.forName("com.mysql.jdbc.Driver");`
 - 但是这样做也把类名写死了，若要换成其他数据库，则需要修改源码
 - **解耦思路是通过读取配置文件来获取想要创建的对象的全限定类名**

#### 传统的三层架构中的耦合问题 ####

- 传统的三层架构中的耦合问题：
 - Service层由于依赖DAO层，因此会`new`一个DAO实现类对象
 - 表现层由于依赖Service层，因此会`new`一个Serivice实现类对象
- **解耦思路：**
 1. **运用工厂模式，用工厂类来创建所需的DAO/Service实现类对象**
 1. **在工厂类中，读取配置文件来获取所需的DAO/Service实现类对象的全限定类名**
 2. **在工厂类中，用反射创建所需的DAO/Service实现类对象**

###### 运用工厂模式和配置文件进行解耦 ######

解耦过程：
 Resource目录下的配置文件bean.properties中的内容：
```
accountDao = pers.cris.dao.impl.AccountDaoImpl
accountService = pers.cris.service.impl.AccountServiceImpl
```
 工厂类BeanFactory：
```
/**
 * @BelongProject: SpringDemo
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/7/15 15:25
 * @Description: 工厂类，通过读取配置文件和反射，来获取Bean对象
 */
public class BeanFactory {
    private final static Properties prop;

    // 通过静态代码块加载配置文件的信息
    static {
        InputStream in = BeanFactory.class.getClassLoader().getResourceAsStream("bean.properties");
        prop = new Properties();
        try {
            prop.load(in);
        } catch (IOException e) {
            throw new ExceptionInInitializerError("初始化工厂类出错啦~");
        }
    }

    /**
     * 根据Bean的名称获取对象
     * @param beanName Bean的名称（需要在配置文件中配置其全限定类名）
     * @return Bean对象
     */
    public static Object getBean(String beanName) {
        String className = prop.getProperty(beanName);
        try {
            return Class.forName(className).getDeclaredConstructor().newInstance();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }
}
```
 在Service层中，获取DAO实现类的方式变成：`IAccountDao accountDao = (IAccountDao) BeanFactory.getBean("accountDao");`
 在表现层中，获取Service实现类的方式变成：`IAccountService accountService = (IAccountService) BeanFactory.getBean("accountService");`

- 这样解耦后
 - 即使没有DAO实现类或Service实现类，也只是运行时报错，而编译期也不会报错
 - 想要修改DAO实现类或Service实现类，只需要在配置文件中更改属性值即可

###### 工厂模式解耦的升级版——单例 ######

- 一般我们需要通过工厂模式生产的Bean对象只需要单例即可，不用每次都创建一个新的对象，因此可以对工厂进行升级，将其所生产的Bean对象变为单例的（单例最好不要定义成员属性，否则容易产生多线程并发问题）

升级版工厂类BeanFactory（创建单例Bean对象）：
```
/**
 * @BelongProject: SpringDemo
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/7/15 15:25
 * @Description: 工厂类，通过读取配置文件和反射，来获取单例的Bean对象
 */
public class BeanFactory {
    private static final Properties prop;
    private static final Map<String, Object> beans;   // 用于存储单例的Bean对象（key为Bean的全限定类名，value为Bean对象）

    // 通过静态代码块加载配置文件的信息，并初始化beans
    static {
        InputStream in = BeanFactory.class.getClassLoader().getResourceAsStream("bean.properties");
        beans = new HashMap<>();
        prop = new Properties();
        try {
            prop.load(in);
        } catch (IOException e) {
            throw new ExceptionInInitializerError("初始化工厂类出错啦~");
        }
    }

    /**
     * 根据Bean的名称获取其单例对象
     * @param beanName Bean的名称（需要在配置文件中配置其全限定类名）
     * @return Bean的单例对象
     */
    public static Object getBean(String beanName) {
        String className = prop.getProperty(beanName);
        try {
//            return Class.forName(className).getDeclaredConstructor().newInstance();   // 多例

            // 单例
            // 若创建过该类对象，则直接从beans中返回该对象即可
            // 若没创建过该类对象，则通过反射创建该类对象，并保存到beans中，然后再返回该对象
            if (beans.containsKey(className))
                return beans.get(className);
            Object bean = Class.forName(className).getDeclaredConstructor().newInstance();
            beans.put(className, bean);
            return bean;
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }
}
```

---

## IoC ##

- **IoC（Inversion of Control，控制反转），就是把创建对象的权力交给框架/工厂类，好处是降低程序的耦合**，例如：
 - 原来Service层是自己`new`一个DAO实现类对象：`IAccountDao accountDao = new AccountDaoImpl();`，因此Service层可以自己决定`new`哪个DAO实现类对象
 - 但现在用工厂模式，通过工厂类来获取DAO实现类对象：`IAccountDao accountDao = (IAccountDao) BeanFactory.getBean("accountDao");`，Service层完全不知道获取到的是哪个DAO实现类对象，而是由工厂类（或配置文件）决定的

#### Spring基于XML的IoC环境搭建和入门 ####

- 步骤：
 1. 创建Maven工程，在pom.xml中导入spring-context的依赖，并添加：
```
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.encoding>UTF-8</maven.compiler.encoding>
        <java.version>11</java.version>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
    </properties>
```
 2. 在Resource目录下创建Spring的XML配置文件bean.xml，其约束为：
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
</beans>
```
 3. 在bean.xml中配置Bean：
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- id属性指定Bean的唯一标识，class属性指定Bean的全限定类名 -->
    <bean id="accountDao" class="pers.cris.dao.impl.AccountDaoImpl"/>
    <bean id="accountService" class="pers.cris.service.impl.AccountServiceImpl"/>
</beans>
```
 4. 在表现层可以通过Spring核心容器获取Service实现类对象：
```
        // 1. 获取核心容器对象，要指定配置文件
        ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
        // 2. 通过id获取Bean对象，要指定将Bean强转成的类型
        accountService = ac.getBean("accountService", IAccountService.class);
```
---

#### Spring的IoC核心容器 ####

- **核心容器BeanFactory接口和核心容器ApplicationContext接口：**
 - **BeanFactory：在构建核心容器时，创建Bean对象使用的方式是延迟加载**，也就是什么时候通过id获取Bean对象，什么时候才真正创建Bean对象，**适用于多例的Bean**
 - **ApplicationContext（常用）：在构建核心容器时，可以通过配置Bean是单例或多例来决定创建Bean对象使用的方式是立即加载还是延迟加载**
- **核心容器ApplicationContext接口的三个常用实现类：**
 - **ClassPathXmlApplicationContext：可以加载类路径下的配置文件（常用）**
 - **FileSystemXmlApplicationContext：可以加载磁盘任意路径下的配置文件**
 - **AnnotationConfigApplicationContext：可以读取注解来创建容器（常用）**

##### Spring中的Bean的细节 #####

###### Spring中创建Bean的三种方式 ######

- Spring中创建Bean并将其存入核心容器的三种方式，及其在配置文件中的写法：
 - **使用Bean的默认构造函数创建Bean对象：**
```
    <!-- 创建Bean方式一：默认构造函数 -->
    <!-- class属性指定Bean的全限定类名 -->
    <bean id="accountService" class="pers.cris.service.impl.AccountServiceImpl"/>
```
 - **使用某个类（如普通工厂类）中的某个非静态方法获取Bean对象：**
```
    <!-- 创建Bean方式二：某个类（如普通工厂类）中的非静态方法（非静态方法是属于对象的） -->
    <!-- 先指定该普通工厂类，再指定Bean，其中factory-bean属性指定该普通工厂类的id，factory-method属性指定要调用该普通工厂类的非静态方法名 -->
    <bean id="instanceFactory" class="pers.cris.factory.InstanceFactory"/>
    <bean id="accountService" factory-bean="instanceFactory" factory-method="getAccountService"/>
```
 - **使用某个类（如静态工厂类）中的某个静态方法获取Bean对象：**
```
    <!-- 创建Bean方式三：某个类（如静态工厂类）中的静态方法（静态方法是属于类的） -->
    <!-- 先指定该静态工厂类，再指定bean，其中class属性指定该静态工厂类的id，factory-method属性指定要调用该静态工厂类的静态方法名 -->
    <bean id="staticFactory" class="pers.cris.factory.StaticFactory"/>
    <bean id="accountService" class="pers.cris.factory.StaticFactory" factory-method="getAccountService"/>
```

###### Spring中Bean的作用范围 ######

- Spring中Bean的作用范围可以在配置文件的`<bean>`标签中通过`scope`属性来指定，有以下取值：
 - **`scope=singleton`：单例（默认值）**
 - **`scope=prototypr`：多例**
 - `scope=request`：作用于Web应用的Request域
 - `scope=session`：作用于Web应用的Session域
 - `scope=global-session`：作用于集群环境下的全局Session域（当不是集群环境时，它就是Session域）

###### Spring中Bean的生命周期 ######

- 对于**单例Bean对象（立即加载）**，其生命周期为：
 - 出生：当核心容器创建时，Bean对象出生
 - 活着：只要核心容器还在，Bean对象一直活着
 - 死亡：当核心容器销毁时，Bean对象销毁
 - 总结：**单例Bean的生命周期和核心容器相同**
- 对于**多例Bean对象（延迟加载）**，其生命周期为：
 - 出生：当我们调用核心容器的方法来获取该Bean的对象时，它才出生
 - 活着：Bean对象只要在使用过程中就一直活着
 - 死亡：当Bean对象长时间不被使用，且没有别的对象引用时，由JVM的垃圾回收器回收
- `<bean>`标签的`init-method`属性和`destroy-method`属性可以指定Bean创建和销毁时要执行的方法（Bean单例时，要想销毁方法被执行，需要手动销毁核心容器；Bean多例时，就算手动销毁核心容器，也不会执行销毁方法，而是需要等该Bean对象销毁时才会执行）

---

#### 依赖注入（DI，Dependency Injection） ####

- IoC的作用是解耦，即降低程序之间的依赖关系，但不可能完全消除耦合，而**依赖注入的作用就是通过配置文件或注解，由Spring来维护程序之间的依赖关系**
- 依赖注入能注入的数据有三类：
 - 基本类型和String
 - Bean（使用XML配置文件或注解配置过的Bean，即在Spring核心容器中的Bean）
 - 数组/集合类型
- **依赖注入的方式有三种：**
 1. **使用构造函数注入**
 2. **使用set方法注入**
 3. **使用注解注入**

###### 使用构造函数注入 ######

- 配置文件中，**在`<bean>`标签内部使用`<constructor-arg>`标签**来配置，该标签中的属性：
 - **`name`属性：用于指定给构造函数中指定名称的参数赋值**
 - `type`属性：用于指定给构造函数中指定数据类型的参数赋值
 - `index`属性：用于指定给构造函数中指定索引的参数赋值
 - **`value`属性：用于指定要给参数赋的基本数据类型或String类型的数据值**
 - **`ref`属性：用于指定要给参数赋的Bean类型的数据值，它指的是在IoC核心容器中的Bean类型**
- 步骤：
 1. 给AccountServiceImpl类添加几个待注入的属性，并在saveAccount()方法中输出它们的值：
```
public class AccountServiceImpl implements IAccountService {
    // 要注入的参数
    public String arg_str;
    public int arg_int;
    public Date arg_date;

    public AccountServiceImpl(String arg_str, int arg_int, Date arg_date) {
        this.arg_str = arg_str;
        this.arg_int = arg_int;
        this.arg_date = arg_date;
    }
    
    @Override
    public void saveAccount() {
        System.out.println("AccountServiceImpl中的saveAccount()方法被调用啦~");
        System.out.println("arg_str = " + arg_str);
        System.out.println("arg_int = " + arg_int);
        System.out.println("arg_date = " + arg_date);
    }
}
```
 2. 在bean.xml中进行accountService及其依赖注入的配置：
```
    <!-- 1. 使用构造函数注入 -->
    <bean id="accountService" class="pers.cris.service.impl.AccountServiceImpl">
        <constructor-arg name="arg_str" value="jdt"/>
        <constructor-arg name="arg_int" value="7"/> <!-- 会自动将字符串转换成int类型 -->
        <constructor-arg name="arg_date" ref="now_date"/>   <!-- 引用核心容器中id为"now_date"的对象 -->
    </bean>

    <bean id="now_date" class="java.util.Date"/>    <!-- 往核心容器中存入当前时间的Date对象 -->
```
 3. 在业务层调用accountService的saveAccount()方法进行验证：
```
public class Client {
    public static void main(String[] args) {
        ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
        IAccountService accountService = ac.getBean("accountService", IAccountService.class);
        accountService.saveAccount();
    }
}
```
结果：accountService中的arg_str、arg_int和arg_date注入成功
- 使用构造函数注入的优势：创建的对象的全部属性都会有值，否则无法创建成功
- **使用构造函数注入的弊端：在创建对象时，不需要用到的属性也必须提供，否则无法创建成功**

###### 使用set方法注入（常用） ######

- 配置文件中，**在`<bean>`标签内部使用`<property>`标签**来配置，该标签中的属性：
 - **`name`属性：用于指定给构造函数中指定名称的参数赋值**
 - **`value`属性：用于指定要给参数赋的基本数据类型或String类型的数据值**
 - **`ref`属性：用于指定要给参数赋的Bean类型的数据值，它指的是在IoC核心容器中的Bean类型**
- 步骤：
 1. 给AccountServiceImpl2类添加几个待注入的属性，并**给它们创建set方法**，并在saveAccount()方法中输出它们的值：
```
public class AccountServiceImpl2 implements IAccountService {
    // 要注入的参数
    private String arg_str;
    private int arg_int;
    private Date arg_date;

    public void setArg_str(String arg_str) { this.arg_str = arg_str; }
    public void setArg_int(int arg_int) { this.arg_int = arg_int; }
    public void setArg_date(Date arg_date) { this.arg_date = arg_date; }

    @Override
    public void saveAccount() {
        System.out.println("AccountServiceImpl中的saveAccount()方法被调用啦~");
        System.out.println("arg_Str = " + arg_str);
        System.out.println("arg_int = " + arg_int);
        System.out.println("arg_date = " + arg_date);
    }
}
```
 2. 在bean.xml中进行accountService2及其依赖注入的配置：
```
    <bean id="now_date" class="java.util.Date"/>    <!-- 往核心容器中存入当前时间的Date对象 -->

    <!-- 2. 使用set方法注入 -->
    <bean id="accountService2" class="pers.cris.service.impl.AccountServiceImpl2">
        <property name="arg_str" value="jdtjdt"/>
        <property name="arg_int" value="77"/>
        <property name="arg_date" ref="now_date"/>
    </bean>
```
 3. 在业务层调用accountService2的saveAccount()方法进行验证：
```
public class Client {
    public static void main(String[] args) {
        ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");

        IAccountService accountService2 = ac.getBean("accountService2", IAccountService.class);
        accountService2.saveAccount();
    }
}
```
结果：accountService2中的arg_str、arg_int和arg_date注入成功
- 使用set方法注入的优势：创建对象时对属性值没有限制，可以直接调用默认构造函数
- **使用set方法注入的弊端：由于是用默认构造函数创建对象，如果某个属性必须有值，则获取对象时，可能并没有使用set方法进行注入，导致该属性没有值**

###### 数组/集合数据的注入 ######

- 配置文件中，**在`<bean>`标签内部使用`<property>`标签及其子标签**来配置，**数组/集合数据的注入时，结构相同，子标签可以互换：**
 - 用于给数组、List、Set进行注入的标签：`<array>`、`<list>`、`<set>`
 - 用于给Map、Properties进行注入的标签：`<map>`、`<props>`
- 步骤：
 1. 给AccountServiceImpl3类添加几个待注入的数组/集合属性，并给它们创建set方法，并在saveAccount()方法中输出它们的值：
```
public class AccountServiceImpl3 implements IAccountService {
    // 要注入的数组或集合
    private String[] arg_arr;
    private List<String> arg_list;
    private Set<String> arg_set;
    private Map<String ,String> arg_map;
    private Properties arg_props;

    public void setArg_arr(String[] arg_arr) { this.arg_arr = arg_arr; }
    public void setArg_list(List<String> arg_list) { this.arg_list = arg_list; }
    public void setArg_set(Set<String> arg_set) { this.arg_set = arg_set;}
    public void setArg_map(Map<String, String> arg_map) { this.arg_map = arg_map; }
    public void setArg_prop(Properties arg_props) { this.arg_props = arg_props; }

    @Override
    public void saveAccount() {
        System.out.println("AccountServiceImpl中的saveAccount()方法被调用啦~");
        System.out.println("arg_arr[] = " + Arrays.toString(arg_arr));
        System.out.println("arg_list = " + arg_list);
        System.out.println("arg_set = " + arg_set);
        System.out.println("arg_map = " + arg_map);
        System.out.println("arg_props = " + arg_props);
    }
}
```
 2. 在bean.xml中进行accountService3及其依赖注入的配置：
```
    <!-- 注入数组/集合数据 -->
    <!-- 数组/集合数据的注入时，结构相同，标签可以互换：
            用于给数组、List、Set进行注入的标签：<array>、<list>、<set>
            用于给Map、Properties进行注入的标签：<map>、<props>  -->
    <bean id="accountService3" class="pers.cris.service.impl.AccountServiceImpl3">
        <property name="arg_arr">
            <array>
                <value>arr_1</value>
                <value>arr_2</value>
                <value>arr_3</value>
            </array>
        </property>

        <property name="arg_list">
            <list>
                <value>list_1</value>
                <value>list_2</value>
                <value>list_3</value>
            </list>
        </property>

        <property name="arg_set">
            <set>
                <value>set_1</value>
                <value>set_2</value>
                <value>set_3</value>
            </set>
        </property>

        <property name="arg_map">
            <map>
                <entry key="key_1" value="val_1"/>
                <entry key="key_2" value="val_2"/>
                <entry key="key_3" value="val_3"/>
            </map>
        </property>
        
        <property name="arg_props">
            <props>
                <prop key="propKey_1">propVal_1</prop>
                <prop key="propKey_2">propVal_2</prop>
            </props>
        </property>
    </bean>
```
 3. 在业务层调用accountService3的saveAccount()方法进行验证：
```
public class Client {
    public static void main(String[] args) {
        ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
        IAccountService accountService3 = ac.getBean("accountService3", IAccountService.class);
        accountService3.saveAccount();
    }
}
```
结果：accountService3中的arg_arr、arg_list、arg_set、arg_map和arg_props注入成功
---

#### Spring基于XML的IoC案例 ####

- 实现三层架构，持久层选用DBUtils
- 步骤：
 1. pom.xml中导入Spring、MySQL、DBUtils、C3P0连接池、junit的依赖：
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>SpringDemo5_xmlProject</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.2.7.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.20</version>
        </dependency>

        <dependency>
            <groupId>commons-dbutils</groupId>
            <artifactId>commons-dbutils</artifactId>
            <version>1.6</version>
        </dependency>

        <dependency>
            <groupId>c3p0</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.1.2</version>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.encoding>UTF-8</maven.compiler.encoding>
        <java.version>11</java.version>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
    </properties>
</project>
```
 2. 建出三层架构，其中AccountDaoImpl中有QueryRunner属性，AccountServiceImpl中有IAccountDao属性
 3. 给上述两个属性配置依赖注入：给它们加上set方法，然后在bean.xml中进行配置如下：
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- 配置Service对象 -->
    <bean id="accountService" class="pers.cris.service.impl.AccountServiceImpl">
        <property name="accountDao" ref="accountDao"/>  <!-- 注入DAO -->
    </bean>

    <!-- 配置DAO对象 -->
    <bean id="accountDao" class="pers.cris.dao.impl.AccountDaoImpl">
        <property name="runner" ref="runner"/>  <!-- 注入QueryRunner -->
    </bean>

    <!-- 配置QueryRunner，配置成多例模式，避免多线程并发问题 -->
    <bean id="runner" class="org.apache.commons.dbutils.QueryRunner" scope="prototype">
        <constructor-arg name="ds" ref="dataSource"/>   <!-- 没有set方法，只能用构造函数的方法注入数据源DataSource -->
    </bean>

    <!-- 配置数据源DataSource -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <!-- 注入JDBC四大参数 -->
        <property name="driverClass" value="com.mysql.jdbc.Driver"/>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/mydb?serverTimezone=Asia/Shanghai"/>
        <property name="user" value="Cris"/>
        <property name="password" value="123"/>
    </bean>
</beans>
```
 4. 测试（**注意：要从核心容器中取出accountService来测试，而不是自己`new`一个！**）

---

#### Spring基于注解的IoC配置 ####

- Spring的IoC注解分为四类：
 - 用于创建Bean对象并添加到核心容器中的：作用和XML配置文件中的`<bean>`标签一样
 - 用于往Bean对象中注入数据的：作用和XML配置文件`<bean>`标签中的`<property>`子标签一样
 - 用于改变Bean对象作用范围的：作用和XML配置文件`<bean>`标签中的`scope`属性一样
 - 和Bean对象生命周期相关的：作用和XML配置文件`<bean>`标签中的`init-method`属性和`destroy-method`属性一样

##### 用于创建Bean对象并添加到核心容器中的注解： `@Component`、`@Controller`、`@Service`、`@Repository` ######

- **注解`@Component`**写在Bean类上，**作用是将Bean添加到核心容器中，可以用`value`属性指定其在核心容器中的id（若不指定value，则id默认为首字母小写的类名）**
 - 一般，**注解`@Controller`、`@Service`、`@Repository`分别用于表现层、业务层和持久层，它们的作用和`@Component`没有任何区别，只是名字不同，可以使得三层结构更加清晰**
- 步骤：
 1. 在AccountServiceImpl类上加一个`@Component`注解，并用value属性指定其在核心容器中的id：
```
@Component("accountService")    // 若不指定value值，则默认为首字母小写的类名
public class AccountServiceImpl implements IAccountService {
	...
}
```
 2. 在配置文件bean.xml中告知Spring在创建容器时要扫描注解的包（**注意：由于`<context:component-scan>`标签在context名称空间中，因此bean.xml的XML约束发生了改变**）：
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 告知Spring在创建容器时要扫描注解的包，配置所需要的标签不是在之前的beans约束中，而是在context名称空间和约束中 -->
    <context:component-scan base-package="pers.cris"/>
</beans>
```
 3. 测试：
```
public class Client {
    public static void main(String[] args) {
        ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
        IAccountService accountService = ac.getBean("accountService", IAccountService.class);
        System.out.println(accountService);
    }
}
```
结果：成功从Spring核心容器中获取Service实现类对象

##### 用于注入基本数据类型数据和String类型数据的注解：`@Value` #####

- `@Value`注解写在属性或set方法上，使用其`value`属性指定要注入的数据值
```
    @Value("jdt") // 注入基本数据类型和String类型的数据
    private String arg_str;
    
    private int arg_int;

    @Value("7")
    public void setArg_int(int arg_int) {
        this.arg_int = arg_int;
    }
```

##### 用于注入Bean类型数据的注解：`@Autowired`、`@Qualifier`、`@Resource` #####

###### 按照类型自动注入数据的注解：`@Autowired` ######

- **将注解`@Autowired`写在属性或set方法上，Spring就会根据核心容器中的类型进行自动注入，要求是：**
 - **核心容器中只有一个该类对象**
 - **核心容器中有多个该类对象，但核心容器中某个Bean的id和要注入的属性的属性名相同**
- 步骤：
 1. 通过`@Service`注解和`@Repository`注解将AccountServiceImpl和AccountDaoImpl配置到核心容器中，并给AccountServiceImpl的accountDao属性配上`@Autowired`注解：
```
@Service("accountService")    // 若不指定value值，则默认为首字母小写的类名
public class AccountServiceImpl implements IAccountService {
    @Autowired	// 根据类型自动注入
    private IAccountDao accountDao;

    @Override
    public void saveAccount() {
        accountDao.saveAccount();
    }
}
```
```
@Repository("accountDao")
public class AccountDaoImpl implements IAccountDao {
	...
}
```
 2. 在配置文件bean.xml中告知Spring在创建容器时要扫描注解的包
 3. 测试：
```
public class Client {
    public static void main(String[] args) {
        ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
        IAccountService accountService = ac.getBean("accountService", IAccountService.class);
        accountService.saveAccount();
    }
}
```
结果：accountService中没有报空指针异常，说明accountService的accountDao属性自动注入成功
- 若再在核心容器中配置一个AccountDaoImpl2（此时核心容器中有两个IAccountDao类对象），则若要注入的属性的属性名`accountDao`与核心容器中其中一个该类对象的id`accountDao`相同，则会自动注入该对象，否则注入失败
- 若在核心容器中没有该类对象，则注入失败
- **细节：在使用注解进行注入时，set方法不是必须的**

###### 在按照类型自动注入的基础上指定id的注解：`@Qualifier` ######

- **`@Qualifier`注解写在属性上时，不能单独使用，需要配合`@Autowired`注解：**
 - `@Autowired`注解用于指定按照类型自动注入
 - **`@Qualifier`注解的`value`属性用于指定要注入的Bean在核心容器中的id**
```
    @Autowired
    @Qualifier("accountDao2") // 自动通过类型注入核心容器中的对象，若有多个该类型的对象，则指定要注入id为"accountDao2"的对象
    private IAccountDao accountDao;
```

###### （常用）直接按照核心容器中的id进行注入的注解：`@Resource` ######

- **`@Resource`注解可以直接按照Bean在核心容器中的id来进行注入，其`name`属性指定Bean在核心容器中的id**
- 若发现没有`@Resource`注解，则在pom.xml中引入如下javax.annotation-api的依赖：
```
<dependency>
    <groupId>javax.annotation</groupId>
    <artifactId>javax.annotation-api</artifactId>
    <version>1.3.1</version>
</dependency>
```
```
    @Resource(name = "accountDao2")	// 指定要注入核心容器中id为"accountDao2"的对象
    private IAccountDao accountDao;
```

#### 和Bean的作用范围相关的注解：`@Scope` ####

- **`@Scope`注解写在类上，其`value`属性可以指定该Bean的作用范围**，取值可以为`singleton`（默认）或`prototype`
```
@Service("accountService")
@Scope("prototype")	// 指定AccountServiceImpl为多例的
public class AccountServiceImpl implements IAccountService {
	...
}
```

#### 和Bean的生命周期相关的注解：`@PostConstruct`、`@PreDestroy` ####

- 在方法上写上`@PostConstruct`或`@PreDestroy`注解，则会在该Bean对象创建后或销毁前执行（注意：单例Bean的销毁方法需要手动关闭核心容器才会执行，多例Bean的销毁方法即时手动关闭核心容器也不会立即执行）

---

#### Spring基于注解的IoC案例 ####

- 实现三层架构，持久层选用DBUtils
- 步骤：
 1. pom.xml中导入Spring、MySQL、DBUtils、C3P0连接池、junit、**javax.annotation-api**的依赖：
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>SpringDemo6_annotationProject</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.2.7.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.20</version>
        </dependency>

        <dependency>
            <groupId>commons-dbutils</groupId>
            <artifactId>commons-dbutils</artifactId>
            <version>1.6</version>
        </dependency>

        <dependency>
            <groupId>c3p0</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.1.2</version>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>javax.annotation</groupId>
            <artifactId>javax.annotation-api</artifactId>
            <version>1.3.1</version>
        </dependency>
    </dependencies>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.encoding>UTF-8</maven.compiler.encoding>
        <java.version>11</java.version>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
    </properties>
</project>
```
 2. 建出三层架构，并加上注解：
```
@Repository("accountDao")
public class AccountDaoImpl implements IAccountDao {
    @Resource(name = "runner")
    private QueryRunner runner;
	...
}
```
```
@Service("accountService")
public class AccountServiceImpl implements IAccountService {
    @Resource(name = "accountDao")
    private IAccountDao accountDao;
	...
}
```
 3. 配置bean.xml：
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 告知Spring在创建容器时要扫描注解的包 -->
    <context:component-scan base-package="pers.cris"/>

    <!-- 配置QueryRunner，配置成多例模式，避免多线程并发问题 -->
    <bean id="runner" class="org.apache.commons.dbutils.QueryRunner" scope="prototype">
        <constructor-arg name="ds" ref="dataSource"/>   <!-- 没有set方法，只能用构造函数的方法注入数据源DataSource -->
    </bean>

    <!-- 配置数据源DataSource -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <!-- 注入JDBC四大参数 -->
        <property name="driverClass" value="com.mysql.jdbc.Driver"/>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/mydb?serverTimezone=Asia/Shanghai"/>
        <property name="user" value="Cris"/>
        <property name="password" value="123"/>
    </bean>
</beans>
```
 4. 测试

---

#### Spring基于注解的IoC配置 之 去除配置文件bean.xml ####

- 上面基于注解的IoC仍然需要配置文件bean.xml，如何去除？
 - 解决方案：
 1. **可以创建一个Spring的配置类**，在该配置类上**写上`@Configuarion`注解，表明其为Spring的配置类**，使得其**作用与bean.xml相同**
- Spring配置类如何替代配置文件中告知Spring在创建容器时要扫描注解的包？例如：
```
<!-- 告知Spring在创建容器时要扫描注解的包 -->
<context:component-scan base-package="pers.cris"/>
```
 - 解决方案：
 2. 在该配置类上写上**`@Component`注解**，该注解的**`value`属性和`basePackage`属性等价，都可以指定创建核心容器时要扫描注解的包（可以有多个）**
- Spring配置类如何替代配置文件中将jar包中无法添加注解的Bean添加到核心容器中？例如：
```
<!-- 配置QueryRunner，配置成多例模式，避免多线程并发问题 -->
<bean id="runner" class="org.apache.commons.dbutils.QueryRunner">
   <constructor-arg name="ds" ref="dataSource"/>   <!-- 没有set方法，只能用构造函数的方法注入数据源DataSource -->
</bean>
<!-- 配置数据源DataSource -->
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
   <!-- 注入JDBC四大参数 -->
   <property name="driverClass" value="com.mysql.jdbc.Driver"/>
   <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/mydb?serverTimezone=Asia/Shanghai"/>
   <property name="user" value="Cris"/>
   <property name="password" value="123"/>
</bean>
```
 - 解决方案：
 1. 在配置类中**创建一个方法，返回值为要添加到核心容器中的Bean，并在该方法上添加`@Bean`注解，来将方法返回的Bean添加到核心容器中，该注解的`name`属性可以指定该Bean在核心容器中的id**
- 同时，由于没有了配置文件bean.xml，因此**核心容器**不能再使用ClassPathXmlApplicationContext，而**要使用AnnotationConfigApplicationContext，创建它时需要指定Spring配置类（可以有多个）**

 替代bean.xml配置文件的Spring配置类：
```
@Configuration  // 指定该类为Spring的配置类
@ComponentScan("pers.cris") // 指定创建核心容器时要扫描注解的包
public class SpringConfiguration {

    @Bean(name = "runner")  // 将方法的返回值Bean添加到核心容器中，并指定其在核心容器中的id
    @Scope("prototype") // 指定QueryRunner为多例的
    public QueryRunner createQueryRunner(DataSource dataSource) {   // 方法有参数时，会自动到核心容器中查找，查找方式与@Autowired相同
        return new QueryRunner(dataSource);
    }

    @Bean(name = "dataSource")
    public DataSource createDataSource() {
        ComboPooledDataSource ds = new ComboPooledDataSource();
        try {
            ds.setDriverClass("com.mysql.jdbc.Driver");
            ds.setJdbcUrl("jdbc:mysql://localhost:3306/mydb?serverTimezone=Asia/Shanghai");
            ds.setUser("Cris");
            ds.setPassword("123");
        } catch (Exception e) {
            throw new RuntimeException();
        }
        return ds;
    }
}
```

 测试：
```
    @Test
    public void testFindAll() {
        // 核心容器使用AnnotationConfigApplicationContext，要指定Spring配置类
        ApplicationContext ac = new AnnotationConfigApplicationContext(SpringConfiguration.class);
        IAccountService accountService = ac.getBean("accountService", IAccountService.class);

        List<Account> accounts = accountService.findAll();
        for (Account account : accounts)
            System.out.println(account);
    }
```

###### 注意事项 ######

1.  **若配置类中创建Bean的方法有参数**（如上述createDataSource()方法）时，Spring**会自动到核心容器中进行查找，查找方式和通过类型自动注入的`@Autowired`的方式相同**（先找相同的类型对象，若不止一个相同类型对象则找其中名字相同的，若没有名字相同的则报错）
2. 若配置类中创建Bean的方法有参数时，**也可以在方法的形参前面添加`@Qulifier`注解来指定要从核心容器中获取的Bean的id**，那么在核心容器中有多个该类型的Bean时，就**可以实现自由选择**，例如：
```
    @Bean(name = "runner")  // 将方法的返回值Bean添加到核心容器中，并指定其在核心容器中的id
    @Scope("prototype") // 指定QueryRunner为多例的
    // 方法有参数时，会自动到核心容器中查找，查找方式与@Autowired相同，也可以在形参前用@Qualifier注解来指定要获取的Bean在核心容器中的id
    public QueryRunner createQueryRunner(@Qualifier("dataSource") DataSource dataSource) {
        return new QueryRunner(dataSource);
    }
```

###### 多个配置类的情况 ######

- 有多个配置类时：
 - 可以在创建AnnotationConfigApplicationContext时同时指定多个配置类的字节码文件，则它们之间是兄弟关系
 - **可以在A配置类上用`@Import`注解（`value`属性指定要导入的子配置类）导入B配置类，此时A配置类为父配置类，B配置类为子配置类**
- A配置类上可以省略`@Configuration`注解的情况：
 - 在创建AnnotationConfigApplicationContext时指定过A配置类
 - 在其他配置类上的`@ComponentScan`注解中配置了扫描A配置类所在的包
 - 在其他配置类（父配置类）上用`@Import`注解导入了A配置类（子配置类）

 Spring父配置文件，用`@Import`注解导入子配置文件：
```
@Configuration  // 指定该类为Spring的配置类，在创建AnnotationApplicationContext时指定过，可以省略@Configuration
@ComponentScan("pers.cris") // 指定创建核心容器时要扫描注解的包
@Import(SpringConfiguration2.class) // 导入子配置文件
public class SpringConfiguration { }
```

 Spring子配置文件：
```
//@Configuration    // 可以省略@Configuration，会由父配置文件导入
public class SpringConfiguration2 {
    @Bean(name = "runner")  // 将方法的返回值Bean添加到核心容器中，并指定其在核心容器中的id
    @Scope("prototype") // 指定QueryRunner为多例的
    public QueryRunner createQueryRunner(DataSource dataSource) {   // 方法有参数时，会自动到核心容器中查找，查找方式与@Autowired相同
        return new QueryRunner(dataSource);
    }

    @Bean(name = "dataSource")
    public DataSource createDataSource() {
        ComboPooledDataSource ds = new ComboPooledDataSource();
        try {
            ds.setDriverClass("com.mysql.jdbc.Driver");
            ds.setJdbcUrl("jdbc:mysql://localhost:3306/mydb?serverTimezone=Asia/Shanghai");
            ds.setUser("Cris");
            ds.setPassword("123");
        } catch (Exception e) {
            throw new RuntimeException();
        }
        return ds;
    }
}
```

###### @PropertySource注解和SpEL表达式 ######

- 上面的子配置文件中，JDBC四大参数还是写死了，应该写在配置文件中，再由Spring来进行注入
 - **给配置类加上`@PropertySource`注解，其`value`属性指定配置文件位置**（关键字`classpath`可以表示类路径）
 - 在配置类中定义JDBC四大参数的属性，再用`@Value`注解配合SpEL表达式来进行注入

 Resource目录下的jdbcConfig.properties配置文件：
```
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/mydb?serverTimezone=Asia/Shanghai
jdbc.user=Cris
jdbc.password=123
```

 配置类上用`@PropertySource`指定配置文件的位置：
```
@Configuration    // 可以省略@Configuration，会由父配置文件导入
@PropertySource("classpath:jdbcConfig.properties")	// 指定配置文件的位置
public class SpringConfiguration2 {
    @Value("${jdbc.driver}")	// 配合SpEL表达式来进行注入
    private String driverClass;

    @Value("${jdbc.url}")
    private String jdbcUrl;

    @Value("${jdbc.user}")
    private String user;

    @Value("${jdbc.password}")
    private String password;

    @Bean(name = "runner")
    @Scope("prototype")
    public QueryRunner createQueryRunner(DataSource dataSource) {
        return new QueryRunner(dataSource);
    }

    @Bean(name = "dataSource")
    public DataSource createDataSource() {
        ComboPooledDataSource ds = new ComboPooledDataSource();
        try {
            ds.setDriverClass(driverClass);
            ds.setJdbcUrl(jdbcUrl);
            ds.setUser(user);
            ds.setPassword(password);
        } catch (Exception e) {
            throw new RuntimeException();
        }
        return ds;
    }
}
```

---

- Spring选择基于XML开发还是选择基于注解开发？
 - 一般来说，**自定义的类用注解配置较为方便，jar包中的类由于无法添加注解，因此用XML较为方便，可以采用XML和注解相结合的方式**

---

### Spring和junit的整合 ###

- 测试时，每次都需要创建核心容器，并从核心容器中获取Bean对象（例如accountService），虽然可以像以前一样用junit的`@Before`来完成，但测试工程师不一定会Spring的操作，那么能不能**在测试类中将要从核心容器中获取的Bean对象注入进来**呢？
 - 问题在于，**junit**的测试方法执行时，会调用它集成的main方法，而该main方法中**不可能会有创建Spring核心容器的操作，也不知道Spring配置文件或配置类的位置，因此无法注入**，这就**需要更换运行器Runner**（带有main方法的类）
- 步骤：
 1. pom.xml中导入**spring-test**的依赖，同时当Spring的版本为5.x时，junit的版本需要是4.12及以上：
```
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>5.2.6.RELEASE</version>
            <scope>test</scope>
        </dependency>
```
 2. 在测试类上添加**`@RunWith`注解，用于更换运行器Runner**，其`value`属性指定为`SpringJUnit4ClassRunner.class`
 3. 在测试类上添加**`@ContextConfiguration`注解，用于指定Spring配置文件位置或配置类字节码文件**，其`classes`属性可以指定配置类字节码文件，`locations`属性可以指定配置文件位置
 4. 在测试类中将要获取的Bean通过Spring注入的方式获取
```
@RunWith(SpringJUnit4ClassRunner.class) // 更换运行器
@ContextConfiguration(classes = SpringConfiguration.class)  // 指定Spring配置文件位置或配置类字节码文件
public class TestAccountService {

    @Autowired	// 通过Spring进行注入
    private IAccountService accountService;

    @Test
    public void testFindAll() {
//        ApplicationContext ac = new AnnotationConfigApplicationContext(SpringConfiguration.class);
//        IAccountService accountService = ac.getBean("accountService", IAccountService.class);

        List<Account> accounts = accountService.findAll();
        for (Account account : accounts)
            System.out.println(account);
    }

	...
}
```

---

#### 事务控制问题 ####

- 转账业务暴露出的事务控制问题

 AccountServiceImpl中的转账业务：
```
public class AccountServiceImpl implements IAccountService {
    // 使用IoC来注入accountDao
    private IAccountDao accountDao;

    // 给要注入的属性提供set方法
    public void setAccountDao(IAccountDao accountDao) {
        this.accountDao = accountDao;
    }

    @Override
    public void transfer(int sourceId, int targetId, double money) {
        // 获取转出账户和转入账户
        Account source = accountDao.findById(sourceId);
        Account target = accountDao.findById(targetId);
        // 转出账户扣钱，转入账户加钱
        source.setMoney(source.getMoney() - money);
        target.setMoney(source.getMoney() + money);
        // 更新转入账户和转出账户
        accountDao.update(source);

        int i = 1 / 0;  // 让程序在这里终止，则转出账户已扣钱，而转入账户没有加钱！

        accountDao.update(target);
    }

	...
}
```

- 分析可知，上述转账业务与数据库进行了4次交互（获取转出账户、获取转入账户、更新转出账户、更新转入账户），**为了保证事务的一致性，它们应该使用同一个Connection对象**，而accountDao中的QueryRunner却每次都会创建一个新的Connection对象
- 解决步骤：
 1. 要用到**ThreadLocal**来获取当前线程专属的Connection，创建一个**ConnectionUtils工具类**，用于获取或解除线程专属的Connection对象，应**注入DataSource，在当前线程无Connecetion绑定时用于获取新的Connecetion并绑定**：
```
public class ConnectionUtils {
    private final ThreadLocal<Connection> tl = new ThreadLocal<>();

    private DataSource dataSource;  // 连接池，需要注入

    public void setDataSource(DataSource dataSource) { this.dataSource = dataSource; }

    /**
     * 获取当前线程专属的Connection对象
     * @return 当前线程专属的Connection对象
     */
    public Connection getThreadConnection() {
        try {
            Connection conn = tl.get();
            if (conn == null) { // 若当前线程没有Connection对象，则通过dataSource创建一个，并将其添加到ThreadLocal中
                conn = dataSource.getConnection();
                tl.set(conn);
            }
            return conn;
        } catch (Exception e) {
            throw new RuntimeException("获取线程Connection失败");
        }
    }

    /**
     * 将当前线程与线程专属的Connection解绑
     */
    public void removeConnection() {
        if (tl.get() != null)
            tl.remove();
    }
}
```
 2. 要用到事务控制，创建一个**TransactionManager工具类**，用于实现事务的开启、提交、回滚、释放资源等操作，类中**应注入ConnectionUtils，用于获取当前线程专属的Connection**：
```
public class TransactionManager {
    private ConnectionUtils connectionUtils;    // Connection工具类，需要注入

    public void setConnectionUtils(ConnectionUtils connectionUtils) { this.connectionUtils = connectionUtils; }

    /**
     * 开启事务
     */
    public void beginTransaction() {
        try {
            connectionUtils.getThreadConnection().setAutoCommit(false);
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }

    /**
     * 提交事务
     */
    public void commitTransaction() {
        try {
            connectionUtils.getThreadConnection().commit();
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }

    /**
     * 回滚事务
     */
    public void rollbackTransaction() {
        try {
            connectionUtils.getThreadConnection().rollback();
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }

    /**
     * 释放资源
     */
    public void release() {
        try {
            connectionUtils.getThreadConnection().close();  // close()只是将Connection关闭并归还给连接池
            connectionUtils.removeConnection(); // 要将当前线程与该Connection解绑，否则下次当前线程拿到的会是已关闭的Connection
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }
}
```
**注意：释放资源时，close()方法只是将连接关闭并归还给连接池，还要将当前线程与该连接解绑，否则当前线程下次获取连接时，会从ThreadLocal中获取到这个已关闭的连接**
 3. **DAO层应注入ConnectionUtils，用于给每条SQL语句都指定使用当前线程的专属Connection对象**：
```
public class AccountDaoImpl implements IAccountDao {
    // 需要注入的属性
    private QueryRunner runner;
    private ConnectionUtils connectionUtils;    // DAO在执行SQL语句时要指定使用当前线程专属的Connection

    public void setRunner(QueryRunner runner) { this.runner = runner; }

    public void setConnectionUtils(ConnectionUtils connectionUtils) { this.connectionUtils = connectionUtils; }

    @Override
    public Account findById(int id) {
        try {
            return runner.query(connectionUtils.getThreadConnection(),
                    "SELECT * FROM tb_account WHERE id = ?;", new BeanHandler<>(Account.class), id);	// SQL语句指定使用当前线程专属的Connection
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }

    @Override
    public void update(Account account) {
        try {
            runner.update(connectionUtils.getThreadConnection(),
                    "UPDATE tb_account SET money = ?, uid = ? WHERE id = ?;",
                    account.getMoney(), account.getUid(), account.getId());
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

	...
}
```
 4. 配置bean.xml进行注入，**由于Connection是从ConnectionUtils中获取，并在SQL语句中进行指定，因此QueryRunner中不用再注入DataSource**：
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- 配置Service -->
    <bean id="accountService" class="pers.cris.service.impl.AccountServiceImpl">
        <property name="accountDao" ref="accountDao"/>
        <property name="transactionManager" ref="transactionManager"/>  <!-- 注入TransactionManager -->
    </bean>

    <!-- 配置DAO -->
    <bean id="accountDao" class="pers.cris.dao.impl.AccountDaoImpl">
        <property name="runner" ref="runner"/>
		<property name="connectionUtils" ref="connectionUtils"/>
    </bean>

    <!-- 配置TransactionManager -->
    <bean id="transactionManager" class="pers.cris.utils.TransactionManager">
        <property name="connectionUtils" ref="connectionUtils"/>
    </bean>

    <!-- 配置ConnectionUtils -->
    <bean id="connectionUtils" class="pers.cris.utils.ConnectionUtils">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!-- 配置QueryRunner，在QueryRunner中不用再配置DataSource -->
    <bean id="runner" class="org.apache.commons.dbutils.QueryRunner" scope="prototype"/>

    <!-- 配置数据源DataSource -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <!-- 注入JDBC四大参数 -->
        <property name="driverClass" value="com.mysql.jdbc.Driver"/>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/mydb?serverTimezone=Asia/Shanghai"/>
        <property name="user" value="Cris"/>
        <property name="password" value="123"/>
    </bean>
</beans>
```
 4. 测试
```
    /**
     * 测试Service层的transfer()方法（会抛异常，看能否回滚）
     */
    @Test
    public void testTransfer() {
        ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
        IAccountService accountService = ac.getBean("accountService", IAccountService.class);

        accountService.transfer(2, 1, 100);
    }
```
结果：回滚成功，事务控制成功

- 上述解决方案可以实现事务控制，但过于繁琐，且仍然存在**方法间的依赖（改变一个方法名，则其他调用该方法的位置都需要修改）**

---

#### 动态代理 ####

- **动态代理的作用：在不修改源码的基础上，对方法进行增强**
- 动态代理的特点：字节码随用随创建，随用随加载
- 动态代理分为：
 - **基于接口的动态代理：需要被代理类至少有一个实现接口**
 - **基于子类的动态代理：被代理类可以没有实现接口**

###### 基于接口的动态代理 ######

 1. 被代理类FactoryImpl，实现了接口IFactory：
```
public class FactoryImpl implements IFactory {
    public void produce(String goods, float price) { System.out.println("生产" + goods + "，成本为" + price); }

    public void sale(String goods, float price) { System.out.println("销售" + goods + "，成本为" + price); }
}
```
 2. 基于接口动态代理过程：
```
    public static void main(String[] args) {
        // 被代理对象
        final FactoryImpl factory = new FactoryImpl();  // 由于内部类要访问factory，因此需要final

        // 代理对象，通过Proxy.newInstance()方法创建（参数分别是：类加载器、被代理对象实现的接口、InvocationHandler实现类）
        IFactory proxyFactory = (IFactory) Proxy.newProxyInstance(FactoryImpl.class.getClassLoader(),
                factory.getClass().getInterfaces(),
                new InvocationHandler() {   // 匿名内部类实现InvocationHandler
                    /**
                     * @param proxy     代理对象的引用（一般用不上）
                     * @param method    调用的方法
                     * @param args      调用方法的参数
                     * @return          和被代理对象有相同的返回值
                     */
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        // 增强代码（price乘以0.8）
                        float price = (Float) args[1];
                        price *= 0.8f;

                        return method.invoke(factory, args[0], price);  // 是被代理对象factory调用方法，而不是代理对象proxy
                    }
                });

        proxyFactory.produce("运动鞋", 500);	// 生产运动鞋，成本为400.0
        proxyFactory.sale("运动鞋", 1000);	// 销售运动鞋，成本为800.0
    }
```

- 注意事项：
 1. **代理对象的类型应该是被代理对象实现的接口的类型，才能调用该接口的方法**
 2. `Proxy.newInstance()`方法的参数依次是：类加载器（用被代理类的类加载器，是固定写法）、接口数组（被代理类实现的接口数组，是固定写法）、InvocationHandler接口的实现类（一般用匿名内部类的形式，要实现invoke()方法）
 3. InvocationHandler接口的invoke()方法的参数含义依次是：`Object proxy`是代理对象的引用（一般用不上）、`Method method`是当前执行的方法、`Object[] args`是当前执行方法的参数
 4. InvocationHandler接口的invoke()方法的返回值是通过反射调用被代理对象的method方法的返回值，如`return method.invoke(factory, args);`，**注意是被代理对象factory调用method方法，而不是代理对象proxy调用**。同时，**内部类中引用外部类的成员时，该成员需要加final**

###### 基于子类的动态代理 ######

 1. 需要在pom.xml中**添加cglib的依赖**
 2. 被代理类Factory，没有实现任何接口：
```
public class Factory {
    public void produce(String goods, float price) { System.out.println("生产" + goods + "，成本为" + price); }

    public void sale(String goods, float price) { System.out.println("销售" + goods + "，成本为" + price); }
}
```
 3. 基于子类的动态代理过程：
```
    public static void main(String[] args) {
        // 被代理对象
        final Factory factory = new Factory();  // 由于内部类要访问factory，因此需要final

        // 被代理对象，用Enhancer.create()方法创建（参数分别是：被代理对象的字节码、Callback的实现类，一般实现其子接口MethodInterceptor）
        Factory proxyFactory = (Factory) Enhancer.create(Factory.class, new MethodInterceptor() {
            /**
             * @param o             代理对象的引用（一般不用）
             * @param method        当前执行的方法
             * @param objects       当前执行方法的参数
             * @param methodProxy   当前执行方法的代理（一般不用）
             * @return 和被代理对象执行方法中的返回值相同
             */
            @Override
            public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
                // 增强代码（price乘以0.9）
                float price = (Float) objects[1];
                price *= 0.8f;

                return method.invoke(factory, objects[0], price);   // 被代理对象调用method方法，并指定参数
            }
        });

        proxyFactory.produce("篮球鞋", 1000);	// 生产篮球鞋，成本为900.0
        proxyFactory.sale("篮球鞋", 1500);	// 销售篮球鞋，成本为1350.0
    }
```

- 注意事项：
 - **代理对象的类型应该和被代理对象相同**
 - Enhancer.create()方法的参数依次是：字节码（被代理对象的字节码，固定写法）、Callback接口的实现类（一般写其子接口MethodInterceptor的实现类）