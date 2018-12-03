* [Java使用Redis](#Java使用Redis)
    * [概述](#概述)
    * [搭建Java环境](#搭建Java环境)
    * [简单测试](#简单测试)
# Java使用Redis

## 概述
Java开发中使用Redis是借助Jedis工具包来实现的。Jedis是集成了redis的一些命令操作，封装了redis的java客户端，提供了连接池管理和分布式支持。

## 搭建Java环境
  * 开发环境：IDEA+MAVEN
  * 创建快速项目，并在pom.xml中引入Jedis依赖包：
  ```
  <dependency>
      <groupId>redis.clients</groupId>
      <artifactId>jedis</artifactId>
      <version>2.9.0</version>
    </dependency>
  ```
  * 基于Junit创建测试类：
  ```
  import redis.clients.jedis.Jedis;
  public class JedisTest {
    Jedis jedis= new Jedis("127.0.0.1");
    @Before
    public void setup(){
        System.out.println("连接成功");
        //查看服务是否运行
        System.out.println("服务正在运行："+jedis.ping());
    }

    @After
    public void teardown(){
        jedis.close();
        System.out.println("Jedis关闭");
    }
}

  ```

## 简单测试
```
//字符串测试
    @Test
    public void testString(){
        jedis.set("name","Leon");
        System.out.println(jedis.get("name"));
    }

//列表测试
    @Test
    public void testList(){
        jedis.lpush("group","leon","jack","tom","lucy");
        List<String> list=jedis.lrange("group",0,2);
        for(String s:list){
            System.out.println(s);
        }
    }
```

## Jedis详细使用参考：Jedis API 文档。
