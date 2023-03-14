# Kubernetes架构

![kubernetes architecture diagram](https://images.clickittech.com/2020/wp-content/uploads/2022/04/13202329/Diagram-55-1024x858.jpg)

## kube-apiserver

​       kubernetes对外交互的入口，所有的资源管理都是通过kube-apiserver来进行，其他组件也需要kube-apiserver来工作。kube-apiserver由两部分组成，master和extension-apiserver，master负责核心资源，extension-apiserver提供CRD资源的管理。除了基本的资源管理，还提供容器访问，日志查看，port-forward之类的功能。

Service资源的clusterIP分配发生在kube-apiserver这里。

kube-apiserver默认使用etcd作为存储，带etcd不是必须，可以替换为其他的存储。

## controller-manager

controller-manager是一堆controller的组合，每个controller都会通过kube-apiserver监听一个或多个资源的变动事件，并根据controller的业务对这些事件作出相应的操作。

node-controller监听node事件，并分配PodCIDR（todo： 补充更多功能)

## scheduler

负责Pod的调度，根据Pod的资源需求，亲和，反亲和，节点选择等参数，以及集群的资源池实际情况，将Pod分配到合适的节点执行。

## kubelet

负责节点的注册，状态更新（资源，容器)，维持心跳，监听Pod事件，如果有Pod分配到它所在的节点，为该Pod分配IP，挂载需要的卷，通过容器运行时管理对应的容器，向kube-apiserver上报Pod相关状态，代理容器访问，容器日志查看请求。

todo: 补充 DNS相关

## kube-proxy

运行在每个节点，负责代理容器对服务的访问，以ClusterIP为例，clusterIP通常跟PodCIDRs不在一个网段，容器通过clusterIP访问服务时，kube-proxy会通过对应的请求转换成服务后端Pod的请求。现在默认的模式是ipvs，ipvs模式下，kube-proxy会为每个Service创建对应的ipvs virtual server, 然后将service的endpoints作为real server添加到 virtual server里。有些服务会有拓扑感知相关的配置，因此endpoints会经过一定的过滤。

一个服务的endpoint访问该服务时会失败，但kube-proxy实现了hairpin，解决了这个问题。

ipvs模式下，kube-proxy启动时会配置一些系统参数：

*  net/bridge/bridge-nf-call-iptables
*  net/ipv4/vs/conntrack
*  net/ipv4/vs/conn_reuse_mode
*  net/ipv4/vs/expire_nodest_conn

## coredns
为集群里的容器提供服务域名解析，每个容器通过域名访问服务时，要先通过coredns解析域名，获取服务的IP地址，然后才能通过IP地址去访问。
## CNI

容器网络接口标准，有多个实现。

## CRI

## CSI

