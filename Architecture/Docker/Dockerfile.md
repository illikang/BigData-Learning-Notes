# Dockerfile

## Dockerfile语法
### Docker支持的语法命令如下：
```
#指令不区分大小写，但是命名约定为全部大写
INSTRUCTION argument
```
### 常用Dockerfile命令
```
# 1.FROM：所有Dockerfile都必须以From命令开始。From命令会指定基于那个基础镜像创建。
FROM <image name>  
# 2.MAINTAINER：设置该镜像的作者
MAINTAINER <author name>
# 3.RUN：在shell或者exec的环境下执行的命令。RUN指令会在新创建的镜像上添加新的层面。
RUN <command>
# 4.ADD:复制文件指令。复制文件指令。它有两个参数<source>和<destination>。destination是容器内的路径。source可以是URL或者是启动配置上下文中的一个文件。
ADD <src> <destination>
# 5.CMD:提供了容器默认的执行命令。 Dockerfile只允许使用一次CMD指令。 使用多个CMD会抵消之前所有的指令，只有最后一个指令生效。CMD有三种形式：
CMD ["executable","param1","param2"]
CMD ["param1","param2"]
CMD command param1 param2
# 6.EXPOSE:指定容器在运行时监听的端口。语法如下：
EXPOSE <PORT>
# 7.ENTRYPOINT:配置给容器一个可执行的命令，这意味着在每次使用镜像创建容器时一个特定的应用程序可以被设置为默认程序。同时也意味着该镜像每次被调用时仅能运行指定的应用。类似于CMD，Docker只允许一个ENTRYPOINT，多个ENTRYPOINT会抵消之前所有的指令，只执行最后的ENTRYPOINT指令。语法如下：
ENTRYPOINT ["executable", "param1","param2"]
ENTRYPOINT command param1 param2
# 8.WORKDIR:指定RUN、CMD与ENTRYPOINT命令的工作目录。语法如下：
WORKDIR /path/to/workdir
# 9.ENV：设置环境变量。它们使用键值对，增加运行程序的灵活性。语法如下：
ENV <key> <value>
# 10.USER:镜像正在运行时设置一个UID。语法如下：
USER <uid>
# 11.VOLUME:授权访问从容器内到主机上的目录。语法如下：
VOLUME ["/data"]
```
