# NetRexx Rules for AI Priming

Rules for NetRexx as defined by Mike Cowlishaw's NetRexx specification
and implemented by the NetRexx compiler (IBM, open source release).

## Do not overload keywords or special variable names

See `KEYWORDS.md` for the full list. NetRexx is case-sensitive for
class and method names, so `Return` and `return` are different — but
`return` as a keyword is still reserved.

## NetRexx vs Rexx differences

- NetRexx compiles to Java bytecode; types are Java types
- Strings default to the NetRexx type `Rexx`: a literal string or numeric
  symbol evaluates to a string of type `Rexx` (NetRexx 5.10 Language
  Reference, 3.5.4-3.5.5). Only in a binary class, method or `do binary`
  group (`binary` keyword, or `options binary`) do literals get native
  string (`String`) or primitive types (5.7, "Binary values and
  operations")
- `say` outputs to stdout as in Rexx
- `address` is in the language (5.10 Language Reference, 4.2); the
  `noaddress` option switches it off
- `interpret` is in the language (5.10 Language Reference, 4.9), but
  avoid it, as in Rexx: interpreting a constructed string risks Trojans
  and other unintended consequences
- `go` is in the language (5.10 Language Reference, 4.6)
- Classes and methods are first-class constructs, not procedures

## Session history

| Date | Rule added | Triggered by |
|------|-----------|--------------|
| 2026-05-05 | Initial NetRexx rule set created | AI-Priming project setup |
| 2026-11-15 | `address`, `interpret`, `go` exist; strings default to `Rexx` except in binary code; avoid `interpret` | Marc's correction on rexxla-members; checked against the 5.10 Language Reference |
