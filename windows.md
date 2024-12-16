##  windows Ping IPV6
```bat
ping -S fe80::156c:1b0f:61ee:414 fe80::156c:1b0f:61ee:414
```


## 生成文件MD5
```bat
certutil -hashfile yourfilename.ext MD5
```

## 生成文件SHA1
```bat
certutil -hashfile C:\Users\Downloads\kotlin-compiler-embeddable-2.0.0.jar SHA1
```
> 备注: gradle jar包目录名




## 删除以lastUpdated结尾的文件
```bat
for /r %i in (*.lastUpdated) do del %i
```

## wlan

生成wlan报告，管理员身份执行：
```bat
netsh wlan show wlanreport
```

报告位置：
```
C:\ProgramData\Microsoft\Windows\WlanReport\
```

查看wlan网卡当前的模式：

```bat
cd C:\Windows\System32\Npcap\

WlanHelper.exe wlan mode

```

查看wlan网卡支持的工作模式：

```bat
WlanHelper.exe wlan mode
```

获取wlan网卡具体参数：
```bat
netsh wlan show interfaces
```

displays information about the supported hardware：
```bat
netsh wlan show wirelesscapabilities
```

## 查看系统启动的算法套：

PowerShell下执行：
```bat
Get-TlsCipherSuite
```

## 使能算法套：

PowerShell下执行：
```bat
Enable-TlsCipherSuite -Name TLS_DHE_DSS_WITH_AES_256_CBC_SHA
```

## 新电脑开机不激活

开机执行到【让我们为你连接到网络】时，按住
快捷键：
```
【Shift+F10】或【Fn+Shift+F10】
```
在跳出的CMD命令窗口输入以下命令：
```cmd
oobe\bypassnro
```
输入代码后回车，系统安装会自动重启，重新回到【让我们为你连接到网络】界面。在这个界面会发现比之前多了一个【我没有Internet连接】，点击这个选项。

如果没有出现【我没有Internet连接】，则继续按快捷键【Shift+F10】或【Fn+Shift+F10】，在跳出的CMD命令窗口输入以下代码：
```
oobe\bypassnro.cmd
```

在跳出的界面一次点击【我没有Internet连接】、【继续执行受限设置】，后面的安装就按部就班进行安装即可。
