# yarn 框架
- 分布式框架
主从框架

- yarn 的功能
    - 集群资源的管理
        - 主节点 ->RM
        - 从节点 ->ND
- 任务调度
    - yarn 运行多个应用程序
    - 应用程序需要运行资源
    - 对应用程序的资源如何分配
    - yarn 如何任务调度
- resurcemanager
    - 处理客户端请求
    - 启动/监控 applicationMaster
    - 监控 nodemanager
    - 资源分配和调度
- nodemanager
    - 单个节点管理
    - 处理来自 resourcemanager 的命令
    - 处理来自 applicationMaster 的命令
- applicationMaster
    - 申请资源
    - 监控管理 nodemanager 上的任务运行情况
- container
    - 对资源的抽象和封装, 让每个应用程序对应的任务完整执行, 任务之间不能相互影响, 任务之间不能交叉使用和共享
<div align = center>
<img height="300" width="600" src="images/3.png">
</div>

