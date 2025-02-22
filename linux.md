## sed

### 将yyyymmdd替换为YYYYMMDD：
```sh
sed -i "s/yyyymmdd/YYYYMMDD/g" `grep -rl yyyymmdd  /home/albert/*.ddl`
```

- -i 原地替换
- -l 输出包含yyyymmdd的文件名
- -r 遍历子目录


### 统计文件行数

```sh
sed -n '$=' /usr/aaa.DAT
```


## grep

### 统计字符串出现次数

```sh
grep -o '字符串' file |wc -l
```

## Ping IPV6地址：
```sh
ping6 ::1
```

## Ping IPV6地址，指定网口：
```sh
ping6 -Ieth1 fe80::20c:29ff:fec4:ae9c
```
## MTR

MTR的名称来源是My TraceRoute。mtr是一个网络诊断工具，将ping和traceroute命令的功能合二为一。

对于数据包从源到目的地所经过的每个节点，mtr都会记住。而后向这些节点发送ICMP数据包，通过ICMP响应数据包来获取这些节点的网络信息（如延迟、丢包率等）。大多数情况下mtr检测网络信息时使用的数据包是ICMP数据包，不过也可以是TCP或者UDP数据包。

### MTR报告
```sh
mtr www.google.com --report
```
#### 报告解析
- 第一列：所经过的路由节点的IP地址，如果支持反向解析的则反解为主机名。
- Loss%：丢包率。
- Snt：已发送的数据包。
- Last：最近一次数据包的延时，mtr所显示的延时相关的时间单位均是毫秒。
- Avg：平均延时。
- Best：最小延时。
- Wrst：最大延时。
- Stdev：标准差（Standard Deviation），延时的标准差，这个值越小则延时越稳定。

有时候 mtr 的报告中会出现 ??? 的标志，这是因为 traceroute 拿不到中间节点的信息，一般是因为这个节点被设置成不回应 ICMP 包，但是能够正常转发包。所以这种情况下即不能拿到 IP，也无法测试丢包率，延迟等。

不要去试图明白每一次丢包背后的原因。网络协议本身就是设计成有很多容错和降级的。任何时候都有可能发生路有错误，网络拥塞，设备维护等问题。如果 mtr 显示丢包率有 10%，一般不会有什么大问题，因为一般的上层应用协议都会处理好这部分丢包。如果去排查每一次网络丢包问题，只会徒增人力成本。

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



