# Spring Boot简介
# 什么是Spring Boot

Spring Boot是由Pivotal团队提供的全新框架，其设计目的是用来`简化新Spring应用的初始搭建以及开发过程`。该框架使用了特定的方式来进行配置，从而使开发人员不再需要定义样板化的配置。通过这种方式，Spring Boot致力于在蓬勃发展的快速应用开发领域成为领导者

## Spring Boot有什么好处？
一句话：使项目创建变得简单、快速、方便！

平时如果我们需要搭建一个spring web项目时需要做什么事呢？
  1. 配置web.xml，加载spring和spring mvc
  2. 配置数据库连接，配置spring事务
  3. 配置加载配置文件的读取，开启注解
  4. 配置日志文件
  5. ....

配置完成之后部署Tomcat调试。。。

现在非常流行微服务，如果我这个项目仅仅只是需要发送一个邮件，如果我的项目仅仅是产生一个积分，我都需要这样折腾一遍！是不是很痛苦。

不过，如果有了spring boot,我仅仅只需要非常少的几个配置就可以迅速方便的搭建起来一套web项目或者构建一个微服务。是不是很爽快！

## Spring Boot的作用总结
  1. 简化编码
  2. 简化配置
  3. 简化部署
  4. 简化监控

## 使用Spring Boot快速搭建一个“Hello World”Spring框架项目
  1. 新建Project,选择Spring Initializr,点击Next。
  2. 设置项目Maven坐标。
  3. 项目依赖的引入。选择web选项下的web,引入Spring Web容器。
  4. 点击Finish，项目创建完成。

## 项目结构介绍
  1. pom文件关键代码
  ```
  //父级依赖，统一管理与父级依赖相关的版本信息
  <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.1.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
  </parent>
  //starter-web:引入与springboot相关的web容器以及与web开发相关的jar包
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
  //starter-test：引入与test相关的jar包
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
  </dependency>
  ```
  2. 启动类：项目名称+Application

  Spring Boot自动创建的类，有两个作用：1.启动项目，默认Tomcat；2，启动自动配置（通过注解@SpringBootApplication来实现,包含了3个关键注解@SpringBootConfiguration，@EnableAutoConfiguration和@ComponentScan）

  3. application.properties
