# AI-Priming

Language-specific rule sets for priming AI engines (Claude, GPT, etc.)
before working in a programming language, by Shmuel (Seymour J.) Metz.

Each rule set corrects misconceptions, supplies idioms, provides a
bibliography of authoritative sources, and lists reserved words that
must not be overloaded. Rule sets are plain Markdown — upload or paste
at the start of a session and instruct the AI to treat them as ground
truth.

## Structure

```
AI-Priming/
  CONVENTIONS.md  -- Cross-language conventions (foo/bar/baz, *office, etc.)
  <Language>/
    RULES.md        -- Corrections and rules the AI failed to ingest
    IDIOMS.md       -- Canonical idioms and patterns
    BIBLIOGRAPHY.md -- Authoritative sources; AI should defer to these
    KEYWORDS.md     -- Reserved words and special variables; do not overload
```

Language directory names follow established casing conventions:
- Product names: use the vendor's established casing (e.g. `ooRexx`, `NetRexx`)
- ANSI/ISO standard names: use the standard's casing (e.g. `Rexx`)
- Initialisms: all caps (e.g. `HLASM`, `PL1`)

## Languages

| Language | Directory | Status | Notes |
|----------|-----------|--------|-------|
| ooRexx | `ooRexx/` | Active | Primary development language |
| Rexx | `Rexx/` | Active | Classic Rexx; shares many rules with ooRexx |
| NetRexx | `NetRexx/` | Active | Rexx for the Java platform |
| PL/I | `PL1/` | Active | IBM Enterprise PL/I for z/OS |
| LaTeX | `LaTeX/` | Active | arXiv papers, CTAN package, website TeX layer |

## Top-level rule files

In addition to language directories, some rule files live at the top level
because they apply across multiple languages or to non-language-specific
concerns:

| File | Scope |
|------|-------|
| `CONVENTIONS.md` | Cross-language conventions (placeholders, *office, session zip, etc.) |
| `LaTeX-RULES.md` | LaTeX/TeX projects: arXiv papers (LCS + M-Atlas), CTAN package, website |

## Usage

At the start of a Claude or GPT session:

1. Upload or paste `CONVENTIONS.md` plus `RULES.md`, `IDIOMS.md`,
   `BIBLIOGRAPHY.md`, and `KEYWORDS.md` for the relevant language.
2. Instruct the AI: *"Treat the uploaded documents as ground truth
   for this language. Where your training contradicts these rules,
   defer to the documents."*

## Adding a new language

1. Create a subdirectory using the correct casing convention above.
2. Copy an existing `RULES.md` as a template.
3. Create `KEYWORDS.md` from the language reference manual.
4. Add the language to the table above.
5. Add a session history entry to `RULES.md` noting the date and trigger.

## Tooling

Rule sets are plain Markdown. No build step is required. The `AI-Priming`
repository (`github.com/shmuelmetz/AI-Priming`) hosts the canonical copy.

## Author

Shmuel (Seymour J.) Metz <smetz3@gmu.edu>
https://mason.gmu.edu/~smetz3

## License

MIT License. See `LICENSE`.
