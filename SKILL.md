---
name: alphaear
description: >-
  A-Share/HK/US financial market intelligence system. Use when the user needs
  real-time signals, news aggregation, sentiment analysis, stock data, market
  search, signal tracking, forecasting, reporting, or investment analysis for Chinese
  and global equity markets. Covers signal generation, tracking, and reporting
  across the full investment research workflow.
---

# AlphaEar — Financial Market Intelligence

A comprehensive financial market intelligence system covering stock data, news,
sentiment analysis, signal tracking, market search, forecasting, and reporting.

## Skill Map

| Subskill | What It Does |
|---|---|
| `alphaear-stock` | Search A-Share/HK/US tickers; OHLCV price history; fundamentals |
| `alphaear-news` | Real-time hot news from Weibo/Zhihu/WSJCN; Polymarket prediction markets |
| `alphaear-sentiment` | FinBERT or LLM sentiment scoring on financial text |
| `alphaear-search` | Web search (Jina/DDG/Baidu) + local RAG search |
| `alphaear-signal-tracker` | Track investment signal evolution; assess strengthened/weakened/falsified |
| `alphaear-deepear-lite` | Fetch latest signals from DeepEar Lite dashboard |
| `alphaear-logic-visualizer` | Draw.io XML logic/flow diagrams for signal transmission chains |
| `alphaear-predictor` | Kronos time-series market forecasting + news-aware adjustments |
| `alphaear-reporter` | Generate structured financial reports with chart configs |

## Data Sources

- **A-Share/HK**: `akshare` (EastMoney), Tushare Pro
- **US**: `yfinance`, Twelve Data API
- **News**: Weibo, Zhihu, WallstreetCN, Polymarket
- **Signals**: DeepEar Lite (`https://deepear.vercel.app/latest.json`)

## Shared Dependencies

```
requests, loguru, sqlite3, pandas, numpy
torch, transformers    # FinBERT sentiment
duckduckgo-search      # Web search
akshare, yfinance      # Market data
```

## Quick Reference

```bash
# Stock data
python scripts/stock_tools.py --ticker 600519 --start 20240101

# News aggregation
python scripts/news_tools.py --source weibo --count 10

# Sentiment analysis
python scripts/sentiment_tools.py --text "茅台Q3净利润增长15%"

# Signal tracking
python scripts/fin_agent.py --signal-id S001 --new-info "政策利好"

# Fetch DeepEar signals
python scripts/deepear_lite.py

# Generate report
python scripts/report_agent.py --signals signals.json
```

## Common Patterns

### Full Research Workflow
1. `alphaear-stock` → find ticker, get price/history
2. `alphaear-news` → fetch relevant hot news
3. `alphaear-sentiment` → score news sentiment
4. `alphaear-search` → web + RAG search for context
5. `alphaear-signal-tracker` → generate or update signal
6. `alphaear-predictor` → generate forecast
7. `alphaear-reporter` → assemble into structured report

### Signal → Report Pipeline
1. Use `alphaear-signal-tracker` prompts to build `InvestmentSignal` objects
2. Use `alphaear-deepear-lite` to cross-reference external signals
3. Use `alphaear-logic-visualizer` to diagram transmission chains
4. Use `alphaear-reporter` prompts to write final report

## Tushare Pro Token

Set `TUSHARE_TOKEN` in environment. Token is stored in memory.

---

## Subskill Details

### alphaear-stock
Stock search (A-Share/HK/US tickers) and OHLCV price history. Also supports
fundamentals (sector, market cap, PE ratio). Uses `akshare` for A/H shares and
`yfinance` for US stocks.
- **Scripts**: `scripts/stock_tools.py`, `scripts/database_manager.py`
- **Key methods**: `search_ticker(query)`, `get_stock_price(ticker, start, end)`,
  `get_stock_fundamentals(ticker)`

### alphaear-news
Real-time hot news from Weibo, Zhihu, WallstreetCN + Polymarket prediction market data.
- **Scripts**: `scripts/news_tools.py` (`NewsNowTools`, `PolymarketTools`)
- **References**: `references/sources.md` (valid `source_id` list)
- **Key methods**: `fetch_hot_news(source_id, count)`,
  `get_unified_trends(sources)`, `get_market_summary(limit)`

### alphaear-sentiment
FinBERT or LLM-based sentiment analysis on financial text. Score range: -1.0 (negative)
to +1.0 (positive). Supports batch processing.
- **Scripts**: `scripts/sentiment_tools.py` (`sentiment_tools.py`)
- **Key methods**: `analyze_sentiment(text)`, `batch_update_news_sentiment(source, limit)`
- **Note**: For reasoning-heavy analysis, use the LLM prompt in the original skill
  instead of the local FinBERT tool

### alphaear-search
Web search (Jina/DDG/Baidu) + local RAG search against the `daily_news` database.
- **Scripts**: `scripts/search_tools.py`, `scripts/hybrid_search.py`
- **References**: `references/PROMPTS.md` (Search Cache Relevance Prompt)
- **Key methods**: `search(query, engine, max_results)`,
  `aggregate_search(query)`, `SearchTools` with `engine='local'`

### alphaear-signal-tracker
Agentic workflow to track investment signal evolution (Strengthened/Weakened/Falsified).
Uses FinResearcher + FinAnalyst + Signal Tracking prompts.
- **Scripts**: `scripts/fin_agent.py` (`_sanitize_signal_output`)
- **References**: `references/PROMPTS.md`
- **Dependencies**: `agno`, `sqlite3`
- **Note**: Input: existing Signal State + New Information → Output: Updated Signal

### alphaear-deepear-lite
Fetch latest signals from the DeepEar Lite real-time dashboard.
- **Scripts**: `scripts/deepear_lite.py` (`DeepEarLiteTools`)
- **Endpoint**: `https://deepear.vercel.app/latest.json`
- **Key methods**: `fetch_latest_signals()` → titles, summaries, confidence scores

### alphaear-logic-visualizer
Generate Draw.io XML diagrams for signal transmission chains and investment logic flows.
- **Scripts**: `scripts/visualizer.py`, `scripts/visualizer_prompt.py`
- **References**: `references/PROMPTS.md` (Draw.io XML Generation Prompt)
- **Key method**: `visualizer.render_drawio_to_html(xml_content, filename)`
- **Workflow**: Generate XML from prompts → render to HTML

### alphaear-predictor
Kronos time-series market forecasting with news-aware prediction adjustments.
- **Scripts**: `scripts/kronos_predictor.py`, `scripts/forecast_agent.py`
- **References**: `references/PROMPTS.md` (Forecast Adjustment Prompt)
- **Key class**: `KronosPredictorUtility`
- **Requirements**: Kronos model weights in `exports/models/kronos_news_v1.pt`,
  `EMBEDDING_MODEL` env var
- **⚠️ Model Security**: Only load `kronos_news_*.pt` patterns from `exports/models`

### alphaear-reporter
Structured financial report generation: signal clustering → section writing →
final assembly. Also generates chart configurations.
- **Scripts**: `scripts/report_agent.py`, `scripts/visualizer.py`
- **References**: `references/PROMPTS.md` (Cluster Signals, Write Section, Final Assembly prompts)
- **Workflow**: Cluster Signals → Write Section per cluster → Final Assembly

---

## Notes

- For A-Share data, direct connection works best in China; proxy may be needed for US access
- US stock data via `yfinance` may need `HTTP_PROXY`/`HTTPS_PROXY` set
- `alphaear-predictor` requires Kronos model weights in `exports/models/`
