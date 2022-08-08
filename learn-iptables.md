# iptables学习

[toc]

## 基础概念： 链与表



iptables是netfilter的用户接口，用户通过iptables命令往netfilter框架里添加一些规则，从而完成封包过滤、封包重定向和网络地址转换（NAT）等功能。

netfilter定义了一些回调点，这些回调点在iptables被称为“链(chain)”, 如图:



![chains](https://www.zsythink.net/wp-content/uploads/2017/02/021217_0051_2.png)

iptables总共有5个链：

* PREROUTING
* INPUT
* FORWARD
* OUTPUT
* POSTROUTING

根据上图，我们能够想象出某些常用场景中，报文的流向：

* 到本机某进程的报文：PREROUTING –> INPUT

* 由本机转发的报文：PREROUTING –> FORWARD –> POSTROUTING
* 由本机的某进程发出报文（通常为响应报文）：OUTPUT –> POSTROUTING



在每个链可以定义很多规则，当数据到达某个回调点时，就会被netfilter按序执行链里定义的规则。因为规则是按序执行，仿佛一条链子，所以在iptables，这些规则集合就被成为链。

有些规则具有相同的功能，在iptables里，具有相同规则的集合被成为表(table)。iptables定义了四种表：

- filter表：负责过滤功能，防火墙；内核模块：iptables_filter
- nat表：network address translation，网络地址转换功能；内核模块：iptable_nat
- mangle表：拆解报文，做出修改，并重新封装 的功能；iptable_mangle
- raw表：关闭nat表上启用的连接追踪机制；iptable_raw https://www.frozentux.net/iptables-tutorial/iptables-tutorial.html#RAWTABLE

## 表链关系

并非所有的表都存在于每个链中，具体的组合关系如下：

![img](https://www.zsythink.net/wp-content/uploads/2017/02/021217_0051_7.png)

因为链里的规则是按序执行，所以表也是分优先级的，顺序如下：

​		raw –> mangle –> nat –> filter



![tables & chains](https://www.zsythink.net/wp-content/uploads/2017/02/021217_0051_6.png)

## 规则

规则由匹配条件和处理动作组成，一个数据只有满足匹配条件才会被该规则的处理动作处理。

### 匹配条件

匹配条件分为两大类：

* 基本匹配条件： 源地址和目标地址
* 扩展匹配条件： 源端口和目标端口。扩展匹配条件以模块的形式存在，除端口外，还有其他匹配条件模块。

### 处理动作

处理动作在iptables中被称为target（这样说并不准确，我们暂且这样称呼），动作也可以分为基本动作和扩展动作。

此处列出一些常用的动作，之后的文章会对它们进行详细的示例与总结：

- **ACCEPT**：允许数据包通过。
- **DROP**：直接丢弃数据包，不给任何回应信息，这时候客户端会感觉自己的请求泥牛入海了，过了超时时间才会有反应。
- **REJECT**：拒绝数据包通过，必要时会给数据发送端一个响应的信息，客户端刚请求就会收到拒绝的信息。
- **SNAT**：源地址转换，解决内网用户用同一个公网地址上网的问题。
- **MASQUERADE**：是SNAT的一种特殊形式，适用于动态的、临时会变的ip上。
- **DNAT**：目标地址转换。
- **REDIRECT**：在本机做端口映射。
- **LOG**：在/var/log/messages文件中记录日志信息，然后将数据包传递给下一条规则，也就是说除了记录以外不对数据包做任何其他操作，仍然让下一条规则去匹配。
- **RETURN**：停止在当前链遍历规则，如果当前在子链中，那就回到主链继续遍历；如果是主链，那就以执行默认策略

*注：更多targets参考此文： [Iptables targets and jumps](https://www.frozentux.net/iptables-tutorial/chunkyhtml/c3965.html)*

### 默认策略

每个链可以设置一个默认策略(policy)，当一个链上的规则都不匹配时就会执行这个默认策略

![img](https://www.zsythink.net/wp-content/uploads/2017/04/041317_0547_6.png)

如图，INPUT链的默认规则是ACCEPT，图里packets和bytes是默认策略匹配的数据数量。

## 常见操作

### 列出表里的规则

```shell
iptables -t nat -L
iptables -t nat -S
iptables -t nat -L FILTER #列出指定列的规则
iptables -vn --line-numbers -L # 提供更详细的信息，更多参数看help
```

![img](https://www.zsythink.net/wp-content/uploads/2017/04/041317_0547_3.png)

- **pkts**:对应规则匹配到的报文的个数。
- **bytes**:对应匹配到的报文包的大小总和。
- **target**:规则对应的target，往往表示规则对应的”动作”，即规则匹配成功后需要采取的措施。
- **prot**:表示规则对应的协议，是否只针对某些协议应用此规则。
- **opt**:表示规则对应的选项。
- **in**:表示数据包由哪个接口(网卡)流入，即从哪个网卡来。
- **out**:表示数据包将由哪个接口(网卡)流出，即到哪个网卡去。
- **source**:表示规则对应的源头地址，可以是一个IP，也可以是一个网段。
- **destination**:表示规则对应的目标地址。可以是一个IP，也可以是一个网段。

### 添加，修改，删除规则

```shell
# 添加： iptables -t 表名 -A 链名 匹配条件 -j 动作
iptables -t filter -A INPUT -s 192.168.1.146 -j DROP 

# 在头部添加： iptables -t 表名 -I 链名 匹配条件 -j 动作
iptables -t filter -I INPUT -s 192.168.1.146 -j ACCEPT

# 在指定位置添加规则: iptables -t 表名 -I 链名 规则序号 匹配条件 -j 动作
iptables -t filter -I INPUT 5 -s 192.168.1.146 -j REJECT

# 设置默认策略： iptables -t 表名 -P 链名 动作
iptables -t filter -P FORWARD ACCEPT

# 根据匹配条件删除规则：iptables -t 表名 -D 链名 匹配条件 -j 动作
iptables -t filter -D INPUT -s 192.168.1.146 -j DROP

# 修改指定位置的规则： iptables -t 表名 -R 链名 规则序号 规则原本的匹配条件 -j 动作
iptables -t filter -R INPUT 3 -s 192.168.1.146 -j ACCEPT

# 删除制定位置的规则：iptables -t 表名 -D 链名 规则序号
iptables -t filter -D INPUT 3

# 清空某表某链的规则
iptables -t filter -F INPUT

# 清空某个表的所有规则
iptables -t filter -F
```

### 匹配操作

```shell
# 对源地址匹配
iptables -t filter -I INPUT -s 192.168.1.111,192.168.1.118 -j DROP
iptables -t filter -I INPUT -s 192.168.1.0/24 -j ACCEPT
iptables -t filter -I INPUT ! -s 192.168.1.0/24 -j ACCEPT

# 对目标地址匹配
iptables -t filter -I OUTPUT -d 192.168.1.111,192.168.1.118 -j DROP
iptables -t filter -I INPUT -d 192.168.1.0/24 -j ACCEPT
iptables -t filter -I INPUT ! -d 192.168.1.0/24 -j ACCEPT

# 对协议匹配
iptables -t filter -I INPUT -p tcp -s 192.168.1.146 -j ACCEPT
iptables -t filter -I INPUT ! -p udp -s 192.168.1.146 -j ACCEPT

# 对接口匹配(只能用于PREROUTING,INPUT,FORWARD链)
iptables -t filter -I INPUT -p icmp -i eth4 -j DROP
iptables -t filter -I INPUT -p icmp ! -i eth4 -j DROP


# 对接口匹配(只能用于OUTPUT,POSTROUTING)
iptables -t filter -I OUTPUT -p icmp -o eth4 -j DROP
iptables -t filter -I OUTPUT -p icmp ! -o eth4 -j DROP

# 协议匹配
iptables -t filter -I OUTPUT -d 192.168.1.146 -p tcp -m tcp --sport 22 -j REJECT
iptables -t filter -I INPUT -s 192.168.1.146 -p tcp -m tcp --dport 22:25 -j REJECT
iptables -t filter -I INPUT -s 192.168.1.146 -p tcp -m tcp --dport :22 -j REJECT
iptables -t filter -I INPUT -s 192.168.1.146 -p tcp -m tcp --dport 80: -j REJECT
iptables -t filter -I OUTPUT -d 192.168.1.146 -p tcp -m tcp ! --sport 22 -j ACCEPT

# 多端口匹配
iptables -t filter -I OUTPUT -d 192.168.1.146 -p udp -m multiport --sports 137,138 -j REJECT
iptables -t filter -I INPUT -s 192.168.1.146 -p tcp -m multiport --dports 22,80 -j REJECT
iptables -t filter -I INPUT -s 192.168.1.146 -p tcp -m multiport ! --dports 22,80 -j REJECT
iptables -t filter -I INPUT -s 192.168.1.146 -p tcp -m multiport --dports 80:88 -j REJECT
iptables -t filter -I INPUT -s 192.168.1.146 -p tcp -m multiport --dports 22,80:88 -j REJECT

# tcp-flags匹配, 参考https://www.zsythink.net/archives/1578
iptables -t filter -I INPUT -p tcp -m tcp --dport 22 --tcp-flags SYN,ACK,FIN,RST,URG,PSH SYN -j REJECT
iptables -t filter -I OUTPUT -p tcp -m tcp --sport 22 --tcp-flags SYN,ACK,FIN,RST,URG,PSH SYN,ACK -j REJECT
iptables -t filter -I INPUT -p tcp -m tcp --dport 22 --tcp-flags ALL SYN -j REJECT
iptables -t filter -I OUTPUT -p tcp -m tcp --sport 22 --tcp-flags ALL SYN,ACK -j REJECT

# 用于匹配tcp新建连接的请求报文，相当于使用”–tcp-flags SYN,RST,ACK,FIN  SYN”
iptables -t filter -I INPUT -p tcp -m tcp --dport 22 --syn -j REJECT

# udp匹配 https://www.zsythink.net/archives/1588
iptables -t filter -I INPUT -p udp -m udp --dport 137 -j ACCEPT
iptables -t filter -I INPUT -p udp -m udp --dport 137:157 -j ACCEPT

# icmp匹配 https://www.zsythink.net/archives/1588
iptables -t filter -I INPUT -p icmp -m icmp --icmp-type 8/0 -j REJECT
iptables -t filter -I INPUT -p icmp --icmp-type 8 -j REJECT
iptables -t filter -I OUTPUT -p icmp -m icmp --icmp-type 0/0 -j REJECT
iptables -t filter -I OUTPUT -p icmp --icmp-type 0 -j REJECT
iptables -t filter -I INPUT -p icmp --icmp-type "echo-request" -j REJECT
```

其他常见模块参考：https://www.zsythink.net/archives/1564

### 链接追踪与状态

state模块可以让iptables实现”连接追踪”机制，对于state模块而言的”连接”并不能与tcp的”连接”画等号，在TCP/IP协议簇中，UDP和ICMP是没有所谓的连接的，但是对于state模块来说，tcp报文、udp报文、icmp报文都是有连接状态的，我们可以这样认为，对于state模块而言，只要两台机器在”你来我往”的通信，就算建立起了连接。

对于state模块的连接而言，”连接”其中的报文可以分为5种状态，报文状态可以为NEW、ESTABLISHED、RELATED、INVALID、UNTRACKED

- **NEW**：连接中的第一个包，状态就是NEW，我们可以理解为新连接的第一个包的状态为NEW。
- **ESTABLISHED**：我们可以把NEW状态包后面的包的状态理解为ESTABLISHED，表示连接已建立。
- **RELATED**：从字面上理解RELATED译为关系。有些程序会有两个进程占用不同的端口发送数据，对这种情况需要使用特定的模块，例如nf_conntrack_ftp。
- **INVALID**：如果一个包没有办法被识别，或者这个包没有任何状态，那么这个包的状态就是INVALID，我们可以主动屏蔽状态为INVALID的报文。
- **UNTRACKED**：报文的状态为untracked时，表示报文未被追踪，当报文的状态为Untracked时通常表示无法找到相关的连接。

参考：http://www.iptables.info/en/connection-state.html

```shell
# 只允许回包通过，不允许其他主机主动发送数据
iptables -t filter -I INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT

# 允许回包转发, conntrack和state是一个模块
iptables -t filter -A FORWARD -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
```

### 自定义链

iptables允许用户自定义链，这样方便管理规则，但自定义链需要被默认链引用才能其作用

```
# 创建链
iptables -t filter -N IN_WEB

# 引用自定义链
iptables -t filter -I INPUT -p tcp --dport 80 -j IN_WEB

# 重命名链
iptables -E IN_WEB WEB

# 删除链，链必须是空的，且没有被引用
iptables -X WEB
```

## 数据流动资料

https://www.frozentux.net/iptables-tutorial/chunkyhtml/c962.html#TRAVERSINGGENERAL

![img](https://www.frozentux.net/iptables-tutorial/chunkyhtml/images/tables_traverse.jpg)

## 其他学习资料

* https://www.booleanworld.com/depth-guide-iptables-linux-firewall/#Protocols_and_modules
* https://www.linuxrumen.com/rmxx/642.html
* https://www.zsythink.net/archives/1199
* https://www.frozentux.net/iptables-tutorial/iptables-tutorial.html
