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

## Stylized names: check for a dedicated macro before hand-typing one

Many TeX-family and organization/product names have idiosyncratic
capitalization or kerning that plain text can't reproduce correctly
-- `\LaTeX`, `\TeX`, `\BibTeX`, and the `metalogo` package's
`\XeLaTeX`/`\LuaLaTeX`/`\pdfLaTeX` all exist because hand-typing
"LaTeX" as plain text loses the intended kerning between the
lowered "a" and raised "T". Before typesetting a stylized name as
plain text in any LaTeX source (paper, CTAN package doc, or the
website's TeX shirt sources), check whether a dedicated macro
already exists for it (`metalogo`, the name's own project if it
ships one, or a macro already defined locally) rather than assuming
plain text is correct by default.

**RexxLA** (the Rexx Language Association) was checked directly
2026-08-27 while fixing dead newsletter links on the website: no
package or macro defines a stylized `\RexxLA`-type command, and it
doesn't currently appear in any LaTeX source in this project (only
in HTML, where it's typed as plain text matching the organization's
own styling — "RexxLA", not "REXXLA" or "REXLA", both of which were
found as errors on the website and corrected). If it's ever needed
in a paper or shirt source, this repo has no existing convention for
it yet — define one locally (e.g. in `site-macros.sty`) rather than
inventing an ad hoc per-file spelling.

---

## Repository update log

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-05-13 | Initial creation | Explicit statement that LCS + M-Atlas macros must be normalized into a common file |
| 2026-08-27 | Stylized names: check for a dedicated macro first | User: "note that there are LaTeX commands for stylized names," while fixing an org-name spelling inconsistency (REXXLA/REXLA/RexxLA) on the website |
