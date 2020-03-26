	姓名：坏坏
	学习时间：2020年3月24日
	整理时间：2020年3月24日

# ICMP协议

## ICMP应用

- `Tracert`路由跟踪
	* Tracert显示数据包在网络传输过程中经过的每一跳

# ARP协议

ARP（Address Resolution Protocol）地址解析协议，负责完成逻辑地址向物理地址的动态映射，将逻辑（IP）地址转换为物理（MAC）地址

- **静态映射**：创建一个表，存储逻辑地址和物理地址的关联系。将网络中的每个主机都存储在这个表中。
	* 缺点：映射表必须周期的更新，增加了网络的开销
- **动态映射**：地址解析协议ARP和逆向地址解析协议RARP
- 数据链路层在进行数据封装时，需要目的MAC地址

```bash
arp -a [inet_addr] 显示地址映射表
arp -g [inet_addr] 功能与-a相同
arp -d inet_addr 删除ARP表中的指定表项
arp -s inet_addr pyhs_addr 增加有inet_addr和rhys_addr指定的静态表项
arp /? 显示帮助
```

## ARP代理

位于不同网络的网络设备在不配网关的情况下，能够通过ARP代理实现互相通信

- VLAN内代理
- VLAN间代理
- 路由式代理

## 免费ARP

- 免费ARP可以用来探测IP地址是否冲突

# 传输层协议

传输层定义了主机应用程序之间端到端的连通性。传输层中最常见的两个协议：传输控制协议TCP (Transmission Control Protocol )和用户数据包协议UDP (User Datagram Protocol) 。

## TCP

- 面向连接的传输层协议，提供可靠的传输服务
- 端口号用来区分不同的网络服务

### TCP建立过程

TCP通过三次握手建立可靠连接

1. 主机A发送SYN，seq=a，a为主机A的序列号
2. 服务器收到主机A的SYN请求后，会回复SYN，ACK，seq=b，ack=a+1，b为服务器的序列号，a+1表示已经确认，再返回一个a+1给主机A
3. 主机A收到服务器的回应之后，会回复ACK，seq=a+1，ack=b+1，a+1表示主机A已经收到，b+1表示已经收到服务器的确认

![1585064370197](E:%5CBad%5CPictures%5CTypora%20Picture%5C1585064370197.png)

### TCP流量控制

TCP流量控制也叫滑动窗口机制。

1. 主机A向服务器发送四个1024的数据包
2. 服务器收到主机A发送的三个1024的数据包后，因为缓存区已满，第四个数据包将会被丢弃
3. 服务器向主机A回应，只能收到三个1024长度的数据包，也就是3072长度的数据
4. 第二次主机A就会按照3027的数据长度向服务器发送数据
5. 服务器向主机A同样的回应

![1585125561540](E:%5CBad%5CPictures%5CTypora%20Picture%5C1585125561540.png)

### TCP关闭连接

1. 主机A向服务器发送FIN、ACK报文，seq=a，a为主机A的序列号，ack=b，b为之前与服务器建立连接时服务器的序列号
2. 服务器收到后，回复ACK，seq=b，ack=a+1，对主机A发送的确认关闭会话的回应
3. 服务器同样也会发送FIN、ACK报文，seq=b，b为服务器的序列号，ack=a+1。请求关闭连接
4. 主机A收到后，回复ACK，seq=a+1，ack=b+1，确认关闭会话

![1585126412370](E:%5CBad%5CPictures%5CTypora%20Picture%5C1585126412370.png)

## UDP

- 面向无连接的传输协议，传输可靠性没有保证
- 传输速率快
- 不提供重传机制，占用资源小，处理效率高

# 数据转发过程

## TCP封装

- **传输层**：封装源端口号、目的端口号
- **网络层**：封装协议号、源IP、目的IP
- **数据链路层**：封装目的MAC地址、源MAC地址

## 解封装

- **数据帧解封装**：收到数据帧后，检查MAC地址，是自己则继续处理该数据帧，MAC地址不是自己则丢弃
- **数据包解封装**：收到数据包后，检查数据包的目的IP地址，目的IP与自己IP相同，剥掉数据包的IP头部信息，发往上层协议继续进行处理。目的IP与自己不同，丢弃数据包
- **数据段解封装**：检查TCP头部的目的端口，然后将数据段发送给应用层的协议进行处理

# 交换网络基础

- 交换机工作在数据链路层，隔离以太网中的冲突域，提升以太网的性能

## 交换机的转发行为

- 泛洪
	* 向除了数据进入交换机的端口，向所有的端口转发数据
- 转发
	* 数据进入交换机后直接向目的端口转发数据
- 丢弃
	* 数据从某个接口进入，又从此接口发出

- 交换机的初始状态
	/ 交换机初始状态的MAC地址表为空
- 转发数据帧
	* 当数据帧的目的MAC地址不在MAC地址表中，或者目的MAC地址为广播地址时，交换机会泛洪该帧
- 目标主机回复
	* 交换机根据MAC地址表将目标主机的回复信息单播转发给源主机

```bash
关闭自协商
undo negotiation auto
speed 100  //把端口设置为100M
duplex full  //端口设置为全双工
```

# VLAN原理和配置

VLAN（Virtual Local Area Network），虚拟局域网。

- VLAN能够隔离广播域
- VLAN帧通过Tag区分不同的VLAN

> - TPID
> - PRI：优先级
> - CRI
> - VLAN ID：VLAN号

```bash
undo info en  //不会提示信息
vlan batch 5 to 15  //创建多个VLAN
int e0/0/3  //进入接口
dis port vlan  //查看接口类型
port link-type access  //改变接口类型
port default vlan 10  //将接口划分进VLAN10
dis th  //查看当前接口下的配置
q  //退出接口
dis port vlan  //查看接口相关信息
```

## 链路类型

- 用户主机和交换机之间的链路为接入链路（Access），交换机与交换机之间的链路为干道链路（Trunk）

## PVID

- 表示端口在缺省情况下所属的VLAN

## 端口类型

- Access端口接收到数据后，会添加VLAN Tag
- Access端口在转发数据前会去除VLAN Tag

**Access端口发送数据**

```flow
flowchat
st=>start: Access接口收到数据
cond=>condition: 有无tag标签？
op1=>operation: 根据PVID打上标签
cond1=>condition: 检查VLAN ID与PVID是否相同?
op2=>operation: 丢弃

e=>end: 收

st->cond
cond(yes)->cond1
cond(no)->op1->e
cond1(yes,right)->e
cond1(no)->op2
```

**Access端口发送数据**

```flow
flowchat
st=>start: Access发送数据包
cond=>condition: 检查VLAN ID与PVID是否相同？
op1=>operation: 去标签转发
op2=>operation: 丢弃

e=>end：
st->cond
cond(yes,right)->op1
cond(no)->op2
```

- Trunk端口收到帧时，如果帧不包含Tag，将打上端口的PVID，如果该帧包含Tag，则不改变
- 当Trunk端口发送帧时，该帧的VLAN ID在Trunk的允许发送列表中，若与端口的PVID相同时，则剥离Tag发送，若不同时，则直接发送

**Trunk端口接收数据**

```flow
flowchat
st=>start: Trunk接收数据包
op1=>operation: 检查allow-list
cond1=>condition: 是否被允许？
op2=>operation: 丢弃
cond2=>condition: 是否有Tag标签？
op3=>operation: 根据PVID打上标签

e=>end: 收

st->op1->cond1
cond1(yes,right)->cond2(yes)->e
cond2(no)->op3->e
cond1(no)->op2
```

**Trunk端口发送数据**

```flow
flowchat
st=>start: 发送数据包
op1=>operation: 检查allow-list
cond1=>condition: 是否允许？
cond2=>condition: 检查VLAN ID与PVID是否相同？
op3=>operation: 去标签发送
op4=>operation: 保持标签发送

e=>end: 丢弃

st->op1->cond1
cond1(yes)->cond2(yes)->op3
cond2(no)->op4
cond1(no)->e
```

- Access端口配置：
	1. 创建VLAN
	2. 进入接口，设置接口类型：`port link-type trunk`
	3. 把接口化劲VLAN：`port trunk allow-pass vlan 2 3`
	4. 修改PVID：`port trunk pvid vlan x`

**Hybrid**（华为私有）

- Hybrid端口既可以连接主机，也可以连接交换机
- Hybrid端口可以以Tagged或Untagged方式加入VLAN

**Hybrid接收数据**

```flow
flowchat
st=>start: Hybrid接收数据
cond1=>condition: 是否有Tag标签？
op1=>operation: 根据PVID打上标签
cond2=>condition: 检查tag/untag列表是否允许？
op2=>operation: 收

e=>end: 丢弃

st->cond1
cond1(yes)->cond2(yes)-op2
cond1(no)->op1->cond2(yes)->op2
cond2(no)->e
```

**Hybrid发送数据**

```flow
flowchat
st=>start: Hybrid发送数据
cond1=>condition: 检查tag/untag列表是否允许？
cond2=>condition: 允许tag-list（yes）？允许untag-list（no）？
op1=>operation: 去标签发
op2=>operation: 保持标签发

e=>end: 丢弃

st->cond1
cond1(yes)->cond2(yes)->op2
cond2(no)->op1
cond1(no)->e
```









