# Python Rules for AI Priming

Rules and corrections for AI engines working with Python. These
address specific errors observed in AI-generated Python code during
sessions starting 2026-08-27 (initial seeding from the
`pygments-extensions` project).

Treat this document as ground truth. Where your training contradicts
these rules, defer to this document and to the references in
BIBLIOGRAPHY.md (once one exists for this language).

---

## Style conventions: indentation, naming case

[IMPORTANT]

These follow PEP 8 (https://peps.python.org/pep-0008/), the
authoritative Python style guide, cross-checked against Pygments' own
codebase since matching upstream style matters for PR acceptance.

**Indentation:** 4 spaces per level. Never tabs; never mixed
tabs-and-spaces (Python 3 rejects mixed indentation outright with a
`TabError` at parse time — this is enforced by the language, not just
a style nit).

**Naming case, by kind of name:**

| Kind | Convention | Example |
|------|-----------|---------|
| Variable, function, method, module, package | `snake_case` | `token_type`, `get_lexer_by_name`, `pygments_extensions` |
| Class | `PascalCase` (a.k.a. CapWords) | `OORexxLexer`, `RegexLexer` |
| Constant | `UPPER_SNAKE_CASE` | `DEFAULT_ENCODING`, `MAX_LINE_LENGTH` |
| Internal/non-public name | leading underscore | `_internal_helper`, `_cache` |
| "Private" name needing name-mangling (rare; avoid unless actually needed) | leading double underscore | `__really_private` |

```python
# WRONG -- camelCase for a function/variable (common mistake carried
# over from Java/JS/C#-flavored training data)
def getLexerByName(lexerName):
    tokenType = ...

# CORRECT -- snake_case for functions and variables, PascalCase reserved for classes
def get_lexer_by_name(lexer_name):
    token_type = ...

class OORexxLexer(RegexLexer):   # class: PascalCase
    ...
```

A common AI-generated-code failure mode: mixing conventions within one
file (e.g. `snake_case` functions but `camelCase` local variables), or
defaulting to `camelCase` throughout because it's the dominant
convention in other training-data-heavy languages (JavaScript, Java,
C#). Python's own standard library and virtually all major frameworks
(Django, Flask, Pygments included) follow the table above consistently
— treat deviation from it as a bug to fix, not a stylistic choice.

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-08-27 | Indentation and naming-case conventions | User: "That should include things like indentation rules, camel vs snake vs ... for variable and procedure names" |

---

## Never register a plugin/entry-point for a module that doesn't exist yet

[IMPORTANT]

Many Python plugin systems built on `importlib.metadata` entry points
—including Pygments' own lexer registry—resolve a *single* named
plugin by iterating and importing **every** registered entry in the
group, not just the one being looked up. One broken or not-yet-written
entry point anywhere in the group breaks lookup of every other,
perfectly working entry too.

Reproduced directly (Pygments 2.21.0, `pygments-extensions`):
registering `pli = "pygments_extensions.lexers.pli:PLILexer"` in
`pyproject.toml` before `pygments_extensions/lexers/pli.py` existed
caused `get_lexer_by_name('oorexx')` — a *different*, fully-working
lexer — to fail outright:

```
Traceback (most recent call last):
  File "<string>", line 6, in <module>
    lexer = get_lexer_by_name('oorexx')
  File ".../pygments/lexers/__init__.py", line 129, in get_lexer_by_name
    for cls in find_plugin_lexers():
  File ".../pygments/plugin.py", line 60, in find_plugin_lexers
    yield entrypoint.load()
  File ".../importlib/metadata/__init__.py", line 179, in load
    module = import_module(match.group('module'))
ModuleNotFoundError: No module named 'pygments_extensions.lexers.ispf_dtl'
```

Root cause, read directly off the traceback: `pygments/plugin.py`'s
`find_plugin_lexers()` does `yield entrypoint.load()` for **every**
entry in the `pygments.lexers` group, unconditionally and without a
`try`/`except`, so it can inspect each candidate's `.aliases` looking
for a match — one bad entry aborts the whole generator before it ever
reaches the entry that would have matched.

**Fix:** comment out entry points for modules that don't exist yet;
never pre-declare a live entry point ahead of the code:

```toml
# WRONG -- pli.py doesn't exist yet, breaks lookup of every OTHER lexer too
[project.entry-points."pygments.lexers"]
pli = "pygments_extensions.lexers.pli:PLILexer"
oorexx = "pygments_extensions.lexers.oorexx:OORexxLexer"

# CORRECT
[project.entry-points."pygments.lexers"]
oorexx = "pygments_extensions.lexers.oorexx:OORexxLexer"

# Not implemented yet -- uncomment each line as its module is written.
# pli = "pygments_extensions.lexers.pli:PLILexer"
```

This generalizes beyond Pygments: any plugin system using
`importlib.metadata` entry points that eagerly imports the whole group
(rather than lazily resolving by name first) has the same failure
mode. Don't assume "I only touched the PL/I entry" means only the
PL/I lookup is at risk.

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-08-27 | Never register entry points for not-yet-written modules | `get_lexer_by_name('oorexx')` failing with `ModuleNotFoundError` for an unrelated, unwritten `pli` module |

---

## Flat-layout projects need an explicit package include list

[IMPORTANT]

In a flat (non-`src/`) project layout, setuptools' automatic package
discovery treats *any* top-level directory as a candidate package —
including non-code directories like `samples/` or `tests/` — and
refuses to build rather than guess once it finds more than one
candidate:

```
error: Multiple top-level packages discovered in a flat-layout: ['samples', 'pygments_extensions'].

To avoid accidental inclusion of unwanted files or directories,
setuptools will not proceed with this build.
```

**Fix:** be explicit the moment there's more than one top-level
directory:

```toml
[tool.setuptools.packages.find]
include = ["pygments_extensions*"]
```

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-08-27 | Flat-layout needs explicit packages.find include | `pip install -e .` failing with "Multiple top-level packages discovered" once `samples/` existed alongside the real package |

---

## Use SPDX license expressions, not classifier strings

The `License :: OSI Approved :: ...` classifier form is deprecated in
favor of the PEP 639 SPDX license expression:

```
Please consider removing the following classifiers in favor of a SPDX license expression:
License :: OSI Approved :: MIT License
```

**Fix:**

```toml
# WRONG (deprecated)
license = { text = "MIT" }
classifiers = ["License :: OSI Approved :: MIT License", ...]

# CORRECT
license = "MIT"
classifiers = [...]   # drop the License :: OSI Approved :: ... entry
```

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-08-27 | SPDX license expression over classifier string | Deprecation warning on `pip install -e .` |

---

## `\s*` crosses newlines — use `[ \t]*` for same-line whitespace

[IMPORTANT]

In Python's `re` module (and most regex engines), `\s` matches
newlines too. A pattern intended to mean "optional whitespace on the
same line" that uses `\s*` will silently cross into the next line.

This caused a real, test-caught bug in an ooRexx lexer: a label
pattern's trailing `\s*` swallowed a newline plus the next line's
leading `::`, corrupting an unrelated adjacent token — the label rule
"ate" a token it should never have touched.

```python
# WRONG -- crosses line boundaries
label_pattern = r'[A-Za-z_]\w*\s*:'

# CORRECT -- confined to the current line
label_pattern = r'[A-Za-z_]\w*[ \t]*:'
```

Applies to any regex-based lexer, parser, or line-oriented text
processing, not just Pygments lexers.

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-08-27 | `\s*` crosses newlines | ooRexx lexer directive detection silently broken by a label rule eating an adjacent line |

---

## Windows: `python`/`pip` on PATH can silently resolve to different interpreters

[IMPORTANT]

On a Windows machine with multiple Python installs (e.g. a bundled
interpreter from another application, plus a separately-installed
one), bare `python` and `pip` on PATH can resolve to **different**
interpreters. `pip install X` followed by `python -c "import X"` can
silently install into one interpreter and test against another,
looking exactly like a failed install when it's actually two different
environments. Confirmed case: `pip` pointed at a Microsoft Store
Python 3.13 install while bare `python` resolved to a LibreOffice-
bundled Python 3.8 with no pip at all.

**Fix:** use `python -m pip install X` (guarantees pip and python are
the same interpreter), and/or explicitly resolve both to confirmed
full paths before trusting an install→import round trip rather than
relying on PATH resolution.

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-08-27 | `python -m pip` over bare `pip` on Windows | Install appeared to fail; `pip` and `python` were resolving to different interpreters (Microsoft Store 3.13 vs. LibreOffice-bundled 3.8) |

---

## Editable installs (`pip install -e .`) don't hot-reload entry-point metadata

Plain source-code edits are live immediately under an editable
install. Changes to `[project.entry-points...]` in `pyproject.toml`
are **not** — entry-point tables are baked into installed package
metadata at install time. After changing entry points, re-run
`pip install -e .` before the new/changed entries take effect; testing
against the old metadata will look like the edit didn't happen.

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-08-27 | Entry-point edits need a fresh `pip install -e .` | Confirmed while iterating on `pyproject.toml`'s entry-points table |

---

## Pygments-specific: `RegexLexer` rule order, `analyse_text`, and error-token testing

These three are Pygments-specific rather than general Python, but
worth keeping alongside the entry-point/packaging rules above since
they were found in the same project and are easy to get wrong the same
way (copying an unfamiliar pattern without verifying it).

**Rule order within a state matters.** Within one `RegexLexer` state's
rule list, the *first* pattern that matches at the current position
wins — not the longest match across separate list entries (unlike
alternation `(a|b)` *within* one pattern, which does prefer the
longest branch). Overlapping-prefix tokens must be listed
longest-first:

```python
# WRONG -- '~' always matches before '~~' gets a chance
tokens = {'root': [
    (r'~', Operator),
    (r'~~', Operator),
]}

# CORRECT -- longest alternative first
tokens = {'root': [
    (r'~~', Operator),
    (r'~', Operator),
]}
```

**`analyse_text` takes no `self`.** Verified against Pygments' own
live `RexxLexer` source: it's defined `def analyse_text(text):` with
no `self` parameter, yet Pygments' class machinery calls it correctly
via `guess_lexer()`. Adding `self` because it "looks like a method" is
an easy mistake when copying from an unfamiliar example without
checking the actual source.

**Testing pattern: assert no `Error` tokens over real samples.**
Iterate a lexer's `get_tokens()` output across real sample files and
assert `pygments.token.Error` never appears. Cheap and general — it
catches "this character/sequence isn't handled by any rule" gaps that
hand-picked unit-test fragments can miss entirely, since a hand-picked
fragment only exercises what its author already thought to test.

| Date | Entry | Triggered by |
|------|-------|--------------|
| 2026-08-27 | RegexLexer rule order, analyse_text signature, Error-token test pattern | Building the ooRexx Pygments lexer (`~`/`~~` operator collision; verifying `analyse_text` against live Pygments source rather than assuming a signature) |
