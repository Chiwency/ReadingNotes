# TCP/IP Illustrated

## Link Layer

+ **CSMA/CD**: (carrier  sense,  multiple  access  with  collision  detection) 载波监听，碰撞检测协议。以太网（ Ethernet）用的一种协调同一总线上各站点发送数据帧（frame，链路层的PUD）的协议。

  > 但是CSMA/CD 一般用于 10Mb/s 的以太网， 到后来 100Mb/s 甚至更高带宽的以太网，用的是星型拓扑结构，每个主机之间用交换机（switch）连接起来，每条线路都是全双工通道（可以同时接收和发送信息），交换机可以隔离碰撞域，所以就不需要CSMA/CD协议了。

+ **Frame format**:

  ![frame_format](./images/frame_format.png)

+  **FCS**: (Frame Check Sequence/Cyclic Redundancy Check (CRC)): 循环冗余码检测, 检测帧在传输过程中是否

  是否出现差错，但是不能定位差错

+ **Frame Sizes**: 数据链路层的帧长度有最大最小限制，一般而言（IEEE的某个协议规定）最小是 ``64 bytes``  ,不足的用``0 bits``填充. 最大长度是``1518 bytes`` 但是对于网络层的最大``64 KB`` 的数据报，帧长度显然不够，就需要对数据报进行分片，至少``44 frames`` , 为了提高数据传输率，有的协议会扩大帧长度，能超过``9000 bytes`` 

  > 最小帧长度是为了适应在使用CSMA/CD以太网中，发送时延满足一个往返时间。当发送最后一个bit时，能检测到有无碰撞。 
  >
  > 设置最大长度是为了在CRC检测到错误丢弃帧时，减少重发带来的损耗。

