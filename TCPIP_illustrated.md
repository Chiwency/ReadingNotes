# TCP/IP Illustrated

## Link Layer

+ **CSMA/CD**: (carrier  sense,  multiple  access  with  collision  detection) 载波监听，碰撞检测协议。以太网（ Ethernet）用的一种协调同一总线上各站点发送数据帧（frame，链路层的PUD）的协议。

  > 但是CSMA/CD 一般用于 10Mb/s 的以太网， 到后来 100Mb/s 甚至更高带宽的以太网，用的是星型拓扑结构，每个主机之间用交换机（switch）连接起来，每条线路都是全双工通道（可以同时接收和发送信息），交换机可以隔离碰撞域，所以就不需要CSMA/CD协议了。
  >
  > 以前不用交换机的以太网就是半双工通信，要进行碰撞检测。

+ **Frame format**:

  ![frame_format](./images/frame_format.png)

+  **FCS**: (Frame Check Sequence/Cyclic Redundancy Check (CRC)): 循环冗余码检测, 检测帧在传输过程中是否

  是否出现差错，但是不能定位差错

+ **Frame Sizes**: 数据链路层的帧长度有最大最小限制，一般而言（IEEE的某个协议规定）最小是 ``64 bytes``  ,不足的用``0 bits``填充. 最大长度是``1518 bytes`` 但是对于网络层的最大``64 KB`` 的数据报，帧长度显然不够，就需要对数据报进行分片，至少``44 frames`` , 为了提高数据传输率，有的协议会扩大帧长度，能超过``9000 bytes`` 

  > 最小帧长度是为了适应在使用CSMA/CD以太网中，发送时延满足一个往返时间。当发送最后一个bit时，能检测到有无碰撞。 
  >
  > 设置最大长度是为了在CRC检测到错误丢弃帧时，减少重发带来的损耗。

+ **VLAN**: 使用交换机的以太网可以方便的实现多播、广播，但是当以太网中的主机过多，多个主机同时进行广播就会严重占用信道资源，同时也出于一些安全考虑，就出现一种策略，将一个以太网通过``交换机端口(常用)``、``MAC地址``或``IP地址`` 来分为多个不同的虚拟局域网``VLAN`` , 进行逻辑上的切分，在链路上发送帧时，通过帧内的``VLAN ID`` 来区分。不同虚拟局域网之间的通信要通过路由器``router`` 来完成。

+ **LACP**:（Link Aggregation Control Protocol），有的操作系统有多个网络接口可以实现链路聚合，可以同时通过多个接口发送数据实现高速可靠的数据传输，从而降低了对高性能交换机的需求。

  > 书中举例将有限以太网接口和无线局域网接口聚合，生成一个虚拟接口，数据报通过虚拟接口转发，然后有五种模式来确定数据报实际是由哪个接口发送。比如两个接口可以实现负载均衡转发或者备份转发。
  
+  **Duplex Mismatch**: 双工不匹配问题。当通信的两个接口之间一个使用全双工通信，一个使用半双工通信时，通常不会报告错误。当线路上的流量增长到一定程度，半双工接口会检测到碰撞，从而触发CSMA/CD协议的指数退避机制，被丢失的数据报又会触发发送方的高层协议(TCP)的重发机制，严重降低性能。 而这种错误只有在半双工接口在发送数据，但是又在持续接收数据时才能检测到。

+ **Link-Layer FLow Control**: 链路层也有流量控制。交换机可以储存转发数据帧，当多个站点同时向一个站点发送数据时，交换机会缓存一些数据帧，如果长时间没有被处理，该数据帧就会被丢弃。所以协议规定，交换机可以发送``PAUSE frames`` 到所有站点告知他们停止一段时间（512 bit times）再发送数据. 但是这也引发一些问题，交换机会对那些无辜的站点也发送停止信息。因此链路层的流量控制不是很常用。

+ **Bridges And Switches**: 交换机就是高性能网桥，接口多。 交换机的转发表``filtering databases`` 是通过学习得来的。开机时转发表是空的，然后每次转发都会记录数据帧源MAC地址，如果没有目的地址，就转发到所有端口，所以一开始交换机学习的过程中网络链路是高负载的。一些操作系统也实现了交换机的学习功能，所以基于windows和linux的电脑也可以充当交换机。因为有的设备的MAC地址会变，所以转发表的每条记录都有一个有效期，有效期一过就要删除该记录，重新学习。

+ **STP**: (Spanning Tree Protocol) 生成树协议（由每个交换机实现）

  **RSTP**: (Rapid Spanning Tree Protocol )

  > 下面这种拓扑结构在交换机学习的过程中会产生广播风暴，所以使用生成树协议打破闭环，生成唯一一条路径，任何站点可以通过该路径达到任何一个站点。生成树路径可以有很多条，但是通过各链路的带宽选择性能最高的一条。下面黑线加粗的部分就是一条生成树路径，路径上的端口可以转发分组，其他端口都被阻塞。

  ![](./images/STP.png)

+ **MRP**: (Multiple Registration Protocol) 有``MVRP (for registering VLANs) ``和 ``MMRP  (for  registering  group  MAC  addresses)``两种应用， MCRP处理一个新站加入VLAN的情况，避免STP被重新计算。MMRP是为站点注册他们的多播组，以便交换机准确转发多播信息。

  > MMRP  is  a  method  for  stations  to  register  their  interest  in  group  MAC addresses  (multicast  addresses). 

+  **WLAN(WI-FI)**:  下面是架构图

  ​	![](./images/wlan_bss.png)

  ​		有很多种数据帧格式，下面是通用的一种，有些字段在不同类型的数据帧中可能有所不同

  ![](./images/wlan_frame.png)

  + **Frame Ctrl**: (Frame  Control  Word) 标定帧的类型，有三种：``management frames`` `` control frames`` ``data  frames`` 每种类型都有一些子类型

  + **Management Frames **:  管理``station`` 和 ``access points`` 之间联系的建立维持和结束,以及其他的一些控制信息

    > 一个WI-FI接口扫描附近可用的网络(access apoints)时，就是在发送特定的``Management Frames``

  + **Control Frames**: RTS/CTS and ACK

    当站点之间要传输的``packages`` 大小超过一个阈值时，会启用流量控制。 ``Sender``在发送数据之前先发送一个``RTS(request-to-send)`` ,如果``recipient`` 可以接收，则返回一个``CTS(clear-to-send)``

    ``ACK(retransmission/acknowledgment)``用来重传确认（仅适用于单播s）。如果发送方没有在一定时间内收到``ACK``,就会触发重传机制。重传的数据的 ``Frame Ctrl`` 内设置一个``Retry bit``
    
  + **Data Frames, Fragmentation, and Aggregation**: Data Frames 就是传输数据的帧。 而且协议支持对帧分片。如果每个比特出现差错的概率是相同的，那么越大的帧出错的概率越大，重传次数就越多，所以分片可以降低单片传输差错，而且降低重传开销。协议还支持帧聚合(有两种模式)，对发往同一个地点的帧组装起来一起发送.而且每个子帧有自己的FCS(循环冗余码检测)，所以如果帧出错可以只重发出错的子帧(针对A-MPDU,A-MSDU则没有这个功能)。帧聚合在比特差错少的信道中表现良好.
  
+ **PSM/TSF**: (Power Save Mode/Tme Sync Function) 在无线网中，接收站（如手机）的网卡会持续运行以接收信号，接收站可以进入省电模式，AP为它缓存帧数据，每过一段时间AP向其发送``beacon  frames `` ，通知接收站，接收站决定是否接收缓存的帧. TSF是用来同步站点和AP之间的时间，以保证AP在正确的时间唤醒站点检查缓存的帧。 除此之外，还有许多其他的省电技术。

+ **Virtual Carrier Sense**: 虚拟载波监听通过每个站点设置一个计时器，``Network  Allocation  Vector (NAV) `` 。发送器在发送``RTS/CTS`` 帧时设置一个持续时间（`Duration`）。站点持续监听这两种帧，取出`Duration` 值与`NAV` 比较，如果大于`NAV` 的值，曾更新`NAV`, `NAV` 会随时间衰减，为0时则代表信道空闲，可以发送

+ **Physical Carrier Sense**： 物理方法实现载波监听，通过clear channel assessment（CCA），监听信道中的能量和波形来判定信道是否空闲。通常与`NAV` 一起使用

+ **DCF Collision Avoidence** : (distributed  coordinating  function): 因为对于无线设备，很难在发送的同时进行碰撞检测（以太网就是这样干的，所以叫CSMA/CD），所以一般采用碰撞避免（CSMA/CA）。站点想要发送数据前等待一个``DIFS (distributed inter-frame  space)`` 时间来让高优先级的站点使用信道。在这个时间内检测到信道繁忙（这个时候进行载波监听），则再等一段`DIFS`时间. 如果检测到信道空闲，发送方执行碰撞避免程序，和以太网退避程序一样，等待一个随机时间后发送。

  >  当站点正确接收数据帧后，等待一个`SIFS (Short Interframe Space)` 时间后发送`ACK` 信息（没有碰撞避免程序），`SIFS`的时间比` DIFS` 短，以此实现`ACK` 信息有更高的发送优先级。