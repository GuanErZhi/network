# Cisco Packet Tracer 实验

## 直接连接两台 PC 构建 LAN

将两台 PC 直接连接构成一个网络。注意：直接连接需使用交叉线。

进行两台 PC 的基本网络配置，只需要配置 IP 地址即可，然后相互 `ping` 通即成功。

![两台 PC](https://github.com/GuanErZhi/network/blob/main/network_pic/pic3/1.1.png)

![两台 PC](https://github.com/GuanErZhi/network/blob/main/network_pic/pic3/1.2.png)

![两台 PC](https://github.com/GuanErZhi/network/blob/main/network_pic/pic3/1.3.png)

## 用交换机构建 LAN

构建如下拓扑结构的局域网：

![交换机](https://github.com/GuanErZhi/network/blob/main/network_pic/ps3/1.png)

各PC的基本网络配置如下表：

| 机器名 | IP | 子网掩码 |
| ---------- | ---------- | ---------- |
| PC7  |   192.168.1.1   | 255.255.255.0 |
| PC8  |   192.168.1.2   | 255.255.255.0 |
| PC9  |   192.168.2.1   | 255.255.255.0 |
| PC10 |   192.168.2.2   | 255.255.255.0 |


> 问题：
> 1. PC0 能否 `ping` 通 PC1、PC2、PC3 ？
> 2. PC3 能否 `ping` 通 PC0、PC1、PC2 ？为什么？
> 3. 将 4 台 PC 的掩码都改为 `255.255.0.0` ，它们相互能 `ping` 通吗？为什么？
> 4. 使用二层交换机连接的网络需要配置网关吗？为什么？

回答：（PC0 对应 PC7，PC1 对应 PC8，PC2 对应 PC9，PC3 对应 PC10）
1. PC7 能 `ping` 通 PC8， `ping` 不通 PC9 和 PC10 

![ping](https://github.com/GuanErZhi/network/blob/main/network_pic/pic3/2.1.png)

2. PC10 能 `ping` 通 PC9， `ping` 不通 PC7 和 PC8。因为子网掩码均为 `255.255.255.0` 的情况下，根据他们的 IP 地址，相与得到的结果表示 PC7 和 PC8 处于同一子网，PC9 和 PC10 处于同一子网。

![ping](https://github.com/GuanErZhi/network/blob/main/network_pic/pic3/2.2.png)

3. 能够相互 `ping` 通。因为子网掩码都是 `255.255.0.0`，结合上述网络配置表意味着处于这四台计算机处于同一子网中。

![ping](https://github.com/GuanErZhi/network/blob/main/network_pic/pic3/2.3.png)

4. 需要配置网关，因为其与相连的自治系统可以向核心系统通告可达信息。

>  试一试
>  集线器 Hub 是工作在物理层的多接口设备，它与交换机的区别是什么？请在 CPT 软件中用 Hub 构建网络进行实际验证。

答：区别在于交换机工作在数据链路层，通过MAC地址转发数据；集线器工作在物理层，通过广播的形式转发数据。集线器内部采用了总线型拓扑，在同一时间内必须是单向的，只能维持在半双工模式下；交换机上的两个端口之间的通道是相互独立的，可以实现全双工通信。

## 交换机接口地址列表

二层交换机是一种即插即用的多接口设备，它对于收到的帧有 3 种处理方式：广播、转发和丢弃（请弄清楚何时进行何种操作）。那么，要转发成功，则交换机中必须要有接口地址列表即 MAC 表，该表是交换机通过学习自动得到的！

![接口地址列表](https://github.com/GuanErZhi/network/blob/main/network_pic/ps3/1.png)

仍然构建上图的拓扑结构，并配置各计算机的 IP 在同一个一个子网，使用工具栏中的放大镜点击某交换机如左边的 Switch3，选择 `MAC Table`，可以看到最初交换机的 MAC 表是空的，也即它不知道该怎样转发帧（那么它将如何处理？），用 PC0 访问（`ping`）PC1 后，再查看该交换机的 MAC 表，现在有相应的记录，请思考如何得来。随着网络通信的增加，各交换机都将生成自己完整的 MAC 表，此时交换机的交换速度就是最快的！

![接口地址列表](https://github.com/GuanErZhi/network/blob/main/network_pic/pic3/3.1.png)

![接口地址列表](https://github.com/GuanErZhi/network/blob/main/network_pic/pic3/3.2.png)

![接口地址列表](https://github.com/GuanErZhi/network/blob/main/network_pic/pic3/3.3.png)

1. MAC 表是空的，交换机会广播转发帧
2. `ping` 后 MAC 表的生成是，广播转发登记后找到该 IP 的 MAC 地址后进行记录。

> 秘籍：
> 你还可以使用 CPT 的 `Simulation` 模式即模拟方式进一步看清楚这个过程！

## 生成树协议（Spanning Tree Protocol）


交换机在目的地址未知或接收到广播帧时是要进行广播的。如果交换机之间存在回路/环路，那么就会产生广播循环风暴，从而严重影响网络性能。

而交换机中运行的 STP 协议能避免交换机之间发生广播循环风暴。

只使用交换机，构建如下拓扑：

![生成树协议](https://github.com/GuanErZhi/network/blob/main/network_pic/pic3/4.1.png)

这是初始时的状态。我们可以看到交换机之间有回路，这会造成广播帧循环传送即形成广播风暴，严重影响网络性能。

随后，交换机将自动通过生成树协议（STP）对多余的线路进行自动阻塞（Blocking），以形成一棵以 Switch4 为根（具体哪个是根交换机有相关的策略）的具有唯一路径树即生成树！

经过一段时间，随着 STP 协议成功构建了生成树后，Switch5 的两个接口当前物理上是连接的，但逻辑上是不通的，处于Blocking状态（桔色）如下图所示：

![生成树协议](https://github.com/GuanErZhi/network/blob/main/network_pic/pic3/4.2.png)

在网络运行期间，假设某个时候 Switch4 与 Switch5 之间的物理连接出现问题（将 Switch4 与 Switch5 的连线剪掉），则该生成树将自动发生变化。Switch5 上方先前 Blocking 的那个接口现在活动了（绿色），但下方那个接口仍处于 Blocking 状态（桔色）。如下图所示：

![生成树协议](https://github.com/GuanErZhi/network/blob/main/network_pic/pic3/4.3.png)

> 注意：
> 交换机的 STP 协议即生成树协议始终自动保证交换机之间不会出现回路，从而形成广播风暴。

> 秘籍：
> 使用 CPT 的 Simulation 即模拟方式可非常清楚看到这个过程！
