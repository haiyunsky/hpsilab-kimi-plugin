---
description: 生成指定股票的完整 HPSILab 研究报告
---

调用 HPSILab MCP 的 `generate_stock_research_report`，为 `$ARGUMENTS` 生成完整研究报告。

这是一个可能通过 x402 按次计费的 Pro 工具。调用前提示用户可能产生费用。若调用成功，用中文概括报告的核心结论，并提供服务返回的完整报告或下载信息。

不能只因 HTTP 200 或 MCP 调用完成就判定成功。仅当结果没有 `isError: true`、`status: error`、payment required、认证错误或所有数据源 unavailable，并且确实包含可用研究数据时，才视为成功。

如果调用因 x402 支付失败、余额不足、付款未完成或缺少支付能力而失败：

1. 明确说明报告尚未生成，不要编造或补写报告内容；
2. 原样保留有助于排查的支付错误摘要；
3. 提示用户完成 x402 支付、补充余额或配置所需支付能力后重试；
4. 不要把支付错误误报为股票代码无效或 HPSILab 分析失败。

如果返回 429、`RATE_LIMITED`、`QUOTA_EXCEEDED` 或 `PLAN_LIMIT_EXCEEDED`，停止连续重试并说明实际状态。Kimi Code CLI 不一定支持自动创建并提交 x402 payment payload，不要假设支付已自动完成。

若报告内容显示所有来源 unavailable、方向分数为默认值或图片全部不可用，应明确报告生成失败，不要把降级模板当作完整研究报告。

若股票代码不明确，尤其是非美股代码，不要凭记忆猜测格式，应先确认。不要输出或要求用户粘贴完整 `HPSILAB_API_TOKEN`。
