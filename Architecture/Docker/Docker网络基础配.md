# Docker网络基础配置
大量的互联网应用服务包括多个服务组件，这往往需要多个容器之间通过网络通信进行相互配合。Docker目前提供了映射容器端口到宿主机器和容器互联机制来为容器提供网络服务。

本文讲解如何使用Docker的网络功能。包括使用端口映射机制来将容器内应用服务提供给外部网络，以及通过容器互联让多个容器之间进行快捷的网络通信。

## 一、端口映射实现访问容器
在启动容器的时候，如果不指定对应参数，在容器外部是无法通过网络来访问容器内的网络应用和服务的。当容器中运行一些网络应用时，要让外部访问这些应用时，可以通过-P或-p参数来指定端口映射。

注意：容器有自己的内部网络和IP地址（使用docker inspect + 容器ID可以获取所有的变量值）。

### -P随机映射

当使用-P标记时，Docker会随时映射一个49000~49900的端口至容器内部开放的网络端口：
```
$ docker run -d -P mysql
-P 随机映射一个49000~49900的端口至容器内部开发的网络端口
$ docker ps
# 0.0.0.0:49153->22/tcp, 0.0.0.0:49154->3306/tcp
```
检查容器端口映射情况，可见容器的22端口被映射到本地的49153端口，容器的3306端口被映射到本地的49154端口

### -p指定映射
1. 映射所有接口地址
```
$ docker run -d -p 5000:5000 training/webapp python app.py
-p hostPort:ContainerPort
```
使用hostPort:ContainerPort格式将本地5000端口映射到容器5000端口，此时默认会绑定本地所有接口上的所有地址。
2. 映射到指定地址的指定端口
```
$ docker run -d -p 127.0.0.1:5000:5000 training/webapp python app.py
-p ip:hostPort:ContainerPort
```
绑定本机地址：127.0.0.1（localhost）的5000端口到容器内应用的5000端口
3. 映射到指定地址的任意端口
```
$ docker run -d -p 127.0.0.1::5000 training/webapp python app.py
-p ip:t:ContainerPort
```
绑定localhost的任意端口到容器的5000端口，本地主机会自动分配一个端口
-p ip:hostPort:ContainerPort | ip::ContainerPort | hostPort:ContainerPort

## 二、查看端口映射配置
使用docker port来查看当前映射的端口配置，也可以看到绑定的地址
```
$ docker port mysql 3306
127.0.0.1:49155
```
## 三、容器互联实现容器间通信
容器的连接（linking）系统是出了端口映射外另一种可以与容器中应用进行交互的方式。它会在源和接受容器之间创建一个隧道，接受容器可以看到源容器制定的信息。
1. 自定义容器命名
连接系统依据容器的名称来执行。因此，首先需要自定义一个好记的容器命名。虽然当创建容器的时候，系统默认会分配一个名字，但自定义命名容器有两个好处：
  * 自定义的明明，比较好记，比如一个Web应用容器，我们可以给它起名叫web。
  * 当要连接其他容器的时候，可以作为一个有用的参考点。

使用--name标记可以为容器自定义命名：
```
$ docker run -d -P --name db postgres
```
2. 容器互联
使用--link参数可以让容器之间安全的进行交互
```
# --link name:alias,name是要链接的容器的名称，alias是这个连接的别名。
$ docker run -d --name db postgres
$ docker run -d -P --name web --link db:db training/webapp python app.py
```
Docker在两个互联的容器之间创建了一个安全隧道，而且不用映射它们的端口到宿主主机上。在启动db容器的时候并没有使用-p或-P标记，从而避免了暴露数据库端口到外部的网络上。

既然没有暴露端口，那么Docker是如何实现这两个容器互联的呢？

Docker通过如下两种方式为容器公开连接信息：
 * 环境变量
 * 更新/etc/hosts文件

使用env命令来查看web容器的环境变量，可以发现web容器中添加了DB容器的IP地址与端口作为环境变量。
```
$ docker run --rm --name web2 --link db:db training/webapp ENV
...
DB_NAME=/web2/db
DB_PORT=tcp://172.17.0.5:5432
DB_PORT_5000_TCP=tcp://172.17.0.5:5432
DB_PORT_5000_PROTO=tcp
DB_PORT_5000_PROT=5432
DB_PORT_5000_ADDR=172.17.0.5
```
启动DB_开头的环境变量是供web容器链接db容器使用，前缀采用大写的连接别名。

除了环境变量，Docker还添加host信息到父容器的/etc/hosts的文件。
```
docker run -it --rm --link db:db training/webapp /bin/bash
root@aed84ee21bde:/opt/webapp## cat /etc/hosts
...
172.17.0.7 aed84ee21bde
...
172.17.0.5 db
```
这里有两个hosts信息，第一个是web容器，web容器用自己的id作为默认主机名，第二个是db容器的ip和主机名。可以在web容器中安装ping命令来测试跟db容器的连通：
```
root@aed84ee21bde:/opt/webapp## apt-get install -ypp inetutils-ping
root@aed84ee21bde:/opt/webapp## ping db
```
