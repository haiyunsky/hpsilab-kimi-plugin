# HPSILab Kimi Code CLI 插件

这是 HPSILab 为 Kimi Code CLI 提供的量化与期权分析插件。插件把 [HPSILab](https://hpsilab.com) 的 Remote MCP server 接入 Kimi Code，提供隐含波动率、期权压力与 Max Pain、蒙特卡洛模拟、AI 方向信号、交易前风险扫描、研究报告和研究配图等能力。

本仓库只负责 Kimi Code CLI 的插件清单、Skill 与斜杠命令分发；实际分析能力由 HPSILab 主站托管的 Remote MCP 服务 `https://hpsilab.com/mcp` 提供。产品、账户、API Token 与服务说明以 [hpsilab.com](https://hpsilab.com) 为准。

## 安装

先启动 Kimi Code CLI，然后在 TUI 中执行 `/plugins install`。

从本地路径安装：

```text
/plugins install /absolute/path/to/hpsilab-kimi-plugin
```

在 Windows 上也可以传入仓库的绝对路径，例如：

```text
/plugins install E:\apps\hpsilab-kimi-plugin
```

从 GitHub URL 安装：

```text
/plugins install https://github.com/haiyunsky/hpsilab-kimi-plugin
```

安装后新建会话，或执行 `/plugins reload` 使插件配置生效。

## 配置 HPSILAB_API_TOKEN

插件通过 `HPSILAB_API_TOKEN` 环境变量向 HPSILab Remote MCP server 发送 Bearer Token。该 Token 用于识别和授权 HPSILab API 请求，不是 Kimi Code 的登录凭据。

macOS / Linux：

```bash
export HPSILAB_API_TOKEN="your-token"
kimi
```

Windows PowerShell：

```powershell
$env:HPSILAB_API_TOKEN = "your-token"
kimi
```

请勿把真实 Token 提交到 Git 仓库或写入插件文件。部分 Pro 工具可能通过 x402 按次计费，具体以 HPSILab 服务返回的信息为准。

## 内置命令

- `/analyze <股票代码>`：调用 HPSILab 做一次全景量化分析。
- `/report <股票代码>`：生成完整股票研究报告；此 Pro 工具可能触发 x402 计费。

所有分析、预测与报告仅供研究参考，不构成任何投资建议。
