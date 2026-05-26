### vscode无法安装gopls等插件，响应超时
1. 输入以下命令，设置 Go 模块代理和代理地址：
```
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.cn,direct
```
> 说明：

GO111MODULE=on：启用 Go 模块功能。

GOPROXY=https://goproxy.cn,direct：设置代理地址为 https://goproxy.cn，并使用 direct 作为备用代理。

2. 安装 gopls 等插件
```   
go install -v golang.org/x/tools/gopls@latest
```

> 说明：

go install -v：使用 go install 命令安装 gopls，并使用 -v 参数显示安装过程。

> 注意事项

- 如果以上方法无法解决问题，可以尝试使用其他国内 Go 代理，例如：
https://goproxy.io
- 建议将代理设置添加到系统环境变量中，以便在所有 Go 项目中生效。



