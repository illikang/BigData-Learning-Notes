# SpringBoot带来了什么变化

相比于传统的Mavan方式创建项目，SpringBoot主要带来了几方面的变化：

## 一、引用jar包的变化。
SpringBoot将很多常用的jar包做了整合处理，并重新命名了jar包。采用SpringBoot方式创建的项目，它会根据我们创建时的选项而集成jar包。jar都是以“spring-boot-starter-xxx”的方式命名。理也并非所有的jar包都提供了Springboot的打包方式，比如mysql-connector-java，依然是采用原来的方式命名。

也就是说，SpringBoot只是一个集成了很多以SpringBoot方式打包命名的jar包的maven项目。Maven的方式依然有效。

理解jar包的层级结构和作用，能帮助我们更好的理解SpringBoot。

  * spring-boot-starter:
  * srping-boot-starter-web;
  * mybaits-spring-boot-starter;

## 二、配置方式的变化
SpringBoot中加入的spring-boot-starter-autoconfigure使得基于SSM的配置方式发生了变化。主要有application.properties和application.yml两种配置方式。

理解配置文件的内容和设置方法，是理解和使用SpringBoot的第二个核心内容。

## 三、项目启动方式的变化
project+Application的类加上@SpringBootApplication注解就可以完成项目的启动。注意扫描组件只能与此类同级或低于此类。

明白了前两点，SpringBoot的使用就很简单了。我们甚至可以不用Spring Initializr的方式创建SpringBoot项目。用Maven创建一个空的工程，根据项目需求，引入SpringBoot的jar包，就可以搭建起适合自己需求的项目。SpringBoot+Duboo，SpringBoot+DubboX项目就是以这样的方式创建的。
