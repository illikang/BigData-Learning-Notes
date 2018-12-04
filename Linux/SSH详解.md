* [SSH详解](#SSH详解)
    * [什么是SSH](#什么是SSH)
    * [SSH是如何保证安全的？](#SSH是如何保证安全的？)
    * [SSH两种级别安全验证](#SSH两种级别安全验证)
    * [LinuxSSH配置](#LinuxSSH配置)
    * [SSH基本用法](#SSH基本用法)
# SSH详解

## 什么是SSH
SSH为Secure Shell的缩写，是专为远程登陆会话和其他网络服务提供安全性的协议。利用SSH协议可以有效防止远程管理过程中的信息泄露问题。SSH最初是UNIX系统上的一个程序，后来又迅速拓展到其他操作平台。SSH在正确使用时可弥补网络中的漏洞。几乎所有UNIX平台，包括HP-UX、Linux、AIX、Solaris、Digital UNIX、Irix，都可运行SSH

## SSH是如何保证安全的？
传统的网络服务程序，如：ftb,pop和telnet在本质上都是不安全的，因为他们在网络上用明文传送口令和数据，别有用心的人非常容易就可以截获这些口令和数据。而且，这些服务程序的安全验证方式也是有其弱点的，就是很容易受到“中间人”这种方式的攻击。所谓“中间人”的攻击方式，就是“中间人”冒充真正的服务器接收你传给服务器的数据，然后再冒充你把数据传给真正的服务器。服务器和你之间的数据传送被“中间人”一转手做了手脚之后，就会出现很严重的问题。通过使用SSH，你可以把所有传输的数据进行加密，这样“中间人”这种攻击方式就不能实现了，而且也能够方式DNSA欺骗和IP欺骗。使用SSH，还有一个额外的好处就是传输的数据是经过压缩的，所以可以加快传输的速度。SSH有很多功能，它既可以代替Telnet，又可以为FTP、PoP、甚至为PPP提供一个安全的"通道"。

## SSH两种级别安全验证
  1. 基于口令的安全验证

  只要你知道自己的账号和口令，就可以登陆到远程主机。所有传输的数据都会被加密，但是不能保证你正在连接的服务器就是你想连接的服务器。可能会有别的服务器在冒充真正的服务器，也就是受到“中间人”这种方式的攻击。
  
  2. 基于密钥的安全验证

  需要依靠密钥，也就是你必须为自己创建一对密钥，并把公用密钥放在需要访问的服务器上。如果你要连接到SSH服务器上，客户端软件就会向服务器发出请求，请求用你的密钥进行安全验证。服务器收到请求之后，先在该服务器上你的主目录下寻找你的公用密钥，然后把它和你发送过来的公用密钥进行比较。如果两个密钥一致，服务器就用公用密钥加密“质询”(challenge)并把它发送给客户端软件。客户端端软件收到“质询”之后就可以用你的私人密钥解密再把它发送给服务器。这个级别不仅加密所有传送的数据，而且“中间人”这种攻击方式也是不可能的。

## LinuxSSH配置
### LinuxSSH组成
SSH是远程连接工具，由服务端和客户端两部分组成：
  * 服务端（一般用于Linux系统）：openssh-server(提供SSH服务)，openssl(提供加密程序)
  * 客户端（Window系统和Linux系统）：ssh-client(Linux平台)，Xshell（Windows平台），Securecrt,Mobaxterm等。

### SSH安装与配置
1. 查看SSH是否已经安装
```
rpm -qa|grep -E "openssh"
```
2. 查看SSH是否已经启动：
```
ps -e|grep sshd
//  ssh-agent表示客户端启动
//  ssdh表示服务器端启动
```
3. 客户端和服务端安装（只需要登陆别的机器，只需安装openssh-client，如果需要本机开放SSH服务就需要安装openssh-server）:
  * Ubuntu:有默认安装，如果没有,执行命令：
  ```
  sudo apt-get install openssh-client
  ```
  * CentOS（默认安装了openssh）如果没有安装，执行:
  ```
  sudo yum install openssh*
  ```
4. 配置OpenSSH服务（默认的配置已可以正常工作，主配置文件：/etc/ssh/sshd_config）
  ```
  Port=22  设置SSH的端口号是22(默认端口号为22)

  Protocol 2  启用SSH版本2协议

  ListenAddress 192.168.0.222  设置服务监听的地址

  DenyUsers   user1 user2 foo  拒绝访问的用户(用空格隔开)

  AllowUsers  root osmond vivek  允许访问的用户(用空格隔开)

  PermitRootLogin  no  禁止root用户登陆

  PermitEmptyPasswords no  用户登陆需要密码认证

  PasswordAuthentication  yes  启用口令认证方式

  ```

## SSH基本用法
1. 开启SSH服务端：
```
sudo systemctl enable sshd
sudo firewall-cmd --zone=public --add-port=22/tcp --permanent
sudo service firewalld restart   //开启防火墙的22端口，或者关闭防火墙也可以
service sshd start     //开启SSH服务器端
chkconfig sshd on      //设置SSH开机运行
```
2. SSH远程口令登陆：
```
ssh user@hostname   //如：ssh leon@192.168.0.111
ssh host         //如果本地用户名与远程用户名一致，登陆时可以省略用户名
ssh -p 2222 user@host   //SSH的默认端口是22，也就是说，你的登陆请求会送进远程主机的22端口。使用-p，可以修改这个端口
```
  如果是第一次登陆，系统会提示：
  The authenticity of host 'host (12.18.429.21)' can't be established.
  RSA key fingerprint is 98:2e:d7:e0:de:9f:ac:67:28:c2:42:2d:37:16:58:4d.
  Are you sure you want to continue connecting (yes/no)?

  这段话的意思是，无法确认host主机的真实性，只知道它的公钥指纹，问你还想继续连接吗？

  所谓"公钥指纹"，是指公钥长度较长（这里采用RSA算法，长达1024位），很难比对，所以对其进行MD5计算，将它变成一个128位的指纹。上例中是98:2e:d7:e0:de:9f:ac:67:28:c2:42:2d:37:16:58:4d，再进行比较，就容易多了。

  当远程主机的公钥被接受以后，它就会被保存在文件$HOME/.ssh/known_hosts之中。下次再连接这台主机，系统就会认出它的公钥已经保存在本地了，从而跳过警告部分，直接提示输入密码。

  每个SSH用户都有自己的known_hosts文件，此外系统也有一个这样的文件，通常是/etc/ssh/ssh_known_hosts，保存一些对所有用户都可信赖的远程主机的公钥。

3. SSH远程公钥登陆：
  使用密码登陆，每次都必须输入密码，非常麻烦。好在SSH还提供了公钥登陆，可以省去输入密码的步骤。
  1. 本地生成密钥对：
  ```
  ssh keygen -t rsa
  //系统会出现一些列提示，一路回车
  ```
  2. 在$HOME/.ssh/目录下，会新生成两个文件：id_rsa.pub和id_rsa。前者是你的公钥，后者是你的私钥。输入下面的命令，将公钥传送到远程主机host上面：
  ```
  ssh-copy-id user@host   //或者采用下面的方式
  $ ssh user@host 'mkdir -p .ssh && cat >> .ssh/authorized_keys' < ~/.ssh/id_rsa.pub
  ```
  3. 如果还是不行，打开远程主机的/etc/ssh/sshd_config文件，检查下面几行前面的“#”注释是否取掉：
  ```
  RSAAuthentication yes
　PubkeyAuthentication yes
　AuthorizedKeysFile .ssh/authorized_keys
  //然后重启远程主机的SSH服务
  //Ubuntu系统
  service ssh restart
  //debian系统
  /etc/init.d/ssh restart
  ```
  4. 补充说明authorized_keys文件。远程主机将用户的公钥，保存在登录后的用户主目录的$HOME/.ssh/authorized_keys文件中。公钥就是一段字符串，只要把它追加在authorized_keys文件的末尾就行了。不管是直接执行ssh-copy-id命令，还是直接修改authorized_keys文件，本质上都是往authorized_keys文件末尾追加内容的过程。
