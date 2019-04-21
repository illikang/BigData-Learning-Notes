# 创建支持SSH服务的镜像
一般情况下，Linux系统管理员通过SSH服务来管理操作系统，但Docker的很多镜像是不带SSH服务的，那么我们怎样才能管理操作系统呢？

进入容器的办法，比如用attach,exec等命令都无法解决远程管理容器的问题。因此，当需要远程登陆到容器内进行一些操作的时候，就必须要SSH的支持了。

创建带有SSH服务的镜像有两种方法：
 * 基于docker commit命令创建
 * 基于Dockerfile创建

## 一、基于docker commit命令创建带有SSH服务的镜像
