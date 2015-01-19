#### TCP/IP Linux参数介绍
***  

1. 解决SYN Flood攻击参数设置  
    第一个：tcp_synack_retries，可以用来减少重试次数；  
    第二个：tcp_max_syn_backlog，可以增大SYN连接数；  
    第三个：tcp_abort_on_overflow，处理不过来干脆就直接拒绝连接了;  

2. 大并发的短链接下，TIME_WAIT状态过多，这也会消耗很多系统资源，相关参数介绍:  

    第一个：关于tcp_tw_reuse: tcp_tw_reuse加上tcp_timestamps（又叫PAWS, for Protection Against Wrapped Sequence Numbers）可以保证协议的角度上的安全，需要tcp_timestamps在两边都被打开; [tcp_twsk_unique](http://lxr.free-electrons.com/ident?i=tcp_twsk_unique)  

    第二个：关于tcp_tw_recycle: 如果是tcp_tw_recycle被打开了话，会假设对端开启了tcp_timestamps，然后会去比较时间戳，如果时间戳变大了，就可以重用; [tcp_timewait_state_process](http://lxr.free-electrons.com/ident?i=tcp_timewait_state_process)  

    第三个：关于tcp_max_tw_buckets: 控制并发的TIME_WAIT的数量，默认值是180000，如果超限，那么，系统会把多的给destory掉，然后在日志里打一个警告;  

    第四个：关于tcp_timestamps 要使用 tcp_tw_reuse/tcp_tw_recycle 必须设定tcp_timestamps;  

    (谨慎使用如上参数)  More [ip-sysctl](https://www.kernel.org/doc/Documentation/networking/ip-sysctl.txt) 

****
/proc/sys/net/ipv4/somaxconn - Limit of socket listen() backlog, known in userspace as SOMAXCONN.  
/proc/sys/net/ipv4/tcp_max_syn_backlog  
/proc/sys/net/ipv4/ip_local_port_range  
/proc/sys/net/ipv4/tcp_mem  

/proc/sys/net/core/rmem_max - Maximum TCP Receive Window  
/proc/sys/net/core/wmem_max - Maximum TCP Send Window  
/proc/sys/net/ipv4/tcp_rmem - memory reserved for TCP receive buffers  
/proc/sys/net/ipv4/tcp_wmem - memory reserved for TCP send buffers  
/proc/sys/net/ipv4/tcp_timestamps - timestamps (RFC 1323) add 12 bytes to the TCP  header...  
/proc/sys/net/ipv4/tcp_sack - tcp selective acknowledgements.  
/proc/sys/net/ipv4/tcp_window_scaling - support for large TCP Windows (RFC 1323).   Needs to be set to 1 if the Max TCP Window is over 65535.  

/proc/sys/net/ipv4/tcp_timestamps  
/proc/sys/net/ipv4/tcp_tw_recycle  
/proc/sys/net/ipv4/tcp_tw_reuse  
/proc/sys/net/ipv4/tcp_max_tw_buckets  

/proc/sys/net/ipv4/tcp_max_orphans  
/proc/sys/net/ipv4/tcp_orphan_retries  
/proc/sys/net/ipv4/tcp_fin_timeout  
/proc/sys/net/ipv4/tcp_rfc1337  


Other TCP Parameters to consider

/proc/sys/net/ipv4/tcp_fastopen  
	Enable TCP Fast Open feature (draft-ietf-tcpm-fastopen) to send data
	in the opening SYN packet. To use this feature, the client application
	must use sendmsg() or sendto() with MSG_FASTOPEN flag rather than
	connect() to perform a TCP handshake automatically.  

/proc/sys/net/ipv4/tcp_rfc1337  
	If set, the TCP stack behaves conforming to RFC1337. If unset,
	we are not conforming to RFC, but prevent TCP TIME_WAIT
	assassination.  
	Default: 0  
  
TCP_FIN_TIMEOUT  
This setting determines the time that must elapse before TCP/IP can release a closed connection and reuse its resources. During this TIME_WAIT state, reopening the connection to the client costs less than establishing a new connection. By reducing the value of this entry, TCP/IP can release closed connections faster, making more resources available for new connections. Addjust this in the presense of many connections sitting in the TIME_WAIT state:

echo 30 > /proc/sys/net/ipv4/tcp_fin_timeout  
(default: 60 seconds, recommended 15-30 seconds)  

Notes:  
You can use any of the earlier described methods to reapply these settings at boot time.
Here is a quick way to view the number of connections and their states:

netstat -tan | grep ':80 ' | awk '{print $6}' | sort | uniq -c  

TCP_KEEPALIVE_INTERVAL  
This determines the wait time between isAlive interval probes. To set:

echo 30 > /proc/sys/net/ipv4/tcp_keepalive_intvl  
(default: 75 seconds, recommended: 15-30 seconds)

TCP_KEEPALIVE_PROBES  
This determines the number of probes before timing out. To set:

echo 5 > /proc/sys/net/ipv4/tcp_keepalive_probes  
(default: 9, recommended 5)

TCP_TW_RECYCLE  
It enables fast recycling of TIME_WAIT sockets. The default value is 0 (disabled). The sysctl documentation incorrectly states the default as enabled. It can be changed to 1 (enabled) in many cases. Known to cause some issues with hoststated (load balancing and fail over) if enabled, should be used with caution.

echo 1 > /proc/sys/net/ipv4/tcp_tw_recycle  
(boolean, default: 0) 

TCP_TW_REUSE  
This allows reusing sockets in TIME_WAIT state for new connections when it is safe from protocol viewpoint. Default value is 0 (disabled). It is generally a safer alternative to tcp_tw_recycle

echo 1 > /proc/sys/net/ipv4/tcp_tw_reuse  
(boolean, default: 0)

Note: The tcp_tw_reuse setting is particularly useful in environments where numerous short connections are open and left in TIME_WAIT state, such as web servers. Reusing the sockets can be very effective in reducing server load.

| 可调节的参数  |  默认值 | 选项说明 |   
| /proc/sys/net/core/rmem_default |	"110592" |	定义默认的接收窗口大小；对于更大的 BDP 来说，这个大小也应该更大。|  
| /proc/sys/net/core/rmem_max |	 "110592" |	定义接收窗口的最大大小；对于更大的 BDP 来说，这个大小也应该更大。|  
| /proc/sys/net/core/wmem_default | "110592" |	定义默认的发送窗口大小；对于更大的 BDP 来说，这个大小也应该更大。|  
| /proc/sys/net/core/wmem_max | "110592" | 定义发送窗口的最大大小；对于更大的 BDP 来说，这个大小也应该更大。|  
| /proc/sys/net/ipv4/tcp_window_scaling	| "1" |	启用RFC 1323定义的 window scaling；要支持超过64KB的窗口,必须启用该值。|  
| /proc/sys/net/ipv4/tcp_sack | "1" | 启用有选择的应答（Selective Acknowledgment），这可以通过有选择地应答乱序接收到的报文来提高性能（这样可以让发送者只发送丢失的报文段）；（对于广域网通信来说）这个选项应该启用，但是这会增加对 CPU 的占用。|  
| /proc/sys/net/ipv4/tcp_fack | "1" | 启用转发应答（Forward Acknowledgment），这可以进行有选择应答（SACK）从而减少拥塞情况的发生；这个选项也应该启用。|  
| /proc/sys/net/ipv4/tcp_timestamps | "1" | 以一种比重发超时更精确的方法（请参阅 RFC 1323）来启用对 RTT 的计算；为了实现更好的性能应该启用这个选项。|  
| /proc/sys/net/ipv4/tcp_mem | "24576 32768 49152" | 确定 TCP 栈应该如何反映内存使用；每个值的单位都是内存页（通常是 4KB）。第一个值是内存使用的下限。第二个值是内存压力模式开始对缓冲区使用应用压力的上限。第三个值是内存上限。在这个层次上可以将报文丢弃，从而减少对内存的使用。对于较大的 BDP 可以增大这些值（但是要记住，其单位是内存页，而不是字节）。 |  
| /proc/sys/net/ipv4/tcp_wmem | "4096 16384 131072" | 为自动调优定义每个socket 使用的内存。第一个值是为 socket 的发送缓冲区分配的最少字节数。第二个值是默认值（该值会被 wmem_default 覆盖），缓冲区在系统负载不重的情况下可以增长到这个值。第三个值是发送缓冲区空间的最大字节数（该值会被 wmem_max 覆盖）。|  
| /proc/sys/net/ipv4/tcp_rmem | "4096 87380 174760" | 与 tcp_wmem 类似，不过它表示的是为自动调优所使用的接收缓冲区的值。|  
| /proc/sys/net/ipv4/tcp_low_latency | "0" | 允许TCP/IP 栈适应在高吞吐量情况下低延时的情况；这个选项应该禁用。|  
| /proc/sys/net/ipv4/tcp_westwood | "0" | 启用发送者端的拥塞控制算法，它可以维护对吞吐量的评估，并试图对带宽的整体利用情况进行优化；对于 WAN 通信来说应该启用这个选项。|  
| /proc/sys/net/ipv4/tcp_bic | "1" | 为快速长距离网络启用 Binary Increase Congestion；这样可以更好地利用以 GB 速度进行操作的链接；对于 WAN 通信应该启用这个选项。|  

