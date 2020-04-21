---
姓名：坏坏
实验时间：2020年4月21日
整理时间：2020年4月21日

---
参考博客：
[【CSDN】]( https://blog.csdn.net/qq_45668124/article/details/105671086 )

如下拓扑：

![](E:%5CBad%5CPictures%5CTypora%20Picture%5CHCIA_R&S-%E6%B5%8B%E8%AF%95.png)

	说明：
		按照拓扑需求搭建实验环境，根据拓扑需求配置相应IP地址，子网掩码为24位
		实验中所使用的ACL编号，地址池名称均使用题目的要求的。不可以自定义

```bash
# AR1配置
[AR1]int s4/0/0
[AR1-Serial4/0/0]ip ad 100.1.1.2
[AR1-Serial4/0/0]q
[AR1]int lo 1
[AR1-LoopBack1]ip ad 1.1.1.1 32

# AR2配置
[AR2]int g0/0/0
[AR2-GigabitEthernet0/0/0]ip ad 23.1.1.2 24
[AR2-GigabitEthernet0/0/0]int s4/0/0
[AR2-Serial4/0/0]ip ad 100.1.1.1 24
[AR2-Serial4/0/0]q

# AR3配置
[AR3]int g0/0/1
[AR3-GigabitEthernet0/0/1]ip ad 31.1.1.3 24
[AR3-GigabitEthernet0/0/1]int g0/0/0
[AR3-GigabitEthernet0/0/0]ip ad 23.1.1.3 24
[AR3-GigabitEthernet0/0/0]q
```


# 局域网需求

1. 在SW1和SW2上创建VLAN 10 和 VLAN 20
2. 把SW1和SW2的G0/0/2接口划分到VLAN 10，G0/0/3划分到VLAN 20

```bash
# SW1配置
[SW1]vlan batch 10 20
[SW1]int g0/0/2
[SW1-GigabitEthernet0/0/2]port link-type access 
[SW1-GigabitEthernet0/0/2]port default vlan 10
[SW1-GigabitEthernet0/0/2]int g0/0/3
[SW1-GigabitEthernet0/0/3]port link-type access
[SW1-GigabitEthernet0/0/3]port default vlan 20

# SW2配置
[SW2]vlan batch 10 20
[SW2]int g0/0/2
[SW2-GigabitEthernet0/0/2]port link-type access 
[SW2-GigabitEthernet0/0/2]port default vlan 10
[SW2-GigabitEthernet0/0/2]int g0/0/3
[SW2-GigabitEthernet0/0/3]port link-type access 
[SW2-GigabitEthernet0/0/3]port default vlan 20
```

# 可靠性

1. 在SW1和SW2上创建Eth-Trunk 1，并且让G0/0/10和 G0/0/11加入到聚合组
2. 把聚合组设置为Trunk链路，允许VLAN 10，VLAN 20通过

```bash
# SW1配置
[SW1]int Eth-Trunk 1
[SW1-Eth-Trunk1]trunkport GigabitEthernet 0/0/10 to 0/0/11
[SW1-Eth-Trunk1]port link-type trunk 
[SW1-Eth-Trunk1]port trunk allow-pass vlan 10 20

# SW2配置
[SW2]int Eth-Trunk 1
[SW2-Eth-Trunk1]trunkport GigabitEthernet 0/0/10 to 0/0/11
[SW2-Eth-Trunk1]port link-type trunk 
[SW2-Eth-Trunk1]port trunk allow-pass vlan 10 20
```


# Vlan间路由

1. 在SW1上创建VLAN三层接口，VLAN 10的IP为192.168.1.254/24，VLAN 20的IP为192.168.2.254/24
2. 创建VLAN 30，把G0/0/1划分到VLAN 30，并且配置三层接口，IP地址为31.1.1.1/24

```bash
[SW1]vlan 30
[SW1-Vlanif30]ip ad 31.1.1.1 24
[SW1-Vlanif30]q
[SW1]int Vlanif 10
[SW1-Vlanif10]ip ad 192.168.1.254 24
[SW1-Vlanif10]q
[SW1]int Vlanif 20
[SW1-Vlanif20]ip ad 192.168.2.254 24
[SW1-Vlanif20]q

[SW1]int g0/0/1
[SW1-GigabitEthernet0/0/1]port link-type access 
[SW1-GigabitEthernet0/0/1]port default vlan 30
[SW1-GigabitEthernet0/0/1]q
```

# IGP

1. 在R2、R3、SW1上启用OSPF路由协议。使用区域0使得内网实现全互联

```bash

# AR3配置
[AR3]ospf 
[AR3-ospf-1]area 0
[AR3-ospf-1-area-0.0.0.0]net 23.1.1.3 0.0.0.0
[AR3-ospf-1-area-0.0.0.0]net 31.1.1.3 0.0.0.0

# AR2配置
[AR2]ospf 
[AR2-ospf-1]area 0
[AR2-ospf-1-area-0.0.0.0]net 23.1.1.2 0.0.0.0
[AR2]ip route-static 1.1.1.1 255.255.255.255 100.1.1.2  //配置静态

# AR1配置
[AR1]ip route-static 0.0.0.0 0.0.0.0 100.1.1.1

# SW1配置
[SW1]ospf 
[SW1-ospf-1]import-route static  //引入静态
[SW1-ospf-1]area 0
[SW1-ospf-1-area-0.0.0.0]net 192.168.1.0 0.0.0.255
[SW1-ospf-1-area-0.0.0.0]net 192.168.2.0 0.0.0.255
[SW1-ospf-1-area-0.0.0.0]net 31.1.1.0 0.0.0.255
```

> 这里如果不引入静态的话，是无法互通的。当然也可以在R3、SW1上配置静态。（这是来自老师发自灵魂深处的盘问，整个过程大气不敢出一口！！！）

# DHCP 

1. 在R3上开启DHCP服务，基于全局模式分配地址，SW1为DHCP中继
2. 在R3上：
	- 创建地址池1，分配192.168.1.0网段的地址，并且下发网关
	- 创建地址池2，分配192.168.2.0网段的地址，并且下发网关

```bash
# 配置1地址池
[AR3]dhcp enable 
[AR3]ip pool 1  //创建地址池
[AR3-ip-pool-1]network 192.168.1.0 mask 24  //分配网段
[AR3-ip-pool-1]gateway-list 192.168.1.254  //配置网关

# 配置2地址池
[AR3]ip pool 2
[AR3-ip-pool-2]net 192.168.2.0 mask 24
[AR3-ip-pool-2]gateway-list 192.168.2.254

[AR3-ip-pool-2]dis ip pool   //查看地址池信息

[AR3]int g0/0/1
[AR3-GigabitEthernet0/0/1]dhcp select global  //调用本地的地址池

# 配置DHCP中继
[SW1]dhcp enable 	
[SW1]int Vlanif 10
[SW1-Vlanif10]dhcp select relay 
[SW1-Vlanif10]dhcp relay server-ip 31.1.1.3  //DHCP服务器的出接口地址
[SW1-Vlanif10]q
[SW1]int Vlanif 20
[SW1-Vlanif20]dhcp select relay 
[SW1-Vlanif20]dhcp relay server-ip 31.1.1.3
[SW1-Vlanif20]q
[SW1]int Vlanif 30
[SW1-Vlanif30]dhcp select relay 
[SW1-Vlanif30]dhcp relay server-ip 31.1.1.3
```

> 这里的服务器的出接口地址注意不能填错，在做DHCP中继时，如果配错，多余的配置要删除

# 广域网

1. R2与R1之间为广域网链路，为了加强安全性，在R2上开启CHAP验证
	- 用户名为bad1 ，密码为huawei123

```bash
# AR2配置
[AR2]int s4/0/0
[AR2-Serial4/0/0]ppp authentication-mode chap  //PPP认证模式修改为CHAP
[AR2-Serial4/0/0]q

# 配置AAA
[AR2]aaa
[AR2-aaa]local-user bad1 password cipher huawei123
[AR2-aaa]local-user bad1 service-type ppp

# AR1配置
[AR1]int s4/0/0
[AR1-Serial4/0/0]ppp chap user bad1
[AR1-Serial4/0/0]ppp chap password cipher huawei123
[AR1-Serial4/0/0]q
```

> 连通性测试

# Internet

1. 在R2上使用easy ip，做地址转换。使得192.168.1.0 与 192.168.2.0可以访问Internet
	- ACL使用2001

```bash
# 配置ACL
[AR2]acl 2001
[AR2-acl-basic-2001]rule permit source 192.168.1.0 0.0.0.255	
[AR2-acl-basic-2001]rule permit source 192.168.2.0 0.0.0.255

# nat调用ACL
[AR2]int s4/0/0
[AR2-Serial4/0/0]nat outbound 2001
```

# 设备管理

1. 在R2上开启Telnet服务，使用用户名密码的验证方式
2. 在AAA试图下创建用户名为bad2，密码为：huawei123的用户

```bash
[AR2]telnet server enable  //开启Telnet服务

# 配置aaa
[AR2]aaa 
[AR2-aaa]local-user bad2 password cipher huawei123
[AR2-aaa]local-user bad2 service-type telnet  //配置用户类型
[AR2-aaa]local-user bad2 privilege level 5  //配置用户等级
[AR2-aaa]q
[AR2]user-interface vty 0 4
[AR2-ui-vty0-4]protocol inbound all  //开启所有的协议
[AR2-ui-vty0-4]authentication-mode aaa  //认证模式为AAA
[AR2-ui-vty0-4]return 
```

> 本地Telnet登录验证

	实验结果：
		PC通过DHCP获取的地址可以通过在命令行使用ipconfig查看
		PC端均能ping通R1的1.1.1.1 


# 附加题

1. 在全网互通的情况下，实现192.168.1.0不能ping 通1.1.1.1，但是可以Telnet  1.1.1.1

```bash
[AR3]acl 3000
[AR3-acl-adv-3000]rule deny icmp source 192.168.1.0 0.0.0.255 destination 1.1.1.1 0.0.0.0 
[AR3-acl-adv-3000]q
[AR3]int g0/0/1
[AR3-GigabitEthernet0/0/1]traffic-filter inbound acl 3000
```

> Ping测试，Telnet无法通过登录验证