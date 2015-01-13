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

/proc/sys/net/ipv4/tcp_fin_timeout  
/proc/sys/net/ipv4/ip_local_port_range  
/proc/sys/net/ipv4/tcp_orphan_retries  
/proc/sys/net/ipv4/tcp_rfc1337  
/proc/sys/net/ipv4/tcp_max_orphans  
/proc/sys/net/ipv4/tcp_max_syn_backlog  
/proc/sys/net/ipv4/tcp_mem  

Other TCP Parameters to consider

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
