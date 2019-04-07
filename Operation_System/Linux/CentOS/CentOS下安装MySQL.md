# CentOS下安装MySQL

1. 获取压缩包并解压缩到/usr/local目录下(管理员)
  ```
  $tar -zxvf mysql-5.7.1*tar.gz /usr/local/
  ```
2. 更改解压缩后的文件名为mysql
  ```
  $cd /user/local/
  $mv mysql-5.7.1*tar.gz mysql
  ```
3. 在mysql目录下创建目录data
  ```
  $mkdir /usr/local/mysql/data/
  ```
4. 创建用户及用户组mysql,并为用户设置密码
  ```
  $useradd mysql
  $passwd mysql
  ```
5. 切换到mysql用户，初始化数据库
  ```
  $cd /user/local/mysql/bin/
  $./mysqld --initlize --user mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data
  # mysql会生成一个随机密码：A temporary password is generated for root@localhost: GnB<1tl!7HDe
  ```
6. 切换回管理员用户，生成mysql服务
  ```
  $cp /user/local/mysql/supprot-files/mysql.server /etc/init.d/mysql
  ```
7. 设置mysql开机启动
  ```
  $chkconfig mysql on
  ```
8. 启动mysql
  ```
  $service mysql start
  ```
9. 利用临时密码登陆后修改密码
  ```
  /user/local/mysql/bin/mysql -u root -p
  mysql>set password for root@localhost=password('root')
  ```
10. 修改mysql root用户的访问作用域（默认只能loacalhost访问），即修改mysql数据库user表host字段值为‘%’
```
mysql>use mysql;
mysql>select host,user from user;
mysql>update user set host='%'where user='root'
```
11. 用windows工具，比如SqlYog测试访问。
