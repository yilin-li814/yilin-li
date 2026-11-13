# Build Audit: beef-processor FX hedge workbook

**Author:** Yilin Li  
**Date:** 2026-11-13  
**Workbook:** `capabilities/fx-hedging/model.xlsx`  
**Built from:** `capabilities/fx-hedging/spec.md` v0.3  
**Built with:** Claude (Anthropic), from the committed spec. I reviewed the workbook in Excel (view-only, no editing license); the recalculation test in Finding 3 was run by Claude on a test copy at my request.

---

## Summary

The workbook meets all seven build-contract items. The `Checks` tab reports 25 PASS and 1 FAIL. The single FAIL is the parity check, which reflects the indicative inputs, not a formula error (Finding 4). One spec defect was found and fixed (Finding 5).

## Findings

**Finding 1: named ranges (checked, confirmed).** Checked Formulas → Name Manager. All ten contract names exist and point to the right cells on `Inputs` (B5–B14). The build also added names for assumptions (`DAY_BASIS`, `GRID_STEP`, `PARITY_TOL`), outputs (`USD_FWD`, `USD_MM`, `PARITY_GAP`, `PUT_COST`, `CALL_COST`, `USD_PUT_FLOOR`), and the sensitivity columns (`SENS_*`). No fix needed.

**Finding 2: formulas, not pasted values (checked, confirmed).** All 237 calculated cells are formulas; the only typed numbers are the yellow inputs, the blue assumptions, and the blue expected values on `Checks`. The forward cell is `=FC_AMT*F0_in`, not the typed result $4,544,670. The money-market hedge is three separate cells (`MM_BORROW`, `MM_USD_NOW`, `USD_MM`), not one nested formula. No fix needed.

**Finding 3: recalculation when inputs change (checked, confirmed).** In a test copy, `S0_in` was changed from 1.1224 to 1.15. The sensitivity grid moved from 1.0663–1.1785 to 1.0925–1.2075, and the 0% row's no-hedge proceeds changed to $4,485,000 (= 3,900,000 × 1.15). The grid is formula-driven, not hand-typed. The original file was not changed.

**Finding 4: parity check fails, reported, not forced.** `PARITY_GAP` = $152,191 (3.35% of `USD_FWD`), against a tolerance of 0.05%. `F_IMPLIED` = 1.1263 versus the quoted `F0_in` = 1.1653. Every other check figure matches the FX Hedging Lab within $1. Cause: the scenario's indicative forward is inconsistent with the 2026-11-05 spot and interest rates; this is an input issue documented in spec §4, not a formula error, since `MM_BORROW`, `MM_USD_NOW` and `USD_MM` each match the Lab. Action: left as FAIL; to be re-tested with a live forward at Stage 4.

**Finding 5: spec defect in §7 rule 1 (found, fixed in spec).** Rule 1 said "No formula uses a cell address." The build cannot meet this inside the 11-row grids: each row's `S_T` must reference that row's shift cell (e.g. `=S0_in*(1+A6)`), and the "Best overall" label uses the header range. Every input and output is referenced by name. The rule, not the workbook, was wrong, so I rewrote rule 1 to allow same-row references inside the grids (spec v0.4).

**Finding 6: check figures are tied to the placeholder inputs (noted for Stage 4).** The expected values on `Checks` are the spec §7 figures for the 2026-11-05 inputs. When Stage 4 replaces the inputs, the check-figure rows will fail until the expected values are updated; rules 3–9 stay valid for any inputs. Action: update the expected values at Stage 4.