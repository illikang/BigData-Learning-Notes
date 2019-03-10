# Docker安装与配置

## 一、Docker安装（以CentOS7为例）
1. Docker要求CentOS系统的内核版本高于3.10，首先检查系统版本来验证是否支持Docker。
```
$ uname -r
```
2. 使用管理员权限，更新yum包到最新
```
$ sudo yum update
```
3. 查看是否已安装就版本，如果已经安装，卸载
```
$ yum list installed |grep *docker*
$ yum remove docker  docker-common docker-selinux docker-engine
```
4. 安装需要的软件包，yum-util提供yum-config-manager功能，另外两个是devicemapper驱动依赖的
```
$ sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```
5. 设置yum源，更新yum缓存
```
$ sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
$ sudo yum makecache fast
```
6. 可以查看所有仓库中所有docker版本，并选择特定版本安装。
```
$ yum list docker-ce --showduplicates | sort -r
```
7. 安装Docker
```
$ sudo yum install docker-ce  #由于repo中默认只开启stable仓库，故这里安装的是最新稳定版17.12.0
$ sudo yum install <FQPN>  # 例如：sudo yum install docker-ce-17.12.0.ce
```
8. 启动并加入开机启动
```
$ sudo systemctl start docker
$ sudo systemctl enable docker
```
9. 验证安装是否成功
```
$ docker version
$ sudo docker run hello-world
```

## 二、删除Docker CE
```
$ sudo yum remove docker-ce
$ sudo rm -rf /var/lib/docker
```

## 三、镜像加速
鉴于国内网络问题，后续拉取Docker镜像十分缓慢，我们需要配置加速器来解决。这里使用的是网易的镜像地址：http://hub-mirror.c.163.com。

新版的 Docker 使用 /etc/docker/daemon.json（Linux） 或者 %programdata%\docker\config\daemon.json（Windows） 来配置 Daemon。请在该配置文件中加入（没有该文件的话，请先建一个）：

```
{
  "registry-mirrors": ["http://hub-mirror.c.163.com"]
}
```

## 四、几个问题
1. 采用yum安装的方式，docker安装到哪里去了？
2. 需要配置docker环境变量吗？
3. 使用sudo方式安装docker会出现权限的问题。docker文件其属主为root，权限为rw,可读可写；其属组为docker，权限为rw,可读可写。如果要当前用户可直接读取该文件，那么我们就把当前用户添加到docker属组即可。
```
#如果还没有 docker group 就添加一个：
$ sudo groupadd docker
#将用户加入该 group 内。然后退出并重新登录就生效啦。
$ sudo gpasswd -a ${USER} docker
$ sudo usermod -a -G gourpA user
#重启 docker 服务
$ sudo service docker restart
#切换当前会话到新 group 或者重启 X 会话
$ newgrp - docker
```
