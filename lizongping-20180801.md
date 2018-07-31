# Technique
## calico简介
   Calico是一个纯三层的协议，为OpenStack虚机和Docker容器提供多主机间通信。Calico不使用重叠网络比如flannel和libnetwork重叠网络驱动，它是一个纯三层的方法，使用虚拟路由代替虚拟交换，每一台虚拟路由通过BGP协议传播可达信息（路由）到剩余数据中心。
   Calico不使用重叠网络比如flannel和libnetwork重叠网络驱动，它是一个纯三层的方法，使用虚拟路由代替虚拟交换，每一台虚拟路由通过BGP协议传播可达信息（路由）到剩余数据中心。

   Calico在每一个计算节点利用Linux Kernel实现了一个高效的vRouter来负责数据转发，而每个vRouter通过BGP协议负责把自己上运行的workload的路由信息像整个Calico网络内传播——小规模部署可以直接互联，大规模下可通过指定的BGP route reflector来完成。

   这样保证最终所有的workload之间的数据流量都是通过IP路由的方式完成互联的。
![image](https://2.bp.blogspot.com/-w-FqCC5cNt4/WMLSOMR6kmI/AAAAAAAAOWw/bVIR5Uv5L00ehcJTRbJ5tU0fpmKniKPlQCLcB/s1600/3d0851d60bac0b72802fc4bb8cfa76b0.png)

参考blog : https://blog.csdn.net/sk1137/article/details/64798152
## docker跨主机的实现
### docker网络的简单介绍
   docker的网络连接有以上几种方式：host、none、bridge、自定义网络连接等。前三种连接方式 和虚拟机的连接方式类似、calico属于自定义连接。
### calico的工作原理
   calico是通过路由表的方式进行转发与其它自定义网络连接（vxlan）不同，vxlan是通过ip封包的方式（每经过一个路由，加一层原始ip）,达到路由的功能，比calico多了一个封包拆包的过程。
   calico的路由功能是利用host主机当路由功能，因此calico需要两个功能，一个是存储calico的路由信息，一个是建立路由节点（每个host建立一个）.
   calico默认存储路由信息使用的是etcd分布式存储key/value的数据库（对比ZooKeeper）.
   calico建立路由节点则是创建一个容器维护节点信息。
   
   calicoctl node run --node-image=quay.io/calico/node:v2.6.10
   
   在docker.service中配置etcd的存储类型--cluster-store=etcd://ip:port.这样calico就可以把信息存储在etcd中，实现多个节点的通讯。
###  calico网络容器的创建
   docker network create --driver calico --ipam-driver calico-ipam net1
   
   ipam-driver参考文献地址：https://github.com/docker/libnetwork/blob/master/docs/ipam.md
   
   创建容器：docker run --net net1 --name workload-A -tid busybox
   
   创建了一个net1网络的busybox容器
   
   部署了calico节点的net1网络的容器都可以ping通了
   
###  tips
    calico创建节点的时候，主机名不能重复，在同步节点信息到etcd，会认为同一个主机名为同一节点，不能实现网络互通了。（节点名称默认为主机名，如果修改了节点名称，也会创建一个主机名的节点）
