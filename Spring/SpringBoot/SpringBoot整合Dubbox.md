# SpringBoot整个Dubbox

##Dubbo开发的大致流程
1. 开发公用模块，提供pojo以及service接口。如果是基于非Rest方式（非Dubbox），则Pojo类需要实现Serializable接口，以序列化输出。如果基于Rest方式，则应该引入Jackson的jar包，将对象转换为JSON字符串。
2. 开发服务提供者模块。
  * 引入公共模块
  *
