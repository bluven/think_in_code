最近测试FabEdge时发现了一个bug，伴随着这个bug发现了一个奇怪的现象：无法从边缘节点ping通云端节点。Bug很容易修复，因为生成ipset的代码出了问题，但我还是好奇为什么ipset出问题的时候无法ping通，经过一番苦战，终于搞明白了原因，特此记录一番。

测试环境有3个节点和三个pod，分别是:
master：10.22.46.11
node1：10.22.46.16
edge1：10.22.46.41
net-tool-master：10.234.79.169
net-tool-node1: 10.234.102.161
net-tool-edge1: 192.168.0.16

从net-tool-edge1 ping net-tool-node1的时候会失败，抓包的时候发现icmp包到不了net-tool-node1节点，另外源地址是10.22.46.41，而不是192.168.0.16，即node1上收到的icmp包做了SNAT:
```shell
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on any, link-type LINUX_SLL2 (Linux cooked v2), snapshot length 262144 bytes
08:31:02.913070 tunl0 In  IP 10.22.46.41 > 10.234.102.161: ICMP echo request, id 169, seq 97, length 64
08:31:03.913027 tunl0 In  IP 10.22.46.41 > 10.234.102.161: ICMP echo request, id 169, seq 98, length 64
08:31:04.913049 tunl0 In  IP 10.22.46.41 > 10.234.102.161: ICMP echo request, id 169, seq 99, length 64
```

如果从net-tool-master上平net-tool-node1则是正常的，这说明calico正常工作，这时我就比较好奇包是从哪儿开始丢的，通过用trace来debug iptables发现一个新情况，从net-tool-master ping net-tool-node1时，包经过的iptables 规则如下所示:
```shell
raw:PREROUTING:policy:3
mangle:PREROUTING:rule:1
mangle:cali-PREROUTING:rule:3
mangle:cali-from-host-endpoint:return:1
mangle:cali-PREROUTING:return:5
mangle:PREROUTING:policy:2
nat:PREROUTING:rule:1
nat:cali-PREROUTING:rule:1
nat:cali-fip-dnat:return:1
nat:cali-PREROUTING:return:2
nat:PREROUTING:rule:2
nat:KUBE-SERVICES:return:18
nat:PREROUTING:policy:4
mangle:FORWARD:policy:1
filter:FORWARD:rule:1
filter:cali-FORWARD:rule:1
filter:cali-FORWARD:rule:2
filter:cali-from-hep-forward:return:1
filter:cali-FORWARD:rule:4
filter:cali-to-wl-dispatch:rule:3
filter:cali-tw-cali20fd069ebc8:rule:3
filter:cali-tw-cali20fd069ebc8:rule:4
filter:cali-pri-_zbxMTbNMDRyfczFBup:rule:1
filter:cali-pri-_zbxMTbNMDRyfczFBup:rule:2
filter:cali-tw-cali20fd069ebc8:rule:5
filter:cali-FORWARD:rule:5
filter:cali-to-hep-forward:return:1
filter:cali-FORWARD:rule:6
filter:cali-cidr-block:return:1
filter:cali-FORWARD:return:7
filter:FORWARD:rule:2
filter:FABEDGE-FORWARD:return:4
filter:FORWARD:rule:3
filter:KUBE-FORWARD:return:5
filter:FORWARD:rule:4
filter:KUBE-SERVICES:return:1
filter:FORWARD:rule:5
filter:KUBE-EXTERNAL-SERVICES:return:1
filter:FORWARD:rule:6
filter:DOCKER-USER:return:1
filter:FORWARD:rule:7
filter:DOCKER-ISOLATION-STAGE-1:return:2
filter:FORWARD:rule:12
mangle:POSTROUTING:rule:1
mangle:cali-POSTROUTING:rule:1
mangle:POSTROUTING:policy:2
nat:POSTROUTING:rule:1
nat:cali-POSTROUTING:rule:1
nat:cali-fip-snat:return:1
nat:cali-POSTROUTING:rule:2
nat:cali-nat-outgoing:return:2
nat:cali-POSTROUTING:return:4
nat:POSTROUTING:rule:2
nat:FABEDGE-POSTROUTING:return:3
nat:POSTROUTING:rule:3
nat:KUBE-POSTROUTING:rule:1
nat:POSTROUTING:policy:5
```
如果是从net-tool-edge1 ping net-tool-node1，包的trace记录如下:
```shell
raw:PREROUTING:policy:4
mangle:PREROUTING:rule:1
mangle:cali-PREROUTING:rule:3
mangle:cali-from-host-endpoint:return:1
mangle:cali-PREROUTING:return:5
mangle:PREROUTING:policy:2
nat:PREROUTING:rule:1
nat:cali-PREROUTING:rule:1
nat:cali-fip-dnat:return:1
nat:cali-PREROUTING:return:2
nat:PREROUTING:rule:2
nat:KUBE-SERVICES:return:18
nat:PREROUTING:policy:4
```
可以看出，在nat:PREROUTING:policy:4后就没记录了，起初以为nat表的PREROUTING的默认规则是DROP，但实际上不是:
```shell
[root@node1 ~]# iptables -t nat -L PREROUTING --line-numbers
Chain PREROUTING (policy ACCEPT)
num  target     prot opt source               destination         
1    cali-PREROUTING  all  --  anywhere             anywhere             /* cali:6gwbT8clXdHdC1b1 */
2    KUBE-SERVICES  all  --  anywhere             anywhere             /* kubernetes service portals */
3    DOCKER     all  --  anywhere             anywhere             ADDRTYPE match dst-type LOCAL
```
这就比较费解了，看了一下著名的[netfilter packet flow 图](https://upload.wikimedia.org/wikipedia/commons/3/37/Netfilter-packet-flow.svg)，可以看出在nat:PREROUTING之后就是**bridging decision**, 在这里会决定是发向本机还是转发给其他设备。
关于bridging decision在网上没有太多资料，只在[这篇文章](https://opengers.github.io/openstack/openstack-base-netfilter-framework-overview/)看到一点描述:
> 图中下方中间位置的bridging decision类似普通二层交换机的查表转发功能，根据数据包目的MAC地址判断此数据包是转发还是交给上层处理，具体可以参考我另一篇文章[linux-bridge](https://opengers.github.io/openstack/openstack-base-virtual-network-devices-bridge-and-vlan/#linux-bridge)
> 图中中心位置的routing decision就是路由选择，根据系统路由表(ip route查看), 决定数据包是forward，还是交给本地处理

后来又是一通搜索，终于找到一个相近的文章： [IP Packets stuck in routing decision](https://serverfault.com/questions/974057/ip-packets-stuck-in-routing-decision)，根据文章里用到的命令终于确定就是路由问题:
```shell
ip r get 10.234.102.161 from 10.22.46.41 iif tunl0
RTNETLINK answers: Cross-device link

ip r get 10.234.102.161 from 10.234.79.168 iif tunl0
10.234.102.161 from 10.234.79.168 dev cali20fd069ebc8 
    cache iif *
```
正如在文章里提到的那样，出现" Cross-device link"是因为有rp_filter验证：
```shell
ip netconf show dev tunl0
inet tunl0 forwarding on rp_filter strict mc_forwarding off 
inet6 tunl0 forwarding off mc_forwarding off 

```
关闭rp_filter后，再次在node1抓包就能看到回包了:
```shell
08:55:15.967766 tunl0 In  IP 10.22.46.41 > 10.234.102.161: ICMP echo request, id 170, seq 1, length 64
08:55:15.968483 cali20fd069ebc8 Out IP 10.22.46.41 > 10.234.102.161: ICMP echo request, id 170, seq 1, length 64
08:55:15.968552 cali20fd069ebc8 In  IP 10.234.102.161 > 10.22.46.41: ICMP echo reply, id 170, seq 1, length 64
08:55:15.968567 eth0  Out IP 10.234.102.161 > 10.22.46.41: ICMP echo reply, id 170, seq 1, length 64
08:55:16.967545 tunl0 In  IP 10.22.46.41 > 10.234.102.161: ICMP echo request, id 170, seq 2, length 64
08:55:16.967751 cali20fd069ebc8 Out IP 10.22.46.41 > 10.234.102.161: ICMP echo request, id 170, seq 2, length 64
08:55:16.967786 cali20fd069ebc8 In  IP 10.234.102.161 > 10.22.46.41: ICMP echo reply, id 170, seq 2, length 64
08:55:16.967795 eth0  Out IP 10.234.102.161 > 10.22.46.41: ICMP echo reply, id 170, seq 2, length 64
```
当然，因为路径不对称，net-tool-edge1还是收不到包，但iptables里的trace记录正常了。

稍微总结一下用到的调试命令:
使用trace来查看一个包流经的规则，这篇文章非常好：[https://www.opsist.com/blog/2015/08/11/how-do-i-see-what-iptables-is-doing.html](https://www.opsist.com/blog/2015/08/11/how-do-i-see-what-iptables-is-doing.html)
```shell
 # 使用trace记录
iptables -t raw -A PREROUTING -d 10.234.102.161/32 -j TRACE
```

查看内核看到的实际路由, ip route get完整命令配置项较多，需要去相关的网站查看
```shell
ip r get 10.234.102.161 from 10.234.79.168 iif tunl0
```
使用`ip netconf`命令查看某设备的一些配置:
```shell
ip netconf show dev tunl0
```
最后使用netstat查看因为rp_filter导致的丢包:
```shell
nstat -az TcpExtIPReversePathFilter
```
