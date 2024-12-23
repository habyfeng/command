## iostat
iostat 命令是 I/O statistics(输入/输出统计)的缩写，用来报告系统的 CPU 统计信息和块设备及其分区的 IO 统计信息。iostat 是 sysstat 工具集的一个工具

```sh
iostat -x -k -t 5 > /var/opt/iostat.log
```

- -x 显示更多的设备列
- -k  以 k 为单位显示
- -t 显示每次输出的时间


await：每一个IO请求的处理的平均时间（单位是微秒毫秒）。这里可以理解为IO的响应时间，一般地系统IO响应时间应该低于5ms，如果大于10ms就比较大了。
这个时间包括了队列时间和服务时间，也就是说，一般情况下，await大于svctm，它们的差值越小，则说明队列时间越短，反之差值越大，队列时间越长，说明系统出了问题。
			   
svctm:  表示平均每次设备I/O操作的服务时间（以毫秒为单位）。如果svctm的值与await很接近，表示几乎没有I/O等待，磁盘性能很好，如果await的值远高于svctm的值，则表示I/O队列等待太长，系统上运行的应用程序将变慢。
		avgrq-sz:平均每次IO操作的数据量(扇区数为单位)



## mpstat(Multiprocessor Statistics)
mpstat是是实时系统监控工具。其报告与CPU的一些统计信息，这些信息存放在/proc/stat文件中。在多CPUs系统里，其不但能查看所有CPU的平均状况信息，而且能够查看特定CPU的信息。mpstat最大的特点是：可以查看多核心cpu中每个计算核心的统计数据；而类似工具vmstat只能查看系统整体cpu情况。

```sh
mpstat -P ALL 5 > /var/opt/mpstat.log
```

- -P {|ALL} 表示监控哪个CPU， cpu在[0,cpu个数-1]中取值
- 5: internal 相邻的两次采样的间隔时间


## vmstat

vmstat是Virtual Meomory Statistics（虚拟内存统计）的缩写，可实时动态监视操作系统的虚拟内存、进程、CPU活动。

```sh
vmstat -t -S M 3 > /var/opt/vmstat.lo

vmstat 2 5 | awk '{ system("date +%H:%M:%S"); print $0}'

vmstat 2 5 | (while read -r line; do echo "$(date): $line"; done)
```

- -t 显示每次输出的时间
- -S：使用指定单位显示。参数有 k 、K 、m 、M
- 3: 采样间隔

iostat、mpstat、sar、sa安装：  
```sh
yum -y install sysstat
```

iotop 安装：
```sh
yum -y install iotop
```

stat安装 :
```sh
yum install -y dstat
```

## iotop
iotop命令 是一个用来监视磁盘I/O使用状况的top类工具。

iotop使用Python语言编写而成，要求Python2.5（及以上版本）和Linux kernel2.6.20（及以上版本）。

```sh
iotop -o -P -t -d 3  > /var/opt/iotop.log
```

- -o：只显示有io操作的进程
- -P：只显示进程，一般为显示所有的线程
- -t：在每一行前添加一个当前的时间
- -d：SEC：间隔SEC秒显示一次。


## dstat
dstat命令是一个用来替换vmstat、iostat、netstat、nfsstat和ifstat这些命令的工具，是一个全能系统信息统计工具

```sh
dstat -t -a
```
- -a：此为默认选项，等同于-cdngy。
- -t ：将当前时间显示在第一行
- -c：显示CPU系统占用，用户占用，空闲，等待，中断，软件中断等信息。
- -C：当有多个CPU时候，此参数可按需分别显示cpu状态，例：-C 0,1 是显示cpu0和cpu1的信息。
- -d：显示磁盘读写数据大小。
- -n：显示网络状态。
- -N eth1,total：有多块网卡时，指定要显示的网卡。
- -m：显示内存使用情况。
- -g：显示页面使用情况。
- -p：显示进程状态。
- -s：显示交换分区使用情况。
- -r：I/O请求情况。
- -y：系统状态。
  


## pidstat
pidstat是sysstat工具的一个命令，用于监控全部或指定进程的cpu、内存、线程、设备IO等系统资源的占用情况。pidstat首次运行时显示自系统启动开始的各项统计信息，之后运行pidstat将显示自上次运行该命令以后的统计信息。用户可以通过指定统计的次数和时间来获得所需的统计信息。

```sh 
pidstat -u -p ALL
```

- -u：默认的参数，显示各个进程的cpu使用统计
- -r：显示各个进程的内存使用统计
- -d：显示各个进程的IO使用情况
- -p：指定进程号
- -w：显示每个进程的上下文切换情况



# iperf

Iperf是一个网络性能测试工具。Iperf可以测试TCP和UDP带宽质量。Iperf可以测量最大TCP带宽，具有多种参数和UDP特性。 Iperf可以报告带宽，延迟抖动和数据包丢失。利用Iperf这一特性，可以用来测试一些网络设备如路由器，防火墙，交换机等的性能。

```sh
iperf -s

iperf -c 192.168.10.48 -m -t 60 -i 10 -b 1000M
```

- -s：  Iperf服务器模式
- -c：如果Iperf运行在服务器模式，并且用-c参数指定一个主机，那么Iperf将只接受指定主机的连接。此参数不能工作于UDP模式。
- -m：输出TCP MSS值（通过TCP_MAXSEG支持）。MSS值一般比MTU值小40字节。
- -t：设置传输的总时间。Iperf在指定的时间内，重复的发送指定长度的数据包。默认是10秒钟。
- -i：设置每次报告之间的时间间隔，单位为秒。如果设置为非零值，就会按照此时间间隔输出测试报告。默认值为零。
- -b： 、UDP模式使用的带宽，单位bits/sec。此选项与-u选项相关。默认值是1 Mbit/sec。

双向测试：
```sh
iperf -c 192.168.10.48 -m -t 60 -i 10 -b 1000M -d
```
- -d：运行双测试模式。这将使服务器端反向连接到客户端
