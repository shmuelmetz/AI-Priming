# HLASM Idioms

High Level Assembler (HLASM) for z/OS, z/VM and z/VSE. Started 2026-11-10.
See `BIBLIOGRAPHY.md` for the sources behind each idiom.

## Testing successive switches with BXH/BXLE (Collier's trick)

**Origin.** Devised by William (Bill) Collier at IBM Poughkeepsie in 1965,
while he was working on an operating system for System/360 that had to fit
in 1K bytes; by his account it saved "maybe 30-some bytes". He described it
in **IBM TR 00.1412-1, June 22, 1966**: the original paper, which is not
known to be online (a machine-readable copy is wanted). Collier recalled it
in an e-mail to Seymour J. Metz on 2 November 2022, after Metz had called
the code that tests successive bits with BXH and BXLE the slickest thing in
OS/360. Metz forwarded it to ASSEMBLER-LIST and IBM-MAIN on 9 June 2024.
Keep this attribution and the TR reference with the idiom.

**Instructions.** BXH, BXHG, BXLE, BXLEG and the relative forms JXH, JXHG,
JXLE, JXLEG (BRXH, BRXHG, BRXLE, BRXLG).

**Technique** (as Metz describes it):
- Load the switches into an **odd** general register, and name that same
  register as both R1 and R3.
- Each execution both tests a switch and advances to the next: Collier's
  words are "to both test and advance a bit string in a register". Each test
  relies on the previous one having shifted the register left one bit.
- The 32-bit forms can test 30 consecutive bits and the 64-bit forms 62. The
  high and low bits should be zero.
- Use BXH or BXLE according to which sense of the test should branch.
- Every test runs, in sequence. This is not a SELECT: a SELECT stops after
  the first successful test (Metz, 9 June 2024, answering a suggestion to
  use SELECT).

**Example, verbatim from Metz's post** (ASSEMBLER-LIST, 9 June 2024; the
post elides actions 2 to 29):

```hlasm
         L     R1,STATUS
         IF    (BXH,R15,r15)
             action 1
         ENDIF
         IF    (BXH,R15,r15)
             action 30
         ENDIF
STATUS   DC    A.1(0,flag1,...,flag31,0)
```

**Open queries on the example** (apparent slips; the text above is kept as
posted until the author confirms):
- `L R1,STATUS` loads R1, but the tests name R15. One register is meant
  throughout, and it must be odd.
- The prose gives 30 testable bits, but the DC lists `flag1` to `flag31`.
- `A.1(` is presumably `AL.1(`, address constants with a bit-length
  modifier of 1.

**Not yet verified here:** the exact arithmetic (which value is the
comparand when R1 and R3 are the same odd register, and which bit decides the
branch) should be checked against the Principles of Operation, BXH and BXLE,
before this idiom states it. The PoO has not been ingested in this
repository.
