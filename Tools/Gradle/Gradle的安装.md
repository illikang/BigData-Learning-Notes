# Gradle的安装

## 准备条件
1. Gradle是基于JVM的构建工具，因此安全前请先确保JDK成功安装。
2. 下载Gradle的合适版本：https://gradle.org/releases/

## Windows环境手动安装
1. 在C盘(根据自己需要)创建文件夹Gralde.
2. 解压下载的Gradle压缩包，例如：Gradle-5.3.1-bin.zip并将文件夹Gradle-5.3.1放入C:/Gradle中。
3. 配置Windows的环境变量。在Path中添加：C:\Gradle\gradle-5.3.1\bin
4. 验证安装是否成功：在window命令行窗口执行：gradle -v

## Linux或Mac环境手动安装
1. 创建合适的文件夹，并将下载的Gradle解药到文件夹中
```
$ mkdir /opt/gradle
$ unzip -d /opt/gradle gradle-5.3.1-bin.zip
$ ls /opt/gradle/gradle-5.3.1
```
2. 配置环境变量
修改/etc/profile文件,添加如下内容：
```
$ export PATH=$PATH:/opt/gradle/gradle-5.3.1/bin
```
