---
姓名：坏坏
实验时间：2020年7月11日
整理时间：2020年7月12日
---

[toc]

如下拓扑图：

![HCNA综合实验](F:\GitHub\HCIA R&S\HCIA综合实验（曹Sir）.assets\HCNA综合实验.png)

# 实验需求

Ⅰ. 命令行基础
1. 配置R1仅接受SSH网管
	且仅允许10.1.10.0/24管理
	设置SSH登录限制时间为15min
	登录前提示"Warning; Operation will be Accounting"
	登录成功提示“Login Success”
	登录账户为(USER/HUAWEI)
2. 备份R1的配置到FTP服务器

Ⅱ. 交换部分
1. SW1，SW2互联部署链路聚合
2. 调整STP结构保障阻塞端口位于SW3
3. SW4部署Hybrid实现R3与R2、R4通信，R2与R4之间无法直接通信

Ⅲ. 路由部分
1. R1作为边界设备配置静态路由实现可以访问公网使用最精简配置方式
2. R2，R3，R4部署RIPv2
3. 其中保障R2，R4之间通信总是使用以太链路，除非以太链路故障， 才能使用串行低速链路

Ⅳ. IP服务部分
1. 配置R1作为DHCP服务器，保障PC1地址总是获取为10.1.10.10/24
2. 允许10.1.10.0/24的用户访问互联网访问SERVER1方式为(http://www. baidu. com)
3. 公网用户client1可以访问内网SERVER2的web服务访问方式为( http://www.huawei.com:10080)

Ⅴ. 安全部分
1. 配置R1禁外网ping和traceroute
2. R1可以ping或tracert外网

Ⅵ. 广域网部分
1. R5使用PPPOE拨号方式接入互联网
	宽带账户为(USER/HUAWEI)
	不要在R5上配置静态路由实现访问公网
2. 配置GRE隧道实现PC1与PC2之间通信

## Ⅰ. 命令行基础

- R1配置

```bash
sys
sys AR1
int g0/0/0
  ip ad 10.1.10.254 24
int g0/0/1
  ip ad 10.1.20.254 24
int g0/0/2
  ip ad 20.1.12.1 24
```
```bash
[AR1]rsa local-key-pair create  //生成密钥
```

## 配置aaa

```bash
aaa
  loc USER password cip HUAWEI
  loc USER ser ssh
  loc USER pri lev 15
  q
```

- 配置ssh使用AAA，并限制登陆时间

```bash
ssh user USER auth password  //ssh用户USER认证方式为密码认证
user-int vty 0 4
  auth aaa  //vty0-4使用aaa认证
  pro	in ssh  //协议使用ssh
  idle-time 15 0  //限制登录时间
	q
```

## 设置登陆提示

```bash
head login info "Warning; Operation will be Accounting"  //登陆前的提示
head shell info "Login Success"  //登陆后的提示
```

# Ⅱ. 交换部分

## 基本配置

- SW1基本配置

```bash
vlan bat 10 20
int g0/0/1
  port link ac
  port de vlan 10
int g0/0/11
  port link tr
  port tr al vlan all
```

- SW2基本配置

```bash
vlan bat 10 20
int g0/0/1
  port link ac
  port de vlan 20
int g0/0/12
  port link tr
  port tr al vlan all
```

- SW3基本配置

```bash
vlan bat 10 20
int g0/0/1
  port link ac
  port de vlan 10
int g0/0/2
  port link ac
  port de vlan 20
int g0/0/11
  port link tr
  port tr al vlan all
int g0/0/12
  port link tr
  port tr al vlan all
```

## 配置链路聚合

- SW1配置

```bash
int eth 1
  tru g0/0/3
  tru g0/0/4
  port link tr
  port tr all vlan all
```

- SW2配置

```bash
int eth 1
  tru g0/0/3
  tru g0/0/4
  port link tr
  port tr all vlan al
```

## 调整STP结构

- 在SW1、SW2、SW3修改生成树模式和根路径计算方法

```bash
stp mode stp  //修改为STP模式
stp pathcost-standard dot1d  //修改根路径计算方法
```

- 修改开销

```bash
# SW1
stp priority 8192
# SW2
stp priority 16384
```

> **验证**：在SW3上查看接口状态`dis stp bri`，G0/0/12接口堵塞

## 配置Hybrid

```bash
vlan bat 2 3 4
int g0/0/3
	port hy pv vl 3
	port hy un vl 2 3 4
int g0/0/2
	port hy pv vlan 2
	port hy un vlan 2 3
int g0/0/4
	port hy pv vl 4
	port hy un vl 3 4
```

## R2、R3、R4配置IP

- R2配置

```bash
int lo 0
	ip ad 2.2.2.2 32
int g0/0/0
	ip ad 20.1.0.2 24
int g0/0/1
	ip ad 20.1.2.254 24
int g0/0/2
	ip ad 20.1.12.2 24
int s1/0/0
	ip ad 20.1.24.2 24
```

- R3配置

```bash
int lo 0
	ip ad 3.3.3.3 32
int g0/0/0
	ip ad 20.1.0.3 24
int g0/0/1
	ip ad 20.1.3.254 24
```

- R4配置

```bash
int lo 0
	ip ad 4.4.4.4 32
int g0/0/0
	ip ad 20.1.0.4 24
int g0/0/1
	ip ad 20.1.45.4 24
int s1/0/0
	ip ad 20.1.24.4 24
```

> **验证**：
> 	- 测试R3与R2、R4的连通性
> 	- 测试R2与R3、R4的连通性
> 	- 测试R4与R3、R2的连通性

# 路由部分

- R1作为边界设备，配置一条静态路由

```bash
ip route 0.0.0.0 0  20.1.12.2
```

## 配置RIPv2

- R2配置

```bash
rip
	v 2
	undo sum  //关闭汇总
	net 2.0.0.0
	net 20.0.0.0
```

- R3配置

```bash
rip
	v 2
	undo sum
	net 3.0.0.0
	net 20.0.0.0
```

- R4配置

```bash
rip
	v 2
	undo sum
	net 4.0.0.0
	net 20.0.0.0
```

> **验证**：R2的环回口与R4的环回口互通`ping -a 2.2.2.2 4.4.4.4`

## 配置R2、R4通信走以太网链路

**分析**：

1. 使用`tracert -a 2.2.2.2 4.4.4.4`可以发现R2到R4走的是串行链路，因为串行链路跳数小。
2. 关闭串行口，查看R2的路由表会发现没有R4的路由。因为R3上配置的RIP水平分割（从一个接口收到的路由不会再从这个接口发布），使R2无法学习到R4环回口的路由
- 在R3的G0/0/0接口关闭水平分割（`undo rip split-horizon`），R2、R4可以正常学习到对方的路由
3. R2还是无法访问R4，因为R2访问R4的ARP请求无法经过（路由器）R3发送给R4
- 在R2上指定静态ARP`arp static 20.1.0.4 R3的MAC`
	* `dis int g0/0/0 | incl address`查看接口的MAC地址
- 同样的在R4指定去往R2的静态ARP
4. 由于跳数限制，R2访问R4还是会走串行线路，为了使R2访问R4走以太网链路，需要把串行链路的度量值（跳数）改大

```bash
# R3
int g0/0/0
	undo rip split-horizon  //关闭水平分割
dis int g0/0/0 | incl address  //查看接口的MAC地址
```

- 手工指定静态ARP

```bash
# R2
arp static 20.1.0.4 00e0-fc7c-7473
int s1/0/0
	rip metricout 5
```
```bash
# R4
arp static 20.1.0.2 00e0-fc7c-7473
int s1/0/0
	rip metricout 5
```

> **验证**：在R2上路由追踪，查看R2访问R4的路径`tracert -a 2.2.2.2 4.4.4.4`

# IP部分

- 创建地址池，10.1.10.10与PC1的MAC绑定

```bash
dhcp enable
ip pool BAD
	net 10.1.10.0 mask 24
	gateway-list 10.1.10.254
	static-bind ip-address 10.1.10.10 mac-address 5489-98AE-1B67
	dns-list 20.1.2.1
	q
int g0/0/0
	dhcp select global  //接口选用DHCP地址池
	q
```

> **验证**：在PC1上查看是否获得IP

- 配置NAT

```bash
acl 2000
	rule permit source 10.1.10.0 0.0.0.255
	q
int g0/0/2
	nat outbound 2000
	q
```

- Server1上配置

```bash
IP:20.1.2.1/24
网关：20.1.2.2
```
```bash
DNS1:20.1.2.1——>www.baidu.com
DNS2:20.1.12.5——>www.huawei.com
```

> **验证**：在PC1上测试与Server1的IP、域名的连通性

- 配置公网端口映射，将公网IP20.1.12.5的10080端口映射到内网IP10.1.20.1的80 端口

```bash
int g0/0/2
	nat server protocol tcp global 20.1.12.5 10080 inside 10.1.20.1 80
```

- 配置Server2，开启HTTP服务

```bash
IP:10.1.20.1/24
网关：10.1.20.254
```

- Cilent1配置

```bash
IP:20.1.3.1/24
网关：20.1.3.254
DNS：20.1.2.1
```

> **验证**：在Client1使用HTTPClient客户端访问http://www.huawei.com:10080

# 广域网部分

## 服务端（AR4）配置

```bash
int g0/0/1
	ip ad 20.1.45.4 24
```

- 配置AAA

```bash
aaa
	local-user USER password cipher HUAWEI
	local-user USER service-type ppp
```

- 配置虚管理模板

```bash
int Virtual-Template 1  //创建虚管理模板
	ip ad unnumbered interface g0/0/1  //从接口借地址
	ppp authentication-mode chap  //ppp认证方式
	remote ad 20.1.45.5  //为对端分配地址
```

- 接口绑定虚模板

```bash
int g0/0/1
	pppoe-server bind virtual-template 1
```

## 客户端（AR5）配置

```bash
int g0/0/0
	ip ad 10.1.5.254 24
	q
int Dialer 1  //创建拨号接口
	link-protocol ppp  //链路层协议ppp
	ip address ppp-negotiate  //IP地址通过ppp协商获取
	dialer user USER  //拨号用户
	ppp chap user USER  //ppp认证用户名为USER
	ppp chap password simple HUAWEI  //ppp认证密码
	dialer bundle 1  //接口绑定物理接口
	mtu 1492  //优化，避免分片
	ppp ipcp default-route  //不需要配置静态路由
```

- 绑定拨号接口

```bash
int g0/0/1
	ppoe-client dial-bundle-number 1  //物理接口绑定拨号接口
```

> **验证**：
> 	- 查看R5的g0/0/1接口IP
> 	- 查看PPPoE会话`is pppoe-client session summary`
> 	- 查看是否有默认路由
> 	- 测试与20.1.2.1的连通性

## 配置GRE隧道

- AR5配置

```bash
int t0/0/0
	tunnel-protcol gre  //使用gre协议
	source Dialer 1  //使用拨号接口
	destination 20.1.12.1  //目的IP
	ip ad 10.1.15.5 24  //配置IP
ip route-static 10.1.10.0 24 t0/0/0
```

- AR1配置

```bash
int t0/0/0
	tunnel-protocol gre
	source g0/0/2
	destination 20.1.45.5
	ip ad 10.1.15.1 24
ip route-static 10.1.5.0 24 t0/0/0
```

> **验证**：
> - 在R1测试与10.1.15.5的连通性
> - 在PC2测试与10.1.10.10的连通性

# 安全部分

- R1配置

```bash
acl 3000
	rule deny icmp icmp-type echo  //拒绝icmp外围的echo包
	rule deny udp destination-port range 33434 33464  //拒绝udp协议目的端口是33434-33464的随机端口
int g0/0/2
	traffic-filter inbound acl 3000
```

- 网管限制，调用acl 2000

```bash
user-interface vty 0 4
	acl 2000 inbound 
```

# 保存配置上传FTP

- 保存配置，开启FTP服务器（Server2），将配置上传至FTP服务器

```bash
ftp 10.1.20.1
	put vrpcfg.zip
```

> **验证**：在Server2的FTP目录下查看是否有vrpcfg.zip配置文件