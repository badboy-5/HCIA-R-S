	姓名：坏坏
	学习时间：2020年3月21日
	整理时间：2020年3月23日

# 网络工程师认证体系

- 思科：CCNA、CCNP、CCIE
- 华为：HCIA、HCIP、HCIE

# 网络工程师发展的方向

- R&S---路由与交换
- 网络安全---防火墙、VPN
- ISP---运营商
- WLAN---无线
- 云计算
- 存储
- 大数据
- DC---数据中心

# 主要学习内容

- 网络互联基础（IP编址、ARP、ICMP、OSI七层模型）
- 路由协议基础和实现（静态路由、RIP、OSPF）
- 以太网交换技术基础和实现（VLAN、STP、RSTP）
- 广域网技术基础与实现（PPP、HDLC、FR、NAT）

# 数据通信基础

- 数据：
	* 组成：数字、字母、符号、图片、视频、文字等
- 数据通信
	* 数据交换的过程
	* 通过传输介质进行数据传输
	* 传输介质：网线、无线、光纤、同轴电缆等
- 数据通信系统的组成：
	* 发送方
	* 接收方
	* 传输介质
	* 约定---协议
	* 数据包---报文
- 数据流的方向
	* 单工---只接受数据，不会发送数据
	* 半双工---在发送数据的时候，不可以接收数据
	* 双工---在发送数据数据的同时也可以接受数据

# 数据帧

## Ethernet_II 帧格式

![1584894011467](E:%5CBad%5CPictures%5CTypora%20Picture%5C1584894011467.png)

- Ethernet_II 帧类型值大于等于1536
- 以太网数据帧的长度在64-1518字节之间

## IEEE802.3 帧格式

![1584894071069](E:%5CBad%5CPictures%5CTypora%20Picture%5C1584894071069.png)

- IEEE802.3 帧长度字段值小于等于1500

## 数据帧的传输

- 数据链路层基于MAC地址进行帧的传输

# MAC地址

- 由供应商代码和序列号组成
- 前24位代表供应商代码，有IEEE管理和分配（华为的厂商代码为`0x00e0fc`）
- 剩余24位序列号由厂商自己分配

# IP编址

- 网络层的功能和设备
	* 功能：为了在不同的网络之间转发数据，路由寻址和选择路由的过程
	* 设备：路由器
- 常见的网络层协议：
	* IP协议
	* ICMP协议
	* ARP协议
- IP报头：
	* 版本：IPv4、IPv6
	* 报文长度：IP报头的大小为20字节
	* 服务类型：IE中的内容（QOS）
	* 总长度：IP报文+IP报文的上层协议的总长度，单位是字节
	* 生存时间：TTL值，三个默认值64（Linux）、128（Windows）、255（路由器）
- 协议：IP报头中的协议号为6，说明IP报头上承载的是TCP协议
- 报头校验和：用来校验IP报头的完整性
- 源IP地址和目的IP地址
- IP选项位
- 标志位
- 标识符：用来标识该分片的数据包是从哪一个完整的数据包中分片的，标识符相同，则表明是来自同一个数据包

> `MTU`最大传输单元
> 默认网络中一个最大的数据包不能超过1500字节，超过了，路由器、交换机将会对数据包进行分片处理

## IP报文头部

![1584895428577](E:%5CBad%5CPictures%5CTypora%20Picture%5C1584895428577.png)

- `Version`：版本，IPv4、IPv6
- `Header Length`：报文长度
- `DS Field`：服务类型
- `Total Length`：总长度
- `Identification`：标识符
- `Flags`：标识位（做路由控制时使用）
- `Fragment Offset`：片偏移
- `Time to Live`：TTL值，生存时间
- `Protocol`：协议号（6是TCP、17是UDP、1位ICMP）
- `Header Checksum`：报头校验和
- `Source IP Address`：源IP地址
- `Destination IP Address`：目的IP地址
- `IP Options`：IP选项

> - 子网掩码的作用：区分网络位和主机位
> - IP报文头部的TTL字段的作用：防环
> - 网关的作用：用来转发不同网段的数据包

# ICMP协议

- Internet控制报文协议，**ICMP** (Internet Control Message Protocol) 是网络层的一个重要协议，用来在网络设备间传递各种差错和控制信息

## ICMP消息类型和编码类型

| 类型 | 编码 | 描述 |
|:---:|:---:|:---:|
| 0 | 0 | Echo Reply |
| 3 | 0 | 网络不可达 |
| 3 | 1 | 主机不可达 |
| 3 | 2 | 协议不可达 |
| 3 | 3 | 端口不可达 |
| 5 | 0 | 重定向 |
| 8 | 0 | Echo Request |

    以上内容均属原创，如有不详或错误，敬请指出。

<div class="post-copyright">
    <div class="author">
        <b>本文作者： </b>
        <a href="https://blog.csdn.net/qq_45668124" target="_blank">
            <b>坏坏</b> 
        </a> 
    </div>
    <div class="link">
        <b>本文链接： </b>
	https://blog.csdn.net/qq_45668124/article/details/105059324 
    </div>
    <div class="copyright">
        <b> 版权声明： </b>
        本博客所有文章除特别声明外，均采用  
        <a href="https://creativecommons.org/licenses/by-nc-sa/4.0/">
            CC BY-NC-SA 4.0 
        </a> 许可协议。转载请注明出处！
    </div>
</div>