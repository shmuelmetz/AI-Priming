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

## `address...with` I/O types

Valid I/O redirect types in the `address...with` clause are:
`NORMAL`, `STEM`, `STREAM`, and `USING`. `STRING` is not valid.

To provide empty stdin (preventing interactive blocking):

```rexx
noIn.0 = 0
address system cmd with output stem out. error stem err. input stem noIn.
```

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-06-01 | Valid WITH I/O types | `input string ''` rejected with Error 25.933 |

---

## Prefer infozip over PowerShell for zip operations

[IMPORTANT]

Use PowerShell only when there is no working alternative. For zip
inspection and extraction, always prefer infozip (`unzip.exe`) via
`address system` rather than PowerShell's `System.IO.Compression`
or `Expand-Archive`.

**Rationale:** PowerShell startup is slow and can hang in some
execution contexts (e.g., when run from a GUI-less session or under
certain priority settings). `infozip` is a plain console binary with
no such latency.

**Pattern for zip membership test** (replaces PowerShell `ZipFile`):

```rexx
/* Returns .TRUE if zipFile contains entry, .FALSE otherwise */
noIn.0 = 0
address system 'cmd /C \"\"'infoUnzipBin'\" -l -q \"'zipFile'\"\"' ,
    with output stem zcOut. error stem zcErr. input stem noIn.
zcRc = rc
found = .FALSE
do i = 1 to zcOut.0
    parse var zcOut.i . . . zcName   /* fields: length  date  time  name */
    if strip(zcName) = entry then do; found = .TRUE; leave; end
end
```

**Pattern for zip extraction** (use infoUnzipBin, not Expand-Archive):

```rexx
address system 'cmd /C \"\"'infoUnzipBin'\" -j -o -q \"'zipFile'\" member -d \"'destDir'\"\"'
```

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-06-02 | Prefer infozip over PowerShell for zip ops | zipContains hung using PS ZipFile class |

---

## DATE() and TIME() functions

<!-- See also: Rexx-RULES DATE()/TIME() -->
For the full `DATE()` and `TIME()` format table, see
[Rexx-RULES.md](../Rexx/RULES.md) — DATE()/TIME() section.

---

## String literal framing and quoting

<!-- ooRexx string literal framing: use opposite delimiter -->
Use the framing delimiter that avoids escaping:
- Literal contains apostrophes but no double quotes: frame with `"`
- Literal contains double quotes but no apostrophes: frame with `'`
- Literal contains both: frame with whichever requires fewer doublings.
To include the framing delimiter literally, double it:
- `''''` inside `'...'` produces one apostrophe
- `""` inside `"..."` produces one double quote

---

## Caseless string comparisons

<!-- ooRexx caseless string methods: prefer over translate() -->
Prefer ooRexx caseless string methods over `translate()` in new code:
- `str~caselessEquals(other)` instead of `translate(str) = translate(other)`
- `str~caselessPos(needle)` instead of `pos(needle, translate(str))`
- `str~caselessCompareTo(other)` for ordering
`translate()` is classic Rexx and portable; caseless methods are ooRexx-specific but cleaner.

---

## Template receivers vs. match patterns

In a `parse` template, a variable **not** enclosed in parentheses is a
**receiver**: it is assigned the next parsed token/field from the source
string. It does **not** match against the variable's current value.

A variable enclosed in parentheses `(foo)` is a **match pattern**: REXX
uses `foo`'s current value as a literal string to scan for in the
parse source.

```rexx
parse var line word rest     /* word receives first token; rest gets remainder */
parse var line (delim) rest  /* scans line for value of delim; rest follows it */
```

Confusing these is a silent logic error: the unparenthesized form always
succeeds and overwrites the variable; the parenthesized form may leave
variables empty if the pattern is not found.

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-06-05 | Template receiver vs. pattern | Session item 3 |

---

## Prefer `parse var` over `parse value ... with`

When the parse source is already a variable, use `parse var`:

```rexx
parse var foo template
```

not:

```rexx
parse value foo with template
```

`parse var` is more readable, saves a keyword, and makes the source
obvious at a glance. Reserve `parse value ... with` for expression
sources:

```rexx
parse value foo || bar with template
parse value myFunc()   with template
```

**This is not just a style preference for collection-indexed access.**
`parse var` requires an actual variable name as its target; an indexed
expression like `dir['out']` or `arr[1]` is not a legal `parse var`
target and is a hard syntax/parse error, not merely less readable. Any
time the source is a collection element (a `captureCmd`/`wrapCmd`
result's `['out']`/`['err']`, a stem tail, an array index), use
`parse value` with the expression, not `parse var`:

```rexx
result = captureCmd('...')
/* result['out'] is a .Array of lines, not a string -- index it first */
parse value result['out'][1] with template
```

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-06-05 | Prefer `parse var` over `parse value ... with` | Session item 4 |
| 2026-07-11 | Indexed expressions are not legal `parse var` targets -- use `parse value` | Bug caught wiring next-pending.rex into session-2026-05-02.rex (captureCmd `['out']` is a `.Array`, not a string) |

---

## stem.tail syntax: inherited from Rexx; ooRexx adds no new restrictions

The stem compound-variable syntax (`stem.tail`) is inherited unchanged
from classic Rexx. ooRexx does not add new restrictions on tail
expressions. However, the idiom of using stems to simulate arrays
(`stem.0` as count, `stem.1`...`stem.N` as elements) is superseded
by `.Array`. Do not use stems for array simulation in new ooRexx code.

### .Array methods to know

| Method | Purpose |
|--------|---------|
| `~append(item)` | Add item at end |
| `~insert(item, idx)` | Insert before index (omit idx to prepend) |
| `~allindexes` | Supplier of all integer indexes |
| `~allitems` | Supplier of all values |
| `~items` | Count of non-empty slots |
| `~size` | Highest occupied index |
| `~at(idx)` / `arr[idx]` | Retrieve by index |

Iteration: `do x over arr` visits all non-empty items in index order.

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-06-07 | stem.tail inherited from Rexx; .Array methods | Session item |

---

## When to replace independent routines with a class and methods

*(This belongs in Rexx-RULES.md, not here -- it's a Rexx-family rule
[classic Rexx, ooRexx, NetRexx], not ooRexx-specific. Rexx-RULES.md
wasn't in this session's zip, so I can't move it there without risking
clobbering content I haven't seen -- upload it if you want this
relocated properly. Noting the general-scope correction in place for
now.)*

Look for cases where replacing independent routines with a class and
methods leads to cleaner code.

If you need access to the caller's variables, consider packaging things
as objects rather than reaching for shared/exposed variable scope.

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-07-11 | When to replace independent routines with a class | User-stated rule |
| 2026-07-11 | Rule restated in general form -- earlier version added specific trigger conditions (shared state, setup/teardown) that weren't in the original rule | User correction: "too specific; my rule was deliberately general" |
| 2026-07-11 | Clarified scope: applies to Rexx-like languages generally (Rexx, ooRexx, NetRexx), not ooRexx specifically; added caller-variable-access corollary | User: "My intent was for that to be a persistent hint for, e.g., rexx, oorexx, netrexx" / "If need access to the callers variables, consider packaging things as objects." |


## Stream I/O: prefer stream methods over BIFs

<!-- ooRexx stream I/O: prefer stream methods over BIFs -->
Prefer ooRexx object methods over classic BIFs for stream I/O:
- Use `stream~command('OPEN WRITE REPLACE')` before full-file writes
  instead of relying on `SysFileDelete` + `lineout` (which appends
  to a new file and silently duplicates content if the delete fails).
- Use `stream~command('CLOSE')` or `stream~close()` to flush.
- `lineout(file, content)` opens in append mode; always precede a
  full-file overwrite with `call stream file, 'C', 'OPEN WRITE REPLACE'`
  (BIF form) or `stream~command('OPEN WRITE REPLACE')` (method form).
- For new ooRexx code, prefer the method form on a `.Stream` object:
  ```
  s = .Stream~new(path)
  s~command('OPEN WRITE REPLACE')
  s~lineout(content)
  s~close()
  ```
Classic BIF form (`call stream file, 'C', 'OPEN WRITE REPLACE'`) is
acceptable in existing code; the key invariant is the `OPEN WRITE REPLACE`
before any full-file write.
