# Zookeeper编程基础一

## ZooKeeper Data Model(ZooKeeper数据模型)
ZooKeeper有一个类似与分布式文件系统的层级命名空间。唯一不同的是，Zookeeper中的节点还可以绑定数据。这就好比一个文件系统中，一个文件可以同时是一个路径。

![znodes](img/zknamespace.jpg)

### ZNodes
ZooKeeper树中的每一个节点被称为一个znode。比如上图中的“/”,"/app1","/app1/p_1"等等都是一个节点。每个节点除了维护了一个“stat”的数据结构，还可以绑定不超过1M的字符数据。
```
$ create /test test
Created /test
$ get /test
test
cZxid = 0xe7
ctime = Mon Dec 24 10:02:53 CST 2018
mZxid = 0xe7
mtime = Mon Dec 24 10:02:53 CST 2018
pZxid = 0xe7
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 4
numChildren = 0
```
#### ZNodes的种类
  1. 持久节点（PERSISTENT，默认），客户端与zookeeper断开连接后，该节点依旧存在
  ```
  create [-s] [-e] path data acl
  ```
  2. 临时节点（EPHEMERAL），客户端与zookeeper断开连接后，该节点被删除。
  ```
  create -e /test2 test2   //-e：表示创建临时节点
  ```
  3. 序列持久节点，与持久节点类似，只是节点创建时，zookeeper会在给定路径下为节点添加唯一编号。
  ```
  create -s /test test     //-s：表示创建序列持久节点
  Created /test0000000003  //客户端重启后节点不会消失
  ```
  4. 序列临时节点，与临时节点类似，只是节点创建时，zookeeper会在给定路径下为节点添加唯一编号。
  ```
  create -e -s /test test  //-s -e: 表示创建临时序列节点
  Created /test0000000004     //客户端重启后，节点消失
  ```

#### ZooKeeper中的时间
ZooKeeper提供了分布式协作服务，而时间是分布式协作中非常核心的概念，那么ZooKeeper是如何跟踪时间的呢？有多种方式：
  * Zxid：ZooKeeper的状态每次改变都会产生一个Zxid。每个Zxid号码都唯一，并且Zxid号码越小，表明改变发生的时间越早。
  * Version numbers:节点每次发生改变，都会导致节点的版本号增加。stat中共有三个版本号：version:跟踪节点数据变化的次数；cversion:跟踪节点的子节点变化次数；aversion:跟踪节点的授权变化的次数
  * Ticks：当有多个ZooKeeper服务器时，服务器使用ticks事件的时间，比如状态更新，session超时，连接超时等。
  * Real time：只有在stat中的ctime(znode创建时间)和mtime(znode最后修改时间)中，ZooKeeper会使用真实时间，其他时间不使用。

#### Stat Structure (Stat数据结构)
  * czxid:节点产生事件对应的zxid
  * mzxid:节点最后一次修改事件对应的zxid
  * pzxid:节点的子节点最后一次修改事件对应的zxid
  * ctime:节点创建的时间
  * mtime:节点的最后修改时间
  * version：节点改变的次数
  * cversion：节点数据改变的次数
  * aversion：节点的子节点改变的次数
  * ephemeralOwner：如果节点不是临时节点为0，如果是临时节点，表示节点所有者的session id.
  * dataLength:节点关联数据的长度
  * numChildren：节点的子节点数量


#### 观察者（Watches）
客户端可以为任意节点设置观察者。当节点发生变化（比如节点被删除，节点的关联数据变化，节点的stat发生变化）时，就会触发观察者，并随之发送客户端一个通知消息。观察者一旦触发以后，就会被清空。
```
get path [watch]   //客户端get节点的时候，可以添加观察者
```

## ZooKeeper Sessions



## ZooKeeper Watches
## ZooKeeper access control using ACLs
