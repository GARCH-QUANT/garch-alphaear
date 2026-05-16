# AlphaEar — 金融市场的智能耳朵

A-Share / HK / US 全市场情报系统 — 股票数据、实时新闻、情绪分析、信号追踪、预测与报告生成。

## 核心功能

| 模块 | 功能 |
|------|------|
| `alphaear-stock` | A/H股 ticker 搜索 + OHLCV 价格历史 + 基本面 |
| `alphaear-news` | 微博/知乎/华尔街见闻实时热榜 + Polymarket 预测市场 |
| `alphaear-sentiment` | FinBERT / LLM 金融文本情绪打分（-1.0 ~ +1.0）|
| `alphaear-search` | Web 搜索（Jina/DDG/Baidu）+ 本地 RAG 检索 |
| `alphaear-signal-tracker` | 投资信号全生命周期追踪（强化/弱化/证伪）|
| `alphaear-deepear-lite` | DeepEar Lite 实时信号面板接入 |
| `alphaear-logic-visualizer` | Draw.io 信号传导链逻辑图生成 |
| `alphaear-predictor` | Kronos 时序市场预测 + 新闻增强调整 |
| `alphaear-reporter` | 结构化投研报告生成（信号聚类 → 章节撰写 → 汇编）|

## 数据源

- **A/H股**：akshare（东方财富）、Tushare Pro
- **美股**：yfinance、Twelve Data API
- **新闻**：微博、知乎、华尔街见闻、Polymarket
- **信号**：DeepEar Lite (`https://deepear.vercel.app/latest.json`)

## 快速开始

```bash
# 股票数据
python scripts/stock_tools.py --ticker 600519 --start 20240101

# 新闻聚合
python scripts/news_tools.py --source weibo --count 10

# 情绪分析
python scripts/sentiment_tools.py --text "茅台Q3净利润增长15%"

# 生成报告
python scripts/report_agent.py --signals signals.json
```

## 完整投研流程

```
alphaear-stock → alphaear-news → alphaear-sentiment
    → alphaear-search → alphaear-signal-tracker
    → alphaear-predictor → alphaear-reporter
```

## 环境配置

```bash
export TUSHARE_TOKEN="your_tushare_token"    # A/H股数据
export TWELVEDATA_KEY="your_twelve_data_key"  # 美股行情
export TELEGRAM_BOT_TOKEN="***"              # 可选：Telegram推送
```