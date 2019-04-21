# Docker创建镜像
创建镜像的方法有三种：基于已有镜像的容器创建、基于本地模板导入、基于Dockerfile创建。这里对这三种方法一一讲解。

## 一、基于已经镜像的容器创建镜像
通过前面内容我们知道，镜像在被启动以后，会成为一个容器。镜像本身是只读的，但是变成容器以后，会给镜像添加一个可读写层，从而记录后续操作给镜像带来的变化，比如在容器中创建新文件，配置新的环境变量等，来使当前容器能够满足我们个性化的需要。同时，通过Docker我们可以随时将更改后的容器保存为新的镜像，从而最大程度上方便了对需求环境的管理，满足实际开发或生产中开发或测试环境持续变化的要求。这就类似于虚拟机的快照功能。

### 命令格式：
```
$ docker commit [options] Container [Repository:[:tag]]
-a, --author="" 作者信息
-m, --message="" 提交信息
-p, --pause=true 提交时暂停容器运行
```
### 简单实例
1. 启动一个镜像，并在其中进行修改操作，例如创建一个test文件，之后退出：
```
$ docker run -it ubuntu:14.04 /bin/bash
root@xxxxxx:/# touch test
root@xxxxxx:/# touch exit
```
2. 此时该容器跟原ubuntu:14.04镜像相比，已经发生了变化，可以使用docker commit命令来提交为一个新的镜像。提交时可以使用ID或名称来指定容器：
```
$ docker commit -m "aa" -a "leon" xxxxxx test_image
```

## 二、基于本地模板导入
模板是压缩包，比如从OpenVZ可以下载各种系统的模板：http://openvz.org/Download/templates/precreated。
```
$ sudo cat ubuntu-14.04-x86_64-miniaml.tar.gz | docker import - ubuntu:14.04
```

## 三、使用Dockfile创建镜像
Dockerfile是一个文本格式的配置文件，由一行行命令语句组成，用户可以使用Dockerfile快速创建自定义的镜像。

### 1).Dockfile基本结构
一般而言，Dockerfile分为四部分：
1. 基础镜像信息：一开始必须指明所基于的镜像名称。
2. 维护者信息：接下来一般会说明维护者信息。
3. 镜像操作指令：每运行一条RUN命令，镜像添加新的一层，并提交。
4. 容器启动时执行指令：指定运行容器时的操作命令。

### 2).Dockfile指令
指令的一般格式为
```
INSTRUCTION arguments
```

1. FROM
第一条指令必须为FROM指令。
```
FROM <image>
FROM <image>:<tag>
```
2. MAINTAINER
指定维护者信息
```
MAINTAINER <name>
```
3. RUN
每条RUN指令将在当前镜像基础上执行指定命令，并提交为新的镜像（CMD也是在当前镜像基础上执行指定命令，但不会提交新的镜像，其实从Dockerfile的角度，不必严格区分这二者，因为它们都可以配置出我们想要的环境）。有两种格式：
```
RUN <command>
RUN ["executable","param1","param2"]
RUN ["/bin/bash","bin/bash","-c","echo hello"]
```
前者将在shell终端中运行命令，即/bin/sh -c；后者则使用exex执行。
4. CMD
指定启动容器时执行的命令，每个Dockerfile只能有一条CMD命令。如果制定了多条命令，只有最后一条会被执行。（由此可见，CMD并不适合配置环境，适合执行开机脚本或开机进入容器等）如果用户启动容器时候指定了运行的命令，则会覆盖掉CMD指定的命令。支持三种格式：
```
CMD ["executable","param1","param2"] 使用exec执行，推荐方式
CMD command param1 param2  在/bin/sh中执行，提供给需要交互的应用
CMD ["param1","param2"]  提供给ENTRYPOINT的默认参数
```
5. EXPOSE
告诉Docker服务器端容器暴露的端口号，供互联系统使用。在启动容器时需要通过-P，Docker主机会自动分配一个端口转发到指定的端口；使用-p，则可以具体指定哪个本地端口映射过来。
```
EXPOSE <port> [<port>...]
```
6. ENV
指定一个环境变量，会被后续的RUN指令使用，并在容器运行时保持。
```
ENV <key> <value>
env PG_MAJOR 9.3
```
7. ADD
复制指定的<src>到容器中的<dest>。其中<src>可以是Dockerfile所在目录的一个相对路径（文件或目录）；也可以是一个URL；还可以是一个tar文件（自动解压为目录）
```
ADD <src> <dest>
```
8. COPY
复制本地主机的<src>（为Dockerfile所在目录的相对路径，文件或目录）为容器中的<dest>。目标路径不存在时，会自动创建。当使用本地目录为源目录时，推荐使用COPY。
```
COPY <src> <dest>
```
9. ENTRYPOINT
配置容器启动后执行的命令，并且不可被docker run提供的参数覆盖。每个Dockerfile中只能有一个ENTRYPOINT，当指定多个ENTRYPOINT时，只有最后一个生效。
```
ENTRYPOINT ["executable","param1","param2"]
ENTRYPOINT command param1 param2 (shell中执行)
```
10. VOLUME
创建一个可以从本地主机或其他容器挂载的挂载点，一般用来存放数据库和需要保持的数据等。
```
VOLUME ["/data"]
```
11. USER
指定运行容器时的用户名或UID，后续的RUN也会使用该指定用户。当服务不需要管理员权限时，可以通过该命令指定运行用户。并且可以在之前创建所需要的用户，例如： RUN groupadd -r postgres && useradd -r -g postgres postgres。
```
USER daemon
```
12. WORKDIR
为后续的RUN、CMD、ENTRYPOINT指令配置工作目录。
```
WORKDIR /home/leon/workdir
相当于开机执行
$ cd /home/leon/workdir
```
13. ONBUILD
配置当所创建的镜像作为其他新创建镜像的基础镜像时，所执行的操作命令。
```
ONBULID [INSTRUCTION]
```

### 3).创建镜像
编写完成Dockerfile之后，可以通过docker build命令来创建镜像。
```
docker build [选项] 路径
```
该命令将读取指定路径下（包括子目录）的Dockerfile，并将该路径下所有内容发送给Docker服务器，有服务器端来创建镜像。因此一般建议放置Dockerfile的目录为空目录。

要指定镜像的标签信息，可以通过-t选项。
