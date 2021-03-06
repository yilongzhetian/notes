## 术语  
* ### 平均负载  
* ### cpu使用率  
* ### cpu上下文切换
* ### cpu性能指标
* ### cpu进程运行状态
### 平均负载  
  平均负载指的是单位时间内系统处在可运行状态和不可中断进程的平均进程数,可运行状态指的是系统处于R状态的进程数，不可中断进程指的是系统处于D状态的进程数，即不可中断进程数，（等待IO）磁盘读写不能被打断  
 ~~~
 uptime
 15:05:05 up 5 days, 1:06, 1 user, load average:0.01,0.04,0.05
 ~~~
 后三个数字指的是系统1分钟，5分钟，15分钟的平均负载  
 合理的平均负载数与cpu个数有关，当平均负载为1时，cpu个数为1时，则cpu满负荷运行，当cpu个数为2是，则只占用了50%的cpu，依次次类推，当且仅当平均负载数大于cpu个数时，则认为cpu超负荷运行  
 ~~~
 #查看cpu个数
 grep 'model name' /proc/cpuinfo|wc -l  
 ~~~
 ### cpu使用率
  单位时间内cpu繁忙程度，不包含等待cpu，cpu使用率不一定等于平均负载数，在cpu密集型作业中，基本相同，在io密集型作业中，cpu使用率可能户小于平均负载数

  ### cpu上下文切换  
  1.进程上下文切换   
  不同进程切换时，需要保存进程的虚拟内存，程序计数器，cpu寄存器，栈等资源，系统调用属于同一进程内不同内核状态调用（由用户态切换为内核态，调用完成后由内核态再切换为用户态，切换过程中，只保存部分cpu寄存器，指令位置等少量资源），进程切换时，虚拟内存的更新会造成TLB的更新，内存访问变慢  
  进程切换场景：1）优先级 2）分配的时间片用完 3）进程自身sleep 4）等待资源  5)硬件中断  
  2.线程上下文切换  
  同一进程之间的线程上下文切换开销小于进程间的上下文切换，因为同一进程的线程切换时，只用保存自己私有的pc计数器和内存栈，线程共享的虚拟内存等公共资源不需要切换  
  3.中断上下文切换  
同一个cpu，中断比进程拥有更高的优先级，切换时，只用保存必须的寄存器，内核堆栈，中断参数等资源，中断程序应短小精悍，快速完成  
切换分自愿切换和非资源切换，自愿切换一般是发生等待资源场景(IO/内存不满足),非自愿切换是指分的时间片用完，被系统强制调出，一般发生在大量进程争抢cpu  
### cpu性能指标
* us =>用户cpu使用率
* sy => 内核cpu使用率
* ni => 低优先级用户cpu使用率
* wa =>io等待cpu使用率（不包含io等待时间）
* id =>空闲cpu时间
* hi =>硬中断cpu使用率
* si =>软中断cpu使用率
* st =>系统运行在虚拟机中被其他虚拟机占用的时间  
top 按下数字1 展示各个cpu使用率
cpu使用率=1-cpu空闲时间/总时间  
### cpu进程运行状态  
 * R =>正在运行的进程  
 * D =>不可中断进程（进程正在等待io读写）
 * S =>可中断休眠进程
 * Z =>僵尸进程（父进程没有及时回收运行完毕的子进程的资源，可造成pid用尽） 
 * I =>空闲状态（用在不可中断内核线程睡眠上，D状态会导致平均负载升高，I不会）  
 * T =>暂停或跟踪状态
 * x => 死亡状态  
 ### 工具验证
 ~~~
 #需安装好stress(性能测试) 和sysstat(系统状态包含mpstat和pidstat两个工具) 工具  
 #所有cpu运行状态（占用率）
 mpstat -P -ALL 5
 #进程占用cpu状态（占用率）
 pidstat -u 5
 #观测系统整体切换次数
 vmstat 1 1
 #观测进程切换次数
 pidstat -w 5
 #观测线程切换次数
 pidstat -wt 5  
 #查看进程io
 pidstat -d -p 1234 1 3
 #观测中断
  watch -d cat /proc/interrupts
 #cpu性能监测（调用函数取样）
 perf top
 #cpu 记录监测指标 ctrl+c中断
 perf record
 perf record -g #采集时加入调用链
 #cpu 性能指标展示
 perf report
 #pstree 查看进程调用关系
 pstree -aps 12345
 #dstat 查看所有指标（cpu，io，网络）
 dstat 1 10
 ~~~  
 

  
