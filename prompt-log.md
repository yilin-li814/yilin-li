# Prompt log

## 2026-09-29
- **What I asked:** Run the Kumu setup prompt: create the folder skeleton, AGENTS.md tailored to my resume, CLAUDE.md, prompt-log.md, and .gitignore.
- **What the AI produced (Claude, Anthropic):** .gitignore, CLAUDE.md, AGENTS.md, this prompt log, and one-line README.md stubs for capabilities/, docs/briefs/, docs/decisions/, data/, and analysis/figures/.
- **What was wrong and how it was caught:** The setup prompt's step 5 would have replaced my finished README.md bio with a placeholder line. Claude noticed README.md and RESUME.md were already written and pushed, and skipped that step so my bio was kept.


## 2026-11-05: FX hedging Stage 2, spec review

**Prompt:** Asked Claude to review my full spec.md for gaps, not to confirm it was good.

**Gap found → fix:**
- Before: §7 rule 9 said "Forward and Money market are identical in every grid row."
- Problem: with the placeholder inputs, they differ by the $152,191 parity gap, so a correct workbook would fail this rule.
- After: "Forward and Money market are each constant across all 11 grid rows."

**Other fixes accepted:** corrected the §6 put description below the strike; made "Best overall" name its four columns; clarified §7 rule 5; added all 11 check-figure rows.

**Rejected:** none.

## 2026-11-13: FX hedging Stage 3, AI build + audit

**Prompt:** Gave Claude my committed spec (v0.3) and asked it to build capabilities/fx-hedging/model.xlsx exactly as specified, with every build-contract requirement binding.

**Before the build:** compared the spec with the Stage 3 build contract and found four gaps (Cover provenance block, four-color legend, call premium and payoff table, Checks tab); fixed them in the spec (v0.3) before generating.

**Audit:** 6 findings in analysis/2026-11-13-beef-processor-build-audit-analysis.md. Parity check fails by $152,191 and is reported, not forced. Found a spec defect (§7 rule 1 too strict for grid rows) and fixed the spec (v0.4).

## 2026-11-19: FX hedging Stage 4, market data + population

**Prompt:** Asked Claude to retrieve live market data as of 19 Nov 2026, populate model.xlsx through the named input cells only, and write the market-data memo.

**What the AI did:** pulled spot (ECB reference rate 1.0992), the 3-month T-bill yield (4.26%) and the 3-month Euribor fixing (2.825%); computed the CIP-implied forward (1.1032) and the gap to the bank quote ($242,350); confirmed zero formulas changed versus the Stage 3 workbook; replaced the hard-coded check figures (Stage 3 audit Finding 6).

**My decisions:** switched from overnight policy rates to 3-month rates to match the 91-day horizon; kept the bank's 1.1653 forward and the scenario premiums, as Stage 4 requires; left the parity check as FAIL; cross-checked the outputs in the FX Hedging Lab myself.
