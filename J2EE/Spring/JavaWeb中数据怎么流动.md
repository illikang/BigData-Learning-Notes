# JavaWeb中数据怎么流动
JaveWeb是如何浏览器进入服务器，数据在服务器中又是如何在各层之间传递，最后数据处理完以后，又是如何返回到浏览器的。
理清楚这个过程，能帮助我们更好的理解项目的开发。

## 从浏览器到服务器
数据从浏览器到服务器就是通过HTTP协议的五个常用动词：GET、POST、PUT、PATCH和DELETE，数据通过请求头或请求体发送给服务器。下面以GET和POST两种方式举例说明：

### GET方式
由HTTP协议知道，get请求的格式如下（以提交一个百度搜索为例）：
```
General
Request URL: https://mbd.baidu.com/newspage/data/landingsuper?context=&n_type=0&p_from=1
Request Method: GET
Status Code: 200 OK (from disk cache)
Remote Address: 180.97.33.107:443
Referrer Policy: no-referrer-when-downgrade
```
而数据正式通过URL发送给服务器的。具体来说就是：context=&n_type=0&p_from=1被Web服务器接收到以后，会以Map(键值对的形式)被服务器存入到request.Attribute中。服务器是通过输入流的形式读取到URL字符串中的数据，并存入到request对象中的。

随着JavaWeb的发展，早期Servlet既处理数据有负责页面的方式，逐渐演变成了Servlet与JSP分工协作的方式。由Servelt处理数据与逻辑，而JSP专门处理页面。此时数据就随着的Request对象而进入了Serlet中。在SpringMVC中，就是DispathcerServerlet,然后再由DispathcerServerlet根据请求的地址，分发给相应的Controller进行处理。

### POST方式
POST方式也是类似，有HTTP协议知道，post请求的格式如下（以请求百度翻译为例）：
```
General
Request URL: https://fanyi.baidu.com/v2transapi
Request Method: POST
Status Code: 200 OK
Remote Address: 106.39.162.57:443
Referrer Policy: no-referrer-when-downgrade

Form Data
from: zh
to: en
query: 我要翻译这个句子
transtype: translang
simple_means_flag: 3
```
可以看到，POST请求的URL不携带数据。数据是通过单独的Form Data发送过去的。类似的，Web服务器接收到请求以后，会以数据流的形式读入Form Data中的字符串数据，并通过字符串处理，将数据转化成键值对的数据形式，并存入到request对象中。键值对的键，来自前段页面元素标签的name属性，值则来自value属性。

总之就是，浏览器请求如果发送数据的话，数据最终都会以键值对的形式存放到servlet的request中。在SpringMVC框架中，框架帮我们处理了很多背后事情，因此我们可以更加方便得拿到浏览器请求的数据。
```
@RequestMapping("/welcome")
//请求http://**welcome?username=admin,这参数username传入admin
public String welcome(@RequestParam String username,)
@RequestMapping("/user")
//请求http://**welcome?username=admin&&username=1234,这参数username传入admin,userPassword传入1234
public String welcome(@RequestParam String username,@RequestParam String userPassword)
//如果请求发送来的键值与对象的成员变量名称一致，则springmvc会直接把数据封装入对象。
@RequestMapping("/login.htm")
public void  dologin(User user, HttpSession session, HttpServletRequest request, HttpServletResponse response){}
```

### 服务器各层之间的传输
数据首先进入服务器的Control层。在Control层中加入Service层实现类的成员变量，调用service层类的方法，数据就进入到service层，同样的方法，在service层中调用dao层的方法，数据进入到DAO层。DAO层借助数据库工具，比如MyBatis可以方便得完成与数据库的交互，并返回需要的数据。服务器各层之间数据的传输其实就是纯粹的JAVA模式。

### 服务器到浏览器
