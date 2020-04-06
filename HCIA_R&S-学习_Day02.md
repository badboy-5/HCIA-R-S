	姓名：坏坏
	学习时间：2020年3月24日
	整理时间：2020年3月27日

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

**实验**：如下配置两台PC，要求实现两台PC的互相通信。

![1585201100238](E:%5CBad%5CPictures%5CTypora%20Picture%5C1585201100238.png)

```bash
为PC各自配置IP，网关设置为G0/0/0口和G0/0/1接口的IP
配置AR3的接口IP，并开启相关的服务

<Huawei>sy  //进入系统视图
[Huawei]sys R1  //更改设备名称
[R1]int g0/0/0  //进入g0/0/0接口
[R1-GigabitEthernet0/0/0]ip add 192.168.10.254 24  //配置IP地址
Mar 26 2020 13:31:37-08:00 R1 %%01IFNET/4/LINK_STATE(l)[0]:The line protocol IP on the interface GigabitEthernet0/0/0 has entered the UP state. 
[R1-GigabitEthernet0/0/0]undo info en  //不会提示信息
[R1-GigabitEthernet0/0/0]arp-proxy enable  //开启ARP代理
[R1-GigabitEthernet0/0/0]int g0/0/1
[R1-GigabitEthernet0/0/1]ip add 192.168.20.254 24
[R1-GigabitEthernet0/0/1]arp-proxy enable 
[R1-GigabitEthernet0/0/1]dis ip int bri  //查看所有的接口信息，检查IP地址是否配上以及接口是否双up
Interface                         IP Address/Mask      Physical   Protocol  
GigabitEthernet0/0/0              192.168.10.254/24    up         up     
GigabitEthernet0/0/1              192.168.20.254/24    up         up     
[R1-GigabitEthernet0/0/1]dis arp all  //查看ARP表项

在PC上做连通性测试
```

> 1. 可以通过配置网关实现互通，网关地址为路由器与PC接口的IP
> 2. 通过ARP代理实现互通，需要改变子网掩码使不同网段的IP处于同一网段，如本题中的可以将子网掩码修改为255.255.192.0，即可不通过网关实现互通

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
undo negotiation auto  //关闭自协商
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

**实验**：如下图配置PC的IP地址，需求相同VLAN可以互通，不同VLAN不能互通。

![1585204012630](E:%5CBad%5CPictures%5CTypora%20Picture%5C1585204012630.png)

```bash

[SW1]dis vlan  //查看VLAN
[SW1]vlan batch 10 20  //创建VLAN10、VLAN20
[SW1]int e0/0/2  //进入e0/0/2接口
[SW1-Ethernet0/0/2]port link-type access  //设置接口类型为Access
[SW1-Ethernet0/0/2]port default vlan 10  //默认划分进VLAN10
[SW1-Ethernet0/0/2]dis th  //查看当前接口下的配置命令
[SW1-Ethernet0/0/2]q  //退出当前接口

# 同样方法配置e0/0/3接口，划分进VLAN 20

[SW1]int g0/0/1  //进入g0/0/1接口
[SW1-GigabitEthernet0/0/1]port link-type trunk  //配置接口类型为Trunk
[SW1-GigabitEthernet0/0/1]port trunk allow-pass vlan 10 20  //设置允许通过的VLAN为10 20 ，VLAN1默认允许通过

#SW2相同的配置

#做连通性测试
```

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

**实验**：按照如下拓扑，配置相关IP地址。
需求：
1. 不同楼层的HR部门和市场部门实现部门内部通信
2. 两部门之间不允许通信
3. IT部门可以访问任意部门

![1585207113199](E:%5CBad%5CPictures%5CTypora%20Picture%5C1585207113199.png)

```bash
[SW1]vlan batch 10 20 30  //创建VLAN10、20、30
[SW1]dis vlan  //查看是否创建
[SW1]int e0/0/3  //进入e0/0/3接口
[SW1-Ethernet0/0/3]port hybrid untagged vlan 20 30  //设置允许通信的VLAN
[SW1-Ethernet0/0/3]port hybrid pvid vlan 20  //设置PVID
[SW1-Ethernet0/0/3]dis th  //查看当前接口下的命令

#同样方法配置e0/0/2接口
port hybrid pvid vlan 10
port hybrid untagged vlan 10 30

#配置e0/0/4接口
port hybrid pvid vlan 30
port hybrid untagged vlan 10 20 30

#配置e/0/1接口
port hybrid tagged vlan 10 20 30
默认PVID是VLAN 1

#SW2同样的配置

#进行连通性测试
```

# VLAN的划分

- 基于端口（最常见）
- 基于MAC地址
- 基于IP子网划分
- 基于协议划分
- 基于策略

**配置命令**：

```bash
undo info en  //不会提示信息
vlan 5  //创建一个VLAN
vlan batch 5 to 15  //创建多个VLAN
dis port vlan  //查看接口类型
port link-type access  //配置access接口类型
port link-type trunk  //配置trunk接口类型
port trunk allow-pass vlan 10 20  //配置允许通过的VLAN
port default vlan 10  //将接口划分进VLAN10
dis th  //查看当前接口下的配置
q  //退出接口
dis port vlan  //查看接口相关信息
```

# VLAN间路由

- 单臂路由
- 三层交换

**实验**：如下拓扑图，为PC配置IP地址。配置单臂路由，实现PC间互通。

![1585237839901](E:%5CBad%5CPictures%5CTypora%20Picture%5C1585237839901.png)

```bash
#先给PC配置相应的IP地址，网关254

[SW1]vlan batch 10 20 30  //创建VLAN
[SW1]dis vlan  //查看VLAN是否创建成功
[SW1]int g0/0/2  //进入g0/0/2接口
[SW1-GigabitEthernet0/0/2]port link-type access  //配置接口类型为access
[SW1-GigabitEthernet0/0/2]port default vlan 10  //划分默认VLAN

#同样的方法配置g0/0/3、g0/0/4接口

#配置trunk接口，并允许所有VLAN通过
[SW1]int g0/0/1
[SW1-GigabitEthernet0/0/1]port link-type trunk  //配置接口类型为trunk   
[SW1-GigabitEthernet0/0/1]port trunk all vlan all  //允许所有VLAN通过

#在R1上配置子接口
[R1]int g0/0/0.1  //配置子接口
[R1-GigabitEthernet0/0/0.1]ip add 192.168.10.254 24  //为子接口配置IP
#同样方法配置其他子接口
[R1]dis ip int br  //查看所有接口详细信息

#封装VLAN号
[R1]int g0/0/0.1
[R1-GigabitEthernet0/0/0.1]dot1q termination vid 10  //指定vid，即这个接口对应的VLAN ID
[R1-GigabitEthernet0/0/0.1]arp broadcast enable  //开启ARP的广播功能

#同样方法配置其他的子接口

#进行连通性测试
```

1. 把PC划分到相应的VLAN
2. 把g0/0/1接口配置成trunk，并允许所有VLAN通过
3. 配置路由器的子接口配置IP地址
4. 子接口配置VLAN ID封装（`dot1q termination vid 10`）
5. 接口开启arp广播（`arp broadcast enable`）

**实验**：如下拓扑图，配置相应IP地址。配置三层交换，使PC间互通。

![1585239930825](E:%5CBad%5CPictures%5CTypora%20Picture%5C1585239930825.png)

```bash
[SW1]int Vlanif 10  //创建VLAN10
[SW1-Vlanif10]ip add 192.168.10.254 24  //配置IP地址
[SW1-Vlanif10]int vlanif 20
[SW1-Vlanif20]ip add 192.168.20.254 24
[SW1-Vlanif20]int vlanif 30
[SW1-Vlanif30]ip add 192.168.30.254 24

#进行连通性测试
```

# STP原理与配置

生成树协议（Spanning Tree Protocol），可以在提高可靠性的同时又能避免环路带来的各种问题。

- 二层交换网络
	* 交换机之间通过多条链路互联时，虽然能够提升网络可靠性，但同时也会带来环路问题
- 广播风暴
	* 环路会引起广播风暴
	* 网络中的主机会受到重复数据帧
- MAC地址表震荡

## STP的作用

通过阻塞端口来消除环路，并能够实现链路备份的目的。

- 构建STP：
1. 选举一个根桥（MAC地址越小越优）
2. 每个非根交换机选举一个根端口（RP）
3. 每个网段选举一个指定端口（DP）
4. 阻塞非根、非指定端口（AP）

- 根桥选举

1. 每台交换机启动STP后，都会认为自己是根桥
2. 交换机向外发送BPDU（桥协议数据单元），小的为根桥

- 根端口选举

非根交换机在选举根端口时分别依据该端口的根路径开销、对端BID、对端PID和本端PID

> - 根路径开销：交换机去往根桥的路径的开销
> - 对端BID：发送端的桥ID
> - 对端PID：对端的端口ID
> - 本端PID：本端的端口ID

- 指定端口选举
	* 非根交换机在选举指定端口（DP）时分别依据根路径开销、BID、PID
	* 未被选举为根端口（RP）或指定端口（BP）的端口为预备端口（AP），将会被阻塞

**实验**：

![1585294890972](E:%5CBad%5CPictures%5CTypora%20Picture%5C1585294890972.png)

SW1：4c1f-cc5c-74c7
SW2：4c1f-cc2d-7013
SW3：4c1f-cc80-7370
SW4：4c1f-cc6f-1691

1. 选举根桥
	* 交换BPDU，比较BPDU，相同
	* 比较MAC地址，SW2的MAC最小，选举为根桥
2. 选举根端口
	* 比较路径开销，SW1在1号线路到达根桥路径开销最小，所以SW1的1接口为RP（同理SW3的1接口、SW4的1接口都为RP）
	* 如果路径开销相同，比较BID（优先级、MAC地址）
	* 如果BID也相同，则比较PID（优先级、端口号）
3. 选举指定端口
	* 在网络上（每条线路上）选举指定端口
	* 根桥开销为0，所以SW2的1、2、3接口都为DP
	* 4号线路上走1、3线路开销相同，比较BID（优先级、MAC地址），SW1的MAC地址小，则SW1的2接口为DP，SW3的3接口为AP
	* 5号线路上走2、3线路开销相同，比较BID（优先级、MAC地址），SW4的MAC地址小，则SW4的2接口为DP，SW3的2接口为AP

```bash
#查看MAC地址
[SW1]dis stp  //查看MAC地址

[SW1]dis stp bri  //查看SW1的STP
 MSTID  Port                        Role  STP State     Protection
   0    Ethernet0/0/1               ROOT  FORWARDING      NONE
   0    Ethernet0/0/2               DESI  FORWARDING      NONE
#Ethernet0/0/1为RP，FORWARDING为正常转发数据，Ethernet0/0/2为DP

[SW2]dis stp bri  //查看SW2的STP
 MSTID  Port                        Role  STP State     Protection
   0    Ethernet0/0/1               DESI  FORWARDING      NONE
   0    Ethernet0/0/2               DESI  FORWARDING      NONE
   0    Ethernet0/0/3               DESI  FORWARDING      NONE
   0    Ethernet0/0/4               DESI  FORWARDING      NONE
   0    Ethernet0/0/5               DESI  FORWARDING      NONE

[SW3]dis stp bri  //查看SW3的STP
 MSTID  Port                        Role  STP State     Protection
   0    Ethernet0/0/1               ROOT  FORWARDING      NONE
   0    Ethernet0/0/2               ALTE  DISCARDING      NONE
   0    Ethernet0/0/3               ALTE  DISCARDING      NONE
#Ethernet0/0/1为RP，数据正常转发，Ethernet0/0/2和Ethernet0/0/3为AP，DISCARDING端口关闭，不转发数据

[SW4]dis stp bri  //查看SW4的STP
 MSTID  Port                        Role  STP State     Protection
   0    Ethernet0/0/1               ROOT  FORWARDING      NONE
   0    Ethernet0/0/2               DESI  FORWARDING      NONE
```

**拓展**：使SW1为根桥，SW3位次根桥


```bash
[SW1]stp root primary  //使SW1成为主根桥
[SW1]dis stp  //查看cost优先级为0


[SW3]stp root secondary  //使SW3成为次根桥
[SW3]dis stp  //查看cost优先级为4096

#增长为12次方增长，下一个是8192，一次类推

[SW1]int e0/0/1
[SW1-Ethernet0/0/1]stp cost ?  //修改接口开销
  INTEGER<1-200000000>  Port path cost
[SW1-Ethernet0/0/1]stp cost 55
```

## 端口状态转换

- `Disabled` 关闭状态，端口禁用或链路失效
- `Blocking` 启动状态，端口初始化或使能
- `Listening` 侦听状态，端口被选为根端口或指定端口
- `Learning` 学习MAC地址状态
- `Forwarding` 数据转发状态

> STP基于计时器，RSTP基于P/A

# BPDU

包含桥ID、路径开销、端口ID、计时器等参数

- `Message Age` 生存时间
- `Max Age` 最大生存时间
- `Hello Time` 一般为2s
- `Fwd Delay` 一般为15s

## 计时器

- BPDU间隔时间为`Hello Time`时间
- 配置BPDU报文每经过一个交换机，Message Age都加1
- 当Message Age大于Max Age，非根桥会丢弃该配置BPDU

## 根桥故障

- 非根桥会在BPDU老化之后开始根桥的重新选举

## 直连链路故障

- 检测到直连链路物理故障后，会将预备端口转换为根端口
- 预备端口会在30s后恢复到转发状态

## 非直连链路故障

- 预备端口恢复到转发状态大约需要50秒


    以上内容均属原创，如有不详或错误，敬请指出。

<div class="post-copyright">
    <div class="author">
        <b>本文作者： </b>
        <a href="https://blog.csdn.net/qq_45668124" target="_blank">
            <b>坏坏</b> 
        </a> 
    </div>
    <div class="link">
        <b>本文链接： </b>
			https://blog.csdn.net/qq_45668124/article/details/105144472 
    </div>
    <div class="copyright">
        <b> 版权声明： </b>
        本博客所有文章除特别声明外，均采用  
        <a href="https://creativecommons.org/licenses/by-nc-sa/4.0/">
            CC BY-NC-SA 4.0 
        </a> 许可协议。转载请注明出处！
    </div>
</div>