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

- `/hpsilab-kimi-plugin:analyze <股票代码>`：调用 HPSILab 做一次全景量化分析。
- `/hpsilab-kimi-plugin:report <股票代码>`：生成完整股票研究报告；此 Pro 工具可能触发 x402 计费。
- `/hpsilab-kimi-plugin:hpsilab-check`：只读检查 MCP 连接、9 个工具的发现状态、轻量调用和 Pro 工具提示，不执行支付。

## 本地验证步骤

下面的步骤需要本机已安装 Kimi Code CLI，并已设置有效的 `HPSILAB_API_TOKEN`：

1. 在 Kimi Code CLI 中安装本地插件：

   ```text
   /plugins install /absolute/path/to/hpsilab-kimi-plugin
   ```

2. 执行 `/plugins reload`，或退出后新开一个 Kimi 会话。
3. 执行 `/mcp`，确认 `hpsilab` 的连接状态为 `connected`。
4. 执行 `/plugins info hpsilab-kimi-plugin`，确认 diagnostics 中没有 manifest、路径、认证或 MCP 连接错误。
5. 先执行 `/hpsilab-kimi-plugin:hpsilab-check`，确认 9 个工具均已发现，并完成一次 `get_iv_radar` 的轻量探测。
6. 使用真实代码 `AAPL`，要求 Kimi 分别调用以下工具，并逐项检查返回值：

   ```text
   analyze_stock
   get_iv_radar
   get_option_pressure
   get_monte_carlo
   get_ai_prediction
   get_equity_curves
   generate_stock_images
   get_pretrade_risk_scan
   generate_stock_research_report
   ```

   建议明确要求 Kimi 每次只调用一个指定工具，并记录成功、失败、HTTP 状态、超时、审批拒绝或支付错误，避免 `analyze_stock` 的聚合结果掩盖某个底层工具失败。

7. 对 `get_pretrade_risk_scan` 和 `generate_stock_research_report` 单独测试：

   - 先使用有效的 `HPSILAB_API_TOKEN`，确认账户套餐内调用是否成功。
   - 再在测试环境中移除 Token，确认服务端是否返回 x402 payment-required challenge。
   - Kimi Code CLI 未必具备自动创建和附加 x402 payment payload 的能力。若只收到支付错误或普通失败结果，应视为客户端不支持自动支付，不要假设 Kimi 已完成付款。

服务端通过 MCP `tools/list` 自动公开工具，插件无需手工维护工具清单。除非需要主动隐藏故障或不希望用户调用的工具，否则不应添加 `enabledTools` 或 `disabledTools`。

诊断时不能只看 HTTP 200。还应检查 MCP `isError`、结构化结果中的 `status`、`error`、`complete`、`warnings`，以及聚合结果中每个底层工具的状态。`status: error`、`isError: true`、所有数据源 unavailable 或只有降级模板都不算成功。

所有分析、预测与报告仅供研究参考，不构成任何投资建议。
