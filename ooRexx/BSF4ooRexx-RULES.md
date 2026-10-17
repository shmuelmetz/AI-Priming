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

## When to use BSF4ooRexx [IMPORTANT]

Prefer native ooRexx classes; exploit BSF4ooRexx when appropriate.

- First check the standard ooRexx distribution (`json.cls`,
  `csvStream.cls`, `yaml.cls`, `mime.cls`, `rxregexp.cls`,
  `socket.cls`, RexxUtil, the built-in classes) for a class that does
  the job, and use it.
- Reach for BSF4ooRexx when ooRexx has nothing comparable and a Java
  library does the job properly -- e.g. parsing real-world HTML into a
  tree (jsoup), where string scanning is fragile.
- A script that uses BSF4ooRexx says so in its header, along with any
  extra jars it needs; it will not run where Java and BSF4ooRexx are
  not installed (e.g. OREXX on ArcaOS without them).

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
- Field access (static or instance): `obj~fieldName`, e.g.,
  `clzColor~red` after `clzColor = bsf.importClass("java.awt.Color")`.
- Method calls: `obj~methodName(args)`, e.g., `dim~setSize(777,888)`.
- Explicit Java array creation: `bsf.createJavaArray("java.lang.String", n1, n2, ...)`.

## Unverified

1. **Varargs constructor/method matching.** Does passing several
   trailing Rexx arguments directly (e.g., three separate strings to
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
   an actual Java `enum` type. Would be needed for, e.g.,
   `bsf.importClass("java.util.concurrent.TimeUnit")~SECONDS`.
5. **`java.lang.Process` methods through the bridge.** `isAlive()`,
   `exitValue()`, `destroyForcibly()` are real, documented
   `java.lang.Process` methods, but their behavior specifically
   through the BSF4ooRexx bridge (marshalling, exception surfacing on
   failure, etc.) is untested.

## Tested but unverified

Run 2026-10-16 on Windows 11, ooRexx 5.2.0, BSF4ooRexx850
(v850-20260326-refresh), IBM Semeru Java 21.0.5, from a fresh shell
with plain `rexx` (no `rexxj.cmd`):

- `bsf.import("java.lang.System")~getProperty("java.version")` returns
  the Java version as a Rexx string.
- `bsf.import("java.lang.String")~new("x")` returns a Java object;
  `say` shows its Java identity (`java.lang.String@...`), so call
  `~toString` to get the text.
- jsoup: `doc = bsf.import("org.jsoup.Jsoup")~parse(.bsf~new("java.io.File", path), "UTF-8")`
  then `doc~select("p.drop.verse")~size`, `~get(0)~text`,
  `el~select("span.tooltiptext")~size` all work as expected.

`test-bsf-process.rex` (the `ProcessBuilder` questions above) is still
untested.
