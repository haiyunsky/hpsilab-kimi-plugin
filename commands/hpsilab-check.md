---
description: 检查 HPSILab MCP 连接、工具发现、认证和 Pro 工具可用性
---

对当前 HPSILab Kimi 插件执行一次只读诊断，不进行 x402 支付，也不要输出任何 Token、支付签名或敏感凭据。

请完成并汇报：

1. 判断 `hpsilab` MCP server 是否已连接；如果无法从当前会话确认，提示用户执行 `/mcp` 查看是否为 `connected`。
2. 检查当前可用工具中是否包含以下 9 个名称，并逐一列出“已发现/缺失”：
   - `analyze_stock`
   - `get_iv_radar`
   - `get_option_pressure`
   - `get_monte_carlo`
   - `get_ai_prediction`
   - `get_equity_curves`
   - `generate_stock_images`
   - `get_pretrade_risk_scan`
   - `generate_stock_research_report`
3. 如果 9 个工具均已发现，使用 `get_iv_radar` 对 `AAPL` 做一次轻量探测。不要自动调用其余工具。
4. 根据实际返回区分：成功、部分成功、认证失败、Token 无效、限流/额度不足、上游故障或超时。
5. 对两个 Pro 工具只检查是否已发现，不要实际调用或支付。说明 Kimi 不一定支持自动处理 x402 request `_meta`。
6. 提示用户执行 `/plugins info hpsilab-kimi-plugin`，检查 diagnostics 是否有 manifest、路径、认证或 MCP 错误。

最后输出简短诊断结论和建议的下一步，不要把 HTTP 200 但 `status: error`、`isError: true` 或 unavailable 的结果判为成功。
