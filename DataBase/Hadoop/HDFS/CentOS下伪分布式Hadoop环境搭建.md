* [CentOS下伪分布式Hadoop环境搭建](#CentOS下伪分布式Hadoop环境搭建)
    * [一、搭建Hadoop环境的前提条件](#一、搭建Hadoop环境的前提条件)

# CentOS下伪分布式Hadoop环境搭建

## 一、搭建Hadoop环境的前提条件
  1. 操作系统：GNU/Linux是hadoop支持的开发和生产平台，Windows也是hadoop支持平台。这里以CentOS作为演示平台
  2. JAVA：Hadoop是基于Java开发的开源框架，需要JDK环境才能运行。因此需要安装JDK。
  3. SSH：Hadoop脚本使用SSH来管理远程Hadoop后台进程，也就是说,Hadoop使用SSH来完成集群之间的通信。因此，要运行Hadoop，SSH必须安装，并且sshd必须处于运行状态。SSH默认是需要密码连接的，因此需要配置SSH无密码连接。

## 二、环境准备步骤
  1. 安装VMvare虚拟机，并安装CentOS7操作系统。尽量最小安装，尤其不要安装系统自带的openJDK。虚拟机的网络链接采用NAT
  2. 修改系统的hostname和hosts，让机器可以根据hostname识别本机IP地址。比如hostname修改为hadoop，虚拟机IP地址为192.168.40.134，则在host添加
  ```
  192.168.40.134  hadoop
  ```
  3. 关闭防火墙后重启
  ```
  systemctl stop firewalld.service    //关闭防火墙
  systemctl disable firewalld.service    //禁用防火墙
  firewall-cmd-state    //查看防火墙状态

  ```
  4. CentOS默认自带SSH。检查确认SSH已经安装并开启。
  5. 配置SSH无密码登陆。
    1. 生成密钥对
    2. 将公钥放到authorized_keys目录
    3. 添加权限
    4. 测试
    ```
    ssh hadoop@192.168.137.129
    ```
  6. 为Linux安装JDK(这里使用JDK1.8)，并配置JAVA环境变量。

## 三、安装并配置Hadoop

https://blog.csdn.net/lxjzqj2007/article/details/78866250
