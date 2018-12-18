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
  3. 使用Junit测试单元创建测试类，为测试准备好before和after方法：
  ```
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

import java.io.IOException;
import java.net.URI;

/*
Hadoop HDFS Java API 使用
 */
public class HDFSApp {
    //根据core-site.xml配置的fs.default.name
    public final String HDFS_PATH="hdfs://localhost:9000";

    //FileSystem抽象类表示与Hadoop上一个文件系统交互的客户端接口
    FileSystem fileSystem=null;
    Configuration configuration=null;
    @Before
    public void setUp() throws Exception{
        System.out.println("HDFSApp.setUp()");
        configuration= new Configuration();
        fileSystem=FileSystem.get(new URI(HDFS_PATH),configuration,"Leon");

    }
   
   @After
    public void tearDown(){
        configuration=null;
        fileSystem=null;
        System.out.println("HDFSApp.tearDown()");
    }
}
  ```
  4. 编写测试方法
   * 创建文件夹mkdir: 
   ```
    @Test
    public void mkdir() throws IOException {
        fileSystem.mkdirs(new Path("/test/"));
    }
   ```
   * 创建文件
   ```
   @Test
   public void create() throws IOException {
        FSDataOutputStream output=fileSystem.create(new Path("/test/a.txt"));
        output.write("Hello World!".getBytes());
        output.flush();
        output.close();
    }
   ```
   * 查看文本文件
   ```
   @Test
    public void cat() throws Exception{
        FSDataInputStream in= fileSystem.open(new Path("/test/a.txt"));
        IOUtils.copyBytes(in,System.out,1024);
    }
   ```
   * 修改文件名
   ```
   @Test
   public void rename() throws Exception{
        Path oldPath= new Path("/test/a.txt");
        Path newPath= new Path("/test/b.txt");
        System.out.println(fileSystem.rename(oldPath,newPath));
        //返回ture表示修改成功
    }
   ```
   * 复制本地文件到HDFS
   ```
   @Test
    public void copyFromLocal() throws Exception{
        Path localPath= new Path("C:\\Repositories\\groups.conf");
        Path hdfsPath= new Path("/test/");
        fileSystem.copyFromLocalFile(localPath,hdfsPath);
    }
   ```
   
    
  
