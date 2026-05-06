# NetRexx Idioms

## Class definition

```netrexx
class MyClass extends Object
  properties public
    name = String

  method MyClass(aName=String)
    name = aName

  method getName() returns String
    return name
```

## do over (iterating collections)

```netrexx
myList = ArrayList()
myList.add('a')
myList.add('b')
loop item over myList
    say item
end
```

## String handling

```netrexx
str = Rexx 'Hello'
say str.length()      -- Rexx string methods
say str.upper()       -- uppercase
say str.word(1)       -- first word
```
