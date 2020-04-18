# PPPoE配置

## PPPoE Server配置步骤

```bash
# 创建并配置虚拟模板
[PPPoE Server]int Virtual-Template 1  //创建虚拟模板
[PPPoE Server-Virtual-Template1]ip ad 100.100.100.254 24  //虚拟模板配置IP地址
[PPPoE Server-Virtual-Template1]ppp ipcp dns 8.8.8.8  //配置DNS
[PPPoE Server-Virtual-Template1]q

# 创建并配置地址池
[PPPoE Server]ip pool pppoe  //创建地址池
[PPPoE Server-ip-pool-pppoe]network 100.100.100.0 mask 24  //分配网段
[PPPoE Server-ip-pool-pppoe]gateway-list 100.100.100.254  //设置网关
[PPPoE Server-ip-pool-pppoe]q

# 虚拟模板调用地址池并配置认证
[PPPoE Server]int Virtual-Template 1  //进入虚拟模板接口
[PPPoE Server-Virtual-Template1]remote address pool pppoe  //调用地址池
[PPPoE Server-Virtual-Template1]ppp authentication-mode pap  //配置认证模式
[PPPoE Server-Virtual-Template1]q

# 物理接口绑定虚拟模板接口
[PPPoE Server]int g0/0/0
[PPPoE Server-GigabitEthernet0/0/0]pppoe-server bind virtual-template 1  //物理接口绑定虚拟模板

# 配置AAA认证
[PPPoE Server]aaa
[PPPoE Server-aaa]local-user bad password cipher huawei123
[PPPoE Server-aaa]local-user bad service-type ppp
```

##  PPPoE Client的配置步骤

```bash
# 创建Dialer接口并通过配置IP地址
[PPPoE Client]int Dialer 1  //创建Dialer接口
[PPPoE Client-Dialer1]dialer user bad  //指定Dialer用户（可配可不配）
[PPPoE Client-Dialer1]dialer bundle 1  //接口绑定
[PPPoE Client-Dialer1]ip ad ppp-negotiate  //通过邻居分配获得IP地址
[PPPoE Client-Dialer1]ppp ipcp dns request  //配置接受DNS服务器

# 配置PAP认证
[PPPoE Client-Dialer1]ppp pap local-user bad password cipher huawei123

# 绑定拨号接口
[PPPoE Client]int g0/0/1
[PPPoE Client-GigabitEthernet0/0/1]pppoe-client dial-bundle-number 1

# 查看被分配的IP地址
[PPPoE Client]dis ip int bri
*down: administratively down
^down: standby
(l): loopback
(s): spoofing
The number of interface that is UP in Physical is 4
The number of interface that is DOWN in Physical is 1
The number of interface that is UP in Protocol is 2
The number of interface that is DOWN in Protocol is 3

Interface                         IP Address/Mask      Physical   Protocol  
Dialer1                           100.100.100.253/32   up         up(s)     
GigabitEthernet0/0/0              unassigned           up         down      
GigabitEthernet0/0/1              unassigned           up         down      
GigabitEthernet0/0/2              unassigned           down       down      
NULL0                             unassigned           up         up(s)     
# 连通性测试
[PPPoE Client]ping 100.100.100.254

# 客户端物理接口配置IP地址并配置静态路由
[PPPoE Client]ip route-static 0.0.0.0 0 Dialer 1
[PPPoE Client]int g0/0/0
[PPPoE Client-GigabitEthernet0/0/0]ip ad 192.168.43.254 24

# PPPoE服务器配置静态路由（实际情况中无需配置静态路由）
[PPPoE Server]ip route-static 0.0.0.0 0 100.100.100.253

# PC上连通性测试
```

# DHCP服务器

## 接口地址池

```bash
[DHCP Server]dhcp enable  //开启DHCP服务

[DHCP Server]int g0/0/0
[DHCP Server-GigabitEthernet0/0/0]ip ad 192.168.43.254 24  //配置地址
[DHCP Server-GigabitEthernet0/0/0]dhcp select interface  //接口调用
[DHCP Server-GigabitEthernet0/0/0]dhcp server dns-list 8.8.8.8  //配置DNS
[DHCP Server-GigabitEthernet0/0/0]dhcp server excluded-ip-address 192.168.43.244 192.168.43.253  //不参与分配的IP地址
[DHCP Server-GigabitEthernet0/0/0]dhcp server lease day 3  //IP地址租约
```

- PC使用DHCP获取IP地址，查看IP地址

```bash
PC>ipconfig

Link local IPv6 address...........: fe80::5689:98ff:fe20:b69
IPv6 address......................: :: / 128
IPv6 gateway......................: ::
IPv4 address......................: 192.168.43.242  //获取的IP地址
Subnet mask.......................: 255.255.255.0  //掩码
Gateway...........................: 192.168.43.254  //网关
Physical address..................: 54-89-98-20-0B-69
DNS server........................: 8.8.8.8  //DNS服务器
```

## 全局地址池

```bash
[DHCP Server]dhcp enable  //开启DHCP服务
[DHCP Server]ip pool bad  //创建全局地址池
Info: It's successful to create an IP address pool.
[DHCP Server-ip-pool-bad]net 192.168.43.0 mask 24  //添加一个网段
[DHCP Server-ip-pool-bad]gateway-list 192.168.43.254  //配置网关
[DHCP Server-ip-pool-bad]dns-list 114.114.114.114  //配置DNS
[DHCP Server-ip-pool-bad]excluded-ip-address 192.168.43.250 192.168.43.253  //不参与分配的IP地址
[DHCP Server-ip-pool-bad]lease day 5  //IP地址租约时间

[DHCP Server-ip-pool-bad]dis ip pool  //查看地址池的相关信息
  -----------------------------------------------------------------------
  Pool-name      : bad
  Pool-No        : 0
  Position       : Local           Status           : Unlocked
  Gateway-0      : 192.168.43.254  
  Mask           : 255.255.255.0
  VPN instance   : --

  IP address Statistic
    Total       :253   
    Used        :0          Idle        :249   
    Expired     :0          Conflict    :0          Disable   :4     

# 将接口使用本地地址池
[DHCP Server]int g0/0/0
[DHCP Server-GigabitEthernet0/0/0]dhcp select global  //调用本地的地址池
[DHCP Server-GigabitEthernet0/0/0]ip ad 192.168.43.254 24  //接口添加IP地址（与地址池的地址同一网段）
```

- PC查看获取的IP地址

```bash
PC>ipconfig

Link local IPv6 address...........: fe80::5689:98ff:fe20:b69
IPv6 address......................: :: / 128
IPv6 gateway......................: ::
IPv4 address......................: 192.168.43.249
Subnet mask.......................: 255.255.255.0
Gateway...........................: 192.168.43.254
Physical address..................: 54-89-98-20-0B-69
DNS server........................: 114.114.114.114
```

- **拓展**：两台PC分配不同网段的IP（此处的配置是继续上面的实验）

**方法一**：配置单臂路由，配置子接口

```bash
# 交换机上的配置
[SW1]vlan 10
[SW1-vlan10]vlan 20
[SW1-vlan20]int g0/0/2 
[SW1-GigabitEthernet0/0/2]port link-type access 
[SW1-GigabitEthernet0/0/2]port default vlan 10
[SW1-GigabitEthernet0/0/2]int g0/0/3
[SW1-GigabitEthernet0/0/3]port link-type access 
[SW1-GigabitEthernet0/0/3]port default vlan 20
[SW1-GigabitEthernet0/0/3]int g0/0/1
[SW1-GigabitEthernet0/0/1]port link-type trunk 
[SW1-GigabitEthernet0/0/1]port trunk allow-pass vlan 10 20
[SW1-GigabitEthernet0/0/1]q
[SW1]

# 路由器上配置
[DHCP Server]int g0/0/0
[DHCP Server-GigabitEthernet0/0/0]undo dhcp select global  //删除DHCP的配置
[DHCP Server-GigabitEthernet0/0/0]undo ip add  //删除IP地址

# 配置子接口
[DHCP Server]int g0/0/0.1
[DHCP Server-GigabitEthernet0/0/0.1]dot1q termination vid 10  //封装VLAN ID
[DHCP Server-GigabitEthernet0/0/0.1]arp broadcast enable  //开启ARP转发
[DHCP Server-GigabitEthernet0/0/0.1]ip add 192.168.43.254 24  //配置IP地址
[DHCP Server-GigabitEthernet0/0/0.1]int g0/0/0.2
[DHCP Server-GigabitEthernet0/0/0.2]dot1q termination vid 20
[DHCP Server-GigabitEthernet0/0/0.2]arp broadcast enable 
[DHCP Server-GigabitEthernet0/0/0.2]ip add 192.168.53.254 24
[DHCP Server-GigabitEthernet0/0/0.2]q
[DHCP Server]

# 查看地址池
[DHCP Server]dis ip pool 
  -----------------------------------------------------------------------
  Pool-name      : bad
  Pool-No        : 0
  Position       : Local           Status           : Unlocked
  Gateway-0      : 192.168.43.254  
  Mask           : 255.255.255.0
  VPN instance   : --


  IP address Statistic
    Total       :253   
    Used        :2          Idle        :247   
    Expired     :0          Conflict    :0          Disable   :4     

# 创建地址池
[DHCP Server]ip pool boy
[DHCP Server-ip-pool-boy]net 192.168.53.0 mask 24  //分配的网段
[DHCP Server-ip-pool-boy]gateway-list 192.168.53.254  //网关
[DHCP Server-ip-pool-boy]lease day 3  //IP地址租约
[DHCP Server-ip-pool-boy]dns-list 8.8.8.8  //DNS服务器
[DHCP Server-ip-pool-boy]excluded-ip-address 192.168.53.200 192.168.53.253  //不参与分配的IP地址
[DHCP Server-ip-pool-boy]q

# 查看地址池
[DHCP Server]dis ip pool 
  -----------------------------------------------------------------------
  Pool-name      : bad
  Pool-No        : 0
  Position       : Local           Status           : Unlocked
  Gateway-0      : 192.168.43.254  
  Mask           : 255.255.255.0
  VPN instance   : --

  ----------------------------------------------------------------------
  Pool-name      : boy
  Pool-No        : 1
  Position       : Local           Status           : Unlocked
  Gateway-0      : 192.168.53.254  
  Mask           : 255.255.255.0
  VPN instance   : --

  IP address Statistic
    Total       :506   
    Used        :2          Idle        :446   
    Expired     :0          Conflict    :0          Disable   :58    
[DHCP Server]

# 接口调用地址池
[DHCP Server]int g0/0/0.1
[DHCP Server-GigabitEthernet0/0/0.1]dhcp select global  //调用全局地址池
[DHCP Server-GigabitEthernet0/0/0.1]int g0/0/0.2
[DHCP Server-GigabitEthernet0/0/0.2]dhcp select global  //调用地址池
[DHCP Server-GigabitEthernet0/0/0.2]q
[DHCP Server]
```

> 调用地址池中的地址时，只能调用地址池中与当前接口下配置的IP地址网段相同的地址

- PC查看获取的IP地址

```bash
# PC2
PC>ipconfig

Link local IPv6 address...........: fe80::5689:98ff:fe20:b69
IPv6 address......................: :: / 128
IPv6 gateway......................: ::
IPv4 address......................: 192.168.53.199
Subnet mask.......................: 255.255.255.0
Gateway...........................: 192.168.53.254
Physical address..................: 54-89-98-20-0B-69
DNS server........................: 8.8.8.8

# PC1
PC>ipconfig

Link local IPv6 address...........: fe80::5689:98ff:fe61:33c0
IPv6 address......................: :: / 128
IPv6 gateway......................: ::
IPv4 address......................: 192.168.43.248
Subnet mask.......................: 255.255.255.0
Gateway...........................: 192.168.43.254
Physical address..................: 54-89-98-61-33-C0
DNS server........................: 114.114.114.114
```

**方法二**：DHCP中继

> NP阶段学习，这里不做详细说明

# ACL

## 控制PC间的互访

- 配置接口地址

```bash
# R1
[AR1]int g0/0/1
[AR1-GigabitEthernet0/0/1]ip ad 192.168.1.254 24
[AR1-GigabitEthernet0/0/1]int g0/0/0
[AR1-GigabitEthernet0/0/0]ip ad 12.1.1.1 24
[AR1-GigabitEthernet0/0/0]q   
[AR1]

# R2
[AR2]int g0/0/0
[AR2-GigabitEthernet0/0/0]ip ad 12.1.1.2 24
[AR2-GigabitEthernet0/0/0]int g0/0/1
[AR2-GigabitEthernet0/0/1]ip ad 23.1.1.2 24
[AR2-GigabitEthernet0/0/1]q
[AR2]

# R3
[AR3]int g0/0/0
[AR3-GigabitEthernet0/0/0]ip ad 23.1.1.3 24
[AR3-GigabitEthernet0/0/0]int g0/0/1
[AR3-GigabitEthernet0/0/1]ip ad 192.168.2.254 24
[AR3-GigabitEthernet0/0/1]q
[AR3]
```

- 配置RIP

```bash
# R1
[AR1]rip 1
[AR1-rip-1]net 192.168.1.0
[AR1-rip-1]net 12.0.0.0  

# R2
[AR2]rip 1
[AR2-rip-1]net 12.0.0.0
[AR2-rip-1]net 23.0.0.0

# R3
[AR3]rip 1
[AR3-rip-1]net 192.168.2.0
[AR3-rip-1]net 23.0.0.0
```

- PC做连通性测试

```bash
PC>ping 192.168.2.1

Ping 192.168.2.1: 32 data bytes, Press Ctrl_C to break
Request timeout!
Request timeout!
Request timeout!
From 192.168.2.1: bytes=32 seq=4 ttl=125 time=16 ms
From 192.168.2.1: bytes=32 seq=5 ttl=125 time=31 ms

--- 192.168.2.1 ping statistics ---
  5 packet(s) transmitted
  2 packet(s) received
  60.00% packet loss
  round-trip min/avg/max = 0/23/31 ms
```

- AR2上配置ACL

```bash
[AR2]acl 2000
[AR2-acl-basic-2000]rule deny source 192.168.1.0 0.0.0.255  //配置ACL
[AR2-acl-basic-2000]rule permit  //放行其他的IP
[AR2-acl-basic-2000]dis th  //查看配置
[V200R003C00]
#
acl number 2000  
 rule 5 deny source 192.168.1.0 0.0.0.255 
 rule 10 permit 
#
return
[AR2-acl-basic-2000]q
[AR2]int g0/0/0
[AR2-GigabitEthernet0/0/0]traffic-filter inbound acl 2000  //接口入方向调用ACL
[AR2-GigabitEthernet0/0/0]q
[AR2]
```

- PC上做连通性测试

```bash
PC>ping 192.168.2.1

Ping 192.168.2.1: 32 data bytes, Press Ctrl_C to break
Request timeout!
Request timeout!
Request timeout!
Request timeout!
Request timeout!

--- 192.168.2.1 ping statistics ---
  5 packet(s) transmitted
  0 packet(s) received
  100.00% packet loss
```

## 控制访问FTP服务器

- 路由器配置互通

```bash
[AR1]int g0/0/1
[AR1-GigabitEthernet0/0/1]ip add 192.168.1.254 24
[AR1-GigabitEthernet0/0/1]int g0/0/0
[AR1-GigabitEthernet0/0/0]ip ad 12.1.1.1 24
[AR1-GigabitEthernet0/0/0]q
[AR1]rip 1
[AR1-rip-1]net 192.168.1.0
[AR1-rip-1]net 12.0.0.0
[AR1-rip-1]q
[AR1]


[AR2]int g0/0/0
[AR2-GigabitEthernet0/0/0]ip ad 12.1.1.2 24
[AR2-GigabitEthernet0/0/0]int g0/0/1
[AR2-GigabitEthernet0/0/1]ip ad 23.1.1.2 24
[AR2-GigabitEthernet0/0/1]q
[AR2]rip 1
[AR2-rip-1]net 12.0.0.0
[AR2-rip-1]net 23.0.0.0 
[AR2-rip-1]q
[AR2]

[AR3]int g0/0/0
[AR3-GigabitEthernet0/0/0]ip ad 23.1.1.3 24
[AR3-GigabitEthernet0/0/0]int g0/0/1
[AR3-GigabitEthernet0/0/1]ip ad 192.168.2.254 24
[AR3-GigabitEthernet0/0/1]q
[AR3]rip 1
[AR3-rip-1]net 192.168.2.0
[AR3-rip-1]net 23.0.0.0
[AR3-rip-1]q
[AR3]
```

- Client与Server配置

```bash
# Client
IP:192.168.1.1
网关：192.168.1.254

# Server基础配置：
IP：192.168.2.100
网关：192.168.2.254

# Server服务器信息
FTPServer——>配置——>添加FTP文件目录——>启动
```

- 在Client客户端登录FTP测试

- 可以登录之后，在R3上设置ACL访问控制，禁止Client访问Server的FTP

```bash
[AR3]acl 3000  //配置ACL
# 禁止192.168.1.0访问192.168.2.100的FTP服务器
[AR3-acl-adv-3000]rule deny tcp source 192.168.1.0 0.0.0.255 destination 192.168.2.100 0 destination-port eq 21
[AR3-acl-adv-3000]q
[AR3]int g0/0/0
[AR3-GigabitEthernet0/0/0]traffic-filter inbound acl 3000  //接口入方向调用ACL
[AR3-GigabitEthernet0/0/0]q
[AR3]
```

- 再次在Client客户端登录FTP测试

# NAT

- 配置相关的接口IP地址

> - PC1:
> 	* IP：172.16.1.1
> 	* 网关：172.16.1.254
> - Server：
> 	* IP：172.16.1.3
> 	* 网关：172.16.1.254
> - PC2:
> 	* IP：172.17.1.2
> 	* 网关：172.17.1.254
> - PC3：
> 	* IP：172.17.1.3
> 	* 网关：172.17.1.254

```bash
# R1配置
[R1]int g0/0/1
[R1-GigabitEthernet0/0/1]ip ad 172.16.1.254 24
[R1-GigabitEthernet0/0/1]int g0/0/2
[R1-GigabitEthernet0/0/2]ip ad 172.17.1.254 24
[R1-GigabitEthernet0/0/2]int g0/0/0
[R1-GigabitEthernet0/0/0]ip ad 202.169.10.253 24
[R1-GigabitEthernet0/0/0]q
[R1]ip route-static 0.0.0.0 0.0.0.0 202.169.10.254  //配置静态路由

# R2配置
[R2]int g0/0/0
[R2-GigabitEthernet0/0/0]ip ad 202.169.10.254 24
[R2-GigabitEthernet0/0/0]int lo 0
[R2-LoopBack0]ip ad 202.169.20.1 24
[R2-LoopBack0]q
[R2]
```

> - PC端测试与R2的接口IP的连通性，可以连通
> - PC端测试与R2的环回口的连通性，不可以连通（没有做NAT）

## 静态NAT

```bash
[R1]int g0/0/0
[R1-GigabitEthernet0/0/0]nat static global 202.169.10.3 inside 172.16.1.1  //建立公网地址与私网地址的映射关系
[R1-GigabitEthernet0/0/0]q
```

> - PC1与R2的环回口做连通性测试，可以连通
> - PC2、PC3与R2的环回口做连通性测试，不可以连通

## Easy IP

```bash
# 删除静态NAT
[R1]int g0/0/0
[R1-GigabitEthernet0/0/0]undo nat static global 202.169.10.3 inside 172.16.1.1

# 调用ACL
[R1]acl 2000  //配置ACL
[R1-acl-basic-2000]rule permit  //配置允许所有通过
[R1-acl-basic-2000]q
[R1]int g0/0/0
[R1-GigabitEthernet0/0/0]nat outbound 2000  //接口调用ACL
[R1-GigabitEthernet0/0/0]q
[R1]dis nat outbound  //查看
 NAT Outbound Information:
 -----------------------------------------------------------------------
 Interface                     Acl     Address-group/IP/Interface   Type
 -----------------------------------------------------------------------
 GigabitEthernet0/0/0         2000        202.169.10.1            easyip 
 -----------------------------------------------------------------------
  Total : 1
[R1]
```

> PC端与R2接口IP环回口地址做连通性测试，可以连通

## 动态NAT

```bash
# 删除Easy IP配置
[R1]int g0/0/0
[R1-GigabitEthernet0/0/0]undo nat outbound 2000
[R1-GigabitEthernet0/0/0]q
[R1]undo acl 2000

# 创建公网地址池
# 创建名为1范围为202.169.10.2-202.169.10.50的地址池
[R1]nat address-group 1 202.169.10.2 202.169.10.50
# 创建名为2范围为202.169.10.100-202.169.10.200的地址池
[R1]nat address-group 2 202.169.10.100 202.169.10.200

# 配置ACL
[R1]acl 2000
[R1-acl-basic-2000]rule permit source 172.16.1.0 0.0.0.255
[R1-acl-basic-2000]q
[R1]acl 2001
[R1-acl-basic-2001]rule permit source 172.17.1.0 0.0.0.255
[R1-acl-basic-2001]q

# 公网地址池调用ACL
[R1]int g0/0/0
[R1-GigabitEthernet0/0/0]nat outbound 2000 address-group 1 no-pat 
[R1-GigabitEthernet0/0/0]nat outbound 2001 address-group 2 no-pat 
[R1-GigabitEthernet0/0/0]

# 查看地址池
[R1]dis nat outbound 
 NAT Outbound Information:
 -----------------------------------------------------------------------
 Interface                     Acl     Address-group/IP/Interface   Type
 -----------------------------------------------------------------------
 GigabitEthernet0/0/0         2000                1               no-pat
 GigabitEthernet0/0/0         2001                2               no-pat
 -----------------------------------------------------------------------
  Total : 2
```

> PC端与R2做连通性测试，可以连通


## NAT Server

```bash
# 删除动态NAT配置
[R1]int g0/0/0
[R1-GigabitEthernet0/0/0]undo nat outbound 2000 address-group 1 no-pat
[R1-GigabitEthernet0/0/0]undo nat outbound 2001 address-group 2 no-pat
[R1-GigabitEthernet0/0/0]q
[R1]undo acl 2000
[R1]undo acl 2001
[R1]

# 重新配置ACL，并调用
[R1]acl 2000
[R1-acl-basic-2000]rule permit 
[R1-acl-basic-2000]q
[R1]int g0/0/0
[R1-GigabitEthernet0/0/0]nat outbound 2000
[R1-GigabitEthernet0/0/0]q
[R1]
```

- 配置NAT Server

- 在Server上配置FTP服务器
	* 服务信息——FTPserver
	* 启动服务

```bash
# 配置ftp端口映射
[R1]int g0/0/0
[R1-GigabitEthernet0/0/0]nat server protocol tcp global current-interface ftp inside 172.16.1.3 ftp 
Warning:The port 21 is well-known port. If you continue it may cause function failure.
Are you sure to continue?[Y/N]:y  //Y确认
[R1-GigabitEthernet0/0/0]

# 删除环回口地址，添加Client设备，配置接口
[R2]int lo 0
[R2-LoopBack0]undo ip ad
[R2-LoopBack0]q
[R2]int g0/0/1
[R2-GigabitEthernet0/0/1]ip ad 202.169.20.254 24
```

> - Client：
> 	* IP：202.169.20.2
> 	* 网关：202.169.20.254

- 在Server上，对Client进行Ping测试，可以连通

- 在Client上通过端口映射的公网IP，访问Server的FTP
	* 客户端信息——服务器地址`202.169.10.253`
	* 文件传输模式`PORT`——登录