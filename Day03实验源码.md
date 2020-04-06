[TOC]

-----

# 实验一

- 配置接口IP地址

```bash
# R1
<Huawei>sy
[Huawei]sys R1
[R1]int lo 1
[R1-LoopBack1]ip ad 1.1.1.1 32
[R1-LoopBack1]int g0/0/0
[R1-GigabitEthernet0/0/0]ip ad 192.168.12.1 24
[R1-GigabitEthernet0/0/0]int g0/0/1
[R1-GigabitEthernet0/0/1]ip ad 192.168.13.1 24
[R1-GigabitEthernet0/0/1]q
[R1]

# R2
[R2]int lo 1
[R2-LoopBack1]ip ad 2.2.2.2 32
[R2-LoopBack1]int g0/0/0
[R2-GigabitEthernet0/0/0]ip ad 192.168.12.2 24
[R2-GigabitEthernet0/0/0]int g0/0/1
[R2-GigabitEthernet0/0/1]ip ad 192.168.24.2 24
[R2-GigabitEthernet0/0/1]q
[R2]

# 同样的方法配置其余的三台路由器，主机位地址即为设备的编号，实验所有IP地址主机位均设备编号，特属情况除外
```

- 配置静态路由

```bash
# R1上的配置
[R1]ip route-static 2.2.2.2 32 192.168.12.2
[R1]ip route-static 3.3.3.3 32 192.168.13.3
[R1]ip route-static 4.4.4.4 32 192.168.13.3
[R1]ip route-static 4.4.4.4 32 192.168.13.3 preference 100
Info: Succeeded in modifying route.
[R1]ip route-static 4.4.4.4 32 192.168.12.2 preference 10
Info: Succeeded in modifying route.
[R1]ip route-static 192.168.24.0 24 192.168.12.2
[R1]ip route-static 192.168.34.0 24 192.168.12.2
[R1]

# R2上的配置
[R2]ip route-static 1.1.1.1 32 192.168.12.1 
[R2]ip route-static 3.3.3.3 32 192.168.12.1
[R2]ip route-static 4.4.4.4 32 192.168.24.4
[R2]ip route-static 192.168.34.0 24 192.168.24.4
[R2]ip route-static 192.168.13.0 24 192.168.12.1
[R2]

# R3
[R3]ip route-static 1.1.1.1 32 192.168.13.1
[R3]ip route-static 2.2.2.2 32 192.168.13.1
[R3]ip route-static 4.4.4.4 32 192.168.34.4
[R3]ip route-static 192.168.12.0 24 192.168.13.1
[R3]ip route-static 192.168.24.0 24 192.168.34.4
[R3]

# R4
[R4]ip route-static 2.2.2.2 32 192.168.24.2
[R4]ip route-static 3.3.3.3 32 192.168.34.3 
[R4]ip route-static 1.1.1.1 32 192.168.34.3 preference 10
[R4]ip route-static 192.168.12.0 24 192.168.34.3 preference 10
[R4]ip route-static 192.168.13.0 24 192.168.34.3 preference 10
[R4]
```

- Tracer跟踪查看

```bash
# R1上查看
[R1]tracert 4.4.4.4
 traceroute to  4.4.4.4(4.4.4.4), max hops: 30 ,packet length: 40,press CTRL_C to break 
 1 192.168.12.2 20 ms  20 ms  20 ms 
 2 192.168.24.4 30 ms  20 ms  20 ms 
[R1]tracert 192.168.24.4
 traceroute to  192.168.24.4(192.168.24.4), max hops: 30 ,packet length: 40,press CTRL_C to break 
 1 192.168.12.2 30 ms  20 ms  20 ms 
 2 192.168.24.4 20 ms  30 ms  20 ms 
[R1]tracert 192.168.34.4
 traceroute to  192.168.34.4(192.168.34.4), max hops: 30 ,packet length: 40,press CTRL_C to break 
 1 192.168.12.2 20 ms  30 ms  10 ms 
 2 192.168.24.4 20 ms  30 ms  40 ms 
[R1]

# R4上查看
[R4]tracer 1.1.1.1
 traceroute to  1.1.1.1(1.1.1.1), max hops: 30 ,packet length: 40,press CTRL_C to break 
 1 192.168.34.3 50 ms  20 ms  20 ms 
 2 192.168.13.1 20 ms  20 ms  20 ms 
[R4]tracer 192.168.12.1
 traceroute to  192.168.12.1(192.168.12.1), max hops: 30 ,packet length: 40,press CTRL_C to break 
 1 192.168.34.3 20 ms  20 ms  30 ms 
 2 192.168.13.1 30 ms  30 ms  20 ms 
[R4]tracer 192.168.13.1
 traceroute to  192.168.13.1(192.168.13.1), max hops: 30 ,packet length: 40,press CTRL_C to break 
 1 192.168.34.3 30 ms  20 ms  20 ms 
 2 192.168.13.1 30 ms  30 ms  20 ms 
[R4]
```

> 当配置完成后要进行连通性测试，避免出错！



# 实验二

- R1上的配置

```bash
<Huawei>sy
[Huawei]sys R1 
[R1]int lo 1
[R1-LoopBack1]ip ad 1.1.1.1 24 
[R1-LoopBack1]int g0/0/0
[R1-GigabitEthernet0/0/0]ip ad 12.1.1.1 24
[R1-GigabitEthernet0/0/0]q
[R1]rip 1
[R1-rip-1]network 1.0.0.0
[R1-rip-1]network 12.0.0.0
[R1-rip-1]q
[R1]
```

- R2上的配置

```bash
<Huawei>sy
[Huawei]sys R2
[R2]int lo 1
[R2-LoopBack1]ip ad 2.2.2.2 24
[R2-LoopBack1]int g0/0/0
[R2-GigabitEthernet0/0/0]ip ad 12.1.1.2 24
[R2-GigabitEthernet0/0/0]int g0/0/1
[R2-GigabitEthernet0/0/1]ip ad 23.1.1.2 24
[R2-GigabitEthernet0/0/1]q
[R2]rip 1
[R2-rip-1]net 2.0.0.0
[R2-rip-1]net 12.0.0.0
[R2-rip-1]net 23.0.0.0
[R2-rip-1]q
[R2]
```

- R3上的配置

```bash
<Huawei>sy
[Huawei]sys R3
[R3]int lo 1
[R3-LoopBack1]ip ad 3.3.3.3 24
[R3-LoopBack1]int g0/0/0
[R3-GigabitEthernet0/0/0]ip ad 23.1.1.3 24
[R3-GigabitEthernet0/0/0]q
[R3]rip 1
[R3-rip-1]net 3.0.0.0
[R3-rip-1]net 23.0.0.0
[R3-rip-1]q
[R3]
```

- ping 连通性测试

```bash
[R3]ping 1.1.1.1
[R3]ping 2.2.2.2
[R3]ping 12.1.1.1
```

# 实验三

- 配置IP地址

```bash
# R1
<Huawei>sy
[Huawei]sys R1
[R1]int g0/0/1
[R1-GigabitEthernet0/0/1]ip ad 192.168.10.1 24
[R1-GigabitEthernet0/0/1]int g0/0/0
[R1-GigabitEthernet0/0/0]ip ad 12.1.1.1 24
[R1-GigabitEthernet0/0/0]q
[R1]

# R2
<Huawei>sy
[Huawei]sys R2 
[R2]int g0/0/1
[R2-GigabitEthernet0/0/1]ip ad 192.168.20.2 24
[R2-GigabitEthernet0/0/1]
[R2-GigabitEthernet0/0/1]int g0/0/0 
[R2-GigabitEthernet0/0/0]ip ad 12.1.1.2 24
[R2-GigabitEthernet0/0/0]q
[R2]

# R3
<Huawei>sy
[Huawei]sys R3
[R3]int g0/0/0
[R3-GigabitEthernet0/0/0]ip ad 12.1.1.3 24
[R3-GigabitEthernet0/0/0]q
[R3]
```

- 配置RIP并宣告

```bash
[R1]rip 10  //RIP进程10
[R1-rip-10]version 2  //修改版本为V2的版本
[R1-rip-10]net 192.168.10.0  //对外宣告地址
[R1-rip-10]net 12.0.0.0
[R1-rip-10]q
[R1]


[R2]rip 10 
[R2-rip-10]version 2
[R2-rip-10]net 192.168.20.0
[R2-rip-10]net 12.0.0.0
[R2-rip-10]q
[R2]


[R3]rip 10
[R3-rip-10]net 12.0.0.0
[R3-rip-10]q
[R3]
````

- RIP配置认证

```bash
[R1]int g0/0/0
[R1-GigabitEthernet0/0/0]rip au
[R1-GigabitEthernet0/0/0]rip authentication-mode simple cipher huawei@123
[R1-GigabitEthernet0/0/0]q
[R1]




[R2]int g0/0/0
[R2-GigabitEthernet0/0/0]rip authentication-mode simple cipher huawei@123
[R2-GigabitEthernet0/0/0]q
[R2]
```

- 查看R1与R2的路由表

```bash
[R1]dis ip routing-table  //查看全部路由表
Route Flags: R - relay, D - download to fib
------------------------------------------------------------------------------
Routing Tables: Public
         Destinations : 11       Routes : 11       

Destination/Mask    Proto   Pre  Cost      Flags NextHop         Interface

       12.1.1.0/24  Direct  0    0           D   12.1.1.1        GigabitEthernet0/0/0
       12.1.1.1/32  Direct  0    0           D   127.0.0.1       GigabitEthernet0/0/0
     12.1.1.255/32  Direct  0    0           D   127.0.0.1       GigabitEthernet0/0/0
      127.0.0.0/8   Direct  0    0           D   127.0.0.1       InLoopBack0
      127.0.0.1/32  Direct  0    0           D   127.0.0.1       InLoopBack0
127.255.255.255/32  Direct  0    0           D   127.0.0.1       InLoopBack0
   192.168.10.0/24  Direct  0    0           D   192.168.10.1    GigabitEthernet0/0/1
   192.168.10.1/32  Direct  0    0           D   127.0.0.1       GigabitEthernet0/0/1
 192.168.10.255/32  Direct  0    0           D   127.0.0.1       GigabitEthernet0/0/1
   192.168.20.0/24  RIP     100  1           D   12.1.1.2        GigabitEthernet0/0/0
255.255.255.255/32  Direct  0    0           D   127.0.0.1       InLoopBack0
[R1]

# 查看RIP学习到的路由
[R1]dis ip routing-table protocol rip  //查看有RIP学习的路由表
Route Flags: R - relay, D - download to fib
------------------------------------------------------------------------------
Public routing table : RIP
         Destinations : 1        Routes : 1        

RIP routing table status : <Active>
         Destinations : 1        Routes : 1

Destination/Mask    Proto   Pre  Cost      Flags NextHop         Interface

   192.168.20.0/24  RIP     100  1           D   12.1.1.2        GigabitEthernet0/0/0

RIP routing table status : <Inactive>
         Destinations : 0        Routes : 0

[R1]

# 查看RIP协议下的所有配置
[R1]display current-configuration configuration rip
[R1]dis cu conf rip  //上一条命令的简写
[V200R003C00]
#
rip 10
 version 2
 network 192.168.10.0
 network 12.0.0.0
#
return
[R1]
```

- 抑制接口

```bash
[R1]rip 10
[R1-rip-10]silent-interface GigabitEthernet 0/0/0
[R1-rip-10]peer 12.1.1.2  //单播给R2
```

> - 抑制接口后，R1可以收到R2的路由表信息，但是不会发送路由表信息
> - 抑制接口后，可以使用peer进行单播，发送路由表信息













