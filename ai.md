### wsl2重置密码：

关闭Ubuntu窗口

打开Powershell 或 cmd， 以root默认登陆 wsl -u root。

别关，在这个cmd窗口内（重点）输入 wsl 进入，
输入 passwd your_username ，确认密码。
关闭 WSL。 exit

### githubusercontent域名污染解决方法
199.232.28.133 raw.githubusercontent.com

### 下载并安装 nvm
```sh
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.4/install.sh | bash
```

```sh
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
 ```

代替重启 shell：
```sh
\. "$HOME/.nvm/nvm.sh"
```

下载并安装 Node.js：
```sh
nvm install 24
```

验证 Node.js 版本：
```sh
node -v 
# Should print "v24.13.1".
```

验证 npm 版本：
```sh
npm -v
Should print "11.8.0".
``` 

### claude安装
```sh
npm install -g @anthropic-ai/claude-code
```

### claude使用GLM模型
编辑或新增 `settings.json` 文件

Windows 为`用户目录/.claude/settings.json`

新增或修改里面的 env 字段
```json
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "your_zhipu_api_key",
    "ANTHROPIC_BASE_URL": "https://open.bigmodel.cn/api/anthropic",
    "API_TIMEOUT_MS": "3000000",
    "CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC": 1
  }
}
```
再编辑或新增 `.claude.json` 文件

Windows 为`用户目录/.claude.json`

新增 `hasCompletedOnboarding` 参数
```json
{
  "hasCompletedOnboarding": true
}
```

配置模型：

手动修改配置文件 ~/.claude/settings.json，添加或替换如下环境变量参数：
```json
{
  "env": {
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "glm-4.5-air",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "glm-4.7",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "glm-4.7"
  }
}
```
> 注：使用 GLM-5，需要在将上方的环境变量参数值手动修改模型为 “glm-5”。

启动一个新的命令行窗口，运行claude启动 Claude Code，在 Claude Code 中输入/status确认模型状态

如果不适用自定义模型，想要稳定使用Claude，一个纯净的IP必不可少。这里推荐使用静态住宅代理，相关服务商有proxycheap、oxylabs、iproyal等


