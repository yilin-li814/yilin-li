# Prompt log

## 2026-09-29
- **What I asked:** Run the Kumu setup prompt: create the folder skeleton, AGENTS.md tailored to my resume, CLAUDE.md, prompt-log.md, and .gitignore.
- **What the AI produced (Claude, Anthropic):** .gitignore, CLAUDE.md, AGENTS.md, this prompt log, and one-line README.md stubs for capabilities/, docs/briefs/, docs/decisions/, data/, and analysis/figures/.
- **What was wrong and how it was caught:** The setup prompt's step 5 would have replaced my finished README.md bio with a placeholder line. Claude noticed README.md and RESUME.md were already written and pushed, and skipped that step so my bio was kept.v 


## 2026-11-05: FX hedging Stage 2, spec review

**Prompt:** Asked Claude to review my full spec.md for gaps, not to confirm it was good.

**Gap found → fix:**
- Before: §7 rule 9 said "Forward and Money market are identical in every grid row."
- Problem: with the placeholder inputs, they differ by the $152,191 parity gap, so a correct workbook would fail this rule.
- After: "Forward and Money market are each constant across all 11 grid rows."

**Other fixes accepted:** corrected the §6 put description below the strike; made "Best overall" name its four columns; clarified §7 rule 5; added all 11 check-figure rows.

**Rejected:** none.