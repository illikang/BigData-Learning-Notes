# HDFS Java API 编程

## 引言
HDFS提供了多种与之交互的方式。我们可以通过Shell完成对文件系统的操作，也可以通过Java编程的方式完成对文件系统的操作。
由于Hadoop是基于Java开发的，因此HDFS为我们提供了很多HDFS JAVA API，使用起来非常方便。接下来我们就用一个简单的Java项目，演示HDFS Java API 编程。

## 环境介绍
  * JAVA IDE：IntelliJ IDEA 2018 x64
  * JDK:1.8
  * Maven:3.5.4
  * Hadoop: 2.5.2
  
## 项目创建

  1. 创建Maven项目：由于是简单演示，这里直接用maven的quickstart创建项目
  2. 修改pom.xml，引入hadoop依赖包
  ```
  <!-- https://mvnrepository.com/artifact/org.apache.hadoop/hadoop-client -->
  <dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-client</artifactId>
    <version>2.5.2</version>
  </dependency>
  ```
