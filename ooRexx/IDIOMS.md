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
