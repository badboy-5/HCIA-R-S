[TOC]

----

# OSPF-Router-id

- 按图配置IP地址

```bash
# R1
<Huawei>sy
[Huawei]sys R1
[R1]int lo 1
[R1-LoopBack1]ip ad 1.1.1.1 32
[R1-LoopBack1]int g0/0/0
[R1-GigabitEthernet0/0/0]ip ad 12.1.1.1 24
[R1-GigabitEthernet0/0/0]q
[R1]

# R2
<Huawei>sy
[Huawei]sys R2
[R2]int lo 1
[R2-LoopBack1]ip ad 2.2.2.2 32
[R2-LoopBack1]int g0/0/0
[R2-GigabitEthernet0/0/0]ip ad 12.1.1.2 24
[R2-GigabitEthernet0/0/0]int g0/0/1
[R2-GigabitEthernet0/0/1]ip ad 23.1.1.2 24
[R2-GigabitEthernet0/0/1]q
[R2]

# R3
<Huawei>sy
[Huawei]sy R3  
[R3]int lo 1
[R3-LoopBack1]ip ad 3.3.3.3 32
[R3-LoopBack1]int g0/0/0
[R3-GigabitEthernet0/0/0]ip ad 23.1.1.3 24
[R3-GigabitEthernet0/0/0]q
[R3]
```

- 配置OSPF

```bash
# R1
[R1]ospf 1  //指定OSPF的进程号1               
[R1-ospf-1]area 0  //进入骨干区域
[R1-ospf-1-area-0.0.0.0]network 12.1.1.0 0.0.0.255  //宣告网段
[R1-ospf-1-area-0.0.0.0]net 1.1.1.1 0.0.0.0  //宣告精确地址
[R1-ospf-1-area-0.0.0.0]q
[R1-ospf-1]q
[R1]

# R2
[R2]ospf 1
[R2-ospf-1]area 0
[R2-ospf-1-area-0.0.0.0]net 2.2.2.2 0.0.0.0
[R2-ospf-1-area-0.0.0.0]net 12.1.1.2 0.0.0.0
[R2-ospf-1-area-0.0.0.0]net 23.1.1.2 
[R2-ospf-1-area-0.0.0.0]net 23.1.1.2 0.0.0.0
[R2-ospf-1-area-0.0.0.0]q
[R2-ospf-1]q

# 查看邻居关系
[R1]dis ospf peer bri  //查看邻居关系，Router-ID为2.2.2.2

         OSPF Process 1 with Router ID 1.1.1.1
                  Peer Statistic Information
 ----------------------------------------------------------------------------
 Area Id          Interface                        Neighbor id      State    
 0.0.0.0          GigabitEthernet0/0/0             2.2.2.2          Full        
 ----------------------------------------------------------------------------
[R1]

# R3
[R3]ospf 
[R3-ospf-1]area 0
[R3-ospf-1-area-0.0.0.0]net 3.3.3.3 0.0.0.0
[R3-ospf-1-area-0.0.0.0]net 23.1.1.3 0.0.0.0

# Init状态
[R3-ospf-1-area-0.0.0.0]
Apr  7 2020 00:03:49-08:00 R3 %%01OSPF/4/NBR_CHANGE_E(l)[0]:Neighbor changes event: neighbor status changed. (ProcessId=256, NeighborAddress=2.1.1.23, NeighborEvent=HelloReceived, NeighborPreviousState=Down, NeighborCurrentState=Init) 

# 2Way状态
[R3-ospf-1-area-0.0.0.0]
Apr  7 2020 00:03:49-08:00 R3 %%01OSPF/4/NBR_CHANGE_E(l)[1]:Neighbor changes event: neighbor status changed. (ProcessId=256, NeighborAddress=2.1.1.23, NeighborEvent=2WayReceived, NeighborPreviousState=Init, NeighborCurrentState=2Way) 

# ExStart状态
[R3-ospf-1-area-0.0.0.0]
Apr  7 2020 00:03:49-08:00 R3 %%01OSPF/4/NBR_CHANGE_E(l)[2]:Neighbor changes event: neighbor status changed. (ProcessId=256, NeighborAddress=2.1.1.23, NeighborEvent=AdjOk?, NeighborPreviousState=2Way, NeighborCurrentState=ExStart) 

# Exchange状态
[R3-ospf-1-area-0.0.0.0]
Apr  7 2020 00:03:49-08:00 R3 %%01OSPF/4/NBR_CHANGE_E(l)[3]:Neighbor changes event: neighbor status changed. (ProcessId=256, NeighborAddress=2.1.1.23, NeighborEvent=NegotiationDone, NeighborPreviousState=ExStart, NeighborCurrentState=Exchange) 

# Loading状态
[R3-ospf-1-area-0.0.0.0]
Apr  7 2020 00:03:49-08:00 R3 %%01OSPF/4/NBR_CHANGE_E(l)[4]:Neighbor changes event: neighbor status changed. (ProcessId=256, NeighborAddress=2.1.1.23, NeighborEvent=ExchangeDone, NeighborPreviousState=Exchange, NeighborCurrentState=Loading) 

# Full状态
[R3-ospf-1-area-0.0.0.0]
Apr  7 2020 00:03:49-08:00 R3 %%01OSPF/4/NBR_CHANGE_E(l)[5]:Neighbor changes event: neighbor status changed. (ProcessId=256, NeighborAddress=2.1.1.23, NeighborEvent=LoadingDone, NeighborPreviousState=Loading, NeighborCurrentState=Full) 

[R3-ospf-1-area-0.0.0.0]q
[R3-ospf-1]q
[R3]
# 连通性测试
```

> 这里也可以是`[R1-ospf-1-area-0.0.0.0]network 12.1.1.1 0.0.0.255`宣告某一个接口地址，这种方式称为精确宣告。也可以是宣告某一个网段

- 手动指定Router-id

```bash
[R1]ospf router-id 1.1.1.1  //手动指定Router-ID
# 如果没有手动指定router-id会自动选取

# R2
[R2]router id 12.1.1.2  //手动指定Router-ID
Info: Router ID has been modified, please reset the relative protocols manually to update the Router ID.
[R2]q
<R2>reset ospf process  //重新启动OSPF进程
# 其他路由器同样重新启动OSPF进程

# R1
[R1]q     

<R1>reset ospf pro
# 中间会有连接提示信息
<R1>sy
Enter system view, return user view with Ctrl+Z.
[R1]dis ospf peer bri  //查看邻居关系，Router-ID变成了指定的12.1.1.2

         OSPF Process 1 with Router ID 1.1.1.1
                  Peer Statistic Information
 ----------------------------------------------------------------------------
 Area Id          Interface                        Neighbor id      State    
 0.0.0.0          GigabitEthernet0/0/0             12.1.1.2         Full        
 ----------------------------------------------------------------------------
[R1]dis ospf int g0/0/0  //查看接口下的OSPF
```

# OSPF单区域&多区域

## OSPF单区域

- PC上的配置

```bash
# PC1
IP:172.16.1.1
子网掩码：255.255.255.0
网关：172.16.1.254

# PC2
IP:172.16.2.1
子网掩码：255.255.255.0
网关：172.16.2.254

# PC3
IP:172.16.3.1
子网掩码：255.255.255.0
网关：172.16.3.254
```

- 配置路由器相关的接口地址

```bash
# R1
[R1]int lo 1
[R1-LoopBack1]ip ad 1.1.1.1 32
[R1-LoopBack1]int g0/0/0
[R1-GigabitEthernet0/0/0]ip ad 172.16.12.1 24
[R1-GigabitEthernet0/0/0]int g0/0/1
[R1-GigabitEthernet0/0/1]ip ad 172.16.1.254 24
[R1-GigabitEthernet0/0/1]int g0/0/2
[R1-GigabitEthernet0/0/2]ip ad 172.16.13.1 24
[R1-GigabitEthernet0/0/2]q
[R1]

# R2
[R2]int lo 1
[R2-LoopBack1]ip ad 2.2.2.2 32
[R2-LoopBack1]int g0/0/0
[R2-GigabitEthernet0/0/0]ip ad 172.16.12.2 24
[R2-GigabitEthernet0/0/0]int g0/0/1
[R2-GigabitEthernet0/0/1]ip ad 172.16.23.2 24
[R2-GigabitEthernet0/0/1]int g0/0/2
[R2-GigabitEthernet0/0/2]ip ad 172.16.2.254 24
[R2-GigabitEthernet0/0/2]q
[R2]


# R3
[R3]int lo 1
[R3-LoopBack1]ip ad 3.3.3.3 32
[R3-LoopBack1]int g0/0/0
[R3-GigabitEthernet0/0/0]ip ad 172.16.23.3 24
[R3-GigabitEthernet0/0/0]int g0/0/1
[R3-GigabitEthernet0/0/1]ip ad 172.16.13.3 24
[R3-GigabitEthernet0/0/1]int g0/0/2
[R3-GigabitEthernet0/0/2]ip ad 172.16.3.254 24
[R3-GigabitEthernet0/0/2]q
[R3]
```

- 配置OSPF

```bash
# R1
[R1]ospf router-id 1.1.1.1  //手动指定Router-id
[R1-ospf-1]area 0  //进入骨干区域
[R1-ospf-1-area-0.0.0.0]net 1.1.1.1 0.0.0.0  //精确宣告1.1.1.1
[R1-ospf-1-area-0.0.0.0]net 172.16.1.254 0.0.0.0  //精确宣告172.16.1.254
[R1-ospf-1-area-0.0.0.0]net 172.16.13.1 0.0.0.0  //精确宣告172.16.13.1
[R1-ospf-1-area-0.0.0.0]net 172.16.12.1 0.0.0.0  //精确宣告172.16.12.1

# R2
[R2]ospf router-id 2.2.2.2
[R2-ospf-1]area 0
[R2-ospf-1-area-0.0.0.0]net 2.2.2.2 0.0.0.0
[R2-ospf-1-area-0.0.0.0]net 172.16.2.254 0.0.0.0 
[R2-ospf-1-area-0.0.0.0]net 172.16.23.2 0.0.0.0 
[R2-ospf-1-area-0.0.0.0]net 172.16.12.2 0.0.0.0
[R2-ospf-1-area-0.0.0.0]
邻居关系建立提示信息。。。
[R2-ospf-1-area-0.0.0.0]

# R3
[R3]ospf router-id 3.3.3.3 
[R3-ospf-1]area 0
[R3-ospf-1-area-0.0.0.0]net 3.3.3.3 0.0.0.0
[R3-ospf-1-area-0.0.0.0]net 172.16.3.254 0.0.0.0
[R3-ospf-1-area-0.0.0.0]net 172.16.23.3 0.0.0.0
[R3-ospf-1-area-0.0.0.0]net 172.16.13.3 0.0.0.0
[R3-ospf-1-area-0.0.0.0]
邻居关系建立提示信息。。。
[R3-ospf-1-area-0.0.0.0]
```

- 查看OSPF的所有配置

```bash
[R1]dis cu conf ospf  //查看OSPF的所有配置
[V200R003C00]
#
ospf 1 router-id 1.1.1.1 
 area 0.0.0.0 
  network 172.16.1.254 0.0.0.0 
  network 172.16.12.1 0.0.0.0 
  network 172.16.13.1 0.0.0.0 
#
return
[R1]
```

- 查看邻居关系

```bash
# R1
[R1]dis ospf peer bri  //查看OSPF的邻居状态

         OSPF Process 1 with Router ID 1.1.1.1
                  Peer Statistic Information
 ----------------------------------------------------------------------------
 Area Id          Interface                        Neighbor id      State    
 0.0.0.0          GigabitEthernet0/0/0             2.2.2.2          Full        
 0.0.0.0          GigabitEthernet0/0/2             3.3.3.3          Full        
 ----------------------------------------------------------------------------
[R1]

# R2
[R2]dis ospf peer bri

         OSPF Process 1 with Router ID 2.2.2.2
                  Peer Statistic Information
 ----------------------------------------------------------------------------
 Area Id          Interface                        Neighbor id      State    
 0.0.0.0          GigabitEthernet0/0/0             1.1.1.1          Full        
 0.0.0.0          GigabitEthernet0/0/1             3.3.3.3          Full        
 ----------------------------------------------------------------------------
[R2]

# R3
[R3]dis ospf peer bri

         OSPF Process 1 with Router ID 3.3.3.3
                  Peer Statistic Information
 ----------------------------------------------------------------------------
 Area Id          Interface                        Neighbor id      State    
 0.0.0.0          GigabitEthernet0/0/0             2.2.2.2          Full        
 0.0.0.0          GigabitEthernet0/0/1             1.1.1.1          Full        
 ----------------------------------------------------------------------------
[R3]
```

- PC1上做连通性测试

```bash
PC>ping 172.16.2.1

Ping 172.16.2.1: 32 data bytes, Press Ctrl_C to break
Request timeout!
From 172.16.2.1: bytes=32 seq=2 ttl=126 time=31 ms
From 172.16.2.1: bytes=32 seq=3 ttl=126 time=16 ms
From 172.16.2.1: bytes=32 seq=4 ttl=126 time=15 ms
From 172.16.2.1: bytes=32 seq=5 ttl=126 time=32 ms

--- 172.16.2.1 ping statistics ---
  5 packet(s) transmitted
  4 packet(s) received
  20.00% packet loss
  round-trip min/avg/max = 0/23/32 ms

PC>ping 172.16.3.1

Ping 172.16.3.1: 32 data bytes, Press Ctrl_C to break
Request timeout!
From 172.16.3.1: bytes=32 seq=2 ttl=126 time=31 ms
From 172.16.3.1: bytes=32 seq=3 ttl=126 time=16 ms
From 172.16.3.1: bytes=32 seq=4 ttl=126 time=31 ms
From 172.16.3.1: bytes=32 seq=5 ttl=126 time=16 ms

--- 172.16.3.1 ping statistics ---
  5 packet(s) transmitted
  4 packet(s) received
  20.00% packet loss
  round-trip min/avg/max = 0/23/31 ms

PC>

```

- 查看路由表

```bash
[R1]dis ip routing-table protocol ospf  //查看OSPF学习到的路由表
Route Flags: R - relay, D - download to fib
------------------------------------------------------------------------------
Public routing table : OSPF
         Destinations : 5        Routes : 6        

OSPF routing table status : <Active>
         Destinations : 5        Routes : 6

Destination/Mask    Proto   Pre  Cost      Flags NextHop         Interface

        2.2.2.2/32  OSPF    10   1           D   172.16.12.2     GigabitEthernet0/0/0
        3.3.3.3/32  OSPF    10   1           D   172.16.13.3     GigabitEthernet0/0/2
     172.16.2.0/24  OSPF    10   2           D   172.16.12.2     GigabitEthernet0/0/0
     172.16.3.0/24  OSPF    10   2           D   172.16.13.3     GigabitEthernet0/0/2
    172.16.23.0/24  OSPF    10   2           D   172.16.12.2     GigabitEthernet0/0/0
                    OSPF    10   2           D   172.16.13.3     GigabitEthernet0/0/2

OSPF routing table status : <Inactive>
         Destinations : 0        Routes : 0

[R1]
```

## OSPF多区域

- R2 & R3上新增配置

```bash
[R2]int g2/0/0
[R2-GigabitEthernet2/0/0]ip ad 172.16.24.2 24
[R2-GigabitEthernet2/0/0]q
[R2]ospf 1
[R2-ospf-1-area-0.0.0.1]net 172.16.24.2 0.0.0.0
[R2-ospf-1-area-0.0.0.1]

[R3]int g2/0/0
[R3-GigabitEthernet2/0/0]ip ad 172.16.35.3 24
[R3-GigabitEthernet2/0/0]q
[R3]ospf 1
[R3-ospf-1]area 2
[R3-ospf-1-area-0.0.0.2]net 172.16.35.3 0.0.0.0
```

> 需要添加接口卡

- R4&R5上的配置

```bash
[R4]int lo 1
[R4-LoopBack1]ip ad 4.4.4.4 32    
[R4-LoopBack1]int g0/0/0
[R4-GigabitEthernet0/0/0]ip ad 172.16.24.4 24
[R4-GigabitEthernet0/0/0]q
[R4]ospf 1  //进入OSPF进程
[R4-ospf-1]area 1  //进入区域1
[R4-ospf-1-area-0.0.0.1]net 4.4.4.4 0.0.0.0  //精确宣告IP地址
[R4-ospf-1-area-0.0.0.1]net 172.16.24.4 0.0.0.0
[R4-ospf-1-area-0.0.0.1]

[R5]int lo 1
[R5-LoopBack1]ip ad 5.5.5.5 32
[R5-LoopBack1]int g0/0/0  
[R5-GigabitEthernet0/0/0]ip ad 172.16.35.5 24
[R5-GigabitEthernet0/0/0]q
[R5]ospf 1
[R5-ospf-1]area 2  //进入区域2
[R5-ospf-1-area-0.0.0.2]net 5.5.5.5 0.0.0.0  //精确宣告
[R5-ospf-1-area-0.0.0.2]net 172.16.35.5 0.0.0.0
[R5-ospf-1-area-0.0.0.2]
```

- 显示当前学习到的LSA信息

```bash
[R2]dis ospf lsdb  //查看连接的数据库

         OSPF Process 1 with Router ID 2.2.2.2
                 Link State Database 

                         Area: 0.0.0.0
 Type      LinkState ID    AdvRouter          Age  Len   Sequence   Metric
 Router    2.2.2.2         2.2.2.2            975  72    80000012       1
 Router    1.1.1.1         1.1.1.1           1677  60    8000001D       1
 Router    3.3.3.3         3.3.3.3            829  72    80000011       1
 Network   172.16.23.3     3.3.3.3           1667  32    80000004       0
 Network   172.16.13.3     3.3.3.3           1677  32    80000002       0
 Network   172.16.12.2     2.2.2.2           1666  32    80000003       0
 Sum-Net   172.16.35.0     3.3.3.3            761  28    80000001       1
 Sum-Net   172.16.24.0     2.2.2.2            877  28    80000001       1
 Sum-Net   5.5.5.5         3.3.3.3            184  28    80000001       1
 Sum-Net   4.4.4.4         2.2.2.2            328  28    80000001       1
 
                         Area: 0.0.0.1
 Type      LinkState ID    AdvRouter          Age  Len   Sequence   Metric
 Router    4.4.4.4         4.4.4.4            329  48    80000004       0
 Router    2.2.2.2         2.2.2.2            320  36    80000005       1
 Network   172.16.24.2     2.2.2.2            320  32    80000002       0
 Sum-Net   172.16.35.0     2.2.2.2            760  28    80000001       2
 Sum-Net   172.16.13.0     2.2.2.2            877  28    80000001       2
 Sum-Net   172.16.12.0     2.2.2.2            877  28    80000001       1
 Sum-Net   5.5.5.5         2.2.2.2            183  28    80000001       2
 Sum-Net   3.3.3.3         2.2.2.2            877  28    80000001       1
 Sum-Net   172.16.3.0      2.2.2.2            877  28    80000001       2
 Sum-Net   172.16.2.0      2.2.2.2            877  28    80000001       1
 Sum-Net   172.16.1.0      2.2.2.2            877  28    80000001       2
 Sum-Net   2.2.2.2         2.2.2.2            877  28    80000001       0
 Sum-Net   172.16.23.0     2.2.2.2            877  28    80000001       1
 
[R2]
```

- 连通性测试

# HDLC


```bash
[R1]int s1/0/0
[R1-Serial1/0/0]dis th
[V200R003C00]
#
interface Serial1/0/0
 link-protocol ppp  //串行接口下默认使用的是PPP协议
#
return
[R1-Serial1/0/0]
```

- 修改默认的端口协议

```bash
# R1修改端口协议
[R1-Serial1/0/0]link-protocol ?
  fr    Select FR as line protocol  //帧中继
  hdlc  Enable HDLC protocol
  lapb  LAPB(X.25 level 2 protocol)
  ppp   Point-to-Point protocol 
  sdlc  SDLC(Synchronous Data Line Control) protocol 
  x25   X.25 protocol
[R1-Serial1/0/0]link-protocol hdlc   //修改为hdlc协议
Warning: The encapsulation protocol of the link will be changed. Continue? [Y/N]:y  //确认
Apr  9 2020 15:26:37-08:00 R1 %%01IFNET/4/CHANGE_ENCAP(l)[3]:The user performed the configuration that will change the encapsulation protocol of the link and then selected Y. 
[R1-Serial1/0/0]

# R2修改端口协议
[R2]int s1/0/0
[R2-Serial1/0/0]link-protocol hdlc 
[R2-Serial1/0/0]
```

- 配置IP地址

```bash
[R1-Serial1/0/0]ip ad 12.1.1.1 30

[R2-Serial1/0/0]ip ad 12.1.1.2 30
# 配置完IP地址就可以互通
```

- HDLC接口地址借用

```bash
# 取消R1上s1/0/0接口的IP地址，增加环回口地址
[R1]int S1/0/0
[R1-Serial1/0/0]dis th
[V200R003C00]
#
interface Serial1/0/0
 link-protocol hdlc
 ip address 12.1.1.1 255.255.255.252 
#
return
[R1-Serial1/0/0]undo ip ad  //取消配置
[R1-Serial1/0/0]dis th
[V200R003C00]
#
interface Serial1/0/0
 link-protocol hdlc
#
return
[R1-Serial1/0/0]q
[R1]int lo 1
[R1-LoopBack1]ip ad 12.1.1.1 32  //配置环回口地址
[R1-LoopBack1]q
[R1]int s1/0/0
[R1-Serial1/0/0]ip address unnumbered interface LoopBack 1  //接口借用

# 添加静态路由
[R1]ip route-static 12.1.1.0 30 s1/0/0

# 连通性测试
```

# PPP

## PAP认证

```bash
[R1]int s1/0/0
[R1-Serial1/0/0]ip ad 12.1.1.1 30 

[R2]int s1/0/0
[R2-Serial1/0/0]ip ad 12.1.1.2 30 
```

```bash
[R1]int s1/0/0
[R1-Serial1/0/0]dis th
[V200R003C00]
#
interface Serial1/0/0
 link-protocol ppp  //接口为PPP协议
 ip address 12.1.1.1 255.255.255.252 
#
return
[R1-Serial1/0/0]ppp authentication-mode pap  //PPP认证模式修改为PAP
[R1-Serial1/0/0]q

# AAA认证
[R1]aaa
[R1-aaa]local-user bad password cipher huawei123  //配置用户名和密码
Info: Add a new user.
[R1-aaa]local-user bad service-type ppp  //配置用户用于PPP
[R1-aaa]

# R2上配置
[R2]int s1/0/0
[R2-Serial1/0/0]ppp pap local-user bad password cipher huawei123  //被认证方认证
[R2-Serial1/0/0]q
[R2]
# 连通性测试
```

## Chap认证

- R1、R2配置接口IP地址

```bash
# 配置Chap认证
[R1]int s1/0/0
[R1-Serial1/0/0]ppp authentication-mode chap 
[R1-Serial1/0/0]q

# 配置AAA认证
[R1]aaa
[R1-aaa]local-user bad password cipher huawei123  //配置用户名和密码
Info: Add a new user.
[R1-aaa]local-user bad service-type ppp  //配置用户用于PPP
[R1-aaa]

# 
[R2]int s1/0/0 
[R2-Serial1/0/0]ppp chap user bad
[R2-Serial1/0/0]ppp chap password cipher huawei123

# 连通性测试
```