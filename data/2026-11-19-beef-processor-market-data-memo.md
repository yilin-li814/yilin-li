---
type: memo
title: "Market-Data Memo: Beef Processor EUR Receivable"
scenario: beef-processor
date: 2026-11-19
llm_used: "Claude (Anthropic): retrieved the market data, populated the workbook, and recomputed the check figures"
---

# Market-Data Memo: Beef Processor EUR Receivable

**Author:** Yilin Li  
**Date:** 2026-11-19  
**Workbook:** `capabilities/fx-hedging/model.xlsx` (populated with live data)  
**Exposure:** EUR 3,900,000 receivable from an Italian supermarket chain, 91 days

---

## 1. Inputs and provenance

All values retrieved on 2026-11-19. Each can be re-pulled from the source shown.

| Named range | Value | Source | As of | Notes |
|---|---:|---|---|---|
| `FC_AMT` | 3,900,000 EUR | Scenario (instructor email, 2026-10-18) | — | Fixed |
| `T_DAYS` | 91 | Scenario | — | Fixed |
| `S0_in` | 1.0992 | ECB euro foreign exchange reference rate, ecb.europa.eu | 2026-11-19, 14:15 CET fix | Live |
| `R_USD` | 4.26% | US 3-month Treasury bill yield, tradingeconomics.com | 2026-11-19 close | Live; cross-check: FRED `DGS3MO` (Fed H.15) 4.33% on 2026-11-17 |
| `R_FC` | 2.825% | 3-month Euribor fixing, euribor-rates.eu | 2026-11-18 fixing | Latest fixing published at retrieval |
| `F0_in` | 1.1653 | Bank quote from scenario (instructor email) | 2026-10-18 | **Kept**, as required; compared with CIP below |
| `K_PUT` | 1.0992 | Set at live spot | 2026-11-19 | Scenario convention: strike at spot |
| `K_CALL` | 1.0992 | Set at live spot | 2026-11-19 | Scenario convention: strike at spot |
| `PREM_PUT` | $0.0147 per EUR | Scenario | 2026-10-18 | **Assumption**: kept because retail FX option quotes are unreliable |
| `PREM_CALL` | $0.0199 per EUR | Scenario | 2026-10-18 | **Assumption**: kept because retail FX option quotes are unreliable |

## 2. Rate choices

- **R_USD: 3-month US Treasury bill yield.** Its tenor matches the 91-day horizon, and it is the H.15 series named in the spec. I used the 19 Nov market close (4.26%) rather than the H.15 figure (4.33%), which lags two business days, so all inputs share one date.
- **R_FC: 3-month Euribor.** It matches the 91-day horizon and is the standard euro money-market reference. The Stage 2 placeholders used overnight policy rates (Fed funds midpoint, ECB deposit rate); term rates are the better fit for a 3-month hedge.
- **Caveats.** The T-bill is a government rate and Euribor is an interbank rate, so the two legs carry slightly different credit. The T-bill yield is quoted on a 365-day investment basis and is used as-is under the model's ACT/360 convention; the effect on `USD_MM` is under $1,000.

## 3. The bank's forward vs. the CIP-implied forward

CIP-implied forward = S0 × (1 + R_USD × 91/360) / (1 + R_FC × 91/360) = 1.0992 × 1.010768 / 1.007141 = **1.1032**

| | Rate | USD on EUR 3.9M |
|---|---:|---:|
| Bank quote (`F0_in`) | 1.1653 | $4,544,670 |
| CIP-implied forward | 1.1032 | $4,302,320 |
| **Gap** | **0.0621 (5.6% of the implied rate)** | **$242,350** |

The quote is 6.2 cents above where interest rates say a forward should trade. The gap has grown since Stage 2 ($152,191), because spot fell from 1.1224 to 1.0992 while the quote stayed fixed. A bank would not normally leave a forward this far from parity; the most likely explanation is that the quote is stale. **I kept the quote, as the stage requires,** and flag it for Stage 5: before acting on a forward, the firm should ask the bank for a requote.

## 4. Populating the workbook

- Live values were entered only in the yellow named input cells on `Inputs`: `S0_in`, `R_USD`, `R_FC`, `K_PUT`, `K_CALL`. `F0_in`, the premiums, `FC_AMT` and `T_DAYS` were unchanged.
- **Verification:** I compared every formula in the populated workbook with the Stage 3 version. Zero formulas changed; recalculation produced zero errors across 237 formulas.
- **Structural fix (anticipated in the Stage 3 audit, Finding 6):** the expected values on the `Checks` tab were typed for the placeholder inputs, so they would all fail once live data loaded. I replaced them with check figures for the live inputs, recomputed independently and confirmed in the FX Hedging Lab (§6). The rule checks (3–9) needed no change.
- Documentation cells (`Cover` provenance block, `Notes`, source column on `Inputs`) were updated to the live sources.

## 5. Re-run checks

The `Checks` tab shows **25 PASS, 1 FAIL**.

- **Parity: FAIL, not forced.** `PARITY_GAP` = $242,350, 5.33% of `USD_FWD`, against a 0.05% tolerance. Cause: the bank quote vs. live spot and rates (§3), not a formula error; every money-market step matches the independent recomputation.
- **Sensitivity recalculates around the new spot.** The grid now runs from 1.0442 to 1.1542 (was 1.0663–1.1785).

| Output | Stage 2 placeholders | Live (19 Nov 2026) |
|---|---:|---:|
| `USD_FWD` | $4,544,670 | $4,544,670 |
| `MM_BORROW` | 3,875,509 EUR | 3,872,348 EUR |
| `MM_USD_NOW` | $4,349,871 | $4,256,485 |
| `USD_MM` | $4,392,479 | $4,302,320 |
| `F_IMPLIED` | 1.1263 | 1.1032 |
| `PARITY_GAP` | $152,191 | $242,350 |
| `USD_PUT_FLOOR` | $4,320,030 | $4,229,550 |
| `USD_BASE_NO_HEDGE` | $4,377,360 | $4,286,880 |

| Shift | `S_T` | No hedge | Forward | Money market | Put hedge | Best overall | Best hedge |
|---|---:|---:|---:|---:|---:|---|---|
| −5% | 1.0442 | $4,072,536 | $4,544,670 | $4,302,320 | $4,229,550 | Forward | Forward |
| −4% | 1.0552 | $4,115,405 | $4,544,670 | $4,302,320 | $4,229,550 | Forward | Forward |
| −3% | 1.0662 | $4,158,274 | $4,544,670 | $4,302,320 | $4,229,550 | Forward | Forward |
| −2% | 1.0772 | $4,201,142 | $4,544,670 | $4,302,320 | $4,229,550 | Forward | Forward |
| −1% | 1.0882 | $4,244,011 | $4,544,670 | $4,302,320 | $4,229,550 | Forward | Forward |
| 0% | 1.0992 | $4,286,880 | $4,544,670 | $4,302,320 | $4,229,550 | Forward | Forward |
| +1% | 1.1102 | $4,329,749 | $4,544,670 | $4,302,320 | $4,272,419 | Forward | Forward |
| +2% | 1.1212 | $4,372,618 | $4,544,670 | $4,302,320 | $4,315,288 | Forward | Forward |
| +3% | 1.1322 | $4,415,486 | $4,544,670 | $4,302,320 | $4,358,156 | Forward | Forward |
| +4% | 1.1432 | $4,458,355 | $4,544,670 | $4,302,320 | $4,401,025 | Forward | Forward |
| +5% | 1.1542 | $4,501,224 | $4,544,670 | $4,302,320 | $4,443,894 | Forward | Forward |

With live spot, the forward is now the best strategy in every row, including +4% and +5%, where no hedge used to win. That result rests entirely on the 1.1653 quote, which is the number Stage 5 must test.

## 6. FX Hedging Lab cross-check

Live inputs entered in the Lab: `S0_in` 1.0992, `R_USD` 4.26, `R_FC` 2.825, `K_PUT` and `K_CALL` 1.0992; the other inputs as in §1.

| Output | Workbook | FX Lab |
|---|---:|---:|
| Forward hedge | $4,544,670 | $4,544,670 |
| MM step 1: EUR borrowed | 3,872,348 EUR | 3,872,348 EUR |
| MM step 2: USD at spot | $4,256,485 | $4,256,485 |
| MM step 3: USD invested | $4,302,320 | $4,302,320 |
| Implied forward | 1.1032 | 1.1032 |
| Put hedge at −5% / 0% / +5% | $4,229,550 / $4,229,550 / $4,443,894 | $4,229,550 / $4,229,550 / $4,443,894 |

**Result:** Every output matches the workbook to the dollar, and all 11 sensitivity rows match, with Forward as the best strategy in each row. The Lab also flags the same parity gap ($242,350). Its warning says a gap like this usually means a formula error; here the Lab and the workbook compute the gap independently and agree exactly, so the gap comes from the bank's quote, not from either model's formulas. No discrepancy to resolve.
