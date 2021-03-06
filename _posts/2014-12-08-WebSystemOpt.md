##### 系统优化看法 #####
***

可以从两个方面来看系统的优化
  
1. 提高系统的吞吐量或优化系统响应时间;  
2. 改善系统的容错;

***

1.提高系统吞吐量 或者 优化系统响应时间
    1). 可以从操作系统调优方向入手(tcp/ip系统参数调优/io参数)

        增大拥塞窗口初始值 cwnd (接收窗口 滑动窗口协议)  
        慢启动重启  sysctl net.ipv4.tcp_slow_start_after_idle  
        启动窗口缩放  
        减少传输冗余数据  
        压缩要传输的数据  
        尽最大可能重用已经建立的TCP连接  
        TCP/IP  TIME_WAIT状态数异常  

    2). 调研pagespeed:
        这是google开发的针对加速web应用的一个开源项目, 作为apache及nginx服务器模块配置使用;
        给出了几十项优化项: 例如html代整合优化(比如去除页面中的空白行,代码缩进之类的), 对js自动压缩及合并, 还有其他高级优化项;

    3). 提高php解析器的性能;
        比如可以尝试使用facebook开源的hhvm; 这个在baidu首屏展示速度优化中有5~10%的响应时间提升;


2.改善系统的容错(这个对于系统架构调整较大,可以作为长远目标考虑)
    目前在前端调度方面是没有重试容错功能的,就是在apache/nginx访问后端出现问题时是直接返回失败结果,  
    而不是尝试再查询一次(本机房或甚至是跨机房重查一次), 重试能挡住相当一部分由于后台抖动问题导致的  
    失败而影响用户体验;


***
##### 网站速度性能指标:(监测/标准/优化)
***

1. 首屏时间  
    浏览器完成页面第一屏渲染时间, 即肉眼能看到显示器第一屏网页打开的时间, 是用户的第一感知;
2. 总下载时间  
    打开一个网页总的消耗时间, 即网页从开始加载到加载结束的时间, 这个时间包括多屏网页的所有网页元素加载完的总时间;
3. 首包时间  
    从浏览器发送HTTP请求结束开始, 到收到web服务器返回的第一个数据包的消耗时间.
4. 建立连接时间  
    浏览器和web服务器建立TCP/IP连接消耗的时间;
5. DNS时间  
    一个页面需要加载N个元素, 需要进行多次DNS解析, DNS服务器性能及效率决定网页速度;
6. 总下载字节数  
    每个元素页面大小的总和, 决定网页加载速度;
7. 网页元素数量  
    从web服务器下载的元素数量, 元素越多, 与web服务器的交互越多, 网络传输及通信越频繁;
8. 基础页面下载时间  
    包含DNS时间, 建立连接时间, SSL握手时间 决定首屏时间;