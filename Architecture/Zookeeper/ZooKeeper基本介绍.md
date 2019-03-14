# ZooKeeper基本介绍

## 分布式带来的问题
分布式应用带来众多便利的同时，也带来了很多复杂且难以克服的困难，比如：
  * Race condion(竞争条件：由于两个或者多个进程竞争使用不能被同时访问的资源，使得这些进程又可能因为时间上推进的先后原因而出现问题，这叫做竞争条件)；
  * deadlock(指两个或两个以上的进程在执行过程中，因争夺资源而造成的一种互相等待的现象，若无外力作用，它们都将无法推进下去)、
  * Inconsistency of data（不同进程访问同一数据源，却得到了不同数据）等。

总之，在分布式环境中，一个服务的管理与协作是非常复杂的过程。

ZooKeeper通过简单的架构和API解决了这些问题，使得开发者可以更加专注于核心应用逻辑而不用担心分布式环境带来的各种问题。



## ZooKeeper是什么？
ZooKeeper是一个集群（或一群节点）中使用的基于健壮的同步技术的服务，用于集群内部之间的协作和数据共享。简单来说就是管理大量主机的系统服务。ZooKeeper本身也是分布式应用，实现分布式读写技术。提供的常见服务如下：
  * Naming service: 通过命名名称识别集群中的节点。类似于DNS，但是应用于节点。
  * Configuration management:集群中所有节点保持最新配置信息。
  * Cluster management:实时添加/删除节点。
  * Leader election:为协作选举一个leader。
  * Locking and synchronization service: 当修改数据时，锁定数据，实现容灾。
  * Highly reliable data registry：高可用，即使集群中部分节点宕机，数据依然可用。
