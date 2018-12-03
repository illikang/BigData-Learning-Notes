#Hadoop环境搭建

## Hadoop版本选择
Hadoop大致可分为Apache Hadoop和第三方发行第三方发行版Hadoop，考虑到Hadoop集群部署的高效，集群的稳定性，以及后期集中的配置管理，业界多使用Cloudera公司的发行版，简称为CDH。

下面是转载的Hadoop社区版本与第三方发行版本的比较：

Apache社区版本

优点：

完全开源免费。
社区活跃
文档、资料详实
缺点：

复杂的版本管理。版本管理比较混乱的，各种版本层出不穷，让很多使用者不知所措。
复杂的集群部署、安装、配置。通常按照集群需要编写大量的配置文件，分发到每一台节点上，容易出错，效率低下。
复杂的集群运维。对集群的监控，运维，需要安装第三方的其他软件，如ganglia，nagois等，运维难度较大。
复杂的生态环境。在Hadoop生态圈中，组件的选择、使用，比如Hive，Mahout，Sqoop，Flume，Spark，Oozie等等，需要大量考虑兼容性的问题，版本是否兼容，组件是否有冲突，编译是否能通过等。经常会浪费大量的时间去编译组件，解决版本冲突问题。
 第三方发行版本（如CDH，HDP，MapR等）

优点：

基于Apache协议，100%开源。
版本管理清晰。比如Cloudera，CDH1，CDH2，CDH3，CDH4等，后面加上补丁版本，如CDH4.1.0 patch level 923.142，表示在原生态Apache Hadoop 0.20.2基础上添加了1065个patch。
比Apache Hadoop在兼容性、安全性、稳定性上有增强。第三方发行版通常都经过了大量的测试验证，有众多部署实例，大量的运行到各种生产环境。
版本更新快。通常情况，比如CDH每个季度会有一个update，每一年会有一个release。
基于稳定版本Apache Hadoop，并应用了最新Bug修复或Feature的patch
提供了部署、安装、配置工具，大大提高了集群部署的效率，可以在几个小时内部署好集群。
运维简单。提供了管理、监控、诊断、配置修改的工具，管理配置方便，定位问题快速、准确，使运维工作简单，有效。
缺点：

涉及到厂商锁定的问题。（可以通过技术解决）

## CDH和Cloudera Manager
CDH (Cloudera's Distribution, including Apache Hadoop)，是Hadoop众多分支中的一种，由Cloudera维护，基于稳定版本的Apache Hadoop构建，并集成了很多补丁，可直接用于生产环境。

Cloudera Manager则是为了便于在集群中进行Hadoop等大数据处理相关的服务安装和监控管理的组件，对集群中主机、Hadoop、Hive、Spark等服务的安装配置管理做了极大简化。

## Hadoop下载：CDH5 download path:
http://archive-primary.cloudera.com/cdh5/cdh/5/
wget http://archive-primary.cloudera.com/cdh5/cdh/5/hadoop-2.6.0-cdh5.7.0.tar.gz

## 系统环境搭建
### 机器参数设置
hostname:etc/sysconfig/network
### 设置ip和hostname的映射关系
/etc/hosts

### SSH安装
centOS
yum install SSH
Ubuntu
sudo apt-get install ssh
sudo apt-get install rsync
### 配置SSH免密码登陆
ssh-keygen -t rsa

### JDK下载安装：
下载JDK后解压到目标路经
配置JAVA静态变量.bash_profile
export JAVA_HOME=""
export PATH=""
source


## 安装Hadoop
1. 解压缩下载包即可
## HDFS配置文件参数设置
  hadoop-env.sh
  core-site.xml
  hdfs-site.xml
