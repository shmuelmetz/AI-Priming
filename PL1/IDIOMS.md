# PL/I Idioms

## Declaration conventions

```pli
/* Scalars */
DCL myFixed   FIXED BIN(31);
DCL myFloat   FLOAT DEC(15);
DCL myChar    CHAR(80) VARYING;
DCL myBit     BIT(8);
DCL myPtr     POINTER;

/* Arrays */
DCL myArr(10) FIXED BIN(31);
DCL myMat(3,3) FLOAT DEC(15);

/* Structures */
DCL 1 myStruct,
      3 field1 CHAR(8),
      3 field2 FIXED BIN(31);
```

## Procedure structure

```pli
MYPROG: PROCEDURE OPTIONS(MAIN);
  DCL myVar FIXED BIN(31) INIT(0);
  /* ... */
  STOP;
END MYPROG;
```

## ON conditions

```pli
ON ENDFILE(MYFILE) BEGIN;
  eof = '1'B;
END;
```

## String handling

```pli
DCL str CHAR(80) VARYING;
str = 'Hello';
str = str || ' World';    /* concatenation */
PUT SKIP LIST(str);
```
