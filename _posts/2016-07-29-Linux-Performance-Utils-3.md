---
layout: post
title: Linux系统性能剖析工具（三）
---

前面[（一）](http://yangbiao.info/2016/07/20/Linux-Performance-Utils-1)、[（二）](http://yangbiao.info/2016/07/27/Linux-Performance-Utils-2) 两篇文章分别介绍了一些基本的剖析工具，此篇中将会介绍一个更加强大的工具——perf。perf不仅可以统计软件层面的追踪点（tracepoint）——如系统调用调度等，还可以追踪统计系统的软硬件事件，如上下文切换（软件事件）、CPU迁移（软件事件）、缓存失效（硬件事件）等。perf中包含一系列的子命令，常用的有list，record，report，sched，top，下面通过例子列举一些基本用法。

### perf sched

	b20yang@ubuntu$ sudo perf sched record sleep 10
	b20yang@ubuntu$ sudo perf sched latency
	 -----------------------------------------------------------------------------------------------------------------
	  Task                  |   Runtime ms  | Switches | Average delay ms | Maximum delay ms | Maximum delay at       |
	 -----------------------------------------------------------------------------------------------------------------
	  jbd2/sda1-8:161       |      0.170 ms |        3 | avg:    0.016 ms | max:    0.023 ms | max at: 7188685.842895 s
	  unity-scope-loa:2154  |      0.178 ms |        2 | avg:    0.015 ms | max:    0.020 ms | max at: 7188683.454347 s
	  nmbd:1514             |      0.337 ms |        3 | avg:    0.013 ms | max:    0.015 ms | max at: 7188682.641534 s
	  kworker/1:1H:159      |      0.044 ms |        2 | avg:    0.012 ms | max:    0.015 ms | max at: 7188685.834525 s
	  unity-music-dae:2138  |      0.177 ms |        3 | avg:    0.012 ms | max:    0.021 ms | max at: 7188686.454122 s
	  smbd:1532             |      0.050 ms |        2 | avg:    0.012 ms | max:    0.016 ms | max at: 7188686.659628 s
	  khugepaged:27         |      0.070 ms |        1 | avg:    0.012 ms | max:    0.012 ms | max at: 7188689.716026 s
	  watchdog/0:12         |      0.000 ms |        2 | avg:    0.011 ms | max:    0.012 ms | max at: 7188688.148021 s
	  kworker/1:1:12299     |      0.337 ms |       15 | avg:    0.011 ms | max:    0.018 ms | max at: 7188685.804037 s
	  kworker/0:1H:160      |      0.018 ms |        1 | avg:    0.011 ms | max:    0.011 ms | max at: 7188685.834653 s
	  kworker/0:1:14168     |      0.428 ms |       15 | avg:    0.011 ms | max:    0.016 ms | max at: 7188680.816030 s
	  kworker/u16:1:15108   |      0.721 ms |       21 | avg:    0.011 ms | max:    0.017 ms | max at: 7188687.328038 s
	  systemd-journal:210   |      0.040 ms |        1 | avg:    0.010 ms | max:    0.010 ms | max at: 7188686.523157 s
	  rtkit-daemon:1226     |      0.053 ms |        1 | avg:    0.010 ms | max:    0.010 ms | max at: 7188686.659587 s

上面输出是在“perf sched record"之后，通过"perf sched latency"这条命令解析出来的结果。switch列表示上下文切换的次数，Average Delay表示平均的调度延迟。 perf sched还有其他好几个命令，包括map/replay可以从其他视角看linux系统调度的过程。  

	b20yang@ubuntu$ sudo perf sched replay
	run measurement overhead: 80 nsecs
	sleep measurement overhead: 59119 nsecs
	the run test took 1000076 nsecs
	the sleep test took 1060769 nsecs
	nr_run_events:        1468
	nr_sleep_events:      1767
	nr_wakeup_events:     760
	task      0 (     systemd-timesyn:       364), nr_events: 1
	task      "1 (     systemd-timesyn:       375), nr_events: 1
	task      2 (     accounts-daemon:       542), nr_events: 1
	task      3 (     accounts-daemon:       638), nr_events: 7
	task      4 (     accounts-daemon:       651), nr_events: 1
	task      5 (      NetworkManager:       546), nr_events: 1
	task      6 (      NetworkManager:       647), nr_events: 1
	task      7 (      NetworkManager:       664), nr_events: 1
	task      8 (      NetworkManager:       695), nr_events: 1
	task      9 (            thermald:       549), nr_events: 1
	task     10 (            thermald:       694), nr_events: 10
	task     11 (        ModemManager:       553), nr_events: 1

上面输出是“perf sched replay”的输出结果，可以依次看到上下面切换的过程。
  	
	b20yang@ubuntu:~/tmp$ sudo perf sched map
	      *A0       7188680.765033 secs A0 => perf:15304
	  *.   A0       7188680.765074 secs .  => swapper:0
	   .  *B0       7188680.765173 secs B0 => migration/1:14
	   .  *.        7188680.765185 secs
	  *A0  .        7188680.765186 secs
	  *.   .        7188680.765878 secs
	   .  *C0       7188680.768033 secs C0 => rcu_sched:7
	   .  *.        7188680.768050 secs
	  *D0  .        7188680.768052 secs D0 => rcuos/0:9
	  *.   .        7188680.768083 secs
	   .  *C0       7188680.768083 secs
	   .  *.        7188680.768089 secs
	   .  *C0       7188680.772019 secs
	   .  *.        7188680.772029 secs
	  *D0  .        7188680.772032 secs
	   D0 *E0       7188680.772043 secs E0 => rcuos/1:18
	   D0 *.        7188680.772055 secs
	  *.   .        7188680.772068 secs
	   .  *F0       7188680.800438 secs F0 => java:1409
	   .  *.        7188680.800483 secs
	   .  *G0       7188680.804023 secs G0 => kworker/1:1:12299
	   .  *.        7188680.804036 secs
	  *H0  .        7188680.816030 secs H0 => kworker/0:1:14168
	  *.   .        7188680.816039 secs
	  *I0  .        7188680.820024 secs I0 => kworker/u16:1:15108
	  *.   .        7188680.820054 secs
	   .  *J0       7188680.833868 secs J0 => thermald:694
上面输出是“perf sched map"的输出结果。信息也很一目了然，可以清楚的看到每个核心上面的调度情况。

### perf list

	b20yang@ubuntu$ perf list

	List of pre-defined events (to be used in -e):
	  cpu-cycles OR cycles                               [Hardware event]
	  instructions                                       [Hardware event]
	  cache-references                                   [Hardware event]
	  cache-misses                                       [Hardware event]
	  branch-instructions OR branches                    [Hardware event]
	  branch-misses                                      [Hardware event]
	  bus-cycles                                         [Hardware event]
	  ref-cycles                                         [Hardware event]
	
	  cpu-clock                                          [Software event]
	  task-clock                                         [Software event]
	  page-faults OR faults                              [Software event]
	  context-switches OR cs                             [Software event]
	  cpu-migrations OR migrations                       [Software event]
	  minor-faults                                       [Software event]
	  major-faults                                       [Software event]
	  alignment-faults                                   [Software event]
	  emulation-faults                                   [Software event]
	  dummy                                              [Software event]
	
	  L1-dcache-loads                                    [Hardware cache event]
	  L1-dcache-load-misses                              [Hardware cache event]
	  L1-dcache-stores                                   [Hardware cache event]
	  L1-dcache-store-misses                             [Hardware cache event]
	  L1-dcache-prefetches                               [Hardware cache event]
	  L1-icache-loads                                    [Hardware cache event]
	  L1-icache-load-misses                              [Hardware cache event]
	  LLC-loads                                          [Hardware cache event]
	  LLC-load-misses                                    [Hardware cache event]
	  LLC-stores                                         [Hardware cache event]
	  LLC-store-misses                                   [Hardware cache event]
	  dTLB-loads                                         [Hardware cache event]
	  dTLB-load-misses                                   [Hardware cache event]
	  dTLB-stores                                        [Hardware cache event]
	  dTLB-store-misses                                  [Hardware cache event]
	  iTLB-loads                                         [Hardware cache event]
	  iTLB-load-misses                                   [Hardware cache event]
	  branch-loads                                       [Hardware cache event]
	  branch-load-misses                                 [Hardware cache event]
	  branch-instructions OR cpu/branch-instructions/    [Kernel PMU event]
	  branch-misses OR cpu/branch-misses/                [Kernel PMU event]
	  bus-cycles OR cpu/bus-cycles/                      [Kernel PMU event]

“perf list”命令先列出所有支持的软硬件事件的信息，最后还列出了系统的所有tracepoint。

### perf top

下面是一份简单的C语言示例代码，用gcc编译成目标文件后，在系统一个终端上运行。  

	#define MAX 10000000
	int a[MAX];
	
	void loop(){
	    for(int i=0;i < MAX; i++) a[i] = i;
	}
	
	void main(){
	    while(1) {  
			loop();
	        sleep(1);
	    }
	}

然后在另外一个终端上，运行perf top得到如下输出： 
	
	perf top
	Samples: 1K of event 'cycles', Event count (approx.): 874746664
	Overhead  Shared Object                 Symbol
	  50.95%  a.out                         [.] loop
	   2.30%  [kernel]                      [k] kallsyms_expand_symbol.constprop.1
	   2.23%  libc-2.21.so                  [.] __strstr_sse2
	   1.71%  [kernel]                      [k] memcpy
	   1.71%  perf                          [.] 0x000000000005a0b7
	   1.68%  [kernel]                      [k] vsnprintf
	   1.60%  [kernel]                      [k] format_decode
	   1.14%  [kernel]                      [k] number.isra.2
	   1.14%  [kernel]                      [k] module_get_kallsym
	   1.07%  libc-2.21.so                  [.] _int_malloc
	   1.02%  libc-2.21.so                  [.] __GI___strcmp_ssse3
	   0.86%  [kernel]                      [k] string.isra.7
	   0.59%  [kernel]                      [k] apparmor_capable
	   0.57%  libc-2.21.so                  [.] _IO_getdelim
	   0.57%  [kernel]                      [k] strnlen
	   0.54%  libc-2.21.so                  [.] __libc_calloc
	   0.51%  perf                          [.] 0x0000000000061b9c
	   0.51%  perf                          [.] 0x000000000009fb63
	   0.51%  [kernel]                      [k] rcu_check_callbacks
	   0.50%  libc-2.21.so                  [.] __memset_sse2
	   0.50%  libc-2.21.so                  [.] __memcpy_sse2
	   0.49%  [kernel]                      [k] clear_page_c
	   0.49%  [kernel]                      [k] copy_user_generic_string

上面输出结果的最左侧一栏，可以看到该loop函数占用了系统的50%的cpu时间。通过方向键->进入loop函数，可以看到loop函数各个指令的cpu资源使用率。（gcc编译的时候加上-ggdb选项可以在annotate里面显示汇编语言对应的源代码）。

	       │    Disassembly of section .text:
	       │
	       │    0000000000400536 <loop>:
	       │    loop():
	       │      push   %rbp
	       │      mov    %rsp,%rbp
	       │      movl   $0x0,-0x4(%rbp)
	       │    ↓ jmp    20
	 12.81 │ d:   mov    -0x4(%rbp),%eax
	  1.32 │      cltq
	  1.22 │      mov    -0x4(%rbp),%edx
	       │      mov    %edx,0x601080(,%rax,4)
	 49.25 │      addl   $0x1,-0x4(%rbp)
	 13.65 │20:   cmpl   $0x98967f,-0x4(%rbp)
	 21.75 │    ↑ jle    d
	       │      pop    %rbp
	       │    ← retq

上面输出结果的左侧信息是表示这些指令运行时间占该函数总运行事件的比例。  

### perf report
还是上面C语言的例子，先用record命令“perf record -g ./a.out”记录统计目标文件执行的事件统计数据，然后再用“perf report”分析结果。
perf report -g flat  

	Samples: 1K of event 'cycles', Event count (approx.): 876982948, Thread: a.out(19784)
	  Children      Self  Comma  Shared Object      Symbol                                                                                                                                   ◆
	+   99.63%     0.00%  a.out  [unknown]          [.] 0x087e258d4c544155                                                                                                                   ▒
	+   99.63%     0.00%  a.out  libc-2.21.so       [.] __libc_start_main                                                                                                                    ▒
	+   99.63%     0.00%  a.out  a.out              [.] main                                                                                                                                 ▒
	+   99.63%    69.71%  a.out  a.out              [.] loop                                                                                                                                 ▒
	+   29.42%     1.07%  a.out  [kernel.kallsyms]  [k] page_fault                                                                                                                           ▒
	+   28.43%     0.08%  a.out  [kernel.kallsyms]  [k] do_page_fault                                                                                                                        ▒
	+   28.26%     0.17%  a.out  [kernel.kallsyms]  [k] __do_page_fault                                                                                                                      ▒
	+   27.76%     0.25%  a.out  [kernel.kallsyms]  [k] handle_mm_fault                                                                                                                      ▒
	+   26.40%     0.00%  a.out  [kernel.kallsyms]  [k] alloc_pages_vma                                                                                                                      ▒
	+   26.40%     0.00%  a.out  [kernel.kallsyms]  [k] __alloc_pages_nodemask                                                                                                               ▒
	+   22.48%     0.00%  a.out  [kernel.kallsyms]  [k] do_huge_pmd_anonymous_page                                                                                                           ▒

在上面输出中，左侧的overhead被分成两个部分，一个是children, 一个是self。children包含了该函数所有子函数调用的sample，self则是只包含本函数的overhead。

### perf stat

	b20yang@ubuntu:~/tmp$ perf stat -e L1-dcache-loads:k dd if=/dev/zero of=/dev/null count=1000
	1000+0 records in
	1000+0 records out
	512000 bytes (512 kB) copied, 0.000632926 s, 809 MB/s
	
	 Performance counter stats for 'dd if=/dev/zero of=/dev/null count=1000':
	
	           564,068      L1-dcache-loads
	
	       0.001411362 seconds time elapsed

通过选项“-e”可以告诉“perf stat”命令需要统计哪些事件，比如上例中指定了“L1-dcache-loads:k”，后面的k限定了发生在内核空间内的该事件（限定符u为用户空间）。
