# LaTeX Rules for AI Priming

Rules and constraints for AI engines working on LaTeX and TeX projects
for Seymour J. Metz. Treat this document as ground truth.

---

## Project structure

Three independent LaTeX projects exist. Their macro layers must not
cross-contaminate:

| Project | Scope | Macro layer |
|---------|-------|-------------|
| arXiv papers (LCS + M-Atlas) | Papers 1801.05775 and 1906.11690 | `shared-macros.sty` (arXiv-safe) |
| CTAN package | `latex-semantic-markup` | `latex-semantic-markup.dtx` / `.sty` |
| Personal website | HTML/XHTML/TeX pages | `site-macros.sty` (website-only) |

Do not use arXiv macros in the CTAN package, website macros in papers,
or CTAN package macros in the website.

---

## Shared macro constraint: LCS + M-Atlas

The two arXiv papers share a single common macro file (`shared-macros.sty`
or equivalent). This is a **hard constraint**:

- Any macro that appears in both LCS and M-Atlas **must have identical
  semantics** in both papers.
- Where current definitions differ between the two papers, **adjust the
  mathematics** to impose consistency. Do not create paper-specific
  variants of shared macros.
- If a macro is needed in only one paper, it may still live in the shared
  file; it need not be used in both. But if it is used in both, it must
  mean the same thing in both.
- Macro names must reflect **mathematical meaning**, not formatting.

---

## arXiv constraints

- Use only packages supported by arXiv. Do not introduce packages that
  are not available in the arXiv TeX environment.
- Use XeTeX for Unicode input where needed; UTF-8 Hebrew is permitted.
  All other non-ASCII characters must use LaTeX macros.
- Do not use `\usepackage` for packages that arXiv does not support.

---

## CTAN package (latex-semantic-markup)

- Package internals use expl3 naming: `\lsm_...:` prefix.
- User-level macros use semantic prefixes: `\sem...`, `\chart...`,
  `\atlas...`, `\coord...`, `\morph...`, `\obj...`, `\cat...`
- Every user-level macro must be documented in the dtx.
- No paper-specific names in the CTAN package.
- No website macro names in the CTAN layer.

---

## Global reasoning boundaries

- **arXiv**: global reasoning applies across LCS + M-Atlas only.
- **CTAN**: global reasoning applies across the semantic macro package only.
- **Website**: global reasoning applies across HTML/TeX/XHTML pages only.

Do not apply global reasoning across boundaries (e.g., do not rename a
CTAN macro because of a conflict with a website macro).

---

## Repository update log

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-05-13 | Initial creation | Explicit statement that LCS + M-Atlas macros must be normalized into a common file |
