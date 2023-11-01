# hadoop框架
## Apache hadoop概述
+ 狭义上说Hadoop是Apache软件基金会的一款开源软件，底层用java实现，开源。运行用户使用简单的编程模型实现跨机器集群对大数据的分布式计算处理
+ hadoop核心组件
  1. hadoop HDFS(分布式文件存储系统)解决大数据存储
  2. hadoop YARN(集群资源管理和任务调度框架) 解决资源任务调度问题
  3. hadoop MapReduce(分布式计算框架)解决海量数据的计算问题
+ 广义的hadoop指与hadoop相关的整个生态圈
## hadoop集群概述
+ hadoop集群包含两部分:HDFS集群，YARN集群
+ 两个集群逻辑上分离，通常物理上在一起
+ 两个集群都是标准的主从架构集群

## HDFS集群
+ 主角色：NameNode
+ 从角色：DataNode
+ 主角色辅助角色：SecondanaryNameNode
  
## YARN集群(资源管理，调度)
+ 主角色：RsourecManageer从角色：NodeManager

## 问题思考
+ 为什么说两个集群逻辑上分离：可能怕的任务不同
+ 物理上相邻：都在一台电脑的进程
+ Mapreduce是计算框架，没有集群之说

## hadoop集群模式安装
systemctl stop firewalld.service #停止firewalld服务  
systemctl disable firewalld.service #开机禁用firewalld服务  
systemctl status firewalld.service #查看防火墙到底关了没  
#ssh免密登录  
ssh-keygen #生成公钥，私钥  
ssh-copy-id node1 ssh-copy-id node2  
#集群时间同步  
yum -y install ntpdate  
ntpdate ntp4.aliyun.com  
#创建统一工作目录
mkdir -p /export/server #软件安装路径
mkdir -p /export/data/ #数据存储途径
mkdir -p /export/software/ #安装包存放路径  
NameNode format（格式化操作）首次启动hdfs时执行  
format本质上是初始化工作，首次搭建与执行之前执行一次，进行hdfs清除与准备工作  
hdfs namenode -format  
format只可以执行一次，后续不再需要，format除了造成数据丢失，还会导致主从节点互不相认

## 启动,hadoop 
### 手动逐个进程启动
每台机器上手动启动关闭一个角色进程，可以精确控制每个进程的启动，避免群起群停  
+ hdfs集群：hadoop-daemon.sh startup namenode|datanode|secondnarynamenode  ##2.x hdfs -daemon start|stop namenode|datanode|secondarynode
+ yarn集群：yarn -darmon start|stop resourcemanager|nodemanager
### 手动一键启动
+ hdfs start-dfs.sh stop-dfs.sh
+ yarn start-yarn.sh stop-yarn.sh
+ hadoop start-all.sh stop-all.sh

## hadoop基本命令
hadoop fs -ls / #  
hadoop fs -mkdir itcast/#  
hdfs在设计时模仿的linux，命令非常相似#  
hadoopfs -put aaa /itcast #本地文件上传
hdfs是一个文件系统，有目录树结构，和linux相似，分文件，文件夹，上传一个小文件也非常慢  
hadoop jar a.jar #运行a.jar文件

## 分布式存储的核心属性以及功能
+ 分布式存储：数据量大时，解决单机存储遇到的瓶颈
+ 元数据记录：文件分布在不同机器上不利于寻找，使用元数据记录下文件及其存储位置，快速定位
+ 分块存储：解决文件过大，单机存不下的情况。文件分开存储在不同机器，针对并行操作提高效率。
+ 副本机制：冗余存储，保证数据安全

## hdfs系统架构
+ 主从架构：一个NameNode带领一堆DataNode
master/save.NameNode是hdfs的主节点，DataNode是hdfs的从节点，两种角色各司其职，共同协调完成分布式的文件存储任务。
+ 分块存储：里面的数据是一个个block。默认大小是128M。不足128M就是一块。快的大小可以通过改变hdfs-default.xml中：dfs.blocksize改变
+ 副本机制：block和block自己有冗余。每个block都会有副本，默认三个副本1+2,本身的那份也算进去了
+ 元数据记录：元数据是记录数据的数据，也叫解释性数据。文件自身属性信息：文件名称，权限，修改时间，文件大小，数据块大小。文件块位置映射信息：记录文件块和DataNode之间的映射信息，即那个块位于哪个节点上
+ 抽象统一的目录树结构
