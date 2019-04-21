# Docker容器操作
容器是Docker的另一个核心概念。简单得说，容器是镜像的一个运行实例，所不同的是，它带有额外的可写文件层。

本章具体介绍围绕容器的重要操作，包括创建一个容器、启动容器、终止一个容器、进入容器内执行操作、删除容器和通过导入导出容器来实现容器迁移等。

## 一、创建容器
```
$ docker create [参数] 镜像：tag
$ docker create -it ubuntu:latest
-t 让Docker分配一个伪终端（pseudo-tty）并绑定到容器的标准输入上
-i 让容器标准输入保持打开
-d 让Docker容器在后台以守护态（Daemonized）形式运行。
```
使用docker create 命令新建的容器处于停止状态，可以使用docker start命令来启动它。

## 二、启动容器
容器启动有两种方式，一种是将一个在停止状态的容器（包括用create新创建的容器）重新启动，使用命令docker start;另一个是基于镜像新建一个容器并启动，等价于先执行docker create命令，再执行docker start命令。
```
#启动容器，执行后，关闭
$ docker run ubuntu /bin/echo 'Hello World'
Hello World
#启动容器+bash终端，保持开启，允许用户进行交互
$ docker run -it ubuntu /bin/bash
root@af8bae53bdd3:/#
```
当利用docker run来创建并启动容器时，Docker在后台运行的标准操作包括：
* 检查本地是否存在指定的镜像，不存在就从公有仓库下载
* 利用镜像创建并启动一个容器
* 分配一个文件系统，并在只读的镜像层外面挂载一层可读写层
* 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去
* 从地址池配置一个IP地址给容器
* 执行用户指定的应用程序
* 执行完毕后容器被终止

## 三、查看容器信息
```
$ docker ps  查看开启容器的信息
$ docker ps -a  查看所有容器信息
$ docker ps -a -q  查看处于终止状态的容器的ID信息
$ docker logs [options] Container  获取容器的输出信息
```
## 四、终止容器
```
docker stop [options] Container
-t,--time[=10] :等待一段时间后，默认为10秒，发送SIGKILL信号终止容器
```
此外，当Docker容器中指定的应用终结时，容器也自动终止。例如只启动了一个终端的容器，用户通过exit命令或者Ctrl+d来退出终端时，所创建的容器立刻终止。

## 五、进入容器
在使用-d参数时，容器启动后会进入后台，用户无法看到容器中的信息。某些时候需要进入容器进行操作，有多种方法，包括使用docker attach命令、docker exex命令、以及nsenter工具等。
1. attach命令
```
$ docker run -idt ubuntu
# 查看运行容器的名称
$ docker ps    
$ docker attach 容器名称
```
但是使用attach命令有时候并不方便。当多个窗口同时attach到同一个容器的时候，所有窗口都会同步显示。当某个窗口因命令阻塞时，其他窗口也无法执行操作了。
2. exec命令
Docker自1.3版本起，提供了一个更加方便的工具exec，可以直接在容器内运行命令。
```
$ docker exec -ti 243c32535da7 /bin/bash
root@243c32535da7:/#
```
3. nsenter工具

## 六、删除容器
```
$ docker rm [options] Container [Container...]
-f, --force=false  强行终止并删除一个运行中的容器
-l, --link=false 删除容器的连接，但保留容器
-v, --volumes=false  删除容器挂载的数据卷
```
## 七、导入和导出容器
### 导出容器
导出一个已经创建的容器到一个文件，不管此时这个容器是否处于运行状态
```
$ docker export Container >file.tar
```
### 导入容器
```
$ cat file.tar | docker import - test/ubuntu:v1.0
```
实际上，既可以使用docker load命令来导入镜像存储文件到本地的镜像库，又可以使用docker import命令来导入一个容器快照到本地镜像库。这两者的区别在于容器快照文件将丢失所有的历史纪录和元数据信息（即仅保存容器当时的快照状态），而镜像存储文件将保存完整记录，体积也要大。此外，从容器快照文件导入时可以重新指定标签等元数据信息。
