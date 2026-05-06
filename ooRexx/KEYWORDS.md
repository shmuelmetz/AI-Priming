# ooRexx Keywords

Do not use these as variable names, label names, or subroutine names.

## Classic Rexx keywords (inherited by ooRexx)

```
address   arg       call      do        drop
else      end       exit      expose    forward
from      if        interpret iterate   leave
loop      nop       numeric   options   otherwise
parse     procedure pull      push      queue
raise     reply     return    routine   say
select    signal    then      to        trace
until     use       when      while
```

## ooRexx-specific keywords

```
class     forward   guard     inherit   method
mixinclass raise     reply     requires  resource
routine   self      subclass  super     use
```

## Special variables (set automatically — never reassign)

| Variable | Set by |
|----------|--------|
| `rc`     | Host commands (`address system`, bare expressions) |
| `result` | `call` and function return values |
| `sigl`   | Line number of last `signal` or `call` |
