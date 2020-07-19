<link href="http://cdn.bootcss.com/highlight.js/8.0/styles/monokai_sublime.min.css" rel="stylesheet">  
<script src="http://cdn.bootcss.com/highlight.js/8.0/highlight.min.js"></script>  
<script>hljs.initHighlightingOnLoad();</script>  

# 非关系型数据库（NoSQL, Not only SQL）

- 非关系型数据库与关系型数据库的区别：
 - 关系型数据库的数据之间有关联关系，而**非关系型数据库的数据之间没有关联关系**
 - 关系型数据库的数据存储在硬盘上，而**非关系型数据库的数据存储在内存中**
 - 关系型数据的数据存储形式是表，而**非关系型数据库的数据存储形式不是表，而是是键值对等形式**
- **非关系型数据库的优点：性能高、查询速度快（数据在内存中，不需要进行IO操作）**
- 关系型数据库和非关系型数据库是互补关系：
 - **一般将数据存储在关系型数据库中，而在非关系型数据库中备份**
 - **（缓存思想）查询数据时先从非关系型数据库中查询，若查询到则直接返回数据，若查询不到则从关系型数据库中查询，并在非关系型数据库中备份，然后返回数据**
- 非关系型数据库有Redis、MongoDB等

## Redis

- 使用Redis要先开启Redis服务器redis-server.exe，然后使用Redis客户端redis-cli.exe
- **Redis存储的是键值对形式的数据**，其中：
 - **key是字符串类型（string）**
 - **value可以是5种数据结构：字符串类型（string）、哈希类型（hash，即map类型）、列表类型（list，相当于LinkedList）、集合类型（set）、有序集合类型（sortedset）**
- 对于value的数据类型不同的数据，Redis的语法不同

###### value为string类型

- 存储：`SET key value`
- 获取：`GET key`
- 删除：`DEL key [key2...]`

###### value为hash类型（map类型）

- （key为map名，field为map中的key）
- 存储：`HSET key field value`
- 获取：
 - 获取指定map中指定field的value：`HGET key field`
 - 获取指定map中的所有filed的value：`HGETALL key`
- 删除：`HDEL key field [field2...]`

###### value为list类型（相当于LinkedList）

- （key为list名）
- Redis的list类型相当于LinkedList，**可以从左右两边添加或删除元素**
- 添加：
 - 从左边添加：`LPUSH key value [value2...]`
 - 从右边添加：`RPUSH key value [value2...]`
- 范围获取：`LRANGE key start end`（获取所有：`LRANGE key 0 -1`）
- 获取并删除：
 - 从左边获取并删除：`LPOP key`
 - 从右边获取并删除：`RPOP key`

###### value为set类型（不允许重复）

- （key为set名）
- 添加：`SADD key member [member2...]`
- 获取set中所有元素：`SMEMBERS key`
- 删除：`SREM key member [member2...]`

###### value为sortedset类型（不允许重复，且元素有序）

- （key为sortedset名）
- sortedset中的每个元素会存储一个score，根据score来排序
- 添加：`ZADD key score member [score2 member2...]`
- 范围获取：`ZRANGE key start end [WITHSCORES]`
- 删除：`ZREM key member [member2...]`

###### 通用命令

- 查询所有的key：`KEYS *`（也可以用正则表达式代替`*`来查询匹配的key）
- 获取指定的key对应的value类型（来判断用什么指令进行增删改查）：`TYPE key`
- 删除指定的key及其对应的value：`DEL key`
---

### Redis的持久化

- Redis的数据存储在内存中，当Redis服务器重启时，数据会丢失，可以将数据持久化到硬盘上
- **Redis的持久化机制：**
 - **RDB（默认方式）：在一定的间隔时间内检测key的变化数量，并持久化到硬盘上**
 - **AOF：**用日志记录方式，可以记录每一条命令的操作，**每一次命令操作后都持久化数据**

###### Redis持久化机制 之 RDB

- RDB持久化步骤：
 1. 编辑redis.windows.conf文件，设置有指定数量的key改变时，在多少秒后进行持久化
```
#   after 900 sec (15 min) if at least 1 key changed
#   after 300 sec (5 min) if at least 10 keys changed
#   after 60 sec if at least 10000 keys changed
save 900 1
save 300 10
save 60 10000
```
 2. 重新启动Redis服务器，并**指定配置文件名称**，即在配置文件所在目录打开命令行并输入`redis-server.exe redis.windows.conf`
 3. 若有指定数量的key发生改变，则在指定时间后将数据持久化到配置文件所在目录的一个.rdb文件中
 4. 重启Redis服务器，仍能在Redis客户端获取到数据

###### Redis持久化机制 之 AOF

- AOF持久化步骤：
 1. 编辑redis.windows.conf文件，将`appendonly no`改成`appendonly yes`，表示开启AOF持久化
 2. 选择以下三种持久化选项之一
```
# appendfsync always	#（每一次命令后持久化一次）
appendfsync everysec	#（每隔一秒持久化一次）
# appendfsync no		#（不持久化）
```
 3. 重新启动Redis服务器，并**指定配置文件名称**，即在配置文件所在目录打开命令行并输入`redis-server.exe redis.windows.conf`
 4. 重启Redis服务器，仍能在Redis客户端获取到数据

---

### Jedis

- Jedis是**用Java操作Redis数据库**的工具，需要导入jedis.jar和commons-pool2.jar
- 使用Jedis步骤：
 1. 创建Jedis对象
 2. 用Jedis对象对数据进行操作
 3. 关闭连接

```
    /**
     * Jedis步骤：
     *      1. 导入jedis.jar和commons-pool2.jar
     *      2. 创建连接对象 —— Jedis对象
     *      3. 用Jedis对象对数据进行操作
     *      4. 关闭连接
     */
    @Test
    public void jedisDemo1() {
        Jedis jedis = new Jedis("localhost", 6379); // 参数为host和port，若不传参则默认为"localhost"和6379
        jedis.set("username", "zhangSan");  // 执行后在Redis客户端可以查询到数据
        jedis.close();
    }
```

###### Jedis操作string

- Jedis操作string的方法：
 - `get(key)`
 - `set(key, value)`
 - `del(key...)`
 - **`setex(key, second, value)` 设置键值对，并指定second秒后自动过期**

```
    /**
     * Jedis操作string的方法：get()、set()、del()、setex()
     */
    @Test
    public void jedisDemo2() {
        Jedis jedis = new Jedis();

        jedis.set("username", "liSi");
        String username = jedis.get("username");
        System.out.println("username = " + username);
        jedis.del("username");

        jedis.setex("activeCode", 10, "ABCDEFG");   // 设置该数据在10s后自动过期
        
        jedis.close();
    }
```

###### Jedis操作hash

- Jedis操作hash的方法：
 - `hget(key, field)`
 - `hset(key, field, value)`
 - **`hgetAll(key)` 返回一个`Map<String, String>`**
 - `hdel(key, field...)`

```
    /**
     * Jedis操作hash的方法：hget()、hset()、hgetAll()（返回Map）、hdel()
     */
    @Test
    public void jedisDemo3() {
        Jedis jedis = new Jedis();

        jedis.hset("user", "name", "zhangSan");
        jedis.hset("user", "age", "23");
        jedis.hset("user", "gender", "male");

        String name = jedis.hget("user", "name");
        String age = jedis.hget("user", "age");
        String gender = jedis.hget("user", "gender");
        System.out.println("name = " + name + ", age = " + age + ", gender = " + gender);

        Map<String, String> map = jedis.hgetAll("user");
        for (Map.Entry<String, String> entry : map.entrySet())
            System.out.println(entry.getKey() + " : " +  entry.getValue());

        jedis.del("user");	// 直接删除"user"对应的整个map

        jedis.close();
    }
```

###### Jedis操作list

- Jedis操作list的方法：
 - `lpush(key, value...)`
 - `rpush(key, value...)`
 - `lpop(key)`
 - `rpop(key)`
 - **`lrange(key, start, end)` 返回一个`List<String>`**

```
    /**
     * Jedis操作list的方法：lpush()、rpush()、lpop()、rpop()、lrange()（返回List）
     */
    @Test
    public void jedisDemo4() {
        Jedis jedis = new Jedis();

        jedis.lpush("myList", "1", "2", "3");
        jedis.rpush("myList", "a", "b", "c");

        List<String> list1 = jedis.lrange("myList", 0, -1);
        System.out.println("list1 = " + list1); // list1 = [3, 2, 1, a, b, c]

        String ele1 = jedis.lpop("myList");
        String ele2 = jedis.rpop("myList");
        System.out.println("ele1 = " + ele1);   // ele1 = 3
        System.out.println("ele2 = " + ele2);   // ele2 = c

        List<String> list2 = jedis.lrange("myList", 0, -1);
        System.out.println("list2 = " + list2); // list2 = [2, 1, a, b]

        jedis.del("myList");

        jedis.close();
    }
```

###### Jedis操作set

- Jedis操作set的方法：
 - `sadd(key, member...)`
 - **`smembers(key)` 返回一个`Set<String>`**
 - `srem(key, member...)`

```
    /**
     * Jedis操作set的方法：sadd()、smembers()（返回Set）、srem()
     */
    @Test
    public void jedisDemo5() {
        Jedis jedis = new Jedis();

        jedis.sadd("mySet", "Java", "C++", "C", "JavaScript");
        Set<String> set1 = jedis.smembers("mySet");
        System.out.println("set1 = " + set1);   // set1 = [C, Java, JavaScript, C++]

        jedis.srem("mySet", "C");
        Set<String> set2 = jedis.smembers("mySet");
        System.out.println("set2 = " + set2);   // set2 = [Java, JavaScript, C++]

        jedis.del("mySet");

        jedis.close();
    }
```

###### Jedis操作sortedset

- Jedis操作sortedset的方法：
 - `zadd(key, score, member)`
 - **`zrange(key, start, end)` 返回一个`Set<String>`**
 - `zrem(key, member...)`

```
    /**
     * Jedis操作sortedset的方法：zadd()、zrange()（返回Set）、zrem
     */
    @Test
    public void jedisDemo6() {
        Jedis jedis = new Jedis();

        jedis.zadd("mySortedSet", 109, "语文");
        jedis.zadd("mySortedSet", 136, "英语");
        jedis.zadd("mySortedSet", 121, "数学");

        Set<String> sortedSet1 = jedis.zrange("mySortedSet", 0, -1);
        System.out.println("sortedSet1 = " + sortedSet1);    // sortedSet1 = [语文, 数学, 英语]

        jedis.zrem("mySortedSet", "数学");

        Set<String> sortedSet2 = jedis.zrange("mySortedSet", 0, -1);
        System.out.println("sortedSet2 = " + sortedSet2);   // sortedSet2 = [语文, 英语]

        jedis.del("mySortedSet");

        jedis.close();
    }
```

---

#### Jedis连接池：JedisPool

- 连接池的作用：节省资源
- 使用Jedis连接池JedisPool的步骤：
 1. 创建JedisPoolConfig对象，设置连接池参数（可省略）
 2. 创建JedisPool连接池对象，给定JedisPoolConfig（可使用默认配置）
 3. JedisPool对象的getResource()方法获取Jedis连接对象
 4. 使用Jedis对象对数据进行操作
 5. Jedis对象的close()方法将连接归还给连接池

```
    /**
     * 使用Jedis的连接池JedisPool的步骤：
     *      1. 创建JedisPoolConfig对象，设置连接池参数（可省略，使用默认配置）
     *      2. 创建JedisPool连接池对象，给定JedisPoolConfig（可使用默认配置）
     *      3. JedisPool对象的getResource()方法获取Jedis连接对象
     *      4. 使用Jedis对象对数据进行操作
     *      5. Jedis对象的close()方法将连接归还给连接池
     */
    @Test
    public void jedisDemo1() {
        // 创建JedisPoolConfig对象，设置连接池参数
        JedisPoolConfig config = new JedisPoolConfig();
        config.setMaxTotal(50); // 设置最大连接数

        // 创建JedisPool连接池对象
//        JedisPool pool = new JedisPool(); // 若不指定JedisPoolConfig，则使用默认配置
        JedisPool pool = new JedisPool(config, "localhost", 6379);

        // 获取Jedis连接对象
        Jedis jedis = pool.getResource();

        // 操作数据
        jedis.setex("username", 5, "CrisJDT");

        // 将连接归还给连接池
        jedis.close();
    }
```

###### 自定义JedisPoolUtils工具类

- 可以将JedisPool连接池的参数存放在配置文件中，通过自定义的工具类JedisPoolUtils来加载配置参数并获取Jedis连接对象

配置文件：src > jedisPoolConf.properties
```
host = localhost
port = 6379
maxTotal = 50
maxIdle = 10
```

自定义工具类JedisPoolUtils：
```
/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/6/4 20:00
 * @Description: 自定义的JedisPoolUtils工具类，从配置文件中获取将JedisPool连接池的配置信息
 */
public class JedisPoolUtils {
    private static JedisPool pool;

    static {
        try {
            InputStream in = JedisPool.class.getClassLoader().getResourceAsStream("jedisPoolConf.properties");
            Properties prop = new Properties();
            prop.load(in);

            JedisPoolConfig config = new JedisPoolConfig();
            config.setMaxTotal(Integer.parseInt(prop.getProperty("maxTotal")));
            config.setMaxIdle(Integer.parseInt(prop.getProperty("maxIdle")));

            pool = new JedisPool(config, prop.getProperty("host"), Integer.parseInt(prop.getProperty("port")));
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    /**
     * 从JedisPool中获取Jedis连接对象
     * @return Jedis连接对象
     */
    public static Jedis getJedis() {
        return pool.getResource();
    }
}
```

---

### Redis应用

- **在数据不经常发生变化时，可以用Redis做缓存优化**
- 例如加载省份信息时，先查询Redis中是否有省份信息（建议用JSON形式）
 - 若有，则直接返回省份信息
 - 若没有，则调用JDBC的方法查询数据库，将查询到的省份信息转换成JSON串，存在Redis中，再返回JSON串
 - **注意：在数据库进行增删改操作时，需要清空Redis缓存，否则下次读到的可能是Redis中已过时的缓存数据**

---