---
layout: post
title: Linux系统性能剖析工具（二）
---

如果一个系统负载过高时，通过Linux性能剖析工具（一）中介绍的工具，可以看出当前系统大概的状态，了解到比如什么任务的占用的CPU较多等信息。那知道这些信息后，接下来应该怎么办呢？——接下来就应该去试图找出函数级别的热点代码了。本篇将会简单介绍strace（system call trace）,ltrace(library trace在函数性能剖析中的用法。

### strace

	b20yang@ubuntu$ strace -c dd bs=1024 count=1024k if=/dev/zero of=/dev/null
	1048576+0 records in
	1048576+0 records out
	1063256064 bytes (1.1 GB) copied, 40.2985 s, 26.4 MB/s
	% time     seconds  usecs/call     calls    errors syscall
	------ ----------- ----------- --------- --------- ----------------
	 51.91    0.003715           0   1048579           read
	 48.09    0.003442           0   1048579           write
	  0.00    0.000000           0        18        11 open
	  0.00    0.000000           0        10           close
	  0.00    0.000000           0         5           fstat
	  0.00    0.000000           0         1           lseek
	  0.00    0.000000           0        11           mmap
	  0.00    0.000000           0         4           mprotect
	  0.00    0.000000           0         2           munmap
	  0.00    0.000000           0         3           brk
	  0.00    0.000000           0         4           rt_sigaction
	  0.00    0.000000           0         3         3 access
	  0.00    0.000000           0         2           dup2
	  0.00    0.000000           0         1           execve
	  0.00    0.000000           0         1           arch_prctl
	------ ----------- ----------- --------- --------- ----------------
	100.00    0.007157               2097223        14 total

strace, 系统调用（system call trace）。通过选项**-c**可以统计到该任务各个系统调用的运行时间，从上例中的输出结果可以看到，read、write系统调用占了几乎100%的执行时间，另外从calls列也可以看到read/write系统调用的次数基本符合dd命令的count输入，这些数据基本符合我们对dd任务的预期。再看下面的例子：

	b20yang@ubuntu$ dd bs=1G count=1k if=/dev/zero of=/dev/null&
	[1] 3087
	b20yang@ubuntu$ strace -T -p 3087
	read(0, "0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 1073741824) = 1073741824 <0.574003>
	write(1, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 1073741824) = 1073741824 <0.000011>
	read(0, "0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 1073741824) = 1073741824 <0.572800>
	write(1, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 1073741824) = 1073741824 <0.000013>
	read(0, "0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 1073741824) = 1073741824 <0.573986>
	write(1, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 1073741824) = 1073741824 <0.000010>
	read(0, "0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 1073741824) = 1073741824 <0.573231>
	write(1, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 1073741824) = 1073741824 <0.000011>

通过选项-p [pid]可以将strace attach到正在运行的某个进程上，而选项-T则是告知strace统计每次系统调用的时间。上例中pid 3087为dd任务的进程id，同样从输出中可以看到该进程一直在所预期的read/write。

###ltrace

	b20yang@ubuntu$ ltrace -c dd bs=1024 count=1k if=/dev/zero of=/dev/null
	1024+0 records in
	1024+0 records out
	1048576 bytes (1.0 MB) copied, 0.218582 s, 4.8 MB/s
	% time     seconds  usecs/call     calls      function
	------ ----------- ----------- --------- --------------------
	 48.64    0.091193          89      1024 read
	 48.21    0.090370          88      1024 write
	  0.34    0.000632         316         2 dcgettext
	  0.22    0.000421          70         6 strchr
	  0.22    0.000405         135         3 __fprintf_chk
	  0.20    0.000383          95         4 sigaction
	  0.19    0.000354          70         5 strlen
	  0.19    0.000351          87         4 close
	  0.19    0.000350          70         5 __errno_location
	  0.17    0.000313         313         1 setlocale
	  0.10    0.000187          93         2 open
	  0.09    0.000175          87         2 dup2
	  0.08    0.000154          77         2 localeconv
	  0.08    0.000148          74         2 sigaddset
	  0.08    0.000148          74         2 sigismember
	  0.08    0.000146          73         2 clock_gettime
	  0.08    0.000142          71         2 __ctype_b_loc
	  0.08    0.000141          70         2 __freading
	  0.07    0.000140          70         2 __strtoul_internal
	  0.05    0.000089          89         1 fclose
	  0.05    0.000087          87         1 lseek
	  0.04    0.000084          84         1 __cxa_atexit
	  0.04    0.000082          82         1 __sprintf_chk
	  0.04    0.000082          82         1 getpagesize
	  0.04    0.000077          77         1 bindtextdomain
	  0.04    0.000077          77         1 getopt_long
	  0.04    0.000076          76         1 dcngettext
	  0.04    0.000076          76         1 memmove
	  0.04    0.000075          75         1 sigemptyset
	  0.04    0.000075          75         1 strrchr
	  0.04    0.000075          75         1 malloc
	  0.04    0.000074          74         1 textdomain
	  0.04    0.000074          74         1 getenv
	  0.04    0.000071          71         1 fileno
	  0.04    0.000070          70         1 __fpending
	  0.04    0.000070          70         1 fflush
	------ ----------- ----------- --------- --------------------

ltrace，和strace的功能非常类似，甚至很多命令选项都一样，如ltrace也可以attach到指定进程上，也可以指定-T选项统计每个调用的时间。从上面的输出结果就可以看出来，最大的区别是：除了系统调用外ltrace还追踪了一些库函数的调用。这哥俩其实从实现的原理上也很类似：

	b20yang@ubuntu$ ltrace -e trace=ptrace ltrace uptime
	ptrace(PTRACE_SYSCALL, 5293, 0, SIG_0)  = 0
	--- SIGCHLD {si_signo=SIGCHLD, si_code=CLD_TRAPPED, si_pid=5293, si_status=SIGTRAP, si_utime=0, si_stime=0} ---
	ptrace(PTRACE_GETREGSET, 5293, NT_PRSTATUS, [{0x66a6c0, 216}]) = 0
	close(2ptrace(PTRACE_SYSCALL, 5293, 0, SIG_0)  = 0
	--- SIGCHLD {si_signo=SIGCHLD, si_code=CLD_TRAPPED, si_pid=5293, si_status=SIGTRAP, si_utime=0, si_stime=0} ---
	ptrace(PTRACE_GETREGSET, 5293, NT_PRSTATUS, [{0x66a6c0, 216}]) = 0
	)                                = 0
	ptrace(PTRACE_SYSCALL, 5293, 0, SIG_0)  = 0
	--- SIGCHLD {si_signo=SIGCHLD, si_code=CLD_TRAPPED, si_pid=5293, si_status=SIGTRAP, si_utime=0, si_stime=0} ---
	ptrace(PTRACE_GETREGSET, 5293, NT_PRSTATUS, [{0x66a6c0, 216}]) = 0
	exit_group(0)                           = ?
	ptrace(PTRACE_SYSCALL, 5293, 0, SIG_0)  = 0
	--- SIGCHLD {si_signo=SIGCHLD, si_code=CLD_EXITED, si_pid=5293, si_status=0, si_utime=0, si_stime=0} ---

无论是strace还是ltrace，他们都是通过ptrace系统调用来实现的。上面命令选项**-e**告诉ltrace仅仅追踪指定的ptrace系统调用，类似地，也可以从strace中得到对ptrace大量的调用。因此，这哥俩还有一个特性也很相似，就是trace的开销都很大，这是题外话。
