# Prompt log

## 2026-09-29
- **What I asked:** Run the Kumu setup prompt: create the folder skeleton, AGENTS.md tailored to my resume, CLAUDE.md, prompt-log.md, and .gitignore.
- **What the AI produced (Claude, Anthropic):** .gitignore, CLAUDE.md, AGENTS.md, this prompt log, and one-line README.md stubs for capabilities/, docs/briefs/, docs/decisions/, data/, and analysis/figures/.
- **What was wrong and how it was caught:** The setup prompt's step 5 would have replaced my finished README.md bio with a placeholder line. Claude noticed README.md and RESUME.md were already written and pushed, and skipped that step so my bio was kept.v 
