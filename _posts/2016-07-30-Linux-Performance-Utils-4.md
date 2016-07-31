---
layout: post
title: Linux系统性能剖析工具（四）
---

前面[（一）](http://yangbiao.info/2016/07/20/Linux-Performance-Utils-1)、[（二）](http://yangbiao.info/2016/07/27/Linux-Performance-Utils-2)、[（三）](http://yangbiao.info/2016/07/29/Linux-Performance-Utils-3)分别介绍了如何剖析系统CPU、内存、磁盘等性能的工具，主要关注点在单个计算机内部；而在接下来的这篇文章中，会将我们的视野伸向更加广阔的互联网络，介绍一些剖析网络性能相关的工具，包括ping，netstat，tcpdump，iftop，nethogs等。  

 
### ping

ping是一个测试网络可达与否的工具，同时也可测量当前主机到对端主机的RTT (Round-Trip Time)。下面是ping本地localhost的输出：

	b20yang@ubuntu$ ping -c 5 localhost
	PING localhost (127.0.0.1) 56(84) bytes of data.
	64 bytes from localhost (127.0.0.1): icmp_seq=1 ttl=64 time=0.061 ms
	64 bytes from localhost (127.0.0.1): icmp_seq=2 ttl=64 time=0.045 ms
	64 bytes from localhost (127.0.0.1): icmp_seq=3 ttl=64 time=0.061 ms
	64 bytes from localhost (127.0.0.1): icmp_seq=4 ttl=64 time=0.048 ms
	64 bytes from localhost (127.0.0.1): icmp_seq=5 ttl=64 time=0.052 ms
	
	--- localhost ping statistics ---
	5 packets transmitted, 5 received, 0% packet loss, time 3998ms
	rtt min/avg/max/mdev = 0.045/0.053/0.061/0.009 ms

上面输出第一列是ping包的大小，ping包大小也可以通过-s选项指定；最后一列是该包RTT的时间。输出的最后一行包含了RTT的最小、平均、最大的时间。以下是ping命令的几个常规用法：
 
	// -s ping localhost with max packet size
	ping -s 65507 localhost  
	
	//-c, count
	ping -c 5 localhost  
	
	// -f, flood ping with max packet size
	ping -f -s 65507 localhost 

### netstat
netstat可以查看系统当前所有网络连接的状态、路由表及性能相关的统计，比如可以用netstat -ts显示系统tcp连接的统计信息。可用watch命令配合netstat，检查系统tcp的收发状态：

	b20yang@ubuntu$ watch -d netstat -ts
	IcmpMsg:
	    InType0: 271693614
	    InType3: 88
	    InType8: 271656227
	    InType11: 21
	    OutType0: 271656227
	    OutType3: 124
	    OutType8: 271693627
	Tcp:
	    63 active connections openings
	    8 passive connection openings
	    58 failed connection attempts
	    0 connection resets received
	    4 connections established
	    19130904 segments received
	    19087366 segments send out
	    22 segments retransmited
	    0 bad segments received.
	    58 resets sent

从上面的输出中可以看到，系统tcp连接，收发的报文段数目等信息。对于udp协议来说，可以用netstat -us命令来查看。以下是netstat命令的一些其他常规用法：
	//dispaly interface
	netstat -i

	//display all tcp connections state 
	netstat -ta

	//dispaly all udp connections state
	netstat -ua

	//dispaly the pid and program of udp connection 
	sudo netstat -nlpu

	//dispaly the pid and program of tcp connection 
	sudo netstat -nlpu

	//dispaly user related information with -e 
	sudo netstat -te

### tcpdump

先用一个简单的例子，说明tcpdump的作用。案例的场景是这样的：有两台主机，在尾号147的主机上ping另外一台尾号为156的主机，然后在156主机上执行下面命令，如下：

	b20yang@ubuntu$ tcpdump -n -i eth0 icmp
	01:49:09.311201 IP 10.141.115.147 > 10.140.163.156: ICMP echo request, id 1, seq 38336, length 64
	01:49:09.311250 IP 10.140.163.156 > 10.141.115.147: ICMP echo reply, id 1, seq 38336, length 64
	01:49:10.282256 IP 10.141.115.147 > 10.140.163.156: ICMP echo request, id 1, seq 38337, length 64
	01:49:10.282303 IP 10.140.163.156 > 10.141.115.147: ICMP echo reply, id 1, seq 38337, length 64
	01:49:11.283242 IP 10.141.115.147 > 10.140.163.156: ICMP echo request, id 1, seq 38338, length 64
	01:49:11.283291 IP 10.140.163.156 > 10.141.115.147: ICMP echo reply, id 1, seq 38338, length 64
	01:49:12.285230 IP 10.141.115.147 > 10.140.163.156: ICMP echo request, id 1, seq 38339, length 64
	01:49:12.285268 IP 10.140.163.156 > 10.141.115.147: ICMP echo reply, id 1, seq 38339, length 64

可以看到输出中都是ICMP echo的请求和恢复的包，这是因为指定了icmp选项把其他的数据包都过滤了。

再来看一个例子。对先前主机147端的ping命令加上“-p"选项，可以把ping包的内容都填充为0xff，请求端的命令是这样的：“ping -p ff 10.140.163.156”；然后在接收端156的主机上，执行命令“tcpdump -n -X -i eth0 icmp”:

	b20yang@ubtunu$ sudo tcpdump -nX -i eth0 icmp
	tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
	listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
	02:08:45.515524 IP 10.141.115.147 > 10.140.163.156: ICMP echo request, id 1, seq 39510, length 64
	        0x0000:  4500 0054 7d3a 4000 3801 9926 0a8d 7393  E..T}:@.8..&..s.
	        0x0010:  0a8c a39c 0800 3125 0001 9a56 b6b1 9d57  ......1%...V...W
	        0x0020:  0000 0000 d779 0100 0000 0000 ffff ffff  .....y..........
	        0x0030:  ffff ffff ffff ffff ffff ffff ffff ffff  ................
	        0x0040:  ffff ffff ffff ffff ffff ffff ffff ffff  ................
	        0x0050:  ffff ffff                                ....
	02:08:45.515559 IP 10.140.163.156 > 10.141.115.147: ICMP echo reply, id 1, seq 39510, length 64
	        0x0000:  4500 0054 4f56 0000 4001 ff0a 0a8c a39c  E..TOV..@.......
	        0x0010:  0a8d 7393 0000 3925 0001 9a56 b6b1 9d57  ..s...9%...V...W
	        0x0020:  0000 0000 d779 0100 0000 0000 ffff ffff  .....y..........
	        0x0030:  ffff ffff ffff ffff ffff ffff ffff ffff  ................
	        0x0040:  ffff ffff ffff ffff ffff ffff ffff ffff  ................
	        0x0050:  ffff ffff                                ....
	02:08:46.517902 IP 10.141.115.147 > 10.140.163.156: ICMP echo request, id 1, seq 39511, length 64
	        0x0000:  4500 0054 7d3d 4000 3801 9923 0a8d 7393  E..T}=@.8..#..s.
	        0x0010:  0a8c a39c 0800 b41b 0001 9a57 b7b1 9d57  ...........W...W
	        0x0020:  0000 0000 5382 0100 0000 0000 ffff ffff  ....S...........
	        0x0030:  ffff ffff ffff ffff ffff ffff ffff ffff  ................
	        0x0040:  ffff ffff ffff ffff ffff ffff ffff ffff  ................
	        0x0050:  ffff ffff                                ....
	02:08:46.517937 IP 10.140.163.156 > 10.141.115.147: ICMP echo reply, id 1, seq 39511, length 64
	        0x0000:  4500 0054 4f69 0000 4001 fef7 0a8c a39c  E..TOi..@.......
	        0x0010:  0a8d 7393 0000 bc1b 0001 9a57 b7b1 9d57  ..s........W...W
	        0x0020:  0000 0000 5382 0100 0000 0000 ffff ffff  ....S...........
	        0x0030:  ffff ffff ffff ffff ffff ffff ffff ffff  ................
	        0x0040:  ffff ffff ffff ffff ffff ffff ffff ffff  ................
	        0x0050:  ffff ffff                                ....
	02:08:47.520303 IP 10.141.115.147 > 10.140.163.156: ICMP echo request, id 1, seq 39512, length 64
	        0x0000:  4500 0054 7d3f 4000 3801 9921 0a8d 7393  E..T}?@.8..!..s.
	        0x0010:  0a8c a39c 0800 2411 0001 9a58 b8b1 9d57  ......$....X...W
	        0x0020:  0000 0000 e28b 0100 0000 0000 ffff ffff  ................
	        0x0030:  ffff ffff ffff ffff ffff ffff ffff ffff  ................
	        0x0040:  ffff ffff ffff ffff ffff ffff ffff ffff  ................
	        0x0050:  ffff ffff                                ....

在输出的结果中，可以看到收到的所有ping包的payload都为0xff。新加的"-X"选项可以把整个数据包都抓出来，这对分析一些协议如何工作的时候非常有用。

上面例子中对tcpdump的功能做了简单的介绍，不过tcpdump的功能远远不止于此，此处列举一些简单常用的tcpdump场景：

	// listen on interface eth0
	tcpdump -i eth0

	// -c packet count, 
	tcpdump -c 5 -i eth0

	// listen on interface eth0 and port 22
	tcpdump -i eth0 port 22

	// listen on interface with filter dst and port 
	tcpdump -i eth0 dst 10.141.115.147 and port 22

	// -w save the capture into the specifed file
	tcpdump -w dump.pcap -i eth0 dst 10.141.115.147 and port 22

	// -r read the file
	tcpdump -r dump.pcap

	// listen with specified protocol
	tcpdump -i eth0 tcp

	// -A, prints the dump in ASCII format, easy to read for HTTP content.
	tcpdump -i eth0 -A port 8080

	// -XX, prints the dump in hex and ascii formant
	tcpdump -i eht0 -XX port 8080

### iftop
iftop是一个类top的工具，通过它可以很方便地看到各对端主机对系统网卡的使用排名。
	
	                                     12.5Kb                               25.0Kb                               37.5Kb                               50.0Kb                          62.5Kb
	└────────────────────────────────────┴────────────────────────────────────┴────────────────────────────────────┴────────────────────────────────────┴─────────────────────────────────────
	10.140.163.156                                                                  => cnbedc001.china.nsn-net.net                                                      584b    581b    581b
	                                                                                <=                                                                                  628b   1.00Kb  1.00Kb
	10.140.163.156                                                                  => 10.141.115.160                                                                  1.05Kb   896b    896b
	                                                                                <=                                                                                  160b    320b    320b
	255.255.255.255                                                                 => 10.140.162.241                                                                     0b      0b      0b
	                                                                                <=                                                                                 2.48Kb   845b    845b
	10.140.163.156                                                                  => cnbedc002.nsn-intra.net                                                            0b    245b    245b
	                                                                                <=                                                                                    0b    208b    208b
	10.140.162.255                                                                  => 10.140.162.223                                                                     0b      0b      0b
	                                                                                <=                                                                                    0b    325b    325b


### nethogs
nethogs也是一个类top的工具，通过它可以方便的看到各个进程对系统网卡的使用排名。

	NetHogs version 0.8.0
	
	  PID USER     PROGRAM                                                                                                                                DEV        SENT      RECEIVED
	?     root     10.140.163.156:8080-10.144.1.24:18507                                                                                                             1.641       0.505 KB/sec
	21817 b20yang  sshd: b20yang@pts/4                                                                                                                    eth0       0.154       0.059 KB/sec
	?     root     10.140.163.156:8080-10.144.1.24:18510                                                                                                             0.000       0.012 KB/sec
	?     root     10.140.163.156:8080-10.144.1.24:18509                                                                                                             0.000       0.012 KB/sec
	?     root     10.140.163.156:8080-10.144.1.24:18508                                                                                                             0.000       0.012 KB/sec
	?     root     10.140.163.156:8080-10.144.1.24:18506                                                                                                             0.000       0.012 KB/sec
	24862 root     ssh                                                                                                                                    eth0       0.000       0.000 KB/sec
	?     root     10.140.163.156:8080-10.144.1.24:18392                                                                                                             0.000       0.000 KB/sec
	?     root     10.140.163.156:8080-10.144.1.24:18505                                                                                                             0.000       0.000 KB/sec
	?     root     10.140.163.156:8080-10.144.1.24:18306                                                                                                             0.000       0.000 KB/sec
	?     root     10.140.163.156:8080-10.144.1.24:18393                                                                                                             0.000       0.000 KB/sec
	?     root     10.140.163.156:8080-10.144.1.24:18395                                                                                                             0.000       0.000 KB/sec
	1217  tomcat7  /usr/lib/jvm/java-7-openjdk-amd64/bin/java                                                                                             eth0       0.000       0.000 KB/sec
	?     root     10.140.163.156:8080-10.144.1.24:18394                                                                                                             0.000       0.000 KB/sec
	?     root     unknown TCP                                                                                                                                       0.000       0.000 KB/sec
	
	  TOTAL                                                                                                                                                          1.795       0.610 KB/sec
