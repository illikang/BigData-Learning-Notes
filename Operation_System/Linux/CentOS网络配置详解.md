* [CentOS网络配置详解](#CentOS网络配置详解)
    * [一、配置文件详解](#一、配置文件详解)
    * [二、常用网络配置](#二、常用网络配置)

# CentOS网络配置详解

## 一、配置文件详解
在RHEL或者CentOS等Redhat系的Linux系统里，跟网络有关的主要设置文件如下：
```
/etc/host.conf            //配置域名服务客户端的控制文件
/etc/hosts           //完成主机名映射为IP地址的功能
/etc/resolv.conf     //域名服务客户端的配置文件，用于指定域名服务器的位置
/etc/sysconfig/network   //包含了主机最基本的网络信息，用于系统启动
/etc/sysconfig/network-script/   //系统启动时初始化网络的一些信息
/etc/xinetd.conf      //定义了由超级进程xinetd启动的网络服务
/etc/networks        //完成域名与网络地址的映射
/etc/protocols       //设定了主机使用的协议以及各个协议的协议号
/etc/services        //设定主机的不同端口的网络服务
```
1. /etc/host.conf文件的默认信息如下：
```
multi on     #允许主机拥有多个IP地址
order hosts,bind   #主机名解析顺序，即本地解析，DNS域解析的顺序
```
这个文件一般不需要我们修改，默认的解析顺序是本地解析,DNS服务器解析，也就是说在本系统里对于一个主机名首先进行本地解析，如果本地解析没有，然后进行DNS服务器解析。

2. /etc/hosts文件默认的内容大概如下：
```
127.0.0.1  butbueatiful   localhost.localdomain localhost
::1   localhost6.localdomain6 localhost6
```
默认的情况是本机ip和本机一些主机名的对应关系，第一行是IPv4信息，第二行是IPv6信息，如果用不上IPv6本机解析，一般该行注释掉。第一行的解析效果是butbueatiful localhost.localdomain localhost都会被解析成127.0.0.1。可以用Ping试试。

3. /etc/resolv.conf, 指定域名解析的DNS服务器IP等信息, 配置参数一般接触到的有4个：
```
nameserver   #指定DNS服务器的IP地址
domain      #定义本地域名信息
search      #定义域名的搜索列表
sortlist    #对gethostbyname返回的地址进行排序
```
最常用的配置参数是nameserver,其他的可以不配置，这个参数指定了DNS服务器的IP地址，如果设置不正确，就无法进行正常的域名解析。一般来说，推荐设置2个DNS服务器，比如我们用goole的免费DNS服务器，那么该文件的设置如下：
```
nameserver 8.8.8.8
nameserver 8.8.4.4
```
这个文件很危险，如果被人恶意改成了他自己的DNS服务器，他就可以为所欲为的控制你通过域名访问的每个目的地了，这就是常说的DNS劫持。

4. /etc/sysconfig/network, 典型的配置如下：
```
NETWORKING=yes     #设置网络是否有效
NETWORKING_IPV6=no   #设置IPV6网络是否有效
HOSTNAME=butbueatiful    #设置服务器的主机名，最好和/etc/hosts里设置一样，否则在使用一些程序的时候会有问题。
GATEWAY=192.168.0.1   #指定默认网关
```

5. ifcfg-ethX, 设置对应网口的IP等信息, 比如第一个网口, 那么就是/etc/sysconfig/network-scripts/ifcfg-eth0，配置例子：
```
DEVICE="eth0"  #设备名，不要自己乱改，和文件ifcfg-ethX里的ethX要一致
BOOTPROTO="static"   #开机协议，最常见三个参数：static(静态IP)，none(不指定），dhcp(动态获得IP相关信息）
BROADCAST="192.168.0.255"    #广播地址
HWADDR="00:16:36:1B:BB:74"   #物理地址，这个不要乱改
IPADDR="192.168.0.100"    #IP地址
NETMASK="255.255.255.0"   #子网掩码
ONBOOT="yes"    #启动或者重启网络时，是否启动该设备
```

6. route-ethX,配置网口ethX的路由信息。比如第一个网口eth0的路由信息,那么就是/etc/sysconfig/network-scripts/route-eth0，如果没有该文件，需要手动创建并设置以后，次啊能为网口设置静态路由

## 二、常用网络配置
历来Linux系统中修改参数的方式通常有：命令、修改配置文件两种。其中通过命令设置可以立即生效但重启后将失效，通过文件修改实现永久生效，但不会立即生效。Linux中网络参数大致包含以下内容：
  * IP地址
  * 子网掩码
  * 网关
  * DNS服务器
  * 主机名

1. 命令方式设置网络参数

6.0系统以前的版本，用ifconfig和route命令
```
ifconfig    #查看与设置IP地址、子网掩码，命令格式：ifconfig 接口  选项|地址
# ifconfig eth0 up     # 开启eth0网卡
# ifconfig eth0 down   # 关闭网卡
# ifconfig eth0 -arp     # 开启网卡的混合模式
# ifconfig  eth0  promisc   # 关闭网卡的混个模式
# ifconfig  eth0  mtu 1400  # 设置eth0网卡的最大传输单元为1400
# ifconfig  eth0  192.168.0.2/24  # 设置eth0网卡IP地址
# ifconfig  eth0  192.168.0.2  netmask 255.255.255.0   # 功能同上
hostname  #主机名设置
# hostname        # 查看主机名   
# hostname  butbueatiful.com    # 设置主机名为butbueatiful.com
route    #网关设置
# route  add [-net|-host] target [netmask] gw
# oute  del [-net|-host] target [netmask] gw
# route add  -net 192.168.3.0/24  gw  192.168.0.254    # 设置到192.168.3.0网段的网关为192.168.0.254
# route add  -net 192.168.3.0 netmask 255.255.255.0  gw  192.168.0.254    # 功能同上
# route add  -host 192.168.4.4  gw  192.168.0.254    # 设置到192.168.4.4主机的网关为192.168.0.254
# route del  -net 192.168.3.0/24                        # 删除192.168.3.0网段的网关信息
# route del  -host 192.168.4.4                        # 删除192.168.4.4主机的网关信息
# route add default gw  192.168.0.254                # 设置默认网关为192.168.0.254
# route del default gw  192.168.0.254                # 删除默认网关为192.168.0.254
```

6.0以后的CentOS版本不在推荐ifconfig和routh命令，推荐使用ip命令：
```
# ip link show                  # 显示网络接口信息
# ip link set eth0 upi          # 开启网卡
# ip link set eth0 down         # 关闭网卡
# ip link set eth0 promisc on   # 开启网卡的混合模式
# ip link set eth0 promisc offi # 关闭网卡的混个模式
# ip link set eth0 txqueuelen 1200    # 设置网卡队列长度
# ip link set eth0 mtu 1400     # 设置网卡最大传输单元
# ip addr show                  # 显示网卡IP信息
# ip addr add 192.168.0.1/24 dev eth0 # 设置eth0网卡IP地址192.168.0.1
# ip addr del 192.168.0.1/24 dev eth0 # 删除eth0网卡IP地址
# ip route list                 # 查看路由信息
# ip route add 192.168.4.0/24  via  192.168.0.254 dev eth0 # 设置192.168.4.0网段的网关为192.168.0.254,数据走eth0接口
# ip route add default via  192.168.0.254  dev eth0    # 设置默认网关为192.168.0.254
# ip route del 192.168.4.0/24    # 删除192.168.4.0网段的网关
ip route del default    # 删除默认路由
```

2. 修改配置文件设置网络参数
```
# cat  /etc/sysconfig/network-scripts/ifcfg-eth0
DEVICE="eth0"              设备名
NM_CONTROLLED="yes"        设备是否被NetworkManager管理
ONBOOT="no"                开机是否启动
HWADDR="00:0C:29:59:E2:D3" 硬件地址（MAC地址）
TYPE=Ethernet              类型
BOOTPROTO=none             启动协议{none|dhcp}
IPADDR=192.168.0.1         IP地址
PREFIX=24                  子网掩码
GATEWAY=192.168.0.254      默认网关
DNS1=202.106.0.20          主DNS
DOMAIN=202.106.46.151      辅助DNS
UUID=5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03    设备UUID编号
# cat /etc/sysconfig/network
HOSTNAME=butbueatiful.com    主机名
```

注意：在5.0时代DNS服务器写在 /etc/resolv.conf 文件中，但到了6.0时代DNS可以写在/etc/resolv.conf但是此时需要在 /etc/sysconfig/network-scripts/ifcfg-eth0 文件中添加 PEERDNS=no 配置，不然每次重启网卡就会重写/etc/resolv.conf文件的内容，当然了也可以直接写在 /etc/sysconfig/network-scripts/ifcfg-eth0 文件中。
