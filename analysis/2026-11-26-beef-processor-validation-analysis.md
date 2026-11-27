---
type: analysis
title: "Validation: Beef Processor FX Hedge"
scenario: beef-processor
date: 2026-11-26
llm_used: "Claude (Anthropic): Part 1 run in a fresh session with only the spec and market-data memo; in my working session Claude computed the comparison and hand-verification tables and organized my dictated retrospective"
---

# Validation: Beef Processor FX Hedge

**Author:** Yilin Li  
**Date:** 2026-11-26  
**Inputs:** live data of 19 Nov 2026 (`data/2026-11-19-beef-processor-market-data-memo.md`)  
**Workbook:** `capabilities/fx-hedging/model.xlsx`

---

## Part 1: Independent LLM run

On 2026-11-26 I opened a fresh Claude conversation with no history and attached exactly two files: `capabilities/fx-hedging/spec.md` (v0.4) and `data/2026-11-19-beef-processor-market-data-memo.md`. I sent one prompt and did not reply or correct it. The prompt is logged in `prompt-log.md`; the raw output is in Appendix A.

## Part 2a: Comparison, LLM vs. workbook

S_T points: −5% (1.0442), 0% (1.0992), +5% (1.1542).

| Strategy | S_T | LLM | Workbook | Match? |
|---|---|---:|---:|---|
| No hedge | 1.0442 | $4,072,536 | $4,072,536 | Yes |
| No hedge | 1.0992 | $4,286,880 | $4,286,880 | Yes |
| No hedge | 1.1542 | $4,501,224 | $4,501,224 | Yes |
| Forward | all | $4,544,670 | $4,544,670 | Yes |
| Money market: EUR borrowed | — | 3,872,348 EUR | 3,872,348 EUR | Yes |
| Money market: USD at spot | — | $4,256,485 | $4,256,485 | Yes |
| Money market: proceeds | all | $4,302,320 | $4,302,320 | Yes |
| Implied forward / parity gap | — | 1.1032 / $242,350 | 1.1032 / $242,350 | Yes |
| Put | 1.0442 | $4,229,550 | $4,229,550 | Yes |
| Put | 1.0992 | $4,229,550 | $4,229,550 | Yes |
| Put | 1.1542 | $4,443,894 | $4,443,894 | Yes |
| Call cost (payable view) | 1.0442 | $4,150,146 | $4,150,146 | Yes |
| Call cost (payable view) | 1.0992 | $4,364,490 | $4,364,490 | Yes |
| Call cost (payable view) | 1.1542 | $4,364,490 | $4,364,490 | Yes |
| Best strategy (receivable) | all three | Forward | Forward | Yes |

**Discrepancies:** none in the numbers; every figure agrees to the dollar. Two process issues came up instead: the LLM had to assume that the memo's 19 November inputs replace the spec's 5 November placeholders, and it compared its figures with the results table in my memo. Both are discussed in Part 4.

## Part 2b: Hand verification (named-range notation, arithmetic shown, no Excel)

| # | Outcome | Formula | Arithmetic | Result | Workbook |
|---|---|---|---|---:|---:|
| 1 | Forward proceeds | `FC_AMT × F0_in` | 3,900,000 × 1.1653 | $4,544,670 | $4,544,670 ✓ |
| 2a | MM step 1: EUR growth factor | `1 + R_FC × T_DAYS/360` | 1 + 0.02825 × 91/360 = 1 + 0.02825 × 0.252778 = 1 + 0.0071410 | 1.0071410 | — |
| 2b | MM step 1: borrow EUR | `FC_AMT / factor` | 3,900,000 / 1.0071410 | 3,872,348 EUR | 3,872,348 ✓ |
| 2c | MM step 2: convert at spot | `MM_BORROW × S0_in` | 3,872,348 × 1.0992 | $4,256,485 | $4,256,485 ✓ |
| 2d | MM step 3: USD growth factor | `1 + R_USD × T_DAYS/360` | 1 + 0.0426 × 0.252778 = 1 + 0.0107683 | 1.0107683 | — |
| 2e | MM step 3: invest USD | `MM_USD_NOW × factor` | 4,256,485 × 1.0107683 | $4,302,320 | $4,302,320 ✓ |
| 3 | Implied forward | `S0_in × USD factor / EUR factor` | 1.0992 × 1.0107683 / 1.0071410 = 1.1110365 / 1.0071410 | 1.1032 | 1.1032 ✓ |
| 4 | Parity gap | `USD_FWD − USD_MM` | 4,544,670 − 4,302,320 | $242,350 | $242,350 ✓ |
| 5 | Put at S_T = 1.0442 (put exercised) | `FC_AMT × MAX(S_T, K_PUT) − FC_AMT × PREM_PUT` | 3,900,000 × MAX(1.0442, 1.0992) − 3,900,000 × 0.0147 = 4,286,880 − 57,330 | $4,229,550 | $4,229,550 ✓ |
| 6 | Put at S_T = 1.1542 (put expires) | same | 3,900,000 × 1.1542 − 57,330 = 4,501,224 − 57,330 | $4,443,894 | $4,443,894 ✓ |

Every result reconciles to the workbook to the dollar. Check on step 1 of the money market: 3,872,348 × 1.0071410 = 3,900,000, so the receivable repays the EUR loan and its interest exactly.

## Part 4: Spec retrospective

These two issues show that my original specification was not clear enough, and that my memo made it too easy for the AI to find the answers instead of calculating them independently.

**The AI had to guess which inputs to use.** My spec still lists the 5 November placeholder values, while my memo has the live 19 November values, and nothing said which one wins. The AI guessed correctly, but another model could have used the old numbers. Instead of updating the specification every time the market data changes, version 2 would add a rule stating that the latest market-data memo always takes priority over the placeholder values. This makes the specification reusable.

**The answers were available to the AI.** My market-data memo included the full results table, and the AI said its figures matched my memo's table. Matching the correct answers does not necessarily mean the AI understands how to calculate them. For version 2, I would remove the results table from the materials given to the AI and provide only the raw market data. I would keep the expected results in a separate document and use them to check the AI's calculations afterward, so the AI performs its calculations independently.

**What worked.** I would keep the existing formulas, because they were already clear and worked correctly: the AI reproduced every step of the money-market hedge and the option payoffs exactly.

Overall, version 2 should be more specific, consistent, and designed to test the AI's actual calculation and reasoning abilities, which makes the validation process more reliable.

---

## Appendix A: Raw LLM output (fresh session, 2026-11-26, unedited)

I'll use the live 19 Nov inputs from your memo, since they replace the spec's placeholders, and run the spec's §5 formulas on them.

## Assumptions

1. **Inputs:** I used the live values in your 19 Nov memo, not the spec's placeholders: S₀ = K_PUT = K_CALL = 1.0992, R_USD = 4.26%, R_FC = 2.825%, F₀ = 1.1653 (the bank quote you kept), and premiums of 0.0147 for the put and 0.0199 for the call. FC_AMT is EUR 3,900,000, T is 91 days, and the day count is ACT/360.
2. **Formulas:** I followed spec §5 exactly. Premiums are subtracted without growing them at R_USD (your §4 convention).
3. **Call:** I show it as the spec does, as the USD cost of a EUR payable. It does not hedge this receivable and can't be ranked against the other strategies.
4. **Settlement rates:** S_T = 1.0992 × (1 + shift). That gives **1.0442** at −5%, **1.0992** at 0% and **1.1542** at +5%.

## Locked-in hedges (the same at every S_T)

**Forward:** 3,900,000 × 1.1653 = **$4,544,670**

**Money market:**
1. Borrow EUR: 3,900,000 / (1 + 0.02825 × 91/360) = 3,900,000 / 1.0071410 = **EUR 3,872,348**
2. Convert at spot: 3,872,348 × 1.0992 = **$4,256,485**
3. Invest the USD: 4,256,485 × (1 + 0.0426 × 91/360) = 4,256,485 × 1.0107683 = **$4,302,320**

**Parity check:** F_implied = 1.0992 × 1.0107683 / 1.0071410 = **1.1032**. The parity gap is $4,544,670 − $4,302,320 = **$242,350**, which is 5.33% of USD_FWD. The tolerance is 0.05%, so this check **fails**.

**Options:**
- Put cost: 3,900,000 × 0.0147 = **$57,330**
- Put floor: 3,900,000 × (1.0992 − 0.0147) = **$4,229,550**
- Call cost: 3,900,000 × 0.0199 = **$77,610**
- Call cap (payable view): 3,900,000 × (1.0992 + 0.0199) = **$4,364,490**

## Outcomes at −5%, 0% and +5%

| | −5% (1.0442) | 0% (1.0992) | +5% (1.1542) |
|---|---:|---:|---:|
| No hedge: 3.9M × S_T | $4,072,536 | $4,286,880 | $4,501,224 |
| Forward | $4,544,670 | $4,544,670 | $4,544,670 |
| Money market | $4,302,320 | $4,302,320 | $4,302,320 |
| Put: 3.9M × max(S_T, K) − $57,330 | $4,229,550 | $4,229,550 | $4,443,894 |
| *Call, payable cost: 3.9M × min(S_T, K) + $77,610* | *$4,150,146* | *$4,364,490* | *$4,364,490* |
| **Best (receivable)** | **Forward** | **Forward** | **Forward** |

How each put figure is calculated:
- At −5%, the put is exercised: 3.9M × 1.0992 − 57,330 = $4,229,550.
- At 0%, S_T equals the strike, so the result is the same floor.
- At +5%, the put is not exercised: 4,501,224 − 57,330 = $4,443,894, exactly one premium below no hedge.

All of these figures match your memo's §5 table and the FX Lab cross-check.

**Breakevens against the forward:**
- No hedge only beats the forward if S_T is above 1.1653, which is +6.0% from spot.
- The put only beats the forward if S_T is above 1.1653 + 0.0147 = 1.1800, which is +7.4% from spot.

## Recommendation: the forward contract, but only after the bank requotes it

On these numbers the forward wins in every scenario. It locks in $4,544,670 with certainty, which is:
- $242,350 more than the money-market hedge
- $315,120 more than the put floor
- $257,790 more than leaving the receivable unhedged at today's spot

The euro would have to rise more than 6% before not hedging comes out ahead.

**The caveat:** that win comes entirely from the 1.1653 quote, which is dated 18 Oct. It sits 6.2 cents above the 1.1032 that today's interest rates imply, and the parity check fails by about 100 times its tolerance. A bank is unlikely to honor that rate today. So the recommendation is:

1. **Ask the bank to requote the 91-day forward before committing.** If it still offers anything above $4,302,320 (a rate above 1.1032), take the forward.
2. **If the requote comes back near parity (about 1.1032):** the forward and money market both lock in about $4.30M, so they tie. I'd still choose the forward, because it's one contract and doesn't tie up a EUR loan on the balance sheet. Consider the put only if management wants to keep upside from a stronger euro. It costs $57,330, and against a 1.1032 forward it only pays off above about 1.1179, which is +1.7% from spot.
3. **Don't use the call.** It caps the cost of a payable and does nothing to protect this receivable.