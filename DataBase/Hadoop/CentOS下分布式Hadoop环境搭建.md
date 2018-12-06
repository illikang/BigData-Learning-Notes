# CentOS下分布式Hadoop环境搭建

## 一、平台环境
  1. 安装CentOS。VMware虚拟机下安装三个CentOS7，如果不熟悉Linux命令，可以安装图形界面的。（最好是先配置一台机器，然后用VM复制，配置三台机器的工作量不小）
  2. 创建系统管理员账户。除了root账号外，为三个系统创建三个相同的管理员用户和密码，比如：创建用户hadoop，创建密码：hadoop
  3. Host配置。我们希望三个主机之间能够使用主机名称的方式相互访问而不是IP，

      * 需要修改三台机器的/etc/hosts文件。三台机器都按相同的方式修改。原有的127.0.0.1和：：1两行保持不动，根据三台机器的IP地址，添加如下内容：
      ```
      192.168.125.131  hadoop1_master
      192.168.125.129  hadoop2_slave1
      192.168.125.130  hadoop3_slave2
      ```
      * 修改每台机器的hostname：
      ```
      sudo vim /etc/比如hostname
      //进入vim以后，根据主机名称以及ID，设置对应的主机名称,注销重新登陆生效
      //比如192.168.125.130的主机设置
      hadoop3_slave2
      ```

  4. 集群部署规划如下：

      ![](img/planonnodes.jpg)


  5. 设置SSH免密钥：

      * CentOS默认已经安装SSH，请先检查确认SSH是否已经安装。然后启动sshd。
      * SSH免密码设置，要求每两台主机之间设置免密码，自己的主机与自己的主机之间也要设置免密码。注意这项操作要在admin用户下执行。
      ```
      //hadoop1_master
      ssh-keygen -t rsa
      ssh-copy-id hadoop1_master   
      //ssh-copy-id 把本地主机的公钥复制到远程主机的authorized_keys文件上，ssh-copy-id 也会给远程主机的用户主目录（home）和~/.ssh, 和~/.ssh/authorized_keys设置合适的权限 。
      ssh-copy-id hadoop2_slave1
      ssh-copy-id hadoop3_slave2
      //hadoop2_slave1
      ssh-keygen -t rsa
      ssh-copy-id hadoop2_slave1
      ssh-copy-id hadoop1_master
      ssh-copy-id hadoop3_slave2
      //hadoop3_slave2
      ssh-keygen -t rsa
      ssh-copy-id hadoop3_slave2
      ssh-copy-id hadoop1_master
      ssh-copy-id hadoop2_slave1
      ```
  6. 测试SSH面密是否成功

      * 通过文件方式查看。查看authorized_keys文件内容（/.ssh/authorized_keys）,末尾是否加入了类似“hadoop@hadoop2_slave1”这样的字符。三个都添加完以后，应该分别有三个以"hadoop@hadoop","",""
      * 通过命令方式验证
      ```
      //hadoop2_slave1
      ssh hadoop1_master
      ssh hadoop2_slave1
      ssh hadoop3_slave2
      //如果SSH创建错误了，不要直接在文件中删除使用下面的命令，移除错误的SSH
      ssh keygen -R hostname
      ```

  7. 安装配置JDK，三个机器一样的操作

      * 检查机器是否已自带openjdk，如果删除自带JDK
      ```
      //检查机器上已有的Java
      rpm -qa |grep java
      java-1.8.0-openjdk-1.8.0.131-11.b12.el7.x86_64
      java-1.7.0-openjdk-1.7.0.141-2.6.10.5.el7.x86_64
      javapackages-tools-3.4.1-11.el7.noarch
      java-1.7.0-openjdk-headless-1.7.0.141-2.6.10.5.el7.x86_64
      tzdata-java-2017b-1.el7.noarch
      java-1.8.0-openjdk-headless-1.8.0.131-11.b12.el7.x86_64
      //删除已有的JDK
      rpm -e --nodeps java-1.8.0-openjdk-1.8.0.131-11.b12.el7.x86_64
      rpm -e --nodeps java-1.7.0-openjdk-1.7.0.141-2.6.10.5.el7.x86_64
      rpm -e --nodeps java-1.7.0-openjdk-headless-1.7.0.141-2.6.10.5.el7.x86_64
      rpm -e --nodeps java-1.8.0-openjdk-headless-1.8.0.131-11.b12.el7.x86_64
      ```
      * Oracle官网下载JDK，可以在虚拟机上安装，也可以Window上下载了借助Xftp工具上传。在CentOS，hadoop用户根目录下创建softward目录用于存放将要安装的工具。
      * 在根目录下创建app目录，用于安装JDK和以后的Hadoop。将下载好的JDK解压到该目录下
      ```
      cd ~
      mkdir app
      cd ~/software/
      tar -zxvf jdk-7u79-linux-x64.tar.gz -C /home/hadoop/app/
      ```
      * 配置JAVA环境变量
      ```
      $ sudo vim /etc/profile
      export JAVA_HOME=/home/hadoop/app/jdk1.7.0_79
      export PATH=$JAVA_HOME/bin:$PATH
      $ source /etc/profile
      ```
      * 测试JDK是否配置好，在任意目录下：
      ```
      $ java -version
      ```
## 二、安装Hadoop集群
   1. 下载CDH5的Hadoop套件到software目录下。(去官网下载Hadoop2.X的版本也可以。这里选择CDH5是为了方便后续的操作)
   2. 安装hadoop。将压缩包解压到app目录下
   ```
   $ tar -zxvf hadoop-2.6.0-cdh5.7.0.tar.gz -C ../app/
   ```
   3. 配置hadoop集群的HDFS,三台机器都按相同方式配置，可以先配置一台机器，然后复制到其他机器。

     * 修改core-site.xml文件
     ```
     <configuration>
     <!-- 指定HDFS中NameNode的地址 -->
        <property>
                <name>fs.defaultFS</name>
                <value>hdfs://hadoop1_master:9000</value>
        </property>
        <!-- 指定hadoop运行时产生文件的存储目录 -->
        <property>
                <name>hadoop.tmp.dir</name>
                <value>/home/hadoop/app/tmp</value>
        </property>
      </configuration>
     ```
     * 修改hadoop-env.sh文件，为hadoop指定JAVA路径
     ```
     # export JAVA_HOME=${JAVA_HOME}
     export JAVA_HOME=/home/hadoop/app/jdk1.7.0_79
     ```
     * 修改hdfs-site.xml文件，设置dfs副本数以及secondnamenode端口
     ```
     <configuration>
     <!-- 设置dfs副本数，不设置默认是3个   -->
        <property>
                <name>dfs.replication</name>
                <value>2</value>
        </property>
        <!-- 设置secondname的端口   -->
        <property>
                <name>dfs.namenode.secondary.http-address</name>
                <value>hadoop2_slave1:50090</value>
        </property>
      </configuration>
     ```
     * 修改slaves，配置datanodes
     ```
     $ sudo vim slaves
     hadoop1_master
     hadoop2_slave1
     hadoop3_slave2
     ```

   4. 配置hadoop集群的YARN和MapReduce
      * 修改mapred-env.sh，配置mapreduce的Java运行环境
      ```
      $ sudo vim mapred-env.sh
      export JAVA_HOME=/home/hadoop/app/jdk1.7.0_79
      ```
      * 修改mapred-site.xml。指定mapreduce运行在yarn上。
      ```
      $ mv mapred-site.xml.template mapred-site.xml
      $ sudo vim mapred-site.xml
      <configuration>
      <!-- 指定mr运行在yarn上 -->
      <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
      </property>
      </configuration>
      ```
      * 修改yarn-site.xml，配置YARN的Java运行环境
      ```
      $ sudo vim yarn-site.xml
      <configuration>
      <!-- reducer获取数据的方式 -->
        <property>
          <name>yarn.nodemanager.aux-services</name>
          <value>mapreduce_shuffle</value>
        </property>
      <!-- 指定YARN的ResourceManager的地址 -->
        <property>
          <name>yarn.resourcemanager.hostname</name>
          <value>hadoop2_slave1</value>
        </property>
      </configuration>
      ```

  5. 配置环境变量，可以只配置Master节点
  ```
  [hadoop@hadoop1_master hadoop]$ sudo vim /etc/profile
  export HADOOP_HOME=/home/hadoop/app/hadoop-2.6.0-cdh5.7.0
  export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
  [hadoop@hadoop1_master hadoop]$ source /etc/profile
  ```
## 三、启动验证集群
  1. 启动集群

  如果是第一次启动，需要格式化
  ```
  $ hdfs namenode -format　
  ```

  2. 启动HDFS：
  ```
  $ hdfs-yarn.sh
  ```
  3. jps查看进程
  ```
  $ jps
  ```
  https://www.cnblogs.com/frankdeng/p/9047698.html
