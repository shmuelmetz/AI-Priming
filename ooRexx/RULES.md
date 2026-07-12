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

---

## Avoid single-character variable names

*(Rexx-family rule, not ooRexx-specific -- see note above about
Rexx-RULES.md not being in this session's zip.)*

Single-character variable names are a real hazard in Rexx, not just a
style nit: a symbol placed immediately after a closing quote can collide
with typed-literal suffixes (`'1010'B` binary, `'FF'X` hex, and others),
silently changing what the parser thinks the expression means. `a'|'b`
was written intending "concatenate a, the literal |, and variable b" but
parses as "the string `|` interpreted as a **binary literal with suffix
B**" -- caught live this session, producing "Only 0, 1, and blank are
valid in a binary string; found '|'", a confusing error that pointed
nowhere near the actual (single-character-name) cause. Use descriptive
multi-character names; if a loop index or throwaway needs to be short,
two-plus characters (`ix`, `dir`, `iz`) is enough to avoid the collision
class entirely.

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-07-11 | Avoid single-character variable names | User: "isn't there a rule bout one character variable names" -- prompted by a `'|'b` typed-literal-suffix collision hitting exactly this class of bug |

## Avoid INTERPRET; there are usually cleaner ways

*(Rexx-family rule, not ooRexx-specific.)*

`INTERPRET` builds and executes code from a string at runtime. It's
occasionally the only tool for the job, but it's usually reached for
before checking whether the language already has a direct feature for
what's needed -- e.g. an indirect external-routine call by computed name
is `CALL (expr) args` (the parenthesised form, evaluated once and used
as the call target -- see "Indirect external calls" below), not
`INTERPRET 'call ''' name ''' args'`. Prefer the direct language feature;
reach for `INTERPRET` only once it's confirmed nothing else does the job.

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-07-11 | Avoid INTERPRET; there are usually cleaner ways | User-stated rule, in response to Claude reaching for INTERPRET to work around an indirect-call syntax question that had a direct answer |

## Indirect external calls: `CALL (expr) args`

`CALL (expr) args` -- an expression in parentheses, not a bare
name/variable -- evaluates `expr` first, and its value becomes the call
target (checked against label names and built-in function names
case-sensitively; the language processor does not uppercase it).
This is the correct way to call a routine whose name/path is held in a
variable:

```rexx
nextPendingExe = toolsDir'\PriorityQueue\next-pending.rex'
call (nextPendingExe) queueFile, sentinelDir, sentinelPrefix
result1 = result
```

A bare `CALL variableName args` (no parens) does **not** dereference the
variable -- it uses the literal symbol text `VARIABLENAME` as the call
target, which is almost never what's wanted and fails confusingly (looks
like a missing-program error, not a variable-vs-literal mixup).

Caution: this specific parenthesised form is confirmed from the ooRexx
language reference, but Regina (classic Rexx) rejects it outright
("String or symbol expected after CALL keyword; found '('") -- it's an
ooRexx extension beyond the ANSI/TRL2 baseline, not something to assume
works unmodified on every Rexx implementation. Verify on the actual
target interpreter (ooRexx here) before relying on it cross-implementation.

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-07-11 | Indirect external calls: `CALL (expr) args` | User quoted the ooRexx language reference after Claude's Regina-based test of this syntax failed and Claude wrongly concluded the syntax itself was invalid, rather than that Regina doesn't implement this ooRexx extension |

## JSON class (`::REQUIRES "json.cls"`)

Bundled with ooRexx 5.x, not yet used anywhere in `session-2026-05-02.rex`.
`.JSON~toString(object)` / `.JSON~fromString(string)` (or via the `json`
mixin's `~makeJSON`/instance parsing, depending on version) convert
between Rexx objects (Directory/Array/String/numbers) and JSON text.
Candidate use case identified but not yet acted on: `download-priority.txt`
is flat pipe-delimited text (`CD01|Title|URL|Bytes`), which is already
straining now that `CD13B` needs adding (see Book-CD-Archive catalog) --
pipe-delimited text doesn't nest, JSON does. Not switched over yet;
flat text has been sufficient so far and a format change mid-project
isn't free. Reach for this if the catalog's structure keeps growing.

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-07-12 | JSON class noted as available, candidate use case identified | User question: "use case for ::requires json.cls?" |

## Validate class

Bundled with ooRexx 5.x. Class methods for asserting argument
correctness -- **each one raises a SYNTAX error on failure and returns
nothing on success** (it's an assertion, not a `.true`/`.false` test).
Supersedes the deprecated `ArgUtil` class.

| Method | Validates | Digits default |
|---|---|---|
| `classType(name, object, class)` | `object` is an instance of `class` | -- |
| `requestClassType(name, object, class)` | `object` convertible to `class` via `request`; returns the converted object | -- |
| `length(name, number, digits)` | zero or positive whole number | `internalDigits` |
| `position(name, number, digits)` | positive whole number | `internalDigits` |
| `logical(name, number)` | `.true` or `.false` | -- |
| `number(name, number)` | valid Rexx number | -- |
| `wholeNumber(name, number, digits)` | whole number | 9 |
| `nonNegativeNumber(name, number, digits)` | zero or positive number | 9 |
| `positiveNumber(name, number, digits)` | positive number | 9 |
| `nonNegativeWholeNumber(name, number, digits)` | zero or positive whole number | 9 |
| `positiveWholeNumber(name, number, digits)` | positive whole number | 9 |
| `numberRange(name, number, min, max, digits)` | number within range | 9 |
| `wholeNumberRange(name, number, min, max, digits)` | whole number within range | 9 |

Candidate use case identified but not yet wired in: `baen-extract.rex`'s
`dryRun`/`verbose`/`overwrite` arguments currently get informally coerced
(`(dryRun \= 0)`) rather than validated; `download-priority.txt`'s
`cdBytes` field is used as a byte count without confirming it's numeric
first. Since `.Validate` raises SYNTAX on failure, wiring it in requires
getting `SIGNAL ON SYNTAX` scoping right -- see the routine-encapsulation
rule below, which resolves the specific problem that blocked doing this
during this session's download/extraction work.

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-07-12 | Validate class documented in full, candidate use cases identified | User uploaded rexxref.pdf and asked to retain this persistently |

## Encapsulate in a routine or method to limit SIGNAL SYNTAX (etc.) side effects

Per the ooRexx language reference (§2.25, RETURN): **"If no internal
routine (subroutine or function) is active, RETURN and EXIT are
identical in their effect on the program that is run."** This means a
`SIGNAL ON SYNTAX NAME label` trap set in the mainline, whose label ends
in a bare `RETURN`, does not "fall through and resume" the way it might
look like it should -- it terminates the program, same as EXIT, because
no internal routine is active at that point.

If code needs to trap a condition (SYNTAX or otherwise) and have it
*not* propagate out to a caller/mainline, wrap the guarded operation in
its own routine or method (an internal label reached via `CALL`, a
`::ROUTINE`, or a `::METHOD`) and set the condition trap *inside* that
routine. With an internal routine active, `RETURN` behaves as a genuine
return-to-caller, not as EXIT, and per §11.2 ("Action Taken when a
Condition Is Trapped"): "The state ... of each condition trap is saved
on entry to a subroutine and is then restored on RETURN" -- so the trap
is automatically scoped to that routine's invocation and doesn't need
manual save/restore of the caller's trap state either.

This is the fix for the specific problem hit this session: a `SIGNAL ON
SYNTAX` guard set directly around a mainline `CALL (expr) args` (to
protect against a bug in a small Tools-repo utility aborting the whole
session run) was abandoned as unverifiable via Regina testing. The
actual fix is to put the guarded `CALL` inside a small wrapper routine
of its own, not to guard it in place in the mainline.

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-07-12 | Encapsulate in a routine/method to limit SIGNAL SYNTAX side effects | User-stated rule, following the discovery that `ignErr`'s mainline-level `SIGNAL ON` + bare `RETURN` may not behave as its own comment claims |


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
