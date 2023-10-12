# 分布式存储hdfs
分布式存储定义：将数据分散存储在多台独立的设备  
hdfs还没有使用go写的成型框架，现在主流还是用java完成  

## gfs(google file system)
gfs被认为是最早出现的分布式存储系统，相对理解难度更大

## hdfs(hadoop file system)
secondary name node，name node，client，data node这四部分组成了hdfs的架构

### client
+ client是客户端，起到文件切分作用，文件上传hdfs时，client将文件分片成一个个block(数据块) 然后进行储存
+ 于dataNode进行交互，读取或写入数据
+ 与namenode进行交互，获取文件的位置
+ 提供一些命令，访问管理hdfs
### nameNode
+ 管理hdfs命名空间与数据块(block)
+ 处理client端请求
### dataNode
+ 存储实际数据块
+ 执行数据块读写操作
### secondary nameNode
+ 辅助nameNode，分布工作量
+ 紧急情况下，恢复和帮助nameNode
### hdfs文件读取
+ 首先调用Open生成df(distributed filesystem)实例
+ df实例通过RPC获取第一批文件文件的block的location
+ 前面返回一个FSDataInputStream对象，，连接datanode，调用read方法
+ 如果第一个block块数据读完，关闭指向第一个block的连接，接着读取下一个
+ 如果第一个dataNode中数据块读完了，FSInputStream获取下一个block的location
### hdfs文件写入
+ 使用create方法创建ds
+ 创建新文件，该文件没有任何block进行关联（只有检查确实没有任何文件关联才会创建）
+ 前面返回一个FSDataOutputStream对象，客户端可以通过该对象写入数据
+ dataNode完成数据接收后，通过ack返回一个相应
