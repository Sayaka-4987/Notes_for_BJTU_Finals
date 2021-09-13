# 计算机网络原理笔记

考勤 5%、作业 15%、实验 20%、期末（英文试卷、半开卷**只能带教材**、中英文答题均可） 60%

参考资料：[中科大郑烇、杨坚全套《计算机网络（自顶向下方法 第7版）》](https://www.bilibili.com/video/BV1JV411t7ow)



## 第一章 Computer Network and the Internet

~~2021/9/6：目前进度是 1.2 网络边缘（network edge）讲完了；~~

2021/9/8：目前进度 1.4 Delay, Loss, and Throughput in Packet-Switched Networks；



### 中英对照名词表

#### 1.1, 1.2 网络边缘

| 中英对照                                            | 概念（待编辑补充）                                           | 备注 |
| --------------------------------------------------- | ------------------------------------------------------------ | ---- |
| 网络边缘（The Network Edge）                        | 主机系统及其运行的应用程序；                                 |      |
| 主机（hosts）、<br>终端系统（end systems）          | 在计算机网络术语中，手机、笔记本电脑、汽车这些设备都被称为主机或终端系统； |      |
| 分组交换机（packet switches）                       | 终端之间由通信链路和分组交换机连接，分组交换机由交换单元、接口单元和控制单元三部分组成； |      |
| 路由器（router）                                    | 负责将包转发到它们的最终目的地；<br>路由器一般用于网络核心； |      |
| 链路层交换机（link-layer switches）                 | 负责将包转发到它们的最终目的地；<br/>链路层交换机一般用于接入网络（access networks）； |      |
| 带宽（bandwidth）                                   | 单位 bps，每秒传输多少比特；1K bps = 1024 bps；<br>上下行带宽不对称，一般下行的更宽； |      |
| 协议（protocol）                                    | 网络协议的简称，是通信计算机双方必须共同遵守的约定，怎样建立连接、怎样互相识别； |      |
| 互联网服务供应商（ISP，Internet Service Providers） | 终端系统需要通过互联网服务提供商接入互联网；                 |      |
| 传输控制协议（TCP，Transmission Control Protocol）  | 两个最重要的互联网协议之一；<br>因特网的主要协议通称为 TCP/IP 协议； |      |
| 互联网协议（IP，Internet Protocol）                 | 两个最重要的互联网协议之一；<br>IP协议指定路由器和终端系统之间发送和接收报文的格式； |      |
| 分布式应用程序（distributed applications）          | 涉及多个相互交换数据的端系统的互联网应用程序；               |      |
| 套接字接口（socket interface）                      | TCP/IP 网络的应用程序编程接口（API，Application Programming Interface） |      |
| 客户端（clients）                                   | 两类主机之一，**为客户提供本地服务的程序**；<br>一般是桌面和移动 pc 、智能手机等； |      |
| 服务器（servers）                                   | 两类主机之一，**对客户端提供服务的程序**；<br>一般是存储 Web 页面、流媒体、转发电子邮件的、更大的机器； |      |
| 数字用户线（DSL，Digital Subscriber Line）          | 基于普通电话线的宽带接入技术，可以在一对铜质双绞线上同时传送数据和话音信号； |      |
| 光纤入户（FTTH，fiber to the home）                 | 指接入线路全部采用光纤，光纤直接接入到用户家中；             |      |
| 长期演进技术（LTE，Long-Term Evolution）            | 通用移动通信技术的长期演进（翻译比较怪）；<br>还有一个别名是 3G 演进型系统； |      |
| 以太网（Ethernet）                                  | 现在使用最广泛的局域网技术标准；需要经由路由器才能接入外网； |      |
| 双绞线（TP，twisted pair）                          | 由两根具有绝缘保护层的铜导线组成的传输介质；                 |      |
| 同轴电缆（coaxial cable）                           | 由四层材料组成的电缆，分别是内导体、介电绝缘层、另一个导电层、和绝缘层； |      |
| 光缆（fiber optic cable）                           | 由玻璃或塑料制成的纤维，可作为光传导工具；                   |      |



#### 1.3 网络核心

| 中英对照                                             | 概念（待编辑补充）                                           | 备注 |
| ---------------------------------------------------- | ------------------------------------------------------------ | ---- |
| 网络核心（The Network Core）                         | 路由器的网状网络；<br>网络 = 网络边缘 + 网络核心 + 接入网络和通信链路； |      |
| 呼叫（call）                                         | 端到端的资源被分配给从源端到目标端的呼叫；                   |      |
| 分组交换（Packet Switching）                         | 两种通过链路和交换机传输数据的基本方法之一；<br>将报文拆分成多个分组（packet），在链路的输入端对每个分组进行 **存储转发传输**，接受端接受完重新组装报文；<br>用排队等待的方式处理数据冲突，如果路由器的缓存已满（等待队列满）就会发生丢包，类似不需要预订的餐厅； |      |
| 电路交换（Circuit Switching）                        | 两种通过链路和交换机传输数据的基本方法之一；<br/>发送端会预留端到端资源（即使该呼叫不进行数据传递），会保障在网络链路中分配一个恒定速率用于传输数据，资源在会话期间被保留，类似有预约制的餐厅；<br/>建立连接耗时较长，不适合有突发性的计算机通信，正在逐步被取代； |      |
| 频分多路复用（FDM，Frequency-Division Multiplexing） | 将用于传输信道的总带宽划分成若干个子频道，每个频道传输一路信号； |      |
| 时分多路复用（TDM，Time-Division Multiplexing）      | 将时间分成周期循环的一些小段，每段时间长度是固定的，每个时段用来传输一个子信道，传输时间不重合；<br>一般用于数字信号传输； |      |
| 统计多路复用（statistical multiplexing）             | 数据包序列没有固定的模式，带宽资源按需共享；                 |      |
| 网络服务提供商（ISPs，Internet Service Providers）   | 能提供为接入互联网而进行的一系列配套服务；<br>现实世界中同级别 ISP 层次连接不付费； |      |



#### 1.4 网络延迟、损失和吞吐量

| 中英对照                               | 概念（待编辑补充）                                           | 备注 |
| -------------------------------------- | ------------------------------------------------------------ | ---- |
| 节点处理时延（nodal processing delay） | 一般是微秒级别；                                             |      |
| 排队时延（queuing delay）              | 取决于路由器的拥塞程度，La/R 是曲线，每组包各不相同；<br>一般是微秒到毫秒之间； |      |
| 传输时延（transmission delay）         | 也叫发送时延，一般是时延的主要来源；<br>数据从 **开始发送** 到 **发送端发送完成** 需要的时间；<br>是关于该分组长度和该链路传输速率的函数；<br>L = 包长度（packet length） ，单位 bits<br/>R = 链路的带宽（link bandwidth）， 单位 bps<br/>传输时延（transmission delay） = L/R |      |
| 传播时延（propagation delay）          | 发送端 **开始发送** 到 **接收端收到数据** 所需要的全部时间；<br>是关于发送方和接收方之间的距离的函数；<br>d = 物理链路长度（length of physical link）<br/>s = 信号传播速度（propagation speed） (~2x108 m/sec)<br/>传播时延（propagation delay）= d/s |      |
| 总节点延迟（total nodal delay）        | <img src=".\media\公式图片1.png" style="zoom:48%;" /><br>上述四项延迟之和； |      |
| 丢包率（Packet loss）                  | 缓存满时再遇到新数据就会丢包；此时可以视（可靠性）需求重发或不重发该包； |      |
| 吞吐量（Throughput）                   | 是速率，单位 bits/time unit（bps）；<br>区分瞬时（instantaneous）吞吐量和平均吞吐量（average）； |      |



#### 1.5 层次结构

| 中英对照                  | 概念（待编辑补充） | 备注 |
| ------------------------- | ------------------ | ---- |
| 层次（layers）            |                    |      |
| 服务（services）          |                    |      |
| 服务模型（service model） |                    |      |
| 协议栈（protocol stack）  |                    |      |
| 段（segment）             |                    |      |
| 数据报文（datagrams）     |                    |      |
| 帧（frames）              |                    |      |
| 封装（encapsulation）     |                    |      |



#### 1.6 网络攻击

| 中英对照                                        | 概念（待编辑补充）                     | 备注 |
| ----------------------------------------------- | -------------------------------------- | ---- |
| 恶意软件（malware）                             |                                        |      |
| 木马（spyware）                                 |                                        |      |
| 病毒（virus）                                   | 一般需要用户主动进行一些操作才能感染； |      |
| 蠕虫（worm）                                    | 一般不需要用户主动进行操作就会感染；   |      |
| 自我复制（self-replicating）                    |                                        |      |
| 拒绝服务攻击（denial-of-service (DoS) attacks） |                                        |      |
| 分布式拒绝服务攻击（DDoS，distributed DoS）     |                                        |      |
| 数据包嗅探（packet sniffer）                    |                                        |      |
| IP 欺骗（IP spoofing）                          |                                        |      |



### TCP/IP 五层模型

TCP/IP 是先有实物后有模型；

每一层的功能依赖于下一层，同时向上一层提供服务：

| 协议栈                | 功能                                                         |
| --------------------- | ------------------------------------------------------------ |
| 应用层（Application） | 为人类用户或者其他应用进程提供网络应用服务；<br>在应用层进行 OSI 模型表示层和会话层的工作<br>协议举例：FTP、SMTP、HTTP、DNS |
| 传输层（Transport）   | 进行主机之间的数据传输，在网络层提供的端到端通信基础上，细分为进程到进程，<br/>将不可靠的通信变成可靠的通信；协议举例：TCP、UDP |
| 网络层（Network）     | 为数据选择路由以完成主机和主机之间的通信，端到端通信，不可靠；<br/>协议举例：IP、路由协议 |
| 链路层（Link）        | 相邻两个网络节点间的数据传输、点到点通信，可靠或不可靠；<br/>协议举例：PPP、（WLAN 的）802.11、以太网（Ethernet） |
| 物理层（Physical）    | 提供二进制比特流传输；                                       |



### OSI ISO 七层模型

| 层次栈 | 功能                                                       |
| ------ | ---------------------------------------------------------- |
| 应用层 | /                                                          |
| 表示层 | 允许应用解释传输的数据，例如加密、压缩、机器相关的表示转换 |
| 会话层 | 进行数据交换的同步、检查点、恢复                           |
| 传输层 | /                                                          |
| 网络层 | /                                                          |
| 链路层 | /                                                          |
| 物理层 | /                                                          |



### 练习题目

#### Circuit Switching, TDM 用时定量计算例题

来自课件 Chapter1_final.ppt P29

> How long does it take to send a file of 640,000 bits from host A to host B over a circuit-switched network?
>
> • All links are 1.536 Mbps
>
> • Each link uses TDM with 24 slots/sec
>
> • 500 msec to establish end-to-end circuit

解：1.536 M / 24 = 64,000；640,000 / 64000 + 500 ms = 10s + 0.5s = 10.5s；



#### Packet Switching 定量计算例题

来自课件 Chapter1_final.ppt P34

> packet switching: 
>
> • with 35 users, probability > 10 active at same time is less than .0004 *

解：概率计算过程见下图（不会用内联公式是这样的）

<img src=".\media\微信截图_20210908092434.png" style="zoom:38%;" />



