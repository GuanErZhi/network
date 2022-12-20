# 验证性实验

> 因实作之间不是连续完成且实验环境不固定，导致某些地址没有保持全文一致。报告格式参考了棋歌教学网，报告内容主要附上实验过程的截图和回答棋歌教学网中相应的问题。

## ipconfig

> `ipconfig` 是微软操作系统的计算机上用来控制网络连接的一个命令行工具。它主要用来显示当前网络连接的配置信息（`/all` 参数）。

### 实作一

使用 `ipconfig/all` 查看自己计算机的网络配置，尽可能明白每行的意思，特别注意 `IP` 地址、子网掩码 `Subnet Mask`、网关 `Gateway`。

![ ipconfig /all 的效果1](https://github.com/GuanErZhi/network/blob/main/network_pic/pic1/1_1_1.1.png)
![ ipconfig /all 的效果2](https://github.com/GuanErZhi/network/blob/main/network_pic/pic1/1_1_1.2.png)
![ ipconfig /all 的效果3](https://github.com/GuanErZhi/network/blob/main/network_pic/pic1/1_1_1.3.png)
![ ipconfig /all 的效果4](https://github.com/GuanErZhi/network/blob/main/network_pic/pic1/1_1_1.4.png)

IP 地址：`192.168.43.221`（ IPv4 地址）
子网掩码：`255.255.255.0`
网关：`192.168.43.1`

### 实作二

使用 `ipconfig/all` 查看旁边计算机的网络配置，看看有什么异同。

- 相同的：子网掩码、默认网关
- 不同的：IPv4 、IPv6 和 Mac 地址

> 问题：
> 你的计算机和旁边的计算机是否处于同一子网，为什么？

答：处于同一子网，因为将 IP 与子网掩码相与，得到的结果相同。

## ping

> `PING` （Packet Internet Groper），因特网包探索器，用于测试网络连接量的程序 。`ping` 是工作在 TCP/IP 网络体系结构中应用层的一个服务命令， 主要是向特定的目的主机发送 ICMP（Internet Control Message Protocol 因特网报文控制协议）Echo 请求报文，测试目的站是否可达及了解其有关状态。

### 实作一

要测试到某计算机如 重庆交通大学 Web 服务器的连通性，可以使用 `ping www.cqjtu.edu.cn` 命令，也可直接使用 IP 地址。

![ ping 交大的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic1/2_1_1.png)

请掌握使用该命令后屏幕显示的反馈回来信息的意思，如：TTL、时间等。

答：TTL （ Time To Live ）是生存时间的意思，每当 ICMP 数据报经过一个路由器，TTL值就减1，直到为0或1时，路由器直接丢弃该IP包。
时间是指往返行程的估计时间，有最短、最长和平均。

### 实作二

使用 `ping/?` 命令了解该命令的各种选项并实际使用。

![ ping /?的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic1/2_2_1.png)

> 问题一：
> 假设你不能 `ping` 通某计算机或 IP，但你确定该计算机和你之间的网络是连通的，那么可能的原因是什么？该如何处理能保证 `ping` 通？

答：一、设置了防火墙，隔绝了ping，解决方法是关闭防火墙（不建议）；二、本机网卡状态有问题，解决方法是 `ping 127.0.0.1` ，测试自己计算机的状态，如果确实有问题，重新进行设置。

> 秘籍：
> 当你的网络出现故障不能访问某计算机如 `14.215.177.39` (百度的 IP 地址之一 ) 时，我们一般可采用由近及远的连通性测试来确定问题所在。现假设你的 IP 是 `192.168.1.89`，你旁边计算机的 IP 是 `192.168.1.64`，网关的 IP 是 `192.168.1.1` ，那么过程如下：
> 1. `ping 127.0.0.1` ，测试自己计算机的状态，如果 OK，那么说明本机网络软件硬件工作正常，否则，问题在本机，检查本机 TCP/IP 配置即网卡状态等
> 2. `ping 192.168.1.64` ，测试到旁边计算机的连通性，如果OK，那么说明本子网内部工作正常，否则，问题在本机网络出口到交换机之间，检查本机网卡到交换机的连线等
> 3. `ping 192.168.1.1`，测试到网关的连通性，如果 OK，那么说明本子网出口工作正常，否则，问题在网关，这是你无能为力的事情，报告给网管
> 4. `ping 14.215.177.39`，测试到百度的连通性，如果 OK，那就 OK，否则，问题在网关以外，这也是你无能为力的事情，报告给网管或者李彦宏？

> 问题二：
> 假设在秘籍中进行的网络排查中，`ping` 百度的 IP 即 `ping 14.215.177.39` 没问题，但 `ping` 百度的域名即 `ping www.baidu.com` 不行，那么可能的原因是什么？如何进行验证和解决？另外，经常有同学问到的："能上 QQ，但不能上网" 跟这个问题的原因是相似的。

答： DNS 发生故障或设置错误而导致解析失败，其功能为将域名解析为 IP 地址。
验证和解决的方法：
1. 咨询当地电信部门，当地 DNS 服务器是否发生故障；
2. 百度搜索当地 DNS 服务器地址，检查当前 DNS 服务器设置是否正确；
3. 可临时设置 DNS 服务器地址为 `8.8.8.8`，该服务器延迟会比较大。

## tracert

> `TRACERT` (Trace Route 的组合缩写)，也称为路由追踪，该命令行程序可用于跟踪 Internet 协议 （IP） 数据包传送到目标地址时经过的路径。

### 实作一

要了解到某计算机如 <http://www.baidu.com> 中间经过了哪些节点（路由器）及其它状态，可使用 `tracert www.baidu.com` 命令，查看反馈的信息，了解节点的个数。

![ tracert 百度的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic1/3_1_1.png)

可通过网站 <http://ip.cn> 查看这些节点位于何处，是哪个公司的，大致清楚本机到百度服务器之间的路径。

### 实作二

`ping.pe` 这个网站可以探测从全球主要的 ISP 到某站点如 <https://qige.io> 的线路状态，当然也包括各线路到该主机的路由情况。请使用浏览器访问 <http://ping.pe/qige.io> 进行了解。

> 问题一：
> `tracert` 能告诉我们路径上的节点以及大致的延迟等信息，那么它背后的原理是什么？本问题可结合第二部分的 Wireshark 实验进行验证。

> 问题二：
> 在以上两个实作中，如果你留意路径中的节点，你会发现无论是访问百度还是棋歌教学网，路径中的第一跳都是相同的，甚至你应该发现似乎前几个节点都是相同的，你的解释是什么？

答：访问子网外的 IP 会先经过网关，出网关的路径（一个或前几个）是相同的，一般不会改变。

> 问题三： 
> 在追踪过程中，你可能会看到路径中某些节点显示为 * 号，这是发生了什么？

答：发生的是 ping 后数据包回不到本机。原因有可能是一、网络拥塞，没有回应；二、运营商出于网络安全考虑，进行了隐藏。

## ARP

> ARP（Address Resolution Protocol）即地址解析协议，是用于根据给定网络层地址即 IP 地址，查找并得到其对应的数据链路层地址即 MAC地址的协议。 ARP 协议定义在 1982 年的 RFC 826。

### 实作一

运行 `arp -a` 命令查看当前的 arp 缓存， 请留意缓存了些什么。

![ arp -a 的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic1/4_1_1.png)

然后 `ping` 一下你旁边的计算机 IP（注意，需保证该计算机的 IP 没有出现在 arp 缓存中，或者使用 `arp -d *` 先删除全部缓存），再次查看缓存，你会发现一些改变，请作出解释。

![ arp -d * 的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic1/4_1_2.png)
![ ping 后 arp 的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic1/4_1_3.png)

### 实作二

请使用 `arp /?` 命令了解该命令的各种选项。

![ arp /? 的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic1/4_2_1.png)

### 实作三

一般而言，arp 缓存里常常会有网关的缓存，并且是动态类型的。

![ arp 的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic1/4_3_1.png)

假设当前网关的 IP 地址是 `192.168.0.1`，MAC 地址是 `5c-d9-98-f1-89-64`，请使用 `arp -s 192.168.0.1 5c-d9-98-f1-89-64` 命令设置其为静态类型的。（实验的时候替换了本机实际的网关 IP 地址和 MAC 地址）

![设置其为静态的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic1/4_3_2.png)
![ arp 变化的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic1/4_3_3.png)

> 问题一：
> 你可能会在实作三的操作中得到 "ARP 项添加失败: 请求的操作需要提升" 这样的信息，表示命令没能执行成功，你该如何解决？

答：原因是权限不够，不能进行操作。解决方法是通过管理员身份运行 `Cmder` ，具体的操作如实作三中的截图。

> 问题二：
> 在实作三中，为何缓存中常常有网关的信息？
> 我们将网关或其它计算机的 arp 信息设置为静态有什么优缺点？

答：ARP 缓存是保存 IP 地址与 MAC 地址的对应映射表。因为网关是通信子网的出入口，在与其它网段通信时需要网关进行转发，所以缓存中常常有网关的信息。
1. 优点：利于管理，稳定性高（人工手动设置），安全性高（能有效防止 ARP 欺骗）；
2. 缺点：不易维护，不适用于大型网络。

## DHCP

> DHCP（Dynamic Host Configuration Protocol）即动态主机配置协议，是一个用于 IP 网络的网络协议，位于 OSI 模型的应用层，使用 UDP 协议工作，主要有两个用途：
> - 用于内部网或网络服务供应商自动分配 IP 地址给用户
> - 用于内部网管理员对所有电脑作中央管理
> 
> 简单的说，DHCP 可以让计算机自动获取/释放网络配置。

### 实作一

一般地，我们自动获取的网络配置信息包括：IP 地址、子网掩码、网关 IP 以及 DNS 服务器 IP 等。使用 `ipconfig/release` 命令释放自动获取的网络配置，并用 `ipconfig/renew` 命令重新获取，了解 DHCP 工作过程和原理。

![自动配置的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic1/5_1_1.png)
![ ipconfig/release 的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic1/5_1_2.png)
![ ipconfig/renew 的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic1/5_1_3.png)

> 问题一：
> 如果你没能成功的释放，请思考有哪些可能的原因并着手进行解决？

答：可能没有开启 DHCP 客户端服务。解决方法是输入 `sc config DHCP start= AUTO`  后重启。

> 问题二：
> 在Windows系统下，如果由于某种原因计算机不能获取 DHCP 服务器的配置数据，那么Windows将会根据某种算法自动配置为 169.254.x.x 这样的 IP 地址。显然，这样的 IP 以及相关的配置信息是不能让我们真正接入 Internet 的，为什么？既然不能接入 Internet，那么Winodws系统采用这样的方案有什么意义？

答：自动配置的 IP 地址和配置信息只能短暂地解决问题，接入 Internet 需要正确的 IP 地址。意义在于这样可以通过查看旁边计算机的配置信息来手动进行网络配置，从而使该计算机能够接入 Internet。

> 秘籍：
> 在我校不少地方如教室，计算机都采用了 DHCP 来获得网络配置。假如某天因 DHCP 服务器问题从而不能获得网络配置，那么我们可以查看隔壁教室计算机的配置信息来手动进行网络配置，从而使该计算机能够接入 Internet。
> 经常的，在一个固定地方的网络配置我都喜欢采用 静态/手动配置，而不是动态 DHCP 来进行。你能想到是什么原因吗？

答：固定地方意味着网络结构一般不会发生变化，采用静态配置，具有安全性和稳定性，降低被攻击的可能性。

## netstat

>无论是使用 TCP 还是 UDP，任何一个网络服务都与特定的端口（Port Number）关联在一起。因此，每个端口都对应于某个通信协议/服务。
> `netstat`（Network Statistics）是在内核中访问网络连接状态及其相关信息的命令行程序，可以显示路由表、实际的网络连接和网络接口设备的状态信息，以及与 IP、TCP、UDP 和 ICMP 协议相关的统计数据，一般用于检验本机各端口的网络服务运行状况。

### 实作一

Windows 系统将一些常用的端口与服务记录在 `C:\WINDOWS\system32\drivers\etc\services` 文件中，请查看该文件了解常用的端口号分配

![常用端口号的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic1/6_1_1.png)

### 实作二

使用 `netstat -an` 命令，查看计算机当前的网络连接状况。更多的 `netstat` 命令选项，可参考上面链接 4 和 5 。

![ netstat -an 的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic1/6_2_1.png)

> 秘籍：
> 打开的计算机端口，就是向 Internet 打开的一个通道。通过 `netstat` 命令，我们可以了解当前计算机哪些端口是打开的，从而找出一些恶意后台程序，分析其所作所为，并且可以进一步将相关端口关闭，降低安全风险。

## DNS

> DNS（Domain Name System）即域名系统，是互联网的一项服务。它作为将域名和 IP 地址相互映射的一个分布式数据库，能够使人更方便地访问互联网。DNS 使用 TCP 和 UDP 的 53 号端口。

### 实作一

Windows 系统将一些固定的/静态的 DNS 信息记录在 `C:\WINDOWS\system32\drivers\etc\hosts` 文件中，如我们常用的 `localhost` 就对应 `127.0.0.1` 。请查看该文件看看有什么记录在该文件中。

![ hosts 文件中的 DNS 信息的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic1/7_1_1.png)

### 实作二

解析过的 DNS 记录将会被缓存，以利于加快解析速度。请使用 `ipconfig /displaydns` 命令查看。我们也可以使用 `ipconfig /flushdns` 命令来清除所有的 DNS 缓存。

![ ipconfig /displaydns 的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic1/7_2_1.png)
![ ipconfig /flushdns 的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic1/7_2_2.png)

### 实作三

使用 `nslookup qige.io` 命令，将使用默认的 DNS 服务器查询该域名。当然你也可以指定使用 `CloudFlare`（`1.1.1.1`）或 `Google`（`8.8.8.8`） 的全球 DNS 服务器来解析，如：`nslookup qige.io 8.8.8.8`，当然，由于你懂的原因，这不一定会得到正确的答案。

![ nslookup qige.io 的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic1/7_3_1.png)

> 秘籍：
> 当我们使用浏览器上网时，或多或少的会看到一些广告。某些网站的广告是非常没有节操的，所以我们一般可以通过安装浏览器插件如 AdGuard、AdBlocker 等来拦截和过滤。
> 这种方法简单方便有效，不过有一定安全或隐私问题，因为该插件实时知道你访问的 URL。
> 假设不使用这种第三方的插件，希望自己完全控制，那么就请考虑在我们前面提到的 hosts 文件中添加 DNS 记录。有自愿者时时在维护全球正确的 DNS 记录文件

> 问题：
> 上面秘籍中我们提到了使用插件或自己修改 `hosts` 文件来屏蔽广告，思考一下这种方式为何能过滤广告？如果某些广告拦截失效，那么是什么原因？你应该怎样进行分析从而能够成功屏蔽它？

答：在文件中添加该广告链接，并且指定一个 IP 地址（通常为本机地址 `127.0.0.1`），因为 hosts 文件优先级是高于 DNS 服务器，这样浏览器解析该广告链接时，优先查 hosts 文件，就相当于更改了该链接本来的 IP 地址，从而使该链接失效。拦截失效的原因可能是跨服务器访问的问题。我想的是服务器进行备份，对那些广告也指向某个 IP 地址。

## cache

> cache 即缓存，是 IT 领域一个重要的技术。我们此处提到的 cache 主要是浏览器缓存。
> 浏览器缓存是根据 HTTP 报文的缓存标识进行的，是性能优化中简单高效的一种优化方式了。一个优秀的缓存策略可以缩短网页请求资源的距离，减少延迟，并且由于缓存文件可以重复利用，还可以减少带宽，降低网络负荷。

### 实作一

> 打开 Chrome 或 Firefox 浏览器，访问 https://qige.io ，接下来敲 `F12` 键 或 `Ctrl + Shift + I` 组合键打开开发者工具，选择 `Network` 面板后刷新页面，你会在开发者工具底部看到加载该页面花费的时间。请进一步查看哪些文件被 cache了，哪些没有。

![整个页面的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic1/8_1_1.png)
![详细的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic1/8_1_2.png)

除了 “ / ” 的 html 文件其它都 cache 了，加载该页面花费了706毫秒。

### 实作二

接下来仍在 `Network` 面板，选择 `Disable cache` 选项框，表明当前不使用 cache，页面数据全部来自于 Internet，刷新页面，再次在开发者工具底部查看加载该页面花费的时间。你可比对与有 cache 时的加载速度差异。

![不 cache 的整个页面的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic1/8_2_1.png)
![不 cache 的详细的效果](https://github.com/GuanErZhi/network/blob/main/network_pic/pic1/8_2_2.png)

加载该页面花费了3.79秒。比 cache 的时间慢得多。

> 秘籍：
> 你的计算机可能还在使用 XX 管家，XX 卫士之类的垃圾软件，那么它可能就会在某些时候提示你计算机的垃圾文件有多少多少，请清理（其实是它本身应该被清理！）之类的弹窗。如果你查看一下它判断的所谓垃圾文件，你会发现大多都是浏览器的缓存，而你已经明白了这些缓存文件的作用。所以，清理吗？

答：不清理。