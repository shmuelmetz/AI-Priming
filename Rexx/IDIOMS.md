# Classic Rexx Idioms

See `../ooRexx/IDIOMS.md` for ooRexx idioms. This file covers
idioms specific to classic Rexx or where classic Rexx differs
from ooRexx.

---

## Uppercasing

```rexx
str = translate(str)   /* classic Rexx -- portable */
```

---

## Capturing command output (TSO/E)

```rexx
call outtrap 'out.'
address tso 'some-command'
cmdRc = rc
call outtrap 'off'
do i = 1 to out.0
    say out.i
end
```

---

## Capturing command output (Regina / other)

Use `rxqueue` or platform-specific facilities. There is no
portable classic Rexx equivalent of ooRexx `address...with`.

---

## Environment variables (classic Rexx)

```rexx
userProfile = value('USERPROFILE',,'ENVIRONMENT')  /* ooRexx / Regina */
```

In TSO/E REXX, use `SYSVAR` or TSO/E-specific functions instead.
