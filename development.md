# TrendBot — development log
Updated 2026-10-20T21:22+00:00.

## Implementation (current)
- Software version 10 (trading rules unchanged since 2026-10-19); execution: signal at close T, fill at open T+1, 5bp slippage, no commission, dividends not credited
- Portfolios: {'A': 'technical rule with reset (10% stop from 2026-10-20)', 'B': "same rule + agent veto + independent review + portfolio-manager decision (10% stop); 'agents and PM' policy while the automated reviewer is unavailable, decisions tagged 'no independent review'"}; C: trend only, slope-ranked, no reset, no stop; approved 2026-10-19 as a hypothesis
- Stop-loss: {'pct': 0.1, 'effective': '2026-10-20', 'applies_to': ['A', 'B'], 'execution': 'checked on the close; sells at the next open with slippage; gaps can exceed 10%'}
- Order integrity: orders carry placed_at; an order written after 9:30 ET cannot fill at that day's open
- Earnings blackout: no Portfolio B entry when earnings fall within the next 5 trading sessions; enforced by a deterministic check in runner.py on the earnings date the agents report, independent of agent or reviewer judgment (added 2026-10-20, tested)
- Review bridge: reviewer.py: OpenAI Responses API, direct from the cloud (api.openai.com allowed; authenticated OK on 2026-10-19). Paid calls refused until the OpenAI account has credits; automated daily reviews and weekly audits switch on after one successful test the owner reviews. Manual paste remains the fallback.
- Portfolio B operating policy now: agents_and_pm — automated GPT review is ON (paid usage approved); when the reviewer is unavailable, agents' vetoes + portfolio-manager decision without independent review, each decision tagged 'no independent review'
- Seasonality: context only; tested on SPY 1993-2026; never changes a trade

## Tests
- engine 11, runner 8, reviewer 6, social 3, rating 5 = 33 passing (2026-10-20)

## Known problems / limitations
- Price data comes from Yahoo Finance through the owner's PC browser pane; a run is skipped (and reported) if the PC is off.
- Dividends are not credited; SPY/QQQ are price-only.
- Backtest universe (82 names) has survivorship bias; the live 15 were chosen on recent performance.
- Reddit is unavailable as a direct source: blocked in both browsers by platform safety rules, and Reddit itself returns HTTP 403 ('blocked by network security') to the cloud even with www.reddit.com allowed (tested 2026-10-20). Not bypassed; Reddit discussion reaches the sentiment agent only second-hand via web search.
- X works only through the owner's signed-in Chrome (Claude in Chrome extension), so it needs the PC and Chrome open at run time.
- OpenAI reviewer: connectivity and key verified; paid calls refused until the OpenAI account has credits.

## Change log
- 2026-10-19 v1: single portfolio, fills at the signal close (superseded same day).
- 2026-10-19 v2: next-open fills with 5bp slippage; A/B portfolios; idempotent runs; veto log; 10-year backtest.
- 2026-10-19 v3: B review-gated (proposals -> packet -> morning decision); learning journal; seasonality tested on SPY 1993-2026; five-section dashboard; morning + weekly tasks.
- 2026-10-19 v4: Portfolio C (trend-only) approved; 10% close-based stop for A/B effective 2026-10-20; order time-stamps (no fill at an open already known); max-loss wording corrected.
- 2026-10-19 v5: review bridge (reviewer.py) with honest failure states; public report files (latest.md, decisions.json, performance.json, development.md); AI-collaboration dashboard section.
- 2026-10-19 v6: primary-source social/smart-money collection (StockTwits, SEC Form 4, Capitol Trades via the browser pane; X via the owner's signed-in Chrome), change detection vs a rolling baseline (social.py), Portfolio A order timestamps backfilled from database write time, report consistency fixes.
- 2026-10-20 v7: Reddit cloud test after allow-listing (HTTP 403 from Reddit; recorded as unavailable); B operating policy 'agents and PM' approved while the automated reviewer is parked; public reports published to GitHub by the evening run.
- 2026-10-20 v8: automated GPT reviews activated after a passed integration test (gpt-5, ~$0.03/review); earnings blackout made a deterministic engine check (agents and reviewer no longer the only guard); 13 day-one orders filled at the Oct 20 open by an owner-requested fill-only run.
- 2026-10-20 v9: intraday task (fills recorded shortly after the open; hourly mark-to-market shown on the dashboard; on-demand via Run now); fill timestamps on positions and trades; fills record moved to runs_intraday.
- 2026-10-20 v10: daily watchlist with frozen 1-5 research rating (criteria v1), research view vs rule action separated, exit conditions/proximity/next catalyst per position, permanent assessment history, weekly predictive-value and cash-adjusted evaluation. No new indicators, agents or portfolios.