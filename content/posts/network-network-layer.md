---
title: 计算机网络-网络层复习笔记
date: 2021-06-24 17:13:06
tags: [计算机网络]
categories: [计算机网络,SDUOJ]



---



## 网络层前置知识

网络层提供的服务包括面向连接服务和无连接服务。面向连接服务主要是虚电路网络，无连接服务主要是数据报网络。**虚电路网络和数据报网络是**典型的两种**分组交换网络**。

网络层提供的连接是到机器的。即：机器与机器之间的通信。这不同于传输层提供的进程到进程的通信。

#### 分组交换简介

###### 和报文交换的区别

报文是指源发送的信息的**整体**。例如：电报。

###### 和电路交换的区别

可以实现统计多路复用。同一条链路上可以同时跑来自不同主机的分组。

统计多路复用是指虽然对有某一个时刻（例如某一毫秒）只能是某一个主机在发送或者接收，但是在长时间来看是大家一起在用。

这和电路交换中的FDM、TDM等不同。因为FDM、TDM等是真正实现了多路复用，但是分组交换这里的统计多路复用只是一种统计规律。

电路交换利用了FDM、TDM等多路复用技术，所以虽然频道是独享的，但是带宽却被分得很小。分组交换与之最大的不同在于，分组交换可以利用全部的带宽。

> 统计多路复用的特点在于：按需共享线路。

###### 由于统计多路复用，分组交换显著好于电路交换

虽然电路交换能够利用FDM、TDM等技术实现多路复用，把信道分成很多子信道，但是每个子信道还是只能为一名用户所独占。如果有非常多用户，那么这样去分信道则每个用户几乎得不到肉眼可见的带宽。但是分组交换却可以。因为每个用户在实际使用的过程中，仍然能占有全部带宽。

从另一个角度说，由于子信道都是被独占的，而且必须拆除才能为其他用户所用，所以只要有用户占着茅坑不拉屎，信道就正在被浪费。例如一个人浏览网页，他不可能随时都在交换数据。显然正在浪费。然而实际上，浏览网页只有不到10%的时间是真正在交换数据，所以大部分时间其实可以给别人用。即90%的资源都被浪费了。

而且，分组交换、报文交换都不需要事先**在底层**建立连接和拆除连接，比较方便。

###### 凭借稳定的特点，电路交换也不是一无是处

分组交换适用于突发的数据传输。所谓突发，就是突然想要传输，但是没有连续性。电路交换由于能够独享信道，所以能实现稳定的连续传输。因此电话使用电路交换就很舒服。如果报文交换要做，例如VoIP，则需要保证带宽保障，否则由于大家共享信道，容易卡顿。

此外，分组交换容易产生拥塞（究其原因还是因为统计多路复用），此时需要高层协议加以解决（包括从根本上解决——拥塞控制算法，和从后果上解决——可靠传输算法）。

###### 分组

把报文本身拆解成若干小的数据包。每个小的数据包加上**头部信息**。像这样的，一部分数据加上头部，称为一个**分组**。

###### 重组

分组交换要做的事情，不仅是上述的拆分和封装，还需要到达目的地后，将这些分组**重组，还原**出原来的报文。

###### 额外开销

分组和重组的过程显然需要额外计算。但是这些计算是在源主机和目的主机上进行的，所以**不会**在传输过程中产生开销。（因为到了目的地才有重组的**必要性**。）

###### 存储转发

router收到一个分组，则先把它暂存，先搞明白了它要去哪里，等到那条路可用了，然后再把它发出去。

报文交换和分组交换都使用存储转发的方式。区别仅仅在于它们的单位不同。前者是报文整体，后者是报文拆分后的分组。

###### 传输延时的计算

从某个对象的第一个比特出现在网上，到最后一个比特放到网上，其中经过了多长时间。

所谓传输延时，就是在现有的网络传输速率下，把整个对象都放到网络上所需要的时间。例如把大小为L的分组，整个都放到速率为R的网络上，需要花费时间为L/R.也就是说传输延时是L/R.

###### 由于存储转发，分组交换显著好于报文交换

1. 在速度上，发送方把整个报文发送到网络上，需要传输延时的时间。之后每经过一个路由器，就要再花费这样一个传输延时（因为路由器是先收取下来，再发送。这个发送时间就是传输延时。）因此经过几跳，就要几个这种延时。然而，如果使用分组交换，则只需要一次这种传输延时，这是一种并行的概念。在报文交换上，每个路由器都要经历一次传输延时，但是在分组交换上，可以看作每个路由器都在同时经历它们该经历的延时。（忽略RTT）
2. 在存储空间上，由于报文一般比较大，所以每个路由器都需要较大的存储空间，且这种空间要求是没有上限的。**成本过高**。

###### 跳（hop）

**经过一段链路**称为一跳。与经不经过路由器没有关系。

###### 分组交换的报文交付时间

T=M/R+(h-1)L/R，其中M是报文大小，R是传播速率，L是分组大小，h是跳数。

> 即：分组交换的报文交付时间相当于整个报文的传输延时+最后一个分组被总共h-1个路由器转发的传输延时。

###### 分组交换主机实际传输数据量

实际数据量+分组数量*头部长度

#### 分组交换的网络性能

###### 网线的速率

这种速率成为标称速率，是网线的理想传输速率。一般达不到。

###### 带宽

指的是数字信道所能传输的**最大数据率**。单位bps。

> 为什么这么说？所谓带宽可以看每一个单位时间，这个单位时间内有多少比特在线路上跑？如果把这一单位时间经过的所有比特都收集起来，然后横向排列，就看起来是它们同时通过的一样。它们横向排列的宽度就是带宽。

###### 路由器带来的三种延时

除了路由器把数据放到网上所需的传输延时以外，每个路由器都会为报文的最终交付贡献一部分延时。这些延时主要是路由器中的排队时间。

当输入的速率超过了路由器所能输出的极限速率（极限速率指的是路由器处理并转发分组的最快速度），分组会在路由器中排队。

路由器对分组的处理：**检查是否出错**、检查地址并确定输出链路。

###### 四种延迟和网络延迟 delay/latency 

| 延时                                | 来源   | 因素                 |
| ----------------------------------- | ------ | -------------------- |
| 结点处理延迟 nodal processing delay | 路由器 | 路由器的处理性能     |
| 排队延迟 queuing delay              | 路由器 | 路由器和链路是否拥塞 |
| 传输延迟 transmission delay         | 路由器 | d=L/R                |
| 传播延迟 propagation delay          | 链路   | t=s/v                |

例如铜线中，电信号的传播速度大约是0.7c，即2*10^8m/s.

> 网络延迟=上述四种延迟的加和。

###### 类比：车队上高速

10辆车从一个收费站聚集，准备通过收费站并进入高速公路，到达100km外的终点。

很不巧，收费站今天很拥挤，除了这10辆车以外，还有很多车在收费站排队等着上高速。——排队延迟 queuing delay = 若干s.

终于到我们的车队了。收费站接收每辆车的ETC信号需要1s。——结点处理延迟 nodal processing delay = 1s.

车队要一辆接一辆上高速了。收费站每放行一辆车需要2s（即：数字信道的数据率是0.5bps），我们有10辆车，则传输延迟是2*10=20s.（每辆车代表一个比特）——传输延迟 transmission delay = 20s.

车队终于都上高速了。车队以100km/h的速度行驶，则将会在1hr后到达终点。——传播延迟 propagation delay = 1h.

###### 流量强度（排队延迟的衡量）

定义流量强度traffic intensity = L*a/R，其中a是平均分组到达速率。

> 说人话，流量强度就是单位时间内，带宽的占用率。比如取一秒来看，带宽本来是10Gbps，这一秒路由器有10Gb的数据到达，则流量强度是100%。

流量强度和排队延迟息息相关。当流量强度>1，路由器超出服务能力，排队时间无穷大（总会有永远也处理不到的分组）。当流量强度->1，路由器的排队延迟指数级别上升。

###### 时延带宽积

即传播延迟*带宽。其意义是：以比特为单位的链路长度，或者说：

> 整个链路能容纳多少比特。

###### 丢包率

为什么会丢包：如果路由器的缓冲区满了，则路由器收到新的包就直接丢弃。

###### 吞吐量

> 吞吐量实际上就是**数据率**。即经过某链路或某实体的数据率。

对于**端到端**的吞吐量，其等于经过的链路上的各种设施的吞吐量的最小值，有最小值的这个链路又称为**瓶颈链路**。

> 一般情况下，瓶颈链路都是端接入链路，而不是中间的核心网。

## 虚电路网络 Virtual Circuits

虚电路网络是一种利用电路交换的思想构成的分组交换网络。它像电路交换那样，建立一条数据通路（这条通路由网络层设备比如路由器进行组织）（这是电路交换的特点），但是却能利用链路的全部带宽（这是分组交换的特点）。

虚电路网络是面向连接的。这与数据报网络相反。网络层提供两种网络，一种就是这种面向连接的，另一种就是无连接的数据报网络。

#### 好处：可以进行资源预分配和服务性能保障

虚电路网络是利用电路交换思想构建的分组交换网络，所以它集电路交换和分组交换的优点于一身。

VC能够提前分配好网络资源（例如带宽和缓存），这使得服务性能可以被预期。

#### 面向连接的通信过程

呼叫建立，数据传输，拆除呼叫。

#### 连接的维护

电路经过沿途的路由器随时维护该连接。

#### VCID Virtual Circuits Identification 虚电路标识

每个在VC上走的分组都把VCID作为标识，<u>**它直接取代了目的IP**</u>。即：走VC的分组并不包含目的地址，而是**只需要**携带VCID即可成功到达目的地。

同一个分组在不同的链路上的VCID一般是不同的。每次这个分组经过路由器，路由器**都会更新**其VCID，以便被下一段链路的路由器所识别，路由到正确方向。

#### VC转发路由表

| 输入接口 | 输入VCID | 输出接口 | 输出VCID |
| -------- | -------- | -------- | -------- |
| 1        | 12       | 3        | 22       |
| ...      | ...      | ...      | ...      |

当一个VCID为12的分组从1号接口来到路由器，路由器就会更新这个分组的VCID为22，并从3号接口转发出去。

即路由器的作用：

1. 维护VC转发表
2. 转发分组
3. 更新（维护）分组的VCID

如果要**建立**新的VC，则在上表中增加一个项目；如果**拆除**则删除一个项目。

#### 虚电路信令协议 Signaling Protocol

###### 应用

该协议用于ATM等网络。

###### VC建立第一步：通过路由进行呼叫

在建立网络之前，呼叫建立的信号需要通过路由到达接收方。所以注意：

> VC网络仍然是需要路由的，只不过只需要在呼叫建立的时候路由**一次**，之后**就一直用**这条探明的路就行了。

所以该呼叫有两个作用：

1. 对目的主机发出建立VC的请求
2. **探路**

###### VC建立第二步：逆路由确认

当目的主机收到呼叫建立，并同意，**会逆着**上述确定好的路由发送一个**确认信息**。

该确认信息有两个作用：

1. 通知源主机，VC建立成功
2. **通知沿途的路由器，开始维护这条VC**

## 数据报网络 Datagram Networks

数据报网络是无连接的，因此每个分组需要携带目的地址来确保送达目的地。路由器的转发表根据目的地址来进行转发，而不是刚才的VCID。

由于数据报网络是无连接的，所以每个分组都是独立选路的。即：这个数据报和上一个数据报虽然都是同一个主机发出的，但是实际经过的链路可能**不同**。这是和VC最大的不同。出现这种情况的原因是：在本次发送前，某些路由器的转发表**更新了**。而且，由于选择了不同的链路，传输速率显然不一定相同，所以包的到达**顺序**无法保证。

#### 路由转发表和最长前缀匹配原则

由于IPv4地址有40多亿种，所以记录准确的IP转发表是不可能的。且没有必要：因为这个路由器并没有直接跟那个IP地址连接。

因此，可以记录IP前缀。如果来了一个IP，就寻找转发表中匹配的**最长前缀**（因为匹配越长，地址就越具体。）

之所以能这么干，是因为IP地址是有逻辑指向性的，你能根据IP前缀查到这个IP在世界上哪个位置，**但是没有人知道**某个MAC地址在世界上哪个位置。

| 目的地址范围                        | 输出链路接口 |
| ----------------------------------- | ------------ |
| 11001000 00010111 00011XXX XXXXXXXX | 0            |
| 11001000 00010111 00011000 XXXXXXXX | 1            |
| 11001000 00010111 00011XXX XXXXXXXX | 2            |
| 其他（到默认端口）                  | 3            |

由于遵循最长前缀匹配原则，所以如果IPv4地址：11001000 00010111 00011000 11101100来了，则会转发到2，而不是1.

## DN和VC的对比

Internet就是一个DN，ATM网络就是一个VC。

| Internet                 | ATM                          |
| ------------------------ | ---------------------------- |
| 端系统必须智能，例如电脑 | 端系统可以很傻，比如电话     |
| 简化网络，复杂边缘       | 简化边缘，复杂网络           |
| 服务不稳定，但是弹性     | 服务有保障，但是要求严格连接 |
| 能互联各种各样的网络     | 必须是能建立VC的网络         |

## Internet网络层

> Internet网络层主要由路由协议、IP、ICMP协议组成。

路由协议算出路由转发表，并由IP协议规定寻址方式，完成数据的正确传输。

IP协议还规定数据报的格式、分组处理的规定。

ICMP提供了路由器的差错报告和信息报告。

## IP协议 Internet Protocol

#### IPv4数据报

| 字段         | 含义                                                         | 常用值          |
| ------------ | ------------------------------------------------------------ | --------------- |
| 版本号       | 直接填4                                                      | 4H              |
| 首部长度     | 因为此字段长度为4，故最大值是15.但是IP首部的固定长度部分已经是20了，所以这里的长度单位是**4B**。对于无变长部分的IP头，这里填5. | 5H              |
| 服务类型TOS  | 用于QoS。即：希望网络为不同TOS的数据报提供不一样的QoS。如果网络不提供这种服务，则这个字段是没有用的。比如**路由器**就不提供这种服务。 | 00H             |
| 总长度       | 整个IP数据报的长度。16位二进制数即65535最大值，则最大可封装数据是65535-20=65515B. | /               |
| TTL          | 剩余可通过的跳数或时间。如果某路由器转发时，TTL降低为0，则被直接丢弃。同时，发送ICMP报文。 | /               |
| 协议         | 指出这个IP数据报封装了什么协议的数据。                       | 6：TCP，17：UDP |
| 首部checksum | 每次路由器收到数据报，都会检验其校验和。在计算校验和时，此字段应当置0.其计算方法同TCP/UDP.由于每次路由器转发的时候，TTL会变化，所以每次路由器都要重新计算其校验和并更新。**因此，校验和是逐跳计算，逐跳检验的。** | /               |
| 选项         | 长度在1-40B之间。携带额外信息。                              | /               |
| 填充         | 把整个**头部**补充到4字节的整数倍。长度在0-3B之间。**因为头部长度字段是以4B为单位**，所以这里也要补充到4的倍数，才能让头部长度正确填写。正是因为要往4的倍数上面补充，所以取了以4为模的4个数字作为其长度范围。 | /               |

#### IP数据报分片

###### 为什么要分片

因为IP数据报最终还是要放到链路层的帧中进行传输。最大帧长是有限制的，例如Ethernet可以最多封装1500B(1.5KB)长度的数据。

###### 链路最大传输单元 MTU

某种链路的最大帧长可以**封装**的最大的数据**的长度**。

###### 谁来分片IP数据报

当路由器要把IP数据报转向一个MTU小于它的数据报总长度的链路时，此路由器将会对IP数据报进行分片。

###### 谁来恢复分片了的IP数据报

> 众所周知，路由器只管分，不管装。

当IP分片逐渐到达目的主机，目的主机对这些分片进行重组，获得完整IP数据报。

因为如果数据报通过了某小MTU链路，路由器负责重组的话，一是要等待所有的分片都到达，二是如果后面还有MTU较小的链路，则又要分片。前者非常浪费时间，后者非常浪费资源。

###### 如果某些片丢失怎么办

这些片都是独立地作为IP数据报到达目的主机。目的主机收到分片的IP数据报以后，会等待一段时间，用于等待其他片的到来。如果等待完成后，其他片还没到，则说明丢失，目的主机会**丢弃**已经到达的其他所有片。

###### 分片相关字段

| 字段                           | 含义                                                         | 常用值 |
| ------------------------------ | ------------------------------------------------------------ | ------ |
| 不要分片（DF，Don't Fragment） | 是否允许此数据报被分片。如果不允许，则向较小的MTU链路转发时，路由器会直接扔掉这个数据报，然后发送ICMP报文。 | 0      |
| 还有分片(MF，More Fragment)    | 如果是1，则说明后面还有其他分片，否则这个就是最后一片（或者没有分片）。 | /      |
| 标识（ID）                     | 用于标识一个原始的分组。每次有新分组，要装入IP数据报进行发送，计数器+1，作为这个新分组的ID。每次分片的时候，此字段都被复制，并原样填入。 | /      |
| 片偏移                         | （**以8B为单位**）此分片相对于原分组的偏移量。因为此字段的长度为12位，即4096种状态，而4096*8=65536/2，所以要以8字节为单位。（因为如果要对65515，即最大长度的数据进行分片，一定是分成相等两片，每片长度为32757，所以完全够用了。） | 0      |
| 总长度                         | 每次分片的时候，总长度都被更新为这个分片的总长度。因为总长度字段的含义是当前IP数据报的长度，而每个分片作为了单独的数据报。 | /      |

> 如果片偏移=0且MF=0，则说明此数据报就是封装了原分组。

###### 分片的数学特点

1. 除最后一片以外，其他片都必然是当前MTU所允许的**可能的**最大分片
2. 每个分片封装的数据长度是8的倍数，即长度为MTU-头部长度20，再除以8的下取整，再乘8.
3. 需要的总片数，即原分组总长度L-头部长度20再除以上述计算的每片的数据长度然后上取整（因为上述"1"的特点。）
4. 片偏移量**字段**取值，即(d/8)*(i-1)，即第一片是0，再往后是片长度的等差数列。这里除以8是因为以8B为单位。
5. 总长度**字段**取值，除了最后一片，都是d；最后一片是L-(n-1)d，即原分组总长度减去前面n-1个最大分片长度。
6. MF标志位**字段**取值，只有最后一片是0.

#### IP编址

###### 对谁编址？

对IP网络中，实现网络层功能的网络接口Interface进行编址。如果这些接口不实现网络层功能，则IP不会对他们进行编址。

###### 机器网络接口的数量

一般的主机一般是1-2个；但是路由器为了实现功能，可以有很多个。

###### IP地址的分配

在分配IP地址的时候，不能随便分配。一定要保证处在同一区域内的主机被分配到的IP地址的若干高位完全相同，这样我们对比前面的路由表的情况，可以看出只有这样分配才能让路由器知道怎么转发。

###### IP子网 Subnet

把具有相同高位（网络号）的网络接口称为这些**接口**在同一个IP子网中。

> 子网的特点是：这些网络接口可以直接物理相连，不能跨越网络层及以上的设备例如路由器。

因此，观察是否是IP子网，一来观察是否高位一致，二来把网络层及以上设备（例如路由器）都删除，看谁跟谁还在连接。

#### 有类编址

> 采用二分的思想。

###### ABC类地址的数学规律

1. 每类地址的总数是逐个除2.因为使用了二分的思想。
2. 每类地址的网络号长度是**8的整数倍**。
3. A类地址虽然比B类地址的数量多，但是A类地址能表示的网络数量比B类少。这是因为A类地址的主机号太大了。
4. 所有地址都是从其“基地址“全0增加到全255.例如：128.0.0.0-191.255.255.255，且全255的这个地址的最高八位是下一个基地址最高八位-1.因此，求某类地址的范围，只需要这一类地址确定“基地址”的最高八位即可确定这一类地址的整个范围。“基地址”的高八位如何确定呢？由于二分，它们是以+128，+64，+32的方式增长的。直接加即可。

###### 五类地址的基地址高八位规律

| 类别 | 基地址高八位 | 较上一个相比 |
| ---- | ------------ | ------------ |
| A    | 0            | /            |
| B    | 128          | +128         |
| C    | 192          | +64          |
| D    | 224          | +32          |
| E    | 240          | +16          |
| 结束 | /            | +16          |

###### 基地址法使用实例

由于C的基地址是192，所以C的第一个地址是192.0.0.0；

由于D的基地址是224，所以C的最后一个地址的高八位是224-1=223，所以C的最后一个地址是223.255.255.255.

###### 特殊地址

| IP地址                      | 作为SIP | 作为DIP | 用途                                                         |
| --------------------------- | ------- | ------- | ------------------------------------------------------------ |
| 0.0.0.0                     | 1       | -       | 当一个主机不知道自己的IP，却还要发送IP数据报时使用。         |
| 255.255.255.255             | -       | 1       | 受限广播地址（路由器不转发）                                 |
| 127.0.0.1-127.255.255.254   | 1       | 1       | 本地环回，即此数据报不会离开本机。它到达本机网络层又回来了。C类中，把去掉全0和全1的网络号为127的子网拿出来做这件事。 |
| 10.0.0.0-10.255.255.255     | 1       | 1       | A类保留的，网络号为10的内网                                  |
| 172.16.0.0-172.31.255.255   | 1       | 1       | B类保留的，网络号为172.16-172.31的内网                       |
| 192.168.0.0-192.168.255.255 | 1       | 1       | C类保留的，网络号为192.168.0-192.168.255的内网               |
| 全0网络号+正常主机号        | -       | 1       | 本IP子网内的网络号为此的主机                                 |
| 正常网络号+全0主机号        | -       | -       | 代表一个IP子网                                               |
| 正常网络号+全1主机号        | -       | 1       | 不受限的广播地址（直接广播地址），直接对此网络号的IP子网进行广播 |

###### 点分十进制网络号

由于网络号的长度都是8的倍数，点分十进制也是以8个bits为一组进行划分，所以可以看出：

| 类别 | 网络号格式                              |
| ---- | --------------------------------------- |
| A    | XXX                                     |
| B    | XXX.XXX                                 |
| C    | XXX.XXX.XXX                             |
| D    | XXX.XXX.XXX.XXX（当然也可以看成主机号） |
| E    | XXX.XXX.XXX.XXX（当然也可以看成主机号） |

> 所以可以看出，所谓A、B、C、D、E类地址，只是根据网络号的实际长度占点分的多少个段来进行的划分。

###### 记住ABC类的网络基地址！！！！

> 0，128.0，192.0.0！
>
> 0，128.0，192.0.0！
>
> 0，128.0，192.0.0！

###### 一个类型的IP编址可以带多少主机？

去掉全0、全1即可。

#### 子网划分

好处：可以利用路由器隔离广播域，有效降低网络流量。（目的地为某子网的数据报不需要发到其他子网上去了。）

###### 子网掩码

把除了实际表示主机的二进制以外的所有位填1，就得到。

| 子网掩码      | 说明                                                         |
| ------------- | ------------------------------------------------------------ |
| 255.0.0.0     | A类IP地址的默认子网掩码                                      |
| 255.255.0.0   | B类IP地址的默认子网掩码                                      |
| 255.255.255.0 | C类IP地址的默认子网掩码                                      |
| 255.255.254.0 | **借用7位**进行子网划分的B类IP地址的子网掩码 11111111 11111111 11111110 00000000 |

###### 路由器利用子网掩码进行转发

路由器将子网掩码和目的IP进行按位与运算，**可以直接得到网络号+子网号的那些二进制位**，然后根据这个进行转发即可。

###### 子网地值范围和可分配地值范围

前者是除了网络号+子网号之外的位，从全0到全1；后者是前者除去主机号为全0和全1的。

###### 划分子网的特殊地址

和上述的有类编址的特殊IP地址的规则**完全一致**。

###### 子网划分会造成IP地址的浪费

划分出来了多少子网，就出来了多少主机号为全0和全1的IP地址。这些地址都不能被分配。

> 虽然造成浪费，但是子网划分带来的性能提升更为重要。所以子网划分牛逼。

#### 无类域间路由 CIDR，Classless InterDomain Routing

CIDR带来了无类别编址。不再区分ABCDE类地址，不再区分网络号和子网号，而是把网络号和子网号统称为“IP前缀”，方便子网划分。

###### 无类地址

形如a.b.c.d/x的IP地址称为无类地址。其中x是IP前缀的长度。x的**单位是bit**。

> 这种写法，可以同时给出地址和其子网掩码，非常方便。

###### 可提升IPv4地址的分配效率

例如，可以不使用B网，而是把很多个C网利用CIDR构成一个大的子网，还可以灵活地对某个浪费的超额的网络进行拆分。

###### 可提升路由效率，构成“超网”，从而实现路由聚合

所谓“超网”，就是一个更大的，由很多子网通过域间路由连接成的大网。

> 这样能在路由器的路由表中，把很多个转发方向融合成同一个，即这个“超网”的地址，从而提升转发效率。这种方式称为**路由聚合**。

这使得路由表的体积大大降低。

~~路由聚合，即某年CSPT3“CIDR合并”。~~

例如一个路由表：

| 目的网络     | 转发到接口 |
| ------------ | ---------- |
| 223.1.0.0/23 | 2          |
| 223.1.2.0/24 | 2          |
| 223.1.3.0/24 | 2          |
| Internet     | 1          |

则通过CIDR合并（路由聚合），可以直接改为：

| 目的网络     | 转发到接口 |
| ------------ | ---------- |
| 223.1.0.0/22 | 2          |
| Internet     | 1          |

在互联网的主干核心网络上，通过路由聚合，路由表的体积可以减少40%-60%。这非常有效。

###### 避免路由黑洞

所谓路由黑洞，就是数据报经过错误的转发，导致找不到目的地。

假设ISP1持有如下IP：200.23.16.0/23，200.23.20.0/23，200.23.30.0/23，则从核心网接入回该ISP的路由器上，通过CIDR合并，可以记录200.23.16.0/20.

假设ISP2持有如下IP：200.23.18.0/23，且其持有的大部分IP都是199.31.0.0开头的，所以从核心网接入回该ISP的路由器上，通过CIDR合并，可以记录199.31.0.0/16.

但是如果这两个ISP都接入同一个路由器，则本来要发给ISP2的网络200.23.18.0/23的数据报会错误地进入ISP1下面。因此，ISP2在路由器处的记录不应该是“199.31.0.0/16，则发给ISP2“，而是应该为“199.31.0.0/16或者200.23.18.0/23，则发给ISP2“。此时，虽然该路由器中同时存在“200.23.16.0/20，则发给ISP1”，**但是根据最长前缀匹配原则，这个数据报可以正确地被转发到ISP2的网络下了。**

#### 网关

###### 概念：默认网关

> 网关就是路由器的某个**接口**。
>
> （因为IP地址是给网络接口分配的，一个IP地址对应一个网络接口。）

对于子网中的主机，它应该把自己的IP数据报发送给哪个网络接口连入服务器进而与外界通信，这个路由器上的这个网络接口就称为其“默认网关”。可见，对于这个子网中的每个主机，都应该填写这个路由器的这个接口的地址。除非，这个子网与多个这种接口相连，通过任意一个这样的接口都可以完成通信，那么这种情况下，默认网关是可以任意从这些接口中选择的。

#### 动态主机配置协议 DHCP Dynamic Host Configuration Protocol

> DHCP服务器可以为主机动态分配IP地址、子网掩码、默认网关、DNS服务器。
>
> 即：Windows上面的网络设置，那整个窗口的选项都可以由DHCP服务器自动帮你填写。

###### 即插即用

只要客户机运行DHCP协议，并且填写了对应的DHCP服务器的IP地址，就可以自动完成一切功能。

###### 允许地址重用、续租

主机可以获取任意的可用的IP地址进行使用。并在到期前提出续租。

###### 支持移动用户加入网络

如果你是新来的，没关系。找DHCP服务器帮你安排一切。

###### 使用UDP协议传输

它的报文封装在UDP段中。（所谓段，就是分组前面加个传输层DU的头。）

###### DHCP是应用层协议。

它的报文封装在UDP段中。（所谓段，就是分组前面加个传输层DU的头。）

| 报文          | 用途                                                         |
| ------------- | ------------------------------------------------------------ |
| DHCP Discover | 发现DHCP服务器，等待offer                                    |
| DHCP Offer    | 某个DHCP服务器愿意响应，并给出拟分配IP地址                   |
| DHCP Request  | 客户机响应Offer，并向所有DHCP服务器炫耀，让其他DHCP服务器及时收回预分配的IP |
| DHCP ACK      | 服务器响应客户机的响应，当客户机收到此ACK，客户机就真正拥有了IP |

###### 源IP目的IP的特点

特点1：无论是源主机还是目的主机，全程都使用受限广播地址作为目的IP

特点2：在上述四个报文中，客户机全程都使用0.0.0.0作为源IP

| 主机   | 其发出报文的目的IP | 其发出报文的源IP |
| ------ | ------------------ | ---------------- |
| 客户机 | 255.255.255.255:67 | 0.0.0.0:68       |
| 服务器 | 255.255.255.255:68 | 服务器地址:67    |

#### 网络地址转换 NAT

内网IP，即私有IP，是不允许出现在公网上的。因此如果子网中某个设备要通过内网IP与外界通信，就需要NAT。

对于一个子网，可以只需要一个对外的公网IP。子网与外界的全部通信都可以只被这一个公网IP所代表。

###### 节约IP地址

一个IP地址都能代表一个子网了.

###### 安全性高

外界**无法直接寻址到**这个子网内的一个主机。

###### 内外网分离性好

无论是内网拓扑变更，还是外网拓扑变更，相互不影响。类似于一层封装，外界不需要得知内部发生了什么，内部出现什么变化也只需保持外特性。

###### 带载能力强

因为端口号是16bits长度，所以在仅有一个公网IP的情况下，它仍能支持65535个不同的并行转换任务。

###### 嵌入路由器所带来的争议

> 现在的NAT一般嵌入在路由器中。

因为路由器是网络层设备，但是NAT却擅自拆开传输层的TPDU，并进行致命性修改，这违背了端到端原则。这使得本该不用考虑路由器的传输层也开始得考虑了！例如P2P应用就受到很大影响。

###### NAT工作原理和转换表

| WAN端地址        | LAN端地址     |
| ---------------- | ------------- |
| 138.76.29.7:5001 | 10.0.0.1:3345 |
| ...              | ...           |

经过NAT的数据报都被拆开，就连传输层的TPDU都拆开来，然后把WAN的地址和端口号和LAN的地址和端口号分别互换即可。

###### NAT穿透

正由于外网无法直接对使用了NAT的内网进行寻址，如果在内网开一个服务器想要让外网访问，都不行。则可以静态配置NAT转换规则，也可以使用UPnP(Universal Plug and Play)协议自动完成上述静态配置，也可以利用中继服务器（即内网的用户通过NAT进入外网然后与处在外网的中继服务器建立连接，外网的用户也与此中继服务器建立连接，则处在外网的这个用户就可以与内网的这个用户通信了。）

#### ICMP协议 Internet Control Message Protocol

负责差错报告和网络探询。ICMP报文是**封装到IP数据报**中进行传输的。

###### 差错报告5类

| 报文       | 解释                                                         |
| ---------- | ------------------------------------------------------------ |
| 目的不可达 | 数据报**已经到达了**目的主机或路由器，但是**无法交付**，则会丢弃此数据报，并且发送此报文 |
| 源抑制     | 当路由器发现自己的缓存要满，则向回发送此报文以控制拥塞。（现实网络不采用） |
| 超时       | 当TTL降低为0，路由器丢弃数据报，并发送此报文到**源主机**     |
| 参数问题   | 如果发现数据报的某些字段存在问题，则路由器丢弃数据报，并发送此报文 |
| 重定向     | 如果某路由器发现源主机给自己的数据报不应该让自己转发出去，而是还有其他路由器可以走，那么给源主机发送此报文，让他把数据报重新定向到其他路由器。 |

###### 网络探询2类

| 报文             | 解释             |
| ---------------- | ---------------- |
| 回显请求和应答   | 探测网络是否通畅 |
| 时间辍请求和应答 | 实现时间辍的获取 |

###### ICMP报文格式

关键部分是类型和编码。ICMP通过类型和编码的组合来表示特定的含义。例如：

| 类型 | 编码 | 含义           |
| ---- | ---- | -------------- |
| 0    | 0    | 回显应答       |
| 3    | 1    | 目的主机不可达 |
| 3    | 3    | 目的端口不可达 |
| 11   | 0    | TTL超时        |

###### 不发送ICMP差错报文的情况

1. 如果**封装了ICMP报文的IP数据报**出错，则不针对此数据报发送ICMP差错报文。
2. 如果出错的IP数据报是分片的，则只针对**第一片**发送ICMP差错报文。
3. 对有**特殊地址**的IP数据报不发送ICMP差错报文。例如：广播、环回等。

###### ICMP报文格式

| 字段     | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| 类型     | 见上                                                         |
| 代码     | 见上                                                         |
| checksum | 计算方法同TCP、UDP、IP，负责校验整个ICMP报文                 |
| 可变部分 | 4字节。具体内容安排取决于ICMP报文的类型。                    |
| 数据部分 | 具体长度取决于类型。比如在实验课上，我观察到回显请求报文甚至长到要分片了。 |

###### ICMP差错报文的数据部分

会把出现差错的IP数据报的首部和其数据部分的前8个字节拿出来，作为ICMP差错报文的数据部分。

如果这个IP数据报里面带了UDP或TCP段，那么8个字节直接就是UDP头长度。且对于TCP，这8个字节能包含源端口和目的端口也绰绰有裕。

> 所以这么封装，可以让这个ICMP报文带上IP首部关键信息和其中高层协议的关键信息。

###### ICMP应用：路径探测

即：找出从源主机到目的主机经过了哪些路由器。

可以发送n组UDP，且目的端口号填写必然没有被使用的。第1组发送TTL=1的，第2组发送TTL=2的，以此类推。这样，沿途的第i个路由器会在第i组UDP段到达时返回ICMP TTL超时报文。目的主机收到这个ICMP报文的时候如果记录RTT就能知道延时多少。这些ICMP报文都会携带路由器的名称和IP地址。等到最后到达了目的主机，目的主机会发送ICMP 目的端口不可达报文。这样，整个路径就都被源主机记录下来了。

#### IPv6

提供更大地址空间、为**快速处理转发**、QoS做出优化的新一代IP。

###### 不再支持分片

IPv6不支持分片。如果一个IPv6数据报走到了MTU过小的链路，则由ICMPv6发送一个“Packet too big”报文。然后，源主机调整发送数据量重新发送。

> 在IPv6下，路由器不会主动对报文进行分片。分片都是由源主机主动进行。

###### 定长的基本首部

IPv6的首部是定长的，称为基本首部。如果要添加其他选项可以在基本首部后面紧接着追加扩展首部。整个数据报看起来就像火车一样，基本首部是车头，后面跟着一溜的扩展首部车厢，最后是真正的数据。IPv6规定了6种不同类型的扩展首部。注意，扩展首部不算在首部里面，而是算在数据部分。

###### 更大的地址空间

地址长度由原来的32bits变成现在的128bits。

###### 为提升处理转发速度作出的改变

不再使用校验和——直接把“每次转发都得重新计算校验和”这步省略。

不再使用可变长度选项字段——首部变成定长40B，扩展首部放到数据部分——路由器只需要固定地处理定长首部即可，其他的扩展首部**一般被忽略**，因为他们只是数据。

###### 基本头部

| 字段       | 解释                                                         | 常用值 |
| ---------- | ------------------------------------------------------------ | ------ |
| 版本       | 和IPv4在结构上唯一相同的字段。都是4个比特。可以说是在结构上向下兼容。 | 6      |
| 优先级     | QoS.以便对不同的IPv6数据报区分优先级。                       | /      |
| 流标签     | 流的定义：一系列从源主机到目的主机的数据报。这一系列数据报的这一字段**相同**。 | /      |
| 载荷长度   | 包括扩展首部的长度和数据的长度。以B为单位。                  | /      |
| 下一个首部 | 对于基本头部，如果有扩展首部，则指向第一个扩展首部；对于扩展首部，如果下面还有扩展首部，则指向下一个扩展首部；如果是最后一个扩展首部或者没有扩展首部，则指向数据部分中，高层协议的首部，比如TCP头。 | /      |
| 跳步限制   | 即TTL。                                                      | /      |

###### 地址表示形式

`一般形式` 每16位一组，利用16进制表示。共分成8组。中间用“:”间隔。

`压缩形式` 如果出现**连续的**多个组都是0，则将形如“1:0:0:0:0:0:1”的地址写为"1::1"。但是在同一个IPv6地址下，如果出现两个或以上这种情况则**只能对其中一块**进行压缩。否则显然会有歧义：你不知道这里面压缩了多少位0.

`IPv4嵌入模式` 众所周知，IPv6的十进制形式由8个16位构成。将1段到5段写全0，倒数第3个段写全1，将倒数两个段构成的32位写入IPv4地址，即可兼容IPv4.例如：0:0:0:0:0:FFFF:192.168.1.100，然后可以压缩成::FFFF:192.168.1.100

###### IPv6 地址前缀

IPv6不使用子网掩码。所有地址**都沿用CIDR**的前缀书写方式。例如:2002:43c:476b::/48.

###### IPv6 URL写法

由于存在':'，为了避免和协议类型、端口号等引起**歧义**，在URL中应该把IPv6地址用中括号**括**起来。例如：http://[3FFE::1:800:200C:417A]:8000

###### IPv6 Anycast 任意播

仍然是对一个组进行的。但是是把消息给这个组离源主机最近的哪个主机。

这种方式是IPv6**新加入**的。

#### ICMPv6

1. 增加若干新的报文，例如“Packet too big”（告诉源主机进行分片）。
2. 集成IGMP协议的功能。

#### 隧道技术

用于兼容两种IP协议，从而实现过渡。从英国到荷兰需要走海下的火车隧道。一家人开车旅游，要度过海峡则把车开上火车，让火车运载过去。这就是隧道技术。

假设有两个IPv6网络中间由IPv4网络相连，则v6数据报要通过v4网络的时候，路由器把v6数据报作为上层协议封装进v4数据报。然后通过之后再取出来，再在v6网络上跑。

## 路由算法

#### 静态路由和动态路由

静态路由就是人工指定，哪些数据报应该往哪个方向发送。一般情况下，由于是人类智慧的结晶，所以静态路由的优先级高于动态路由。静态路由的问题在于，只要人不去干预，就不会作出及时调整。比如哪里拥塞了，哪里坏掉了，它反应不过来。

动态路由能自动地很快地更新路由信息。能完全解决静态路由的缺点。

#### 全局信息路由算法和分散信息路由算法

全局信息算法需要完整的整张网络的拓扑信息和费用信息。例如链路状态算法。

分散信息算法只需要与该路由器物理邻接的拓扑信息（也就是邻接点有哪些）和费用信息。通过邻居之间的信息交换即可计算出路由表。例如距离向量算法。

#### 链路状态路由算法（基于Dijkstra）

由于Dijkstra算法的前提是，整张图必须建立起来，才能在上面跑这个算法，所以链路状态路由是一个**全局信息路由**的算法。

###### 如何获得全局信息？

定义“链路状态分组”为：包含此路由器的所有邻接路由器的IP地址和边权。即：假设整张图用一个邻接链表存储，则这个所谓“链路状态分组”就是把属于自己的**整个邻接链表**放进去。

可以通过“**链路状态广播**”来让任何一个路由器都获取全局信息，进而在自己那里都建立一张完整的图。链路状态广播就是把自己的链路状态分组广播出去，具体地说：

> **每过一段时间**，每个路由器都要构造一个链路状态分组，并且**泛洪**这个分组。

###### 获得全局信息后，可以跑Dijkstra算法了

获取了这些信息，直接就能跑出来到各个点的最短路。**整张路由表瞬间形成。**

###### 路由表的同步更新

由于每过一段时间，每个路由器都会进行链路状态广播，广播完后立即跑算法，**所以每过一段时间**，每个路由器中的表都会被更新一次。

###### 需要注意：在一般情况下，网络这张图是有向图。

> 因此，从A到B的花费和从B到达A的花费是不相等的。

就像从宿舍到ZSY上课和从ZSY上完课回宿舍。这两个用的时间不一样。

###### 振荡问题 Oscillation

所谓振荡，就是网络中各路由器的路由表被**高频率**更新。

简单说，假设链路花费以流量为参照，如果某一时刻某链路上的流量过大，则更新路由表后，处在这条链路两端的路由器可能就不会把数据报发送到这条链路上。如果它们处在一个环上，且这个环是强连通的，那么相当于这个路由器正在把这个数据报往**相反的方向**转发。

这么一变化，这个强连通的环上就可能会有新的流量较大链路了，上述问题又会出现，数据报的发送方向又会被反转一次，以此类推。

> 如果一个数据报每次都恰好在路由表更新，转发方向反转的时刻到达这种路由器，则这个包将会一直在这个强连通的环里面左右横跳，最终由于TTL降低到0而无法到达目的地。

> 总而言之，这种问题的出现原因是，路由器都尽量选择更好的线路，但是流量必然会堆积在某条线路上，如果这条线路没有流量，那么另一条就会有很多流量。巧就巧在这是一个环。方向的改变意味着数据报沿着环旋转的方向改变，于是左右横跳。

这是动态路由算法的弊病。因为对于单点来说，这样的动态更新能保证局部最优解，但是实际上的链路花费是全局的路由器共同决定的，于是会在全局最优解的确定上左右横跳。

#### 距离向量路由算法 Distance Vector, DV（基于Bellman-Ford）

###### 距离向量

定义距离向量Dx，表示从点x到图中所有点的距离构成的向量。每个距离填入一个分量。

###### 利用 Bellman-Ford 动态规划状态转移方程更新本路由器的距离向量

```c++
路由器x在某一时刻更新自己的距离向量：
//定义距离向量Dx，代表点x的距离向量。
//其中，Dx[i]代表x点到i号路由器的距离。
for(router z:Dx){
//对向量的每个分量重新计算其值
    for(router n:x的邻居路由器){
    //利用动态规划求解新值
        Dx[z]=min(Dx[z],Dn[z]+cost[x][n]);
    }
}
```

一句话概括，即，每次本路由器更新自己的距离向量的时候，对每个分量进行一轮动态规划。动态规划转移方程就是到终点的距离=从自己出发，再经过自己的邻居，再到终点，对于所有邻居情况下的路径，最短的那个。这就是Bellman-Ford算法思想。

> 显然，由于每轮动态规划只需要邻居的相关信息，所以这是一个**分散信息的路由算法**。

###### 每个路由器应当维护自己的和所有其邻居的距离向量

继续观察动态规划状态转移方程，会发现方程由

```c++
Dx[z],Dn[z],cost[x][n]
```

这三个要素组成。也就是自己的距离向量、**所有邻居的距离向量和自己到所有邻居的cost**。

> 因此，每个路由器不仅要维护自己的距离向量，还**得保存自己所有邻居的距离向量副本**。为了做到这一点，只需要所有邻居都保持对其邻居**进行 自己的距离向量的广播** 即可。

###### 逐渐松弛，最终收敛于最优解

在一开始的时候，根据SPFA思想，显然并没有有效地松弛多少边，所以距离向量一定不是真正的最短路径。当有效地松弛了n-1轮后，距离向量真的就变成最短路径距离向量了。

###### 距离向量通报 DV Advertisement

当某路由器更新了其距离向量，一旦更新完成，发现新的距离向量和其以前的向量不同了，就会立即通知其邻居，**把新的距离向量给他们**。

###### 在什么情况下，路由器会主动重新计算路由表（距离向量）

当其收到邻居的全新的距离向量时，或者自己周围的链路费用更新时。（即动态规划状态转移方程的要素发生改变，就需要重新动态规划，更新自己的距离向量。）

###### 有限状态机

每个服务器都有如下三种状态：

| 状态         | 解释                                                         |
| ------------ | ------------------------------------------------------------ |
| 等待         | 等待动态规划状态转移方程那俩要素发生改变                     |
| 重新计算     | 重新执行动态规划，计算新的距离向量                           |
| 距离向量通报 | 如果新的距离向量发生改变，则通告所有其邻居，然后自己回到等待状态。 |



###### 路由表的异步更新

> DV算法和LS算法很大的`区别`在于，它的距离向量，或者说是路由表是异步更新的。

每个路由器都各自管各自的，只要其路由表值得被更新，那么就去更新它。然后可能隔壁路由器发现刚才这个路由器的向量发生变化了，然后这个隔壁路由器又会更新。可见，虽然这两个都更新了，但是不存在同时更新的情况。这与LS算法的不同之处在于：LS泛洪是同一时间所有路由器都能接收到的。所有路由器都在同一时间去更新。

###### 每个路由器维护的距离向量表

是一个n乘n的矩阵。每个行向量都是一个距离向量。

###### 距离向量表的初始化

`初始化其他距离向量` 刚开始的时候，没有来自邻居的向量通报，所以除了自身行向量以外，其他向量都是以无穷为元素（初始化为无穷）。

`初始化自身的距离向量` 刚开始的时候，还不能进行动态规划，所以初始自身行向量内的元素，只要是邻接的就填cost，只要是不邻接的就填无穷。

###### 从画图中，看出来的一些特点

1. 每次通报，能够让直接相邻的路由器，它们的table都变得完全相同。（虽然通报者都是只通报自己的那个距离向量，但是所有自己向量变化的路由器都通报其自己的新向量，相当于大家一起同步更新变化的向量。所有变化的向量都被及时更新，所以能够保持整张表的同步。）
2. 如果自己的向量没有变化，就不需要去通报。
3. 如果到了某一轮，发现大家动态规划都无法使自己的向量发生改变，则说明该松弛的都松弛了，算法结束。根据算法规则，所有路由器**此时都进入了等待状态**。

###### 如何探测链路状态（费用）的变化

发送探测报文。

###### 检测到链路状态变化后，异步更新的一些特点

1. 更新一般是异步的。即这个消息是**逐轮**传递出去。
2. 一般当大家计算出的DV都不变化了，大家都进入了等待状态，更新结束。
3. **好消息传播得快**。因为一旦是链路状态变好，则**由于动态规划算法是取min的**，能够第一时间得到更新。

###### 无穷计数问题

当某段链路的花费突然增大，**将会花费很长一段时间，才能使路由器们意识到这个问题**。

> 这个时间取决于花费增长的链路的增长前的花费和其他可行路径的花费之间的差值。因为当计数到超过其他任一可行路径的花费，计数才能停止，路由器们才能意识到去走其他的这个可行路径。

问题就是出在DV算法是一个分散信息的路由算法。所有的路由器都是以其邻接的路由器作为信息来源，信息较为闭塞。当某段链路状态改变，直接与此链路相连的路由器可以直接检测到，但是其他不与此链路直接相连的路由器并不能第一时间意识到这一点——因为这些路由器本应该通过直接与此链路相连的那些路由器去知道这一点。但是直接与此链路相连的路由器此时错误地把不与此链路直接相连的那些路由器作为信息来源，认为通过这些路由器走会比从更新了状态的那个链路走更优。但是错就错在，这个“更优”是链路更新之前的那个状态，只是没有来得及更新罢了。根据之前的思考，这些路由器本来是应该通过与这段链路直接相连的路由器去更新的！所以这些信息本来就是不实时的，不正确的。且目前，直接与此链路相连的路由器和其他不与此链路相连的路由器构成了一个死循环的依赖圈，即：直接与此相连的路由器错误地认为走不直接与此链路相连的路由器更近，于是认为可以走它们；不直接与此链路相连的路由器错误地认为链路状态并没有更新，因为与此链路直接相连的路由器并没有给出正确的反应，**这就导致它们彼此认为走对方的路是更近的**。于是每轮更新都是把对方的向量加它们之间的花费，使得他们产生了以它们之间的花费为公差的等差数列的无穷计数。**直到该等差数列的值增长到大于其他可行路径，无穷计数才能结束**。

###### 毒性逆转法解决无穷计数问题（适用于链路变坏情况）

根据前面的分析，造成无穷计数的根本原因在于，与发生变化的链路直接相连的路由器错误地认为其他不与此链路相连的带有过时信息的路由器的花费更小，然而实际上只是因为它们记录了链路状态变更前的情况。

> 解决此问题的出发点在于<u>**禁止**</u>与链路直接相连的路由器去参考与链路不直接相连的路由器的DV。

如何禁止这种访问？让访问得到的结果为无穷，就相当于不采纳此信息了。也就是说：让不与链路直接相连的路由器给与链路直接相连的路由器通报新DV时，改为通报无穷。（注意，我们采用的模型是：直接与链路相连的路由器和不直接与链路相连的路由器之间是邻接的关系。）

也就是说，一旦这种邻接关系确定，即A与链路间接相连，B与链路直接相连，且A邻接B，即A通过B与链路相连的这种关系一旦确定，就遵循上述的“无穷”规则！注意，关系的确定往往就在一瞬间，往往就在新的DV被计算出来那一瞬间，则就意味着，当想要把新的DV通报出去的时候，就应该考虑是否要替换成无穷了！！

注意：毒性逆转法在结构简单的网络中可用，但是在复杂网络中**无法完全消除无穷计数问题**。

###### 最大度量法解决无穷计数问题（适用于链路断开且没有其他链路可走情况）

如果链路断了，且不存在其他替代线路，那么会无穷计数下去。因为没有其他线路提供数字，自然不存在一个数字能够小于当前的计数，使之停下来。所以为了避免死循环下去，要让他到一定程度就停下来。

> 还有一种方式就是：以最粗暴的方式解决无穷计数问题。既然你数数能数到很大，停不下来，那么我让你数到一定数量就停下来不就行了！

只需要定义某个数字=无穷。这样计数到这个数字就会掉入黑洞，因为无穷加任何数字都是无穷。这样就会导致DV不被更新，仍然是无穷。

但是注意，这种方式比较粗暴，只能适用于链路完全瘫痪，且无其他替代方案的情况。因为它会把花费**导向无穷**。

## 层次路由策略

层次路由是Internet所采用的策略。

#### 为什么不能在全球Internet上粗暴地就直接用路由算法了？

因为规模太过庞大。1e9数量级，光是计算就很慢，而且如果要存储也很麻烦，查找起来也很慢。而且无论是链路状态广播，还是距离向量通报，都需要占用网络资源。由于路由器的数量太过庞大，光是这种信息交换就能让网络**直接**瘫痪。

而且，每个ISP应该都期望用自己的方式管理网络，所以就不可能使用统一标准。况且，Internet讲究各种类型的网络互联！

#### 自治系统 Autonomous System, AS

自治系统是一系列路由器构成的一个网络，可以认为是这些路由器的**聚合**。

每个自治系统都对应一个自治系统号，是一个编码，用于标识此AS。

###### 注意区分AS和Subnet

AS是路由器的集合，子网是一系列主机在一个广播域中。

###### 自治系统内部路由协议 Intra-AS Routing Protocol

同一AS内，可以运行相同的路由协议。只是这些协议的作用范围被限定在本AS内（例如网络拓扑和路由信息都只是本系统的）。这种协议称为“自治系统内部路由协议”。具体用什么协议可以自己定。

###### 网关路由器 Gateway Router

即：位于AS边缘的路由器。**AS通过网关与其他AS相连。**

> 在AS间的路由，由网关完成。

###### AS内的路由器的路由表

此路由表由自治系统内的路由协议和自治系统外的路由协议共同决定。

**它们是有分工的**：

| 目的地         | 负责协议                       |
| -------------- | ------------------------------ |
| AS内其他路由器 | AS内路由协议                   |
| AS外           | **AS内路由协议和AS间路由协议** |

###### 网关路由器的责任（AS间路由协议的责任）

探测自己和哪些其他网关路由器相连（即：找到自己和哪些其他的AS邻接），并告诉自己AS内的所有路由器。

并且还需要探测到网络中还有哪些不与自己邻接的AS，能够通过哪些与自己邻接的AS到达。这就类似于链路状态信息建图。

> 一句话：搞明白本AS的各个网关可以到达的其他AS**分别是哪些**。<u>这些网关可以不知道去往其他AS的路径怎么走，但是必须知道通过它可以到达这些AS。</u>

###### 热土豆路由（在本AS的多个网关之间进行选择）

如果某AS可以以本AS内的多个网关作为出口到达，则根据**AS内的**路由协议，选择距离起点路由器花费最小的网关作为本AS的出口。

###### IGP 内部网络协议 Interior Gateway Protocol

> 所谓IGP，就是AS内部路由协议的别名。

常用的IGP：例如RIP、OSPF

#### RIP 路由信息协议 Routing Information Protocol

RIP是一个利用DV算法的最简单的IGP协议。

###### RIP对于链路花费的度量:hop

花费利用hop来度量。所谓hop，就是走过的**链路数**。因此如果从某一路由器本身出发，光出发这一下就算1跳。因为这不是从这个路由器跳出去了吗！！

###### 有限的通告接收方

RIP中的DV又称为通告(Advertisement).每个DV**最多能有25个目的IP**。即：最多到达25个子网。

###### 同步通告和异步通告相结合

RIP中的DV每隔30秒，就会**例行**通报一次。注意，这里的“例行”是指**不管**DV有没有发生改变，都会进行。当然，如果DV发生了改变，例如某时刻链路状态突然变化，DV**也还是会被异步**通报的。

###### RIP如何预防无穷计数

同时采用毒性逆转法（在DV中增加一项：next hop）和最大度量法(INF=16)来预防无穷计数。

因为采用最大度量法，且INF=16，故RIP**不适用于大规模网络**，因为可能正常的hop数都会超过15.

###### DV中新增的“next hop”项的作用

这样就相当于做了毒性逆转。还是那个模型：A与链路间接相连，B与链路直接相连，且A邻接B，即A通过B与链路相连，则A发给B的DV中，说明A的下一hop为B，则B就不会再错误地采用A的DV信息去更新自己的DV了。直接从根源上预防了无穷计数问题（但是正如毒性逆转那样，不能完全避免）。

###### 基于“超时”的链路失效探测

加入“超时”的概念。即：如果邻接路由器经过了180秒都没有发出通告（即：连续6次本该发出通告但是都没发出），则说明这个邻居或连接它的链路**死掉了**。此时首先发现邻接路由器死掉的路由器会重新计算路由表，然后异步发送通告。相邻的路由器收到后也重新计算DV，如果和以前的DV不同则也发出通告。这样，链路失效信息就快速传递到了全网。

###### RIP采用应用层的进程（称为route-daemon）管理路由表

因此，**DV被封装在UDP中进行通告**！

> 但是也不能说RIP协议是应用层协议。因为具体说这个协议是哪一层，关键看这个协议主要实现哪一层的功能，而不是看其具体实现。

#### OSPF 开放最短路径优先协议 Open Shortest Path First Protocol

对公众开放的，采用LS算法的IGP协议。其链路状态分组在整个AS范围内进行泛洪。此协议与IS-IS极其相似。

###### 什么是“Open”

也就是说它是对公众开放的。并不是某个公司的私有协议。

###### OSPF是网络层实现，这是与RIP的一个很大的区别。

OSPF的报文直接封装在IP数据报中。不需要使用传输层的功能。

###### OSPF报文安全认证

所有OSPF报文都带有安全认证。只有被认证了的OSPF报文才能被路由器采纳。这样能防范恶意的虚假链路状态分组泛洪入侵。

###### OSPF负载均衡

OSPF和RIP的不同点在于，对于起点和终点相同，链路费用也相同的路径，OSPF能够同时维护它们。这样，如果有大量网络流量，可以把他们均摊上去。

###### OSPF是支持QoS的（实现不同数据类型的分流）

OSPF和RIP的不同点在于，OSPF能根据不同的ToS提供不同的QoS。具体做法是：通过**变更链路费用度量**区分不同链路的功能。例如给流媒体ToS的链路设置高费用度量，这样这条链路能传输的数据总量就下降了，链路更通畅。

###### OSPF分层

OSPF支持对大规模的AS继续进行路由层次划分。能带来很多好处。

#### 分层的 OSPF 协议

###### AS的进一步划分

将整个AS进一步分成主干区(Backbone)和**若干个**局部区(Area)。其中，主干区就像是一棵树的树干，局部区就像是这个树干伸出去的若干树枝。不管是主干区，还是局部区，都是由很多路由器组成的。

###### 区域内部路由器 IR, Internal Routers

每个区内跑仅包含本区域的链路状态分组。每个区的内部路由器内也只存有本区的拓扑结构信息。因此，网络上跑的链路状态分组数量大大降低，网络流量**大大降低**；路由器需要计算的数量规模大大降低，计算速度**大大提升**。

###### 区域边界路由器 ABR, Area Border Routers

树干和树枝之间通过类似于网关的东西连接。这就是区域边界路由器。

区域边界路由器掌握去往其他区域网络的大致方向。就类似于网关掌握去往其他AS的大致方向。

**区域边界路由器同时作为主干区路由器和局部区域路由器。**因此它同时与主干区路由器和局部区域路由器交换链路状态分组，因此它能**同时掌握**主干区和所在局部区的网络拓扑。

区域边界路由器需要随时为其他的区域边界路由器通报其所在区域内到各个路由器的花费信息。但是不需要告诉它们所在区域内的详细的网络拓扑。

###### 主干服务器 BR, Backbone Routers

在主干区范围内泛洪主干区内部的链路状态分组，在主干区域拓扑结构内跑Dijkstra。

###### AS边界路由器（即网关） ASBR, AS Boundary Routers

可以有一个或多个。

#### BGP 边界网关协议 Border Gateway Protocol

BGP是Internet的标准协议。目前使用BGPv4。BGP是把整个Internet粘合在一起的**胶水**。BGP是对等的协议，因此连接的路由器双方常以peer相称；BGP是面向连接的协议，它基于半永久TCP连接实现。

> BGP提供了一种方式，即以发送eBGP Session的方式，让一个前缀能够通知Internet中的其他前缀，**宣告他的存在**。

###### 前缀、子网

前缀就是子网，子网就是前缀。因为在CIDR中，IP前缀就代表了一个无差别编址的子网。前缀的长度代表着子网的大小。前缀越小，子网越大。

###### BGP Session

在路由器之间交换BGP报文，又称BGP会话（Session）。

这些路由器之间是平等的，所以又称为peer。

###### BGP是通过应用层实现的

BGP被封装在TCP段中交换。通过半永久TCP连接进行。因为BGP的会话往往一直在进行，所以这个TCP连接可以认为是半永久的（很久都不拆除）。

###### BGP 路径向量 Path Vector, PV

此向量包含了到达某个前缀的完整路径。（这个路径是指需要途经哪些**AS**。）

需要与DV进行区分：DV包含了**所有**邻接点的距离，其每个分量是到每个临界点的距离；PV包含了到**特定**前缀的具体AS路径，其每个分量是经过的AS信息。

###### BGP属性

一个BGP报文中，除了包含前缀，还包括属性。属性信息是指明路径的关键信息。

路径信息=前缀+属性。只知道前缀，没有属性，则无法确定这个前缀如何到达；只有属性，没有前缀，则不知道这个路径的终点去往哪里。

| 属性     | 解释                                                         |
| -------- | ------------------------------------------------------------ |
| AS-PATH  | 以AS为单位的到达前缀的路径。包含了一个AS序列。此序列的最后一项就是发出此eBGP报文的AS。 |
| NEXT-HOP | 发出此eBGP报文的网关的IP地址。收到此eBGP报文的网关能由此知道，它下一跳的AS应该从什么IP地址的网关进入。如果同时有很多路径经过某AS，且是通过了其中的不同的网关，则此字段可以方便地区分不同路径。这里的应用场景**和热土豆路由是一样的。** |



###### eBGP Session

是跑在AS层面上的BGP会话，由网关接收和发送，其方式为**泛洪**（但是也可以受到政策影响，比如美国的军事线路的可达信息的eBGP会话不会往伊朗方向转发。“政策干预”是BGP的特色功能。）。通过eBGP，可以从邻居AS处得知，通过这个邻居AS可以到达哪些前缀。

> 当AS1给AS2通告一个包含某前缀的BGP外部报文，代表AS1承诺：如果有要到达该前缀的分组，可以直接往我AS1转发就没毛病！因为AS1在到达该前缀的AS组成的路径的最末端！

eBGP报文在通告时，会尽量进行CIDR合并（路由聚合）。这样能尽量提供范围更大的前缀信息，落到最终每个内部路由器头上，就是能降低它们的路由表体积。

###### iBGP Session

是跑在内部路由器层面上的BGP会话，由内部路由器进行发送和接收。当一个AS的网关收到了eBGP带来的前缀可达性信息，它可以把该信息广播到所在AS。

当eBGP到达所在AS的网关，该网关给此AS内的所有路由器分发包含收到的前缀可达性信息的iBGP报文。此AS内的内部路由器收到此iBGP报文，则也就相当于得知了这个前缀的可达性信息。把此信息添加到自己的路由表中，即**大功告成**！

当然，此iBGP报文也可以到达此AS内的其他网关处。

###### 封装新的eBGP Session

如果iBGP报文到达了所在AS内其他网关处，则此网关可以根据政策决定是否继续封装新的eBGP报文去传递给其下游的其他AS。

具体封装需要在路径（即AS-PATH属性）的尾部加上本AS，并且更改“下一跳（即NEXT-HOP属性）”为此网关的IP。

###### BGP报文

| 报文类型     | 解释                            |
| ------------ | ------------------------------- |
| OPEN         | 请求与peer建立TCP连接           |
| UPDATE       | 通告新路径或删除老路径          |
| KEEPALIVE    | 保活TCP连接；确认TCP连接请求    |
| NOTIFICATION | 报告先前报文的错误；关闭TCP连接 |

###### BGP输入策略 Import Policy

网关路由器收到外部BGP报文之后，可以根据输入策略决定是否采纳路由。这就是BGP的基于策略路由的特色。

例如：从不将流量路由到某区域。那么如果这个报文里面给出的AS路径经过了这个限制的区域，则不接受这个路由！

###### BGP路由选择

如果此网关能够知道多条到达相同目的AS的路由，则需要进行选择，决定要采用哪一条。

| 优先级 | 策略             | 解释                                                         |
| ------ | ---------------- | ------------------------------------------------------------ |
| 最高   | 本地偏好值       | 又体现了“基于策略路由”的思想。即：为不同的线路设定偏好值，这个值越大，不管距离和花费如何，都要先取选择它。 |
| 次之   | 最短AS-PATH属性  | 此属性中包含的AS序列越短越好。（注意：AS序列短，并不代表实际花费（跳数）少。） |
| 最低   | 最近NEXT-HOP网关 | 即“热土豆路由”。                                             |

###### 与BGP有关的各种网络

| 网络类型                                                     | 解释                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 提供商网络/AS                                                | ISP的网络。可以进行BGP的扩散。即：可以通过AS-PATH的网络。    |
| 客户网络/AS                                                  | 接入ISP的客户用网络。不提供BGP扩散。即：不期望其他网络的流量在它上面走。不承诺作为一条到达某前缀的路径。即：必然不通过AS-PATH的网络。 |
| 桩网络/AS（Stub Network/AS）                                 | 只与一个AS相连的网络或AS。一般属于客户网络。因为ISP网络不可能只有一个连接。那他就不可呢有客户接入，因为唯一的一个连接一定是到Internet的。 |
| 双宿/多宿网络/AS（Dual-homed network/AS, Multi-homed network/AS） | 连接多个AS的网络或AS。可能是ISP网络或者客户网络。这就意味着如果是ISP网络，则可以承诺通过AS-PATH。 |

###### ISP AS的路由选择策略

ISP AS一定**只会**向自己的客户发送eBGP Session。而不会向其他ISP AS发送。因为这样它获取不到任何利益，甚至在帮助竞争对手。

#### IGP各项协议与BGP协议的比较

| 比较项目 | IGP，例如RIP和OSPF   | BGP                                            |
| -------- | -------------------- | ---------------------------------------------- |
| 策略     | 只有一种内部策略     | 政策性强，需要考虑很多政策，甚至涉及到国家机密 |
| 性能     | 以提升性能为首要目标 | 以落实策略为首要目标                           |

