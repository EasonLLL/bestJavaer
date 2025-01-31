# NAT 协议

* [NAT 协议](#nat-协议)
   * [什么是 NAT 协议](#什么是-nat-协议)
   * [NAT 的工作机制](#nat-的工作机制)
   * [什么是 NAT - PT](#什么是-nat---pt)
      * [静态 NAT - PT](#静态-nat---pt)
      * [动态 NAT - PT](#动态-nat---pt)
      * [NAPT - PT](#napt---pt)
   * [NAT 的潜在问题](#nat-的潜在问题)
   * [NAT 的应用](#nat-的应用)
   * [使用 NAT 的意义](#使用-nat-的意义)

> 这是计算机网络连载系列的第十一篇文章，前十篇文章见
>
> [计算机网络基础知识总结](https://mp.weixin.qq.com/s?__biz=MzI0ODk2NDIyMQ==&mid=2247486242&idx=1&sn=fac49b0b79515a5ed6afd4b341aff87b&chksm=e999fe30deee772637e1c52fb9001c60e60a772e7adba6701329c81974e76c57bb7b2e570225&token=850264305&lang=zh_CN#rd)
>
> [TCP/IP 基础知识总结](https://mp.weixin.qq.com/s?__biz=MzI0ODk2NDIyMQ==&mid=2247486408&idx=1&sn=c332ae7ae448f3eb98865003ecade589&chksm=e999fedadeee77cc6281d1b170bd906b58220d6cd83054bc741821f4167f1f18ceee9ba0e449&token=850264305&lang=zh_CN#rd)
>
> [计算机网络应用层](https://mp.weixin.qq.com/s?__biz=MzI0ODk2NDIyMQ==&mid=2247486507&idx=1&sn=622cc363b34bce54f4953076faa1cad6&chksm=e999f939deee702f2444df83ad9805de8c70fb88b89d299fdf0a82b3463e253f32372963c039&token=1398464113&lang=zh_CN#rd)
>
> [计算机网络传输层](https://mp.weixin.qq.com/s?__biz=MzI0ODk2NDIyMQ==&mid=2247487108&idx=1&sn=7b47f421bb1dee4edb357a10399b7fec&chksm=e999fb96deee7280a17bfff44c27ef11a60e93e48f9da738670a779ecf6accb5a6a4ebd3cbcc&token=1398464113&lang=zh_CN#rd)
>
> [计算机网络网络层](https://mp.weixin.qq.com/s?__biz=MzI0ODk2NDIyMQ==&mid=2247487683&idx=1&sn=e0949e72e039759545450852d8bc0ada&chksm=e999e5d1deee6cc7ab9e42b50329924fee39c45955516b406046605d27928825a0f628d13e7c&token=1398464113&lang=zh_CN#rd)
>
> [计算机网络链路层](https://mp.weixin.qq.com/s?__biz=MzI0ODk2NDIyMQ==&mid=2247488884&idx=1&sn=0fdb91b7f5081d2e24c82d891fcc6126&chksm=e999e066deee69704d162b97be2ff0d33225fa9a3d12e4d3bec90a34996e7db7134535f36e8e&token=1398464113&lang=zh_CN#rd)
>
> [计算机网络 ARP 协议](https://mp.weixin.qq.com/s?__biz=MzI0ODk2NDIyMQ==&mid=2247487804&idx=1&sn=f001a24a308053b3723dfb12d36045ee&chksm=e999e42edeee6d383fbb411792e22e4028bb8c2441255786f50cf848443af7b1bd5e382078dc&token=1398464113&lang=zh_CN#rd)
>
> [计算机网络 DNS 协议](https://mp.weixin.qq.com/s?__biz=MzI0ODk2NDIyMQ==&mid=2247487880&idx=1&sn=fd38ce30ae82fa7d08e5f83fabb9d497&chksm=e999e49adeee6d8c1adacbfe27dc59097e4cb9d39c6a04802b0fe61877653330e75721cbde0b&token=1398464113&lang=zh_CN#rd)
>
> [计算机网络 ICMP 协议](https://mp.weixin.qq.com/s?__biz=MzI0ODk2NDIyMQ==&mid=2247488316&idx=1&sn=360c3e6eb45e9cbd7c38f3d43e8850e7&chksm=e999e62edeee6f3806dfe9b5c8d00c5e521cae1a1e7b85fd33d7a7c64fa897b3632dd31b9d50&token=1398464113&lang=zh_CN#rd)
>
> [计算机网络 DHCP 协议](https://mp.weixin.qq.com/s?__biz=MzI0ODk2NDIyMQ==&mid=2247488546&idx=1&sn=9a8ec2b6900d930e51c55d01de3dd7b5&chksm=e999e130deee6826bac33f3f395f763b33b7cbe6809ae5e3b02a2e24daf816b13851d4f3246e&token=1398464113&lang=zh_CN#rd)

## 什么是 NAT 协议

我们的计算机要想访问互联网上的信息，就需要一个地址，而且这个地址是大家（其他主机）所认可的，是公共的，这个地址也叫做**公有 IP 地址**。	

与之相对的，除了公有 IP 地址外，还有**私有 IP 地址**，私有 IP 地址就是我们在私有网络中使用的地址，比如局域网或者公司内部的网络。不知道你没有观察过自己家路由器所分配的 IP 地址，当你登上路由器的网关 192.168.1.1 时，在分配的网络下面一般会看到一些 IP 地址，都是 192.168.1.x ，这种地址就是私有 IP 地址。

>所以现在就会产生一个问题，比如 cxuan 的私有 IP 地址是 192.168.1.3 ，而隔壁 cuanxi 哥的 IP 地址也是 192.168.1.3 ，那么他俩同时在网上冲浪的时候，不会产生 IP 冲突吗？或者是把 cxuan 浏览的内容告诉给了 cxuanxi 哥？

这种问题远不用担心，因为我们有一个很靠谱的兄弟 --- *NAT(Network Address Translator)*，当我们的计算机连接到 Internet 的同时，NAT 就会把我们的私有地址直接转换成互联网上的公有 IP 地址，那么下面我们就来了解一下这个协议和它的工作机制。

我们现在就可以给 NAT 协议下一个定义，即 **NAT 协议是将在本地网络中使用的私有地址，在连接互联网的同时转换成为公共 IP 地址的技术**。实际上，转换的不仅仅只有 IP 地址，还有 TCP 、UDP 端口号，不过这种协议叫做 *NAPT* 协议。通常情况下，我们所说的 NAT 其实指的就是 NAPT。

## NAT 的工作机制

下面我们来聊一下 NAT 具体的工作机制，如下图所示。

![image-20220221082848527](https://tva1.sinaimg.cn/large/e6c9d24ely1gzktv39zq9j21l00o2wjh.jpg)

<div align = "center">图 11-1</div>

私有网络中的客户端 A （IP 192.168.1.6）想向公共网络中的服务器（IP 122.122.122.122）发送数据包，当这个数据包经过 NAT 路由器的时候，就会把它的私有 IP 192.168.1.6 转换为公有的 IP 12.34.56.78，然后这个数据包的源地址就变为 12.34.56.78 ，它经由 Internet 发送给 IP 为 122.122.122.122 的目标服务器。

>NAT 路由器其实就是相当于在路由器上安装的 NAT 软件，装有 NAT 软件的路由器就叫做 NAT 路由器。

NAT 路由器不仅可以把私有 IP 转换为公有 IP ，还可以把公有 IP 转换为私有 IP ，这种转换是双向的。

![image-20220221085746750](https://tva1.sinaimg.cn/large/e6c9d24ely1gzkup8pyptj218c08emxv.jpg)

<div align = "center">图 11-2</div>

拿上图来说，意思就是服务器 122.122.122.122 发送一个数据包，这个数据包通过 Internet 发送给 NAT 路由器，NAT 路由器把它转换为目标地址是 192.168.1.6 的数据包，然后再发送给客户端 A。

>这里有个问题，服务器是发送的数据包，是如何把目标地址的公有 IP 地址转换为私有 IP 地址的呢？

我们上面在引用中说到，NAT 路由器其实就是在路由器上装的 NAT 软件，所以 NAT 路由器它本身就是一台路由器设备，而路由器是有路由表的，路由表中记录一些源地址和目标地址项，数据包根据这些项来进行路由转发。

（我们可以使用 netstat -nr 来显示路由表的信息）

<img src="https://tva1.sinaimg.cn/large/e6c9d24ely1gzkwpw3gtrj20pk0hw41c.jpg" alt="image-20220221100722602" style="zoom:50%;" />

<div align = "center">图 11-3</div>

所以，NAT 路由器内部有一张用来记录转换地址的表，也就是一件可以解释的事情了，所以 NAT 路由器在进行地址转换时，会按照其内部的映射关系来进行处理。

上面只是一台客户端进行 NAT 转换的情形，但是现实生活中我们私有网络中不可能只有一台客户端进行通信，所以当私有网络中所有的客户端都需要上网，进行 NAT 转换的时候，是不是 NAT 路由器会为每一个客户端生成一个 IP 地址呢？如果全世界范围内的私有网络都这么转换的话，那么公有 IP 地址的数量势必会承受非常大的压力。

针对这种情况，提出了使用 IP 地址和端口号一起转换的方式（NAPT），如下图所示。

![image-20220221143753479](https://tva1.sinaimg.cn/large/e6c9d24ely1gzl4j4dk1hj21kh0u0dme.jpg)

<div align = "center">图 11-4</div>

这个大致过程和上面 NAT 的转换模式相同，不一样的是，使用 NAPT 会把客户端 A 和客户端 B 的数据包源地址 192.168.1.6:80、192.168.1.7:80 转换为 12.34.56.78:80 和 12.34.56.78:90 ，然后再发送给目标服务器。在转换的过程中，NAT 路由器会生成转换表，通过转换表就可以正确地转换地址和端口的组合，使客户端 A 和客户端 B 与服务器之间进行通信。

转换表是在 NAT 路由器上自动生成的，当 TCP 开始建立连接的时候，就会生成对应的映射，断开连接时，会删除对应的映射。

## 什么是 NAT - PT

现在互联网情况是一部分 IPv4 的主机和 IPv6 的主机共存的情况，IPv6 作为 IPv4 的替代者，已经越来越多的主机选择升级到了 IPv6 协议，但是罗马不是一日建成的，也不可能全世界范围的计算机都在某几天停机进行升级，这是不现实的，而且很多服务是基于 IPv4 协议建立的，如果升级到 IPv6 不兼容的话，使用 IPv6 的优势也就无从谈起，所以为了解决这个问题，就产生了 *NAT-PT* 规范。

NAT-PT 是一种将 IPv6 首部转换为 IPv4 首部的一种技术，通过 NAT-PT ，IPv6 的主机能够和 IPv4 主机进行通信，同时某些 IPv4 的服务也能够让 IPv6 的主机使用。

>注意这里的 IPv6 只能转换为 IPv4 ，不能反向转换，那么要实现双向通信，该如何做呢？

支持 NAT-PT 的路由器要 DNS-ALG 的支持来实现 IPv4 向 IPv6 的转换。

NAT-PT 机制定义了以下 3 种不同类型的操作：

### 静态 NAT - PT

静态 NAT - PT 模式主要是一对一映射的。IPv6 网络内的节点要访问的 IPv4 网络内都必须在 NAT-PT 设备中设置。每一个目的 IPv4 地址在 NAT-PT 设备中被映射为一个具有预定义 NAT-PT 前缀的 IPv6 地址。

### 动态 NAT - PT

动态模式也提供了一对一映射，只不过这种动态模式是有一个 IPv4 地址池的，池中的 IPv4 地址数量决定了并发IPv6 到 IPv4 转换的最大数目。

### NAPT - PT

网络地址端口转换--协议转换模式提供多个有 NAT-PT 前缀的 IPv6 地址和一个源 IPv4 地址间的多对一动态映射。

## NAT 的潜在问题

聊到现在，我们可以推断出，NAT（NAPT）其实最关键的也是路由器中的转换表，过度依赖转换表会造成下面这些问题

* NAT 无法从外部向内部服务器建立连接。
* 生成转换表存在一定的开销。
* NAT 路由一旦遇到异常情况需要重启，所有的 TCP 连接都会丢失。
* 即使做了灾备，TCP 连接还是会断开。

>那么针对这些问题，该如何解决呢？

第一种是直接升级到 IPv6，因为在 IPv6 的环境中可用的 IP 地址的数量有了极大的扩展，现在看来，怎么造都没事儿。还有一种解决办法是一种叫做 **NAT 穿越**的技术。

## NAT 的应用

NAT 主要可以实现以下几个功能：**数据包伪装、负载均衡、端口转发和透明代理**。

* 数据伪装：可以将内网数据包中的地址信息更改成统一的对外地址信息，不让内网主机直接暴露在因特网上，保证内网主机的安全。同时，该功能也常用来实现共享上网。例如，内网主机访问外网时，为了隐藏内网拓扑结构，使用全局地址替换私有地址。
* 负载均衡：目的地址转换 NAT 可以重定向一些服务器的连接到其他随机选定的服务器。
* 端口转发：当内网主机对外提供服务时，由于使用的是内部私有 IP 地址，外网无法直接访问。因此，需要在网关上进行端口转发，将特定服务的数据包转发给内网主机。
* 透明代理：例如自己架设的服务器空间不足，需要将某些链接指向存在另外一台服务器的空间；或者某台计算机上没有安装 IIS 服务，但是却想让网友访问该台计算机上的内容，这个时候利用 IIS 的 Web 站点重定向即可轻松的帮助我们搞定。

## 使用 NAT 的意义

NAT（NAPT）实际上是为了解决 IPv4 枯竭而开发的技术，不过，现在随着 IPv6 的开发，在 IPv6 中为了提高网络安全也在使用 NAT，在 IPv4 和 IPv6 的通信中经常使用 NAT-PT。然而在安全机制上 IPv4 也潜在着威胁，在配置和管理上也是一个挑战。如果要从根本上解决 IP 地址资源的问题，IPv6 才是最根本之路。

![image-20210717083948590](https://tva1.sinaimg.cn/large/008i3skNly1gsjnhb9f5xj319s0tsn4g.jpg)

![image-20210717084050334](https://tva1.sinaimg.cn/large/008i3skNly1gsjnidv1r3j315s0fs40g.jpg)

