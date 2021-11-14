##### YARN架构概述
* ResourceManager(RM):整个集群资源（内存、CPU等）的老大
* NodeManager(NM):单个节点服务器资源老大
* ApplicationMaster(AM):单个任务运行的老大
* Container:容器，相当于一台独立的服务器，里面封装了任务运行所需要的资源，如内存，CPU，磁盘网络等。  
![](assets/bigData/002-yarn架构概述.png)  
说明1：客户端可以有多个  
说明2：集群上可以运行多个ApplicationMaster  
说明3：每个NodeManager上可以有多个Container  
