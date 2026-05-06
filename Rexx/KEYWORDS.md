# Rexx Keywords

Do not use these as variable names, label names, or subroutine names.

## ANSI X3.274-1996 Rexx keywords

```
address   arg       call      do        drop
else      end       exit      if        interpret
iterate   leave     nop       numeric   options
otherwise parse     procedure pull      push
queue     return    say       select    signal
then      to        trace     until     when
while
```

## Special variables (set automatically — never reassign)

| Variable | Set by |
|----------|--------|
| `rc`     | Host commands (`address system`, bare expressions) |
| `result` | `call` and function return values |
| `sigl`   | Line number of last `signal` or `call` |

## IBM TSO/E REXX additions

```
expose    forward   from      leave     loop
queue     raise     reply     routine   use
```
