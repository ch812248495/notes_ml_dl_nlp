# HDFS 分布式文件系统

## 解决问题: 
- 海量数据的存储, 采用分布式构架设计
- 分布式特点: 集群, 多态机器共同协作完成存储, 主从构架设计
    - namenode: 主节点, 领导
        - 文件的元数据:
            - 文件的名称
            - 位置
            - 副本数量
            - 拥有者 组 权限
            - 存储块(256m)
            - 各个块在哪些 datanode 上面
    - datanode: 从节点, 随从
        - 存储数据

    - 读文件
        - client->namenode
        - client->datanode
        - 就近(和client 距离)原则进行读写
    - 写文件
        - client->namenode
        - client->datanode
        - 同步