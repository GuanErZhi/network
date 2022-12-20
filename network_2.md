#  Wireshark 实验

> 因实作之间不是连续完成且实验环境不固定，导致某些地址没有保持全文一致（例如需要ping旁边计算机时将通常的手机开热点电脑连接的模式（在家）改成手机电脑连接公共WIFI（外面））。报告格式参考了棋歌教学网，报告内容主要附上实验过程的截图和回答棋歌教学网中相应的问题。

## 准备

###  Wireshark 

Wireshark 是非常流行的网络封包分析软件，可以截取各种网络数据包，并显示数据包详细信息，常用于开发测试过程各种问题定位。

###  Wireshark 的基本使用

- 选择对哪块网卡进行数据包捕获
选择菜单栏上 Capture -> Option ，勾选 WLAN 网卡（这里需要根据各自电脑网卡使用情况选择，简单的办法可以看使用的 IP 对应的网卡。
- 开始/停止捕获
选择工具栏上的开始/停止按键（一般是前面两个）
- 了解 Wireshark 主要窗口区域
	1. Display Filter （显示过滤器）
	用于设置过滤条件进行数据包列表过滤。
	2. Packet List Pane （数据包列表）
	用于显示捕获到的数据包，每个数据包包含编号，时间戳，源地址，目标地址，协议，长度，以及数据包信息。
	3. Packet Details Pane （数据包详细信息）
	用来查看协议中的每一个字段。
	4. Dissector Pane （数据包字节区）
- 设置数据包的过滤
	可以对协议、 IP 、端口进行过滤，并且可以结合相应逻辑运算符实现复杂的过滤效果。
	1. 抓包过滤器
	用于在抓取数据包前设置。
	2. 显示过滤器
	用于在抓取数据包后设置过滤条件进行过滤数据包（一般在网络不复杂的情况下使用）。
- 跟踪数据流

## 数据链路层
### 实作一 熟悉 Ethernet 帧结构

使用 Wireshark 任意进行抓包，熟悉 Ethernet 帧的结构，如：目的 MAC、源 MAC、类型、字段等。

![ Ethernet 帧的结构的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/1.1.1.png)

目的 MAC ：`c8:c2:fa:e5:9e:c8`

源 MAC ：`6c:6a:77:6a:b8:4b`

类型：IPV4（`0x0800`）

> 问题：
> 你会发现 Wireshark 展现给我们的帧中没有校验字段，请了解一下原因。

答：帧校验序列 FCS （4字节）：采用循环冗余校验码（ CRC ）用于检验帧在传输过程中有无差错。抓包软件抓到的是去掉前导同步码、帧开始分界符、 FCS 之外的数据， Wireshark 把8字节的前序和4字节的 FCS 都给过滤了。

### 实作二 了解子网内/外通信时的 MAC 地址

1. `ping` 你旁边的计算机（同一子网），同时用 Wireshark 抓这些包（可使用 icmp 关键字进行过滤以利于分析），记录一下发出帧的目的 MAC 地址以及返回帧的源 MAC 地址是多少？这个 MAC 地址是谁的？

![ ping 旁边的计算机的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/1.2.1.1.png)
![ ping 旁边的计算机的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/1.2.1.2.png)

发出帧的目的 MAC 地址：`c8:c2:fa:e5:9e:c8`

返回帧的源 MAC 地址：`c8:c2:fa:e5:9e:c8`

这个 MAC 地址是我的主机所处子网的网关 MAC 地址。

（这个部分我感到很奇怪，得到的实验结果和预期的想法不同，即我认为这个 MAC 地址是被我 `ping` 的旁边这台计算机的 MAC 地址。但找了很久错误都没发现。）

2. 然后 `ping qige.io` （或者本子网外的主机都可以），同时用 Wireshark 抓这些包（可 icmp 过滤），记录一下发出帧的目的 MAC 地址以及返回帧的源 MAC 地址是多少？这个 MAC 地址是谁的？

![ping qige.io 的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/1.2.2.1.png)
![ping qige.io 的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/1.2.2.2.png)
![ping qige.io 的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/1.2.2.3.png)

发出帧的目的 MAC 地址：`c8:c2:fa:e5:9e:c8`

返回帧的源 MAC 地址：`c8:c2:fa:e5:9e:c8`

这个 MAC 地址是我的主机所处子网的网关 MAC 地址。

3. 再次 `ping www.cqjtu.edu.cn` （或者本子网外的主机都可以），同时用 Wireshark 抓这些包（可 icmp 过滤），记录一下发出帧的目的 MAC 地址以及返回帧的源 MAC 地址又是多少？这个 MAC 地址又是谁的？

![ping www.cqjtu.edu.cn 的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/1.2.3.1.png)
![ping www.cqjtu.edu.cn 的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/1.2.3.2.png)

发出帧的目的 MAC 地址：`c8:c2:fa:e5:9e:c8`

返回帧的源 MAC 地址：`c8:c2:fa:e5:9e:c8`

这个 MAC 地址是我的主机所处子网的网关 MAC 地址。

> 问题
> 通过以上的实验，你会有以下发现，并回答为什么？
> 1. 访问本子网的计算机时，目的 MAC 就是该主机的
> 2. 访问非本子网的计算机时，目的 MAC 是网关的

答：网关是一个网络通向其他网络的“关口”，因为在子网内的计算机通信不需要经过网关，可以直接查询 MAC 地址并建立连接，所以目的 MAC 就是该主机的；而访问子网外的计算机需要先将数据发送到网关处，然后由 ARP 或者 DNS 解析得到 MAC 地址后到达目的子网发送到目的地址，所以目的 MAC 就是网关的。

### 实作三 掌握 ARP 解析过程

1. 为防止干扰，先使用 `arp -d *` 命令清空 arp 缓存
2. `ping` 你旁边的计算机（同一子网），同时用 Wireshark 抓这些包（可 arp 过滤），查看 ARP 请求的格式以及请求的内容，注意观察该请求的目的 MAC 地址是什么。再查看一下该请求的回应，注意观察该回应的源 MAC 和目的 MAC 地址是什么。

![ping 旁边的计算机的 ARP 效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/1.3.1.1.png)
![ping 旁边的计算机的 ARP 效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/1.3.1.2.png)
（截图的时候没点到回应的数据包）

请求的目的 MAC 地址：`ff:ff:ff:ff:ff:ff`

回应的源 MAC 地址：`c8:c2:fa:e5:9e:c8`（网关的 MAC 地址）

回应的目的 MAC 地址：`6c:6a:77:6a:b8:4b`（我的计算机的 MAC 地址）

3. 再次使用 `arp -d *` 命令清空 arp 缓存
4. 然后 `ping qige.io` （或者本子网外的主机都可以），同时用 Wireshark 抓这些包（可 arp 过滤）。查看这次 ARP 请求的是什么，注意观察该请求是谁在回应。

![ping 子网外的计算机的 ARP 效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/1.3.2.1.png)
![ping 子网外的计算机的 ARP 效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/1.3.2.2.png)

请求的是：谁有`192.168.43.221`的 MAC 地址，IP 地址为`192.168.43.221`的计算机（即我的计算机）

> 问题：
> 通过以上的实验，应该会有以下发现，并回答为什么？
> 1. ARP 请求都是使用广播方式发送的
> 2. 如果访问的是本子网的 IP，那么 ARP 解析将直接得到该 IP 对应的 MAC；如果访问的非本子网的 IP， 那么 ARP 解析将得到网关的 MAC。

答：
1. 因为在使用 `ping` 命令之前，首先清空了 ARP 缓存，因为查询不到 IP 地址与 MAC 地址的映射关系，无法传输数据包，所以采用 ARP 广播帧确认该 IP 的地址。
2. 网关是通信子网的”关口“，因为访问子网内的计算机不需要经过网关；而访问子网外的需要先发送到网关处再进行转发。

## 网络层
### 实作一 熟悉 IP 包结构

使用 Wireshark 任意进行抓包（可用 ip 过滤），熟悉 IP 包的结构，如：版本、头部长度、总长度、TTL、协议类型等字段。

![ IP 包的结构的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/2.1.1.png)

> 问题：
> 为提高效率，我们应该让 IP 的头部尽可能的精简。但在如此珍贵的 IP 头部你会发现既有头部长度字段，也有总长度字段。请问为什么？

答：头部长度的作用：为了方便接收方的网络层知道从什么地方开始取数据。总长度字段的作用：为了方便接收方的网络层知道传输的数据包含哪些，如果没有该部分，当数据链路层在传输时，对数据进行了填充，对应的网络层不会把填充的部分给去掉。

### 实作二 IP 包的分段与重组

根据规定，一个 IP 包最大可以有 64K 字节。但由于 Ethernet 帧的限制，当 IP 包的数据超过 1500 字节时就会被发送方的数据链路层分段，然后在接收方的网络层重组。

缺省的，`ping` 命令只会向对方发送 32 个字节的数据。我们可以使用 `ping 202.202.240.16 -l 2000` 命令指定要发送的数据长度。此时使用 Wireshark 抓包（用 `ip.addr == 202.202.240.16` 进行过滤），了解 IP 包如何进行分段，如：分段标志、偏移量以及每个包的大小等

![IP 包分段的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/2.2.1.png)
![IP 包分段的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/2.2.2.png)

答：分了两个包，一个包大小1500、偏移量为0，一个包大小548、偏移量为1480

> 问题：
> 分段与重组是一个耗费资源的操作，特别是当分段由传送路径上的节点即路由器来完成的时候，所以 IPv6 已经不允许分段了。那么 IPv6 中，如果路由器遇到了一个大数据包该怎么办？

答：该路由器丢弃该数据包，并向发送方发回一个”分组太大”的 ICMP 差错报文，于是发送方会使用较小长度的 IP 数据报重发数据。

### 实作三 考察 TTL 事件

在 IP 包头中有一个 TTL 字段用来限定该包可以在 Internet上传输多少跳（hops），一般该值设置为 64、128等。

在验证性实验部分我们使用了 `tracert` 命令进行路由追踪。其原理是主动设置 IP 包的 TTL 值，从 1 开始逐渐增加，直至到达最终目的主机。

请使用 `tracert www.baidu.com` 命令进行追踪，此时使用 Wireshark 抓包（用 `icmp` 过滤），分析每个发送包的 TTL 是如何进行改变的，从而理解路由追踪原理。

![路由追踪原理的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/2.3.1.png)
![路由追踪原理的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/2.3.2.png)
![路由追踪原理的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/2.3.3.png)

路由追踪原理：

Tracert 先发送 TTL 为 1 的回应数据包，并在随后的每次发送过程将 TTL 递增 1，直到目标响应或 TTL 达到最大值，从而确定路由。通过检查中间路由器发回的“ICMP 已超时”的消息确定路由。

> 问题：
> 在 IPv4 中，TTL 虽然定义为生命期即 Time To Live，但现实中我们都以跳数/节点数进行设置。如果你收到一个包，其 TTL 的值为 50，那么可以推断这个包从源点到你之间有多少跳？

答： TTL 一般是 2 的整数次幂，如果 TTL 为 50 ，那就找到距离 50 最近的 2 的整数次幂就是 64 ，由于 TTL 的值在每一跳后会减 1 ，所以当收到一个包的 TTL = 50 时，则 64 - 50 = 14 ，说明经过了 14 跳。

## 传输层
### 实作一 熟悉 TCP 和 UDP 段结构

1. 用 Wireshark 任意抓包（可用 tcp 过滤），熟悉 TCP 段的结构，如：源端口、目的端口、序列号、确认号、各种标志位等字段。

![TCP](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/3.1.1.png)

源端口：`54815`

目的端口：`8080`

序列号：1

确认号：1


2. 用 Wireshark 任意抓包（可用 udp 过滤），熟悉 UDP 段的结构，如：源端口、目的端口、长度等。

![UDP](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/3.1.2.png)

源端口：`64878`

目的端口：`53`

长度：42

> 问题：
> 由上大家可以看到 UDP 的头部比 TCP 简单得多，但两者都有源和目的端口号。请问源和目的端口号用来干什么？

答：在一台计算机上，一个进程对应一个端口。端口的作用就是用来唯一标识这个进程。源端口标识发起通信的进程，目的端口标识接收通信的进程。有了端口号，接收到报文后才能够知道将报文发送到对应的哪个进程。

### 实作二 分析 TCP 建立和释放连接

1. 打开浏览器访问 qige.io 网站，用 Wireshark 抓包（可用 tcp 过滤后再使用加上 `Follow TCP Stream`），不要立即停止 Wireshark 捕获，待页面显示完毕后再多等一段时间使得能够捕获释放连接的包。
2. 请在你捕获的包中找到三次握手建立连接的包，并说明为何它们是用于建立连接的，有什么特征。

![握手](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/3.2.1.1.png)

![握手](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/3.2.1.2.png)

特征：

- 第一次握手，同步位（SYN）是1，确认位（ACK）是0；
- 第二次握手，同步位（SYN）是1，确认位（ACK）是1；
- 第三次握手，同步位（SYN）是0，确认位（ACK）是1。

3. 请在你捕获的包中找到四次挥手释放连接的包，并说明为何它们是用于释放连接的，有什么特征。

![挥手](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/3.2.2.1.png)

![挥手](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/3.2.2.2.png)

特征：确认位（ACK）仍然为1和终止控制位（FIN）变为1。

> 问题一:
> 去掉 `Follow TCP Stream`，即不跟踪一个 TCP 流，你可能会看到访问 `qige.io` 时我们建立的连接有多个。请思考为什么会有多个连接？作用是什么？

答：它们之间的连接是属于短连接，一旦数据发送完成后，就会断开连接。虽然了断开连接，但是页面还是存在，由于页面已经被缓存下来，一旦需要重新发送数据，就要再次进行连接。这样的连接，是为了实现多个用户进行访问，对业务频率不高的场合，节省通道的使用，不让其长期占用通道。

> 问题二:
> 我们上面提到了释放连接需要四次挥手，有时你可能会抓到只有三次挥手。原因是什么？

答：客户端向服务端发送断开连接的请求为第一次挥手，服务端向客户端回复同意断开为第二次，然后服务端向客户端发送断开的请求为第三次挥手，客户端向服务端回复同意断开连接为第四次挥手。三次挥手是将服务器向客户端发送断开连接和回复同意断开连接合成一次挥手，其他两次挥手不变。也就是说，如果对方也没有数据发给本端，那么对方也会发送FIN给本端，使得二三次挥手合并为一次。

## 应用层
### 实作一 了解 DNS 解析

1. 先使用 `ipconfig /flushdns` 命令清除缓存，再使用 `nslookup qige.io` 命令进行解析，同时用 Wireshark 任意抓包（可用 dns 过滤）。
2. 你应该可以看到当前计算机使用 UDP，向默认的 DNS 服务器的 53 号端口发出了查询请求，而 DNS 服务器的 53 号端口返回了结果。
3. 可了解一下 DNS 查询和应答的相关字段的含义

![DNS 的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/4.1.1.1.png)
![DNS 的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/4.1.1.2.png)
![DNS 查询和应答相关字段的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/ps2/1.png)
（图片来自C语言中文网）

DNS 查询和应答的相关字段的含义：
- 事务 ID：DNS 报文的 ID 标识。对于请求报文和其对应的应答报文，该字段的值是相同的。通过它可以区分 DNS 应答报文是对哪个请求进行响应的。
- 查询名：一般为要查询的域名，有时也会是 IP 地址，用于反向查询。
- 查询类型：DNS 查询请求的资源类型。通常查询类型为 A 类型，表示由域名获取对应的 IP 地址。
- 域名：DNS 请求的域名。
- 生存时间：以秒为单位，表示资源记录的生命周期，一般用于当地址解析程序取出资源记录后决定保存及使用缓存数据的时间。它同时也可以表明该资源记录的稳定程度，稳定的信息会被分配一个很大的值。

> 问题：
> 你可能会发现对同一个站点，我们发出的 DNS 解析请求不止一个，思考一下是什么原因？

答： DNS 不止一个的原因是 DNS 解析过程是先从浏览器的 DNS 缓存中检查是否有这个网址的映射关系，如果有，就返回 IP ，完成域名解析；如果没有，操作系统会先检查自己本地的 hosts 文件是否有这个网址的映射关系，如果有，就返回 IP ，完成域名解析；如果没有，电脑就要向本地 DNS 服务器发起请求查询域名；本地 DNS 服务器拿到请求后，先检查一下自己的缓存中有没有这个地址，有的话直接返回；没有的话本地 DNS 服务器会从配置文件中读取根 DNS 服务器的地址，然后向其中一台发起请求；直到获得对应的 IP 为止。

### 实作二 了解 HTTP 的请求和应答

1. 打开浏览器访问 qige.io 网站，用 Wireshark 抓包（可用http 过滤再加上 `Follow TCP Stream`），不要立即停止 Wireshark 捕获，待页面显示完毕后再多等一段时间以将释放连接的包捕获。
2. 请在你捕获的包中找到 HTTP 请求包，查看请求使用的什么命令，如：`GET, POST`。并仔细了解请求的头部有哪些字段及其意义。

![请求](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/4.2.1.1.png)

![请求](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/4.2.1.2.png)

请求的头部有：

- Accept 指定客户端能够接收的内容类型
- Cache-Control 指定请求和响应遵循的缓存机制
- Date 请求发送的日期和时间
- Host 指定请求的服务器的域名和端口号 

3. 请在你捕获的包中找到 HTTP 应答包，查看应答的代码是什么，如：`200, 304, 404` 等。并仔细了解应答的头部有哪些字段及其意义。

![应答](https://github.com/GuanErZhi/network/blob/main/network_pic/pic2/4.2.2.png)

请求的头部有：

- Age 从原始服务器到代理缓存形成的估计时间
- Expires 相应过期的日期和时间
- Last-Modified 请求资源的最后修改时间
- Server web服务器软件名称

> 建议：
> HTTP 请求和应答的头部字段值得大家认真的学习，因为基于 Web 的编程中我们将会大量使用。如：将用户认证的令牌信息放到头部，或者把 cookie 放到头部等。

> 问题：
> 刷新一次 qige.io 网站的页面同时进行抓包，你会发现不少的 `304` 代码的应答，这是所请求的对象没有更改的意思，让浏览器使用本地缓存的内容即可。那么服务器为什么会回答 `304` 应答而不是常见的 `200` 应答？

答： `200` 应答是完全的将内容发送给客户端。 `304` 应答可以让浏览器直接加载 cache 缓存里的文件，不需要重新向服务器发送请求再完全地返回数据，这样可以提高加载速度，节约时间，使得花费资源较少。
