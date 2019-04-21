# Docker数据管理
用户在使用Docker的过程中，往往需要能查看容器内应用产生的数据，或者需要把容器你的数据进行备份，甚至多个容器之间进行数据分享，这必然涉及容器的数据管理操作。

容器中管理数据主要有两种方式：
1. 数据卷（Data Volumes）
2. 数据卷容器（Data Volume Container）

本文首先介绍如何在容器内创建数据卷，并且把本地的目录或文件挂载到容器内的数据卷中。接下来，会介绍如何使用数据卷容器在容器和主机、容器和容器之间共享数据，并实现数据的备份和恢复。

## 一、数据卷
数据卷是一个可供容器使用的特殊目录，它绕过文件系统，可以提供很多有用的特性：
 * 数据卷可以在容器之间共享和重用
 * 对数据卷的修改会立马生效
 * 对数据卷的更新，不会影响镜像
 * 卷会一直存在，知道没有容器使用。
数据卷的使用，类似于Linux下对目录或文件进行mount操作。

1. 在容器内创建一个数据卷
在用docker run命令的时候，使用-v标记可以在容器内创建一个数据卷。多次使用-v标记可以创建多个数据卷。
```
$ docker run -d -P --name web -v /webapp training/webapp python app.py
```
2. 挂载一个主机目录作为数据卷
使用-v标记也可以指定挂载一个本地的已有目录到容器中去作为数据卷：
```
$ docker run -d -P --name web -v /src/webapp:/opt/webapp training/webapp python app.py
```
上面的命令加载主机的/src/webapp目录到容器的/opt/webapp目录。这个功能在进行测试的时候十分方便，比如用户可以放置一些程序或数据到本地目录中，然后在容器内运行和使用。另外，本地目录的路径必须是绝对路径，如果目录不存在，Docker会自动创建。

Docker挂载数据卷的默认权限是读写（rw），用户也可以通过：ro指定为只读,容器内挂载的数据卷的数据就无法修改了。
```
$ docker run -d -P --name web -v /src/webapp:/opt/webapp:ro training/webapp python app.py
```
3. 挂载一个本地主机文件作为数据卷
-v标记也可以从主机挂载单个文件到容器中作为数据卷：
```
$ docker run --rm -v ~/.bash_history:/.bash_history ubuntu /bin/bash
```
这样就可以记录在容器输入过的历史命令了

## 二、数据卷容器
如果用户需要在容器之间共享一些持续更新的数据，最简单的方式是使用数据卷容器。数据卷容器其实就是一个普通的容器，抓门用它提供数据卷供其它容器挂载使用的方法如下：
1. 创建一个数据卷容器dbdate，并在其中创建一个数据卷挂载到/dbtata:
```
$ docker run -it -v /dbdata --name dbdata ubuntu
root@3ed94f279b6f:/#
root@3ed94f279b6f:/# ls
bin boot dbdata dev etc home lib lib64 media mnt opt proc root run sbin srv sys tmp usr var
```
2. 然后，可以在其他容器中使用--volumes-from来挂载dbdata容器中的数据卷，例如创建db1和db2两个容器，并从dbdata容器挂载数据卷：
```
$ docker run -it --volumes-from dbdata --name db1 ubuntu
$ docker run -it --volumes-from dbdata --name db2 ubuntu
```
此时，容器db1和db2都挂在同一个数据卷到相同的/dbdata目录。三个容器任何一方在该目录下写入，其他容器都可以看到。例如，在dbdata容器中创建一个test文件：
```
$ docker run -it -v /dbdata --name dbdata ubuntu
root@3ed94f279b6f:/# cd /dbdata
$ docker run -it -v /dbdata --name dbdata ubuntu
root@3ed94f279b6f:/# touch test
$ docker run -it -v /dbdata --name dbdata ubuntu
root@3ed94f279b6f:/# ls
test
```
在db1容器内查看它：
```
$ docker run -it --volumes-from --name db1 ubuntu
root@4128d2d804b4:/# ls
bin boot dbdata dev etc home lib lib64 media mnt opt proc root run sbin srv sys tmp usr var
root@4128d2d804b4:/# ls ls dbdata/
test
```
如果删除了挂载的容器（包括dbdata,db1和db2），数据卷并不会被自动删除。如果要删除一个数据卷，必须在删除最后一个还挂载着它的容器时显式使用docker rm -v 命令来指定同时删除关联的容器。

## 三、利用数据卷容器迁移数据
可以利用数据卷容器对其中的数据卷进行备份、恢复，以实现数据的迁移。
1. 备份
```
$ docker run --volumes-from dbdata -v $(pwd):/backup --name worker ubuntu tar cvf /backup/backup.tar /dbdata
```
首先利用ubuntu镜像创建了一个容器worker。使用--volumes-from dbdata参数来让worker容器挂载dbdata容器的数据卷；使用-v $(pwd):/backup参数来挂载本地的当前目录到worker容器的/backup目录

worker容器启动后，使用了tar cvf /backup/backup.tar /dbdata命令来将/dbdata下内容备份为容器内的/backup/backup.tar，即宿主主机当前目录下的backup.tar。

2. 恢复
如果要恢复数据到一个容器，可以按照下面的操作。首先创建一个带有数据卷的容器dbdata2:
```
$ docker run -v /dbdata --name dabdata2 ubuntu /bin/bash
```
然后创建另一个新的容器，挂载dbdata2的容器，并使用untar解压备份文件到所挂载的容器卷中即可：
```
$ docker run --volumes-from dbdata2 -v $(pwd):/backup busybox tar xvf /backup/backup.tar
```
