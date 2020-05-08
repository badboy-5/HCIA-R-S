---
姓名：坏坏
实验时间：2020年4月24日
整理时间：2020年4月24日
---

参考博客：[【CSDN】](https://blog.csdn.net/qq_45668124/article/details/105738503)

# 子网划分

- **总部---上海**
	* 给定网段：12.1.1.0/24
	* 划分网段：12.1.1.0/30
	* 可用：12.1.1.1/30、12.1.1.2/30
- **总部---广州**
	* 给定网段：13.1.1.0/24
	* 划分网段：13.1.1.0/30
	* 可用：13.1.1.1/30、13.1.1.2/30
- **总部---SW1**
	* 给定网段：192.168.5.0/24
	* 划分网段：192.168.5.30/24
	* 可用：192.168.5.1/30、192.168.5.2/30

# 配置IP地址

- AR1配置

```bash
[AR1]int g0/0/0
[AR1-GigabitEthernet0/0/0]ip ad 192.168.43.2 24
[AR1-GigabitEthernet0/0/0]int g0/0/1
[AR1-GigabitEthernet0/0/1]ip ad 192.168.5.1 30
[AR1-GigabitEthernet0/0/1]int g2/0/0
[AR1-GigabitEthernet2/0/0]ip ad 12.1.1.1 30
[AR1-GigabitEthernet2/0/0]int g2/0/1
[AR1-GigabitEthernet2/0/1]ip ad 13.1.1.1 30 
```

- AR2配置

```bash
[AR2]int g0/0/1
[AR2-GigabitEthernet0/0/1]ip ad 12.1.1.2 30
[AR2-GigabitEthernet0/0/1]int g0/0/0
[AR2-GigabitEthernet0/0/0]ip ad 192.168.3.254 24
```

- AR3配置

```bash
[AR3]int g0/0/1
[AR3-GigabitEthernet0/0/1]ip ad 13.1.1.2 30
[AR3-GigabitEthernet0/0/1]int g0/0/0 
[AR3-GigabitEthernet0/0/0]ip ad 192.168.4.254 24
```

- SW1配置

```bash
[SW1]int vlan
[SW1]int Vlanif 1
[SW1-Vlanif1]ip ad 192.168.5.2 30

[SW1]vlan batch 100 200     
[SW1]int Vlanif 100
[SW1-Vlanif100]ip ad 192.168.1.254 24
[SW1-Vlanif100]q
[SW1]int Vlanif 200
[SW1-Vlanif200]ip ad 192.168.2.254 24

# 配置链路聚合
[SW1]int Eth-Trunk 1
[SW1-Eth-Trunk1]trunkport GigabitEthernet 0/0/3 to 0/0/4
[SW1-Eth-Trunk1]port link-type trunk 
[SW1-Eth-Trunk1]port trunk allow-pass vlan  all 
```

- SW2配置

```bash
[SW2]vlan batch 100 200
[SW2]int e0/0/1
[SW2-Ethernet0/0/1]port link-type access 
[SW2-Ethernet0/0/1]port default vlan 100
[SW2-Ethernet0/0/1]int e0/0/2
[SW2-Ethernet0/0/2]port link-type access 
[SW2-Ethernet0/0/2]port default vlan 200

# 配置链路聚合
[SW2]int Eth-Trunk 1
[SW2-Eth-Trunk1]trunkport Ethernet 0/0/3 to 0/0/4
[SW2-Eth-Trunk1]port link-type trunk 
[SW2-Eth-Trunk1]port trunk allow-pass vlan all 
```

# 配置OSPF动态路由协议

- AR1配置

```bash
[AR1]ospf
[AR1-ospf-1]area 0
[AR1-ospf-1-area-0.0.0.0]net 192.168.5.1 0.0.0.0
[AR1-ospf-1-area-0.0.0.0]net 12.1.1.1 0.0.0.0
[AR1-ospf-1-area-0.0.0.0]net 13.1.1.1 0.0.0.0
```

- AR2配置

```bash
[AR2]ospf
[AR2-ospf-1]area 0
[AR2-ospf-1-area-0.0.0.0]net 12.1.1.2 0.0.0.0 
[AR2-ospf-1-area-0.0.0.0]net 192.168.3.254 0.0.0.0
```

- AR3配置

```bash
[AR3]ospf 
[AR3-ospf-1]area 0
[AR3-ospf-1-area-0.0.0.0]net 192.168.4.254 0.0.0.0
[AR3-ospf-1-area-0.0.0.0]net 13.1.1.2 0.0.0.0
```

- SW1配置

```bash
[SW1]ospf
[SW1-ospf-1]area 0   
[SW1-ospf-1-area-0.0.0.0]net 192.168.5.2 0.0.0.0
[SW1-ospf-1-area-0.0.0.0]net 192.168.1.254 0.0.0.0
[SW1-ospf-1-area-0.0.0.0]net 192.168.2.254 0.0.0.0
```

> PC端做连通性测试

# 配置NAT

```bash
[AR1]acl 2000 
[AR1-acl-basic-2000]rule permit 
[AR1-acl-basic-2000]q
[AR1]int g0/0/0
[AR1-GigabitEthernet0/0/0]nat outbound 2000
[AR1-GigabitEthernet0/0/0]q
[AR1]ip route-static 0.0.0.0 0.0.0.0 192.168.43.1
[AR1]ospf 
[AR1-ospf-1]default-route-advertise  //下发默认路由
```

# 分公司间不能互访

- 配置高级ACL

```bash
[AR1]acl 3000
[AR1-acl-adv-3000]rule deny ip source 192.168.3.0 0.0.0.255 destination 192.168.4.0 0.0.0.255 
[AR1-acl-adv-3000]q
[AR1]int g2/0/0
[AR1-GigabitEthernet2/0/0]traffic-filter inbound acl 3000
```

	实验效果：
		1. 内网可以互通，PC端都可以访问Internet；
		2. PC3与PC4间不能互通