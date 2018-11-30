* [Window下Hadoop环境搭建](#Window下Hadoop环境搭建)
    * [概述](#概述)

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
    1. 编辑“D:\hadoop-2.5.2\etc\hadoop”下的core-site.xml文件，修改<configuration>如下：
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
    2. 编辑“D:\hadoop-2.5.2\etc\hadoop”下的mapred-site.xml（没有就将mapred-site.xml.template重命名为mapred-site.xml）：
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
    3. 编辑“D:\hadoop-2.5.2\etc\hadoop”下的hdfs-site.xml:
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
    4. 编辑“D:\hadoop-2.5.2\etc\hadoop”目录下的yarn-site.xml文件:
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
    5. 根据以上配置，在路径"D:\hadoop-2.5.2\workplace"下创建文件夹：tmp,name,data
  6. 为Hadoop配置JDK路径

    编辑“D:\hadoop-2.5.2\etc\hadoop”目录下的hadoop-env.cmd，将JAVA_HOME用 @rem注释掉，编辑为JAVA_HOME的路径：
    ```
    @rem set JAVA_HOME=%JAVA_HOME%
    set JAVA_HOME=C:\PROGRA~1\Java\jdk1.8.0_181
    ```
    注意：路径中不能有空格，“=”号前后不要有空格，如果Jave路径在program files路径下，改为PROGRA~1。
  7. 加入第三方工具：将下载好的hadooponwindows-master.zip解压，将将解压后的bin目录下的所有文件直接覆盖Hadoop的bin目录。
  8. 运行环境
    1. 运行cmd窗口，执行hdfs namenode -format。
    2. 运行cmd窗口，切换到hadoop的sbin目录，执行start-all.cmd，它将会启动以下4个进程窗口，则说明配置成功：
    ![](../img/hadoop4.png)
    namenode,datanode,resourcemanager,nodemanager四个进程

## 补充
  1. 资源管理GUI:http://localhost:8088/；
  2. 节点管理GUI:http://localhost:50070/
