# Beef Processor — FX Transaction Hedge Model · Technical Specification

| Field | Value |
|------|------|
| **Created by** | Yilin Li |
| **Updated by** | Yilin Li |
| **Date Created** | 2026-11-05 |
| **Date Updated** | 2026-11-13 |
| **Version** | 0.4 |
| **LLM Used** | Claude (Anthropic): helped draft sections and reviewed the draft for gaps |
| **Role** | Treasury Analyst |
| **Audience** | CFO / Director of Treasury |
| **Scenario** | beef-processor |

---

## 1. Problem Statement

A U.S. beef processor holds a EUR 3,900,000 receivable from an Italian supermarket chain, with the exchange rate quoted in USD per EUR. The receivable will settle in 91 days, on February 4, 2027. At today's spot rate of 1.1224, the unhedged receivable is worth $4,377,360. Every 1% decline in EUR/USD would reduce its value by approximately $43,800. For example, a 5% decline to 1.0663 would reduce the proceeds to $4,158,492, resulting in a loss of approximately $219,000. This model compares four strategies—no hedge, forward contract, money-market hedge, and put option—across a range of settlement spot rates to support the Stage 5 decision memo.

## 2. Inputs: the Named-Range Contract

All inputs are workbook named ranges. Values marked *indicative* are placeholders, replaced with live, sourced, timestamped market data at Stage 4.

| Named range | Description | Placeholder value | Unit | Placeholder source (2026-11-05) | Stage-4 live source |
|---|---|---:|---|---|---|
| `FC_AMT` | EUR receivable | 3,900,000 | EUR | Scenario (fixed) | Fixed, not replaced |
| `S0_in` | Spot rate at inception | 1.1224 *indicative* | USD per EUR | ECB euro reference rate | ECB reference rate on the Stage 4 date |
| `F0_in` | 91-day forward rate | 1.1653 *indicative* | USD per EUR | Scenario quote | Live 3-month EURUSD forward quote |
| `R_USD` | USD interest rate | 3.875% *indicative* | Annual %, ACT/360 | Midpoint of Fed target range 3.75–4.00% | Fed H.15 release |
| `R_FC` | EUR interest rate | 2.50% *indicative* | Annual %, ACT/360 | ECB deposit facility rate | ECB deposit facility rate on the Stage 4 date |
| `K_PUT` | Put strike | 1.1224 *indicative* | USD per EUR | Set at spot (`S0_in`) | Reset to the Stage 4 spot |
| `K_CALL` | Call strike | 1.1224 *indicative* | USD per EUR | Set at spot (`S0_in`) | Reset to the Stage 4 spot |
| `PREM_PUT` | Put premium per EUR | 0.0147 *indicative* | USD | Scenario quote | Live option quote |
| `PREM_CALL` | Call premium per EUR | 0.0199 *indicative* | USD | Scenario quote | Live option quote |
| `T_DAYS` | Days to settlement | 91 | Days | Scenario (fixed) | Fixed, not replaced |

## 3. Tab Architecture

The workbook has nine tabs. Tab names contain no spaces.

| Tab | Purpose |
|---|---|
| `Cover` | Title, scenario, author, and date; a summary of key outputs linked by formula; and a data-provenance block listing each market input with its source and date, marked "indicative, replaced with live data at Stage 4." |
| `Legend` | The color key, applied on every tab: **Yellow** = inputs (the ten named ranges), **Blue** = assumptions (fixed modeling conventions: `DAY_BASIS` = 360, `GRID_STEP` = 1%, `PARITY_TOL` = 0.05%), **Green** = formulas (intermediate calculations), **Gray** = outputs (the named results in §8). |
| `Inputs` | All ten named ranges from §2 (yellow) and the three assumption cells (blue). The only place a user changes values. |
| `Forward` | Forward hedge proceeds. |
| `MoneyMarket` | The three money-market steps in separate cells, plus the parity check. |
| `Option` | Put and call premium cost in USD, and an 11-row table of put proceeds and call cost as functions of `S_T`, using the §6 grid. |
| `Sensitivity` | The 11-row grid from −5% to +5% and the four-line comparison chart. |
| `Checks` | Every §7 rule computed as PASS or FAIL, showing actual vs. expected values. A failing check is shown as FAIL, never forced to pass. |
| `Notes` | Data sources with dates, and the assumptions from §4. |

## 4. Assumptions & Constraints

- Quote convention: All exchange rates are quoted in USD per EUR; a higher rate means a stronger euro.
- Horizon: The model uses one settlement date, with `T_DAYS = 91`.
- Day count: ACT/360 is used for both USD and EUR; interest is calculated as rate × `T_DAYS`/360.
- Premium treatment: Option premiums are paid upfront and subtracted as-is, without growing them at `R_USD`, to match the FX Lab. This understates the premium’s true cost by approximately $562.
- Parity expectation: Under covered interest parity, the money-market hedge should equal the forward hedge. With the current placeholders, they do not match: the implied forward is 1.1263 versus the quoted 1.1653, a gap of approximately $152,191 (3.35%). This reflects the scenario’s indicative forward rate against today’s spot rate, not a model error. The parity check will be repeated at Stage 4.
- Payment: The Italian supermarket chain pays the full EUR 3,900,000 on day 91.
- Strikes: Both the put and call option strikes are set at the initial spot rate.
- Call option: Included for reference only. A call option protects a foreign-currency payable, not a receivable.
- Excluded: Transaction costs, bid-ask spreads, counterparty risk, and taxes are excluded.
- Scenarios: Settlement spot rates are varied across a fixed grid, with no probabilities assigned to individual scenarios.

## 5. Calculation Flow

Rates are entered as decimals in formulas (3.875% = 0.03875). `S_T` is the settlement spot rate from the §6 grid. Every step below is its own cell, referenced by name. No formula references an input or output by cell address. Where a formula shows 360, the workbook references the blue assumption cell `DAY_BASIS`.

**Step 1: No hedge.** The firm sells the euros at whatever spot rate prevails at settlement.
`USD_NO_HEDGE(S_T) = FC_AMT × S_T`

**Step 2: Forward hedge.** The rate is locked today, so proceeds are the same for every `S_T`.
`USD_FWD = FC_AMT × F0_in`

**Step 3: Money-market hedge (three separate cells).**

1. Borrow euros today, sized so the receivable repays the loan plus interest exactly at settlement.
   `MM_BORROW = FC_AMT / (1 + R_FC × T_DAYS/360)`
2. Convert the borrowed euros to dollars at today's spot rate.
   `MM_USD_NOW = MM_BORROW × S0_in`
3. Invest the dollars until settlement.
   `USD_MM = MM_USD_NOW × (1 + R_USD × T_DAYS/360)`

**Step 4: Parity check.** Compares the quoted forward with the forward implied by interest rates, and the dollar gap between the two locked-in hedges.
`F_IMPLIED = S0_in × (1 + R_USD × T_DAYS/360) / (1 + R_FC × T_DAYS/360)`
`PARITY_GAP = USD_FWD − USD_MM`

**Step 5: Put hedge.** Below the strike, the put sets a floor; above it, the firm sells at the market rate. The premium is paid in either case.
`USD_PUT(S_T) = FC_AMT × MAX(S_T, K_PUT) − FC_AMT × PREM_PUT`

**Step 6: Call (reference only, payable view).** Shown for comparison; for a payable, the call caps the USD cost. It is not a hedge for this receivable.
`USD_CALL_COST(S_T) = FC_AMT × MIN(S_T, K_CALL) + FC_AMT × PREM_CALL`

## 6. Sensitivity Plan

**Grid.** `S_T = S0_in × (1 + shift)`, with `shift` from −5% to +5% in 1% steps: 11 rows, including the base row at `shift = 0%`. With the placeholder spot of 1.1224, the grid runs from 1.0663 to 1.1785.

**Columns (one row per `S_T`):**

| Column | Formula |
|---|---|
| Shift | −5% … +5% |
| `S_T` | `S0_in × (1 + shift)` |
| No hedge | `USD_NO_HEDGE(S_T)` |
| Forward | `USD_FWD` (constant) |
| Money market | `USD_MM` (constant) |
| Put hedge | `USD_PUT(S_T)` |
| Best overall | Name of the highest of No hedge, Forward, Money market, and Put hedge |
| Best hedge | Name of the highest of Forward, Money market, and Put hedge (excludes No hedge) |

The call (`USD_CALL_COST`) is not part of the grid or chart, since it does not hedge this receivable.

**Chart.** One line chart: `S_T` on the x-axis, USD proceeds on the y-axis, with four series: No hedge, Forward, Money market, Put hedge.

**What the chart should let the CFO see:**

- **No hedge** is a rising diagonal: full exposure in both directions.
- **Forward** and **Money market** are flat lines: certainty. With the current placeholders, Money market sits about $152,191 below Forward (the parity gap in §4).
- **Put hedge** is a "hockey stick": flat at the floor ($4,320,030) below the strike (1.1224), then rising with the market above it, where it runs exactly one premium ($57,330) below No hedge.
- **Crossovers:** No hedge beats Forward only if the euro ends above 1.1653. The put beats Forward only above about 1.1800, which is outside this grid, so the Forward is the best hedge in every row.

## 7. Validation Rules (Check Figures)

The finished workbook must pass every rule below. These rules are the Stage 3 audit checklist. Dollar check figures must match within $1 (rounding); rates within 0.0001. Each rule is computed on the `Checks` tab.

**Structural rules**

1. All ten named ranges from §2 exist, and every formula refers to inputs and outputs by name. Inside the 11-row grids, a formula may reference cells in its own row (for example, `S_T` uses that row's shift); no formula references an input or output by cell address.
2. Every gray output cell is a formula. No output is a typed-in number.
3. No error values anywhere (`#DIV/0!`, `#N/A`, `#REF!`, `#VALUE!`, `#NAME?`).
4. Changing any input on the `Inputs` tab updates every output and the chart.
5. The sensitivity grid has exactly 11 rows, and `S_T` in the 0% row equals `S0_in`.

**Economic rules**

6. **Parity check.** `F_IMPLIED` and `PARITY_GAP` are always displayed. With the current placeholders, the expected gap is $152,191 (3.35% of `USD_FWD`), explained in §4. Once Stage 4 inputs are live, `|PARITY_GAP| / USD_FWD` must be 0.05% or less; if not, stop and find the cause before trusting either hedge.
7. **Put floor.** No `USD_PUT(S_T)` value is below `FC_AMT × (K_PUT − PREM_PUT)`.
8. **Put above the strike.** Wherever `S_T` is at or above `K_PUT`, `USD_PUT(S_T) = USD_NO_HEDGE(S_T) − FC_AMT × PREM_PUT` (exactly $57,330 below No hedge).
9. **Forward and Money market** are each constant across all 11 grid rows (neither depends on `S_T`).

**Check figures (placeholder inputs, verified in the FX Hedging Lab on 2026-11-05)**

| Output | Expected value |
|---|---:|
| `USD_FWD` | $4,544,670 |
| `MM_BORROW` | 3,875,509 EUR |
| `MM_USD_NOW` | $4,349,871 |
| `USD_MM` | $4,392,479 |
| `F_IMPLIED` | 1.1263 |
| `PARITY_GAP` | $152,191 |
| `USD_PUT_FLOOR` | $4,320,030 |

| Shift | `S_T` | No hedge | Forward | Money market | Put hedge | Best overall | Best hedge |
|---|---:|---:|---:|---:|---:|---|---|
| −5% | 1.0663 | $4,158,492 | $4,544,670 | $4,392,479 | $4,320,030 | Forward | Forward |
| −4% | 1.0775 | $4,202,266 | $4,544,670 | $4,392,479 | $4,320,030 | Forward | Forward |
| −3% | 1.0887 | $4,246,039 | $4,544,670 | $4,392,479 | $4,320,030 | Forward | Forward |
| −2% | 1.1000 | $4,289,813 | $4,544,670 | $4,392,479 | $4,320,030 | Forward | Forward |
| −1% | 1.1112 | $4,333,586 | $4,544,670 | $4,392,479 | $4,320,030 | Forward | Forward |
| 0% | 1.1224 | $4,377,360 | $4,544,670 | $4,392,479 | $4,320,030 | Forward | Forward |
| +1% | 1.1336 | $4,421,134 | $4,544,670 | $4,392,479 | $4,363,804 | Forward | Forward |
| +2% | 1.1448 | $4,464,907 | $4,544,670 | $4,392,479 | $4,407,577 | Forward | Forward |
| +3% | 1.1561 | $4,508,681 | $4,544,670 | $4,392,479 | $4,451,351 | Forward | Forward |
| +4% | 1.1673 | $4,552,454 | $4,544,670 | $4,392,479 | $4,495,124 | No hedge | Forward |
| +5% | 1.1785 | $4,596,228 | $4,544,670 | $4,392,479 | $4,538,898 | No hedge | Forward |

Best overall is Forward from −5% through +3%, and No hedge at +4% and +5%. Best hedge is Forward in all 11 rows.

## 8. Outputs

All outputs are gray cells, computed by formula, with exactly these names.

| Output | Definition | Tab | Placeholder value |
|---|---|---|---:|
| `USD_FWD` | `FC_AMT × F0_in` | `Forward` | $4,544,670 |
| `MM_BORROW` | Step 3.1 of §5 | `MoneyMarket` | 3,875,509 EUR |
| `MM_USD_NOW` | Step 3.2 of §5 | `MoneyMarket` | $4,349,871 |
| `USD_MM` | Step 3.3 of §5 | `MoneyMarket` | $4,392,479 |
| `F_IMPLIED` | Step 4 of §5 | `MoneyMarket` | 1.1263 |
| `PARITY_GAP` | `USD_FWD − USD_MM` | `MoneyMarket` | $152,191 |
| `PUT_COST` | `FC_AMT × PREM_PUT` | `Option` | $57,330 |
| `CALL_COST` | `FC_AMT × PREM_CALL` | `Option` | $77,610 |
| `USD_PUT_FLOOR` | `FC_AMT × (K_PUT − PREM_PUT)` | `Option` | $4,320,030 |
| `USD_BASE_NO_HEDGE` | `USD_NO_HEDGE` at `S_T = S0_in` | `Sensitivity` | $4,377,360 |
| Sensitivity table | The 11-row grid from §6 | `Sensitivity` | §7 check figures |
| Comparison chart | The four-line chart from §6 | `Sensitivity` | — |

The `Cover` tab repeats `USD_FWD`, `USD_MM`, `PARITY_GAP`, `USD_PUT_FLOOR`, and `USD_BASE_NO_HEDGE` as a one-glance summary, linked by formula, not retyped.

---

## Appendix A — Change Log

| Version | Date | Author | Change |
|---------|------|--------|--------|
| 0.1 | 2026-11-05 | Yilin Li | Initial draft |
| 0.2 | 2026-11-05 | Yilin Li | Fixed gaps found in AI review: §7 rule 9 (Forward ≠ Money market under parity gap), §6 put description below strike, §6 "Best overall" wording, §7 rule 5 wording, completed all 11 check-figure rows |
| 0.3 | 2026-11-13 | Yilin Li | Aligned spec with Stage 3 build contract: Cover provenance block, four-color legend with blue assumption cells, call premium and payoff table, Checks tab |
| 0.4 | 2026-11-13 | Yilin Li | Stage 3 audit Finding 5: rewrote §7 rule 1 to allow same-row references inside the 11-row grids |
