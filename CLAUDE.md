# Barcelona: Independent P123 Strategy Development

## Project Goal

Build an original Portfolio 123 ranking system and strategy that achieves comparable or better risk-adjusted performance to Aaron's existing system (the "benchmark"). The critical constraint: **this must not be a bootstrap or derivative of the benchmark system.** The new strategy should pursue genuinely different alpha sources so that its returns are meaningfully uncorrelated with the benchmark. The value of a second uncorrelated strategy is diversification — even if it slightly underperforms standalone, it adds value in a portfolio context.

### Ground Rules

1. **No cloning.** Do not copy the benchmark's factor structure with minor weight adjustments. The whole point is a different approach.
2. **Different factor philosophy.** If the benchmark emphasizes growth + profitability + momentum, the new system should find alpha elsewhere — value, capital efficiency, earnings quality, mean reversion, etc.
3. **Valid P123 syntax only.** Every formula must use functions from `Copy of Functions.csv` and factors from `Copy of Factors.csv`. Look up parameter values in `Copy of Parameters.csv`. Do not invent functions or factors.
4. **Testable.** Deliverables must be runnable on the P123 platform — ranking system XML files and buy/sell rule definitions.
5. **Evaluation criteria defined upfront** (see Evaluation Criteria section below).

---

## Benchmark System Profile

Aaron's current system is a multi-factor stock ranking model, live since October 2023. The full ranking system definition is in `ranking-system-benchmark.xml` in this folder — study it to understand what to differentiate from.

### Performance (best variant, 20-year rolling backtest Jan 2006–Jan 2026)

| Metric | Value |
|--------|-------|
| Annualized Return | 21.5% |
| Sharpe Ratio | 1.05 |
| Sortino Ratio | 1.46 |
| Alpha vs. benchmark | 8.5% |
| Benchmark Beat Rate | 82.5% (% of 18-month windows) |
| Sharpe CV | 0.91 (consistency measure — lower is better) |
| Max Drawdown (mean) | -23.1% |
| Win Rate | ~72% |
| Avg Winner | +73% |
| Avg Loser | -13% |
| Avg Holding Period | ~174 days |

### Benchmark's Factor Philosophy (what NOT to replicate)

The benchmark is heavily oriented around:
- **Profitability margins** (gross, operating, net — at multiple time horizons, vs. universe and vs. industry/subsector)
- **Growth** (EPS, sales, EBITDA growth rates — historical and projected)
- **Quality** (ROE, financial strength ratios, CROIC)
- **Momentum/Sentiment** (EPS/sales revisions, analyst recommendations, price momentum via moving averages and Donchian channels, accumulation/distribution)
- **Valuation** (PEG, P/FCF — but as a minor component)

The benchmark's weighting: Growth+Margins+Quality ~60%, EPS ~23%, Sentiment/Momentum/Valuation ~28%, CROIC/OpMgn ~12%.

### Benchmark's Known Blind Spots (opportunities)

- **Energy and Utilities sectors are completely ignored** — the benchmark's ranking actually inverts in these sectors (low-ranked stocks outperform high-ranked ones there). A system that works well in sectors the benchmark ignores would be uncorrelated by construction.
- **Momentum-heavy** — the benchmark rides momentum trends. A mean-reversion or contrarian approach would be naturally uncorrelated.
- **Concentrated** (~10 positions) — a broader approach (20-30 stocks) could provide different diversification characteristics.
- **Analyst-dependent** — heavy use of EPS revisions and recommendations. Signals that don't rely on analyst coverage could differentiate.
- **Growth-biased** — value factors are minor in the benchmark. Deep value approaches are underrepresented.

---

## Portfolio Construction — Flexible

You are NOT constrained to match the benchmark's portfolio construction. You may explore:
- Different **position counts** (10, 15, 20, 30+)
- Different **rebalance frequencies** (weekly, biweekly, monthly, quarterly)
- Different **holding period targets**
- Different **concentration limits** (sector caps, industry caps, or none)
- Different **buy/sell rule structures**

The benchmark uses a custom universe ("AS1K-rdx"), weekly rebalance, ~10 positions, IndCount<5 concentration cap. The full universe definition is in `benchmark-universe.md` — study it carefully because it reveals both constraints and opportunities:

### Benchmark Universe Key Characteristics

- **Starting point:** ~9000+ US-listed stocks including foreign ADRs
- **Filtered to ~1000** by market cap (`FOrder("MktCap", #Previous) <= 1000`)
- **Excludes:** Banking (RBICS 3010), Real Estate (RBICS 3025), MLPs, OTC/pink sheets
- **Liquidity floors:** Price > $10, median and avg daily dollar volume > $35K
- **Analyst coverage required:** At least 2 annual EPS/Sales estimates, 1 quarterly each
- **Timeliness:** Financial reports must be less than 40 days late

### Differentiation Opportunities from Universe Design

The benchmark universe's analyst coverage requirement (`ConsEstCnt > 1-2`) means it **excludes under-covered stocks** — this is a deliberate choice that biases toward well-followed names. A new strategy could:
- Use the same universe but exploit factors the benchmark ignores (different ranking approach, same stock pool)
- Build a different universe that relaxes the analyst coverage requirement to access neglected/under-followed stocks where information asymmetry is higher
- Include Banking or Real Estate sectors that the benchmark excludes entirely
- Target different market cap segments within the US-listed universe

You should ask Aaron about universe flexibility and any hard constraints before finalizing.

---

## P123 Language Reference

### How to Use the Reference Files

The P123 language is a formula language for financial calculations. It is NOT a general-purpose programming language — each formula is a single-line expression.

**Reference files in this folder:**

| File | Purpose | Rows |
|------|---------|------|
| `Copy of Functions.csv` | All available P123 functions with parameters | ~459 |
| `Copy of Factors.csv` | Complete list of financial factors/metrics | ~4,342 |
| `Copy of Parameters.csv` | Valid parameter values for functions | ~765 |
| `Copy of Docs.csv` | Cross-reference links to documentation | ~339 |
| `Copy of P123AcronymsAndFunctions.pdf` | Overview of P123 language and syntax | — |
| `Single File - p123reference-v2.html.pdf` | Complete P123 reference documentation | — |

**Usage rules:**
1. Use only functions listed in `Copy of Functions.csv`
2. Use only factors listed in `Copy of Factors.csv`
3. Look up valid parameter values in `Copy of Parameters.csv`
4. Cross-reference documentation via doc_id in `Copy of Docs.csv`
5. Case is not important in formulas
6. Functions must have at least one parameter in parentheses
7. Optional parameters are denoted with square brackets
8. Operators: `+`, `-`, `*`, `/`, `^` (exponentiation)
9. Conditional operators: `Or`, `And`
10. Logical operators: `=`, `!=`, `>`, `<`, `<=`, `>=`
11. Use parentheses for precedence: `((A1 + B1) * 2)`

See `1. Reference-Instructions.md` for the full syntax guide.

---

## Ranking System XML Format

Ranking systems are defined in XML. Study `Example-RankingSystem.xml` and `ranking-system-benchmark.xml` for working examples.

### Structure

```xml
<RankingSystem RankType="Higher">
  <!-- Top-level composites with weights (must sum to 100 conceptually) -->
  <Composite Name="Category Name" Weight="25" RankType="Higher">

    <!-- Stock Factor: a built-in P123 factor -->
    <StockFactor Weight="10" RankType="Higher" Scope="Universe">
      <Factor>FactorName</Factor>
    </StockFactor>

    <!-- Stock Formula: a custom formula expression -->
    <StockFormula Weight="10" RankType="Higher" Name="Display Name" Description="" Scope="Industry">
      <Formula>your formula here</Formula>
    </StockFormula>

    <!-- Industry Factor: ranks stocks by their industry's aggregate metric -->
    <IndFactor Weight="10" RankType="Higher">
      <Factor>IndustryFactorName</Factor>
    </IndFactor>

    <!-- Nested composites are allowed -->
    <Composite Name="Sub-Category" Weight="5" RankType="Higher">
      ...
    </Composite>
  </Composite>
</RankingSystem>
```

### Key Attributes

- **RankType:** `"Higher"` means higher values rank better; `"Lower"` means lower values rank better (e.g., for volatility or P/E)
- **Scope:** `"Universe"` (rank vs. all stocks), `"Industry"` (rank vs. same industry), `"Sub-Sector"` (rank vs. same subsector)
- **Weight:** Relative weight within the parent composite. Weights are relative, not absolute percentages (a composite with weights 3, 3, 3 is equivalent to 33/33/33).

---

## Buy/Sell Rule Format

Buy and sell rules are conditional expressions that filter which stocks enter or exit the portfolio. See `Example-BuySellRules.md` for examples.

### Buy Rule Examples
```
[Rule Name] formula > threshold
[Sector Cap] SecWeight < 30
[Quality] Rating("Classic Quality") > 75
[Yield] Between(FRank("Yield"), 65, 80)
[No Mortgage REITs] RBICS(REITMORTG) = false
```

### Sell Rule Examples
```
[Weight Cap] eval(Weight > 7.5, .33, 0)
[Quality Drop] Rating("Classic Quality") < 35
[Dividend Cut] DivPS(0,qtr) < DivPS(1,qtr)
```

Key functions for rules:
- `FRank("Factor")` — percentile rank of a factor across the universe (0-100)
- `Rating("RankingSystemName")` — a stock's score in another ranking system
- `SecWeight` / `IndWeight` — current portfolio weight in that sector/industry
- `IndCount` — number of holdings in the same industry
- `eval(condition, true_value, false_value)` — conditional evaluation
- `Between(value, low, high)` — range check
- `NoBars` — number of bars (trading days) since purchase

---

## Suggested Alpha Source Directions

These are starting points for exploration — areas where strong academic and practitioner evidence exists, and that are largely orthogonal to the benchmark's growth+momentum approach:

1. **Value / Contrarian** — Low P/E, P/B, P/S, P/FCF relative to history or peers. Enterprise value ratios (EV/EBITDA, EV/Sales). Stocks trading below intrinsic value estimates.

2. **Capital Allocation Quality** — Share buyback yield, net debt reduction, dividend growth consistency, capital expenditure efficiency. Companies that deploy capital well tend to outperform.

3. **Earnings Quality / Accruals** — Low accruals (cash earnings close to reported earnings), consistent cash flow generation, low revenue recognition aggressiveness. The accruals anomaly is one of the most robust in the literature.

4. **Balance Sheet Strength** — Low leverage, high interest coverage, strong current ratio, Altman Z-score. Avoiding financial distress while capturing the quality premium.

5. **Shareholder Yield** — Combined dividend yield + buyback yield + debt paydown yield. A broader measure of cash returned to shareholders.

6. **Mean Reversion / Relative Value** — Stocks that have underperformed their sector or historical norms. Price-to-52-week-high as a contrarian signal. RSI oversold conditions.

7. **Stability / Low Volatility** — Lower-beta, lower-volatility stocks with consistent earnings. The low-volatility anomaly is well-documented.

8. **Cross-Sector Relative Value** — Instead of finding the best stocks in the best sectors (benchmark's approach), find the best stocks across ALL sectors including ones the benchmark ignores.

---

## Testing Workflow

### Phase 1: Manual Validation (Current)
1. Design ranking system(s) and buy/sell rules
2. Output as XML and rule definitions
3. Aaron manually enters them on P123 and runs backtests
4. Review results, iterate on design

**Key focus in this phase:** Get the P123 syntax right. Every formula must parse without errors. Aaron will flag syntax issues — fix them before iterating on strategy design.

### Phase 2: API-Driven Iteration (Future)
Once syntax is validated:
1. Use P123 API to run backtests programmatically
2. Iterate more rapidly on factor selection and weighting
3. Run rolling backtests for robust comparison to benchmark

Aaron will provide API access details when ready for this phase.

---

## Evaluation Criteria

Define these before running any tests. Compare the new strategy against both the benchmark system and the universe benchmark (equal-weight top 1000).

| Metric | What It Measures | Target |
|--------|-----------------|--------|
| **Sharpe Ratio** | Risk-adjusted return | >= 0.90 |
| **Sortino Ratio** | Downside-risk-adjusted return | >= 1.20 |
| **Alpha** | Excess return vs. universe benchmark | >= 5% |
| **Benchmark Beat Rate** | % of 18-month windows outperforming | >= 70% |
| **Max Drawdown** | Worst peak-to-trough decline | Better than -30% |
| **Sharpe CV** | Consistency of Sharpe across windows | <= 1.10 |
| **Correlation to Benchmark Strategy** | How uncorrelated from Aaron's system | <= 0.60 |
| **Annualized Return** | Raw return | >= 15% |

The correlation target is critical — a strategy with 0.80 Sharpe and 0.30 correlation to the benchmark may be more valuable than one with 1.00 Sharpe and 0.90 correlation.

---

## Questions to Ask Aaron

Before finalizing a strategy design, clarify these with Aaron:

1. **Universe:** Is "AS1K-rdx" the only available universe, or are others (e.g., small-cap, all-cap) available?
2. **Position count preference:** Any strong preference on number of holdings, or fully flexible?
3. **Turnover tolerance:** Is high turnover acceptable, or is there a preference for lower-turnover approaches?
4. **Sector exclusions:** Are there sectors that should be excluded (e.g., financials, utilities)?
5. **Max drawdown tolerance:** What's the maximum acceptable drawdown for the new strategy?
6. **Leverage/shorting:** Is this long-only, or can short positions be considered?
7. **Transaction costs:** What slippage/cost assumptions should be used in backtests?
8. **Custom formulas:** Does Aaron have custom formulas (prefixed with `$`) that could be used, or should the system use only standard P123 factors?
9. **Combination intent:** Will this strategy run standalone or be combined with the benchmark strategy in a multi-strategy portfolio? (This affects how much to weight correlation vs. standalone performance.)
10. **Timeline:** What's the pace for iteration — how quickly can Aaron run manual backtests?
