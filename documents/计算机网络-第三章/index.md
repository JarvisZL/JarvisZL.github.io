# 计算机网络-第三章


# 第三章
<!-- more -->

## 运输层
### 概述
- 运输层协议为运行在不同主机上的应用进程之间提供了逻辑通信，其只工作在**端系统**上，将应用进程的报文移动到网络边缘(**网络层**)，或者从网络层获取报文。
- 两个主要的协议：
  - UDP(用户数据协议)，不可靠的，无序的，无连接的。
  - TCP(传输控制协议)，可靠的，有序的，面向连接的。
- 运输层将主机之间的交付扩展到进程间的交互被称为 **运输层的多路复用和多路分解**。

### 多路复用和多路分解
- $<IP，Port>$这样被称为套接字。
- 将运输层报文段中数据交付到正确的 **套接字**的做工称为 **多路分解**。
- 从不同套接字收集数据，加上首部，生成报文段后传递到网络层 ，这个工作称为 **多路复用**。
- 多路复用和多路分解的要求：
  - 套接字有唯一标识。
  - 报文段中有特殊字段指示所需交付的套接字，即源端口号和目的端口号。 端口号是16比特数，其中$0\sim 1023$这部分是 **周知端口号**，用于如HTTP等协议使用，其他可以随意分配。
  - ![报文段格式](/images/documents/计算机网络-第三章/TCP-UDP报文段格式.png)

#### 无连接的多路复用和多路分解(UDP)
- UDP套接字由一个 **二元组(目的主机IP，目的端口号)** 唯一确定，在这里源端口号和源IP作用是作为返回地址。
#### 面向连接的多路复用和多路分解(TCP)
- TCP套接字由一个 **四元组(源IP,源端口号，目的IP，目的端口号)** 唯一确定，只有四个属性全部一致的报文才会被发送到同一个端口上，**除非**该TCP报文段首部的 **连接建立位是置位**(即该报文用来建立连接)，则都会发送给主机上用来建立连接的端口上。

### UDP(用户数据)协议
- 仅仅提供了复用/分解服务和差错检验服务。
  - 报文段格式：![UDP报文格式](/images/documents/计算机网络-第三章/UDP报文段格式.png)
  - 其中Checksum用于差错检验，其计算方法和因特网校验和一样，二进制求和取反。
  - UDP首部为8字节。
- UDP协议的优点：
  - UDP能够立马发送应用层传下来的数据，不会有过分的延迟传输，对于实时应用是很重要的。
  - 不需要建立连接，不需要维持连接，所以没有连接延迟和维持连接的开销。

### 可靠数据传输原理
- 经 **完全可靠信道传输** 的FSM(有限状态机器)
  - 发送方和接收方都有数据就传输，有数据就接收。
- 经 **具有比特差错信道传输** 的FSM
  - 加入 **差错检验和接收端反馈(ACK/NAK)**，即发送后 **停止等待** 
  - 但没有考虑到接收端发送的ACK/NAK也可能受损，解决方法是在数据分组中加入 **序号** 字段
  或者也可不使用NAK而是发送ACK时候带上ACK的分组序号，由发送方判断是否是刚刚发送的分组的ACK从而决定是否要重发。
- 经 **具有比特差错和丢包信道传输** 的FSM
  - 加入 **定时器**，在时间阈值内没有收到正确的ACK则重发。
  <img src="/images/documents/计算机网络-第三章/rdt3.png" style="zoom:50%">
- **流水线可靠数据传输协议**
  - 针对rdt3的停止等待导致信道利用率低下的问题，改用不使用停止等待，而是允许发送一定量的分组而不需要得到确认。
  - 流水线里面的差错恢复有 **回退N步(滑动窗口)** 和 **选择重传**
  - 回退N步
    - <img src="/images/documents/计算机网络-第三章/GBN.png" style="zoom:100%">
    - 在rdt3的基础上最多允许不加确认的条件下传输N个分组。
    - 一旦超时，则重发从[base,nextseqnum-1]的**全部分组**。
    - 同时在本协议内，接收端丢弃所有失序分组，也就是说如果第i个分组出现丢包，则i后面的分组都会被接收端丢弃。
  - 选择重传
    - 发送方和接收方各自维护一个大小为N的窗口。
    - 发送方的事件和动作
      - 1.从上层收到数据：发送方检查在发送窗口中是否还有可以使用的序号，如果有则发送，没有则缓存数据或返回数据给上层。
      - 超时: 在选择重传中每个分组都有自己的逻辑定时器，工作原理和之前一样。
      - 收到ACK: 如果收到的ACK序号在窗口里，则标记对应项为确认被接收，如果序号等于send_base，则窗口前移一位。
    - 接收方的事件和动作
      - 分组序号在窗口内：正确接收，回传ACK，缓存分组，如果序号等于rcv_base，则将从rcv_base开始连续的缓存分组传给上层，窗口前移。
      - 分组序号在窗口前面(即该分组在之前已经被正确接收过)：仍然返回一个ACK给发送端，因为有可能上一个ACK发生丢包，此时发送端仍然认为接收端未接收，所以重传了过来。
      - 其他情况：忽略该分组。
    - 当窗口过大时，重传会导致接收端不知道该分组是重发分组还是新分组(序号一致)，所以为了避免这样的情况，窗口的大小要小于或等于序号空间大小的一半。
- 其他可见第五章ppt

### **TCP(传输控制)协议**
- TCP是点对点连接，在三次握手时确立了发送缓存和接收缓存，用户进程通过套接字向缓存中发送/收取数据，同时TCP协议也不时从发送缓存中拿取数据封装成TCP报文发送。
- TCP放入报文中的数据受限于**MSS(最大报文段长度)**，其通常为1460字节，原因是因为收到链路层帧长度限制，如以太网帧长度最大为1518字节，其中数据部分 **MTU(最大传输单元)** 为1500字节，然后要**除去TCP首部20字节和IP首部20字节**。

#### TCP报文结构
![报文格式](/images/documents/计算机网络-第三章/TCP报文格式.png)
- 序号是报文段数据首字节的编号(由TCP协议隐形编码)
- 确认号是期望下一次收到的序号。

#### 往返时间(RTT)与超时
- SampleRTT：从某段报文发出(交给IP)到该报文段的确认被收到之间的时间量。
- SampleRTT的测量：
  - 不为重传的报文计算SampleRTT。
  - 在某时刻只做一次测量，比如连续发送三个报文，只会测量其中一个的SampleRTT。
- RTT平均(指数平均): $EstimatedRTT = (1-\alpha)\cdot EstimatedRTT + \alpha\cdot SampleRTT$，一般$\alpha = 0.125$,对新样本的权重要大于旧样本，因为新样本更能放映当时拥塞情况。
- RTT偏差: $DevRTT = (1-\beta)\cdot DevRTT + \beta \cdot |SampleRTT - EstimatedRTT|$,一般$\beta = 0.25$
- 设置重传超时时间(RTO)-Jacobson's Algorithm:
  - $TimeoutInterval = EstimatedRTT + f \cdot DevRTT$, $f$通常取4.
  - 当出现超时情况是，$TimeoutInterval$采取指数后退(通常×2)，直到一个不是重传的报文段被确认，则得到新的$SampleRTT$,更新$EstimatedRTT和TimeoutIntreval$(Karn's Algorithm)

#### TCP协议的可靠传输
- TCP的可靠传输既有GBN的特点也有SR的特点。
- 发送端事件处理大致如下：
  ```C
  /*假设发送方不受TCP流量控制和拥塞控制，每一个数据小于MSS.*/
  NextSeqNum = InitialSeqNum
  SendBase = InitialSeqNum

  loop(永远){
    switch(event):
    
      case 从上层应用接收数据：
        生成序号为NextSeqNum的报文;
        if(定时器没有启动){
          启动定时器;
        }
        向IP传递报文;
        NextSeqNum += 数据字节数;
        break;

      case 超时：
        重传SendBase对应的TCP报文; /*和GBN不同之处*/
        TimeoutInterval *= 2;
        重启定时器; /*每次重传一个报文后都会重新启动定时器*/
        break;

      case 接收ACK, AN = y:
        if(y > SendBase){
          SendBase = y; /*采取累计确认*/
          重新计算TimeoutInterval;
          if(仍有发送且未确认报文){
            重启定时器;
          }
        }
        else{/*实际上此时y == SendBase*/
            y的冗余数量 += 1;
            if(y的冗余数量 == 3){
              /*快速重传*/
              立即重传序号为y对应的报文;
            }
        }
        break;
  }
  ```
- 接收端产生ACK的情况在RFC 5681中建议：
  - | 事件 | 动作 |
    |:----:|:----:|
    |具有**所期望序号**的按序报文段到达<br>且在此**之前的报文段都已经被确认**<br>即本报文段是当前状态下第一个接收但未被确认的报文|Delayed ACK，延迟ACK发送<br>(根据本地的计时器设定,<br>如果在短时间内还有可以接收的报文段，那么可以减少ACK的数量)|
    |具有**所期望序号**的按需报文段到达<br>且当前有一个报文段等待ACK传输，<br>即此时处于事件一的状态|立马发送ACK，确认这两个报文|
    |比**所期望序号大的失序**报文段到达，<br>即接收产生了空隙|立马发送一个冗余ACK|
    |收到能够填补间隙的报文段|如果该报文段序号是间隙的低端，<br>则马上发送ACK|
- 对TCP的一个修改意见是改成 **选择确认**，即有选择地确认收到地报文，而非采用累计确认。

#### 流量控制(Flow control)
- **速度匹配**： 流量控制就是使得发送方的发送速率和接收方的接受速率相匹配。
- 两方都维护一个滑动窗口(TCP中两者既是发送方也是接收方)
  - 接收方：
    - RcvBuffer表示接收缓存大小。LastByteRead:表示用户进程从缓存中读出的最后一个字节的编号。LastByteRcvd:表示接收方接收并放入缓存的最后一个字节编号。
    - $LastByteRcvd -  LastByteRead \leq RcvBuffer$,定义接收窗口大小$rwnd = RcvBuffer - [LastByteRcvd - LastByteRead]$
  - 发送方：
    - LastByteSent: 发送方发送的最后一个字节编号。LastByteAcked：发送方发送的字节中最后一个被确认的字节编号。
    - 需要保证$LastByteSent - LastByteAcked \leq rwnd$
- 为了防止rwnd = 0，且接收端没有数据需要发送给发送方，从而导致缓存清空后发送方并不知道从而不发送接下来的数据，当rwnd = 0时，发送方会继续发送 **只有一个字节** 的报文段，这个报文段会被接收方确认，当rwnd不再为0后，确认报文中会包含一个不为0的rwnd值。

#### TCP连接建立和断开
- 三次握手![三次握手](/images/documents/计算机网络-第三章/三次握手.png)
  - 三次握手的必要性：防止过期的SYN数据报影响。
  - **最后一个单纯的不带数据的ACK不占据seq，所以A发送的下一个数据报的seq仍然是x+1**
- 四次挥手![四次挥手](/images/documents/计算机网络-第三章/四次挥手.png)
  - 等待2MSL的必要性：可能最后A的ACK丢失，导致B重发FIN/ACK，这个2MSL能够保证A能够获得重发的数据报并回应。
- RFC状态转移图 
  - ![连接](/images/documents/计算机网络-第三章/TCP连接.png)
  - ![终止](/images/documents/计算机网络-第三章/TCP终止.png)
- 崩溃恢复
  - 连接过程中，一方crash,另外一方仍然认为在连接阶段，进入**半开状态**
  - 解决办法：
    - 未crash一方等待一个Persistence Time(尝试若干次都超时), 则主动关闭连接并通知使用者。
    - crash一方主动发送 **RST i** 来回应收到的i个分组，另一方收到后断开连接。

#### TCP拥塞控制
- TCP拥塞控制机维护一个cwnd变量(拥塞窗口)，有$LastByteSend - LastByteAcked \leq min\{cwnd,rwnd\}$
- 慢启动
  - 一开始cwnd = 1,之后每收到一个ACK那么cwnd加倍。
  - 当发生丢包现象(超时或者收到3个冗余ACK)，则ssthresh = cwnd/2,并将cwnd重置为1。
  - 当当前cwnd达到设置的阈值ssthresh时，进入拥塞避免模式。
- 拥塞避免
  - 进入拥塞避免模式后，每收到一个ACK或每过一个RTT，cwnd = cwnd + 1。
  - 当发生丢包现象(超时)，则ssthresh = cwnd/2，并将cwnd重置为1，重新进入慢启动状态。
  - 当发生丢包现象(收到3个冗余ACK)(此时会快速重传), 则ssthresh = cwnd/2, cwnd = ssthresh(+3),进入快速恢复。
- 快速恢复
  - 进入快速恢复后，每收到一个和之前一样的冗余ACK,cwnd = cwnd + 1.
  - 当收到重传报文的ACK后，cwnd = sstresh，然后进入拥塞避免状态.
- TCP拥塞控制常被称为**加性增乘性减-AIMD**拥塞控制方式。
