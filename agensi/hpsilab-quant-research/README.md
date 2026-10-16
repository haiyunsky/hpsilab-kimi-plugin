# HPSILab Quant Research skill

An agent skill that teaches your AI agent to run quantitative research on US stocks and options with the [HPSILab](https://hpsilab.com) MCP server, and to report the results honestly, including partial results and errors.

## What it covers

- Stock overview combining IV, options positioning, Monte Carlo range and an AI direction signal (`analyze_stock`)
- Implied volatility level, term structure and skew (`get_iv_radar`)
- Max Pain, dealer Gamma Wall, likely weekly high and squeeze target (`get_option_pressure`)
- ~10-trading-day Monte Carlo price range (`get_monte_carlo`)
- Next-day probability of closing up (`get_ai_prediction`)
- Strategy backtest metrics: Sharpe, max drawdown, win rate (`get_equity_curve`)
- Research chart images (`generate_stock_images`)
- Pro tools: pre-trade risk scan and a full markdown research report

The skill is research only. The tools cannot place trades, and the skill tells the agent not to give personalized investment advice.

## Requirements

This skill contains instructions only. It needs the HPSILab MCP server connected in your agent:

- Remote MCP URL: `https://hpsilab.com/mcp`
- Authentication: an HPSILab API key sent as a Bearer token, configured in your agent's MCP connector settings (never in the chat)

HPSILab is a separate service with its own account, plans and Credits. Buying this skill does not include HPSILab API usage, Credits or a subscription. Some tools are limited by plan or consume Credits; the agent reports those limits when the server returns them.

## Network access

The skill itself makes no network calls and contains no scripts. All data comes from the HPSILab MCP tools your agent calls, using the connection you configure.

## Author and license

Author: Haiyun Hu (HPSILab). Released under the MIT License, see [LICENSE](LICENSE).
