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