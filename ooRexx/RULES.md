# ooRexx Rules for AI Priming

Rules and corrections for AI engines working with ooRexx. These
address specific errors observed in AI-generated ooRexx code during
sessions conducted 2026-04-19 through 2026-05-03.

Treat this document as ground truth. Where your training contradicts
these rules, defer to this document and to the references in
BIBLIOGRAPHY.md.

---

## Special variables: `rc`, `result`, `sigl`

### `rc` — host command return code

`rc` is set **only** by host commands:

```rexx
address system 'some-command'   /* rc set here */
'bare expression'               /* rc set here -- bare expression
                                   is a host command in the current
                                   default environment */
address foo 'expression'        /* rc set here */
```

`rc` is **NOT** set by `call` or function invocations. Using `rc`
after `call SysFileCopy` is a bug — it will read the previous host
command's return code, or the literal string `'RC'` if no host
command has run.

This applies to both classic Rexx and ooRexx.

### `result` — subroutine/function return value

`result` is set by `call` and function invocations:

```rexx
call SysFileCopy src, dst
copyRc = result          /* correct */

call SysFileDelete path
delRc = result           /* correct */
```

### Correct pattern for RexxUtil file operations

```rexx
call SysFileCopy src, dst
copyRc = result
if copyRc \= 0 then say 'ERROR: copy failed (rc='copyRc')'

call SysFileDelete path
delRc = result
if delRc \= 0 then say 'ERROR: delete failed (rc='delRc')'
```

---

## `address...with` — capturing child process output (standard Rexx)

ooRexx supports capturing child process stdout and stderr directly
into stems without temp files or pipes:

```rexx
address system 'some-command' with output stem out. error stem err.
cmdRc = rc
do i = 1 to out.0
    say out.i
end
do i = 1 to err.0
    say '  [stderr]' err.i
end
```

Do NOT use temp file redirection to capture output. The `with` clause
is the correct ooRexx solution.

---

## String concatenation for command construction

When building command strings that contain paths with spaces or
embedded quotes, use `||` concatenation rather than REXX string
continuation (`,`):

```rexx
/* CORRECT */
psCmd = 'powershell -Command "Expand-Archive -Path ''' || srcPath || ''' -Force"'
address system psCmd with output stem out. error stem err.

/* WRONG -- continuation comma produces unparseable string */
address system 'powershell -Command "Expand-Archive -Path' ,
    "'"srcPath"'" '-Force"'
```

---

## Case sensitivity

ooRexx variable names and labels are case-insensitive. However:

- Filenames on Windows (NTFS/JFS) may be case-insensitive but
  should be treated as case-sensitive for portability to Linux.
- The `value()` built-in for environment variables is case-sensitive
  on Linux, case-insensitive on Windows.

---

## `~translate` vs `~upper`

Use `~translate` for uppercasing strings, not `~upper`.
`~upper` is ooRexx-specific and not available on OBJREXX 6.00
(ArcaOS). `~translate` works on both:

```rexx
str = str~translate   /* uppercase -- portable */
str = str~upper       /* ooRexx only -- avoid */
```

---

## Path construction on Windows

Always derive paths from `value('USERPROFILE',,'ENVIRONMENT')` or
similar environment variables. Never hardcode paths. Use `\` as
the separator (Windows convention) and be consistent:

```rexx
userProfile = value('USERPROFILE',,'ENVIRONMENT')
repoRoot    = userProfile'\repos\Personal'
binDir      = userProfile'\bin'
```

Do not mix `\` and `\\` — doubling backslashes is not needed in
REXX string literals (unlike some other languages).

---

## Date arithmetic

To compute days between two dates, use `Date('B', dateString, format)`
to convert to a base date (days since 1 Jan 0001), then subtract:

```rexx
todayBase = Date('B')                         /* today as base */
lastDate  = Date('B', '2026-05-03', 'N')     /* specific date */
daysSince = todayBase - lastDate
```

The `'S'` format is `YYYYMMDD` (no separators). The `'N'` format is
`DD Mon YYYY`. String comparison of date strings is unreliable for
arithmetic -- always convert to base first.

---

## File stream management

Always close streams explicitly after use:

```rexx
call lineout logFile, 'some text'
call stream  logFile, 'C', 'CLOSE'   /* explicit close */
```

Using `call lineout file` (one argument) requests a close but may
not flush on all platforms. `call stream file, 'C', 'CLOSE'` is
unambiguous.

---

## Session history

| Date | Rule added | Triggered by |
|------|-----------|--------------|
| 2026-05-03 | `rc` vs `result` | `SysFileCopy` returning `'RC'` literally |
| 2026-05-03 | `address...with` | Unnecessary temp file redirect pattern |
| 2026-05-03 | String concatenation | PowerShell command string failures |
| 2026-05-03 | `~translate` vs `~upper` | OBJREXX compatibility |
| 2026-05-03 | Stream close | `miktex-update.log` not flushing |

---

## Collection classes

ooRexx provides built-in collection classes. AI engines frequently
generate Rexx-style stem code where ooRexx collection classes are
more appropriate.

```rexx
/* Array -- ordered, integer-indexed */
arr = .Array~new
arr~append('first')
arr~append('second')
do item over arr
    say item
end

/* Directory -- unordered, string-keyed (like a hash map) */
dir = .Directory~new
dir['key'] = 'value'
say dir['key']

/* OrderedCollection -- ordered, append/remove */
oc = .OrderedCollection~new
oc~append('a')
oc~append('b')

/* Bag, Set, Queue, Stack also available */
```

Do NOT generate stem-based collections (e.g. `items.0`, `items.1`)
when an ooRexx collection class is appropriate. Stems are a classic
Rexx idiom; collection objects are the ooRexx idiom.

---

## `do over` — iterating collections

`do var over collection` iterates any ooRexx collection object:

```rexx
arr = .Array~of('a', 'b', 'c')
do item over arr
    say item
end

dir = .Directory~new
dir['x'] = 1
dir['y'] = 2
do key over dir
    say key '->' dir[key]
end
```

This also works over stems:

```rexx
do key over myStem.
    say key '->' myStem.[key]
end
```

Do NOT generate `do i = 1 to stem.0` when `do over` is cleaner.

---

## Array notation for stems

ooRexx supports array-style notation for stem access using `~[]`:

```rexx
/* Classic Rexx stem notation */
stem.1 = 'first'
say stem.1

/* ooRexx array object -- preferred */
arr = .Array~new(3)
arr[1] = 'first'
say arr[1]

/* String-keyed access on Directory */
dir = .Directory~new
dir['name'] = 'Shmuel'
say dir['name']
```

For mixed integer/string keyed collections (e.g. stdout lines plus
metadata), use a `.Array` with named string indices alongside integer
indices:

```rexx
outArr = .Array~new(out.0)
do i = 1 to out.0
    outArr[i] = out.i
end
outArr['rc'] = cmdRc      /* named index alongside numeric */
```

---

## Session history (continued)

| Date | Rule added | Triggered by |
|------|-----------|--------------|
| 2026-05-05 | Collection classes | AI generating stem code instead of `.Array`/`.Directory` |
| 2026-05-05 | `do over` | AI generating `do i = 1 to stem.0` instead |
| 2026-05-05 | Array notation for stems | `captureCmd` refactor using mixed-index `.Array` |

---

## `.Array` vs `.Directory` — correct class for the key type

`.Array` only accepts **positive integer** subscripts. Using a string
subscript on an `.Array` raises Error 93.907.

For mixed integer/string keys, use a `.Directory`:

```rexx
/* WRONG -- .Array does not accept string subscripts */
arr = .Array~new
arr['rc'] = 0        /* Error 93.907 */

/* CORRECT -- .Directory accepts any key */
dir = .Directory~new
dir['rc']  = 0
dir['out'] = .Array~new
dir['err'] = .Array~new
```

Standard pattern for capturing command output:

```rexx
captureCmd: procedure
    parse arg cmd
    address system cmd with output stem out. error stem err.
    cmdRc = rc
    outArr = .Array~new(out.0)
    do i = 1 to out.0; outArr[i] = out.i; end
    errArr = .Array~new(err.0)
    do i = 1 to err.0; errArr[i] = err.i; end
    result = .Directory~new
    result['out'] = outArr
    result['err'] = errArr
    result['rc']  = cmdRc
    return result

/* Caller: */
dir = captureCmd('some-command')
if dir['rc'] = 0 then say 'OK'
do item over dir['out']; say item; end
do item over dir['err']; say '[stderr]' item; end
```

| Date | Rule added | Triggered by |
|------|-----------|--------------|
| 2026-05-05 | `.Array` only takes integer subscripts; use `.Directory` for mixed keys | Error 93.907 on `arr['rc']` |

---

## Reserved special variable names — never use as local variables

Rexx has special variables that are set automatically. Never use these
as local variable names inside procedures or functions:

| Variable | Set by |
|----------|--------|
| `rc` | Host commands (`address system`, bare expressions) |
| `result` | `call` and function return values |
| `sigl` | Line number of last `signal` or `call` |

Using `result` as a local variable name inside a procedure will
conflict with the return value mechanism. Use descriptive names:
`outDir`, `cmdRc`, `retVal`, etc.

| Date | Rule added | Triggered by |
|------|-----------|--------------|
| 2026-05-05 | Never use `result`, `rc`, `sigl` as local variable names | Error 97 from `result = .Directory~new` inside `captureCmd` |

---

## Do not overload keywords or special variable names

### Special variables — never use as variable names

| Variable | Set by |
|----------|--------|
| `rc` | Host commands (`address system`, bare expressions) |
| `result` | `call` and function return values |
| `sigl` | Line number of last `signal` or `call` |

### Keywords — never use as variable or label names

Rexx keywords that must not be used as variable names:

`address`, `arg`, `call`, `do`, `drop`, `else`, `end`, `exit`,
`expose`, `forward`, `from`, `if`, `interpret`, `iterate`, `leave`,
`loop`, `nop`, `numeric`, `options`, `otherwise`, `parse`, `procedure`,
`pull`, `push`, `queue`, `raise`, `reply`, `return`, `routine`,
`say`, `select`, `signal`, `then`, `to`, `trace`, `until`, `use`,
`when`, `while`

ooRexx-specific keywords (in addition to the above):

`class`, `inherit`, `method`, `mixinclass`, `requires`, `self`,
`subclass`, `super`

### Rule

Use descriptive names that cannot be confused with keywords or special
variables: `outDir`, `cmdRc`, `retVal`, `loopIdx`, etc.

| Date | Rule added | Triggered by |
|------|-----------|--------------|
| 2026-05-05 | Do not overload keywords or special variable names | `result = .Directory~new` conflicting with special variable `result` |

---

## Stems, compound variables, and stem objects — three distinct concepts

These three things are frequently conflated. They are not the same.

### Stem
A **stem** is a syntactic concept: the `name.` prefix portion of a
compound variable name. In `out.1`, the stem is `out.`. In `foo.bar`,
the stem is `foo.`. A stem is not a variable, not an object, and not
a data structure — it is a naming prefix.

### Compound variable
A **compound variable** is a variable whose name contains a dot. The
portion after the stem (the *tail*) is substituted at runtime: if the
tail names a variable, its value is used; otherwise the tail is used
literally.

```rexx
i = 3
say out.i      /* accesses out.3 */
say out.0      /* accesses out.0 — tail '0' is literal */
```

Using compound variables that share a stem as a collection
(`out.0` = count, `out.1`…`out.n` = values) is a **convention**,
not a language-enforced data structure.

`address...with output stem out.` populates compound variables
sharing the stem `out.` — it does not create a `.Stem` object.

### Stem object (`.Stem` class)
A **stem object** is an ooRexx collection object of class `.Stem`.
It provides collection methods: `~allIndexes`, `~allItems`, `~size`,
`~supplier`, etc. It is a proper object, distinct from the syntactic
stem concept and from compound variables.

```rexx
/* Compound variables sharing a stem — NOT a stem object */
out.0 = 3
out.1 = 'first'
out.2 = 'second'
out.3 = 'third'

/* Stem object */
s = .Stem~new
s['key'] = 'value'
do item over s~allItems
    say item
end
```

### Iteration summary

| Structure | Iterate values | Index available |
|-----------|---------------|-----------------| 
| Compound variables (stem convention) | `do i = 1 to out.0` | `i` |
| Stem object (`.Stem`) | `do item over s` | `do i over s~allIndexes` |
| `.Array` object | `do item over arr` | `do i over arr~allIndexes` |

### Rule
Never apply `~allIndexes`, `~size`, or other methods to compound
variables sharing a stem — they have no methods.
Never use the `stem.0` count convention on a `.Stem` object or
`.Array` object — they do not use that convention.

| Date | Rule added | Triggered by |
|------|-----------|--------------|
| 2026-05-07 | Stem/compound variable/stem object distinction | AI conflating all three |

---

## `signal` — not a `goto`; flushes the entire call stack

[CRITICAL]

This rule applies to all Rexx variants (classic Rexx, ooRexx, NetRexx,
TSO/E REXX, Regina). It is a fundamental property of the language, not
an ooRexx extension.

`signal` is **not** a `goto`. It does not pop the stack to a prior
level — it **flushes the entire call stack**, terminating all active
`call` frames and `do`/`select`/`loop` blocks in the current execution
context.

### What `signal` does

- Transfers control to a label in the **current routine** only.
- Drops all pending `do`/`select`/`loop` blocks unconditionally.
- Terminates all active `call` frames — the entire stack is gone,
  not unwound to the nearest handler.
- In ooRexx, `signal` within a method body similarly exits all nested
  blocks in that method.

### What `signal` does NOT do

- Does **not** return to the caller (use `return` for that).
- Does **not** resume a `do` loop at the next iteration (use `iterate`).
- Does **not** exit only the innermost block (use `leave`).
- Does **not** behave like a `goto` in C or PL/I — those preserve the
  call stack; `signal` destroys it.
- Does **not** unwind to a matching handler like Java/C++ exceptions —
  use `call on` (see below) if you need resumable condition handling.

### `signal on` vs `call on` — condition handling

Both `signal on` and `call on` trap conditions (errors, syntax faults,
etc.), but they differ critically in stack behaviour.

**`signal on`** — non-resumable. Flushes the stack on condition.
The handler label runs in a clean context; there is no way to resume
the interrupted code.

```rexx
signal on error name cmdErr
signal on syntax name syntaxErr

cmdErr:
    say 'Command error at line' sigl '(rc='rc')'
    exit 1

syntaxErr:
    say 'Syntax error at line' sigl
    exit 1
```

**`call on`** — resumable. Available in ooRexx and some other
implementations (not in classic ANSI Rexx or TSO/E REXX). The
condition handler runs as a subroutine call; on `return`, execution
resumes after the statement that raised the condition.

```rexx
/* ooRexx -- call on for resumable error handling */
call on error  name cmdErr
call on syntax name syntaxErr

/* ... main logic ... */

cmdErr:
    say 'Command error at line' sigl '(rc='rc') -- continuing'
    return   /* resumes after the failing statement */

syntaxErr:
    say 'Syntax error at line' sigl '-- cannot resume'
    exit 1
```

Use `call on` in ooRexx when the handler needs to inspect and
potentially recover; use `signal on` when the condition is always fatal
or when portability to classic Rexx is required.

`call on` is **not** available in classic ANSI Rexx, TSO/E REXX, or
OBJREXX 6.00 (ArcaOS). Use `signal on` in those environments.

### Wrong: using `signal` as a structured exit from nested blocks

```rexx
/* WRONG -- signal from inside a do loop flushes the stack;
 * the loop and any enclosing call frames are gone */
do i = 1 to 10
    if i = 5 then signal done   /* destroys call stack */
end
done:
    say 'reached done'

/* CORRECT -- use leave to exit the loop */
do i = 1 to 10
    if i = 5 then leave
end
say 'loop exited at i='i

/* CORRECT -- use return to exit a subroutine */
myProc: procedure
    if bad then return
    /* ... */
    return
```

### `signal` in the session script context

The `skipGitHub:` label in `session-2026-05-02.rex` uses `signal`
correctly: it is at the top level of the script (no enclosing `call`
frames to lose), and it transfers forward to skip steps 13–14 when
`gh auth status` fails. This is an appropriate use.

Using `signal` inside a `procedure` or `method` to skip to a label
outside that routine is a bug — the label is not reachable from inside
the procedure's scope.

| Date | Rule added | Triggered by |
|------|-----------|--------------|
| 2026-05-11 | `signal` is not `goto`; flushes stack; `call on` for resumable handling | Session-script `skipGitHub` label; general LLM confusion |

---

## Placeholder names and general conventions

See `../CONVENTIONS.md` for cross-language conventions including
`foo`/`bar`/`baz` placeholder names and the `*office` shorthand
(OpenOffice / LibreOffice / FooOffice).

| Date | Rule added | Triggered by |
|------|-----------|--------------|
| 2026-05-11 | Cross-reference to CONVENTIONS.md | foo/bar/baz and *office moved to root |

---

## Stems vs .Array — when each is required

[IMPORTANT]

Prefer `.Array` objects over stem-simulated arrays for all general
collection use. The classic stem convention (`foo.0`, `foo.1`, ...) is
a Rexx idiom; ooRexx provides proper collection classes.

```rexx
/* Prefer .Array */
items = .Array~of('alpha', 'beta', 'gamma')
do item over items
    say item
end

/* Not this */
items.0 = 3
items.1 = 'alpha'
items.2 = 'beta'
items.3 = 'gamma'
do i = 1 to items.0
    say items.i
end
```

### Required exception: ADDRESS with captured output

The `ADDRESS` instruction's `WITH OUTPUT STEM` and `WITH ERROR STEM`
clauses require stems — there is no `.Array` equivalent for captured
command output. This is a language constraint, not a style choice.

```rexx
/* Required -- stems mandatory here */
address system 'gh repo list' with output stem out. error stem err.
do i = 1 to out.0
    say out.i
end
```

| Date | Rule added | Triggered by |
|------|-----------|--------------|
| 2026-05-11 | Prefer .Array over stem simulation; ADDRESS stem exception | Script using stem arrays where .Array was appropriate |

---

## SysFileExists on non-system drives

`SysFileExists` may return 0 (false) for files on removable or network
drives (e.g., drive E:, F:, network shares) even when the file exists.
This is an environment-dependent limitation.

**Workaround:** use `cmd /C dir` and check the return code instead:

```rexx
address system 'cmd /C dir "'path'" >NUL 2>NUL'
fileFound = (rc = 0)
```

This works reliably for all drive letters. Reserve `SysFileExists` for
paths on the system drive (C:) where it is reliable.

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-05-14 | SysFileExists unreliable on non-system drives | E:\temp\LCS.zip not found by SysFileExists but visible to cmd dir |

## parse source — getting the script's own path

`parse source` returns information about the running script:

```rexx
parse source OS Cmd Sfile
```

- `OS`    — operating system (e.g., `WindowsNT`, `OS/2`)
- `Cmd`   — invocation type (`COMMAND`, `SUBROUTINE`, `FUNCTION`, `METHOD`)
- `Sfile` — full path to the script file

Use `filespec(PATH, Sfile)` to extract the directory, and
`filespec(NAME, Sfile)` to extract the filename.

**Typical use:** locating companion files (data files, class packages)
in the same directory as the script, without hardcoding paths:

```rexx
parse source . . Sfile
Sdir = filespec('PATH', Sfile)
dataFile = .stream~new(Sdir'names')
```

This is the pattern used in `Hebrew.cmd` to locate its `names`
transliteration table.

**Note:** in a `::METHOD` context, `parse source` returns the path of
the `.cmd` or `.rex` file that contains the class, not the caller.

| Date | Entry | Triggered by |
|------|-------|--------------| 
| 2026-05-15 | parse source | Hebrew.cmd usage; user question |


## Uninitialized symbols are uppercased

In REXX, an uninitialized symbol evaluates to its own name in uppercase.
For example, if `foo` has not been assigned a value, `foo` evaluates to
`'FOO'`. This means:

- Unquoted lowercase words that look like identifiers may silently
  evaluate to their uppercased names rather than causing an error.
- Any word that must retain its lowercase form must be in a string
  literal (`'foo'`) or assigned to a variable before use.
- This applies to all REXX dialects including ooRexx.

**Common mistake:** passing an unquoted lowercase keyword or option to
`address system` or a function, expecting it to be treated as a string.

```rexx
/* Wrong: 'force' evaluates to 'FORCE' if uninitialized */
address system 'git' force

/* Correct: use a string literal */
address system 'git --force'
```

| Date | Entry | Triggered by |
|------|-------|--------------| 
| 2026-05-15 | Uninitialized symbols are uppercased | User note on lowercase symbols |

## Quote/apostrophe variables for complex string building

When building strings that require both single and double quotes,
define variables at the top of the script:

```rexx
q = '"'   /* double-quote character */
a = "'"   /* apostrophe character   */
```

Then use them to construct complex strings without nesting or escaping:

```rexx
cmd = 'gh api graphql -f query=' a 'mutation { foo(id: ' q nodeId q ') }' a
```

This avoids the need for PS1 helper files or escaped quote sequences.
Applies to any context where quote nesting would otherwise be needed.

| Date | Entry | Triggered by |
|------|-------|--------------| 
| 2026-05-16 | Quote/apostrophe variables | User suggestion re: gh GraphQL quoting |

## SysFileTree — scanning directories for files

`SysFileTree(fileSpec, stem, options)` populates a stem with matching
file paths. Returns 0 on success.

```rexx
call SysFileTree 'C:\Users\Owner\Downloads\*.ps1', files., 'FO'
do i = 1 to files.0
    say files.i   /* full path of each match */
end
```

Options (combinable):
- `F` — files only (no directories)
- `O` — only return names (no size/date prefix)
- `D` — directories only
- `S` — recurse into subdirectories
- `T` — include timestamps in output
- `B` — both files and directories

`fileSpec` supports `*` and `?` wildcards. The stem index `0` holds the
count; indices `1` through `stem.0` hold the paths.

Use `SysFileTree` in preference to `cmd /C dir /B` when you need the
results in ooRexx variables rather than external output.

| Date | Entry | Triggered by |
|------|-------|--------------| 
| 2026-05-16 | SysFileTree | User suggestion for bin/Downloads monitoring |

## Python file writes: use raw strings or binary mode for ooRexx scripts

When Claude writes ooRexx scripts via Python, backslash sequences in
Python strings are interpreted as escape codes. `\f` becomes form feed
(0x0C), `\n` becomes newline, `\t` becomes tab, etc.

To avoid corruption when writing ooRexx code containing `\` (logical
not operator):
- Use raw strings: `r'\found'` instead of `'\found'`
- Or write in binary mode and encode explicitly
- Or double the backslash: `'\\found'`

Always verify with `cat -A` after writing if `\` operators are involved.

| Date | Entry | Triggered by |
|------|-------|--------------| 
| 2026-05-16 | Python backslash escapes corrupt ooRexx \\ operators | form feed at line 224 |

## DATE() and TIME() functions

<!-- See also: Rexx-RULES DATE()/TIME() -->
For the full `DATE()` and `TIME()` format table, see
[Rexx-RULES.md](../Rexx/RULES.md) — DATE()/TIME() section.

## String literal framing and quoting

<!-- ooRexx string literal framing: use opposite delimiter -->
Use the framing delimiter that avoids escaping:
- Literal contains apostrophes but no double quotes: frame with `"`
- Literal contains double quotes but no apostrophes: frame with `'`
- Literal contains both: frame with whichever requires fewer doublings.
To include the framing delimiter literally, double it:
- `''''` inside `'...'` produces one apostrophe
- `""` inside `"..."` produces one double quote

## Caseless string comparisons

<!-- ooRexx caseless string methods: prefer over translate() -->
Prefer ooRexx caseless string methods over `translate()` in new code:
- `str~caselessEquals(other)` instead of `translate(str) = translate(other)`
- `str~caselessPos(needle)` instead of `pos(needle, translate(str))`
- `str~caselessCompareTo(other)` for ordering
`translate()` is classic Rexx and portable; caseless methods are ooRexx-specific but cleaner.
