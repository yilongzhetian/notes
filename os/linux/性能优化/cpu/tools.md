### 工具篇  
1. 安装pstree （进程调用关系）
~~~
yum install psmisc
~~~
2. 安装execsnoop(输出短时进程的基本信息，包括进程 PID、父进程 PID、命令行参数以及执行的结果。)  
~~~
cd /usr/bin
wget https://raw.githubusercontent.com/brendangregg/perf-tools/master/execsnoop
chmod 755 execsnoop
~~~
