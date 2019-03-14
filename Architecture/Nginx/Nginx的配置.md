# Nginx的配置

## nginx.conf概要
Nginx的所有配置都是通过配置nginx.conf文件来实现的，下图是nginx.conf文件的一个结构概要：

![](img/nginx_conf.jpg)

1. 全局块
  * 运行Nginx服务器的用户组
  * Nginx进程PID的存放路径
  * 日志的存放路径
  * 配置文件的引入
  * worker_processes的数量

2. events块
3. Http块：设置HTTP服务器，可以嵌套多个server,如果Nginx支持多个虚拟主机，可以通过配置多个server来实现。配置反向代理功能提供负载均衡支持。
4. server块：配置虚拟主机的相关参数
5. location块：请求URL过滤，正则匹配
