# Classic Rexx Bibliography

See also `../ooRexx/BIBLIOGRAPHY.md` for ooRexx-specific references.

## Primary references

### ANSI REXX Standard
- **Title:** American National Standard for Information Technology —
  Programming Language REXX (X3.274-1996)
- **Notes:** The formal language standard.

### IBM TSO/E REXX Reference
- **Title:** z/OS TSO/E REXX Reference (SA32-0972)
- **URL:** https://www.ibm.com/docs/en/zos/latest?topic=rexx-tsoe-reference
- **Notes:** IBM mainframe REXX reference. Authoritative for `rc`,
  `address`, `outtrap`, and built-in functions. TSO/E REXX is the only
  REXX interpreter on z/OS -- TSO, ISPF, ISPF/PDF EDIT, the OMVS
  shell, batch (`IRXJCL`), and System REXX are the same interpreter
  run in different environments, not separate products. This session
  saw this URL and the PDF variants below return 403; not fetched.

### z/OS Using REXX and z/OS UNIX System Services
- **Title:** z/OS Using REXX and z/OS UNIX System Services (SA23-2283)
- **Notes:** Documents the same TSO/E REXX interpreter's behavior when
  run from the OMVS shell -- a separate manual from the TSO/E REXX
  Reference above, not a different REXX. Confirms `SH` as the initial
  host command environment there.

### z/VM REXX/VM Reference
- **Title:** z/VM REXX/VM Reference (SC24-6314/SC24-5963, per release)
- **URL:** https://www.vm.ibm.com/library/ (per-release PDFs, e.g.
  https://www.vm.ibm.com/library/730pdfs/73631400.pdf)
- **Notes:** Covers REXX/VM under CMS and, in Appendix E, under GCS
  (Group Control System) -- a distinct z/VM guest environment from
  CMS, with its own default `ADDRESS` environment (`GCS`) and lacking
  the `selector` third argument to `VALUE()` entirely. Also documents
  XEDIT macros defaulting to `ADDRESS XEDIT`, falling through to `CMS`
  then `CP` automatically. Fetched directly and text-extracted this
  session (unlike ibm.com, vm.ibm.com PDFs were reachable).

### REXX Reference Summary Handbook
- **Author:** Richard K. Goran
- **Edition:** 4th edition
- **Notes:** Compact reference covering both classic Rexx and ooRexx.

### Regina REXX
- **URL:** https://regina-rexx.sourceforge.io/
- **Notes:** Open source classic Rexx interpreter. Documentation
  covers ANSI REXX compliance and extensions.

## RexxLA
- **URL:** https://www.rexxla.org/
- **Notes:** Hosts RexxLA Newsletter archives including
  "Practicing Safe REXX" (Metz, OS/2 Magazine, 1995).
