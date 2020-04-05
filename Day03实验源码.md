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


