# 计算机网络-第四章


# 第四章
<!-- more -->

## 转发和交换(路由选择)
### 转发
- 是网络层：数据平面实现的唯一功能。
- 是指将一个分组从一个输入链路接口转移到适当的输出链路接口的路由器本地动作，时间尺度很短。
### 交换(路由选择)
- 是网络层：控制平面的功能，当分组从发送方流向接收方时，网络层需要决定这些分组采用的路由或路径，计算这些路径的算法称为 **路由选择算法**
- 是指确定分组从发送方到接收方所采取的端到端路径的网络范围处理过程，事件尺度相对更长。
### 转发表
- 每个网络路由器都有一个转发表，路由器在检测到分组首部的若干字段后，用这些首部值在转发表中索引，从而确定分组转发的输出链路接口。
- **链路层交换机**时根据链路层帧首部的字段(如MAC地址)进行转发，而 **路由器**是根据网络层数据报中的首部(如IP地址)进行转发

## 路由器工作原理
### 路由器体系结构
![体系结构](/images/documents/计算机网络-第四章/路由器结构.png)
- 交换
  - 经内存交换：通过终端路由选择处理器
  - 经总线交换：通过给分组打上标签，到达本地输出链路后去除标签。
  - 经互联网络交换：允许并行，交换时将沿途结点封锁。
### 基于目的地的转发
- 路由器转发表采用前缀匹配的方式生成表项，对于每一个链路接口，其目的地址范围的前缀为表项中的前缀匹配。
- 在收到分组时，路由器用分组的目的地址和表项中的前缀匹配，匹配成功则将该分组转发到对应的链路接口。
- 当存在多个成功匹配时，采用 **最长前缀匹配规则**，转发到匹配长度最长的那个链路接口。

### 分组调度
- 排队的分组如何经输出链路传输。
- **先进先出(FIFO)**
- **优先权排队**
  - 将到达输出链路的分组按照优先级分成多个类，优先级高的类先传输，类内采用FIFO的方式。
  - **非抢占式优先权排队**：一旦分组开始被传输，即使此时有更高优先级的分组到达，该分组的传输也不会被打断。
- **循环排队规则**
  - 也将分组分成多个类，循环调度器在类之间轮流传输。
- **加权公平排队** 
  - 和循环排队类似，但是在第i个类有需要发送的分组的任何时间间隔中，都能保证该类等待发送的分组中有$w_i/(\sum w_j)$的部分接收到了发送服务。
  
## **网际协议：IP**
### **IPv4**
#### IPv4数据报格式 ![IPv4](/images/documents/计算机网络-第四章/IPv4.png)
- 版本号：规定了数据报IP协议版本，路由器根据该部分决定如何识别解释后面的部分
- 首部长度：一般为20字节。
- 服务类型：说明该IP数据报是什么类型。
- 数据报长度：整个IP数据报总长度。
- 标识，标志，片偏移：和IP分片相关。
- 寿命(TTL): 确保数据报不会再网络中循环，没经过一个路由器该值-1，为0时丢弃。
- 上层协议：指示了IP数据报的数据部分应该交给哪一个特定的运输层协议。
- 首部校验和：帮助路由器检测IP数据报中的比特错误。
- 源/目的IP地址，选项，数据。
#### IPv4数据报分片
- 分片原因：由于不同的链路层协议的 **最大传送单元**不一样，有时一个链路层帧并不能完全承载一个IP数据报的内容，故需要将IP数据报分片。
- **最大传送单元**：一个链路层帧能够承载的最大数据量。
- IPv4将重组IP数据报的工作交给了端系统。
- IPv4数据报分片利用**标识，标志，片偏移**三个字段：   
  - 发送主机通常将它发送的每一个数据报的标识号依次加1.
  - 分片后每个片(一个小的数据报)的源地址，目的地址，标识号一致，接收方可以通过标识号判断两个片是否属于同一个大的IP数据报。
  - 将最后一个片的标志为设为0，其他片的标志为设为1。从而让目的主机确认是否收到最后一个片。
  - 为了目的主机能够按照正确的顺序重新组装IP数据报，每一个片的片偏移字段指定了其数据部分在原来数据报数据中的位置。
  ![分片](/images/documents/计算机网络-第四章/IPv4分片.png)
  - 偏移量/8是因为IPv4分片时规定用8个字节为一个计量单位，从而在有限的偏移字段中存储更多信息。

#### IPv4编址
- 一个IP地址和一个接口相关联而非拥有接口的主机或路由器。
- IP地址长为32比特，表示方式采用**点分十进制记法**，如193.32.216.9
- 多个接口在通过一个 **不包含路由器**的网络互联起来，成为一个子网，这个子网中的接口IP地址拥有相同的前缀，IP编址为子网分配一个地址如233.1.1.0/24,其中/24可称为 **子网掩码**(比如/24就等价于子网掩码255.255.255.0)
- **子网**：分开主机和路由器每个接口，产生隔离的网络岛，使用接口端接这些网络的端点。这些隔离网络中每一个都是一个子网。
- 传统ABC类IP地址
  - **A类**IP地址网络部分长度为**8比特**，**B类**IP地址网络部分长度为**16比特**，**C类**IP地址网络部分长度为**24比特**。
  - 固定的网络部分长度使得在实际使用上比较麻烦，比如C类可分配太少，而B类又太多，造成浪费。
- IP广播地址255.255.255.255，使用广播地址作为目的地址时，会被交换机发送到子网中所有其他主机处，路由器也会有选择地向邻近子网发送。
- **无类别域间路由选择(CIDR)**
  - 将子网的概念一般化，没有了ABC类地址的分别。
  - 将IP地址表示为a.b.c.d/x，而/x则指示了IP地址的第一部分即网络部分，通常该部分被称为 **前缀**
  - 地址剩余的32-x部分可以用于分配组织内部设备使用。
- 编址过程
  - 获取地址：组织可以通过向ISP申请获得一块地址。
  - 分配主机地址：通常使用 **动态主机配置协议(DHCP)** 完成，DHCP是即插即用的，能够保证主机每次连接网络时能够获得相同的IP地址或者被分配一个临时的IP地址，子网内可以有专门的DHCP服务器或者使用路由器来担任DHCP中继代理，过程如下：
    - **DHCP服务器发现**：新到达的主机需要使用 **DHCP发现报文** 来开始和子网中DHCP服务器(或中继代理)的交互，该报文的**目的地址**使用 **广播地址**，**源地址**使用 **0.0.0.0**
    - **DHCP服务器提供**：DHCP服务器收到发现报文后，用 **DHCP提供报文**回应，给出推荐的IP地址，子网掩码，和IP地址租用期。此时目的地址仍然使用广播地址。
    - **DHCP请求**：新到达的主机从来自多个DHCP服务器的DHCP提供报文中选择一个，并向选中的服务器使用 **DHCP请求报文** 进行响应。
    - **DHCP ACK**：服务器用 **DHCP ACK报文**对请求报文进行回应。
  - ![DHCP](/images/documents/计算机网络-第四章/DHCP.png)

#### 网络地址转换
- 随着子网(家庭办公室等)的大量出现，为了简化分配IP地址的过程，使用 **网络地址转换(NAT)**
- NAT使能路由器对于外部来说可以就是一个具有单一IP地址的单一设备，而其实际上对外部隐藏了内部网络的细节。
- 内部网络使用的IP地址是RFC 1918中保留的IP地址空间之一，用于家庭网络等**专用网络**或者**具有专用地址的地域**(即其地址只对该网络中设备有意义)
- ![NAT](/images/documents/计算机网络-第四章/NAT.png)
  - 内部主机向外通信，发送源地址为主机IP地址，端口号随机生成的报文。
  - NAT使能路由器接收到报文，改变源地址为自身IP地址，生成一个不在NAT表中的端口号，发送报文，随后添加NAT表项。
  - 外部主机收到后，发送目的地址为路由器IP地址，端口号为路由器生成端口号的响应报文。
  - NAT使能路由器接收到响应报文，通过查NAT表，来转发给内部主机。

### **IPv6**
#### IPv6数据报格式
![IPv6](/images/documents/计算机网络-第四章/IPv6.png)
- 说明：
  - 版本号：和IPv4类似，但是简单的改为4或6不能完成IPv4和IPv6之间的转换。
  - 流量类型(Traffic class): 含义和IPv4中服务类型类似，说明该数据报的类型。
  - 流标签(flow label)：用于标识一条数据报的流，对一条流中的某些数据报给出优先权。
  - 有效荷载长度：给出数据报中数据部分的长度。
  - 下一个首部：指出数据报中的数据部分应该交付到运输层的那个协议(如TCP或者UDP)
  - 跳限制：和IPv4中寿命(TTL)用处一致。
  - 源/目的地址：从原来的32比特改为了128比特。
- **从上面可以发现IPv6的首部长度为40字节。**
- IPv6中删除了分片相关的标志，且规定数据报的分片和重新组装只能在目的主机和源主机上进行，而不能再像IPv4中一样可以在中间路由器上进行，如果中间路由器收到的IPv6数据报太大无法发往输出链路，则只需要丢弃该数据报，并向发送方发送一个"分组太大"的ICMP差错报文(见第五章)
- IPv6还删除了首部校验和，因为运输层协议(TCP与UDP)和链路层(以太网协议)都有差错检测，所以在网络层不需要再进行检测。
#### IPv4到IPv6的迁移
- 建隧道
  - 两台IPv6路由器之间的连续的IPv4路由器合成为一个隧道。
  - 隧道入口处的IPv6路由器，会将IPv6报文当作数据封装在IPv4报文的数据部分，将IPv4数据报的目的地址设为隧道出口处的IPv6路由器，并将IPv4数据报的上层协议设为41(表明数据部分是一个IPv6数据报)
  - 隧道中的IPv4路由器就像处理正常的IPv4数据报一样转发。
  - 出口处的IPv6路由器根据IPv4数据报中上层协议为41从而取出中间的IPv6数据报，然后进行相关操作。
  ![IPv6](/images/documents/计算机网络-第四章/tunnel.png)

### 通用转发和SDN
TBD
