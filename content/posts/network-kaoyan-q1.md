---
title: 计算机网络考研题解Ep.1
date: 2021-06-24 17:13:06
tags: [考研]
categories: [计算机网络,考研]



---



#### 通信基础

`10BaseT的含义` 10Mbps的以太网。

`计算QAM的数据率` 2Blog2(mn)，其中mn是～。

`以太网波特率40MBaud，求数据率` 以太网使用曼彻斯特编码，每个bit需要2次码元变化来表示，所以是20Mbps.

`某信号的数据率为64Kbps，码元有4个有效离散值，求波特率` 有效离散值4个，则可代表2位二进制，所以应64/2=32KBaud.

`信噪比DB` 10lg(S/N).

`计算结果应该是尼奎斯特极限小于香农给出的极限较小的一个。`

`采用4相位的PSK的2400bps的信道的波特率？` 4相位，则log2(4)=2，则2400/2=1200baud.注意，并没有在计算最大速率。直接乘即可。

`PCM量化，128个等级，采样8kHz，求数据率？` 7bits，则8k*7即可，56Kbps.注意，并没有在计算最大速率。直接乘即可。

`链路数据率100Mbps，分组大小1KB，分组头大小20B，发送980000B文件经过3跳，求需要多长时间发送完毕` 

```
将所有分组发送完毕的时间是1M*8/100Mbps=0.08s=80ms;将最后一个分组发送一跳的时间是1K*8/100Mbps=0.08ms,共需要再经历3-1=2跳所以是0.08*2=0.16ms，所以共需要80.16ms.
```

`地球到月球的距离是385000km，传播速率是1c，求RTT.`

```
RTT=2*传播延时，即2*385000*10^3/c=2.57s
```

`地球到月球的距离是385000km，带宽100Mbps,传播速率是1c，求时延带宽积.解释含义。`

```
由于RTT=2.57s，故时延带宽积是RTT*带宽=2.57*100Mbps=32MB.
这代表了这数据一个来回的时间内，发送方可以发送的数据量。
```

`条件同上题，如果地球从月球请求一个大小为25MB的图像，需要多久地球可以接收到全部内容？`

```
从地球发出请求到第一个比特被地球接收，需要1个RTT的时间。
月球完全发送完成，需要传输延迟的时间，该时间为25MB/8*100Mbps=2.1s.
所以总时间是2.1+RTT=2.1+2.57=4.67s.
```

`A和B通过链路，中间用交换机S连接，每条线路上的传播延时都是20us,交换机存储转发需要35us，求10000b从A到B的时间`（分别采用报文交换和分组交换，分2组）

```
发送10000b需要的时间:10000b/10000Kbps=1000us.
报文交换需要的时间: 1000us*2+20*2+35=2075us.
分组交换需要的时间：500us+500us+2*20+35+500us=1575us.
```

`分成若干组进行分组交换` 注意，其中的各段链路的传播延时和各个路由器的处理延时都只需要算一次，因为只需要计算最后一个分组在链路上走的情况。**<u>其中，有k个路由器，则处理延时是k倍的；链路传播延时是k-1倍的；路由器发送传输延时是k-1倍的！</u>**之前的题因为只分了2组，这个问题没有被体现出来。

#### 物理层

`已知某5口集线器的带宽是10Mbps，求每台计算机分得的平均带宽？`

```
由于同时只能有1个口的有效输入，所以平均分得带宽是集线器带宽/口数量，及10/5=2Mbps.
```

#### 数据链路层

`假设某链路的成功率是0.95，且采用无确认无连接服务，某分组交换需要分成10组，求最终的成功率？`

```
即(0.95)^10. 它是独立重复实验。
```

`为了检查2bit的错误，编码的海明距离应该是？纠错呢？`

```
对于d位错误，如果检错，需要海明距离d+1;
如果纠错，需要海明距离2d+1;
所以分别是：2+1=3，2*2+1=5.
```

`对于10bit的信息，求海明码编码需要的冗余位数量？`

```
对于k位编码，需要r位的冗余信息。
满足：2^r>=k+r+1.取r最小值。
r，rong，冗。（记忆）

因此这里是2^r>=11+r，则r=4.
```

`已知数据是1101 0110 11，求生成多项式为10011加了CRC之后的数据？`

```
要点：
1.一定要在最后填上多项式最高次幂个0.
2.模2除法判断能否上商的*唯一*依据是位数够不够除数的位数。因为它不用借位。
3.得到的*余数*才是CRC码。
```

`已知收到了数据 1011 0011 010，生成多项式11001，判断是否出错，并给出原始数据和其CRC码？`

```
要点：
1.判断CRC校验是否正确即用得到的数据除生成多项式（这次不用上0了！！！），如果整除则正确。
2.CRC码的长度和上一题上0的数目一样，都是生成多项式最高次幂。因此直接截取1010即可。
```

`信道效率，又称信道利用率计算`

```
首先定义发送周期T：从开始发送数据到收到此数据的ACK这段时间。
信道效率是针对发送方而言的。指的是发送方的传输延时和发送周期的比值。
其中，设信道带宽R，帧长L，则传输延时是L/R.
则信道利用率是L/TR.
```

`信道吞吐量计算`

```
信道吞吐量仍然是站在发送方的角度来说的。
其中，设信道带宽R，帧长L，发送周期T.
信道吞吐率就是信道吞吐量就是*单位时间内*发送方*平均*给信道发送的数据量。注意这里是平均，所以是L/T.
```

`已知信道带宽4Kbps，单向tprop=30ms，若要信道利用率到达0.8，求帧长度要求？`

```
可知RTT=60ms.
则发送周期应该是RTT+ttrans=60+L/R.
代入公式即可。
```

`可靠传输协议 - 已知采用GBN，单向tprop=270ms，带宽16Kbps，数据帧长度在128-512B，确认帧长度和数据帧长度相等，求使信道利用率最高的情况下，帧序列号使用多少比特数编码？`

> GBN窗口大小和信道利用率的关系：在一个发送周期，即RTT+ttrans内，发送方发送的帧越多，对信道的占用就越多，利用率就越大。

```
信道利用率最高，也就是发送方尽量多地发送数据（因为无论是吞吐量还是利用率都是从发送方的角度来说的。）
要发送更多的帧，则需要发送最小长度，也就是128B。
ttrans=128B/16Kbps=64ms.
RTT=270*2=540ms.
则发送周期是RTT+ttrans数据+ttransACK=540+64+64=668ms.
则要最大化信道利用率，需要让整个发送周期都在发送帧。
即：668ms/ttrans数据=10.4帧，所以需要4位序号。
```

`可靠传输协议 - 对于n大小的滑动窗口，最多可以由多少帧发出了但是还没有确认？`

```
答：n-1个。因为如果是n个，则无法判断是新帧还是旧帧。
```

`可靠传输协议 - 对于序号n位的SR协议，发送方窗口大小是W，上边界序号U，求下边界序号？`

```
（W-U+1）mod2^n.
注意，是2^n！因为我们探讨序号空间。
```

`可靠传输协议 - 使用GBN协议，发送方窗口大小1000，帧长1000B，带宽100Mb/s,忽略ACK的ttrans，单向传播延时50ms，求发送方最大数据率是多少？`

> 注意，这里的发送方速率和所谓信道吞吐率都是指的是发送方平均速率。分母是发送周期。
>
> 即：发送方速率和吞吐率是同义词。
>
> 请注意，当发送方窗口足够大，能保证在发送周期内持续发送时，<u>其吞吐率就是带宽</u>。

```
RTT=100ms，发送方的ttrans=1000B/100Mb/s=0.08ms.
则发送周期T=RTT+ttrans=100.08ms.则若要让数据率达到最大，应该在T内把1000个帧都发完，即1000*1000B=1MB大小，即1MB/100.08ms=80Mbps.
由于80Mbps<100Mbps，所以信道可以提供这个速度，所以答案是80Mbps.
可见，发送方最大速率达不到带宽的原因是协议的限制（窗口故）。
```

`可靠传输协议 - 找出一种情况，使得协议不能正常工作？`

```
关键在于接收方无法区分新旧帧。
```

`可靠传输协议 - 带宽为50Kbps的采用全双工协议的信道上传送长度为1kb的帧，序号空间由3bit编码，单向传播延迟270ms，求SW，GBN，SR协议的信道利用率？`

> 由于不同协议在同样序号空间下的发送窗口大小不同，所以能达到的利用率不同。
>
> 而且，需要注意全双工的Piggybacking策略。

```
由于采用全双工协议，所以采用Piggybacking的方式，即稍带确认。也就是ACK帧的ttrans和数据帧的ttrans相等，ACK的ttrans不可忽略。
则RTT=270*2=540ms,ttrans=1kb/50kbps=20ms.
传输周期=20+20+RTT=580ms.
1）对于SW协议，一个T只能传输一帧，所以信道利用率为20/580=0.034.
2）对于GBN，其发送窗口大小为2^n-1=7，所以利用率为20*7/580=0.241.
3）对于SR，其发送窗口大小最大为2^(n-1)，即4，所以利用率为20*4/580=0.138.
```

`【CSMA/CD】10BaseT以太网最小帧长（64B）计算`

```
以太网规定争用期为51.2us.
10BaseT是10Mbps，则可以发送51.2us*10Mbps=512bit=64B.
```

`【CSMA/CD】长度为10km，数据率为10Mbps的CSMA/CD网络，带宽为200m/us，求最小帧长。`

```
RTT=2*10km/200*10^6=10^(-4)
则最小帧长=W*RTT.
//需要注意的地方就是算RTT的时候，距离乘2.
```

`【CDMA】C收到了20200-20-20202，且A的码片是1111，求C收到的A发送的数据？`

> 分组大小和最后除的数都是码片长度。

```
先分成4个一组，因为码片长度为4，即2020 0-20-2 0202
显然是2+0+2+0 0-2+0-2 0+2+0+2，即4，-4，4，除以4得1，-1，1，即101.
这里除4是因为码片的长度为4.
```

`【Slotted ALOHA】10000个站竞争Slotted Aloha，每个站点平均每小时作18次请求，一个Slot是125us，求通信负载G？`

> 求Slotted ALOHA的通信负载，就是看每个Slot平均发送多少次请求。

```
通信负载G是Slotted ALOHA平均每个Slot的发送次数。
每小时18次，则3600s作18次，200s作1次，1s作1/200次，则10000个站1作10000/200=50次。
一个slot是125us，即1/8ms，所以1ms有8个slot，1s有8000个slot，所以平均每个slot有50/8000=1/160，即G=1/160.
```

`【纯ALOHA】`N个站点共享带宽为56kbps的纯ALOHA，每个站点的速率为100s发送1000bit大小的帧，求N最大值。

> 求纯ALOHA的实际总带宽，就是直接用信道利用率乘总带宽。

```
根据纯ALOHA的利用率，为1/2e，因此ALOHA的实际总带宽为56/2e=25*0.184=10304bps.
每个站需要的最小带宽是1000/100=10bps.
所以N的最大值是10304/10=1030.
```

`【CSMA/CD综合】` 局域网采用CSMA/CD，其带宽为10Mbps，两个主机间距2km，信号传播速率200000km/s。

1）若两方信号发生冲突，最短需要多长时间让双方都检测到？最长呢？

2）若双方采用SW协议，其中甲的帧长是以太网标准最长帧1518B，对方的ACK的长度为64B，求甲的吞吐量和**有效数据率**？

```
1）最长检测时间是算争用期那个模型。最短检测时间是信号正好在链路中心冲突。
所以，前者是RTT，后者是RTT/2。
其中，RTT=2*2km/2000000km/s=0.02ms.
RTT/2=0.01ms.
2）发送方吞吐量联系前面的知识，有*发送数据量/发送周期*。
其中，发送周期=RTT+双方的传输延时（因为这里ACK没有被忽略其大小）。
有效数据率是有效数据的速率，其应该去掉帧头帧尾。对于数据链路层来说，其有效数据是网络层的分组。在这里即以太网的MTU大小，1500B.
其中，ttransA=1518*8/10Mbps=1.2144ms,
ttransB=64*8/10Mbps=0.0512ms.
所以发送周期=1.2144+0.0512+0.02=1.2856ms.
所以发送方的吞吐量是1518*8/1.2856=9.33Mbps.
发送方的有效数据率是1500*8/1.2856=9.33Mbps.
```

`【数据链路层设备】` 求交换机的容量？

```
交换机的容量就是其吞吐量。即同时通信的端口数*端口的速率。
对于全双工交换机，同时通信的端口数就是端口总数。
```

`【数据链路层设备】` 求100Mbps端口带宽的交换机，其直通交换方式的最小延迟？

```
最小延迟即交换机无需排队。
由于目的地址在前6B，共48bit,所以延时是48/100M=0.48us.
```

`【数据链路层设备】` 判断交换机的行为？王道书P125.综合题。

```
1）注意，交换机只会把帧的*源地址*放进自己的转发表，目的地址不管。
2）交换机丢弃帧是判断帧的*目的地址*是否在自己的转发表里面，如果在就丢弃。显然，如果转发表初始状态为空，则交换机*不具有*丢弃功能。
```

`【RIP协议】` 已知C到B、D、E的延迟分别是6，3，5，且B、D、E给C的DV分别是（5，0，8，12，6，2），（16，12，6，0，9，10），（7，6，3，9，0，4），求C计算出的新的DV？

```
B（5，0，8，12，6，2）
D（16，12，6，0，9，10）
E（7，6，3，9，0，4）
它们分别加上C到它们的延迟，并令C分量为0，得：
B (11，6，0，18，12，8)
D（19，15，0，3，12，13）
E（12，11，0，14，5，9）
从这三个向量里面，对于每个分量取出最小的那个，拼凑可得出C的新DV：
（11，6，0，3，5，8）
```

`【IPv4】` 计算A、B、C类IP地址可以带的网络数和主机数？

```
A类IP地址的网络数：去除第一个网络，即0号网络，去除127号回环地址。由于网络号有8-1=7位，即2^7-2.
B类IP地址的网络数：去除第一个网络，即128.0号网络，由于网络号有16-2=14位，即2^14-1.
C类IP地址的网络数：去除第一个网络，即192.0.0号网络，由于网络号有24-3=21位，即2^21-1.
A类IP地址的单网络主机数：子网掩码8位，剩下24位，去除主机号全0全1，所以是2^24-2.
B类IP地址的单网络主机数：子网掩码16位，剩下16位，去除主机号全0全1，所以是2^16-2.
C类IP地址的单网络主机数：子网掩码24位，剩下8位，去除主机号全0全1，所以是2^8-2.
```

`【IPv4】` 下列是单播地址的是

```
一定要把IP地址转化为二进制形式，否则你根本就看不出来！
```

`【IPv4】` 下列是回环地址的是

```
只要网络号是127，即127.xxx.xxx.xxx就是。
```

`【IPv4】` 求192.168.4.0/30中，能接收192.168.4.3地址的主机个数？

```
192.168.4.0/30中，主机号2位，故可以带4-2=2个主机。
其中，192.168.4.3的最后2位都是1（因为2+1=3），所以是广播地址，所以接收主机为2个。
```

`【IPv4】` 网络号198.90.10.0/27，子网掩码为255.255.255.224，则最多可以划分多少子网？每个子网最多可以有多少主机？

```
子网掩码有27位1，则还剩下5位可分配空间。主机编号至少是2位，否则就一个相当于全1，一个相当于全0，所以最长网络号是5-2=3位，所以最多可以划分8个子网。
每个子网的最大主机数就是把整个划分为1个子网，则主机编码可以有5位，则去掉全0全1，得到2^5-2=30.
```

`【IPv4】` 一台主机有2个IP地址，则另一个地址可能是？

```
必然不能选择和此IP在同一个子网里的。
```

`【IPv4】` 一个IP数据报长度4000B，经过的链路的MTU是1500B，求分片情况。

> 每个分片大小确定方式是：取最大的小于等于MTU中数据大小的8的倍数。

```
数据报长度4000B
则IP数据报数据长度4000-20=3800B
MTU是1500B
则MTU数据长度1500-20=1480B
接下来以数据为依据进行分片：
第一片：1480B，是8的倍数，可以；偏移量=0.
第二片：1480B，同样是8的倍数，且同时确保最大。已经2960B。且偏移量=1480/8=185.
第三片：520B。（最后一片可以不是8的倍数。）且偏移量=2960/8=370.
```

`【IPv4】` 一个IP数据报有2000B的数据部分，先后通过MTU为1500B和576B的链路，求最终分片情况？

```
第一个网络：
第一个MTU下，可承载1500-20=1480B的数据。且1480可被8整除。
则分成数据为1480B和520B的两片。（实际大小是1500B和540B。）
第二个网络：
第二个MTU下，可承载576-20=556B的数据。其中，最接近的8的倍数是552B，所以1480B的数据被分成552B、552B、376B的三片。因为520B的那些数据<552B，所以不用分片，所以最终结果是552B,552B,376B,520B.
```

