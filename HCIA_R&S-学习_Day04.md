---
姓名：坏坏
学习时间：2020年4月5日
整理时间：2020年4月9日
---

[TOC]

----

参考博客：
[【CSDN】]()

# 链路状态路由协议——OSPF

开放式最短路径优先OSPF（Open Shortest Path First）协议是IETF定义的一种基于链路状态的内部网关路由协议

**OSPF与RIP比较**

|         内容         |       OSPF       |      RIPv2       |      RIPv1       |
| :------------------: | :--------------: | :--------------: | :--------------: |
|       协议类型       |     链路状态     |     距离矢量     |     距离矢量     |
|         CIDR         |       支持       |       支持       |      不支持      |
|         VLSM         |       支持       |       支持       |      不支持      |
|       自动汇总       |      不支持      |       支持       |       支持       |
|       手动汇总       |       支持       |       支持       |      不支持      |
|      路由的泛洪      | 周期性的组播更新 | 周期性的组播更新 | 周期性的组播更新 |
| 路径的开销（度量值） |       带宽       |       跳数       |       跳数       |
|    路由的收敛速度    |        快        |        慢        |        慢        |
|      跳数的限制      |     没有限制     |     限制15跳     |     限制15跳     |
|         认证         |       支持       |       支持       |      不支持      |
|      层次化网络      |   支持（区域）   |      不支持      |      不支持      |
|       路由更新       |   事件触发更新   |    路由表更新    |    路由表更新    |
|    路由的计算方法    |       SPF        |   Bellman-ford   |   Bellman-ford   |


> - OSPF组播地址为224.0.0.5或224.0.0.6
> - RIPv2组播地址为224.0.0.9

## OSPF原理

- LSA泛洪（链路状态通告）包含：
	* 接口信息
	* IP地址
	* 子网掩码
	* 带宽
	* 链路信息（类型）

**OSPF工作原理**：

![1586185548103](E:%5CBad%5CPictures%5CTypora%20Picture%5C1586185548103.png)

1. 路由器发送LSA（链路状态通告）
2. 每台路由器收到LSA后，会把它放在自己的数据库中（LSDB），完成所有的LSA泛洪之后，每台路由器的LSDB是一样的
3. 根据自己的数据库，以自己为根（定点，节点），使用SPF算法，计算到目标网络的最短路径，然后将此路径放入到路由表中

## OSPF报文

- OSPF报文封装在IP报文中，协议号为89
- 五中报文类型：
	* `Hello`报文，用来建立并维护邻居关系
	* `DD(Database Description)`报文（也叫DBD报文），数据库的目录信息
	* `LSR(LSA Request)`报文（LSA请求报文）
	* `LSU(LSA Update)`报文（LSA更新报文）
	* `LSACK(Link State Acknowledgment)`报文（LSA确认报文）

## Router-id

用来标识一台OSPF的路由器，使用点分十进制来表示，所有的OSPF报文中都会携带Router-id参数

**实验一**：如图配置IP，配置OSPF，要求R1、R2、R3互通。

![1586189592841](E:%5CBad%5CPictures%5CTypora%20Picture%5C1586189592841.png)

```bash
[R1]ospf 1  //指定OSPF的进程号1               
[R1-ospf-1]area 0  //进入骨干区域
[R1-ospf-1-area-0.0.0.0]network 12.1.1.0 0.0.0.255  //宣告网段
[R1-ospf-1-area-0.0.0.0]net 1.1.1.1 0.0.0.0  //宣告精确地址
```

- 反掩码（通配符掩码）
	* **作用**：匹配的是IP地址的范围
	* 二进制0，表示精确匹配
	* 二进制1，表示一个范围

- OSPF选举Router-id过程：
	* 手动指定的Router-id为最优的
	* 没有手动指定，会选择最大的已经激活的物理地址

> - 如果开始启动了OSPF进程，没有配置Router-id，那么OSPF会自动选举Router-id
> - 如果此手动指定Router-id需要重新启动OSPF进程（用户视图下执行`reset ospf pro`），新的Router-id才能生效

- 邻居状态机
	* `Init`交换Hello报文
	* `2Way`交换Hello报文
	* `Exstart`交换DD报文
	* `Exchange`交换DD报文
	* `Loading`交换LSR和LSU报文
	* `Full`交换LSACK报文

![1586192900115](E:%5CBad%5CPictures%5CTypora%20Picture%5C1586192900115.png)

> - **邻居**:两台路由器的状态到达2Way，建立邻居关系。不能交换信息
> - **邻接**:两台路由器的状态到达Full，建立邻接关系。可以交换信息

- 邻居发现：通过Hello报文来发现和维持OSPF邻居关系

## OSPF支持的网络类型

- 广播类型(以太网)
- 点到点类型（P2P）
- 非广播多路访问（NBMA）
- 点到多点（P2NP）

## DR&BDR

- **DR**：指定路由器
- **BDR**：备份指定路由器
- **DR-Other**：其他指定路由器

> DR和BDR可以减少OSPF网络中的LSA，可以减少OSPF网络的流量泛洪

## DR&BDR选举

- DR是基于端口的路由器优先级进行选举的，默认优先级为1
- 优先级高的将成为DR，次高的将成为BDR，优先级为0则不参与选举
- 如果优先级相同，选举Router-id大的为DR，次大的成为BDR

> - DR和BDR具有抢先机制，谁先启动，谁就成为DR或BDR
> - 更改优先级为0`[R2-GigabitEthernet0/0/1]ospf dr-priority 0`
> - 更改优先级后需要重启OSPF进程`[R2]reset ospf pro`


**DR、BDR、DR-other的关系**：

![Day04_OSPF-邻居邻接关系](E:%5CBad%5CPictures%5CTypora%20Picture%5CDay04_OSPF-%E9%82%BB%E5%B1%85%E9%82%BB%E6%8E%A5%E5%85%B3%E7%B3%BB.png)

> - 查看OSPF领句关系，系统视图下`dis ospf peer bri`
> - 重启OSPF进程用户视图下`reset ospf process`
> - 修改OSPF优先级，在接口视图下`ospf dr-priority 10`

## OSPF区域

**划分规则**：

- 划分骨干区域和非骨干区域，有且只有一个骨干区域
- 非骨干区域必须有一个接口跟骨干区域相连

**划分区域的目的**：

- 防止环路
- 减少网络中的LSA泛洪
- Area 0为骨干区域，其余为非骨干区域
- `ABR`区域边界路由器，连接两个或两个以上区域的路由器
- `IR`非骨干区域内的路由器
- `ASBR`自制系统的边界路由器，同时运行两种路由协议的路由器，连接两个自治系统的路由器

> - 每个区域都维护一个独立的LSDB
> - Area 0是骨干区域，其他区域都必须与此区域相连
> - 不规则链路可以通过Vlink解决

**OSPF无法建立邻居关系**：

- router-id重复
- 区域号不一致

## OSPF开销

```bash
# 修改cost值
[R1]interface GigabitEthernet 0/0/0
[R1-GigabitEthernet0/0/0]ospf cost 20 

# 修改带宽
[R1]ospf
[R1-ospf-1]bandwidth-reference 10000

```

## OSPF认证

- 接口认证
- 区域认证

```bash
# 基于接口认证
[R1]interface GigabitEthernet0/0/0
[R1-GigabitEthernet0/0/0]ospf authentication-mode md5 1 cipher huawei
```

> 两种都配置了的话，接口认证优于区域认证

**实验二**：如图配置IP地址，需求使用OSPF配置，实现全网互通。

![1586316708045](E:%5CBad%5CPictures%5CTypora%20Picture%5C1586316708045.png)

- 配置完成后，可以在R1到R2的上抓包，可以看到以下信息

```bash
OSPF Header :
	Version: 2  //主版本号
	Message Type: Hello Packet (1) 
	Packet Length: 48  //包长度
	Source 0SPF Router: 2.2.2.2  //R2的Router-id
	Area ID: 0.0.0. 0 (Backbone)  //区域
	Checksum: 0x8670 [ correct]
	Auth Type: Null (0)  //OSPF认证字段，没有配置为空
	Auth Data ( none): 0000000000000000
0SPF Hello Packet  //Hello报文
	Network Mask: 255. 255.255.0 
	Hello Interval [sec]: 10  //Hello报文间隔时间为10s
> Options: 0x02， (E) External Routing
	Router Priority: 1  //路由优先级
	Router Dead Interval [sec]: 40  //死亡时间40s，是Hello报文存活时间的四倍
	Designated Router: 172.16.12.1  //DR为R1
	Backup Designated Router: 172.16.12.2  //BDR为R2
	Active Neighbor: 1.1.1. 1
```

**拓展**：OSPF多区域配置，需求全网互通。

![1586418750275](E:%5CBad%5CPictures%5CTypora%20Picture%5C1586418750275.png)

# 广域网基本原理

## HDLC&PPP原理与配置

高级数据链路控制（**HDLC**）和点对点协议(**PPP**)是两种典型的串口封装协议

> **PPP**是IE面试会考到的重点

**HDLC**

- **HDLC**(High-level Data Link Control)：高级数据链路控制，面向比特的链路层协议
- HDLC有三种类型的帧：信息帧、监控帧、无编号帧
- 串行接口可以借用Loopback接口的IP地址和对端建立连接

**实验三**：如图配置IP地址，使用HDLC接口调用配置接口。

![1586418390980](E:%5CBad%5CPictures%5CTypora%20Picture%5C1586418390980.png)

```bash
[R1]interface Serial 1/0/0  //进入串口
[R1-Serial1/0/0]link-protocol hdlc  //接口默认是PPP，改为hdlc
[R1-Serial1/0/0]ip address unnumbered interface loopBack 0  //借用地址
[R1]ip route-static 12.1.1.0 30 s1/0/0  //配置静态路由
```

**PPP**

- PPP是点到点的链路层协议
- 主要用于在全双工的同异步链路上进行点到点的数据传输
- PPP组件：

| 名称 | 作用 |
|:---:|:---:|
| 链路控制协议（Link Control Protocol） | 用来建立、拆除和监控PPP数据链路 |
| 网络层控制协议（Network Control Protocol） | 用于对不同的网络层协议进行连接建立和参数协商 |

- PPP链路建立过程

![1586358056646](E:%5CBad%5CPictures%5CTypora%20Picture%5C1586358056646.png)

- PPP链路中启动后会进入**Establish**阶段，LCP链路控制协议交互
- LCP（链路层控制协议）中交换内容：
	* 魔术字（防环）
	* MRU（最大接收单元，1500）
	* 认证参数
	* 链路信息
- PPP认证需要发送**Request**请求
	* 认可，对端会回复ACK，会建立连接
	* 不认可，对端会回复NAK，不会建立连接
	* 无法识别，对端会回复reject，不会建立连接
- **Request**在**Establish**阶段不被认可或无法识别会进入FALL阶段，建立连接失败
- **Request**通过认可通过后会进入**OPENED**阶段，认证不通过会进入FALL，进入终结阶段，建立连接失败
- 认证通过，会进入Network阶段，也叫NCP（网络层控制协议）阶段，然后进入IPCP阶段，互推地址，建立连接成功
- 关闭连接，进入CLOSING，然后进入终结阶段，关闭连接

**实验四-PAP认证**：如图拓扑，配置IP地址，配置PPP的PAP认证。

> 需要添加接口卡，使用串行线连接串行接口

![1586422492493](E:%5CBad%5CPictures%5CTypora%20Picture%5C1586422492493.png)

**LCP报文**：

| 报文类型 | 作用 |
|:----:|:---- |
| Configure-Request | 包含发送者试图与对端建立连接时使用的参数列表 |
| Configure-Ack | 表示完全接受对端发送的Configure-Request的餐宿取值 |
| Configure-Nak | 表示对端发送的Configure-Request中的某些参数取值在本端不被认可 |
| Configure-Reject | 表示对端发送的Configure-Request中的某些参数本端不能识别 |

**LCP协商参数**：

| 参数 | 作用 | 缺省值 |
|:----:|:----|:----:|
| 最大接受单元——MRU | PPP数据帧中Information字段和Padding字段的总长度 | 1500字节 |
| 认证协议  | 认证对端使用的认证协议 | 不认证   |
| 魔术字 | 魔术字为一个随机产生的数字，用于检测链路环路，如果收到的LCP报文中的魔术字和本端产生的魔术字相同，则认为链路有环路 | 启用     |

> - 认证的方式有两种：
> 	* 明文认证——PAP
> 	* 密文认证——Chap，需要MD5的计算

**PPP认证模式-Chap**

- 认证方发送Challenge报文，发起认证请求
- 被认证方收到报文后，会进行MD5计算，回复Response报文
- 认证方收到报文后，会进行加密的报文与收到的报文进行信息的对比
- 正确会回复Success报文，错误会回复Failure报文

**实验四-Chap认证**：如图配合IP地址，配置PPP的Chap认证。

![1586422585646](E:%5CBad%5CPictures%5CTypora%20Picture%5C1586422585646.png)

**IPCP静态地址协商**

![1586361967868](E:%5CBad%5CPictures%5CTypora%20Picture%5C1586361967868.png)

- R1发送Configure-Request报文，包含自己接口的IP地址
- R2收到报文后，会跟接口地址匹配，不冲突，会回复一个Configure-Ack的报文，同时会发送一个Configure-Request报文
- R1同样会与接口地址匹配，不冲突，会回复一个Configure-Ack报文
- 建立连接，可以互推地址

**IPCP动态地址协商**

![1586361995117](E:%5CBad%5CPictures%5CTypora%20Picture%5C1586361995117.png)

- RTA的s1/0/0没有配置IP地址，RTB有IP地址池
- RTA会发送一个请求报文附带0.0.0.0这个IP地址给RTB
- RTB收到后，会回复一个Nak报文，并且附带从地址池中选出一个没有使用的IP地址
- RTA收到报文后，会请求使用附带的IP地址
- RTB收到报文后，会回复一个Ack报文，确认此IP地址可以使用
- RTB发送Configure-Request报文，附带本接口的IP地址
- RTA收到报文后，会与本地接口的IP地址匹配，在同一个网段，就会回复Ack报文
- 完成动态地址协商的过程
