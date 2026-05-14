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
