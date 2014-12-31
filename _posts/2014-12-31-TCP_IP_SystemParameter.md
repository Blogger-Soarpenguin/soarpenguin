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

