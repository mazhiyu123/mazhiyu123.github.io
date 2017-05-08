---
published: true
layout: post
title: Yarn组件-NodeManager
category: Hadoop
tags: 
  - Hadoop
time: 2017.02.16 10:27:00
excerpt: NodeManager是运行在单个节点上的代理，它需要与应用程序的ApplicationMaster和集群管理者ResourceManager交互[14]。从ApplicationMaster上接收有关Container的命令并执行。向ResourceMariager汇报各个Container运行状态和节点健康状况，并领取有关Container的命令执行之。
---

##### NodeManager
NodeManager是运行在单个节点上的代理，它需要与应用程序的ApplicationMaster和集群管理者ResourceManager交互[14]。从ApplicationMaster上接收有关Container的命令并执行。向ResourceMariager汇报各个Container运行状态和节点健康状况，并领取有关Container的命令执行之。  
NodeManager同样和ResourceManager一样由多个不同的模块共同完成NodeManager的所有功能。NodeManager的内部各个功能模块，如下图所示：  
![image](http://od4ghyr10.bkt.clouddn.com/NodeManager%E5%8A%9F%E8%83%BD%E6%A8%A1%E5%9D%97.png)  
NodeManager内部各个功能模块总结如下：  

1. NodeStatusUpdater模块  
NodeStatusUpdater是NodeManager与ResourceManager通信的唯一通道。NodeManager被启动后，由该子模块负责向ResourceManager注册自己，同时也会将所在节点的总可用资源量一并报给ResoureMananger。在运行程序阶段这个组件会周期性的向ResourceManager汇报各种状态并领取ResourceManager下达的命令。

2. ContainerManager模块  
它是NodeManager中非常重要的一个组件。它由多个子模块组成，每个子模块负责实现一部分特定的功能。各个子模块之间通过合作共同管理运行在该节点上的所有Container。各个子模块的具体功能如下。
    1. RPCServer:实现了ContainerManagementProtocol协议，是ApplicationMaster与NodeManager通信的唯一通道。各个ApplicationMaster向ContainerManager发送RPC请求以启动新分配的Container或者停止正在运行的Container。另外，ContainerTokenSecretManager会验证所有的Container操作，以此来防止ApplicationMaster会伪造停止或者启动Container命令。
    
    2. ResourceLocationlizationService:负责Container所需资源的本地化。它能够按照相应配置从HDFS上下载Container所需的文件资源(数据字典，jar包等)，并尽量将下载到的资源分摊到各个磁盘上以防止出现访问热点，缓解IO压力。此外，下载的文件会被她添加相应的访问控制权限，并分配合适的磁盘使用空间。  
    
    3. ContainerstLauncher:维护了一个线程池从而可以并行的完成Container的相关操作，如停止或者启动Container。其中启动Container的请求是ApplicationMaster发起的，而停止Container的请求则可能是ApplicationMaster或者ResourceManager发起的。  

    4. AuxServices:NodeManager允许用户通过配置附属服务的方式扩展自己的功能，这使得不同的节点可以自己定制一些特定框架所需要的服务。这些自定制的服务是与NodeManager上其他服务相互隔离的，因为它有自己的安全验证机制。用户配置的附属服务需要提前配置并且由NodeManager统一启动与停止。比如MapReduce计算框架中用到的Shuffle HTTP Server。它是通过封装成一个附属服务( ShuffleHandler)的方式由各个NodeManager启动的。

    5. ContainersMonitor:负责监控Container的资源使用量同时也为了实现资源隔离和公平共享的特性。ResourceManager为每个Container分配了一定量的计算和存储资源。而ContainersMonitor周期性探测它在运行过程中的资源利用量，一旦发现Container超出了它的允许使用份额上限，就向Container发送信号将其杀掉，这可以避免资源密集型的Container影响同节点上其他正在运行的Container。在Yarn中，只有内存资源是通过ContainersMonitor监控的方式加以限制的。对于CPU资源(及计算资源)，则采用了轻量级资源隔离方案Cgroups。

    6. LogHandler:用户可以通过它控制Container日志的保存地址(即是写到本地磁盘上还是将其打包后上传到一个文件系统中)。它还被设计为一个可插拔的组件。

    7. ContainerEventDispatcher:Container的事件调度器。负责将ContainerEvent类型的事件调度给对应的状态机。
    
    8. AppIicationEventDispatcher:Application事件调度器。负责将ApplicationEvent类型的事件调度给对应的状态机。

3. WebServer模块  
使用户可以通过浏览查看节点上的所有的作业的运行情况，Container列表，节点健康信息。

4. NodeHealthCheckService 模块  
通过周期性的运行特定脚本来检查节点的健康状况。并将节点健康状况通过NodeStatusUpdater传递给ResourceManager。一旦ResourceManager发现一个节点处于不健康状态，则会将它加入黑名单，此后不再为它分配任务，直到再次转为健康状态才会再次给它分配任务。需要注意的是，节点被加人黑名单后，正在运行的Container不会被杀死仍会正常运行。

5. DeletionServer 模块  
负责异步删除文件，从而避免了同步的性能开销。

6. Security 模块  
这个模块主要有两个作用。第一个是确保合法用户访问NodeManager。第二个是确保AM申请的资源都经过RM认证过。

7. ContainerExecutor模块  
    这个模块是负责与底层操作系统交互，安全存放Container需要的文件，可以通过安全的方式启动和停止Container对应的进程。目前，Yarn提供了两种实现方式DefaultContainerExecutor和LinuxContainerExecutor。DefaultContainerExecutor是默认实现未提供任何权限管理措施，它以NodeManager启动者的身份启动和停止Container。而LinuxContainerExecutor则以应用程序拥有者的身份启动和停止Container ,因此更加安全[15]。此外，LinuxContainerExecutor允许用户通过Cgroups对CPU资源进行隔离。