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

```rexx
call outtrap 'mystem.'     /* start trapping into mystem. */
'LISTC LEVEL(MY.DATA)'
call outtrap 'off'         /* stop trapping */
do i = 1 to mystem.0
    say mystem.i
end
```

**Neither classic OS/2 REXX nor OREXX ever had `address...with` at
all.** Checked directly against IBM's own OS/2 Procedures Language
2/REXX Reference (CREXX.INF) and Object REXX Reference (REXX.INF,
c. 2001): the `ADDRESS` instruction's own syntax diagram in *both* is
just `ADDRESS [environment] [expression]` -- no `WITH` clause, of any
kind, in either. This isn't a missing sub-option; the whole I/O
redirection clause postdates both.

**`USING` is an ooRexx-only resource type, not part of the standard.**
ANSI X3.274-1996's own `ADDRESS WITH` semantics define only `STREAM`
and `STEM` as resource types (besides plain `NORMAL`); `input using
(expr)` -- supplying the input value directly, no stem or stream
needed -- is an ooRexx extension beyond that, verified working in
ooRexx 5.2.0. Regina does not have it: its own reference manual's
`ADDRESS WITH` syntax diagram lists only `STREAM`, `STEM`, `LIFO`, and
`FIFO` as resource types (`LIFO`/`FIFO` being Regina's own extensions
beyond ANSI) -- `USING` appears nowhere in it. OREXX/classic OS/2 REXX
moot -- neither has `address...with` at all, per the finding above.

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
- RexxUtil function set differs from ooRexx RexxUtil (see table below)
- `SysGetFileDateTime` may not be available

Write scripts targeting OBJREXX 6.00 using only classic Rexx
syntax and the OBJREXX RexxUtil subset.

### RexxUtil availability: OREXX vs ooRexx vs Regina

Confirmed against three primary sources: IBM's *Object REXX for
Windows Reference* (Version 2.1, SH12-6725-00) and *Object REXX for
AIX Reference* (Version 1.1.3, SH12-6386-01) for OREXX; the current
ooRexx Language Reference §8 ("Rexx Utilities (RexxUtil)") for ooRexx
5.2.0; and Regina's own RegUtil reference manual. The `RexxUtil`
repertoire is not standardized and genuinely differs by
implementation and, for OREXX, by platform edition:

| Function(s) | OREXX | ooRexx 5.2.0 | Regina (`RegUtil`) |
|---|---|---|---|
| `SysFileCopy`, `SysFileMove` | No on Windows -- absent from the Windows 2.1 Reference entirely; not checked on AIX | Yes | No -- `SysCopyObject`/`SysMoveObject` instead. Verified against Regina's own regutil.pdf: despite the "Object" name, these copy/move ordinary files on any platform ("Copies the file named by from to a new name to... Obviously, that doesn't work on other systems" -- referring specifically to the WPS-object-copying bonus, which is OS/2-only; the file-copy behavior itself is universal) |
| The `SysIsFileXxx` family (`SysIsFile`, `SysIsFileDirectory`, `SysIsFileLink`, and the Windows-only detail variants) | No on Windows; not checked on AIX | Yes | No |
| The Workplace-Shell family (`SysCreateObject`, `SysDestroyObject`, `SysSetObjectData`, `SysQueryClassList`, and related) | Yes, but only in the OS/2 edition -- confirmed absent from the Windows and AIX editions | No | No |
| The semaphore family (`SysCreateEventSem`, `SysCreateMutexSem`, and related) | Yes | Yes, but deprecated since (per Appendix B.2.1 of the current Reference) in favor of the `.EventSemaphore`/`.MutexSemaphore` classes | Yes |
| Unix process functions (`SysFork`, `SysWait`, `SysCreatePipe`) and `SysGetMessage`/`SysGetMessageX` (Unix message catalogs) | Yes, in the AIX edition | Yes, on Unix-like platforms | No -- confirmed absent from RegUtil's reference |
| `SysWinGetPrinters`, `SysWinGetDefaultPrinter`, `SysWinSetDefaultPrinter`, `SysFormatMessage`, `SysGetLongPathName`, `SysGetShortPathName`, `SysShutdownSystem` | No | Yes -- later ooRexx-only additions, marked `*NEW*` in its own changelog | No |
| `SysLoadFuncs`/`SysDropFuncs` | Required, to register the package | Deprecated no-ops since ooRexx 4.0.0 (per Appendix B.2.2) -- the package is auto-registered | Required, to register the package |

Ordinary file/directory operations (`SysFileTree`, `SysMkDir`,
`SysRmDir`, `SysSearchPath`, `SysTempFileName`, `SysGetFileDateTime`,
`SysSetFileDateTime`, `SysDriveInfo`, `SysDriveMap`, `SysVolumeLabel`,
`SysWaitNamedPipe`), the macro-space family (`SysAddRexxMacro` and
related), console I/O (`SysCls`, `SysGetKey`, `RxMessageBox`, and
related -- Windows-only in all three), and `SysQueryProcess` are
present in all three implementations; ooRexx's own reference notes
that `SysFileTree` and `SysQueryProcess` each "work differently"
across platforms, so their exact behavior should be verified on each
target rather than assumed identical. Note also a spelling drift with
no functional consequence (Rexx symbols are case-insensitive):
OREXX's manuals spell it `SysGetErrortext`; the current ooRexx
Reference and Regina's RegUtil manual both spell it `SysGetErrorText`.

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
See "CALL ON vs SIGNAL ON" below (in this same file) for the full
treatment including wrong/correct patterns and the `signal on` vs
`call on` distinction — corrected 2026-09-02, since this cross-
reference previously pointed to `../ooRexx/RULES.md`, which has no
such section at all.

Key points for classic Rexx:

- `signal` flushes the entire call stack — all active `call` frames
  and `do`/`select` blocks are gone. It is not a `goto`.
- Use `leave` to exit a loop, `return` to exit a subroutine, `iterate`
  to continue to the next loop iteration.
- `signal on` is available in classic Rexx for non-resumable condition
  handling (error, syntax, halt, novalue, notready, failure).
- `call on` (resumable condition handling) **is** standard ANSI Rexx
  (X3.274-1996), implemented by Regina — it is not an ooRexx extension,
  despite an earlier version of this bullet claiming otherwise. See
  "CALL ON vs SIGNAL ON" below for the full treatment: which conditions
  each form supports, and real platform gaps (e.g. TSO/E lacks
  NOTREADY) that are worth checking instead of the retracted claim
  above.

| Date | Rule added | Triggered by |
|------|-----------|--------------|
| 2026-05-11 | `signal` is not `goto`; flushes stack | Applies equally to classic Rexx |
| 2026-09-02 | Removed the stale "`call on` not in classic Rexx" sub-claim from the row above (and the bullet it described) — it was wrong and had already been correctly retracted elsewhere in this file, under "CALL ON vs SIGNAL ON", since 2026-05-13; this file was asserting both the wrong claim and its own correction until now | Cross-checked while drafting Safe-REXX-Merged-DRAFT.md in the (unrelated) Safe-REXX repo |

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
- A template variable in parentheses, `(name)`, is a *match pattern*,
  not a receiver: Rexx scans for `name`'s *current value* as a literal
  separator, rather than assigning the next token to it. Confusing the
  two is a silent logic error, not a syntax error. Verified directly:
  `parse var line (delim) rest` with `delim = ':'` scans for the
  literal `:` and assigns everything after it to `rest` -- `delim`
  itself is never written to, unlike a plain `parse var line delim
  rest`, where `delim` would instead *receive* the first token.

```rexx
parse var line word rest     /* word RECEIVES the first token */
parse var line (delim) rest  /* Rexx SCANS for delim's current value */
```

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

**Pitfall: falling through into a `PROCEDURE`-led label is a hard
error, not silent misbehavior.** Verified directly: `PROCEDURE` must
be the first instruction actually *executed* immediately after its
own label is reached via `CALL` (or a function invocation) — reaching
it any other way, by straight-line fall-through from the code above
it, raises `Error 17: Unexpected PROCEDURE.` as a `SYNTAX` condition,
at the `PROCEDURE` line itself. This is the concrete mechanism behind
the "do not write code that both calls and falls through" rule above:
an unguarded fall-through into a `PROCEDURE`-led subprocedure crashes
outright rather than silently exposing the wrong variables — which is,
if anything, easier to notice than the alternative failure mode of
plain (non-`PROCEDURE`) code silently running with unintended variable
scope.

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-09-02 | Falling through into a `PROCEDURE`-led label raises Error 17 (SYNTAX), not silent misbehavior | Cross-checked while drafting Safe-REXX-Merged-DRAFT.md in the (unrelated) Safe-REXX repo |

---

### Type and range checking

REXX has no variable typing and no true arrays. Simulate arrays with
compound variables; enforce constraints explicitly:

- Use `DATATYPE(var, type)` to validate values before use.
- Compound variable indexes are not range-checked and need not be
  integers. Code explicit bounds checks when array semantics are required.
- A dropped symbol (ANSI X3.274-1996 §3.1.16: a symbol with no value
  assigned to it, as opposed to a *variable*, §3.1.47/§3.1.52) evaluates
  to its own name in upper case and can be used as a compound index;
  this is a common source of silent bugs.
- This untyped-variable behavior is unchanged in ooRexx. ooRexx
  *objects* are a different matter -- see ooRexx/RULES.md's dynamic
  typing note.

---

### Dropped symbols as constants

Using a dropped symbol as a symbolic constant (its value is its own
name in upper case) is a legitimate and readable idiom. However:

- Choose names that cannot plausibly be reused as true variables.
- If you choose not to rely on this behaviour, add `SIGNAL ON NOVALUE`
  at program start to trap any reference to a dropped symbol.

---

### Variable references (passing by name)

REXX does not support pass-by-reference. The common substitute is to
pass a variable's *name* as an argument and use it to construct compound
variable references. Pitfalls:

- A procedure with `PROCEDURE EXPOSE` can only access variables listed
  in the expose list; passing the name of an unlisted variable gives
  access only to a local copy.
- If a dropped symbol is used to pass its own name, the second
  call will see the value set by the first call, not the name.

**Prefer `VALUE()` to `INTERPRET` for reading/setting a variable by
name.** `VALUE(name)` reads, `VALUE(name, newvalue)` sets and returns
the old value; both touch exactly one variable and nothing else, even
if `name` is malformed or attacker-controlled. `INTERPRET` executes
whatever Rexx source text it is handed, not just an assignment.
Verified directly: handing the same crafted string (an invalid
variable name with a second clause hidden after a semicolon) to each
-- `VALUE()` raised a `SYNTAX` condition on the bad name and ran
nothing else; `INTERPRET` began executing the hidden second clause
(visibly compiling and invoking the routine it named) before failing
only because that call was missing a required argument, not because
the injection was blocked. Reserve `INTERPRET` for genuinely dynamic
code -- a whole statement or expression built at run time -- not as a
substitute for a single indirect variable reference.

`VALUE` also takes an optional third argument, `selector`, naming a
variable pool other than the program's own -- `VALUE(name, newvalue,
'ENVIRONMENT')` reads/sets an environment variable instead of a Rexx
variable; `INTERPRET` has no equivalent. Not universal: per IBM's own
z/VM REXX/VM Reference (Appendix E), GCS's REXX does not support the
`selector` argument at all, only the two-argument form.

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

The default, and what else is available, varies not just by
platform/dialect but by *invocation context*. Only environments
actually documented for that context are listed; a blank cell means
none -- avoid vague filler like "whatever the host app registers".

| Invocation context | Default | Other environments |
|---|---|---|
| OS/2 command prompt (classic REXX) | `CMD` | |
| PC-DOS command prompt (classic REXX) | `COMMAND` | |
| Command prompt (OREXX, ooRexx) | `CMD` | `SYSTEM`, `PATH` on ooRexx |
| Regina command prompt | `SYSTEM` | `COMMAND`, `REXX` |
| TSO/E READY | `TSO` | `MVS`, `CONSOLE`†, the link/attach family, the APPC family |
| ISPF on z/OS | `TSO` | `MVS`, `CONSOLE`†, the link/attach family, the APPC family, `ISPEXEC` |
| ISPF/PDF EDIT on z/OS | `TSO` | `MVS`, `CONSOLE`†, the link/attach family, the APPC family, `ISPEXEC`, `ISREDIT` |
| ISPF on z/VM | `CMS` | `ISPEXEC` |
| ISPF/PDF EDIT on z/VM | `CMS` | `ISPEXEC`, `ISREDIT` |
| OMVS shell | `SH` | `TSO`, `MVS`, `SYSCALL` |
| `IRXJCL` | `MVS` | the link/attach family, the APPC family |
| System REXX | `MVS` (`TSO=NO`) | the link/attach family, `APPCMVS`, `BCPii`, the APPC family; `TSO=YES` adds `TSO`, `ISPEXEC`, `ISREDIT` |
| EDIT macro | `EDIT` | none -- `TSO` unavailable until `END` terminates `EDIT` |
| TEST macro | `TEST` | none -- `TSO` unavailable until `END`/`RUN` terminates `TEST` |
| IPCS macro | `TSO` | `IPCS` -- not available at all in the session's own separate TSO/E mode |
| CMS command line | `CMS` | `COMMAND`, `CP` |
| GCS | `GCS` | `COMMAND` |
| XEDIT macro | `XEDIT` | falls through to `CMS`, then `CP`, automatically |

PC-DOS's classic REXX is IBM's own, bundled with PC DOS 7 -- distinct
from the third-party Personal REXX (Quercus Systems) also available
for DOS. Its ADDRESS discussion uses `ADDRESS COMMAND` throughout
(same structure as OS/2's manual's own `ADDRESS CMD` passage, down to
the identical DIR-STARTUP example) -- PC-DOS's native environment is
`COMMAND`, not `CMD` like OS/2's; don't assume they match.

Link/attach family: `LINK`, `LINKMVS`, `LINKPGM` (link, same task
level), `ATTACH`, `ATTCHMVS`, `ATTCHPGM` (attach, different task
level) -- available in *any* address space, TSO or not. APPC family:
`CPICOMM` (SAA CPI Communications), `LU62` (APPC/MVS, SNA LU 6.2) --
available in any MVS address space. † `CONSOLE` needs an active
extended MCS console session (the TSO/E `CONSOLE` command) plus
console command authority; TSO/E address space only, not batch.

**TSO/E REXX is the only REXX interpreter on z/OS** -- TSO READY,
ISPF, the OMVS shell, batch (`IRXJCL`), and System REXX are all the
*same* interpreter run in different environments, not different
products. `APPCMVS`/`BCPii` do not appear anywhere in the TSO/E REXX
Reference, so they look genuinely specific to System REXX, unlike the
link/attach and APPC families it shares with everything else.

**`EDIT` and `TEST` work by a genuinely different mechanism than
`ISPEXEC`/`ISREDIT`**, not a variant of the same one -- they are not
entries in the SUBCOM host command environment table at all, absent
from it across every edition of the TSO/E REXX Reference from 1988
through the current (2021) one. Their own `EXEC` subcommand's
documentation, in the *TSO/E Command Reference* (SC28-1969, a separate
manual), states the real mechanism: inside a CLIST or exec launched
via `EDIT`'s or `TEST`'s own `EXEC` subcommand, non-REXX clauses
default to `EDIT`/`TEST` subcommands, and `TSO` commands are flatly
unavailable until `END` (or `RUN`, for `TEST`) terminates the facility
-- an invocation-time restriction, not a registered `ADDRESS`-
selectable environment the way every other row in this table is.
`IPCS` genuinely is such an environment, much like `ISPEXEC` --
confirmed directly (ibm.com/docs returns 403 to direct HTTP requests
here, but was reachable through the browser) in the *z/OS MVS IPCS
User's Guide* (SA23-1384): `ADDRESS IPCS` changes the host command
environment to `IPCS`, available only when the exec runs from an IPCS
session. An IPCS session itself has multiple internal modes, and
`ADDRESS IPCS` support is explicit per mode -- it works in IPCS mode
(the session's normal mode) and during a trap stop, but the manual
states plainly that "No ADDRESS IPCS support is intended" for the
session's own separate TSO/E mode, where ordinary TSO commands and
CLISTs run instead.
(LPEX, an OS/2 editor, was checked as a possible addition here --
its macro language, per the actual LPEX Editor User's Guide
(SC09-2795), is REXX-flavored but not documented anywhere in that
guide as using an ADDRESS-based host command environment the way XEDIT
and ISREDIT are; it appears to be LPEX's own scripting language, not
genuine ADDRESS-dispatched Rexx. Not added pending clarification.)

Sourcing: OS/2-family, ooRexx, and Regina rows rest on each
implementation's own manual. The TSO-family rows come from IBM's TSO
Extensions Version 2 REXX Reference -- its host command environment
table grew between editions: SC28-1883-0 (1988, fetched via a
non-ibm.com mirror since ibm.com/docs returns 403 here) documents a
noticeably smaller set, missing `CONSOLE`, the APPC family, and the
`LINKMVS`/`LINKPGM`/`ATTCHMVS`/`ATTCHPGM` forms entirely; SC28-1883-4
(1991, archive.org full text) documents the fuller list reflected
above -- a real difference between TSO/E releases, not an error in
either edition. It documents ISPF's environment list from TSO/E's own
side, and the ISPF Dialog Developer's Guide (SC34-4821, also fetched
via a non-ibm.com mirror) does not independently confirm that list, so
the ISPF-on-z/OS rows reflect TSO/E's documentation of ISPF, not
ISPF's own. ISPF-on-z/VM is inferred by the same pattern (default
unchanged from the underlying platform; `ISPEXEC`/`ISREDIT` added)
since no VM-specific ISPF manual confirms it independently. CMS, GCS,
and XEDIT come from IBM's z/VM REXX/VM Reference (including a
documented `ADDRESS()` example returning `'XEDIT'`); OMVS's `SH`
default from IBM's *z/OS Using REXX and z/OS UNIX System Services* (a
manual distinct from the TSO/E REXX Reference). `ISREDIT` requires an
active edit session regardless of platform. Cross-checked (via the browser
pane, since ibm.com/docs returns 403 to direct HTTP requests here)
against the currently-maintained z/VM 7.4.0 online documentation,
which confirms all three word for word: "Environment"
(https://www.ibm.com/docs/en/SSB27U_7.4.0/com.ibm.zvm.v740.dmsb1/xenvir.htm)
confirms CMS as the default when called from CMS; "z/VM REXX/VM
Interpreter in the GCS Environment"
(https://www.ibm.com/docs/en/SSB27U_7.4.0/com.ibm.zvm.v740.dmsb1/rexxgcs.htm)
confirms GCS as REXX's default under GCS and the missing `selector`
argument, in text identical to the older SC24-6314-73 PDF; "Entering
Commands to GCS"
(https://www.ibm.com/docs/en/SSB27U_7.4.0/com.ibm.zvm.v740.gcta0/icon.htm)
confirms `ADDRESS COMMAND` under GCS behaves like CMS's (host commands
only, no command files or implied CP commands). No corrections were
needed to any of the above -- only this citation upgrade.

---

### Portability: character encoding

Binary and hex constants for character data are platform-dependent:

- CMS and TSO use EBCDIC (`'C1'X` = 'A')
- DOS, OS/2, Linux, Windows: not just plain ASCII -- some combination
  of 7-bit ASCII, an 8-bit code page extending ASCII (Latin-1,
  Windows-1252, etc. -- these disagree above code point 127), and
  Unicode (typically UTF-8/UTF-16), depending on the system, its
  locale/code-page config, and the specific file/stream (`'41'X` = 'A'
  in all of these, since ASCII/UTF-8 agree in the 7-bit range)
- Code page and national language affect even EBCDIC systems

Segregate system-dependent and code-page-dependent values. Document
them clearly. Do not embed character codes inline in portable code.

---

### I/O portability

- REXX's first shipped implementation, on CMS in VM/SP Release 3,
  used `EXECIO`, later inherited by TSO/E and other platforms --
  attributed to REXX specifically, not REX: no evidence exists here
  about what Cowlishaw's original 1979-82 internal-use REX (before the
  rename, before any public VM/SP release) actually used for I/O; the
  primary sources checked (see BIBLIOGRAPHY.md) are all post-rename,
  shipped-product manuals. Stream I/O came later; it's
  part of the language as defined in Cowlishaw's TRL-2 (1990) and
  later formalized by ANSI X3.274-1996 -- both are language
  specifications, not descriptions of any particular product's
  implementation, so neither pins down when a given interpreter
  actually added it. See the VM/SP System Product Interpreter
  Reference entry in BIBLIOGRAPHY.md for the direct primary-source
  check (three original manuals, 1983-1986) establishing that `EXECIO`
  and the program stack were CMS's only I/O mechanisms at least that
  far into its history.
- CMS's `EXECIO` supports three kinds of source or target: the
  program stack (`FIFO`/`LIFO`), a stem (`STEM stem.`), or a single
  plain variable (`VAR name` -- but only for exactly one line at a
  time; the count operand must be `1` with `VAR`) -- the source for a
  write, the target for a read. Verified directly against IBM's z/VM
  7.2.0 `EXECIO` command reference. Of these, TSO/E REXX in MVS
  inherited only a subset: the stack and `STEM` forms, not `VAR`
  (confirmed against the TSO/E REXX Reference's own `EXECIO` syntax
  diagram, which lists only `FIFO`/`LIFO`/`STEM`). In CMS, prefer the
  STEM form regardless, for bulk data.
- `LINEIN(file)`/`LINEOUT(file, string)` read and write whole lines;
  `CHARIN(file)`/`CHAROUT(file, string)` do the same character by
  character; `LINES(file)` and `CHARS(file)` report whether more data
  remain -- an exact count on some implementations, just `0` or `1` on
  others (detail in the next bullet) -- for use as a loop condition
  before the next read; `STREAM(file, option)` queries or acts on a
  stream -- `'State'` reports its overall status, `'Description'` a
  fuller status string, `'Command'` executes an operation on it. TSO/E
  does not support stream I/O at all outside the UNIX System Services
  (OMVS) subsystem, where full stream I/O is available.
- Neither `CHARS()` nor `LINES()` is guaranteed to return an exact
  count anywhere, on any platform, once stream I/O is available at
  all: ANSI Rexx explicitly permits either to report only `0` or `1`
  (at least one more available, or not) instead of a real count. Which
  one actually returns an exact count, for which stream kind, is a
  per-implementation, per-function choice, not a clean platform split
  -- verified directly this session: CMS's own `LINES()` returns an
  exact count for disk files (per the z/VM REXX/VM Reference's own
  example, "7 lines remain") but its `CHARS()` never does, even for
  files ("returns either 0 or 1... 0 otherwise" per the same manual,
  unconditionally); ooRexx's `CHARS()` returns an exact byte count for
  disk files (tested live: 24, then 12 after reading one line) but its
  own `LINES()` returned only `1` in the same test, both before and
  after. On OS/2, both `CHARS()` and `LINES()` return only `0` or `1`,
  for every stream kind. `= 0` is still a reliable, portable
  end-of-file test either way, in every dialect (ANSI Rexx and ooRexx
  included), unlike `STREAM(file,'State')` below. Never assume a
  specific target gives an exact count from either function without
  checking that target's own documented behavior. Some interpreters
  still support `EXECIO` for compatibility with legacy TSO/CMS code,
  but it is not the primary I/O model outside TSO/E and CMS
  themselves.
- **ooRexx note**: I/O object types. Alongside the bare functions
  above, ooRexx models stream I/O as a small class family (per the
  Language Reference §5.2-5.3): `.Stream` is the concrete class most
  code uses, wrapping a file or other stream as an object --
  `aStream~lines`, `aStream~chars`, `aStream~linein`, `aStream~lineout`,
  `aStream~charin`, `aStream~charout`, and so on, with identical
  semantics (the same `0`-or-`1`-vs-exact-count behavior included) as
  their function-call equivalents. `.InputStream`, `.OutputStream`,
  and `.InputOutputStream` are abstract mixin classes underneath it
  (confirmed via their own method tables: `.OutputStream` inherits
  `arrayOut`/`close`/`open`/`charIn`/`lineIn`/`position` and defines
  `charOut`/`lineOut` as abstract; `.InputStream` is the mirror image),
  meant for building custom stream implementations, not for direct use
  on an ordinary file.
- `STREAM(file,'State')` returning `NOTREADY` does not guarantee EOF;
  other conditions also produce NOTREADY, and it only appears after a
  read past the actual end -- prefer the `LINES()`/`CHARS()` test above.
- Encapsulate I/O code and provide platform-specific implementations
  (e.g., EXECIO for TSO/CMS/legacy code, stream I/O for TSO/E's OMVS
  shell and everywhere outside TSO/E and OS/2). Document all such code
  thoroughly.

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

- **`LINEOUT` opens in append mode by default; a full-file overwrite
  needs an explicit replace first.** Standard Rexx behavior, not an
  ooRexx quirk. A script that deletes a file and rewrites it with
  repeated `LINEOUT` calls will silently duplicate content the moment
  the delete step ever fails (locked file, permission issue), since
  `LINEOUT` has no way to know a delete was supposed to have happened:

```rexx
/* WRONG -- if the delete silently fails, this appends instead of
   replacing, duplicating old content underneath the new */
call SysFileDelete path
call lineout path, newContent

/* CORRECT -- explicit replace, independent of whether a prior
   delete succeeded */
call stream path, 'C', 'OPEN WRITE REPLACE'
call lineout path, newContent
call stream path, 'C', 'CLOSE'
```

  ooRexx also offers stream *methods* on a `.Stream` object as an
  alternative, preferred in new ooRexx code: `s = .Stream~new(path)`,
  `s~command('OPEN WRITE REPLACE')`, `s~lineout(newContent)`,
  `s~close()`.

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

`level` is the Rexx *language level* the interpreter targets, not the
interpreter's own version number -- several implementations conflate
the two. The `name`/`level` values actually seen in practice:

| Implementation | `name` | `level` | Source |
|---|---|---|---|
| OS/2 classic REXX | `REXXSAA` | `4.00` | *OS/2 Procedures Language 2/REXX Reference*, S10G-6268, verified directly |
| OREXX | `OBJREXX` | `6.00` | *Object REXX Reference*, OS/2 edition, verified directly (no separate IBM order number found -- shipped as online help, unlike the numbered Windows SH12-6725-00/AIX SH12-6386-01 editions) |
| CMS / TSO/E REXX ("REXX370") | `REXX370` | `4.00` | Widely documented; not checked against a primary manual |
| Regina | `REXX-Regina_<version>` | `5.00` | Regina's own manual + public examples; ANSI-compliant since 3.1 |
| ooRexx | `REXX-ooRexx_<version>(MT)_<bits>-bit` | `6.06` | Verified live, ooRexx 5.2.0 |

`REXXSAA` and `REXX370` share the same `level` (`4.00`) despite one
being PC/workstation and the other mainframe -- neither was ever
brought up to the ANSI-1996 level (`5.00`).

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
