---
name: using-hpsilab
description: 使用 HPSILab Remote MCP 进行股票、期权与量化分析，并正确处理部分成功、认证、限流及支付错误。
---

# 使用 HPSILab

HPSILab 提供面向股票和期权研究的 Remote MCP 工具。用户提出个股分析、隐含波动率、期权压力、蒙特卡洛预测、AI 方向信号、资金曲线、交易前风险检查或研究报告需求时，使用下列工具。

## 调用顺序

1. 个股全景判断优先调用 `analyze_stock`。
2. 如果结果完整，直接总结；只有用户需要更多细节时，才调用相应专项工具。
3. 如果 `analyze_stock` 返回 `partial`、`complete: false`、`warnings`、失败的 `tool_responses` 或 unavailable 模块，只补调失败或缺失的专项工具，不要立即重复整次全景分析。
4. 如果用户只要求单项数据，直接调用对应专项工具，避免不必要的聚合调用和额度消耗。
5. `generate_stock_images` 优先使用缓存；除非用户明确要求刷新图片，否则不要设置 `force: true`。

## 成功与失败判断

不能只根据 HTTP 200 或 MCP 调用完成来判断成功。必须同时检查：

- MCP 结果的 `isError`；
- `structuredContent.status`、`error`、`complete` 和 `warnings`；
- 返回文本中是否出现 unavailable、rate limit、authentication、quota、payment 或 timeout；
- 聚合结果里每个 `tool_responses` 的状态。

如果数据不完整，应明确说明“部分成功”并列出缺失模块。不要把降级结果描述成完整分析，也不要根据缺失数据自行补造结果。

## 错误处理

- `RATE_LIMITED`、`QUOTA_EXCEEDED` 或 HTTP 429：停止连续重试，向用户说明限流或额度状态；如果服务返回 retry-after 信息，应告知可重试时间。同一轮最多重试一次，并且只有服务明确表示可重试时才重试。
- `AUTH_REQUIRED`、`INVALID_TOKEN` 或 HTTP 401/403：提示检查 `HPSILAB_API_TOKEN`。不要读取、显示、记录或要求用户在对话中粘贴完整 Token。
- `PLAN_LIMIT_EXCEEDED`：说明当前套餐不包含该工具或额度已耗尽，不要误报为股票代码错误。
- `PAYMENT_REQUIRED`：说明调用尚未成功，并展示服务端提供的价格、网络和支付要求。不要假设 Kimi 已自动完成 x402 支付。
- `UPSTREAM_TIMEOUT` 或明确可重试的 5xx：可以重试一次；再次失败后停止并报告。
- `INVALID_SYMBOL`：请用户确认代码。对于非美股，不要凭记忆猜测交易所后缀或代码格式。

## Pro 工具与 x402

`get_pretrade_risk_scan` 和 `generate_stock_research_report` 是 Pro 工具。

- 有效的 HPSILab Bearer Token 应优先走账户套餐和额度。
- 无 Token 时，服务端可能返回 x402 payment-required challenge；Kimi Code CLI 不一定具备自动创建 payment payload 并通过 MCP request `_meta` 提交的能力。
- 只有服务明确返回支付成功并给出实际工具结果，才可称为调用成功。
- 如果只返回 payment required、认证失败、429、空报告或所有模块 unavailable，应视为失败。
- 调用前提示用户该工具可能产生费用。

## 工具

### `analyze_stock`

综合价格、IV、期权压力、Max Pain、蒙特卡洛区间和 AI 信号。作为个股全景分析的首选入口。返回 partial 时必须披露缺失模块。

### `get_iv_radar`

查看隐含波动率水平、期限结构、偏斜和曲面特征，用于判断期权定价及波动率风险。

### `get_option_pressure`

分析 Call/Put 分布、关键执行价、Gamma 或对冲压力、Max Pain 和潜在价格压力区。

### `get_monte_carlo`

给出未来价格分布、概率区间和情景结果。它不是确定目标价，解读时要说明期限、概率和假设。

### `get_ai_prediction`

获取 AI 方向预测、置信度和相关信号。必须与市场数据交叉验证，不能作为确定性交易指令。

### `get_equity_curves`

获取策略资金曲线、累计收益、回撤、Sharpe、胜率等表现指标。比较时同时关注收益与风险。

### `generate_stock_images`

生成研究图表或图片资产。优先复用缓存；图片状态为 unavailable 或 error 时，不得声称图片已生成。

### `get_pretrade_risk_scan`

执行交易前风险扫描，识别波动率、流动性、事件和仓位等潜在风险。这是 Pro 工具，适用认证和支付规则。

### `generate_stock_research_report`

生成结构化完整研究报告。这是 Pro 工具；如果数据源全部 unavailable、状态为 error 或支付失败，不得自行补写成完整报告。

## 输出要求

- 清楚区分事实数据、模型预测和推断。
- 披露失败、降级和数据缺口。
- 不暴露 Token、支付签名或其他敏感信息。
- 末尾提醒：量化分析仅供研究参考，不构成投资建议。
