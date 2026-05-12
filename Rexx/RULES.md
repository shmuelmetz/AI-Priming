# Classic Rexx Rules for AI Priming

Rules for classic Rexx (as defined by ANSI X3.274-1996 and implemented
in IBM TSO/E REXX, Regina, and OBJREXX). Where ooRexx extends classic
REXX, see `../ooRexx/RULES.md`.

---

## `rc` and `result` — same as ooRexx

The `rc` / `result` distinction described in `../ooRexx/RULES.md`
applies equally to classic Rexx. `rc` is set by host commands;
`result` is set by `call` and functions. This is standard REXX,
not an ooRexx extension.

---

## `address...with` — standard Rexx

The `address...with output stem` clause is standard Rexx, available
in any implementation that supports the standard. Earlier versions
of this document incorrectly stated it was ooRexx-only.

OBJREXX 6.00 (ArcaOS) may not support it — verify before use.
TSO/E REXX uses `OUTTRAP` instead; Regina supports `address...with`
in recent versions.

---

## `~upper` — ooRexx only

The `~upper` method is ooRexx-specific. Classic Rexx uses:

```rexx
str = translate(str)   /* uppercase */
```

or the method form in ooRexx:

```rexx
str = str~translate    /* portable between classic and ooRexx */
```

---

## OBJREXX 6.00 (ArcaOS)

OBJREXX 6.00 is IBM's classic Object REXX for OS/2, shipped with
ArcaOS. It predates ooRexx and lacks some ooRexx 5.x features.
Key limitations:

- No `address...with` clause
- No `~upper` method
- RexxUtil function set differs from ooRexx RexxUtil
- `SysGetFileDateTime` may not be available

Write scripts targeting OBJREXX 6.00 using only classic Rexx
syntax and the OBJREXX RexxUtil subset.

---

## Session history

| Date | Rule added | Triggered by |
|------|-----------|--------------|
| 2026-05-03 | `rc` vs `result` applies to classic Rexx | AI incorrectly attributed to ooRexx only |
| 2026-05-03 | `address...with` is standard Rexx, not ooRexx-only | Corrected 2026-05-04 |
| 2026-05-03 | OBJREXX 6.00 limitations | ArcaOS compatibility issues |

---

## Do not overload keywords or special variable names

Same rule applies in classic Rexx as in ooRexx. Special variables
`rc`, `result`, and `sigl` must never be used as local variable names.
Rexx keywords must not be used as variable or label names.

See `../ooRexx/RULES.md` for the full keyword list.

| Date | Rule added | Triggered by |
|------|-----------|--------------|
| 2026-05-05 | Do not overload keywords or special variable names | Applies equally to classic Rexx |

---

## `signal` — not a `goto`; flushes the entire call stack

[CRITICAL]

This is a property of all Rexx variants, not ooRexx-specific.
See `../ooRexx/RULES.md` for the full treatment including wrong/correct
patterns and the `signal on` vs `call on` distinction.

Key points for classic Rexx:

- `signal` flushes the entire call stack — all active `call` frames
  and `do`/`select` blocks are gone. It is not a `goto`.
- Use `leave` to exit a loop, `return` to exit a subroutine, `iterate`
  to continue to the next loop iteration.
- `signal on` is available in classic Rexx for non-resumable condition
  handling (error, syntax, halt, novalue, notready, failure).
- `call on` (resumable condition handling) is **not** available in
  classic ANSI Rexx (X3.274-1996), TSO/E REXX, or OBJREXX 6.00
  (ArcaOS). It is an ooRexx extension. Do not generate `call on` for
  classic Rexx targets.

| Date | Rule added | Triggered by |
|------|-----------|--------------|
| 2026-05-11 | `signal` is not `goto`; flushes stack; `call on` not in classic Rexx | Applies equally to classic Rexx |

---

## Placeholder names and general conventions

See `../CONVENTIONS.md` for cross-language conventions including
`foo`/`bar`/`baz` placeholder names and the `*office` shorthand.

| Date | Rule added | Triggered by |
|------|-----------|--------------|
| 2026-05-11 | Cross-reference to CONVENTIONS.md | foo/bar/baz and *office moved to root |
