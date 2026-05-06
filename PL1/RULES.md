# PL/I Rules for AI Priming

Rules for PL/I as implemented by IBM Enterprise PL/I for z/OS.

## Do not overload keywords or restricted identifiers

See `KEYWORDS.md` for the full list. PL/I is unusually permissive
about using keywords as identifiers, but doing so produces confusing
code and occasional parser failures. Avoid all reserved words as
identifiers.

## Case insensitivity

PL/I is case-insensitive. `DECLARE`, `declare`, and `Declare` are
identical. Convention is uppercase for keywords and attributes,
mixed case or uppercase for identifiers.

## Declare before use

All variables must be declared before use (unlike Rexx). Undeclared
variables are assigned the `FIXED BINARY(15)` default under most
compiler options — a common source of bugs.

```pli
DCL myVar FIXED BIN(31);
DCL myStr CHAR(80) VARYING;
DCL myPtr POINTER;
```

## Session history

| Date | Rule added | Triggered by |
|------|-----------|--------------|
| 2026-05-05 | Initial PL/I rule set created | AI-Priming project setup |
