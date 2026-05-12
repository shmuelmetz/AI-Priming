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

## Repository update log

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-05-11 | foo/bar/baz placeholder convention | Not language-specific; moved from ooRexx/RULES.md |
| 2026-05-11 | *office shorthand | StarOffice, OpenOffice, LibreOffice family |
