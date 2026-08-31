# ooRexx Idioms

Canonical patterns for ooRexx code. AI engines should generate code
that follows these idioms rather than patterns from other languages.

---

## Initialisation

```rexx
call RxFuncAdd 'SysLoadFuncs', 'RexxUtil', 'SysLoadFuncs'
call SysLoadFuncs
```

Always load RexxUtil at the start of any script that uses `Sys*`
functions.

---

## Environment variables

```rexx
userProfile = value('USERPROFILE',,'ENVIRONMENT')
osName      = value('OS',,'ENVIRONMENT')
```

The three-argument form of `value()` reads environment variables
portably across platforms.

---

## File existence check before operation

```rexx
if SysFileExists(path) then do
    /* operate on file */
end
else
    say 'WARN:' path 'not found'
```

---

## Safe file copy with result check

```rexx
call SysFileCopy src, dst
copyRc = result
if copyRc \= 0 then do
    say 'ERROR: failed to copy' src '(rc='copyRc')'
    errors = errors + 1
end
else say 'OK: copied' filespec('N',src)
```

---

## Safe file move (copy + delete)

```rexx
call SysFileCopy src, dst
copyRc = result
if copyRc \= 0 then do
    say 'ERROR: failed to copy' src '(rc='copyRc')'
    errors = errors + 1
    return
end
call SysFileDelete src
delRc = result
if delRc = 0 then say 'OK: moved' filespec('N',src)
else do
    say 'WARN: copied but could not delete' src '(rc='delRc')'
    errors = errors + 1
end
```

---

## Capturing command output

```rexx
address system 'some-command' with output stem out. error stem err.
cmdRc = rc
do i = 1 to out.0
    say out.i
end
```

---

## Writing a log file (tee pattern)

```rexx
tee: procedure expose logFile
    line = ''
    do i = 1 to arg()
        if i = 1 then line = arg(1)
        else line = line arg(i)
    end
    say line
    call lineout logFile, line
    return
```

---

## Reading and writing dated log entries

```rexx
/* Write today's date in YYYY-MM-DD format */
todayStr = Date('S')   /* YYYYMMDD */
todayFmt = left(todayStr,4)'-'substr(todayStr,5,2)'-'substr(todayStr,7,2)
call lineout logFile, todayFmt
call stream  logFile, 'C', 'CLOSE'

/* Read and compare */
lastRun  = linein(logFile)
call stream logFile, 'C', 'CLOSE'
parse var lastRun year'-'month'-'day
lastBase  = Date('B', year||month||day, 'S')
todayBase = Date('B')
daysSince = todayBase - lastBase
```

---

## Subroutine with shared error counter

```rexx
errors = 0

myProc: procedure expose errors
    /* ... */
    errors = errors + 1
    return
```

---

## Path construction

```rexx
userProfile = value('USERPROFILE',,'ENVIRONMENT')
repoRoot    = userProfile'\repos\Personal'
binDir      = userProfile'\bin'
logFile     = repoRoot'\scripts\session.log'
```

---

## Platform detection

```rexx
osName = value('OS',,'ENVIRONMENT')
if osName = 'Windows_NT' then
    sep = '\'
else
    sep = '/'
```

---

## Collection classes

```rexx
/* Array */
arr = .Array~of('a', 'b', 'c')
do item over arr
    say item
end

/* Directory (string-keyed) */
dir = .Directory~new
dir['host'] = 'gmu.edu'
dir['user'] = 'smetz3'
do key over dir
    say key '=' dir[key]
end

/* Mixed-index Array (numeric + named) */
outArr = .Array~new(out.0)
do i = 1 to out.0
    outArr[i] = out.i
end
outArr['rc'] = cmdRc
```

---

## Capturing command output and inspecting result

```rexx
/* Silent capture -- caller inspects */
outArr = captureCmd('some-command')
if outArr['rc'] = 0 then say 'OK'
else say 'ERROR rc=' outArr['rc']

/* Display all output */
outArr = captureCmd('some-command')
do i = 1 to outArr~size
    if outArr[i] \== .nil then say outArr[i]
end
```

## File and directory deletion

<!-- SysFileDelete: files only; SysRmDir: empty directories only -->
- `SysFileDelete(path)` -- files only; cannot remove directories
- `SysRmDir(path)` -- empty directories only; fails if non-empty
- To remove a non-empty directory tree (e.g. a git clone with
  read-only objects): `address system 'cmd /C rmdir /S /Q "path"'`

---

## Attribute-less class as a method anchor

A class with no instance attributes at all, used purely as a namespace
to group related class (not instance) methods -- the ooRexx equivalent
of a Java-style "static utility class." Never instantiated; callers
invoke methods directly on the class object itself.

```rexx
::CLASS 'PathUtil' PUBLIC

::METHOD normalize CLASS
    use strict arg path
    return path~changeStr('/', '\')

::METHOD isAbsolute CLASS
    use strict arg path
    return path~left(1) == '\' | path~match(2, ':')

/* Usage -- no ~new anywhere */
say .PathUtil~normalize('a/b/c')
```

---

## Method-less singleton as an attribute anchor (in lieu of globals)

ooRexx has no true global variables -- only `.local` environment
symbols or per-class `::ATTRIBUTE ... CLASS` variables shared across
all instances. A single shared instance of a plain attribute-holding
class (or, more simply, a `.Directory` stashed in `.local`) gives
callers a single named place to read and write shared state without
reaching for `.local` symbols directly everywhere.

```rexx
::CLASS 'Config' PUBLIC

::METHOD init CLASS
    expose instance
    instance = .nil

::METHOD instance CLASS
    expose instance
    if instance == .nil then instance = self~new
    return instance

::ATTRIBUTE sessionId
::ATTRIBUTE verbose

/* Usage -- one shared instance, plain attribute access */
.Config~instance~sessionId = '2026-08-30-01'
if .Config~instance~verbose then say 'session:' .Config~instance~sessionId
```

The simpler alternative for a handful of loosely-related settings: skip
the class entirely and stash one `.Directory` in `.local` at startup
(`.local~config = .Directory~new`), then read/write it by key
(`.local~config['sessionId']`) everywhere else -- less ceremony when
there's no behavior to attach, only data.
