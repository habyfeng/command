##  windows Ping IPV6
```bat
ping -S fe80::156c:1b0f:61ee:414 fe80::156c:1b0f:61ee:414
```


## 生成文件MD5
```bat
certutil -hashfile yourfilename.ext MD5
```

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

