---
layout: post
title: Unix高级环境编程中的一个Bug
---

这个bug发现于Unix高级环境编程2013年出版的第三版第17章第7节——An Open Server，在这一节里作者开发了一个Client-Server结构的示例程序，让读者可以把本章前面几节的一些内容——其中包括Unix Naming Domain Socket, Unique Connection和Passing File Descriptor等知识可以很好地融会贯通起来。不过这个例子在IA-64的Linux的平台下运行时会遇到一个bug，这个bug会导致Client收到的数据和Server发送的数据不匹配，使程序运行时不能产生预期的行为。

17.7示例的主要功能是为了客户端和服务器的文件共享，通过两者之间共享文件描述符而不是的文件内容使进程间的文件共享更加高效。其中客户端和服务器端通过命名的Unix域套接字进行进程间的IPC通信。大概的流程是这样的：一、客户端可以向服务器端发送打开指定路径的文件的请求；二、服务器收到客户端的请求后，会尝试打开客户端指定路径的文件，把打开的文件描述符回传给客户端；接下来客户端可以直接使用文件所支持的操作来访问文件的内容了。为此，作者开了几个API用来发送文件描述符、接受文件描述符以及对应的错误处理：

	int send_fd(int fd, int fd_to_send);
	int recv_fd(int fd, ssize_t (*userfunc)(int, const void*, size_t));
	int send_err(int fd, int status, const char *errmsg);

通过send_fd接口，可以将文件描述符fd_to_send通过套接字fd发送给目标进程；相应地，接受方可以通过recv_fd接受其他进程发给自己的文件描述符。<strong>事实上，本篇博客要说的bug就是出现在这两个函数之中！</strong>在调用套接字的接口sendmsg发送消息之前，send_fd需要对消息进行初始化，这个消息对应的结构体类型为msghdr，其中包括存放用户数据的缓存、控制消息（Control Message，Linux中的man page可以看到控制消息的详细定义cmsghdr和说明）、控制消息长度等结构体成员。<strong>而问题就是出在msghdr结构体的初始化这里。</strong>摘录书中的一小段代码如下：

	#define CONTROLLEN CMSG_LEN(sizeof(int))
	int send_fd(int fd, int fd_to_send)
	{
		//...
		struct msghdr msg;
		cmptr->cmsg_len = CONTROLLEN;
		msg.msg_control = cmptr;
		msg.msg_controllen = CONTROLLEN;
		*(int*)CMSG_DATA(cmptr) = fd_to_send;
		//...
	}

msg的成员msg_controllen表示控制消息的长度。此处，msg_controllen会被赋值成CONTROLLEN这个宏的值，而这个宏被定义成CMSG_LEN(sizeof(int))。CMSG_LEN是POSIX定义的一个宏，用来计算控制消息的长度。控制消息包括控制消息的头和控制消息承载的payload数据，sizeof(int)就是此例所需的payload长度——用来存放一个文件描述符；而后CSMG_LEN宏会加上控制消息头长度，算出来就是控制消息的长度了。

**在x86_64的Linux环境下，控制消息头的长度为16个字节，所以在此例中控制消息长总共为20个字节。send_fd在初始化消息完成后，会调用系统调用sendmsg将其发出，然后客户端调用recvmsg将其接收，然后解析控制消息得到发送过来的文件描述符。但是执行此程序后，发现收到的文件描述符值和发送的文件描述符的值不匹配，同时<strong>发现msg_controllen长度也变了——发送的值是20个字节而收到的却是24字节。那么问题出在什么地方呢？检查内核系统调用sendmsg的调用栈源代码可以看到，在x86-64的运行环境下，当消息从用户空间传到内核空间时，控制消息的长度被要求与64位对齐，所以20个字节的长度会被修改成24个字节。这里就是客户端和服务器段控制消息的长度不一的问题所在了。**

**事实上，在cmsg的mannual page里面有如下一段关于cmsg初始化的描述，msg_controllen需要使用CMSG_SPACE的这个宏来初始化——CMSG_SPACE这个宏会自动控制消息作对齐操作。而Unix高级环境编程书中使用CMSG_LEN宏来初始化msg_controllen的方式是不对的。**
>To create ancillary data, first initialize the msg_controllen member of the msghdr with the length of the control  message buffer.  Use CMSG_FIRSTHDR() on the msghdr to get the first control message and CMSG_NXTHDR() to get all subsequent ones.  In each control message, initialize cmsg_len (with CMSG_LEN()),  the  other  cmsghdr header fields, and the data portion using CMSG_DATA().  Finally, the msg_controllen field of the msghdr should be set to the sum of the CMSG_SPACE() of the length of all control messages in the buffer.  For more  information on the msghdr, see recvmsg(2).  

以上就是关于这个bug的一个总结。：）
