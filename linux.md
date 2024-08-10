## Ping IPV6地址：
```sh
ping6 ::1
```

## Ping IPV6地址，指定网口：
```sh
ping6 -Ieth1 fe80::20c:29ff:fec4:ae9c
```


## tcpdump指定IP
```sh
tcpdump -i bond0 -s 0 ip host 100.200.170.69 -c 10000 -w 170_69"$(date +%Y%m%d_%H%M%S)".pcap
```

- -i：指定网络接口
- -s len 设置要抓取数据包长度为 len，默认只会截取前 96bytes 的内容，-s 0 的话，会截取全部内容。
- ip: 抓IP协议的包，常用的协议有：icmp、ip、tcp、udp、arp
- -c 指定要抓取包的数量
- -w 选项表示把数据报文输出到文件

tcpdump 的输出格式总体上为：
```
系统时间 源主机.端口 > 目标主机.端口 数据包参数
```

## tcpdump指定端口
```sh
tcpdump -i eth0 tcp port 8089 -w /data/tompcap/2024-08-09_8089.pcap
```

## curl
```sh
curl -X PUT "localhost:9200/customer/_doc/1?pretty" -H 'Content-Type: application/json' -d '{  "name": "John Doe"}'
```

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

## lsof
列出一个进程打开的所有文件:
```sh
lsof -p 18987 
```


## 压缩result目录及其子目录
```sh
zip -r result.zip result
```
## for语法
```sh
for i in {1..3} ; do   for F in *KPI.xml ; do cp $F ${F%.xml}_$i.xml;done done
```

```sh
for((i=1;i<348;i++))  do   for F in *KPI.json ; do cp $F ${F%.json}_$i.json;done done
```

# chown
```sh
chown -R root /usr/local/share/data
```

# du（disk usage）
du命令用于显示目录或文件的大小。

du 会显示指定的目录或文件所占用的磁盘空间。

```sh
du -hs data
```

- -h：以K，M，G为单位，提高信息的可读性。

- -s：仅显示指定目录或文件的总大小，而不显示其子目录的大小。

## 10进制转16进制:
```sh
printf %x 172
```

## 生成文件MD5
```
md5sum testfile
```
## ssh
```sh
ssh -p 22356 root@72.175.58.9
```

## scp 
传送app-0.0.1-SNAPSHOT.jar文件到61.174.79.3服务器的/usr/local/share/data目录
```sh
scp -P22356 app-0.0.1-SNAPSHOT.jar root@61.174.79.3:/usr/local/share/data
```

## rpm（redhat package manager）
```sh
rpm -ql |grep mysql

rpm -qa 
```

- -a 　查询所有软件包。
- -q 　使用询问模式
- -l 　显示软件包的文件列表。

## vi
vi设置文件格式为unix格式

```sh
:set ff=unix
```

## 开启ipv6

```sh
vi /etc/sysctl.conf

net.ipv6.conf.all.disable_ipv6 = 0
net.ipv6.conf.default.disable_ipv6 = 0
net.ipv6.conf.lo.disable_ipv6 = 0
```

 需确保/etc/sysconfig/network-scripts/ifcfg-eno1有如下配置： 
```
IPV6INIT=yes
```

## 设置MTU：
```sh
ifconfig eno1 mtu 1500
```

## 关闭网口：
```sh
ifdown eno1
```

如果以ifconfig eth0来设置或者是修改了网络接口后要使用：
```sh
ifconfig virbr0 down
```

## 查看so函数列表:

nm命令是linux下自带的特定文件分析工具，一般用来检查分析二进制文件、库文件、可执行文件中的符号表，返回二进制文件中各段的信息。

```sh
nm -D libjvm.so  > func-list.txt
```

- -D 或--dynamic：显示动态符号而不显示普通符号，一般用于动态库

## zdump

Zdump 对命令行中的每一个 zonename 输出其当前时间。

```sh
zdump -v /etc/localtime
```

- -v：对于命令行的每一个 zonename , 输出可能的最早时间值，可能的最早时间一天以后的时间值，它们均是能被检测到的精确时刻的1秒前的时间值，可能的最晚时间一天以前的时间值, 可能的最晚时间值，如果给出的时间是夏令时，每行以 isdst=1 结束，否则以 isdst=0 结束。

/etc/timezone 的内容是时区，比如 Asia/ShangHai


## more
```sh
more /etc/sysconfig/clock
```

## hwclock访问硬件时钟
```sh
hwclock 
```

带时区：
```
hwclock --localtime
```

## 7z
7z压缩
```sh
7za a -r x.zip D:\tools\ant\apache-ant-1.9.10\*
```

7z解压
```sh
7za x D:\tools\7z\7z1900-extra\x.zip -y -oD:\tools\ant\testst\
```

## nginx
nginx检查配置
```sh
nginx -t
```

nginx重新加载配置 
```sh
nginx -s reload
```

## vi
vi定位

```
H - 移至屏幕上端
M - 移至屏幕中央
L - 移至屏幕下端
```

设置行号
```
:set nu    
```

取消行号
```
:set nonu
```

到第一行
```
gg
```

到最后一行
```
G
```

到第n行
```
nG
```

到第n行
```
:n
```

删除光标所在行
```
dd
```

删除光标所在行到文件最后一行的内容
```
dG
```

删除光标所在处到行尾内容
```
D
```

删除n1行到n2行
```
:n1,n2d
```



