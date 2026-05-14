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

---

## Rules derived from Safe REXX papers

Source: *Safe REXX on the Desktop, Or Will They Still Respect My Code in
the Morning?* by Shmuel (Seymour J.) Metz © 1998, 2023.
Published in two parts in RexxInfo.org.
Source: *Safe REXX in the Enterprise, Or Will They Still Respect My Code
in the Morning?* by Shmuel (Seymour J.) Metz © 1993, 2023.
Note: the published desktop version was split across two issues.

| Date | Rule added | Triggered by |
|------|-----------|--------------|
| 2026-05-13 | Safe REXX rules block | SafeRexx.zip parsed |

---

### Abutment

[IMPORTANT]

REXX supports three concatenation operators: explicit (`||`), abutment
with whitespace (single blank inserted), and abutment without whitespace
(direct concatenation). The three produce different results.

- Do not add or remove blanks between abutted expressions on the
  assumption that they are irrelevant.
- Do not abut a literal string against a single-character variable name;
  if that character is a valid literal suffix (e.g., `X` for hexadecimal,
  `B` for binary), it will be treated as part of the literal, not as a
  variable reference. Avoid one-character variable names for this reason
  among others.
- Note that the hex suffix differs by platform: desktop paper uses `41X`
  → ASCII 'A'; enterprise paper uses `C1X` → EBCDIC 'A'.
- Abutment is legitimate and readable; do not avoid it entirely, but
  use it with care and judgement.

```rexx
dog = 'Peke'
say "Tom's " || dog || 's'  /* explicit: Tom's Pekes */
say "Dick's "dog"s"         /* abutment: Dick's Pekes */
say "Harry's" dog "s"       /* whitespace abutment: Harry's Peke s */
x = 'unknown'
say '41'X                   /* hex literal, NOT abutment: displays 'A' (ASCII) */
```

---

### Continuation

[IMPORTANT]

REXX supports both implicit continuation (when a line would be
syntactically invalid as written) and explicit continuation (trailing
comma). Two pitfalls:

**1. Argument separator vs. continuation:** when breaking a procedure
call after a comma that is an argument separator, the comma is consumed
as a continuation marker. Supply an additional comma for continuation:

```rexx
/* wrong: second comma lost as continuation */
say value('X',,
          'OS2ENVIRONMENT')   /* sets X to 'OS2ENVIRONMENT' */

/* correct: explicit continuation comma after separator */
say value('X',,              ,
          'OS2ENVIRONMENT')   /* retrieves X with no side effects */
```

**2. Expression continuation:** a line ending in a literal or
unparenthesised variable is treated as complete. Supply a trailing comma:

```rexx
'ECHO'                        /* displays blank line */
'DIR'                         /* displays directory -- separate statement! */

'ECHO'                   ,    /* note continuation comma */
'DIR'                         /* displays 'DIR' */
```

In some cases REXX detects the missing continuation as a syntax error;
in others it silently produces wrong results.

---

### Keywords as variable names

[CRITICAL]

Do not use REXX keywords as variable names.

The primary reason is clarity: a reader or programmer who sees `with`,
`value`, or `arg` used as a variable name must mentally distinguish the
variable from the keyword in every context where it appears. This
imposes a cognitive burden that invites misreading and maintenance
errors, independent of whether the interpreter accepts or rejects the
code. Any reader of the code — including the original author returning
weeks later — will find keyword names in variable position confusing.
Choose names that carry meaning and cannot be mistaken for language
constructs.

The secondary reason is that REXX may misinterpret or reject the
statement. In certain contexts — notably PARSE templates and certain
instruction forms — a word that is both a variable name and a keyword
is parsed as the keyword, silently producing wrong results with no
diagnostic. In other contexts the interpreter raises a syntax error.

In particular, do not use any of the following as variable names:

```
ARG      EXTERNAL   NUMERIC   PULL      SOURCE    VALUE
VAR      VERSION    WITH
```

This is especially treacherous in PARSE templates, where `WITH`, `VAR`,
and `VALUE` have syntactic meaning. A variable named `with` used in a
PARSE instruction will be treated as the keyword `WITH`, silently
producing wrong results:

```rexx
with = 'Ada Emmy Gracie Lise'
parse value text with first rest       /* wrong: WITH parsed as keyword,
                                          not as a variable reference   */
parse value text with with first rest  /* also wrong for the same reason */
```

The same applies outside PARSE. A variable named `arg` obscures the
distinction between a variable reference and the ARG instruction; a
variable named `source` invites confusion with PARSE SOURCE. Choose
names that are unambiguously not keywords.

---

### SIGNAL is not GOTO — flushes control stack; CALL ON vs SIGNAL ON

[CRITICAL]

`SIGNAL <label>` jumps to a label but flushes the entire control stack.
Any enclosing `DO`/`SELECT` blocks are terminated; a subsequent `END`
is a syntax error. Use `SIGNAL` only for exceptional conditions:

```rexx
/* wrong */
do forever
    signal BELL
    whatever
    BELL:
    end       /* ERROR: DO was flushed by SIGNAL */
```

Use `LEAVE`, `ITERATE`, and `RETURN` for normal flow control.

**`CALL ON` vs `SIGNAL ON`:**

Both are in ANSI X3.274-1996 and supported by Regina. They differ in
resumability:

- `SIGNAL ON condition` — non-resumable; flushes the call stack like
  `SIGNAL <label>`. Control does not return to the point of the
  condition.
- `CALL ON condition [NAME trapname]` — resumable; the trap routine is
  called as a subroutine and returns to the point of the condition.

Conditions supported by both in ANSI: ERROR, FAILURE, HALT, NOTREADY.
SYNTAX and NOVALUE are supported by `SIGNAL ON` only (not `CALL ON`)
per ANSI.

TSO/E REXX: does not support NOTREADY. Regina: supports all ANSI
conditions for both CALL ON and SIGNAL ON.

The earlier claim that `CALL ON` was an ooRexx extension was incorrect.
It is standard ANSI Rexx and is implemented by Regina.

| Date | Correction | Triggered by |
|------|-----------|--------------|
| 2026-05-13 | `CALL ON` is ANSI X3.274-1996, not ooRexx-only | Verified against RexxInfo.org ANSI-1996 instruction reference and Regina 3.9.7 manual |

---

### Parsing pitfalls

- Abbreviated PARSE forms (`ARG`, `PULL`) translate to upper case;
  `PARSE ARG` and `PARSE PULL` do not. Do not confuse them.
- The last variable (or `.`) in a PARSE template receives the remainder
  including leading and trailing blanks. Use `STRIP` or a trailing `.`
  to discard them.
- `VAR` and `VALUE`/`WITH` in templates are keywords; using them as
  variable names produces surprising results (see Keywords above).
- Variable patterns (the `+(var)` form) are not supported in all
  implementations; MVS/XA REXX does not support them. Verify before use
  in multi-platform code.

---

### Scoping and procedure isolation

[IMPORTANT]

REXX is a hybrid of dynamic and static scoping. Discipline the language
omits must be supplied by the programmer.

- Do not write code that serves as both inline and out-of-line code.
  Programs that both call and fall through into the same code are
  error prone.
- Precede every internal subprocedure with a guard statement (`EXIT` or
  `RETURN`) to prevent accidental fallthrough.
- Begin every internal subprocedure with `PROCEDURE` as the first
  statement after the label, unless the subroutine must access the
  caller's variables dynamically (see Variable References below).
- `PROCEDURE EXPOSE` hides all variables except those explicitly listed.
  Analyse the expose list carefully.
- To expose an entire stem and all its elements, list the stem name
  with its trailing dot: `PROCEDURE EXPOSE fred.` exposes `fred.` and
  every compound variable `fred.x` for all tails `x`. Listing `fred`
  without the dot exposes only the simple variable `fred`, not the
  stem. (Regina manual §2.19; ANSI X3.274-1996 §7.4.20.)
- Stems cannot be passed as arguments in classic Rexx (call by value
  only); use `PROCEDURE EXPOSE stem.` for internal subroutines, or
  pass the stem name as a string and use `VALUE`/`INTERPRET` for
  generic routines. ooRexx supports `USE ARG` for true pass by
  reference.
- Do not mix PROCEDURE-scoped and unscoped subroutines in the same
  program unless there is a clear, documented reason.

```rexx
/* correct isolation */
saytime: PROCEDURE
    say time()
    return

exit    /* guard against fallthrough into putdata */
putdata:
    parse arg name .
    say name'='value(name)
    return
```

---

### Type and range checking

REXX has no variable typing and no true arrays. Simulate arrays with
compound variables; enforce constraints explicitly:

- Use `DATATYPE(var, type)` to validate values before use.
- Compound variable indexes are not range-checked and need not be
  integers. Code explicit bounds checks when array semantics are required.
- An uninitialized variable evaluates to its own name in upper case and
  can be used as a compound index; this is a common source of silent bugs.

---

### Uninitialized variables as constants

Using an uninitialized variable as a symbolic constant (its value is
its own name in upper case) is a legitimate and readable idiom. However:

- Choose names that cannot plausibly be reused as true variables.
- If you choose not to rely on this behaviour, add `SIGNAL ON NOVALUE`
  at program start to trap any uninitialized variable reference.

---

### Variable references (passing by name)

REXX does not support pass-by-reference. The common substitute is to
pass a variable's *name* as an argument and use it to construct compound
variable references. Pitfalls:

- A procedure with `PROCEDURE EXPOSE` can only access variables listed
  in the expose list; passing the name of an unlisted variable gives
  access only to a local copy.
- If an uninitialized variable is used to pass its own name, the second
  call will see the value set by the first call, not the name.

Note: ooRexx and OREXX have `USE ARG` for pass-by-reference; classic
REXX does not.

---

### ADDRESS and the default environment

[IMPORTANT]

Do not assume the default host command environment. Set it explicitly:

- Desktop/OS/2: `ADDRESS CMD`
- Enterprise/TSO: `ADDRESS TSO`
- CMS: `ADDRESS CMS`

This allows a routine to be called from within editors and other
environments that use REXX as their macro language.

---

### Portability: character encoding

Binary and hex constants for character data are platform-dependent:

- CMS and TSO use EBCDIC (`'C1'X` = 'A')
- DOS, OS/2, Linux, Windows use ASCII (`'41'X` = 'A')
- Code page and national language affect even EBCDIC systems

Segregate system-dependent and code-page-dependent values. Document
them clearly. Do not embed character codes inline in portable code.

---

### I/O portability

- `CHARS()` and `LINES()` return exact counts only on CMS; on OS/2,
  Linux, Windows they return only 0 or 1.
- TSO/E REXX in MVS does not support stream I/O outside UNIX System
  Services; use `EXECIO` with the STEM option instead.
- `EXECIO` in TSO/E supports only the STEM and stack forms; the variable
  name form is not supported. In CMS, prefer the STEM form.
- `STREAM(file,'State')` returning `NOTREADY` does not guarantee EOF;
  other conditions also produce NOTREADY.
- Encapsulate I/O code and provide platform-specific implementations
  (e.g., EXECIO for TSO/CMS, stream I/O for OS/2/Linux). Document all
  such code thoroughly.

```rexx
/* portable loop -- works on all platforms */
do while lines(myfile) \= 0
    myline = linein(myfile)
    ...
end

/* TSO/CMS preferred */
'EXECIO * DISKR' name '(STEM mystem. FINIS'
do i = 1 to mystem.0
    parse var mystem.i myline
    ...
end
```

---

### PARSE SOURCE and PARSE VERSION

- Use `PARSE SOURCE system invocation origin` to detect the operating
  system, invocation type, and source file location. Use this to locate
  companion data files, select character encoding, or detect invalid
  invocations.
- Use `PARSE VERSION name level date1 date2 date3` to detect the
  language level and provide alternate implementations for older
  platforms.

```rexx
parse source system invocation origin
select
    when system = 'TSO'  then address TSO
    when system = 'OS/2' then address CMD
    otherwise do
        say system 'not supported by' origin
        exit
    end
end
```

---

### General defensive programming (from Safe REXX)

These are stated explicitly in both papers as equally important to the
REXX-specific rules:

- Use meaningful variable names.
- Use judicious comments.
- Use a consistent indentation style.
- Learn REXX on its own terms; do not rely on analogies with PL/I,
  TSO CLISTs, or other languages.
- Adopt a clear and consistent programming style throughout a program.
