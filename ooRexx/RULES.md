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

### `~of(...)` — one-call construct-and-populate

`~of` is a general **Collection** class method, not something
specific to `.Array` — every collection class exposes it, but the
calling convention splits along the same ordered-vs-keyed line as the
rest of the hierarchy. Verified directly against the interpreter,
2026-08-27, across `Array Table Set Bag Queue List Relation Directory
IdentityTable StringTable CircularQueue`:

```rexx
/* Ordered/unkeyed collections: each argument is a plain item,
 * appended in order -- Array, Set, Bag, Queue, List, CircularQueue. */
arr = .Array~of('a', 'b', 'c')          /* arr[1]='a', arr[2]='b', ... */
set = .Set~of('a', 'b', 'c')
queue = .Queue~of('a', 'b', 'c')

/* Keyed collections: each argument must itself be a single-dimensional
 * array holding (index, item) -- Table, Relation, Directory,
 * IdentityTable, StringTable. A bare value fails immediately:
 * "OF argument 1 must be a single-dimensional array; found "x"." */
dir = .Directory~of(.Array~of('key1', 'val1'), .Array~of('key2', 'val2'))
say dir~at('key1')                       /* val1 */

tbl = .Table~of(.Array~of(1, 'a'), .Array~of(2, 'b'))
say tbl~at(1)                            /* a */
```

`.Array~of(...)` is simply the simplest case of this — the one where
"item" and "index" coincide with sequential position, so no explicit
pairing is needed. Prefer it over `.Array~new` plus a run of
`~append` calls when the full contents are known up front as a
literal list; this is the direct object-collection replacement for
the classic `Withheld.0 = 3 / Withheld.1 = '...' / ...` stem-array
idiom.

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
| 2026-08-27 | `~of(...)` is a general Collection method, not Array-specific | User: "it's basically a collection class method, but .array is the simplest case" -- verified across 11 collection classes before writing up the ordered-vs-keyed calling-convention split |

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
address system '"'infoUnzipBin'" -l -q "'zipFile'"' ,
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
address system '"'infoUnzipBin'" -j -o -q "'zipFile'" member -d "'destDir'"'
```

Note: the original version of these two patterns wrapped the command in
`cmd /C \"\"...\"\"` — doubled, backslash-escaped quotes. That extra
`cmd /C` layer is unnecessary and, per the dedicated rule below, actively
wrong once real-world paths or arguments contain their own quotes.
`address system` already dispatches straight to the native shell; passing
a plain quoted command string (as above) is both simpler and correct.

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-06-02 | Prefer infozip over PowerShell for zip ops | zipContains hung using PS ZipFile class |
| 2026-08-26 | Removed `cmd /C` wrapper from these examples | See "Never wrap commands in `cmd /c`" below |

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

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-06-05 | Prefer `parse var` over `parse value ... with` | Session item 4 |

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

---

## Never wrap commands in `cmd /c`

[IMPORTANT]

Do not wrap `address system` commands in `cmd /c "..."`, even on
Windows, even when the alternative "feels" like it needs a shell.
`address system` already dispatches straight to the platform's native
shell — an explicit `cmd /c` layer is redundant at best.

**It is actively wrong, not just redundant, on Windows:** once the
wrapped command itself contains quoted arguments — a commit message
with spaces, a path with spaces, anything needing its own `"..."` —
passing the whole thing through `cmd /c "..."` mangles the nested
quoting. `cmd.exe`'s quote parser does not handle multiple nested
quoted segments reliably, and the outer `cmd /c` layer adds one more
level than necessary. This is why the bug keeps recurring: it appears
to work on trivial test commands with no embedded quotes, then breaks
the first time someone runs it against real input.

```rexx
/* WRONG -- extra cmd /c layer, breaks once cmd itself has quotes */
address system 'cmd /c cd "' || repoDir || '" && git commit -m "' || msg || '"'

/* CORRECT -- address system already picks the right native shell */
address system 'cd "' || repoDir || '" && git commit -m "' || msg || '"'
```

The same applies to drive-letter switching before a `cd` on Windows
(`C: && cd "..."`) — unnecessary; a quoted `cd "C:\full\path"` changes
drive and directory together.

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-08-26 | Never wrap commands in `cmd /c` | Recurring bug in `git-commit.rex`/`web-commit.rex`; user reported it breaking on Windows itself, not just non-Windows platforms |

---

## Indirect/computed stem access: three forms, only one is safe per context

[IMPORTANT]

There are two *different*, non-interchangeable stem mechanisms in
ooRexx, and mixing up their access syntax produces bugs ranging from a
hard error to a silent wrong answer.

**1. Classic compound variable** (`mystem.1 = 'x'` style storage).
Indirect/computed tail access is `mystem.[expr]` — dot, then bracket:

```rexx
mystem.1 = 'one'
mystem.2 = 'two'
mystem.3 = 'three'
i = 3
say mystem.[i]        /* CORRECT: 'three' */
```

Two wrong forms for this case, verified by direct test:

```rexx
say mystem.(i)         /* WRONG: Error 43, "routine not found" --
                           parsed as a call to a function literally
                           named MYSTEM. */

say mystem[i]           /* WRONG, but does NOT error -- silently
                           returns the wrong value. See below. */
```

The no-dot bracket form is the more dangerous of the two precisely
because it never raises an error. An otherwise-unset simple variable
`mystem` evaluates to the string of its own name (`"MYSTEM"`), and
bracket notation on a bare string invokes ooRexx String's `[]` method,
which does *character indexing*. `mystem[3]` in the example above
silently returns `"S"` (the 3rd character of `"MYSTEM"`) — a
plausible-looking value that is simply wrong.

**2. Real Stem collection object** (`mystem = .stem~new`). Once
`mystem` holds an actual `.stem` instance, plain bracket notation is
correct and idiomatic — this is the genuine "use a collection object"
pattern, not classic-compound-variable simulation:

```rexx
mystem = .stem~new
mystem[foo] = bar
say mystem[foo]        /* CORRECT */
```

**Do not mix the two on what's meant to be the same collection.** A
real Stem object's bracket-indexed storage is a separate namespace
from classic `mystem.tail` compound variables of the same base name —
after `mystem = .stem~new; mystem[3] = 'bar'`, plain `mystem.3` does
**not** see `'bar'`; it still shows the uninitialized
`"MYSTEM.3"`.

**3. Using another compound variable directly as a tail component**
(`stem.othercompound.0`) does not do what it looks like, and — unlike
the two cases above — it is *valid* syntax, so nothing ever flags it:

```rexx
orphans.0 = 0
orphans.0 = orphans.0 + 1
orphans.orphans.0 = 'first'      /* WRONG: does not mean "orphans.1" */
orphans.0 = orphans.0 + 1
orphans.orphans.0 = 'second'     /* WRONG: silently overwrites the same slot */

say orphans.1                     /* uninitialized: "ORPHANS.1" */
say orphans.orphans.0             /* 'second' -- 'first' is gone */
```

The tail is split on periods into independent pieces *before* any
substitution happens — here `["orphans", "0"]` — and each piece is
resolved on its own as a plain simple-variable lookup (or left
literal if purely numeric). A piece is never itself re-parsed as a
compound-variable reference, so there is no way to splice in "the
current value of `orphans.0`" by writing its dotted name inline. In
the example, piece `"orphans"` looks up the simple variable `ORPHANS`
(never assigned, so it defaults to its own name, `"ORPHANS"`) and
piece `"0"` is numeric and stays literal — giving the single **fixed**
derived tail `"ORPHANS.0"` every time, regardless of the stem's actual
count. Every iteration clobbers the same variable
(`ORPHANS.ORPHANS.0`) instead of indexing a growing list, and every
value but the last is silently lost with no error at any point.
Verified by direct test, not reasoned from the syntax alone.

The fix is the same indirection already shown for case 1: copy the
index into a plain simple variable first, then use it as the tail —
`n = orphans.0; orphans.n = value` — a bare simple-symbol tail like
`n` needs no bracket at all; `.[expr]` is only for tails more complex
than a single symbol.

**Related, easy to trip over while testing the above**: a quoted
string literal can never BE a tail either, even when it contains no
dots — `stem.'some/value'` is not "a compound variable with literal
tail `some/value`". Tails are lexical pieces of one symbol token, and
a symbol can only contain letters, digits, `.`, `_`, `!`, `?` — a
slash breaks the token immediately. `stem.'x' = 1` parses as bare
`stem.` (the stem's own tail-less/default value) *concatenated* with
the string literal `'x'` via abuttal, which is not a valid assignment
target at all; whatever oddity results is not a compound-variable
write. The only way to make an arbitrary string (slashes included)
into a tail is symbol substitution — `p = 'some/value'; stem.p = 1` —
never a literal glued onto the stem. Confirmed 2026-08-27 debugging a
mock test for `remote-orphan-cleanup.rex` that used the literal form
and got a silently wrong (but non-crashing) result.

**Recommendation:** prefer explicitly instantiating `.stem~new` (or
`.Array`/`.Table`/`.Directory` as appropriate — see "Collection
classes" above) and using plain bracket notation throughout, rather
than classic compound variables plus the `.[expr]` indirect-tail form.
It is less error-prone and matches the general preference for ooRexx
collection objects over stem simulation.

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-08-26 | Indirect stem access: three forms | User reported "lots of stem.(expression) errors" despite the collection-object rule already being documented above |
| 2026-08-27 | Added case 3: nested compound reference as a tail component is valid but silently wrong | Real bug in `remote-orphan-cleanup.rex` (`orphans.orphans.0 = remRel`), caught by mock-data test before running live; my own first fix comment mischaracterized it as "invalid" until the user corrected it |
| 2026-08-27 | Added: a quoted string literal can never be a tail | Debugging a retest of the case-3 fix above -- a mock test written with `stem.'literal'` produced a silently wrong, non-crashing result |

---

## Prefer chained methods over nested function calls — including for plain character strings

[IMPORTANT]

Every ooRexx string is a `.String` object with methods. The chained-method
style isn't reserved for "real" objects like `.Array`/`.Directory` — it
applies just as much to ordinary character-string manipulation, where
classic-Rexx habit reaches for nested BIF calls instead:

```rexx
/* WRONG -- classic-Rexx nested-function style */
result = translate(substr(str, 1, 5))
result = strip(space(translate(str)))

/* CORRECT -- ooRexx: chain methods on the string object */
result = str~substr(1, 5)~translate
result = str~translate~space~strip
```

Nested function calls read inside-out (evaluate the innermost call
first to understand what happens first); chained methods read
left-to-right in actual execution order, which is why ooRexx style
prefers them even when every value involved is "just a string." This
generalizes the same "collection objects over stem simulation"
preference already documented above (see "Collection classes"): the
underlying point in both cases is that ooRexx values are objects with
methods, and BIF/nested-function style is the classic-Rexx fallback,
not the ooRexx-idiomatic default.

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-08-27 | Chained methods over nested functions, including for strings | User style guideline, given while reviewing a Pygments ooRexx lexer under construction |
