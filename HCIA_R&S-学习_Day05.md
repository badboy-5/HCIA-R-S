---
姓名：坏坏
学习时间：2020年4月13日
整理时间：2020年4月13日
---

[TOC]

----

参考博客：
[【CSDN】]()

# 复习回顾

**RIP**

- 报文：
	* `request`请求报文
	* `respones`回应报文
- 防环机制：
	* 水平分割
	* 毒性逆转
	* 触发更新
	* 16跳不可达

**OSPF**

- 五种报文：
	* `hello`报文
	* `DD`报文
	* `LSR`报文
	* `LSU`报文
	* `LSACK`报文
- 七种状态机：
	* `DOWN`
	* `Init`
	* `2-Way`
	* `Exstart`
	* `Exchange`
	* `Loading`
	* `Full`

- OSPF交互LSA（链路状态通告）
	* IP地址
	* 掩码
	* 链路类型
	* 接口类型
	* 带宽

**PPP**

- **LCP**（链路控制协议）
	* 魔术字
	* MRU（最大接受单元）
	* 认证参数

- **NCP**（网络控制协议）
	* `IPCP`互推地址的过程

# PPPoE原理和配置

- PPPoE协议通过在以太网上提供点到点的连接，建立PPP会话，使以太网中的主机能够连接到远端的宽带接入服务器上
- PPPoE特点：范围广、安全性高、计费方便

## PPPoE会话建立

| 阶段 | 描述 |
| ---- | ---- |
| 发现阶段 | 获取对方的以太网地址，以及确定唯一的PPPoE会话 |
| 会话阶段 | PPP协商阶段和PPP报文传输阶段  |
| 会话终结阶段 | 会话建立以后的任意时刻，发送报文结束PPPoE会话 |

## PPPoE协议报文

| 类型 | 描述 |
|:---:|:---:|
| PADI | PPPoE发现初始报文 |
| PADO | PPPoE发现提供报文 |
| PADR | PPPoE发现请求报文 |
| PADS | PPPoE发现会话确认报文 |
| PADT | PPPoE发现终止报文 |

> - PADI（PPPoE Active Discovery Initiation）报文：用户主机发起的PPPoE服务器探测报文，目的MAC地址为广播地址。查找PPPoE的服务器
> - PADO（PPPoE Active Discovery Offer）报文：PPPoE服务器收到PADI报文之后的回应报文，目的MAC地址为客户端主机的MAC地址。offer报文中包含分配给主机的IP地址
> - PADR（PPPoE Active Discovery Request）报文：用户主机收到PPPoE服务器回应的PADO报文后，单播发起的请求报文，目的地址为此用户选定的那个PPPoE服务器的MAC地址。
> - PADS（PPPoE Active Discovery Session Configuration）报文：PPPoE服务器分配一个唯一的会话进程ID，并通过PADS报文发送给主机。
> - PADT（PPPoE Active Discovery Terminate）报文：当用户或者服务器需要终止会话时，可以发送这种PADT报文。


- PPPoE发现阶段：
	* 客户端通过广播发送PADI报文，找PPPoE的服务器
	* PPPoE服务器收到客户端的PADI报文后，将客户端请求的服务与自己可以提供的服务进行比较，可以提供相应服务则会单播回应PADO报文
	* 客户端选择最先收到的PADO报文的PPPoE服务器，单播发送PADR报文
	* PPPoE服务器收到PADR报文后，会生成唯一的PPPoE Session ID，发送给客户端，会话建立成功
- PPPoE会话阶段：
	* 会话建立后，进行PPP协商（LCP、认证、NCP）
	* PPP协商成功后，就可以传输PPP数据
- PPPoE会话终结：
	* 客户端发送PADT报文，结束PPPoE会话

![1586830782179](E:%5CBad%5CPictures%5CTypora%20Picture%5C1586830782179.png)


## PPPoE Server配置步骤

1. 配置虚拟接口模板，并配置地址
2. 配置PPPoE Client分配地址池
3. 指定为PPPoE Client分配IP地址的地址池
4. 配置PPPoE Server作为认证方
5. 配置接口上启用PPPoE Server功能
6. 配置PPPoE Client指定DNS服务器

##  PPPoE Client的配置步骤

1. 创建Dialer接口并进入到Dialer接口视图
2. 使能共享的DCC能
3. 指定Dialer接口使用的Dialer bundle
4. 配置Dialer接口的IP地址
5. 配置PPPoE Client作为被认证方
6. 配置接口应用PPPoE Client
7. 配置接受PPPoE Server指定的DNS服务器

**实验**：如下拓扑，配置PPPoE，使PC与PPPoE Server互通

![1586843142570](E:%5CBad%5CPictures%5CTypora%20Picture%5C1586843142570.png)

# DNCP原理与配置

动态主机配置协议DHCP（Dynamic Host Configuration Protocol），可以减少管理圆的工作量，避免用户手动配置网络参数时造成地址冲突

## DHCP报文类型

| 报文类型 | 含义 |
|:---:|:---:|
| DHCP Discover | 客户端用来寻找DHCP服务器 |
| DHCP Offer | DHCP服务器用来响应DHCP Discover报文，携带各种配置信息 |
| DHCP Request | 客户端用来请求配置确认，或者续借租期 |
| DHCP ACK | 服务器对Request报文的确认响应 |
| DHCP NAK | 服务器对Request报文的拒绝响应 |
| DHCP Release | 客户端钥匙房地址时用来通知服务器 |

- DHCP工作原理

![1586845019165](E:%5CBad%5CPictures%5CTypora%20Picture%5C1586845019165.png)

- DHCP租期更新

![1586845154231](E:%5CBad%5CPictures%5CTypora%20Picture%5C1586845154231.png)

> IP租约期限到达50%时，DHCP客户端会请求更新IP地址租约

- DHCP重绑定

![1586845281570](E:%5CBad%5CPictures%5CTypora%20Picture%5C1586845281570.png)

> DHCP客户端在租约期限达到87.5%时，还没有收到服务器响应，会申请重绑IP

- IP地址释放
	* IP租约到期前都没有收到服务器的响应，客户端会停止使用此IP地址
	* DHCP客户端不再使用分配的IP地址，也会主动向DHCP服务器发哦送DHCP Release报文，释放IP地址

- 地址池
	* 全局地址池（可以配几个网段）
	* 接口地址池（在当前接口下，只能有一个网段）

**实验**：配置DHCP，使PC1与PC2自动获取IP地址。

![1586878618063](E:%5CBad%5CPictures%5CTypora%20Picture%5C1586878618063.png)

1. 配置接口地址池
2. 配合全局地址池
3. 配置DHCP，使两台PC获得不同网段的IP地址

# AAA

AAA是Authentication（认证）、Authorization（授权）和Accounting（计费）的简称

- 配置AAA步骤：
	*  起aaa（`aaa`）
	*  配置本地用户和密码（`local-user bad password cipher huawei@123  `）
	*  应用的服务类型（`local-user bad service-type telnet  `）
	*  设置权限（`local-user bad privilege level 5`）
	*  允许同时登录的用户数量（`user-interface vty 0 4`）
	*  修改认证模式（`authentication-mode aaa`）

**实验**：配置Telnet和Stelnet登录

[【参考GitHub源代码】](https://github.com/bad-5/Routing-Switching/tree/master/WLAN)

# 访问控制列表

访问控制列表ACL（Access Control List）

- 










