---
type: memo
title: "Hedge Decision: EUR 3.9M Receivable from an Italian Supermarket Chain"
scenario: beef-processor
date: 2026-11-26
llm_used: "Claude (Anthropic): asked me questions, critiqued my answers, and organized my dictated answers into sections A–E; the recommendation and reasoning are mine"
---

# Hedge Decision: EUR 3.9M Receivable

**Created by:** Yilin Li  
**Updated by:** Yilin Li  
**Date Created:** 2026-11-26  
**Date Updated:** 2026-11-26  
**Version:** 1.0  
**LLM Used:** Claude (Anthropic): questioned and critiqued my reasoning, then organized my dictated answers into this structure  
**To:** CFO  
**Market data:** 19 Nov 2026 (`data/2026-11-19-beef-processor-market-data-memo.md`)

---

## Executive Summary (≤150 words)

I recommend hedging the receivable, not leaving it open, and asking the bank for an updated forward quote before choosing the instrument. If the updated forward rate is at or above about 1.1032, we should use the forward contract; if it is below that, we should use the money-market hedge, which guarantees $4,302,320. Either way, we lock in roughly $4.3 million and remove the risk of the euro losing value. The current forward quote of 1.1653 would give $4,544,670, but it is a month old and $242,350 above the fair forward rate, so I would not rely on it. The put option keeps the upside but its $57,330 premium is expensive for the protection it gives. Our business is processing and selling beef, not profiting from exchange-rate movements, so predictable cash flow matters more than a possible gain.

---

## A. Exposure summary

An Italian supermarket chain owes us EUR 3,900,000, payable in 91 days. At the 19 November spot rate of 1.0992, that is worth $4,286,880. The euro amount is fixed, but the dollar amount we will receive is not. The euro has already weakened: spot fell from 1.1224 on 5 November to 1.0992 on 19 November.

## B. Hedge outcomes

- **Money-market hedge: $4,302,320, guaranteed.** It eliminates the risk of the euro losing value. To build it, the company borrows EUR 3,872,348 for 91 days, converts it at spot and invests the dollars.
- **Forward contract: $4,544,670 at the current quote.** It offers a higher amount than the money market, but the quote is already a month old, so I would not rely on that number without getting an updated quote.
- **Put option: floor of $4,229,550.** It is a reasonable choice because it allows the company to benefit if the euro appreciates, but the $57,330 premium is expensive. The floor is $72,770 below what the money-market hedge guarantees, and the put only comes out ahead of the money market if the euro rises more than about 1.7% (to roughly 1.1179) in three months.
- **No hedge: $4,286,880 at today's spot.** I would not recommend leaving the receivable unhedged, because a 5% decrease in the euro (to 1.0442) would cost the company around $214,000.
- **Call option.** A call gains when the euro rises, which already helps a receivable, so it does not hedge this exposure.

## C. Sensitivity interpretation

**If the euro weakens**, the locked-in hedges protect us. At −5% (1.0442), no hedge falls to $4,072,536, while the money market still delivers $4,302,320.

**If the euro strengthens**, locking in has a cost. At +5% (1.1542), staying unhedged would bring $4,501,224, about $199,000 more than the money-market hedge. I think giving up that potential gain is acceptable. I see it as an opportunity cost of reducing risk: although the euro could increase by 5%, it could also decrease and cause a significant loss.

## D. Recommendation

**Hedge the full EUR 3,900,000. Ask the bank for an updated 91-day forward quote, then:**

- **If the forward rate is at or above about 1.1032, use the forward.** At 1.1032 the forward and the money market guarantee almost the same dollar amount, so this rate is the break-even between them. The forward is simpler, because the company does not need to borrow euros, convert them into dollars and invest the proceeds. It also requires fewer transactions and less administrative work. As long as the updated forward rate is competitive and there are no significant additional fees, the forward is the more practical choice.
- **If the forward rate is below 1.1032, use the money-market hedge,** which guarantees $4,302,320.

**The bank's quote and the fair forward do not agree.**

| | Rate | USD on EUR 3.9M |
|---|---:|---:|
| Bank quote (18 Oct 2026) | 1.1653 | $4,544,670 |
| CIP-implied (fair) forward, 19 Nov 2026 | 1.1032 | $4,302,320 |
| **Gap** | **0.0621** | **$242,350** |

I think the main reason for the $242,350 difference is that the bank's forward quote is outdated. The quote of 1.1653 was provided on 18 October, but market conditions have changed since then. The spot rate decreased from 1.1224 on 5 November to 1.0992 on 19 November, showing that the euro has weakened against the U.S. dollar. The fair forward rate of 1.1032 reflects more recent market conditions and the interest-rate difference between the two currencies, while the bank's original quote remained unchanged. The bank may also include a spread or profit margin in its quote, but I do not think that alone would explain such a large difference. Without knowing the exact market conditions on 18 October, we cannot fully explain the gap.

**Does the gap change the recommendation?** It changes which instrument we use, not whether we hedge. Because the quote is unreliable, I base the plan on the fair value of about $4.3 million and let the updated quote decide between the forward and the money market.

## E. Executive justification

- **Cash-flow stability.** The company is in the business of processing and selling beef, not making profits from foreign-exchange movements. A predictable cash flow is more important because the company needs to pay for inventory, employee wages and other operating expenses.
- **Budget certainty.** By locking in about $4.3 million, the CFO can plan the company's budget with more confidence.
- **Liquidity and simplicity.** A forward at a fair rate needs no borrowing. The money-market hedge reaches the same value but requires a EUR 3.87 million loan for 91 days, so it is the fallback if the forward quote is not competitive.
- **Optionality and premium cost.** The put keeps the upside, but the $57,330 premium is expensive for a floor below what the locked-in hedges guarantee.
- **Overall.** In my opinion, financial stability is more important than the possibility of earning extra money from an uncertain exchange rate.

**Next step:** request an updated 91-day EURUSD forward quote on EUR 3,900,000 from the bank, and choose between the forward and the money-market hedge using the rule in Section D.
