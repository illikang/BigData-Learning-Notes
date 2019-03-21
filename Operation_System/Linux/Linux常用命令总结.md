# 常用命令总结

Linux命令基本格式：
```
$command 选项 参数
```

## 文件和目录操作
  1. 创建层级目录：
  ```
  $mkdir -p a/b/c   # 如果父目录不存在，自动创建
  ```
  2. 重命名文件：
  ```
  $mv name1 name2  #当nam1和name2在同一路径下时，为重命名。（可以是文件或文件夹）
  ```
  3. 移动文件：
  ```
  $mv name1 name2 #当name1和name2不在同一路径下，为移动。（可以是文件或文件夹）
  ```
  4. 删除文件或目录：
  ```
  $rm -rf name1  #删除name1。（可以是文件或文件夹）
  ```
  5. 复制文件或目录：
  ```
  $cp -rf name1 name2   #复制name1(目录或文件)到同目录下并重命名为name2
  ```

## 压缩和解压缩
  ```
  $tar -zxvf aa**tar.gz   #解压缩tar.gz文件
  $tar -zcvf aa**tar.gz   #压缩tar.gz文件
  $tar -xvf aa**tar.gz    #解压缩tar文件
  ```

## 文档操作

  1. vi或vim编辑文档（查看其他教程）
  2. 查看文档
  ```
  $cat 1.txt  #查看当前路径下的1.txt文件
  $head -10 1.txt   #查看1.txt文件的前10行
  $tail -10 1.txt   #查看1.txt文件的后10行
  $less 1.txt     #分页查看1.txt文件
  ```

## 用户命令

  1. 添加删除用户（需要管理员账户）
  ```
  $useradd leon    #添加用户leon
  $userdel leon   #删除用户。如果用户已经登陆了不能删除，删除后/home/用户目录依然存在，只是变成普通文件夹了
  ```
  2. 管理用户密码（需要管理员账户）
  ```
  $passwd    #修改root账户的密码
  $passwd leon   #为leon用户添加密码，新用户未设置密码无法使用
  ```
  3. 组操作
  ```
  $groupadd leon   #添加leon组
  $useradd -g group_name user_name   #为用户组添加用户
  $usermod -g group_name user_name   #修改用户的用户组
  ```

## 权限操作

  1. 修改文件或目录权限(需要管理员权限)
  ```
  $chmod -R 777 /home/leon/Desktop/1.txt  # 修改1.txt文件权限为777（7表示读写执行）
  #常见权限：7:wrx读写执行，6:wr-读写，4:-r-读
  ```
  2. 更改文件或目录的所有者（需要管理员权限）
  ```
  $chown -R user.group  修改文件的所有者以及所有组
  ```

## 进程命令

  1. 查看进程
  ```
  $ps -ef   # 查看所有进程的全部信息
  $ps -ef |grep java  #查看所有与java相关的进程
  $jps   #查看java进程
  $kill -9 进程号   #强制关闭进程
  ```

## 服务
所有系统服务都存于：/etc/init.d/
  1. 服务命令
  ```
  $service 服务名 start  #启动服务
  $service 服务名 stop  #停止服务
  $service 服务名 restart  #重启服务
  ```
  2. 服务配置命令
  ```
  $chkconfig 服务名 off   #关闭服务开机启动
  $chkconfig 服务名 on   #开启服务开机启动
  ```
  3. 防火墙服务（iptables）
  ```
  $service iptables stop   #关闭防火墙
  $chkconfig iptables off  #关闭防火墙开机启动
  $vim /etc/sysconfig/iptables    # 开放端口
  -A RH-Firewall-1-INPUT -m state --state NEW -m tcp -p tcp --dport 8080 -j ACCEPT
  $service iptables restart # 重启防火墙

  ```

## 软件管理命令
### yum软件安装与卸载
yum安装为CentOS的在线安装。注意yum安装的一定要用yum卸载。yum安装不适用于生产环境，不好控制版本。

  1. 查看yum仓库软件
  ```
  yum list |grep gedit   # 查看yum仓库中的gedit软件
  $yum info gedit   #查看yum仓库某个软件的所有信息，包括版本
  Release     : 4.el6   centOS 6
  ```
  2. 安装软件（需要管理员权限）
  ```
  $yum install -y gedit   # 默认同意所有选项安装gedit
  ```
  3. 查看已安装的软件
  ```
  $yun installed |grep gedit  # 查看通过Yum已安装的所有gedit软件
  ```
  4. 卸载软件
  ```
  $yum remove -y gedit   # 默认同意所有选项卸载gedit
  ```

### tar解压缩安装（生产环境最常用的方式，版本，配置都是手动控制）
  1. 下载tar安装包
  2. 解压到安装位置
  3. 配置
  4. 启动测试

### RPM软件命令
  1. 查看已安装：
  ```
  $rpm -qa|grep -i mysql   #忽略大小写查看已安装的mysql信息
  ```
  2. 卸载
  ```
  rpm -e --nodeps 软件名   #
  ```
