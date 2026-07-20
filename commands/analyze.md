---
description: 对指定股票代码做一次 HPSILab 全景量化分析
---

调用 HPSILab MCP 的 `analyze_stock` 分析 `$ARGUMENTS`。

请用中文总结分析结果，至少覆盖：

- 隐含波动率（IV）及其关键信号
- Max Pain 与主要期权压力位
- 蒙特卡洛模拟的核心概率区间
- AI 方向信号、置信度及主要风险

不能只因 MCP 调用完成或 HTTP 200 就判定成功。检查 `isError`、`status`、`complete`、`warnings`、`error` 和各项 `tool_responses`。

如果结果为 partial 或有 unavailable 模块：

1. 明确标注“部分成功”并列出缺失模块；
2. 只补调失败或缺失的专项工具，不要重复调用整个 `analyze_stock`；
3. 遇到 429、额度不足或不可重试错误时停止重试；
4. 不得根据缺失数据自行补造结论。

清楚区分观测数据、模型结果与推断；若股票代码不明确，尤其是非美股代码，不要凭记忆猜测格式，应先确认。不要输出或要求用户粘贴完整 `HPSILAB_API_TOKEN`。

末尾注明：以上内容仅供研究参考，不构成任何投资建议。
