# AI-Priming — General Conventions

These conventions apply across all languages in this repository.
They are not language-specific and should be included alongside any
language-specific rule set when priming an AI engine.

---

## Placeholder names: foo, bar, baz

[IMPORTANT]

`foo`, `bar`, and `baz` are conventional metasyntactic placeholder
names used in code examples across all programming languages. They
signal "insert your actual name here" and are universally understood
by programmers.

Use `foo`, `bar`, and `baz` for throwaway or illustrative names in
examples rather than inventing plausible-sounding but potentially
misleading names. Reserve meaningful names for canonical task examples
(see each language's IDIOMS.md).

```
foo = some value or variable
bar = another value or variable
baz = a third value or variable, if needed
```

This convention originates in the hacker/MIT tradition and is
documented in the Jargon File. It is not ooRexx-specific, not
Rexx-specific, and not tied to any particular paradigm.

---

## Generic product shorthand: *office

[IMPORTANT]

"*office" uses the same metasyntactic wildcard convention as `foo`:
the `*` stands for any prefix, denoting any member of the `?office`
family of compatible office suites. The family name derives from
StarOffice and its successors, which share the ODT file format.

Known members of the family (not exhaustive):

- **StarOffice** — the original proprietary suite from Star Division,
  later acquired by Sun Microsystems; discontinued. ODT did not exist
  in the StarOffice epoch; it was introduced with OpenOffice.
- **OpenOffice** (Apache OpenOffice, openoffice.org) — introduced the
  ODT format; the open-source successor to StarOffice. Used on ArcaOS,
  replacing the StarOffice originally installed for OS/2.
- **LibreOffice** (libreoffice.org) — the primary community fork;
  current standard on Windows and most Linux systems
- **EuroOffice** — a LibreOffice derivative; exists but not used in
  this project
- Others may exist; the `*` wildcard covers them all

When these notes say "*office" without qualification, any compatible
member of the `?office` family is meant.

---

## Numbering in responses

Numbering used by the user in a response to a Claude question is local
to that response. Claude must not echo that numbering back as if it
were a directive for labelling, heading structure, or rule identifiers.
Claude generates its own numbering in a fashion appropriate to the
content and context.

---

## Session zip: primary file delivery vehicle

[IMPORTANT]

The session zip (`session-YYYY-MM-DD.zip`) is the canonical delivery
vehicle for all files exchanged between Claude and the author. The
script locates the most recent `session-*.zip` in `Downloads\`
automatically by date pattern; no hardcoded filename is needed.

**Rules for Claude:**

- Do NOT request extraneous downloads for files that can travel inside
  the session zip. If a file needs to reach the Windows machine, put
  it in the zip.
- Do NOT hardcode a session zip filename (e.g., `session-2026-05-02.zip`)
  in scripts or instructions. The zip is always found by the pattern
  `session-*.zip`, sorted by date descending, so the most recent one
  is used automatically.
- At the start of each session, ingest the uploaded session zip in
  full before taking any action. The zip is the ground truth for the
  current state of the project. Do not rely on memory of previous
  sessions; always re-read from the zip.
- If a session is interrupted (capacity limit, timeout, etc.), the
  user will upload a new session zip at the start of the resumption.
  Ingest it and resume from the state it represents.

**Files that travel in the session zip:**

- Updated script (`session-YYYY-MM-DD.rex`)
- AI-Priming rule sets (`CONVENTIONS.md`, `ooRexx-RULES.md`,
  `Rexx-RULES.md`, `AI-Priming-README.md`)
- Session notes (`SESSION-NOTES.md`)
- Web source files that were edited this session
- TeX sources that were edited this session
- The GitHub profile approved draft (`github-profile-approved.md`)
  when ready to deploy

**Files that do NOT travel in the zip (too large or binary):**

- High-resolution images (downloaded by the script from Wikimedia
  or other sources via `Invoke-WebRequest`)
- Build artifacts (PDFs, PNGs)
- Install packages (Poppler, MiKTeX, etc.)

---

## Indentation style

[IMPORTANT]

Use a consistent indentation style in all generated code, regardless of
language. Rules:

- **Indent unit**: 4 spaces. Never tabs.
- **Continuation lines**: align with the opening delimiter or indent 8
  spaces (2 units) from the statement start — whichever is more readable.
- **Nested blocks**: each level adds 4 spaces.
- **Comments**: align with the code they annotate.
- **Language overrides**: if a language community has a strong contrary
  convention (e.g. 2-space JavaScript, 8-space kernel C), note it in the
  language-specific RULES file; CONVENTIONS.md default applies otherwise.

ooRexx/Rexx specifics are in `ooRexx-RULES.md` and `Rexx-RULES.md`.

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-05-13 | Indentation style | Explicit instruction: consistent indent in generated code |

## Upload tracking

[IMPORTANT]

Claude must maintain a permanent record of all files uploaded during a
session and carry that record forward across the session. Before claiming
that a file was not uploaded or asking the user to re-upload something,
Claude must check the upload record first.

**Rules:**

- At the start of each session, reconstruct the upload record from the
  session zip's SESSION-NOTES.md upload log.
- Every upload received during the session must be appended to
  SESSION-NOTES.md under "## Uploads YYYY-MM-DD" immediately upon
  receipt, before any other processing.
- Before saying "please upload X" or "X was not uploaded", check the
  current session's upload record and all prior session upload logs in
  SESSION-NOTES.md.
- If a file was uploaded but its content was pruned or not retained,
  say "not retained" or "pruned from [source]" — not "not uploaded".
- If a file was genuinely not uploaded in any session, say "not yet
  uploaded" and request it.

---

## Repository update log

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-05-11 | foo/bar/baz placeholder convention | Not language-specific; moved from ooRexx/RULES.md |
| 2026-05-11 | *office shorthand | StarOffice, OpenOffice, LibreOffice family |
| 2026-05-13 | Session zip delivery rule | Capacity-limit hang; need explicit rule for zip-first workflow |

## Capacity monitoring and adaptive checkpointing

[IMPORTANT]

Claude must monitor conversation length and take more frequent
checkpoints as capacity limits approach.

**Rules:**

- Take a checkpoint (rebuild session zip, present it) after every
  substantive block of work, not just at explicit request.
- As the conversation grows long, increase checkpoint frequency:
  - Normal: checkpoint every 3-5 exchanges or major task
  - Long conversation (>50 exchanges): checkpoint every 2-3 exchanges
  - Near capacity: checkpoint after every exchange
- Before any operation that could trigger a capacity hang (large file
  parsing, multiple file extractions, long script edits), take a
  checkpoint first.
- If a capacity limit hang occurs mid-task, the user will upload the
  last session zip at resumption. The zip must therefore always
  reflect the last known good state.
- When in doubt, checkpoint. A redundant checkpoint costs seconds;
  a lost session costs the full rework.

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-05-14 | Capacity monitoring and adaptive checkpointing | Capacity limit hang earlier in session |

## General AI priming rules (from AIprofile.txt)

### Hallucination avoidance

When generating code, use available language references, OS documentation,
and reference manuals to verify syntax. Do not generate syntax from memory
alone when a reference is available.

When asked to justify a claim, cite accessible sources if they exist, or
state explicitly that the claim is not based on accessible sources.

### Corrections and requirements

- Do not drop one requirement to satisfy another without permission.
- Retain corrections: do not regenerate something, or a synonym, that
  was earlier flagged as incorrect.
- Query about and correct apparent typos rather than silently accepting
  them.

### Name normalization

Normalize the typography of the following names consistently. In LaTeX
sources use the macros shown; in HTML and plain text use the forms shown:

| Name | LaTeX | HTML | Plain text |
|------|-------|------|------------|
| TeX | `\TeX` | T<sub>E</sub>X | TeX |
| LaTeX | `\LaTeX` | L<sup>A</sup>T<sub>E</sub>X | LaTeX |
| LaTeX 2ε | `\LaTeXe` | L<sup>A</sup>T<sub>E</sub>X 2ε | LaTeX 2e |
| pdfTeX | `pdf\TeX` | pdfT<sub>E</sub>X | pdfTeX |
| XeTeX | `\XeTeX` | X<sub>Ǝ</sub>T<sub>E</sub>X | XeTeX |
| XeLaTeX | `\XeLaTeX` | X<sub>Ǝ</sub>L<sup>A</sup>T<sub>E</sub>X | XeLaTeX |
| LuaTeX | `Lua\TeX` | LuaT<sub>E</sub>X | LuaTeX |
| BibTeX | `Bib\TeX` | BibT<sub>E</sub>X | BibTeX |
| Wikimedia | — | Wiki**m**edia | Wikimedia |

- **REXX** (all caps) on z/OS and z/VM platforms
- **Rexx** (mixed case) on other platforms (desktop, OS/2, Linux, Windows)
- **ooRexx** for Object-Oriented Rexx extensions
- When code requires more than classic Rexx, identify the specific
  implementation (ooRexx, Regina, TSO/E REXX, NetRexx, etc.)

### PDF page fragments

When linking to a specific page in a PDF using the `#page=` fragment,
use the **physical page number** (1-based position of the page in the
file), not the page number rendered on the page. These differ whenever
the document has front matter (title pages, tables of contents) that
use Roman numerals or are unnumbered.

```
https://example.org/paper.pdf#page=12   ← physical page 12 in the file
```

### TeX name rendering in markup languages

In LaTeX sources, use the standard macros for typeset names:
`\TeX`, `\LaTeX`, `\LaTeXe`, `\BibTeX`, `\XeTeX`, `\XeLaTeX`,
`\LuaTeX`, `\pdfTeX`, etc.

In wikitext (Wikipedia and Wikimedia projects), use the
`{{TeX wordmark|name}}` template with the wordmark name:

| Wordmark name | Renders as |
|---------------|-----------|
| `tex` | TeX |
| `latex` | LaTeX |
| `latex2e` | LaTeX2ε |
| `latex3` | LaTeX3 |
| `bibtex` | BibTeX |
| `pdftex` | pdfTeX |
| `xetex` | XeTeX |
| `xelatex` | XeLaTeX |
| `luatex` | LuaTeX |
| `lualatex` | LuaLaTeX |
| `context` | ConTeXt |
| `lyx` | LyX |
| `musixtex` | MusiXTeX |
| `revtex` | REVTeX |
| `tetex` | teTeX |
| `texmacs` | TeXmacs |
| `xymtex` | XϒMTeX |

Do not use `{{TeX}}`, `{{LaTeX}}` etc. as standalone templates for
these names — the correct template is `{{TeX wordmark|name}}`.
Do not invent wordmark names not listed above.

### Markup and links

**Do not drop links when ingesting.**
When processing a marked-up document, retain the semantic content of
all existing links — the target URL and the anchor text — in the
output. Render them in the syntax appropriate to the output format:
- Input HTML `<a href="url">text</a>` → output Markdown `[text](url)`
- Input HTML `<a href="url">text</a>` → output wikitext `[url text]`
- Input LaTeX `\href{url}{text}` → output HTML `<a href="url">text</a>`
- and so on.

Do not alter link targets, anchor text, or citation keys unless
explicitly asked.

**Do not strip schemes from URLs.**
Preserve the scheme (`https://`, `http://`, `ftp://`, etc.) in all
URLs. Do not output bare hostnames or paths when a full URL with scheme
was present in the source.

### Author information

| Field | Value |
|-------|-------|
| Full name | Seymour Jerome Metz |
| Email | smetz3@gmu.edu |
| Website | http://mason.gmu.edu |
| GitHub | https://github.com/shmuelmetz |
| LinkedIn | https://www.linkedin.com/in/seymour-metz-a89915152 |
| ORCID | https://orcid.org/0000-0002-6358-8257 |
| Hebrew name | שמואל בן ל״ביש |

Name handling:
- Replace middle names with initials where appropriate.
- The Hebrew patronymic may be dropped.
- Do not add alternate typography or Yiddish equivalents for Hebrew
  names without permission.
- Render contact information in a style appropriate to the document
  context.

### Time estimates

Provide time estimates for tasks when asked or when the task is
non-trivial.

### Wikipedia sourcing (when contributing to Wikipedia)

Evaluate sources for WP:RS (reliable sources) and provide citations in
WP:CS2 form:
- Set `=` signs off on both sides with spaces and align them.
- Place `title=` and `id=` parameters first; place `publisher=` and
  `access-date=` last.
- Provide `author-link=` parameters if an author article exists on wiki.
- Wikilink publisher if a Wikipedia page exists.
- Attempt to generate `section-url=` and `url=` pointing to sources
  not behind pay walls or subscription walls.

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-05-14 | AIprofile.txt rules folded in | TSM/AIprofile.txt parsed from LCS.zip |
