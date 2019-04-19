# Docker镜像命令

## 一、获取镜像
命令格式：
```
$ docker pull NAME [:tag]
$ docker pull java

```
对于Docker镜像来说，如果不显式地指定tag,则默认会选择latest标签，即下载仓库中最新版本的镜像。

下载过程中可以看出，镜像文件一般由若干层组成，每层有唯一的id.当不同的镜像包括相同的层时，本地仅存储层的一份内容，减小了需要的存储空间。

严格的讲，镜像的仓库名称中还应该添加仓库地址（即registry,注册服务器）作为前缀，只是我们默认使用的是Docker Hub服务，该前缀可以忽略。例如：
```
$ docker pull ubuntu:14.04
#等同于
$ docker pull registry.hub.docker.com/ubuntu:14.04
```
如果从非官方的仓库下载，则需要在仓库名称前指定完整的仓库地址。例如从网易镜像源下载ubuntu14.04镜像，可以使用如下命令：
```
$ docker pull hub.c.163.com/public/ubuntu:14.04
```
## 二、查看镜像信息
命令格式：
```
$ docker images
```

## 三、搜寻镜像
命令格式：
```
$ docker search
--automated=false 仅显示自动创建的镜像
--no-trunc=false 输出信息不截断显示
-s/--stars=0 指定仅显示评价为指定星级以上的镜像
```
除了用search命令意外，还可以后很多其他方法去寻找镜像，比如去dockhub官网搜索，也是很不错的办法。
## 四、删除镜像
1. 使用镜像标签删除镜像
2. 使用镜像ID删除镜像

## 五、存出和载入镜像
