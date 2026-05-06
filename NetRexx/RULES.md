# NetRexx Rules for AI Priming

Rules for NetRexx as defined by Mike Cowlishaw's NetRexx specification
and implemented by the NetRexx compiler (IBM, open source release).

## Do not overload keywords or special variable names

See `KEYWORDS.md` for the full list. NetRexx is case-sensitive for
class and method names, so `Return` and `return` are different — but
`return` as a keyword is still reserved.

## NetRexx vs Rexx differences

- NetRexx compiles to Java bytecode; types are Java types
- String handling uses Java `String` not classic Rexx string model
- `say` outputs to stdout as in Rexx
- No `address` instruction (no host command interface)
- No `interpret` instruction
- Classes and methods are first-class constructs, not procedures

## Session history

| Date | Rule added | Triggered by |
|------|-----------|--------------|
| 2026-05-05 | Initial NetRexx rule set created | AI-Priming project setup |
