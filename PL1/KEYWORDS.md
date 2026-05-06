# PL/I Keywords

Do not use these as variable names, label names, or entry names.
PL/I is case-insensitive; keywords shown in uppercase by convention.

## PL/I reserved words (ANSI X3.74 / IBM Enterprise PL/I)

```
ALLOCATE   ATTACH     BEGIN      BY         CALL
CHECK      CLOSE      DCL        DECLARE    DEFAULT
DELETE     DETACH     DISPLAY    DO         ELSE
END        ENDFILE    ENDPAGE    ENTRY      EXIT
FETCH      FILE       FORMAT     FREE       GET
GO         GOTO       IF         INITIAL    KEY
KEYFROM    KEYTO      LEAVE      LOCATE     ON
OPEN       OTHERWISE  PACKAGE    PICTURE    PROCEDURE
PUT        READ       RELEASE    RETURN     REVERT
REWRITE    SELECT     SIGNAL     STOP       THEN
TO         UNLOCK     WAIT       WHEN       WHILE
WRITE
```

## Commonly confused PL/I attributes (not keywords but restricted)

```
ALIGNED    AREA       BASED      BIN        BINARY
BIT        CHAR       CHARACTER  COMPLEX     CONDITION
CONTROLLED DECIMAL    DEC        DEFINED     DIRECT
ENVIRONMENT EXTERNAL  FIXED      FLOAT       INTERNAL
LIKE       NONVARYING OFFSET     POINTER     REAL
RECORD     SEQUENTIAL STATIC     STREAM      STRING
UNALIGNED  VARYING
```

## Notes

- PL/I allows keywords as identifiers in many contexts (it is a
  "free-form" language with context-dependent keyword recognition)
  but this is confusing and should be avoided
- IBM Enterprise PL/I and OS PL/I have slightly different reserved
  word sets; when in doubt, avoid all of the above as identifiers
