<link href="http://cdn.bootcss.com/highlight.js/8.0/styles/monokai_sublime.min.css" rel="stylesheet">  
<script src="http://cdn.bootcss.com/highlight.js/8.0/highlight.min.js"></script>  
<script>hljs.initHighlightingOnLoad();</script>

# 框架 #

- **框架就是软件开发中的一套解决方案**，不同的框架可以解决不同的问题，它是半成品，相当于组件
- 使用框架的好处：框架**封装了许多细节**，使得开发人员可以以更简便的方式实现功能，**提高了开发效率**
- **三层架构：**
 - **表现层：用于展示数据**
 - **业务层：用于处理需求**
 - **持久层：用于与数据库的交互**
- SSM框架：
![](.\MyPic\SSM.bmp)

## MyBatis ##

- 之前持久层技术的解决方案：
 - JDBC技术：Connection、PreparedStatement、ResultSet
 - Spring的JdbcTemplate：它是Spring中对JDBC技术的简单封装
 - Apache的DBUtils：它也是对JDBC技术的简单封装
 - 但以上这些都不是框架，**JdbcTemplate和DBUtils都只是工具类**

### MyBatis概述 ###

- MyBatis是一个**持久层框架**，是用Java编写的
- MyBatis**封装了JDBC操作的许多细节，使得开发者只需要关注SQL语句本身**，而不用关注驱动注册、创建连接等繁杂过程
- **使用了ORM（Object Relational Mapping，对象关系映射）思想实现结果集的封装**
 - ORM思想：Object Relational Mapping，对象关系映射，就是**把数据库表和实体类及其属性对应起来**，使得通过操作实体类，就可以操作数据库表

### MyBatis入门（IDEA） ###

#### MyBatis环境搭建（基于XML配置） ####

1. **创建Maven工程**，并**添加MyBatis、MySQL、log4j、junit的依赖**，若遇到`Error:java:不再支持源选项5。请使用6或更高版本`错误，则再在pom.xml中加上如下信息：
```
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.encoding>UTF-8</maven.compiler.encoding>
        <java.version>11</java.version>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
    </properties>
```
2. 创建JavaBean对象和DAO接口
3. 在`src > main > resources`中**创建MyBatis的主配置文件**，一般命名为**sqlMapConfig.xml**，内容示例如下：
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!-- MyBatis的主配置文件 -->
<configuration>
    <!-- 配置环境 -->
    <environments default="mysql">	<!-- default的值必须是下面id的其中一个 -->
        <!-- 配置MySQL的环境 -->
        <environment id="mysql">
            <!-- 配置事务的类型 -->
            <transactionManager type="JDBC"/>
            <!-- 配置数据源（连接池） -->
            <dataSource type="POOLED">
                <!-- 配置连接数据库的四大参数 -->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatisdb?serverTimezone=Asia/Shanghai"/>
                <property name="username" value="Cris"/>
                <property name="password" value="123"/>
            </dataSource>
        </environment>
    </environments>

    <!-- 指定映射配置文件的位置，映射配置文件指的是每个DAO独立的配置文件 -->
    <mappers>
        <mapper resource="pers\cris\dao\UserDaoInterf.xml"/>
    </mappers>
</configuration>
```
4. 在`src > main > resource`中的与DAO接口相对应目录下**创建映射配置文件**，如`UserDaoInterf.xml`，内容示例如下：
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="pers.cris.dao.UserDaoInterf">    <!-- namespace是该接口的全限定类名 -->
	<!-- select表示要执行的是查询操作，id是UserDaoInterf中的方法名，resultType指明要将结果封装成什么对象 -->
    <select id="findAll" resultType="pers.cris.domain.User">
        SELECT * FROM users;    <!-- 该方法的SQL语句 -->
    </select>
</mapper>
```
5. 在`src > main > resources`下创建**`log4j.properties`**，其内容如下：
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


###### MyBatis环境搭建的注意事项 ######

 1. **在MyBatis中，`Mapper`就是`Dao`的意思**，因此`UserDaoInterf.java`和`UserDaoInterf.xml`通常会写成`UserMapperInterf.java`和`UserMapperInterf.xml`
 2. **MyBatis的映射配置文件的位置必须和DAO接口包结构相同（只不过一个在resource目录下，一个在java目录下）（注意：IDEA中创建Diretory时名字要写`pers\cris\dao`，才会创建多级目录，若写`pers.cris.dao`可能创建的是单级目录，建议Show In Explorer确认一下）**
 3. **MyBatis的映射配置文件中`<mapper>`标签的`namespace`属性值必须是DAO接口的全限定类名**，如`<mapper namespace="pers.cris.dao.UserDaoInterf">...</mapper>`
 4. **MyBatis的映射配置文件中的操作配置的`id`属性值必须是DAO接口的方法名**，如`<select id="findAll">SELECT * FROM users;</select>`
 5. **遵循2~4后，开发中无需再写DAO的实现类**，MyBatis会自动创建实现类

## 实体类属性名和数据库列名对应的情况 ##

#### MyBatis入门案例（查询所有） ####

- 按照“MyBatis环境搭建”中的步骤搭建好MyBatis环境后，做入门案例，从数据库中查询所有信息，步骤：
 1. **读取MyBatis的主配置文件`sqlMapConfig.xml`**（主配置文件中有JDBC四大参数和映射配置文件位置）
 2. 利用主配置文件**创建SqlSessionFactory工厂类**
 3. 使用工厂类**生产SqlSession对象**
 4. 使用SqlSession对象可以**创建指定DAO接口的实现类对象（代理对象）**
 5. **使用DAO接口的代理对象执行方法**（映射配置文件中有DAO接口中每个方法的SQL语句以及要将结果封装成的类型）
 6. **释放资源**

```
    /**
     *  测试findAll()方法，查询出数据库中的所有User信息
     */
    @Test
    public void testAll() throws IOException {
        // 1. 读取MyBatis的主配置文件sqlMapConfig.xml（主配置文件中JDBC四大参数和映射配置文件位置）
        InputStream in = Resources.getResourceAsStream("sqlMapConfig.xml");

        // 2. 创建SqlSessionFactory工厂
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        SqlSessionFactory factory = builder.build(in);

        // 3. 使用工厂生产SqlSession对象
        SqlSession session = factory.openSession();

        // 4. 使用SqlSession创建DAO接口的代理对象（通过映射配置文件可以自动创建DAO接口的实现类）
        UserDaoInterf userDao = session.getMapper(UserDaoInterf.class);

        // 5. 使用DAO接口的代理对象执行方法（映射配置文件中有每个方法的SQL语句和要将结果封装成的类型）
        List<User> res = userDao.findAll();
        for (User user : res)
            System.out.println(user);

        // 6. 释放资源
        session.close();
        in.close();
    }
```

###### 入门案例分析 ######

- 创建SqlSessionFactory工厂时，不是自己创建，而是通过SqlSessionFactoryBuilder构建者来创建，使用了**构建者模式，好处是将对象创建的细节隐藏，直接调用方法即可得到对象**
- 创建SqlSession对象时，不是自己创建，而是通过SqlSessionFactory工厂类来获取，使用了**工厂模式，好处是解耦，即降低了类之间的依赖关系**
- 创建DAO接口的实现类时，不是自己实现，而是通过`session.getMapper(Class)`，使用了**代理模式，好处是在不修改源码的基础上对已有方法进行增强**
- MyBatis会用一个Mapper对象存储方法的SQL语句和返回值类型，再**用Map存储所有Mapper，其中key是`DAO接口的全限定类名.方法名`，value是Mapper（包含该方法的SQL语句和返回值类型），再将JDBC四大参数和这个Map存到一个Configuration对象中**

#### MyBatis基于注解配置 ####

- MyBatis基于注解开发的步骤和基于XML开发的不同：
 - **不需要映射配置文件**
 - **主配置文件`sqlMapConfig.xml`中的`<mapper>`里应配置`class`属性**而不是`resource`属性，如：
```
    <!-- 指定映射配置文件的位置，映射配置文件指的是每个DAO独立的配置文件
		 若是基于注解来配置，则应使用class属性指定DAO接口的全限定类名 -->
    <mappers>
        <!-- <mapper resource="pers\cris\dao\UserDaoInterf.xml"/>     基于XML -->
        <mapper class="pers.cris.dao.UserDaoInterf"/>   <!-- 基于注解 -->
    </mappers>
```
 - **DAO接口的方法上应加上SQL语句的注解**，如：
```
public interface UserDaoInterf {
        /**
         * 查询所有用户
         * @return 存储着所有用户的List
         */
        @Select("SELECT * FROM users")  // 基于注解
        List<User> findAll();
}
```

- 除了支持基于XML配置和基于注解配置外，MyBatis也支持自己创建DAO的实现类对象，再往实现类中传入SqlSessionFacory，调用SqlSession的`selectList(DAO的全限定类名.方法名)`等方法（参数就是能获取配置信息的key）来与数据库进行交互，但这样做繁琐且无意义

#### 保存操作 ####

1. UserDaoInterf接口中添加方法声明：
```
    /**
     * 往数据库中保存用户
     * @param user 要保存的用户
     */
    void saveUser(User user);
```
2. 映射配置文件UserDaoInterf.xml中添加映射配置：
```
<mapper namespace="pers.cris.dao.UserDaoInterf">    <!-- namespace是该接口的全限定类名 -->
	...
    <!-- insert表示要执行的是插入操作，id是方法名，parameterType指明参数的类型 -->
    <insert id="saveUser" parameterType="pers.cris.domain.User">
        INSERT INTO users(username, sex, address) VALUES(#{username}, #{sex}, #{address});
        <!-- #{JavaBean属性}，会从参数对象中取出JavaBean属性值作为SQL语句的参数 -->
    </insert>
</mapper>
```
3. 测试：
```
public class MyBatisTest2 {
    private InputStream in;
    private SqlSession session;
	private UserDaoInterf userDao;

    /**
     *  初始化操作，即读取MyBatis主配置文件，创建SqlSession对象
     */
    @Before // 在测试方法执行前调用该方法
    public void init() throws IOException {
        in = Resources.getResourceAsStream("sqlMapConfig.xml");
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(in);
        session = factory.openSession();
		userDao = session.getMapper(UserDaoInterf.class);
    }

    /**
     * 收尾操作，即提交事务和释放资源
     */
    @After  // 在测试方法执行后调用该方法
    public void destroy() throws IOException {
        // 注意！MyBatis中默认开启事务，需要手动提交，否则事务会回滚，同时，commit()会自动调用close()
        session.commit();
//        session.close();
        in.close();
    }

    /**
     * 测试saveUser()方法
     */
    @Test
    public void testSaveUser() {
        User user = new User();
        user.setUsername("MyBatis_add_user3");
        user.setSex("female");
        user.setAddress("Portugal");

        userDao.saveUser(user);
    }
}
```

###### 注意事项 ######

1. 在映射配置文件中进行配置时，
```
    <insert id="saveUser" parameterType="pers.cris.domain.User">
        INSERT INTO users(username, sex, address) VALUES(#{username}, #{sex}, #{address});
    </insert>
```
其中**parameterType属性是该方法参数的全限定类名，而`#{...}`可以将该参数的JavaBean对象取出来作为SQL语句的参数，`#{}`里面的必须是JavaBean属性（以get、set方法名为准）**
2. **MyBatis默认开启事务，因此完成增删改操作后需要执行`session.commit()`来提交事务，提交时会自动调用`session.close()`释放资源，而若直接`session.close()`，则事务会回滚，更新无效**

#### 更新操作 ####

1. 在UserDaoInterf接口中添加方法声明
2. 在映射配置文件UserDaoInterf.xml中添加映射配置：
```
<mapper namespace="pers.cris.dao.UserDaoInterf">    <!-- namespace是该接口的全限定类名 -->
	...
    <update id="updateUser" parameterType="pers.cris.domain.User">
        UPDATE users SET username = #{username}, sex = #{sex}, address = #{address} WHERE id = #{id};
    </update>
</mapper>
```
3. 测试：
```
    /**
     * 测试updateUser()方法
     */
    @Test
    public void testUpdateUser() {
        User user = new User();
        user.setId(7);
        user.setUsername("MyBatis_update_user");
        user.setSex("female");
        user.setAddress("no address");

        userDao.updateUser(user);
    }
```

#### 删除操作 ####

1. 在UserDaoInterf接口中添加方法声明
2. 在映射配置文件UserDaoInterf.xml中添加映射配置：
```
<mapper namespace="pers.cris.dao.UserDaoInterf">    <!-- namespace是该接口的全限定类名 -->
	...
    <delete id="deleteUser" parameterType="java.lang.Integer">
        DELETE FROM users WHERE id = #{uid};    <!-- 当参数只有一个时，#{}里可以是任意内容，充当占位符即可 -->
    </delete>
</mapper>
```
3. 测试：
```
    /**
     * 测试deleteUser()方法
     */
    @Test
    public void testDeleteUser() {
        userDao.deleteUser(10);
    }
```

###### 注意事项 ######

1. 在映射配置文件中进行配置时，
```
	<delete id="deleteUser" parameterType="java.lang.Integer">
        DELETE FROM users WHERE id = #{uid};    <!-- 当参数只有一个时，#{}里可以是任意内容，充当占位符即可 -->
    </delete>
```
parameterType属性既可以是`java.lang.Integer`，也可以是`int`或`Integer`，且**SQL语句只有一个参数，则`#{}`中可以是任意内容，只充当占位符**

#### 查询单个操作 ####

1. 在UserDaoInterf接口中添加方法声明
2. 在映射配置文件UserDaoInterf.xml中添加映射配置：
```
<mapper namespace="pers.cris.dao.UserDaoInterf">    <!-- namespace是该接口的全限定类名 -->
	...
    <select id="findById" parameterType="int" resultType="pers.cris.domain.User">
        SELECT * FROM users WHERE id = #{uid};  <!-- 参数只有一个，只充当占位符 -->
    </select>
</mapper>
```
3. 测试：
```
    /**
     * 测试findById()方法
     */
    @Test
    public void testFindById() {
        User user = userDao.findById(1);
        System.out.println(user);
    }
```

#### 模糊查询 ####

1. 在UserDaoInterf接口中添加方法声明
2. 在映射配置文件UserDaoInterf.xml中添加映射配置：
```
<mapper namespace="pers.cris.dao.UserDaoInterf">    <!-- namespace是该接口的全限定类名 -->
	...
    <select id="findByName" parameterType="String" resultType="pers.cris.domain.User">
        <!-- resultType是每行结果封装成的对象（User），而不是所有行（List<User>） -->
        SELECT * FROM users WHERE username LIKE #{name};    <!-- 参数只有一个，只充当占位符 -->
    </select>
</mapper>
```
3. 测试：
```
    /**
     * 测试findByName()方法
     */
    @Test
    public void testFindByName() {
        List<User> userList = userDao.findByName("%MyBatis%");   // 注意：通配符要配在参数里，因为SQL语句模板中没有配置
        for (User user : userList)
            System.out.println(user);
    }
```

###### 注意事项 ######

1. **当查询多行多列时，映射配置文件中的resultType参数应是单行结果封装成的对象，而不是所有行结果封装成的集合对象**（如findAll()方法的resultType参数应是`pers.cris.domain.User`，而不是`List<pers.cris.domain.User>`）
```
	<select id="findAll" resultType="pers.cris.domain.User">
        SELECT * FROM users;
    </select>
```
2. **在模糊查询时，一般将通配符配在参数中，这样会使用PreparedStatement的占位符，较为安全，也可以将通配符配在SQL语句中，但这种方式用的是Statement的字符串拼接，较不安全**，了解一下即可：
```
    <select id="findByName" parameterType="String" resultType="pers.cris.domain.User">
		SELECT * FROM users WHERE username LIKE #{name};    <!-- 通配符配在参数中 -->
        <!-- SELECT * FROM users WHERE username LIKE '%${name}%';    通配符配在SQL模板中 -->
    </select>
```
```
	userDao.findByName("%MyBatis%");	// 通配符配在参数中
	// userDao.findByName("MyBatis");	// 通配符配在SQL模板中
```

#### 查询一行一列（聚合函数） ####

1. 在UserDaoInterf接口中添加方法声明
2. 在映射配置文件UserDaoInterf.xml中添加映射配置：
```
<mapper namespace="pers.cris.dao.UserDaoInterf">    <!-- namespace是该接口的全限定类名 -->
	...
    <select id="getTotal" resultType="int">
        SELECT COUNT(*) FROM users;
    </select>
</mapper>
```
3. 测试：
```
    /**
     * 测试getCount()方法
     */
    @Test
    public void testGetCount() {
        int total = userDao.getTotal();
        System.out.println("total = " + total);
    }
```

#### 保存User并将自增长的主键赋值给User ####

- **在保存User到数据库时，由于主键id是自增长的，因此我们不会指定id，但保存后想知道该User在数据库中的id，就可以利用SQL的`SELECT last_insert_id()`语句，配合映射配置文件中的`<selectKey>`标签，在保存User后将数据库中自增长的主键赋回给User**，步骤：
1. 在UserDaoInterf接口中添加方法声明
2. 在映射配置文件UserDaoInterf.xml中添加映射配置：
```
<mapper namespace="pers.cris.dao.UserDaoInterf">    <!-- namespace是该接口的全限定类名 -->
	...
    <insert id="saveUser2" parameterType="pers.cris.domain.User">
        <!-- 配置在保存User时，将数据库自增长的id赋给User -->
        <!-- keyProperty是实体类的属性名，keyColumn是数据库的列名，order="AFTER"表示在保存操作之后执行 -->
        <selectKey keyProperty="id" keyColumn="id" resultType="int" order="AFTER">
            SELECT LAST_INSERT_ID();    <!-- SQL语言提供的语句 -->
        </selectKey>
        INSERT INTO users(username, sex, address) VALUES(#{username}, #{sex}, #{address});
    </insert>
</mapper>
```
3. 测试：
```
    /**
     * 测试saveUser2()方法，保存User，并将数据库自增长的id赋给User
     */
    @Test
    public void testSaveUser2() {
        User user = new User();
        user.setUsername("insert_user");
        user.setSex("male");
        user.setAddress("xxx");

        System.out.println("保存操作之前：" + user);   // 保存操作之前：User{id=0, username='insert_user', sex='male', address='xxx'}
        userDao.saveUser2(user);
        System.out.println("保存操作之后：" + user);   // 保存操作之后：User{id=10, username='insert_user', sex='male', address='xxx'}
    }
```

#### 利用自定义的包装对象作为SQL语句参数 ####

- **映射配置文件中，`parameterType`属性只能指定一个，即只能有一个参数，若有多个参数，可以将它们包装到一个包装类中（也需要满足JavaBean规范）来作为SQL语句的参数**，例如查询username中包含"MyBatis"或包含"wang"的User：
1. 自定义查询包装类QueryVo，有两个JavaBean属性user1和user2
2. 在UserDaoInterf接口中添加方法声明
2. 在映射配置文件UserDaoInterf.xml中添加映射配置：
```
<mapper namespace="pers.cris.dao.UserDaoInterf">    <!-- namespace是该接口的全限定类名 -->
	...
    <select id="findByQueryVo" resultType="pers.cris.domain.User" parameterType="pers.cris.domain.QueryVo">
        SELECT * FROM users WHERE username Like #{user1.username} OR username LIKE #{user2.username};
        <!-- 符合JavaBean规范就可以一直往里取 -->
    </select>
</mapper>
```
3. 测试：
```
    /**
     * 测试findByQueryVo()方法
     */
    @Test
    public void testFindByQueryVo() {
        User user1 = new User();
        user1.setUsername("%MyBatis%");

        User user2 = new User();
        user2.setUsername("%wang%");

        QueryVo queryVo = new QueryVo();
        queryVo.setUser1(user1);
        queryVo.setUser2(user2);

        List<User> list = userDao.findByQueryVo(queryVo);
        for (User user : list)
            System.out.println(user);
    }
```
---

### 实体类属性名和数据库列名不一致的情况 ###

- 上面的映射配置文件中，例如
```
    <select id="findAll" resultType="pers.cris.domain.User">
        SELECT * FROM users;
    </select>
```
之所以能将数据库查询到的数据封装到User对象中，是因为User的属性名和数据库中的列名是一致的，但若不一致怎么办？
- **若实体类属性名和数据库列名不一致，有两种解决方案：**
 - **修改SQL语句，使用别名，使得查询结果的列名与属性名一致（效率高，但写起来麻烦）**
 - **映射配置文件中使用`<resultMap>`标签进行约定（写起来简单，但要解析XML，效率不如别名）**

例如：
- Student（sid、sname、gender、location）是属性名与数据库users表的列名（id、username、sex、address）不一致的实体类

#### 解决方案一：修改SQL语句，使用别名，使得查询结果的列名与属性名一致 ####

- 映射配置文件StudentDaoInterf.xml中的配置如下：
```
<mapper namespace="pers.cris.dao.StudentDaoInterf">
	<!-- 解决方案一：使用别名，使得查询结果的列名与属性名一致 -->
    <select id="findAll" resultType="pers.cris.domain.Student">
        SELECT id sid, username sname, sex gender, address location FROM users; 
    </select>
</mapper>
```

#### 解决方案二：映射配置文件中使用`<resultMap>`标签进行约定 ####

- 映射配置文件StudentDaoInterf.xml中的配置如下：
```
<mapper namespace="pers.cris.dao.StudentDaoInterf">
    <!-- 解决方案二：使用resultMap标签进行对应 -->
    <!-- 配置查询结果的列名和实体类的属性名之间的对应关系，id是这个对应关系的标识，type指定要封装的实体类的全限定类名 -->
    <resultMap id="stuMap" type="pers.cris.domain.Student">
        <!-- 配置主键的对应关系 -->
        <id property="sid" column="id"/
        <!-- 配置非主键的对应关系 -->
        <result property="sname" column="username"/>
        <result property="gender" column="sex"/>
        <result property="location" column="address"/>
    </resultMap>

    <select id="findByName" parameterType="String" resultMap="stuMap">    <!-- 使用resultMap属性（resultMap标签的id）指定封装对象及属性对应关系 -->
        SELECT * FROM users WHERE username LIKE #{name};
    </select>
```

---

#### `<typeAlias>`标签和`<package>`标签 ####

- 在映射配置文件中，resultType和parameterType属性，若是自定义的类，都必须写全限定类名，而int、String等类则不用，且不区分大小写，这是因为它们配置了别名
- 我们也**可以在主配置文件sqlMapConfig.xml的`<configuartion>`标签中通过`<typeAliases>`和`<typeAlias>`标签给自定义类配置别名**，起到简化代码的作用，**配置别名后映射配置文件的resultType和parameterType属性就可以直接写该类的别名，而不用再写包名，且不区分大小写**，例如：
```
<configuration>
	...
	<typeAliases>
		<typeAlias type="pers.cris.domain.Student" alias="Student"/>
	</typeAliases>
</configuration>
```
- **若实体类比较多，也可以在主配置文件sqlMapConfig.xml的`<configuartion>`标签中通过`<typeAliases>`和`<package>`标签给domain包中的所有类同时配置别名**，例如:
```
<configuration>
	...
	<typeAliases>
		<package name="pers.cris.domain"/>	<!-- 给该包下所有实体类都配置了别名 -->
	</typeAliases>
</configuration>
```
- **DAO接口较多时，在主配置文件sqlMapConfig.xml中配置映射配置文件的位置时，也可以在`<mappers>`标签中直接通过`<package>`标签指定DAO所在的包，进而可以自动找到映射配置文件位置，不用再写`<mapper>`标签**，例如：
```
<mappers>
	<package name="pers.cris.dao"/>	<!-- 该包下所有DAO都不需要再写mapper指定映射配置文件位置或全限定类名 -->
</mappers>
```
---

- 在主配置文件sqlMapConfig.xml中，可以引入外部配置文件来设置JDBC四大参数：
 - 资源路径（Resource目录）下的jdbcConfig.properties中的内容：
```
driver = com.mysql.jdbc.Driver
url = jdbc:mysql://localhost:3306/mybatisdb?serverTimezone=Asia/Shanghai
username = Cris
password = 123
```
 - 主配置文件sqlMapConfig.xml中的内容：
```
<configuration>
    <!-- 引入外部配置文件 -->
    <properties resource="jdbcConfig.properties"/>

    <!-- 配置别名 -->
    <typeAliases>
        <package name="pers.cris.domain"/>
    </typeAliases>

    <!-- 配置环境 -->
    <environments default="mysql">
        <environment id="mysql">
            <transactionManager type="JDBC"/>
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
        <package name="pers.cris.dao"/>
    </mappers>
</configuration>
```

---

### MyBatis中的连接池 ###

- 实际开发中都会使用数据库连接池，因为它可以减少获取数据库连接的时间
- MyBatis中提供了三种配置连接池的方式，可以在主配置文件sqlMapConfig.xml的`<dataSource>`标签中的type属性进行配置：
 - `<dataSource type="POOLED">...</dataSource>`：使用javax.sql.DataSource规范的连接池
 - `<dataSource type="UNPOOLED">...</dataSource>`：不使用连接池
 - `<dataSource type="JNDI">...</dataSource>`：使用服务器提供的JNDI技术实现连接池，来获取DataSource对象，不同的服务器拿到的DataSource对象不同，例如Tomcat拿到的是dbcp连接池

###### MyBatis中UNPOOLED的连接池的原理 ######

- 获取连接时，每次都用传统JDBC的方式创建一个连接并返回
- 连接用完后，直接关闭该连接释放资源

###### MyBatis中POOLED的连接池的原理 ######

- 配置`<dataSource>`标签的type属性值为POOLED时，用的连接池对象是PooledDataSource，可以进去看源码
- **连接池有空闲池idleConnections和活动池activeConnections，它们都是List集合，获取连接时**：
 - 若**空闲池**idleConnections**不为空**，则**从头部取出一个连接并返回**
 - 若**空闲池**idleConnections**为空**，则**判断活动池**activeConnections**的连接数是否已经达到设定的最大活动连接数**poolMaximumActiveConnections
 - 若activeConnections.size()<poolMaximumActiveConnections，则**用传统的JDBC获取连接的方式获取一个新的连接**，并**添加到活动池**activeConnections的**尾部并返回该连接**
 - 若activeConnections.size()>=poolMaximumActiveConnections，则**从活动池**activeConnections的**头部取出一个最久之前的连接**，清理之后**返回该连接**
- **连接用完后**，将其进行清理并**放回空闲池**idleConnections的**尾部**

#### SqlSession的自动提交 ####

- **SqlSession默认是关闭事务的自动提交的，因此增删改操作后需要调用commit()方法来提交事务，但是也可以在创建SqlSession对象时指定自动提交：`factory.openSession(true);`**
```
    /**
     * 测试SqlSession事务的自动提交
     */
    @Test
    public void testAutoCommit() throws IOException {
        InputStream in = Resources.getResourceAsStream("sqlMapConfig.xml");
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(in);

        SqlSession session = factory.openSession(true); // 设置自动提交事务

        UserDaoInterf userMapper = session.getMapper(UserDaoInterf.class);
        User user = new User();
        user.setUsername("auto_commit_user");
        userMapper.saveUser(user);

        session.close();    // 已经设置了自动提交，直接关闭session即可

        in.close();
    }
```
- 但设置自动提交后，就无法控制有多个连接的事务，因此更多的是采取手动提交

---

#### 动态SQL语句 ####

- 动态SQL语句可以实现映射配置文件中SQL语句的拼接，有三个标签：`<if>`、`<where>`、`<foreach>`

###### 动态SQL语句场景一：条件及条件个数不确定 ######

- 例如参数User中可能有username，也可能有sex，也可能两者都有或都没有，按照其给出的有的条件进行查询，映射配置文件中的SQL语句可以有如下两种写法：
 1. 用`<if>`标签
```
<mapper namespace="pers.cris.dao.UserDaoInterf">
	...
    <!-- 按条件查询，参数中的User可能有username条件，可能有sex条件，也可能二者都有或都没有 -->
    <select id="findByCondition" resultType="pers.cris.domain.User" parameterType="pers.cris.domain.User">
        SELECT * FROM users WHERE 1 = 1
        <if test="username != null">
            AND username = #{username}
        </if>
        <if test="sex != null">
            AND sex = #{sex}
        </if>
    </select>
</mapper>
```
 2. 用`<if>`标签和`<where>`标签
```
<mapper namespace="pers.cris.dao.UserDaoInterf">
	...
    <!-- 按条件查询，参数中的User可能有username条件，可能有sex条件，也可能二者都有或都没有 -->
    <select id="findByCondition" resultType="pers.cris.domain.User" parameterType="pers.cris.domain.User">
        SELECT * FROM users
        <where>
            <if test="username != null">
                AND username = #{username}
            </if>
            <if test="sex != null">
                AND sex = #{sex}
            </if>
        </where>
    </select>
</mapper>
```

 测试：
```
    /**
     * 测试findByCondition()方法
     */
    @Test
    public void testFindByCondition() {
        User user = new User();
        user.setUsername("zhangSan");

        List<User> list = userMapper.findByCondition(user); // 查出性别分别为"male"和"female的"两个"zhangSan"
        for (User u : list)
            System.out.println(u);

        System.out.println("---------------");

        user.setSex("female");
        list = userMapper.findByCondition(user);    // 只查出性别为"female"的"zhangSan"
        for (User u : list)
            System.out.println(u);
    }
```

###### 动态SQL语句场景二：IN语句 ######

- 例如`SELECT * FROM users WHERE id IN(...)`里面的条件不确定，可以使用`<foreach>`标签和自定义包装对象QueryVo，其中QueryVo中的idxes是存储着要查询的id的List，映射配置文件中的SQL语句可以写为：
```
<mapper namespace="pers.cris.dao.UserDaoInterf">
	...
    <!-- 查询id包含在queryVo.idxes中的所有用户的信息 -->
    <select id="findUserInIdxes" resultType="pers.cris.domain.User" parameterType="pers.cris.domain.QueryVo">
        SELECT * FROM users
        <where>
            <if test="idxes != null and !idxes.isEmpty()">
                <!-- 在open和close之间遍历collection，取出每个元素为item，元素间用seperator分隔 -->
                <foreach collection="idxes" open="id IN (" close=")" item="uid" separator=",">
                    #{uid}
                </foreach>
            </if>
        </where>
    </select>
</mapper>
```

 测试：
```
    /**
     * 测试findUserInIdxes()方法
     */
    @Test
    public void testFindUserInIdxes() {
        QueryVo queryVo = new QueryVo();
        List<Integer> list = List.of(1, 7, 12);
        queryVo.setIdxes(list);

        List<User> res = userMapper.findUserInIdxes(queryVo);
        for (User user : res)
            System.out.println(user);
    }
```

---

#### 抽取重复的SQL语句 ####

- 主配置文件sqlMapConfig.xml中，**可以用`<sql>`标签抽取SQL语句的共有部分，再用`<include>`标签进行引用**，例如有许多方法的SQL语句开头都是`SELECT * FROM users`，就可以将其抽取出来，以后要修改表名等，较为方便：
```
<mapper namespace="pers.cris.dao.UserDaoInterf">    <!-- namespace是该接口的全限定类名 -->
    <!-- 抽取重复的SQL语句，id属性为其唯一标识 -->
    <sql id="defaultSelectSql">
        SELECT * FROM users
    </sql>

    <select id="findBySex" resultType="pers.cris.domain.User" parameterType="String">
        <include refid="defaultSelectSql"/>    <!-- 引入抽取的SQL语句，refid为sql标签的id -->
        WHERE sex = #{str}
    </select>
	...
</mapper>
```

 测试：
```
    /**
     * 测试findBySex()方法
     */
    @Test
    public void testFindBySex() {
        List<User> list = userMapper.findBySex("male");
        for (User user : list)
            System.out.println(user);
    }
```
---

## MaBatis多表查询 ##

### 一对一关系（`<association>`标签+`javaType`属性） ###

- 一个账户Account对应一个账户所有人User
 - Account类中会有一个User属性
 - 账户表account_tb的uid是外键，指定用户表users中的主键id

#### 查询所有账户信息，同时包含账户所有人信息 ####

 映射配置文件AccountDaoInterf.xml中的配置应该如下：
```
<mapper namespace="pers.cris.dao.AccountDaoInterf">

    <!-- 定义封装Account以及Account中的User的resultMap -->
    <resultMap id="account_user" type="pers.cris.domain.Account">
        <id property="id" column="aid"/>
        <result property="money" column="money"/>
        <result property="uid" column="uid"/>
        <!-- 用association标签封装Account里的User对象，javaType指定封装成User类 -->
        <association property="user" javaType="pers.cris.domain.User">
            <id property="id" column="uid"/>
            <result property="username" column="username"/>
            <result property="address" column="address"/>
        </association>
    </resultMap>

    <select id="findAll" resultMap="account_user">	<!-- 用定义封装Account及User的resultMap来解析结果集 -->
        SELECT a.id aid, a.money money, a.uid uid, u.username, u.sex, u.address FROM account_tb a, users u WHERE a.uid = u.id;
    </select>
</mapper>
```

 测试accountMapper：
```
    /**
     * 测试AccountMapper的findAll()方法，查询所有账户信息，包含其所有人的信息
     */
    @Test
    public void testFindAll() {
        List<Account> accounts = accountMapper.findAll();
        for (Account account : accounts)
            System.out.println(account);
    }
```

### 一对多关系（`<collection>`标签+`ofType`属性） ###

- 一个用户User可能有多个账户Account
 - User类中会有一个存储该用户的账户信息的`List<Account>`
 - 账户表account_tb的uid是外键，指定用户表users中的主键id

#### 查询所有用户信息，同时包含每个用户的账号信息 ####

 映射配置文件UserDaoInterf.xml中的配置应该如下：
```
<mapper namespace="pers.cris.dao.UserDaoInterf">

    <!-- 定义封装User及User中的List<Account>的resultMap -->
    <resultMap id="user_accounts" type="pers.cris.domain.User">
        <id property="id" column="id"/>
        <result property="username" column="username"/>
        <result property="sex" column="sex"/>
        <result property="address" column="address"/>
        <!-- 用Collection标签封装User中的List<Account>集合，ofType属性指明集合中的元素类型 -->
        <collection property="accounts" ofType="pers.cris.domain.Account">
            <id property="id" column="aid"/>
            <result property="money" column="money"/>
            <result property="uid" column="id"/>
        </collection>
    </resultMap>

    <select id="findAll" resultMap="user_accounts">	<!-- 用定义封装User及List<Account>的resultMap来解析结果集 -->
        SELECT u.*, a.id aid, a.money money FROM users u LEFT OUTER JOIN account_tb a ON u.id = a.uid;
    </select>
</mapper>
```

 测试：
```
    /**
     * 测试UserMapper的findAll()方法，查询所有用户信息，包含其账户信息
     */
    @Test
    public void testFindAll() {
        List<User> users = userMapper.findAll();
        for (User user : users)
            System.out.println(user);
    }
```

### 多对多关系（`<collection>`标签+`ofType`属性） ###

- 一个学生Student可能有多个老师Teacher，一个老师也可能有多个学生
 - Student类中会有一个存储该学生所有老师信息的`List<Teacher>`
 - Teacher类中会有一个存储该老师所有学生信息的`List<Student>`
 - 多对多关系在数据库中需要中间表来表示，中间表stu_teacher_tb存储着学生和老师的对应关系

#### 查询所有学生信息，同时包含每个学生的教师信息 ####

 映射配置文件StudentDaoInterf.xml中的配置应该如下：
```
<mapper namespace="pers.cris.dao.StudentDaoInterf">
    <!-- 配置包含Student及其List<Teacher>的映射关系的resultMap -->
    <resultMap id="studentTeacherMap" type="pers.cris.domain.Student">
        <id property="sno" column="sno"/>
        <result property="sname" column="sname"/>
        <result property="gender" column="gender"/>
        <collection property="teachers" ofType="pers.cris.domain.Teacher">
            <id property="tno" column="tno"/>
            <result property="tname" column="tname"/>
            <result property="salary" column="salary"/>
        </collection>
    </resultMap>

    <select id="findAll" resultMap="studentTeacherMap">
        SELECT s.*, t.*
        FROM stu_tb s LEFT OUTER JOIN stu_teacher_tb st
        ON s.sno = st.sno
        LEFT OUTER JOIN teacher_tb t
        ON st.tno = t.tno;
    </select>
</mapper>
```
 
 测试：
```
    /**
     * 测试StudentMapper的findAll()方法
     */
    @Test
    public void testFindAll() {
        List<Student> students = studentMapper.findAll();
        for (Student student : students) {
            System.out.println("-------------学生信息-----------------");
            System.out.println("学生信息：" + student);
            System.out.println("该学生的教师信息：" + student.getTeachers());
        }
    }
```

#### 查询所有教师信息，同时包含每个教师的学生信息 ####

 映射配置文件TeacherDaoInterf.xml中的配置应该如下：
```
<mapper namespace="pers.cris.dao.TeacherDaoInterf">
    <!-- 配置包含Teacher及其List<Student>的映射关系的resultMap -->
    <resultMap id="teacherStuMap" type="pers.cris.domain.Teacher">
        <id property="tno" column="tno"/>
        <result property="tname" column="tname"/>
        <result property="salary" column="salary"/>
        <collection property="students" ofType="pers.cris.domain.Student">
            <id property="sno" column="sno"/>
            <result property="sname" column="sname"/>
            <result property="gender" column="gender"/>
        </collection>
    </resultMap>

    <select id="findAll" resultMap="teacherStuMap">
        SELECT t.*, s.*
        FROM teacher_tb t LEFT OUTER JOIN stu_teacher_tb st
        ON t.tno = st.tno
        LEFT OUTER JOIN stu_tb s
        ON st.sno = s.sno;
    </select>
</mapper>
```

 测试：
```
    /**
     * 测试TeacherMapper的findAll()方法
     */
    @Test
    public void testFindAll() {
        List<Teacher> teachers = teacherMapper.findAll();
        for (Teacher teacher : teachers) {
            System.out.println("--------------教师信息------------------");
            System.out.println("教师信息：" + teacher);
            System.out.println("该教师的学生信息：" + teacher.getStudents());
        }
    }
```

###### 注意事项 ######

1. **在映射配置文件中写较长的SQL语句，在换行时要注意加空格**，否则两行的单词可能会连在一起导致无法识别

---

## MyBatis中的延迟加载和立即加载 ##

- **延迟加载（懒加载）：在真正需要使用数据时才发起查询，不用的时候不查询**，例如一个用户有100个账户，若查询用户时就将所有账户信息查询出来，会很浪费内存
- **立即加载：不管用不用，只要一调用方法，马上发起查询**，例如查询账户信息时，一般都会将用户信息一起查询出来
- 什么时候用延迟加载？什么时候用立即加载？
 - **通常情况下，一对一和多对一用立即加载，一对多和多对多用延迟加载**

#### 一对一延迟加载 ####

- 在查询账户Account时先不查询其用户User的信息，而是在调用某个Account的方法时才查询它的User信息：
 1. Mybatis-3.4.6.jar包换成最新的Mybatis-3.5.1.jar
 2. 在主配置文件sqlMapConfig.xml中添加配置：
```
<configuration>
    <!-- 配置延迟加载策略 -->
    <settings>
        <setting name="lazyLoadingEnabled" value="true"/>   <!-- 开启延迟加载开关 -->
        <setting name="aggressiveLazyLoading" value="false" />  <!-- 将积极加载改为消息加载即按需加载 -->
    </settings>
	...
</configuration>
```
 3. 在映射配置文件AccountDaoInterf.xml进行配置：**用`<association>`标签的`select`属性指定延迟加载时要执行的方法，`column`属性指定延迟加载时执行方法的参数**
```
<mapper namespace="pers.cris.dao.AccountDaoInterf">

    <resultMap id="account_user" type="pers.cris.domain.Account">
        <id property="id" column="id"/>
        <result property="money" column="money"/>
        <result property="uid" column="uid"/>
        <!-- 配置一对一延迟加载：<association>标签
                javaType属性指定要封装成的对象
                select属性是延迟加载时要调用的方法
                column属性是用当前结果的uid作为延迟加载时的参数
         -->
        <association property="user" javaType="pers.cris.domain.User" select="pers.cris.dao.UserDaoInterf.findById" column="uid"/>
    </resultMap>

    <select id="findAll" resultMap="account_user">
        SELECT * FROM account_tb;	<!-- 此时只需要查询account_tb表，调用方法进行延迟加载时再调用UserDao的方法查询users表 -->
    </select>
</mapper>
```
 4. 测试：调用AccountDao的findAll()方法，若不对结果中的Account进行方法的调用，则日志中只有一条查询account_tb表的SQL语句，若对结果中的Account进行方法的调用，则会有查询users表的SQL语句

#### 一对多延迟加载 ####

- 在查询用户User时先不查询其账户集合List&lt;Account&gt;，而是在调用某个User的方法时才查询其List&lt;Account&gt;的信息：
 1. Mybatis-3.4.6.jar包换成最新的Mybatis-3.5.1.jar
 2. 在主配置文件sqlMapConfig.xml中添加配置：
```
<configuration>
    <!-- 配置延迟加载策略 -->
    <settings>
        <setting name="lazyLoadingEnabled" value="true"/>   <!-- 开启延迟加载开关 -->
        <setting name="aggressiveLazyLoading" value="false" />  <!-- 将积极加载改为消息加载即按需加载 -->
    </settings>
	...
</configuration>
```
 3. 在映射配置文件UserDaoInterf.xml进行配置：**用`<collection>`标签的`select`属性指定延迟加载时要执行的方法，`column`属性指定延迟加载时执行方法的参数**
```
<mapper namespace="pers.cris.dao.UserDaoInterf">

    <resultMap id="user_accounts" type="pers.cris.domain.User">
        <id property="id" column="id"/>
        <result property="username" column="username"/>
        <result property="sex" column="sex"></result>
        <result property="address" column="address"/>
        <!-- 配置一对多延迟加载：<collection>标签
                ofType属性指定封装集合中的对象
                select属性指定延迟加载时要调用的方法
                column属性表明将本次查询结果中的"id"作为延迟加载时执行方法的参数
         -->
        <collection property="accounts" ofType="pers.cris.domain.Account" select="pers.cris.dao.AccountDaoInterf.findByUid" column="id"/>
    </resultMap>

    <select id="findAll" resultMap="user_accounts">
        SELECT * FROM users;    <!-- 此时只需查询users表，调用方法进行延迟加载时再调用AccountDao的方法查询account_tb表 -->
    </select>
</mapper>
```
 4. 测试：调用UserMapper的findAll()方法，若不对结果中的User进行方法的调用，则日志中只有一条查询users表的SQL语句，若对结果中的User进行方法的调用，则会有查询account_tb表的SQL语句

---

## MyBatis的缓存 ##

- 什么是缓存？
 - 缓存就是存放在内存中的数据
- 为什么使用缓存？
 - 为了减少和数据库之间的交互次数，提高执行效率
- 什么样的数据适合用缓存？
 - 经常需要查询的数据
 - 不经常改变的数据
 - 数据的正确与否影响不大的数据（如银行的汇率、股票的牌价等绝对不能用缓存）

#### MyBatis的一级缓存（SqlSession） ####

- **MyBatis的一级缓存：SqlSession中的缓存**，查询数据库信息后，MyBatis会将信息缓存在SqlSession中，若再次查询相同的信息，则直接返回SqlSession缓存中的信息
- **触发清空一级缓存的清空：使用SqlSession创建的DAO代理对象对数据库进行增删改操作或调用其commit()、close()、clearCache()等方法时，MyBatis会将一级缓存清除**，再次查询相同的信息时会到数据库中查询，这样就**实现了一级缓存和数据库之间数据的同步**

```
    /**
     * 测试MyBatis一级缓存：SqlSession中的缓存
     */
    @Test
    public void testFirstLevelCache() {
        User user1 = userMapper.findById(1);
        User user2 = userMapper.findById(1);

        System.out.println("user1 = " + user1); // user1 = pers.cris.domain.User@654d8173
        System.out.println("user2 = " + user2); // user2 = pers.cris.domain.User@654d8173
        System.out.println(user1 == user2); // true

        // 使用SqlSession创建的DAO代理对象对数据库进行增删改操作或调用其commit()、close()、clearCache()等方法时，会清空一级缓存，以保证数据库和一级缓存中的数据同步
        session.commit();

        User user3 = userMapper.findById(1);
        System.out.println("user3 = " + user3); // user3 = pers.cris.domain.User@636e8cc
        System.out.println(user1 == user3); // false
    }
```

- 如果是别的SqlSession对数据库进行了增删改操作，则本SqlSession不会清空缓存，会存在与数据库的数据不一致的情况：
```
    /**
     * 如果是别的SqlSession对数据库进行了增删改操作，则本SqlSession不会清空缓存，会存在与数据库的数据不一致的情况
     */
    @Test
    public void testFirstLevelCache2() throws IOException {
        InputStream in = Resources.getResourceAsStream("sqlMapConfig.xml");
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(in);

        SqlSession session1 = factory.openSession();
        UserDaoInterf userMapper1 = session1.getMapper(UserDaoInterf.class);
        User user1 = userMapper1.findById(12);
        System.out.println(user1.getUsername());    // zhangSan

        // 用另一个SqlSession来对数据库进行更改
        SqlSession session2 = factory.openSession();
        UserDaoInterf userMapper2 = session2.getMapper(UserDaoInterf.class);
        User user2 = new User();
        user2.setId(12);
        user2.setUsername("updated_user");  // 更改id为12的用户的username为updated_user
        userMapper2.update(user2);
        session2.commit();

        User user3 = userMapper1.findById(12);

        System.out.println(user3.getUsername());    // zhangSan，说明获取到的仍然是一级缓存中的过时数据
        System.out.println(user1 == user3); // true
    }
```

#### MyBatis的二级缓存（SqlSessionFactory） ####

- **MyBatis的二级缓存：SqlSessionFactory中的缓存**，同一个SqlSessionFactory创建出来的SqlSession共享一个缓存区域，**但二级缓存中存储的不是对象，而会将对象进行序列化，再次查询时会重新组装一个对象返回（仍然不用查询数据库）**
- 开启MyBatis二级缓存：
 1. 配置主配置文件sqlMapConfig.xml使得MyBatis框架开启二级缓存：
```
<configuration>
    <!-- 配置MyBatis框架开启二级缓存 -->
    <settings>
        <setting name="cacheEnabled" value="true"/>
    </settings>
</configuration>
```
 2. 配置映射配置文件UserDaoInterf.xml使得UserDao支持二级缓存：
```
<mapper namespace="pers.cris.dao.UserDaoInterf">
    <!-- 开启UserDao支持二级缓存 -->
    <cache/>
</mapper>
```
 3. 配置映射配置文件UserDaoInterf.xml中要执行的操作，使得其支持二级缓存：
```
	<select id="findById" resultType="pers.cris.domain.User" parameterType="int" useCache="true">    <!-- 开启当前操作支持二级缓存 -->
        SELECT * FROM users WHERE id = #{uid};
    </select>
```
 4. 测试：
```
    /**
     * 测试MyBatis二级缓存：SqlSessionFactory中的缓存
     */
    @Test
    public void testSecondLevelCache() throws IOException {
        InputStream in = Resources.getResourceAsStream("sqlMapConfig.xml");
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(in);

        SqlSession session1 = factory.openSession();
        UserDaoInterf userMapper1 = session1.getMapper(UserDaoInterf.class);
        User user1 = userMapper1.findById(1);
        session1.close();   // 一级缓存被清空

        SqlSession session2 = factory.openSession();
        UserDaoInterf userMapper2 = session2.getMapper(UserDaoInterf.class);
        User user2 = userMapper2.findById(1);
        session2.close();

        System.out.println(user1 == user2); // 虽然是false，但日志显示只进行了一次对数据库的查询操作
                                            // 这是因为在二级缓存中会将对象进行序列化，再次查询时又重新组装一个新的对象返回
    }
```

---

## MyBatis注解开发 ##

- 注意：如果在DAO对应的资源路径下存在对应的映射配置文件文件(例如pers.cris.dao.IUserDao.xml)，再在该DAO上使用注解就会报错，因为MyBatis不知道该用注解还是该用映射配置文件，即使在主配置文件中用`<mapper class="pers.cris.dao.IUserDao">`配置成注解方式也一样

#### 基于注解的单表CRUD操作 ####

###### 属性名与数据库中的列名一致的User类  ######

- 主配置文件sqlMapConfig.xml中配置映射信息：
```
<configuration>
	...
    <!-- 配置映射信息 -->
    <mappers>
        <package name="pers.cris.dao"/>	<!-- 直接配了这个包下的所有DAO -->
    </mappers>
</configuration>
```
- IUserDao中的内容：
```
public interface IUserDao {
    /**
     * 查询所有用户信息
     * @return 所有用户信息
     */
    @Select("SELECT * FROM users")
    List<User> findAll();

    /**
     * 插入用户信息
     * @param user 用户
     */
    @Insert("INSERT INTO users(username, sex, address) VALUES(#{username}, #{sex}, #{address})")
    void insert(User user);

    /**
     * 更新用户信息
     * @param user 要更新的用户信息（将id为user.id的用户的信息更新成user的信息）
     */
    @Update("UPDATE users SET username=#{username}, sex=#{sex}, address=#{address} WHERE id=#{id}")
    void update(User user);

    /**
     * 按id删除用户信息
     * @param id 待删除用户的id
     */
    @Delete("DELETE FROM users WHERE id=#{uid}")
    void delete(int id);
    /**
     * 按照username模糊查询用户
     * @param name 模糊查询的name
     * @return 所有username中包含name的用户
     */

    @Select("SELECT * FROM users WHERE username LIKE #{name}")
    List<User> findByName(String name);

    /**
     * 根据id查询用户
     * @param id 要查询的id
     * @return id为id的用户
     */
    @Select("SELECT * FROM users WHERE id=#{id}")
    User findById(int id);

    /**
     * 查询用户总数
     * @return 用户总数
     */
    @Select("SELECT COUNT(*) FROM users")
    int getTotal();
}
```
- 测试：
```
    /**
     * 测试IUserDao的findAll()方法
     */
    @Test
    public void testFindAll() {
        List<User> users = userDao.findAll();
        for (User user : users)
            System.out.println(user);
    }

    /**
     * 测试IUserDao的insert()方法
     */
    @Test
    public void testInsert() {
        User user = new User();
        user.setUsername("annotation_insert_user");
        userDao.insert(user);
    }

    /**
     * 测试IUserDao的update()方法
     */
    @Test
    public void testUpdate() {
        User user = new User();
        user.setUsername("annotation_update_user");
        user.setId(13);
        user.setSex("male");
        user.setAddress("Mars");
        userDao.update(user);
    }

    /**
     * 测试IUserDao的delete()方法
     */
    @Test
    public void testDelete() {
        userDao.delete(13);
    }

    /**
     * 测试IUserDao的findByName()方法
     */
    @Test
    public void testFindByName() {
        List<User> users = userDao.findByName("%MyBatis%");
        for (User user : users)
            System.out.println(user);
    }

    /**
     * 测试IUserDao的findById()方法
     */
    @Test
    public void testFindById() {
        User user = userDao.findById(1);
        System.out.println(user);
    }

    /**
     * 测试IUserDao的getTotal()方法
     */
    @Test
    public void testGetTotal() {
        int total = userDao.getTotal();
        System.out.println("total = " + total);
    }
```


###### 属性名与数据库中的列名不一致的Student类  ######

- Student类的属性（sno、sname、gender、location）与数据库中的列名（id、username、sex、address）不一致，**可以用以下三个注解配合使用来实现映射**：
 - **`@Results`注解：相当于`<resultMap>`标签，用于封装一组映射关系，id属性是其唯一标识**
 - ** `@Result`注解：相当于`<id>`标签或`<result>`标签，指定一个`column`属性和一个`property`属性，来实现一对映射关系，并用`id`属性指明这对映射是否为主键**
 - **`@ResultMap`注解：相当于配置XML的SQL语句时的`resultMap`属性，用于引用定义过的映射关系**
- 主配置文件sqlMapConfig.xml中配置映射信息：
```
<configuration>
	...
    <!-- 配置映射信息 -->
    <mappers>
        <package name="pers.cris.dao"/>
    </mappers>
</configuration>
```
- IStuDao中的内容：
```
public interface IStuDao {
    /**查询所有学生信息
     * @return 所有学生信息
     */
    @Select("SELECT * FROM users")
    @Results(id = "stu_map", value = {  // @Results注解相当于<resultMap>标签，用于实体类属性和数据库列名之间的映射
                                        // id属性为这个映射关系的标识，可在别处引用
            @Result(id = true, column = "id", property = "sno"),    // @Result注解相当于<id>或<result>标签，id属性表示是否为主键
            @Result(column = "username", property = "sname"),
            @Result(column = "sex", property = "gender"),
            @Result(column = "address", property = "location")
    })
    List<Student> findAll();

    /**
     * 通过id查询学生信息
     * @param id id
     * @return id为id的学生信息
     */
    @Select("SELECT * FROM users WHERE id=#{id}")
    @ResultMap(value = {"stu_map"}) // @ResultMap注解用于引用之前定义过的@Results注解中的映射关系
    Student findById(int id);
}
```
- 测试：
```
    /**
     * 测试IStuDao的findAll()方法
     */
    @Test
    public void testFindAll() {
        List<Student> students = stuDao.findAll();
        for (Student student : students)
            System.out.println(student);
    }

    /**
     * 测试IStuDao的findById()方法
     */
    @Test
    public void testFindById() {
        Student student = stuDao.findById(1);
        System.out.println(student);
    }
```

#### 基于注解的多表查询操作（立即加载/延迟加载） ####

###### 一对一 ######

- 每个Account对象对应一个User对象
- 要想在查询Account类时，加载Account类中的User类的信息，**用`@Result`注解中的`one`属性绑定`@One`注解，`cloumn`属性指定关联查询的参数**
 - **`@One`注解：相当于XML配置中`<association>`标签中的`select属性`，其`select`属性指定关联查询时调用的方法，`fetchType`属性指定关联查询的时机（`FetchType.EAGER`表示立即加载，`FetchType.LAZY`表示延迟加载）**
1. IAccountDao中的配置：
```
    /**
     * 查询所有账户信息（会立即加载账户的用户信息）
     * @return 所有账户信息（会立即加载账户的用户信息）
     */
    @Select("SELECT * FROM account_tb")
    @Results(id = "account_user_map", value = {
            @Result(id = true, column = "id", property = "id"),
            @Result(column = "uid", property = "uid"),
            @Result(column = "money", property = "money"),
            /* 一对一关联查询：
                @Result注解中通过one属性指定@One注解，column属性指定关联查询的参数
                @One注解中的select属性指定关联查询的方法，fetchType属性指定关联查询的时机，FetchType.EAGER表示立即加载，FetchType.LAZY表示延迟加载
            */
            @Result(property = "user", 
					one = @One(select = "pers.cris.dao.IUserDao.findById",  fetchType = FetchType.EAGER), column = "uid")
    })
    List<Account> findAll();
```
2. 测试：
```
    /**
     * 测试AccountDao的findAll()方法，查询所有账户信息，同时立即加载账户的用户信息
     */
    @Test
    public void testFindAll() {
        List<Account> accounts = accountDao.findAll();
        for (Account account : accounts) {
            System.out.println("----------账户信息----------");
            System.out.println("账户信息：" + account);
            System.out.println("该账户的用户信息：" + account.getUser());
        }
    }
```
结果：Account中的User封装成功

###### 一对多 ######

- 每个User对象对应多个Account对象
- 在查询User对象时，需要配置其`List<Account>`属性的加载和加载时机，**和上面的“一对一”类似，只是`one`属性的`@One`注解换成`many`属性的`@Many`注解**

1. IUserDao中的配置：
```
    /**
     * 查询所有用户信息（会延迟加载用户的所有账户信息）
     * @return 所有用户信息（会延迟加载用户的所有账户信息）
     */
    @Select("SELECT * FROM users")
    @Results(id = "userMap", value = {
            @Result(id = true, column = "id", property = "id"),
            @Result(column = "username", property = "username"),
            @Result(column = "sex", property = "sex"),
            @Result(column = "address", property = "address"),
            @Result(property = "accounts", 
					many = @Many(select = "pers.cris.dao.IAccountDao.findByUid", fetchType = FetchType.LAZY), column = "id")    
					// 注意：column属性是在本查询（查询users表）结果中的列名
    })
    List<User> findAll();
```
2. 测试：
```
    /**
     * 测试UserDao的findAll()方法，查询所有用户的信息，同时会延迟加载用户中的账户信息
     */
    @Test
    public void testFindAll() {
        List<User> users = userDao.findAll();
        for (User user : users) {
            System.out.println("-------用户信息-------");
            System.out.println("用户信息：" + user);
            System.out.println("该用户的账户信息：" + user.getAccounts());
        }
    }
```
结果：User中的List&lt;Account&gt;封装成功，且是延迟加载（自定义输出和日志输出穿插，且若不调用User的方法，只会查询一次users表）
---

#### MyBatis基于注解开发的缓存 ####

- 一级缓存没什么可说的，和基于XML一样
- **MyBatis**基于XML开发时，开启**二级缓存**需要在主配置文件sqlMapConfig.xml、映射配置文件、相应的方法标签中配置，但**基于注解开发时，没有映射配置文件，也没有相应的方法标签**
 - 主配置文件sqlMapConfig.xml中开启二级缓存的配置和基于XML开发时一样：
```
<configuration>
    <settings>
        <setting name="cacheEnabled" value="true"/> <!-- 默认就是true -->
    </settings>
	...
</configuration>
```
 - **在DAO接口的接口名上使用`@CacheNamespace(blocking = true)`来使得该DAO支持二级缓存：**
```
@CacheNamespace(blocking = true)    // 配置该DAO开启二级缓存
public interface IUserDao {
	...
}
```
 - 测试：
```
    /**
     * 测试基于注解的二级缓存
     */
    @Test
    public void testSecondLevelCache() throws IOException {
        InputStream in = Resources.getResourceAsStream("sqlMapConfig.xml");
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(in);

        SqlSession session1 = factory.openSession();
        IUserDao userDao1 = session1.getMapper(IUserDao.class);
        User user1 = userDao1.findById(1);
        session1.close();   // 一级缓存被清空

        SqlSession session2 = factory.openSession();
        IUserDao userDao2 = session2.getMapper(IUserDao.class);
        User user2 = userDao2.findById(1);
        session1.close();   // 一级缓存被清空

        System.out.println(user1 == user2); // 虽然为false，但是通过日志可以看出只查询了一次数据库
                                            // 这是因为MyBatis的二级缓存不存储对象，而是将对象进行序列化，再次获取时重新组装一个新的对象
    }
```