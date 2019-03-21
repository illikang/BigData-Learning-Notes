# DockerHub
类似于GitHub帮助我们存放项目,DockerHub帮助我们存放镜像文件。有了它的帮助，我们就可以是自己的开发平台成为可移动的了，在任何装有Docker服务器和客户端的机器上都可以拉取镜像并创建容器。

## DockerHub vs 类似于GitHub
DockerHub中registries（码头工人登记处）其实就是DockerHub的账号，类似于Github的总路径。仓库都是创建在registries下的。

|      | DockerHub     | GitHub     |
| :------------- | :------------- |:------------- |
| 仓库     | registries（用户名）      |git账号      |
| 镜像/项目      | repository     |repository     |

## 如何上传本地镜像到DockerHub上
1. Tag the image(给镜像添加标签)
```
#
$ docker tag image username/repository:tag
#例如：
$ docker tag hello-world illikang/test_repository:latest
```
2. Publish the image(发布镜像)
```
docker push username/repository:tag
```
3. Pull and run the image from the remote repository(从远程仓库拉取并执行镜像)
```
docker run -p 4000:80 username/repository:tag
```
如果要执行的镜像不在本地，Docker就会自动从远程仓库拉取。

## 区分几个概念
1. registries:注册表，
2. repository：仓库，用来存放标签过的镜像，用标签来区分不同的镜像。
3. tag：用来区分同一仓库中的不同镜像，相当于镜像的名字。
