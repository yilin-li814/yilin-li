---
type: brief
title: "FX Hedging Executive Brief: Beef Processor EUR Receivable"
scenario: beef-processor
engagement: fx-hedging
capability: fx-hedging
date: 2026-10-23
status: committed
hypothesis: "I expect the forward and money-market hedge to lock in about $4.54M, and the put to beat the forward only if EURUSD ends above ~1.18, because interest parity links the first two and the put's $57,330 premium must be earned back first"
llm_used: "Claude (Anthropic)"
---

# FX Hedging Executive Brief: Beef Processor EUR Receivable

**Created by:** Yilin Li  
**Updated by:** Yilin Li  
**Date Created:** 2026-10-20  
**Date Updated:** 2026-10-23  
**Version:** 1.0  
**LLM Used:** Claude (Anthropic)

---

## Executive Summary (≤150 words)

We are owed EUR 3.9 million by an Italian supermarket chain, with payment due in 91 days. If the euro falls 5%, we could receive about $227,000 less than the $4.54 million it is worth today. We will compare three protections: a forward contract, a money-market hedge, and a put option. A tested model will guide our final recommendation in Stage 5, based on evidence rather than instinct.

---

## Background & Objectives

An Italian supermarket chain owes us EUR 3,900,000, which we will receive in 91 days. The euro amount is fixed, but the dollar amount we will receive is not.

At today’s forward rate of 1.1653, the receivable is worth about $4,544,670. However, if the euro falls by 5% before payment, we would receive about $227,000 less. Since a 5% exchange rate change over three months is realistic, this is a risk we need to control.

Our goal is to protect the dollar value of this receivable while keeping costs reasonable. 

---

## Methods

A forward contract locks in an exchange rate today, guaranteeing that we receive about $4,544,670 in 91 days. Its main advantage is certainty, but the downside is that we cannot benefit if the euro strengthens.

A money-market hedge involves borrowing euros now, converting them into dollars, and investing the dollars until the payment is due. It gives the same certainty as a forward, but it uses our borrowing capacity and involves more steps to set up.

A put option costs a $57,330 premium and sets a minimum dollar value based on today’s exchange rate. Think of it as insurance: we pay upfront to protect ourselves if the euro falls, but we can still benefit if it strengthens. The downside is the premium, which we pay regardless of what happens to the exchange rate.

The model will compare all three strategies across a range of euro exchange rates to identify the best choice.

---

## What I Am Assuming

We assume the Italian supermarket chain will pay the full EUR 3,900,000 in 91 days, and our bank can provide forward contracts and put options. We also have enough credit to borrow euros for the money-market hedge, and the quoted forward rate of 1.1653 and option premium of $0.0147 per euro are realistic estimates. The assumption I would test with more time is payment timing, since supermarket chains sometimes pay late and a forward is tied to a fixed date.

---

## Hypothesis

I expect the forward and the money-market hedge to lock in nearly the same amount, about $4.54 million, because interest rates and forward rates are linked. I expect the put to beat the forward only if the euro ends above about 1.18, because its $57,330 premium has to be earned back first.

---

## How I Would Know I Was Wrong

I would be wrong if the model shows the money-market hedge and the forward differing by much more than transaction costs, or if the put beats the forward at euro rates below 1.18.

---

## Limitations & Next Steps

Limitations: The forward rate and option premium are indicative scenario quotes, not live bank prices. Spot and interest rates will be collected at market close on the day Stage 2 begins.

Next Steps: In Stage 2, I will design the model’s tabs, inputs, formulas, and checks on paper. In Stage 3, I will use AI to build the workbook, then audit and fix it. In Stage 4, I will replace placeholders with live, sourced, timestamped market data. In Stage 5, I will independently validate the results and present my final recommendation in a decision memo.

This process ensures the final decision rests on evidence, not a gut call.

---

## References

- Stauffer, A. (2026, October 18). *FIN-321: Your FX hedging scenario* [Email].
- Stauffer, A. (2026). *FX Hedging project, Stage 1: Executive Brief* [Course materials]. University of Hawaiʻi.
- Stauffer, A. (2026). *FX Hedging Lab* [Course tool]. University of Hawaiʻi.