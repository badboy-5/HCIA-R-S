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





















