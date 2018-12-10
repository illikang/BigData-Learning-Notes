# Nginx的安装(CentOS环境)

## 一、Nginx运行环境配置

1. Nginx的运行需要c++编译环境，因此需要确保CentOS环境中已经安装了C++
```
//查看系统中是否已经安装了C++
rpm -qa|grep gcc*
//yum安装C++
yum install gcc-c++
```
2. 安装模块依赖库
  * pcre库
  ```
  yum install pcre*
  ```
  * openssl库
  ```
  yum install openssl*
  ```
  * zlib库
  ```
  yum install zlib*
  ```
  * 在CentOS没有网络环境的情况下，可以使用下载好的压缩包来安装
  ```
  //安装openssl
  tar -zxvf openssl-fips-2.0.16.tar.gz
  cd openssl-fips-2.0.16
  ./config
  make
  make install
  //其他模块都是同样的方法，先解压，然后config，然后make,然后make install
  ```

## 二、Nginx安装
```
tar -zxvf nginx-1.12.0.tar.gz
cd nginx-1.12.0.tar.gz
./config
make
make install
```

## 三、开放80端口
```
//修改/etc/sysconfig/iptables
sudo vim /etc/sysconfig/iptables
```

## 四、Nginx常用命令
```
usr/local/nginx/sbin/nginx   //启动Nginx
http://服务器IP      //访问Nginx
usr/local/nginx/sbin/nginx -s stop   //停止
usr/local/nginx/sbin/nginx -s reload   //重启
usr/local/nginx/sbin/nginx -t  //检查配置文件nginx.conf是否合法
```
