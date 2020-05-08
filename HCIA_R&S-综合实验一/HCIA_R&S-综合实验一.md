---
姓名：坏坏
实验时间：2020年4月19日
整理时间：2020年4月19日

---
参考博客：
[【CSDN】](https://blog.csdn.net/qq_45668124/article/details/105616774)

# HCIA-R&S综合实验一

**如下拓扑图**：

![](E:%5CBad%5CPictures%5CTypora%20Picture%5CHCIA_R&S-%E7%BB%BC%E5%90%88%E5%AE%9E%E9%AA%8C%E4%B8%80.png)

**实验要求**：
1. 内部所有网段从192.168.0.0/24中划分，运营商分配202.102.24.96/30
    和120.202.249.192/30两个网段给边界路由器；
2. 内部客户端A属于VLAN 10，内部客户端B属于VLAN 20；
3. 内部三台交换机之间的双链路使用以太网通道将链路聚合；
4. 多层交换VLAN间互通；
5. 阻塞内部二层交换机B上面向内部交换机A的两个端口；
6. 内部路由使用OSPF协议；
7. 分别映射两台FTP服务器的TCP 21端口至两台边界路由器的外部端口；
8. 不允许内部客户端A访问FTP服务器A；
   不允许内部客户端B访问FTP服务器B的TCP 21端口

## 子网划分

- **ISP运营商---http-pc**
	* G0/0/0：100.100.100.1/24
	* http-pc：100.100.100.2/24
- **ISP运营商---边界路由器A**
	* G0/0/1:202.102.24.97/30
	* 边界路由器A：G0/0/0：202.102.24.98/30
- **ISP运营商---边界路由器B**
	* G0/0/2：120.202.249.193/30
	* 边界路由器B：G0/0/0：120.202.249.194/30

> **分析**：
> - **ISP运营商---边界路由器A**
> 	* 运营商分配的网段为：202.102.24.96/30
> 	* 网段为：202.102.24.96
> 	* 广播地址：202.102.24.99
> 	* 可用子网：202.102.24.97和202.102.24.98
> - **ISP运营商---边界路由器B**
> 	* 运营商分配的网段为：120.202.249.192/30
> 	* 网段为：120.202.249.192
> 	* 广播地址：120.202.249.195
> 	* 可用子网：120.202.249.193和120.202.249.194

- **边界路由器A---SW3---边界路由器B**
	* 边界路由器A：G0/0/1：192.168.0.97/27
	* 边界路由器B：G0/0/1：192.168.0.98/27
	* SW3-Vlanif1：192.168.0.126/27

> **分析：**
> - 给定网段为：192.168.0.96/27
> - 网段：192.168.0.96
> - 广播地址：192.168.0.127
> - 可用子网：192.168.0.97-192.168.0.126

- **内部路由器A---SW3---内部路由器B**
	* 内部路由器A：G0/0/0：192.168.0.65/27
	* 内部路由器B：G0/0/0：192.168.0.66/27
	* SW3-Vlanif100：192.168.0.94/27

> **分析**：
> - 给定网段为：192.168.0.64/27
> - 网段：192.168.0.64
> - 广播地址：192.168.0.95
> - 可用子网：192.168.0.65-192.168.0.94

- **内部路由器A---FTP_A**
	* 内部路由器A：G0/0/1：192.168.0.1/27
	* FTP_A：E0/0/0：192.168.0.2/27

> **分析**：
> - 给定网段：192.168.0.0/27
> - 网段：192.168.0.0
> - 广播地址：192.168.0.32
> - 可用子网：192.168.0.1-192.168.0.31

- **内部路由器B---FTP_B**
	* 内部路由器B：G/0/0/1：192.168.0.33/27
	* FTP_B：E0/0/0：192.168.0.34/27

> **分析**：
> - 给定网段：192.168.0.32/27
> - 网段：192.168.0.32
> - 广播地址：192.168.0.63
> - 可用子网：192.168.0.33-192.168.0.62

- **SW_A---PC_A**
	* SW_A-Vlanif10:192.168.0.158/27
	* PC_A:192.168.0.129/27

> **分析**：
> - 给定网段：192.168.0.128/27
> - 网段：192.168.0.128
> - 广播地址：192.168.0.159
> - 可用子网：192.168.0.129-192.168.0.158

- **SW_B---PC_B**
	* SW_B-Vlanif20：192.168.0.222/27
	* PC_B：192.168.0.193/27

> **分析**：
> - 给定网段：192.168.0.192/27
> - 网段：192.168.0.192
> - 广播地址：192.168.0.223
> - 可用子网：192.168.0.193-192.168.0.222

## 配置相关IP地址

- **http-pc**：
	* IP：100.100.100.2/24
	* 网关：100.100.100.1/24

- **ISP运营商**

```bash
[ISP]int g0/0/0
[ISP-GigabitEthernet0/0/0]ip ad 100.100.100.1 24
[ISP-GigabitEthernet0/0/0]int g0/0/1
[ISP-GigabitEthernet0/0/1]ip ad 202.102.24.97 30
[ISP-GigabitEthernet0/0/1]int g0/0/2
[ISP-GigabitEthernet0/0/2]ip ad 120.202.249.193 30
[ISP-GigabitEthernet0/0/2]q
[ISP]dis ip int brief 
Interface                         IP Address/Mask      Physical   Protocol  
GigabitEthernet0/0/0              100.100.100.1/24     up         up    
GigabitEthernet0/0/1              202.102.24.97/30     up         up    
GigabitEthernet0/0/2              120.202.249.193/30   up         up    
NULL0                             unassigned           up         up(s) 
```

- **边界路由器A**

```bash
[BoadeA]int g0/0/0
[BoadeA-GigabitEthernet0/0/0]ip ad 202.102.24.98 30
[BoadeA-GigabitEthernet0/0/0]
[BoadeA-GigabitEthernet0/0/0]int g0/0/1
[BoadeA-GigabitEthernet0/0/1]ip ad 192.168.0.97 27
[BoadeA-GigabitEthernet0/0/1]q
[BoadeA]dis ip int bri
Interface                         IP Address/Mask      Physical   Protocol  
GigabitEthernet0/0/0              202.102.24.98/30     up         up   
GigabitEthernet0/0/1              192.168.0.97/27      up         up   
GigabitEthernet0/0/2              unassigned           down       down 
NULL0                             unassigned           up         up(s) 
[BoadeA]
```

- **边界路由器B**

```bash
[BoadeB]int g0/0/0
[BoadeB-GigabitEthernet0/0/0]ip ad 120.202.249.194 30
[BoadeB-GigabitEthernet0/0/0]int g0/0/1
[BoadeB-GigabitEthernet0/0/1]ip ad 192.168.0.98 27
[BoadeB-GigabitEthernet0/0/1]q
[BoadeB]dis ip int bri
Interface                         IP Address/Mask      Physical   Protocol  
GigabitEthernet0/0/0              120.202.249.194/30   up         up   
GigabitEthernet0/0/1              192.168.0.98/27      up         up   
GigabitEthernet0/0/2              unassigned           down       down 
NULL0                             unassigned           up         up(s)
[BoadeB]
```

- **内部路由器A**

```bash
[ltemalA]int g0/0/0
[ltemalA-GigabitEthernet0/0/0]ip ad 192.168.0.65 27
[ltemalA-GigabitEthernet0/0/0]int g0/0/1
[ltemalA-GigabitEthernet0/0/1]ip ad 192.168.0.1 27
[ltemalA-GigabitEthernet0/0/1]q
[ltemalA]dis ip int bri
Interface                         IP Address/Mask      Physical   Protocol  
GigabitEthernet0/0/0              192.168.0.65/27      up         up   
GigabitEthernet0/0/1              192.168.0.1/27       up         up   
GigabitEthernet0/0/2              unassigned           down       down 
NULL0                             unassigned           up         up(s) 
[ltemalA]
```

- **内部路由器B**

```bash
[ltemalB]int g0/0/0
[ltemalB-GigabitEthernet0/0/0]ip ad 192.168.0.66 27
[ltemalB-GigabitEthernet0/0/0]int g0/0/1
[ltemalB-GigabitEthernet0/0/1]ip ad 192.168.0.33 27
[ltemalB-GigabitEthernet0/0/1]q
[ltemalB]dis ip int bri
Interface                         IP Address/Mask      Physical   Protocol  
GigabitEthernet0/0/0              192.168.0.66/27      up         up     
GigabitEthernet0/0/1              192.168.0.33/27      up         up     
GigabitEthernet0/0/2              unassigned           down       down   
NULL0                             unassigned           up         up(s) 
[ltemalB]
```

- **SW3**

```bash
[SW3]vlan batch 10 20 100
[SW3]int Vlanif 1
[SW3-Vlanif1]ip ad 192.168.0.126 27
[SW3-Vlanif1]q
[SW3]int Vlanif 100
[SW3-Vlanif100]ip ad 192.168.0.94 27
[SW3-Vlanif100]q
[SW3]
```

- **FTP_A**:
	* IP地址：192.168.0.2
	* 子网掩码：255.255.255.224
	* 网关：192.168.0.1

- **FTP_B**
	* IP地址：192.168.0.34
	* 子网掩码：255.255.255.224
	* 网关：192.168.0.33

- **PC_A**
	* IP地址：192.168.0.129
	* 子网掩码：255.255.255.224
	* 网关：192.168.0.158

- **PC_B**
	* IP地址：192.168.0.193
	* 子网掩码：255.255.255.224
	* 网关：192.168.0.222

## SW3配置

```bash
[SW3]vlan batch 10 20
[SW3]int Vlanif 10
[SW3-Vlanif10]ip ad 192.168.0.158 27	
[SW3-Vlanif10]int vlan 20
[SW3-Vlanif20]ip ad 192.168.0.222 27
[SW3-Vlanif20]q
[SW3]
```

## 划分VLAN

```bash
# SWA配置
[SWA]vlan 10
[SWA-vlan10]q
[SWA]int g0/0/10
[SWA-GigabitEthernet0/0/10]port link-type access 
[SWA-GigabitEthernet0/0/10]port default vlan 10
[SWA-GigabitEthernet0/0/10]q
[SWA]

# SWB配置
[SWB]vlan 20
[SWB-vlan20]q
[SWB]int g0/0/10
[SWB-GigabitEthernet0/0/10]port link-type access 
[SWB-GigabitEthernet0/0/10]port default vlan 20
[SWB-GigabitEthernet0/0/10]q
[SWB]

# SW3配置
[SW3]int g0/0/5	
[SW3-GigabitEthernet0/0/5]port link-type access 
[SW3-GigabitEthernet0/0/5]port default vlan 100
[SW3-GigabitEthernet0/0/5]q
[SW3]int g0/0/6
[SW3-GigabitEthernet0/0/6]port link-type access 
[SW3-GigabitEthernet0/0/6]port default vlan 100
```

## 配置链路聚合

```bash
# SWA配置
[SWA]int Eth-Trunk 1
[SWA-Eth-Trunk1]trunkport GigabitEthernet 0/0/1 to 0/0/2
[SWA-Eth-Trunk1]port link-type trunk 
[SWA-Eth-Trunk1]port trunk allow-pass vlan all 
[SWA-Eth-Trunk1]q
[SWA]int Eth-Trunk 2
[SWA-Eth-Trunk2]trunkport GigabitEthernet 0/0/5 to 0/0/6
[SWA-Eth-Trunk2]port link-type trunk 
[SWA-Eth-Trunk2]port trunk allow-pass vlan all 
[SWA-Eth-Trunk2]q
[SWA]

# SWB配置
[SWB]int Eth-Trunk 1
[SWB-Eth-Trunk1]trunkport GigabitEthernet 0/0/3 to 0/0/4
[SWB-Eth-Trunk1]port link-type trunk 
[SWB-Eth-Trunk1]port trunk allow-pass vlan all 
[SWB-Eth-Trunk1]q
[SWB]int Eth-Trunk 2
[SWB-Eth-Trunk2]trunkport GigabitEthernet 0/0/5 to 0/0/6
[SWB-Eth-Trunk2]port link-type trunk 
[SWB-Eth-Trunk2]port trunk allow-pass vlan all 
[SWB-Eth-Trunk2]q
[SWB]

# SW3配置
[SW3]int Eth-Trunk 1
[SW3-Eth-Trunk1]trunkport GigabitEthernet 0/0/3 to 0/0/4
[SW3-Eth-Trunk1]port link-type trunk 
[SW3-Eth-Trunk1]port trunk allow-pass vlan all 
[SW3-Eth-Trunk1]q
[SW3]int Eth-Trunk 2
[SW3-Eth-Trunk2]trunkport GigabitEthernet 0/0/1 to 0/0/2
[SW3-Eth-Trunk2]port link-type trunk 
[SW3-Eth-Trunk2]port trunk allow-pass vlan all 
[SW3-Eth-Trunk2]q
[SW3]
```

## 三层接口（已配置）

```bash
[SW3]dis ip int bri
Interface                         IP Address/Mask      Physical   Protocol  
MEth0/0/1                         unassigned           down       down   
NULL0                             unassigned           up         up(s) 
Vlanif1                           192.168.0.126/27     up         up     
Vlanif10                          192.168.0.158/27     up         up     
Vlanif20                          192.168.0.222/27     up         up     
Vlanif100                         192.168.0.94/27      up         up     
```

## 阻塞端口

```bash
[SW3]stp root primary  //将SW3设置为根桥

# 查看SW3的端口角色
[SW3]dis stp brief 
 MSTID  Port                        Role  STP State     Protection
   0    GigabitEthernet0/0/5        DESI  FORWARDING      NONE
   0    GigabitEthernet0/0/6        DESI  FORWARDING      NONE
   0    GigabitEthernet0/0/10       DESI  FORWARDING      NONE
   0    GigabitEthernet0/0/11       DESI  FORWARDING      NONE
   0    Eth-Trunk1                  DESI  FORWARDING      NONE
   0    Eth-Trunk2                  DESI  FORWARDING      NONE
[SW3]

# 查看SWB的端口角色
[SWB]dis stp brief
 MSTID  Port                        Role  STP State     Protection
   0    GigabitEthernet0/0/10       DESI  FORWARDING      NONE
   0    Eth-Trunk1                  ROOT  FORWARDING      NONE
   0    Eth-Trunk2                  ALTE  DISCARDING      NONE
[SWB]
```

## 配置OSPF

```bash
# BoadeA配置
[BoadeA]ospf
[BoadeA-ospf-1]area 0
[BoadeA-ospf-1-area-0.0.0.0]network 192.168.0.96 0.0.0.31  //宣告一个网段
[BoadeA-ospf-1-area-0.0.0.0]q
[BoadeA-ospf-1]default-route-advertise  //交换默认路由

# BoadeB配置
[BoadeB]ospf
[BoadeB-ospf-1]area 0
[BoadeB-ospf-1-area-0.0.0.0]net 192.168.0.96 0.0.0.31
[BoadeB-ospf-1-area-0.0.0.0]q
[BoadeB-ospf-1]default-route-advertise

# SW3配置
[SW3]ospf
[SW3-ospf-1]area 0
# 精确宣告
[SW3-ospf-1-area-0.0.0.0]network 192.168.0.126 0.0.0.0
[SW3-ospf-1-area-0.0.0.0]network 192.168.0.158 0.0.0.0
[SW3-ospf-1-area-0.0.0.0]net 192.168.0.222 0.0.0.0
[SW3-ospf-1-area-0.0.0.0]net 192.168.0.94 0.0.0.0

# ltemalA
[ltemalA]ospf
[ltemalA-ospf-1]area 0
[ltemalA-ospf-1-area-0.0.0.0]net 192.168.0.0 0.0.0.31
[ltemalA-ospf-1-area-0.0.0.0]net 192.168.0.64 0.0.0.31

# ltemalB
[ltemalB]ospf
[ltemalB-ospf-1]area 0
[ltemalB-ospf-1-area-0.0.0.0]net 192.168.0.32 0.0.0.31
[ltemalB-ospf-1-area-0.0.0.0]net 192.168.0.64 0.0.0.31
```

> - OSPF精确宣告：`[BoadeA-ospf-1-area-0.0.0.0]network 192.168.0.97 0.0.0.0`
> - 测试FTP_A与PC的连通性
> - 查看所有设备的路由表

## 配置FTP映射

```bash
# BoadeA配置
[BoadeA]ip route-static 0.0.0.0 0.0.0.0 202.102.24.97
[BoadeA]acl 2000
[BoadeA-acl-basic-2000]rule permit 
[BoadeA-acl-basic-2000]q
[BoadeA]int g0/0/0
[BoadeA-GigabitEthernet0/0/0]nat outbound 2000
[BoadeA-GigabitEthernet0/0/0]nat server protocol tcp global current-interface 21 inside 192.168.0.2 21
Warning:The port 21 is well-known port. If you continue it may cause function fa
ilure.
Are you sure to continue?[Y/N]:y  //Y确认
[BoadeA-GigabitEthernet0/0/0]

# BoadeB配置
[BoadeB]ip route-static 0.0.0.0 0.0.0.0 120.202.249.193
[BoadeB]acl 2000
[BoadeB-acl-basic-2000]rule permit 
[BoadeB-acl-basic-2000]q
[BoadeB]int g0/0/0
[BoadeB-GigabitEthernet0/0/0]nat outbound 2000
[BoadeB-GigabitEthernet0/0/0]nat server protocol tcp global current-interface 21 inside 192.168.0.34 21
Warning:The port 21 is well-known port. If you continue it may cause function fa
ilure.
Are you sure to continue?[Y/N]:y  //Y确认
[BoadeB-GigabitEthernet0/0/0]

# ISP配置静态路由
[ISP]ip route-static 192.168.0.0 27 202.102.24.98
[ISP]ip route-static 192.168.0.32 27 120.202.249.194
```

> - FTP_A与ISP运营商连通性测试
> - FTP_A与http-pc连通性测试
> - http-pc访问FTP_A测试
> 	* 配置内网FTP_A服务器
> 	* http-pc访问内网FTP_A(文件传输模式PORT)

## 禁止访问FTP

```bash
# ltemalA配置ACL
[ltemalA]acl 3000
[ltemalA-acl-adv-3000]rule deny tcp source 192.168.0.129 0.0.0.0 destination-port eq 21 
[ltemalA-acl-adv-3000]q
[ltemalA]int g0/0/0
[ltemalA-GigabitEthernet0/0/0]traffic-filter inbound acl 3000

# ltemalB配置ACL
[ltemalB]acl 3000
[ltemalB-acl-adv-3000]rule deny tcp source 192.168.0.193 0.0.0.0 destination-port eq 21
[ltemalB-acl-adv-3000]q
[ltemalB]int g0/0/0
[ltemalB-GigabitEthernet0/0/0]traffic-filter inbound acl  3000
```

> - PC测试登录FTP服务器
> - PC与FTP连通性测试