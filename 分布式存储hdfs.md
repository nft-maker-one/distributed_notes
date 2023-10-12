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
### 文件读取和写入
+ 首先调用Open生成df(distributed filesystem)实例
+ df实例通过RPC获取第一批文件文件的block的location
+ 
