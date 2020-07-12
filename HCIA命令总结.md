---
姓名：坏坏
整理时间：2020年7月6日
---

**目录**

[toc]

# 命令符

- 从用户视图切换到系统视图
	* `system–view`
- 从系统视图切换到用户视图
	* `quit`
- 连入接口命令
	* `interface`
- IP地址、子网掩码配置命令
	* `ip address`
- 接口IP信息查看命令
	* `display ip interface brief`
- IPv4路由表信息查询命令
	* `display ip routing–table`
- 配置完成退回视图界面命令
	* `return`
- 命令自动补全快捷键
	* `【Tab】`
- 快捷键查看命令
	* `display hotkey`
- 路由名称修改命令
	* `sysname （参数）`
- 设置路由器时钟命令
	* `clock datetime`
- 设置路由器时区命令
	* `clock  timezone(时区）{add|minus}（偏移时间）[正向偏移add；负向偏minus]`
- 登录标题修改命令
	* `header login`
	* `header login information “ ”`
- 登录成功后标题设置命令
	* `header shell`
	* `header  shell  information “ ”`
- 路由信息查看命令
	* `display  version`
- 路由当前配置查看命令
	* `display current–configuration`
- 接口状态查询命令
	* `display interface gigabitethernet0/0/0`

# 快捷键

- 删除光标位置的前一个字符，光标左移
	* `退格键BavkSpace`
- 光标向左移动一个字符位置；若已经到达命令起始位置，则停止
	* `左光标键或<Ctrl+B>`
- 光标向右移动一个字符位置；若已经到达命令起始位置，则停止
	* `右光标键或<Ctrl+F>`
- 删除光标所在位置的一个字符,光标位置保持不动，光标后方字符向左移动一个字符位置
	* `删除键Delete`
- 显示上一条历史命令。如果需显示更早的历史命令，可以重复使用该功能键
	* `上光标键【Ctrl+P】`
- 显示下一条历史命令，可重复使用该功能键
	* `下光标键【Ctrl+N】`
- 将光标移动到当前行的开始
	* `Ctrl+A`
- 将光标移动到当前行的末尾
	* `Ctrl+E`
- 清空当前行输入的命令
	* `Ctrl+X`
- 停止当前正在执行的功能
	* `Ctrl+C`
- 返回到用户视图，相当于return命令
	* `Ctrl+ F`
- 部分帮助的功能，输入不完整的关键字后按下Tab键，系统自动补全关键
	* `{Tab}键`

# 用户配置

- 设备当前支持用户界面信息查看命令
	* `display user-interface`
- 用户界面切换命令
	* `user-interface(用户界面相对编号)（用户界面可选参数）`
- 在对应的用户视图下对用户权限配置命令
	* `user privilege level (用户级别)`
- 配置用户界面验证方式的命令
	* `authentication-mode{aaa|none|password}`
- 配置VTY为aaa验证方式的用户名和密码 
	* `aaa`
	* `local-user (用户名) password cipher (密码)`
	* `local-user (用户名) service-type telnet(代指接入类型)`
- 配置用户权限命令
	* `local-user (用户名) password cipher (密码) privilege level(权限)`
	* `local-user(用户名) privilege level(权限)`
- 配置Console用户界面为Password验证
	* `set authentication password cipher (密码)`
- Console用户配置信息查看命令
	* `display current-configuration`
- 手动保存当前配置命令
	* `save【configuration-file】参数

> configuration-file为指定的配置文件名，格式必须是”*.cfg"或”*.zip" 周期性自动保存的设置

# 用户视图命令

- 开启周期性自动保存命令
	* `autosave interval on`

- 设置自动保存周期命令
	* `autosave intervaltime`

> 参数time为指定周期的时间周期（参数：time取值应大于10 min）

- 开启定时自动保存命令
	* `autosave time on`
- 设置定时保存命令
	* `autosave time`
- 下次启动的配置文件夹设置命令
	* `startup saved-configuration`
- 当前配置与下次启动配置文件差异查看命令
	* `compare configuration`
- 查看当前文件下命令
	* `dir[/all][filename|directory]`

> - all表示查看当前命令下的文件和目录
> - 参数filename表示待查看文件的名称
> - directory表示待查看目录的路径

- 所在目录查询命令
	* `pwd`
- 新建目录的命令
	* `mkdir  directory`

>  参数directory表示需要创建的目录(创建文件夹)

- 复制并重命名文件
	* `copy  source-fliename destinction-filename`

> - 参数source-fliename表示被复制文件的路径及源文件名
> - destinction-filename表示目标文件的路径及其目标文件名

- 修改当前工作路径命令
	* `cd directory`
- 删除文件命令
	* `delete 【/unreserved】[/force]filename`

> - /unerserved表示彻底删除指定文件，删除的文件将不可恢复
> - /force 表示无需确认直接删除文件
> - 参数filename表示删除的文件名
> - 如果不适用/unreserved,则delete命令删除的文件将被保存到回收站中。

- 恢复回收站中的文件命令
	* `undelete`
- 彻底删除回收站中的所有文件命令
	* `reset recycle-bin`

# Telnet

- 配置Telnet的验证方式为密码验证方式
	* `authentication-mode password`
	* `ste authentication password cipher (密码)`
- 远程登录设备命令
	* `Telnet ip-address`

> 参数ip-address为登录设备IP地址

- 查看已经登录信息命令
	* `display users`

# SSH

- 生成本地RSA主机秘钥命令
	* `rsa local-key-pair create`
- 新建SSH用户命令
	* `ssh user (用户名) authentication-type password(代指类型)`
- 在ssh服务端查看ssh用户配置命令
	* `display ssh user-information (用户名)`
- 开启ssh服务命令
	* `stelnet server enable(服务开启命令)`
- 首次启用认证命令
	* `ssh client first-time enable 客户端命令`
- 指定用户只支持ssh协议
	* `protocol inbound ssh`
- 指定用户的服务类型
	* `local-user (用户名) service-type ssh`
- 查看SSH服务器端的当前回话连接信息
	* `display ssh server session `
- 查看ssh服务全局配置信息命令
	* `display ssh server status`
- 查看本地秘钥对中的公钥部分命令
	* `display rsa local-key-pair public`

# FTP

- 建立FTP连接命令
	* `ftp host-ip【port-nuber】`

> - 参数host-ip表示FTP服务器的IP地址
> - port-number表示FTP服务器的端口号

- 从FTP服务器下载文件到FTP客户端命令
	* `get source-filename【destination-filename】`
- 从FTP客户端上传文件到FTP服务器命令
	* `put source-filename【destination-filename】`
- 查看FTP服务器文件夹状态查询命令
	* `ls`
- 查看设备当前设置的下次启动时所用的启动文件情况命令
	* `display startup`
- 设置下次启动使用的系统软件文件的命令
	* `startup system-software system-file

> 参数system-file表示指定的系统软件文件名

# TFTP

- TFTP文件传输命令
	* `tftp tftp-server{get|put}source-filename[destination-filename]`
> - tftp-server表示TFTP服务器的IP地址
> - get表示从TFTP服务器下载文件到TFTP客户端
> - Put表示TFTP客户端上传文件到TFTP服务
> - source-filename表示源文件名
> - destination-filename表示目标文件名

# SFTP

- 指定FTP用户可访问目录的命令
	* `local-user (用户名) ftp-directory flash`(默认为空，不可不配)

# 配置交换机双工模式

- 关掉自协议商双工
	* `undo negotiation auto`
- 手工指定双工模式为全双工
	* `duplex full`
- 以太网接口速率配置
	* `speed (大小)`【单位Mbit/s】

# ARP及proxy ARP

- 在PC机下查看主机ARP表
	* `arp -a`
- 查看ARPb表
	* `display arp all`
- 添加静态IP和MAC地址
	* `arp static (IP地址) (MAC地址)`
- 开启代理ARP（Proxy ARP）
	* `arp-proxy enable`【接口视图下】

# VLAN基本配置及Access接口

- 创建单个VLAN
	* `vlan (参数)`
- 创建多个VLAN
	* `vlan batch (参数1) (参数2)`
- 配置接口为Access类型接口
	* `port link-type access`
- 配置接口的默认VLAN同时加入VLAN中
	* `port default vlan  (参数)`
- 查看VLAN相关信息（接口和所属vlna的对应关系）
	* `display vlan`

# vlan基本配置  trunk

- 标记命令
	* `description (参数)`
- 查看vlan的简单信息
	* `display vlan summary`
- 查看vlan和接口配置情况
	* `display port vlan`
- 设置接口为trunk接口
	* `port link-type trunk`
- 设置trunk接口允许通过的vlan
	* `port trunk allow-pass vlan (参数)`【all为全部】

# Hybrid接口

- 恢复接口默认VLAN
	* `undo port default vlan`
- 删除VLAN
	* `undo VLAN (参数)`
- 修改接口类型为默认的Hybrid类型
	* `port link-type hybrid`
- 配置交换机在该接口转发指定VLAN的帧
	* `port hybrid untagged vlan (参数/all）`
- 设置Hybrid接口的默认VLAN ID
	* `port hybrid pvid vlan (参数)`
- 配置接口接收的VLAN tag帧
	* `port hybrid tagged vlan (参数/all)`

# 单臂路由实VLAN间路由

- 配置路由端口子接口（逻辑接口）
	* `interface  gigabitethernet0/0/0.x`
- 配置子接口对一层tag报文的终极功能
	* `dot1q termination vid (参数)`
- 开启子接口ARP广播功能
	* `ARP broadcast enable`

> 不开启此功能，将导致子接口无法主动发送ARP广播报文，以及向外转发IP报文

- Ping跟踪查看命令
	* `tracert (ip)`

# 使用三层交换实现VLAN间路由

- 创建VLANif接口命令
	* `interface VLANif (参数)`

# GVRP配置

- 开启gvrp服务
	* `gvrp`
- 查看gvrp的使用情况
	* `display gvrp status` 
- 查看端口的gvrp统计信息
	* `display gvrp statistics`
- 配置模式为fixed
	* `gvrp registration fixed (接口)`
- 配置模式为forbidden
	* `gvrp registration forbidden (接口)`

# STP基本配置命令

- 配置设备STP的工作模式
	* `stp mode {mstp|rstp|stp}`
- 开启stp服务
	* `stp enable`
- 配置桥的优先级
	* `stp priority priority`

> 取值范围0——61440 步长4096 缺省值32768 参数越小，设备被选举为根桥的可能性越大

- 配置设备为根桥
	* `stp root primary`
- 配置设备为备份根桥
	* `stp root secondary`【桥有限级默认为4096 不可修改优先级】
- 生成树的状态信息与统计信息查询
	* `display stp [interface interface-type interface-number][brief]`
- 配置接口的开销值
	* `stp cost (参数)`
- 查看设备的接口信息
	* `display （interface-type interface-number)`

# stp定时器

- 定时配置
	* `stp timer (参数) (时间单位 cs)`
- 关闭命令
	* `shutdown`
- 配置网络直径
	* `stp bridge-diameter (参数)`
- 边缘接口配置
	* `stp edged-port enable`

# MSTP基础配置

- 进入mst域视窗
	* `stp region-configuration`
- 配置MST域名
	* `region-name (参数)`
- 配置mstp的修订级别
	* `revision-level (参数)`
- 制定VLAN的映射到mstp实例生成树
	* `instance (参数) VLAN (参数)`
- 激活mst
	* `active region-configuration`
- 查看mst域配置信息
	* `display stp region-configuration`
- 查看实例中的生成树信息
	* `display stp instance (参数) brief`
- 配置交换机为实例中的根
	* `stp instance (参数) priority (参数)`

# Smart Link 与Monitor Link配置

- 创建smart link组并接入窗口
	* `smart-link group (参数)`
- 开启smart link组功能
	* `smart-link enable`
- 关闭生成树
	* `stp disable   【接口命令】`
- 配置主接口
	* `port（nterface-type interface-number）master`
- 配置备份接口
	* `port（nterface-type interface-number） slave`
- 开启回切功能
	* `restore enable`
- 设置回切时间
	* `timer wtr (参数)`
- 查看smart link组状态
	* `display  smart-link group (参数)`
- 启用monitor link组
	* `monitor-link group (参数)`
- 配置monitor link上行接口
	* `port（nterface-type interface-number）uplink`
- 配置monitor link下行接口
	* `port（nterface-type interface-number）downlink`
- 配置monitor link回切时间
	* `timer recover-time (参数)`

# 配置Eth-trunk链路聚合

- 创建Eth-trunk接口
	* `interface Eth-trunk (参数)`
- 指定Eth-trunk为手工负载分担模式
	* `mode manual load-balance`
- 查看Eth-trunk的接口状态
	* `display Eth-trunk (参数)`
- 查看Eth-trunk的接口信息
	* `display interface Eth-trunk (参数)`
- 查看Eth-trunk的详细信息
	* `disp trunkmembership eth-trunk (参数)`
- 指定Eth-trunk为静态lacp模式
	* `mode lacp-static`
- 指定接口加入指定Eth-trunk接口
	* `Eth-trunk (参数)`
- 配置活动接口上线阙值
	* `max active-linknumber (参数)`
- 修改优先级
	* `lacp priority (参数)`【可取值范围0~32768】

# 静态路由

- 配置下一跳路由ip
	* `ip route-static (目的地址IP段) (子网掩码) (目的IP)`
	* `ip route-static (参数1) (参数2) (nterface-type interface-number)`
- 配置静态IP
	* `ip route-static 0.0.0.0 0 (参数3)`
- 配置路由表优先级
	* `ip route-static (参数) preference (参数)`
- 查看静态路由信息
	* `display ip routing-table protocol static`
- 查看路由表手动添加信息
	* `display ip routing-table protocol static`

# PPP认证

- 设置本端的ppp协议对对端设备的认证方式为pap
	* `ppp  authentication-mode pap domain (域名)`
- 进入aaa视图，创建认证方案
	* `authentication-scheme (域名-id)`
- 配置认证模式为本地认证
	* `authentication-mode local`
- 创建域，并进入域视图
	* `domain (域名)`
- 配置域的认证方式
	* `authentication-scheme (域名-id)`
- 在aaa视图下，配置储存在本地，对对端口认证所使用的用户名和密码
	* `local-user (用户名) passwork cipher (密码)`
	* `local-user  (用户名) service-type ppp`
- 配置本端被对端以pap认证时本地发送的pap用户和密码
	* `ppp pap local-user (用户名) password cipher (密码)`
- 在接口下配置认证方式为chap
	* `ppp authentication-mode chap`
- 在对端接口下配置chcp认证的用户名和密码
	* `ppp chap (用户名)`
	* `ppp chap password (密码)`
- 在接口下配置链路层协议HDLC
	* `link-protocol hdlc`

# 帧中继

- 接口配置链路层协议为FR
	* `link-protocol fr`
- 允许帧中继逆向解析地址生产地址映射表
	* `fr inarp`
- 接口下手动配置ip地址与DLCI的静态映射
	* `fr map ip (ip地址) (DLCI) [broadcast]`
- 查看pvc的建立情况
	* `display fr pvc-info`
- 手工配置ospf邻居
	* `peer (ip地址)`

# RIP配置

- 环回地址接口
	* `Loopback (参数)`
- 开启并创建rip协议
	* `rip`【默认情况下是ripv1】
- 网段接口rip功能制定（开启）命令
	* `network (参数)`【ip段】
- 查看rip协议更更新情况并开启rip调试功能
	* `debugging rip (参数) <>命令`
- 开启debug信息屏幕显示功能
	* `terminal debugging`
	* `terminal monitor`
- 关闭debug命令
	* `undo debugging rip (参数)`
	* `undo debugging all`
- 某类型调试信息查看
	* `debugging rip (参数) (?)`【参数？表示获取帮助，查看相关命】
- 令ripv2搭建(开启ripv2)
	* `version 2`

## RIPv2认证

- 简单密码认证
	* `rip authentication-mode simple (密码)`
- MD5密文验证
	* `rip authentication-mode md5 (usual/nonstandard) (密码)`

> - usual表示使用通用报文格式
> - nonstandard表示使用非标准报文格式（IETF标准）

## RIP路由协议的汇总

- 查看rip默认配置信息
	* `display default-parameter rip`
- ripv2自动汇总
	* `summary always (自动开启)`
- ripv2汇总关闭
	* `undo summary`
- 关闭相应接口下水平分割功能
	* `undo rip split-horizon`
- 相应接口下配置ripv2手动汇总
	* `rip summary-address (网络地址) (子网掩码)`

## 配置RIP的版本兼容，定时器及协议优先级

- 配置以广播形式发送ripv2报文命令
	* `rip version 2 broadcast`
- 配置以组播形式发送ripv2报文命令
	* `rip version 2 multicast`
- 停止发送rip路由更新(撤销rip报文更新)命令
	* `undo rip output (端口视图下命令)`
- rip发布- 数据库中所有激活路由查看命令
	* `display rip  (域) database`
- rip定时器修改命令
	* `timers rip (更行定时器) (超市计时器) (垃圾收集定时器)`
- rip路由优先级需改命令
	* `preference (参数)`
- rip配置全局信息查看命令
	* `display rip`

## 配置RIP抑制接口及单播更新

- 配置rip接口为抑制接口(静默接口)
	* `silent-interface (接口号)`
- 配置rip单播更新命令
	* `peer (ip地址)`
- 禁止接口接收rip报文命令
	* `undo rip input`
- 禁止接口转发rip报文命令
	* `undo rip output`
- 优先级
	* `silent-interface > rip output`
	* `silent-interface > rip output`

## RIP与不连续子网

- ripv1中解决不连续子网问题的方法， 给接口配置第二个ip地址
	* `ip address (ip地址)  sub`
- ripv2 中解决不连续子网问题的方式
	* 关闭vipv2自动汇总

## RIP的水平分割和触发更新

- 打开debug（调试)功能
	* `debugging rip 1 send (接口号)`
- 查看外发的路由条目
	* `terminal monitor`
	* `terminal debugging`
- 关闭debug功能
	* `undo debugging all`
- 接口视图下关闭水平分割功能
	* `undo rip split-horizon`
- 接口视图下开启毒性逆转
	* `rip poison-reverse`

## 配置RIP路由附加度量值

- 配置rip Metricin（增加接受度量）
	* `rip metricin 2`【接口】
- 配置rip Metricout(增加发送度量)
	* `rip metricout 2`【接口】
- 检测源地址到达目标地址所经过网管
	* `tracert #.#.#.#`

## RIP故障处理

- 查看接口配置信息命令
	* `display current-configuration interface(接口号)`
- 接口水平分割信息查询命令
	* `display rip 1 interface(接口号) verbose`
- 查看链路认证信息命令
	* `display rip1 statistics  interface(接口号)`
- 检测是否可以正常收发rip路由(查看rip路由表)
	* `display ip routing-table protocol rip`
- 检查路由路由度量值命令
	* `display rip 1 route`
- 查看所有与字符串rip相关的配置命令
	* `display  current-configuration |{include（包含所有) rip参数}`

## RIP路由引入

- 引入源路由~路由引入命令
	* `import-route direct`【直连】
- 学习引入~路由引入命令
	* `import-route {static}`【静态】
- 查看rip邻居
	* `display rip 1 neighbor`

# OSPF区域配置

- 创建ospf命令
	* `ospf 1`【OSPF进程】
- 创建ospf区域命令
	* `area`【0默认为主干区域】
- 指定ospf协议接口和接口所属区域
	* `network (iP地址段)`【ip所属网管对应子网位】
- 检查ospf接口通告
	* `display ospf interface`
- 查看ospf邻居状态
	* `display ospf peer brief`
- 查看ospf路由表
	* `display ip routing-table protocol ospf`
- 查看ospf链路状态数据库信息
	* `display ospf lsdb`

## OSPF认证与被动接口配置

- 区域明文认证
	* `authentication-mode simple plain (密码)`【plain表示明文显示】
- 区域密文认证
	* `authentication-mode md5 1 (密码)`【1为验证字标识符】
- 链路认证
	* `ospf authentication-mode md5 1 (密码)`
- 配置被动接口，禁止接口接收和发送ospf报文
	* `silent-interface (接口号) {all}`

## Router-ID DR与BDR

- 查看当前设备Router-ID
	* `display router id`
- 配置router-id
	* `router id #.#.#.#`
- 重置ospf协议进程
	* `reset ospf process`
- 配置ospf协议私有router-id
	* `ospf 1 route-id (IP地址)`
- ospf网络类型点到多点配置
	* `ospf network-type p2mp`
- 还原默认广播类型
	* `ospf network-type broadcast`
- 配置接口DR优先级
	* `ospf dr-priority (参数)`

## OSPF开销值，协议优先级及计时器的修改

- 修改ospf协议优先级
	* `prefernece (参数)`
- 配置运行ospf协议所需开销值
	* `ospf cost (参数)`
- 修改hello计时器命令
	* `ospf timer hello (参数)`
- 修改dead计时器命令
	* `ospf timer dead(参数)`

# RIP与OSPF的配置

- 双向引入路由命令
	* `import-route (参数rip/ospf)`
- 手工配置引入时的开销
	* `imoprt-toute (rip/ospf) cost (ospf/rip)`
- rip默认路由发布
	* `default-route originate`
- ospf默认路由发布
	* `default-route-advertise always`

# IPv6

- 开启ipv6全局功能
	* `ipv6`
- 在端口下开启ipv6功能
	* `ipv6 enable`
- 配置自动生成的链路本地地址
	* `ipv6 address auto link-local`
- 查看自动生成的链路本地地址
	* `display ipv6 interface`
- 配置ipv6命令
	* `ipv6 address (地址)`
- 查看配置的全局地址
	* `display ipv6 interface(接口号)`
- 配置结果查看命令
	* `display ipv6 interface brief`

# DHCP

- 开启dhcp功能
	* `dhcp enable`【全局】
- 开启接口dhcp服务功能
	* `dhcp select interface `
- 配置租期
	* `dhcp servse lease [day] [hour] [minute]`
- 配置地址池中不参与分配的地址范围
	* `dhcp server excluded-ip-address (ip段始)（ip段尾)`
- 指定接口下dns服务器
	* `dhcp server dns-list (ip 地址)`
- 查看dhcp地址池中地址分布情况
	* `display ip pool`
- 创建一个全局地址池
	* `ip pool (名称)`
- 配置全局地址池分配网段范围
	* `network（ip地址) `
- 配置dncp客户端网关地址
	* `gateway-list (ip地址)`
- 开启接口的dncp功能使用全局地址池为客户端分配地址
	* `dhcp  select global`
- 开启接口dhcp中继gon功能
	* `dhcp select relay`
- 指定dhcp服务器地址
	* `dhcp reley server-ip (ip地址)`
- 创建dhcp服务器组
	* `dhcp server group (名称)`
- 添加远端dhcp服务器组
	* `dhcp-server (ip地址)`
- 配置端口到指定的服务器组
	* `dhcp relay server-select (名称)`

# 基本ACL

- 基本acl规则的命令结构
	* `rule [rule-id] {deny|permit} [source {source-address source-wildcard|any }|fragment|logging|time-range time-name]`

>  - rele-id表示这条规则的编号，默认步长位5
>  -  deny|permit表示这条规则的相关处理动作。deny表示"拒绝"；permit表示"允许"
>  -  source表示源ip地址信息
>  -  source-address表示具体的源ip地址
>  -  source-wildcard表示与source-address相对应得匹配符
>  -  any表示源ip地址可以是任何地址
>  -  fragment表示该规则只对分区非首片分区报文有效
>  -  logging表示需要将匹配上该规则的ip报文进行日志记录
>  -  time-range time-name表示该规则的生效时间段为time-name

**编号区分**

- 2000~2999基本acl 
- 3000~3999高级acl
- 4000~4999二层acl
- 5000~5999自定义acl

- 创建ACL：acl (编号)
- 拒接源ip地址规报文则
	* `rule deny source (ip地址) (匹配符)`
- 拒接目的ip地址报文规则
	* `rule deny destination (ip地址) (匹配符)`
- 使用报文过滤技术将acl规则应用到接口上
	* `traffic-filter [utbound/inbound] acl (编号)`

> - utbound表示出站
> - inbound表示入站

- 允许源地址的规则
	* `rule (规则id) permit source (ip地址)（子网掩码）[any]`
- 拒接源地址的规则
	* `rule (规则id）deny source (ip地址)（子网掩码）[any]`
- 查看acl（编号)的配置
	* `display acl (编号)`

## 基础过滤工具

- 数据方向调用acl
	* `acl(编号) [inbound//utbound]
- 查看设备控制访问列表
	* `display acl all

# 高级acl规则格式

- 高级acl规则的命令结构
	* `rule (规则id） permit|deny (协议) source (ip地址）（子网掩码) [any]
- 配置(filter-policy）过滤策略调用acl
	* `filter-policy (acl编号) impory`
- 配置前缀列表过滤路由
	* `ip ip-prefix 1 deny (ip地址) (子网掩码) greater-equal (网络位) less-equal（网络位)`

> - greater-equal表示大于等于
> - less-equal表示小于等于

- 配置放行所有其它路由
	* `ip ip-prefix 1 permit 0.0.0.0 0 less-equal 32`
- 配置(filter-policy）过滤策略调前缀列表
	* `filter-policy ip-prefix 1 import`

# SNMP协议基础配置

- 开启agent命令
	* `snmp-agent
- 查看系统信息
	* `display SNMP-agent sys-info`
- 配置snmp版本
	* `snmp-agent sys-info [v1/v2c/v3]`
- 查看agent信息
	* `display snmp-agent sys-info version`

**配置nms管理权限**

- 限制管理设备
	* `rule (规则编号) permit source (ip地址) (子网掩码)`  
- 不允许管理设备
	* `rule (规则编号) deny source (ip地址) (子网掩码)` 
- 配置用户组，用户名，并指定使用acl
	* `snmp-agent usm-user v3 (用户名) (用户组）acl (编号)`
- 查看SNMPv3的用户信息
	* `display snmp-agent sum-user`
- 配置Agent发送trap消息
	* `snmp-agent target-host trap-hostname (网管名) address (目标地址池)`
	* ` udp-port (端口号) trap-paramsname (网管名)`
- 开启设备告警开关
	* `snmp-agent trap enable`
- 设置告警消息列队长度
	* `snmp-agent trap queue-size (参数值)`
- 设置报文消息保存时间
	* `snmp-agent trap life (参数值)`
- 配置管理员联系方式
	* `snmp-agent sys-info contact call admin (电话号码)`
- 查看snmp Agent输出网管
	* `display snmp-agent target-host`

# GRE协议基本配置(tunnel隧道创建)

- 创建隧道接口
	* `interface tunnel (接口号)`
- 指定隧道模式为gre
	* `tunnel-protocol gre`
- 配置接口源地址
	* `source (ip地址)
- 配置 接口目标地址
	* `destination (ip地址)`

> - 一条隧道链路两端接口ip地址必须在同一个网站

# NAT

- 静态nat内部地址池到外部地址池一对一转换
	* `nat static global (外部地址) inside (内部地址)`
- 查看静态ntp配置信息
	* `display nat static`
- 配置nat outbound 外网地址池
	* `nat addres-group 1 (头ip地址) (尾ip地址)`
	* `acl (编号)`
	* `rule (规则编号)permit source (ip地址) (子网掩码)`
- 在接口下将acl与地址池相关联
	* `nat outbounb (acl编号) address-group 1 no-pat`

> 只有acl规定的地址才可以使用地址池进行地址转换

- 查看NAT outbound信息
	* `display nat outbound`
- 在接口上配置Easy-ip特性，使用接口地址为转化后的地址
	* `nat outbound (acl编号)`
- 查看NAT Session的详细信息
	* `display nat session protocol udp verbose`
- 在接口指定内网服务器映射之外网，外网访问内网服务器,指定服务器通信协议类型为tcp
	* `nat server protocol [tcp(协议类型)] global (服务器公网地址）[fcp(服务协议)] inside (服务器私网地址) [ftp(协议端口号，ftp协议默认端口号为21)]`
- 启用nat alc功能
	* `nat alg ftp enable`
- 查看nat server信息
	* `display nat server`

# VRRP

- 在接口穿件vrrp备份组
	* `vrrp vrid (id号) virtual-ip (ip地址)`
- 配置接口vrrp优先级
	* `vrrp vrid (id 号) priority (参数)`
- 查看vrrp信息
	* `display vrrp brief(简要状态)|interface (详细状态)`
- 修改虚拟组为非抢占模式
	* `vrrp vrid (id号) preempt-mode disable`
- 配置上行接口监控
	* `vrrp vrid (id号) track interface(上行接口号) reduced (参数)`
- vrrp接口md5认证
	* `vrrp vrid (id号) authentication-mode md5（密码)`