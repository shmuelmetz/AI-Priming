# AI-Priming

Language-specific rule sets for priming AI engines (e.g. Claude, GPT)
before working with a programming language. Each rule set corrects
misconceptions, supplies idioms, and provides a bibliography of
authoritative sources that the AI should treat as ground truth.

Rule sets are language-specific and tracked independently from other
projects. They are intended to be uploaded or pasted at the start of
a session when working in that language.

## Structure

Each language has its own subdirectory:

```
AI-Priming/
  ooRexx/
    RULES.md        -- Corrections and rules the AI failed to ingest
    IDIOMS.md       -- Canonical ooRexx idioms and patterns
    BIBLIOGRAPHY.md -- Authoritative sources; AI should defer to these
  Rexx/
    RULES.md
    IDIOMS.md
    BIBLIOGRAPHY.md
  ...
```

## Tooling

Rule sets are plain Markdown. No build step is required. To use:

1. Open the relevant `RULES.md`, `IDIOMS.md`, and `BIBLIOGRAPHY.md`
2. Paste or upload them at the start of a Claude/GPT session
3. Instruct the AI: "Treat the uploaded documents as ground truth
   for this language. If your training contradicts these rules,
   defer to the documents."

The `tools` repo `build.rex` is not involved -- these are documentation
artifacts, not scripts.

## Adding a new language

1. Create a subdirectory named after the language (lowercase, hyphenated)
2. Copy `ooRexx/RULES.md` as a template
3. Fill in language-specific content
4. Add the language to the table below

## Languages

| Language | Status | Notes |
|----------|--------|-------|
| ooRexx   | Active | Primary development language for this project |
| Rexx   | Active | Classic Rexx; shares many rules with ooRexx |

## Author

Shmuel (Seymour J.) Metz <smetz3@gmu.edu>
https://mason.gmu.edu/~smetz3
