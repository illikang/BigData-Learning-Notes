* [CentOS软件管理之YUM](#CentOS软件管理之YUM)
    * [工作区](#工作区)


# CentOS软件管理之YUM
## 概述
yum,是Yellow dog Updater,Modified的简称，是杜克大学为了提高RPM软件包安装性而开发的一种软件包管理器(CentOS另一个RMP软件包管理工具是RPM)。yum的宗旨是自动化地升级，安装和移除rpm包，收集rpm包的相关信息，检查依赖性并自动提示用户解决。

yum的关键之处是要有可靠的repository，顾名思义，这是软件的仓库，它可以是http或ftp站点，也可以是本地如软件池，但必须包含rpm的header，header包括了rpm包的各种信息，包括描述，功能，提供的文件，依赖性等。正是收集了这些header并加以分析，才能自动化地完成余下的任务。

yum 的理念是使用一个中心仓库(repository)管理一部分甚至一个distribution 的应用程序相互关系，根据计算出来的软件依赖关系进行相关的升级、安装、删除等等操作，减少了Linux 用户一直头痛的dependencies 的问题。这一点上，yum 和apt 相同。apt 原为debian 的deb 类型软件管理所使用，但是现在也能用到RedHat 门下的rpm 了。

yum 主要功能是更方便的添加/删除/更新RPM 包，自动解决包的倚赖性问题，便于管理大量系统的更新问题。yum 可以同时配置多个资源库(Repository)，简洁的配置文件（/etc/yum.conf），自动解决增加或删除rpm 包时遇到的依赖性问题，保持与RPM 数据库的一致性。

## yun安装与配置
1. CentOS默认已经安装了yum。查看系统默认安装的yum
```
$ rpm -qa|grep yum
```
2. yum配置
yum的配置文件分为两部分：main和repository
  * main部分定义了全局配置选项，整个yum配置文件应该只有一个main。常位于/etc/yum.conf中。
  ```
  achedir=/var/cache/yum/$basearch/$releasever
  //yum 缓存的目录，yum 在此存储下载的rpm 包和数据库，默认设置为/var/cache/yum
  keepcache=0
  ////安装完成后是否保留软件包，0为不保留（默认为0），1为保留
  debuglevel=2
  //Debug 信息输出等级，范围为0-10，缺省为2
  logfile=/var/log/yum.log
  //yum 日志文件位置。用户可以到/var/log/yum.log 文件去查询过去所做的更新。
  exactarch=1
  //有1和0两个选项，设置为1，则yum 只会安装和系统架构匹配的软件包，例如，yum 不会将i686的软件包安装在适合i386的系统中。默认为1。
  obsoletes=1
  //这是一个update 的参数，具体请参阅yum(8)，简单的说就是相当于upgrade，允许更新陈旧的RPM包。
  tolerant=1
  //有1和0两个选项，表示yum 是否容忍命令行发生与软件包有关的错误，比如你要安装1,2,3三个包，而其中3此前已经安装了，如果你设为1,则yum 不会出现错误信息。默认是0。
  retries=6
  //网络连接发生错误后的重试次数，如果设为0，则会无限重试。默认值为6.
  gpgcheck=1  
  // 有1和0两个选择，分别代表是否是否进行gpg(GNU Private Guard) 校验，以确定rpm 包的来源是有效和安全的。这个选项如果设置在[main]部分，则对每个repository 都有效。默认值为0。
  plugins=1
  //是否启用插件，默认1为允许，0表示不允许。我们一般会用yum-fastestmirror这个插件
  installonly_limit=5
  bugtracker_url=http://bugs.centos.org/set_project.php?project_id=23&ref=http://bugs.centos.org/bug_report_page.php?category=yum
  distroverpkg=centos-release
  ```
  * 本地源配置（repository）。yum的本地源配置文件在/etc/yum.repos.d/目录下：
  ```
  $ cd /etc/yum.repos.d/
  $ ls -l
  //将CentOS-Base.repo改名为CentOS-Base.repo.bak表示禁用默认的yum网络源。否则会先在网络源中寻找适合的包，改名后直接从本地源读取
  -rw-r--r--. 1 root root 1664 Aug 30  2017 CentOS-Base.repo
  //CentOS-Base.repo 是yum 网络源的配置文件
  -rw-r--r--. 1 root root 1309 Aug 30  2017 CentOS-CR.repo
  -rw-r--r--. 1 root root  649 Aug 30  2017 CentOS-Debuginfo.repo
  -rw-r--r--. 1 root root  314 Aug 30  2017 CentOS-fasttrack.repo
  -rw-r--r--. 1 root root  630 Aug 30  2017 CentOS-Media.repo
  //entOS-Media.repo 是yum 本地源的配置文件
  -rw-r--r--. 1 root root 1331 Aug 30  2017 CentOS-Sources.repo
  -rw-r--r--. 1 root root 3830 Aug 30  2017 CentOS-Vault.repo
  $ ls -l cat CentOS-Media.repo
  [c7-media]
  name=CentOS-$releasever - Media
  baseurl=file:///media/CentOS/
        file:///media/cdrom/
        file:///media/cdrecorder/
  gpgcheck=1
  enabled=0
  gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
  ```
  * 配置国内yum源。修改/etc/yum.repos.d/CentOS-Base.repo：
  ```
  #上海交通大学yum源
  [base]
  name=CentOS-$releasever - Base
  #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os
  baseurl=http://ftp.sjtu.edu.cn/centos/$releasever/os/$basearch/
  gpgcheck=1
  gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5
  #released updates
  name=CentOS-$releasever - Updates
  #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates
  baseurl=http://ftp.sjtu.edu.cn/centos/$releasever/updates/$basearch/
  gpgcheck=1
  gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5
  #additional packages that may be useful
  [extras]
  name=CentOS-$releasever - Extras
  #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras
  baseurl=http://ftp.sjtu.edu.cn/centos/$releasever/extras/$basearch/
  gpgcheck=1
  gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5
  #additional packages that extend functionality of existing packages
  [centosplus]
  name=CentOS-$releasever - Plus
  #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus
  baseurl=http://ftp.sjtu.edu.cn/centos/$releasever/centosplus/$basearch/
  gpgcheck=1
  enabled=0
  gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5
  #contrib - packages by Centos Users
  [contrib]
  name=CentOS-$releasever - Contrib
  #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=contrib
  baseurl=http://ftp.sjtu.edu.cn/centos/$releasever/contrib/$basearch/
  gpgcheck=1
  enabled=0
  gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5
  ```
## yum软件管理
  1. rpm包的安装和删除
  ```
  yum install xxx(软件名)
  yum remove xxx(软件名)
  ```
  2. rpm包的更新
  ```
  yum check -update    //检查可以更新的软件包
  yum update      //更新所有的软件包
  yum update kernel   //更新特定的软件包
  yum upgrade    //大规模的升级
  ```
  3. yum缓存的相关信息
  ```
  yum clean packages   //清楚缓存中rpm包文件
  yum clean headers    //清楚缓存中rpm的头文件
  yum clean old headers    //清楚缓存中旧的rpm头文件
  yum clean all    //清楚缓存中的rpm头文件和包文件
  ```
  4. 软件包信息查询
  ```
  yum list    //列出资源库中所有可以安装或更新的rpm包
  yum list firefox*   //列出资源库中特定的安装或更新以及已经安装的rpm包
  yum list updates   //列出资源库中所有可以安装或更新的rpm包
  yum list installed
  yum list extras   //列出已经安装但是不包含在资源库中的rpm包
  yum info firefox*
  yum search firefox   //搜索匹配特定字符的rpm包
  ```
