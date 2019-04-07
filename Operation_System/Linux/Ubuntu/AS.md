# AS

## 一、安装虚拟机及Ubuntu系统
略

## 二、设置root密码
Ubuntu的默认root密码是随机的，即每次开机都有一个新的root密码。第一个创建的user在admin组，所以他可以给root设置密码：
```
$ sudo passwd root
```
## 三、安装OpenJDK8
1. 检查机器上是否已经安装了JDK：
```
$ java -version
```
2. 如果没有安装，执行：
```
$ sudo apt-get update
$ sudo apt=get upgrade
$ sudo apt install openjdk-8-jre-headless
$ java -version
$ javac
```
3. 如果需要卸载：
```
$ sudo apt remove java
```

## 四、安装Pip和Python
1. 检查机器是否已经安装了Python
```
$ python --version
```
2. 安装Python2.7和Pip
```
$ sudo apt install python-pip
```
3. 验证
```
$ python --verison
$ pip --version
```

## 五、安装Docker和Docker Compose（包安装）
1. 下载安装包（Ubuntu18的内核版本为bionic）
在浏览器中打开地址：https://download.docker.com/linux/ubuntu/dists/bionic/pool/stable/amd64/
2. 下载Docker-ce、Docker-ce-cli以及Containerd.io,例如：
  * containerd.io_1.2.0-1_amd64.deb
  * docker-ce-cli_18.09.0~3-0~ubuntu-bionic_amd64.deb
  * docker-ce_18.03.1~ce~3-0~ubuntu_amd64.deb
3. 按如下顺序执行安装：
```
$ sudo dpkg -i /安装包的路径/containerd.io.deb
$ sudo dpkg -i /安装包的路径/Docker-ce-cli.deb
$ sudo dpkg -i /安装包的路径/Docker-ce.deb
```
4. 验证安装是否成功
```
& sudo docker run hello-world
```
5. 卸载或更新版本
```
$ sudo dpkg -I     #查看已经安装的软件
$ sudo dpgk -r 软件名    #卸载已经安装的软件
#更新版本，重新下载不同版本并安装就可以了
```

## 六、安装NVM和NodeJS
1. 安装NVM
```
$wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
# 下载并安装到‘/home/user/.nvm路径，并自动设置环境变量’
```
2. 验证NVM是否安装成功（）
```
#重新打开命令行窗口
$ command -v nvm
#输出nvm
```
3. 利用NVM安装Nodejs
```
$ nvm install stable   #安装最稳定Node版本
$ nvm install 6.3.0   #安装Node 6.3.0版本
$ node -v    #验证当前node版本
```

## 七、安装配置AWS cli
1. 安装AWS CLI
```
$ sudo apt install awscli
```
2. 验证版本与安装是否成功
```
$ aws --version
```
2. 配置CLI
在AWS控制台的My Secrity Credential上生成Key和Password,然后再控制台执行命令：
```
asw configure
# 输入key和Pass
# 选择默认区域
# 选择格式JSON
```
## 八、安装Ruby环境
1. 安装版本管理工具rbenv或rvm
```
$ sudo apt install git
$ curl -fsSL https://github.com/rbenv/rbenv-installer/raw/master/bin/rbenv-installer | bash
$ wget -q https://github.com/rbenv/rbenv-installer/raw/master/bin/rbenv-installer -O- | bash
# 安装路径：~/.rbenv
```
2. 检验是否安装成功
```
$ curl -fsSL https://github.com/rbenv/rbenv-installer/raw/master/bin/rbenv-doctor | bash
$ wget -q https://github.com/rbenv/rbenv-installer/raw/master/bin/rbenv-doctor -O- | bash
# 根据doctor提示继续修正安装中的问题
```
3. 使用rbenv安装Ruby：
```
#安装ruby-build插件，用来编译安装Ruby源码
$ apt-get install autoconf bison build-essential libssl-dev libyaml-dev libreadline6 libreadline6-dev zlib1g zlib1g-dev
$ sudo apt-get install -y libssl-dev libreadline-dev zlib1g-dev
$ 安装Ruby
$ rbenv install --list   #列出所有可安装版本
$ rbenv install 2.5.0    #安装Ruby环境2.5.0
```
4. 安装包地址
https://cache.ruby-lang.org/pub/ruby
