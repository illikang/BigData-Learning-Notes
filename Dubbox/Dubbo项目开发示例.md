# Dubbo项目开发示例

## 环境准备

### 一、搭建注册中心（ZooKeeper）
  1. 获取并解压ZooKeeper，zookeeper-3.4.6.tar.gz
  2. 更改zooKeeper的配置文件
    * 在zookeeper主目录/conf下，改名zoo_sample.cfg为zoo.cfg，打开修改如下：
    ```
    dataDir=F:/server/zookeeper-3.4.12/data
    clientPort=2181   //访问端口2181
    ```
    * 根据dataDir配置的路径，创建data文件夹。
  3. 启动zookeeper服务器
  4. 启动zookeeper客户端（如果启动失败，一闪而过，则是JAVA_HOME的配置问题。）

### 搭建监控中心
  1. 获取监控中心项目：dubbox-master\dubbo-admin，修改项目中的dubbo.properties配置文件内容：
  ```
  dubbo.registry.address=zookeeper://127.0.0.1:2181
  dubbo.admin.root.password=root
  dubbo.admin.guest.password=guest
  ```
  2. 将dubbo-admin项目打包并发布到Tomcat。
  3. 在浏览器访问dubbo监控中心项目。

## 二、项目开发
### 创建公共项目
  1. 新建dubbox-project项目，在项目中建立smbms-common模块
    * 新建数据库dubbo_smbms
    * 新建表smbms_user：用户表
    ```
    id  数字（6）  主键
    userCode   字符（25）  非空
    userName   字符（25）  非空
    password   字符（25）  非空
    ```
    * 新建表smbms_order：订单表
    ```
    id  数字（12）  主键
    ownerUserId   数字（6）  非空
    productName   字符（50）  非空
    price   浮点（10，2）  非空
    amount  数字（8）   非空
    ```
    * 插入数据
    ```
    {17,'zhangsan','张三','1234'}，{18,'zhangsan','张三','1234'}
    ```
  2. 新建实体类与服务接口类
    ```
    cn.kgc1803.smbms-common.pojo.User
    cn.kgc1803.smbms-common.pojo.Order
    cn.kgc1803.smbms-common.service.UserService
    cn.kgc1803.smbms-common.service.OrderService
    ```
  3. 利用maven将公用项目打成jar包（package）并安装（install）到maven仓库中。

### 开发Provider(dubbo)
  1. 新建一个module smbms-user-provider
  2. 增加jar包依赖
    * zookeeper jar包 ：和注册中心通信的公共基础包
    * zkClient jar包： 和注册中心通信的客户端包
    * dubbo jar包：  发布服务于订阅服务包
    * smbms-common jar包：  公共的实体类与service接口
    * hession jar包： 提供通信协议（http,dubbo等协议）
    ```
        <dependency>
            <groupId>cn.kgc.1803</groupId>
            <artifactId>smbms-common</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>

        <dependency>
            <groupId>com.101tec</groupId>
            <artifactId>zkclient</artifactId>
            <version>0.8</version>
        </dependency>

        <dependency>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
            <version>3.4.6</version>
        </dependency>

        <dependency>
            <groupId>repository.com.alibaba</groupId>
            <artifactId>dubbo</artifactId>
            <version>2.8.4</version>
        </dependency>
        <!--通信包-->
        <dependency>
            <groupId>com.caucho</groupId>
            <artifactId>hessian</artifactId>
            <version>4.0.7</version>
        </dependency>
    ```
  3. 编写服务实现类
    ```
    #修改User类继承序列化接口
    public class User implements Serializable
    #编写UserS而viceImpl类
    public class UserServiceImpl implements UserService {
      public User loginUser(User u) {
        if(u.getUserCode().equals("zhangsan") && u.getPassword().equals("1234")){
            return u;
        }
        else{
            return null;
        }
    }
}
    ```
  4. 配置dubbo发布服务（spring）
    * 新建spring配置文件dubbo_user_provider.xml
    * 注入service到spring中
    ```
    <bean id="userService" class="cn.kgc1803.smbms_user_provider.service.UserServiceImpl"/>
    ```
    * 在spring配置文件中增加命名空间
    ```
    <?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://code.alibabatech.com/schema/dubbo
       http://code.alibabatech.com/schema/dubbo/dubbo.xsd
">
    ```
    * 利用dubbo命名空间配置应用名称
    ```
    <dubbo:application name="user_provider_app" owner="kgc" organization="kgc1803"/>
    ```
    * 配置注册中心
    ```
    <dubbo:registry address="zookeeper://127.0.0.1:2181"/>
    ```
    * 配置dubbo协议
    ```
    <!--配置协议(dubbo协议)-->
    <dubbo:protocol name="dubbo" contextpath="dubbo" />
    ```
    * 发布服务
    ```
    <!--发布服务-->
    <dubbo:service protocol="dubbo" interface="cn.kgc1803.smbms_common.service.UserService" ref="userService"/>
    ```
  5. 编写测试类
    ```
    public class TestProvider {
    public static void main(String[] args) {
        //加载spring配置文件
        ApplicationContext ctx=
                new ClassPathXmlApplicationContext("dubbo_user_provider.xml");
        try {
            Thread.sleep(60*60*1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
    ```
  6. 启动测试

    访问：http://127.0.0.1:8080/dubbo-admin-2.8.4/，查看服务是否已经注册进去

### 添加RESTful支持（dubbox）

dubbo中，User类借助序列化接口的实现完成了服务的发布。如果要加入RESTful的支持，就需要使得服务能够通过HTTP访问，而HTTP只能接收String，因此需要引入JSON包。

  1. 添加支持RESTful的依赖包
    * JSON插件包
    ```
    <dependency>
      <groupId>org.codehaus.jackson</groupId>
      <artifactId>jackson-mapper-asl</artifactId>
    <version>1.9.12</version>
    ```
    * REST风格访问dubbo,即dubbox
    ```
    <dependency>
      <groupId>repository.com.alibaba</groupId>
      <artifactId>dubbo-rpc-rest</artifactId>
      <version>2.8.4</version>
    </dependency>
    ```
  2. 改写Order类，支持转化为Json。为每个类成员变量添加@JsonProperty注解
  ```
  //实体类可以自动转化为Json类对象数据的属性
  @JsonProperty
  private Integer id;
  @JsonProperty
  private Integer ownerUserId;
  @JsonProperty
  private String productName;
  @JsonProperty
  private Double price;
  @JsonProperty
  private Integer amount;
  ```
  3. 编写service实现类，OrderServiceImpl
  ```
  @Path("/order")
@Produces(ContentType.APPLICATION_JSON_UTF_8)
public class OrderServiceImpl implements OrderService {
    @Path("/orderlist.htm")
    @GET
    public List<Order> findlist() {
        List<Order> orders= new ArrayList<Order>();
        Order o= new Order();
        o.setId(101);
        o.setOwnerUserId(11);
        o.setAmount(33);
        o.setPrice(55.3);
        o.setProductName("华为P8");
        orders.add(o);
        return orders;
    }
  ```
  4. 配置dubbo以REST协议发布OrderService服务。在dubbo_user_provider.xml中添加信息：
  ```
  <!--service注入-->
  <bean id="orderService" class="cn.kgc1803.smbms_user_provider.service.OrderServiceImpl"/>
  <!--Rest协议-->
  <dubbo:protocol name="rest" port="20888" />
  <!--发布REST服务-->
  <dubbo:service protocol="rest" interface="cn.kgc1803.smbms_common.service.OrderService" ref="orderService"/>
  ```
  5. 重新启动测试类，测试两个服务是否注册成功。
    * dubbo注册成功的服务，可以在监控中心（http://127.0.0.1:8080/dubbo-admin-2.8.4/）查询到。
    * REST注册成功的服务，可以通过REST端口的HTTP方式访问：http://127.0.0.1:20888/order/orderlist.htm

### 开发Consumer
  1. 新建Module smbms-user-consumer
  2. 添加Jar包依赖
    * spring-webmvc (spring + spring web+spring mvc)
    * dubbo-admin
    * zookeeper
    * zkclient
    * hession
  3. 编写控制器（编写过程与SpringMVC开发一致）
  ```
  @Controller
  @RequestMapping("/user")
  public class UserController {
    @Resource
    private UserService userService;

    @RequestMapping(value = "/dologin.htm", method = RequestMethod.POST)
    public String doLogin(User user, HttpSession session){
        User u=userService.loginUser(user);
        if(u==null){  //登陆失败
            return "login";
        }else {
            session.setAttribute("session_user",u);
            return "index";
        }
    }
  }
  ```
  4. 编写页面
  5. 配置web.xml(只针对SpringMVC)
  ```
  <servlet>
    <servlet-name>mvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!--启动spring mvc配置文件-->
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring-servlet.xml</param-value>
    </init-param>
  </servlet>

  <servlet-mapping>
    <servlet-name>mvc</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>
  ```
  6. 如何把IDEA中一个非WEB项目转化为WEB项目

    ![](img/web.jpg)

    * 打开Project Settings,在项目模块中添加Web框架。
    * 检查右侧的xml文件路径是否正确。如果不正确，点击"-"删除，重新添加并配置正确的路径。
    * 在模块项目的pom.xml文件中加入war打包方式
    ```
    <packaging>war</packaging>
    ```
  7. 添加dubbo配置文件dubbo_user_consumer.xml，并编写文件内容，实现订阅。
  ```
    <!--配置dubbo发布服务-->
    <!--设置提供者本次发布服务的应用名称-->
    <dubbo:application name="user_consumer_app" owner="kgc" organization="kgc1803"/>
    <!--注册中心-->
    <dubbo:registry address="zookeeper://127.0.0.1:2181"/>
    <!--订阅服务-->
    <dubbo:reference
            interface="cn.kgc1803.smbms_common.service.UserService"
            id="userService"/>
  ```
  8. 改写web.xml文件，添加dubbo配置。
  ```
  <!--添加以下内容-->
  <!--启动dubbo-->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:dubbo_user_consumer.xml</param-value>
    </context-param>

    <!--多个spring容器之间访问-->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
  ```
