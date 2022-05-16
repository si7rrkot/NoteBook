# DHCP

## 1.DHCP简介

dhcp协议是动态主机配置协议，是互联网的基本协议。lwip内核也实现了该协议。

lwip实现dhcp的代码在core/dhcp.c中。

>DHCP（Dynamic Host Configuration Protocol，动态主机配置协议）使用UDP协议工作，采用67（DHCP服务器文）和68（DHCP客户端）两个端口号。546号端口用于DHCPv6 Client，而不用于DHCPv4，是为DHCP failover服务。
>
>DHCP客户端向DHCP服务器发送的报文称之为DHCP请求报文，而DHCP服务器向DHCP客户端发送的报文称之为DHCP应答报文。
>
>DHCP采用C/S（客户端/服务器）模式，可以为客户机自动分配IP地址、子网掩码以及缺省网关、DNS服务器的IP地址等，并能够提升地址的使用率。

### 1.1 DHCP报文种类

DHCP一共有8种报文，分别为DHCP Discover、DHCP Offer、DHCP Request、DHCP ACK、DHCP NAK、DHCP Release、DHCP Decline、DHCP Inform。各种类型报文的基本功能如下：

| DHCP 报文类型 | 说明                                                         |
| ------------- | ------------------------------------------------------------ |
| DHCP Discover | DHCP 客户端在请求 IP 地址时并不知道 DHCP 服务器的位置，因此 DHCP 客户端会在本地网络内以 广播方式发送 Discover 请求报文，以发现网络中的 DHCP 服务器。所有收到 Discover 报文的 DHCP 服务 器都会发送应答报文， DHCP 客户端据此可以知道网络 中存在的 DHCP 服务器的位置。 |
| DHCP Offer    | DHCP 服务器收到 Discover 报文后，就会在所配置的 地址池中查找一个合适的 IP 地址，加上相应的租约期 限和其他配置信息（如网关、 DNS 服务器等），构造一 个 Offer 报文，发送给 DHCP 客户端，告知用户本服务 器可以为其提供 IP 地址。但这个报文只是告诉 DHCP 客户端可以提供 IP 地址，最终还需要客户端通过 ARP 来 检测该 IP 地址是否重复。 |
| DHCP Request  | DHCP 客户端可能会收到很多 Offer 请求报文，所以必 须在这些应答中选择一个。通常是选择第一个 Offer 应答 报文的服务器作为自己的目标服务器，并向该服务器发 送一个广播的 Request 请求报文，通告选择的服务器， 希望获得所分配的 IP 地址。另外， DHCP 客户端在成 功获取 IP 地址后，在地址使用租期达到50% 时，会向 DHCP 服务器发送单播 Request 请求报文请求续延租约， 如果没有收到 ACK 报文，在租期达到87.5% 时，会再次发送 广播的 Request 请求报文以请求续延租约。 |
| DHCP ACK      | DHCP 服务器收到 Request 请求报文后，根据 Request 报文 中携带的用户 MAC 来查找有没有相应的租约记录，如果有 则发送 ACK 应答报文，通知用户可以使用分配的 IP 地址。 |
| DHCP NAK      | 如果 DHCP 服务器收到 Request 请求报文后，没有发现有相 应的租约记录或者由于某些原因无法正常分配 IP 地址，则向 DHCP 客户端发送 NAK 应答报文，通知用户无法分配合适的 IP 地址。 |
| DHCP Release  | 当 DHCP 客户端不再需要使用分配 IP 地址时，就会主动向 DHCP 服务器发送 RELEASE 请求报文，告知服务器用户不 再需要分配 IP 地址，请求 DHCP 服务器释放对应的 IP 地址。 |
| DHCP Decline  | DHCP 客户端收到 DHCP 服务器 ACK 应答报文后，通过地址 冲突检测发现服务器分配的地址冲突或者由于其他原因导致 不能使用，则会向 DHCP 服务器发送 Decline 请求报文，通知 服务器所分配的 IP 地址不可用，以期获得新的 IP 地址。 |
| DHCP Inform   | DHCP 客户端如果需要从 DHCP 服务器端获取更为详细的配 置信息，则向 DHCP 服务器发送 Inform 请求报文； DHCP 服 务器在收到该报文后，将根据租约进行查找到相应的配置信 息后，向 DHCP 客户端发送 ACK 应答报文。目前基本上不用了。 |

### 1.2 DHCP报文格式

DHCP服务的8种报文的格式是相同的，不同类型的报文只是报文中的某些字段取值不同。DHCP报文格式基于BOOTP的报文格式。下面是各字段的说明。

![img](.\DHCP-1.jpg)

**OP**：报文的操作类型。分为请求报文和响应报文。1：请求报文，2：应答报文。即client送给server的封包，设为1，反之为2。

**请求报文**： DHCP Discover、DHCP Request、DHCP Release、DHCP Inform和DHCP Decline。

**应答报文**： DHCP Offer、DHCP ACK和DHCP NAK。

**Htype**： DHCP客户端的MAC地址类型。 MAC地址类型其实是指明网络类型 ，Htype值为1时表示为最常见的以太网MAC地址类型。

**Hlen**： DHCP客户端的MAC地址 长度。以太网MAC地址长度为6个字节，即以太网时Hlen值为6。

**Hops**：DHCP报文经过的DHCP中继的数目，默认为0。DHCP请求报文每经过一个DHCP中继，该字段就会增加1。没有经过DHCP中继时值为0。( 若数据包需经过router传送，每站加1，若在同一网内，为0。 )

**Xid**：客户端通过DHCP Discover报文发起一次IP地址请求时选择的随机数，相当于请求标识。用来标识一次IP地址请求过程。在一次请求中所有报文的Xid都是一样的。

**Secs**：DHCP客户端从获取到IP地址或者续约过程开始到现在所消耗的时间，以秒为单位。在没有获得IP地址前该字段始终为0。( DHCP客户端开始DHCP请求后所经过的时间。目前尚未使用，固定为0。)

**Flags**：标志位，只使用第0比特位，是广播应答标识位，用来标识DHCP服务器应答报文是采用单播还是广播发送，0表示采用单播发送方式，1表示采用广播发送方式。其余位 尚未使用 。(即 从0-15bits，最左1bit为1时表示server将以广播方式传送封包给client。 )

**【注意】**在客户端正式分配了IP地址之前的第一次IP地址请求过程中，所有DHCP报文都是以广播方式发送的，包括客户端发送的DHCP Discover和DHCP Request报文，以及DHCP服务器发送的DHCP Offer、DHCP ACK和DHCP NAK报文。当然，如果是由DHCP中继器转的报文，则都是以单播方式发送的。另外，IP地址续约、IP地址释放的相关报文都是采用单播方式进行发送的。

**Ciaddr**：DHCP客户端的IP地址。仅在DHCP服务器发送的ACK报文中显示，在其他报文中均显示0，因为在得到DHCP服务器确认前，DHCP客户端是还没有分配到IP地址的。只有客户端是Bound、Renew、Rebinding状态，并且能响应ARP请求时，才能被填充。

**Yiaddr**：DHCP服务器分配给客户端的IP地址。仅在DHCP服务器发送的Offer和ACK报文中显示，其他报文中显示为0。

**Siaddr**：下一个为DHCP客户端分配IP地址等信息的DHCP服务器IP地址。仅在DHCP Offer、DHCP ACK报文中显示，其他报文中显示为0。( 用于bootstrap过程中的IP地址)

**Giadd**r：DHCP客户端发出请求报文后经过的第一个DHCP中继的IP地址。如果没有经过DHCP中继， 则显示为0。( 转发代理（网关）IP地址 )

**Chaddr**：DHCP客户端的MAC地址。在每个报文中都会显示对应DHCP客户端的MAC地址。

**Sname**：为DHCP客户端分配IP地址的DHCP服务器名称（DNS域名格式）。在Offer和ACK报文中显示发送报文的DHCP服务器名称，其他报文显示为0。

**File**：DHCP服务器为DHCP客户端指定的启动配置文件名称及路径信息。仅在DHCP Offer报文中显示，其他报文中显示为空。

**Options**： 可选项字段，长度可变，格式为"代码+长度+数据"。

列出部分可选的选项： 	

| 代码 | 长度(字节)                        | 说明                                                         |
| ---- | --------------------------------- | ------------------------------------------------------------ |
| 1    | 4                                 | 子网掩码                                                     |
| 3    | 长度可变，必须是4个字节的倍数。   | 默认网关（可以是一个路由器IP地址列表）                       |
| 6    | 长度可变，必须是4个字节的整数倍。 | DNS服务器（可以是一个DNS服务器IP地址列表）                   |
| 15   | 长度可变                          | 域名称（主DNS服务器名称）                                    |
| 44   | 长度可变，必须是4个字节的整数倍。 | WINS服务器（可以是一个WINS服务器IP列表）                     |
| 51   | 4                                 | 有效租约期（以秒为单位）                                     |
| 53   | 1                                 | 报文类型1: DHCP Discover2: DHCP Offer3: DHCP Request4: DHCP Decline5: DHCP ACK6: DHCP NAK7: DHCP Release8: DHCP Inform |
| 58   | 4                                 | 续约时间                                                     |

## 2.使用

要使用`DHCP`协议,需要先在`lwipopt.h`中开启相关的宏

```c
#define LWIP_DHCP                       1
#define LWIP_UDP                        1
```

再在代码中调用

```c
err_t dhcp_start(struct netif *netif);
```

两个相关的定时器

```c
/** 每一分钟调用一次 */
void dhcp_coarse_tmr(void);
/** 500ms调用一次 */
void dhcp_fine_tmr(void);
```

### 2.1 DHCP结构体

```c
struct dhcp
{
  u32_t xid;  //事务ID，随机数，有客户端生成，服务器Reply时，会把Request中的Transaction拷贝到Reply报文中。
  
  struct udp_pcb *pcb;  //连接dhcp服务器的udp控制块
  
  struct dhcp_msg *msg_in;  //dhcp输入报文
  
  u8_t state; //当前dhcp状态机
  
  u8_t tries; //重请求次数
  u8_t subnet_mask_given;	//是否有子网掩码

  struct pbuf *p_out; /* 输出的报文的pbuf pbuf of outcoming msg */ 
  struct dhcp_msg *msg_out; /* 输出的报文 outgoing msg */
  u16_t options_out_len; /* 输出的报文的选项长度 outgoing msg options length */
  //请求超时时间
  u16_t request_timeout; /* #ticks with period DHCP_FINE_TIMER_SECS for request timeout */
  u16_t t1_timeout;  /* #ticks with period DHCP_COARSE_TIMER_SECS for renewal time */
  u16_t t2_timeout;  /* #ticks with period DHCP_COARSE_TIMER_SECS for rebind time */

  ip_addr_t server_ip_addr;   //dhcp服务器地址
  ip_addr_t offered_ip_addr;  //服务器提供的ip
  ip_addr_t offered_sn_mask;  //服务器提供的子网掩码
  ip_addr_t offered_gw_addr;  //服务器提供的网关地址
 
  //dhcp报文选项字段中的时间
  u32_t offered_t0_lease; /* 租借地址的时间 lease period (in seconds) */
  u32_t offered_t1_renew; /* 续租的时间 recommended renew time (usually 50% of lease period) */
  u32_t offered_t2_rebind; /* 租约重新设定的时间 recommended rebind time (usually 66% of lease period)  */
  /* @todo: LWIP_DHCP_BOOTP_FILE configuration option?
     integrate with possible TFTP-client for booting? */
};
```

dhcp报文实质上是udp报文，故结构体有udp控制块。**其中需要关注与时间有关的变量：**

```c
  u16_t request_timeout; //请求超时时间
  u16_t t1_timeout;  //续租超时时间
  u16_t t2_timeout;		//重绑定超时时间
```

