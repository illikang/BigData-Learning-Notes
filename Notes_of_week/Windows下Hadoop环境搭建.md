* [Window下Hadoop环境搭建](#Window下Hadoop环境搭建)
    * [概述](#概述)
    * [演示环境](#演示环境)
    * [配置步骤](#配置步骤)

# Window下Hadoop环境搭建
## 概述
目前大数据生态主要部分是Hadoop软件框架和Spark内存级计算引擎。

Hadoop是大数据的核心武器。学大数据就必然要尽早开始学习Hadoop。就像学其他计算机一样，学Hadoop必然要结合实践与实际操作，否则必然事倍功半。相反如果能搭建一个Hadoop环境来实际操作和实践的话，必然如鱼得水。

Hadoop是原生的Linux平台工具，Apache官网并没有提供Windows版本的支持。在2.0以前，需要借助cygwin，不过Hadoop2.0及以后的版本可以直接在windows上跑，不用cygwin了。本文详细说明了如何在Windows环境下搭建Hadoop环境。
## 演示环境
  * 操作系统：Win10 企业版
  * JDK：Oracle 1.8
  * Hadoop:2.5.2
  * 第三方工具：hadooponwindows-master

## 配置步骤
  1. 下载所需文件：JDK，Hadoop2.5.2,hadooponwindows
  2. 安装JDK并配置JAVA环境变量
  3. 解压Hadoop到本地目录：D:\hadoop-2.5.2
  4. 配置Hadoop环境变量
    * 新建HADOOP_HOME=D:\hadoop-2.5.2
    * Path中增加：%HADOOP_HOME%\bin
  5.  配置Hadoop文件
   * 编辑“D:\hadoop-2.5.2\etc\hadoop”下的core-site.xml文件，修改<configuration>如下：
   ```
   <configuration>
      <property>
        <name>hadoop.tmp.dir</name>
        <value>/D:/hadoop-2.5.2/workplace/tmp</value>
      </property>
      <property>
        <name>dfs.name.dir</name>
        <value>/D:/hadoop-2.5.2/workplace/name</value>
      </property>
      <property>
        <name>fs.default.name</name>
        <value>hdfs://localhost:9000</value>
      </property>
    </configuration>
   ```
   * 编辑“D:\hadoop-2.5.2\etc\hadoop”下的mapred-site.xml（没有就将mapred-site.xml.template重命名为mapred-site.xml）：
   ```
   <configuration>
      <property>
       <name>mapreduce.framework.name</name>
       <value>yarn</value>
      </property>
      <property>
       <name>mapred.job.tracker</name>
       <value>hdfs://localhost:9001</value>
      </property>
    </configuration>
   ```
   * 编辑“D:\hadoop-2.5.2\etc\hadoop”下的hdfs-site.xml:
   ```
   <configuration>
    <!-- 这个参数设置为1，因为是单机版hadoop -->
      <property>
        <name>dfs.replication</name>
        <value>1</value>
      </property>
      <property>
        <name>dfs.data.dir</name>
        <value>/D:/hadoop-2.5.2/workplace/data</value>
      </property>
    </configuration>
   ```
   * 编辑“D:\hadoop-2.5.2\etc\hadoop”目录下的yarn-site.xml文件:
   ```
   <configuration>
      <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
      </property>
      <property>
        <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
        <value>org.apache.hadoop.mapred.ShuffleHandler</value>
      </property>
    </configuration>
   ```
   * 根据以上配置，在路径"D:\hadoop-2.5.2\workplace"下创建文件夹：tmp,name,data。
   

    
