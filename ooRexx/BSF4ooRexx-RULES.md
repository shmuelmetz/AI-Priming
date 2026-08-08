# BSF4ooRexx-RULES.md

Rules and cautions specific to BSF4ooRexx (the ooRexx-to-Java bridge).
No single canonical, versioned reference manual is publicly indexed for
BSF4ooRexx; the closest available authoritative source used here is the
tutorial deck by its author, Rony Flatscher, presented at the
International Rexx Symposium (2024 edition consulted). That absence of
a single canonical reference is itself worth noting when deciding how
much confidence to place in any given construct below.

Entries are graded by confidence:

- **Established**: confirmed directly against the tutorial's own
  worked examples.
- **Unverified**: inferred from the tutorial, from general Java
  knowledge, or by analogy; not yet exercised against a real
  BSF4ooRexx installation.
- **Tested but unverified**: observed to work (or fail) in an actual
  run on this machine, but not yet cross-checked against authoritative
  documentation, or only exercised in one narrow case — treat as
  provisional, not as an established convention, until confirmed
  across more than one run/case.

## Established (confirmed against the tutorial)

- `::requires "BSF.CLS"` must be the LAST line of the file. This is a
  core ooRexx directive/prolog rule, not a BSF4ooRexx quirk: all loose
  executable (non-directive) code must appear BEFORE the first `::`
  directive in a file, and `::requires` has no body of its own, so any
  executable statement placed after it at the top level is a syntax
  error ("Unrecognized directive instruction", Error 99.916). Every
  BSF4ooRexx tutorial example follows this ordering.
- Object construction for fixed-arity constructors:
  `obj = .bsf~new("fully.qualified.ClassName", arg1, arg2, ...)`.
- Field access (static or instance): `obj~fieldName`, e.g.
  `clzColor~red` after `clzColor = bsf.importClass("java.awt.Color")`.
- Method calls: `obj~methodName(args)`, e.g. `dim~setSize(777,888)`.
- Explicit Java array creation: `bsf.createJavaArray("java.lang.String", n1, n2, ...)`.

## Unverified

1. **Varargs constructor/method matching.** Does passing several
   trailing Rexx arguments directly (e.g. three separate strings to
   `ProcessBuilder(String...)`) get matched to a Java varargs
   parameter, or must a `bsf.createJavaArray(...)` array be built
   first and passed as a single argument? Not demonstrated either way
   in the tutorial. Used untested in `test-bsf-process.rex`.
2. **Overload resolution.** `ProcessBuilder` has both
   `ProcessBuilder(String...)` and `ProcessBuilder(List<String>)`
   constructors. No stated rule for how BSF4ooRexx picks among
   overloads when more than one could match a given call.
3. **Rexx `.array` -> Java `List`/`Collection` marshalling on input.**
   The tutorial only shows the reverse direction (a BSF-created Java
   array iterable via `do over` on the Rexx side); passing an ordinary
   Rexx `.array` as an argument to a Java method expecting a `List` is
   unaddressed.
4. **Enum constant access via `clz~CONSTANT_NAME`.** Inferred by
   analogy to plain static fields (Java enum constants are static
   fields under the hood); never demonstrated in the tutorial against
   an actual Java `enum` type. Would be needed for e.g.
   `bsf.importClass("java.util.concurrent.TimeUnit")~SECONDS`.
5. **`java.lang.Process` methods through the bridge.** `isAlive()`,
   `exitValue()`, `destroyForcibly()` are real, documented
   `java.lang.Process` methods, but their behavior specifically
   through the BSF4ooRexx bridge (marshalling, exception surfacing on
   failure, etc.) is untested.

## Tested but unverified

(none yet — pending results of `test-bsf-process.rex`)
