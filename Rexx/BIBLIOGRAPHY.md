# Classic Rexx Bibliography

See also `../ooRexx/BIBLIOGRAPHY.md` for ooRexx-specific references.

## Primary references

### ANSI REXX Standard
- **Title:** American National Standard for Information Technology —
  Programming Language REXX (X3.274-1996)
- **Notes:** The formal language standard. Section citations (§X.Y.Z)
  in this repo are drawn from RexxLA's hosted copy of the X3J18
  committee's document (self-identified throughout as "X3J18-199X",
  https://www.rexxla.org/rexxlang/standards/j18pub.pdf) -- the last
  public-review draft before 1996 ratification, not a copy of the
  final published ANSI text itself. Same content lineage (same
  committee, ratified with no known section-numbering changes), but
  worth knowing if a specific citation is ever double-checked against
  a purchased ANSI copy.

### IBM TSO/E REXX Reference
- **Title:** z/OS TSO/E REXX Reference (current form SA32-0972); two
  older editions of the same manual's lineage were fetched and
  text-extracted this session instead, since ibm.com/docs and its PDF
  URLs return 403 here: *TSO Extensions Version 2 REXX Reference*
  SC28-1883-0 (December 1988) and SC28-1883-4 (August 1991). (TSO
  Extensions predates the z/OS/TSO-E rebrand.)
- **URL:** https://www.ibm.com/docs/en/zos/latest?topic=rexx-tsoe-reference
  (current, blocked here); working mirrors used instead:
  https://vtda.org/docs/computing/IBM/Mainframe/SysSoft/TSO/SC28-1883-0_TSOExtensionsV2REXXReference_Dec88.pdf
  (1988) and
  https://archive.org/stream/bitsavers_ibm370TSOESOExtensionsVersion2ProceduresLangageMVS_48315466/SC28-1883-4_TSO_Extensions_Version_2_Procedures_Langage_MVS_REXX_Reference_Aug1991_djvu.txt
  (1991, full text)
- **Notes:** IBM mainframe REXX reference. Authoritative for `rc`,
  `address`, `outtrap`, and built-in functions. TSO/E REXX is the only
  REXX interpreter on z/OS -- TSO, ISPF, ISPF/PDF EDIT, the OMVS
  shell, batch (`IRXJCL`), and System REXX are the same interpreter
  run in different environments, not separate products. The host
  command environment table grew noticeably between the two editions
  above -- the 1988 edition's table (TSO/E READY: TSO default + MVS,
  LINK, ATTACH; non-TSO/E address space: MVS default + LINK, ATTACH;
  ISPF: TSO default + MVS, LINK, ATTACH, ISPEXEC, ISREDIT) is missing
  CONSOLE, the APPC family (CPICOMM, LU62), and the
  LINKMVS/LINKPGM/ATTCHMVS/ATTCHPGM forms that the 1991 edition
  documents in full -- see Rexx/RULES.md's ADDRESS environments table
  for the complete, current list and which environments are available
  in which address spaces. Both editions document ISPF's environment
  list from TSO/E's own side; see the ISPF Dialog Developer's Guide
  entry below for why that distinction matters. Neither edition
  documents EDIT, TEST, or IPCS as host command environments, despite
  a full sweep of every `ADDRESS <name>` occurrence in both texts --
  these are believed to exist by the same registration pattern (per
  direct operator experience) but are not primary-sourced here.

### ISPF Dialog Developer's Guide and Reference
- **Title:** z/OS ISPF Dialog Developer's Guide and Reference
  (SC34-4821, edition checked: z/OS V1R6.0, SC34-4821-03)
- **URL:** http://www.manmrk.net/tutorials/ISPF/ispzdg30.pdf (non-ibm.com
  mirror; ibm.com/docs PDF variants return 403 from this session)
- **Notes:** The authoritative ISPF-specific manual, distinct from the
  TSO/E REXX Reference. Does **not** independently restate the REXX
  host command environment list for ISPF -- that list, as used in
  Rexx/RULES.md, rests on the TSO/E REXX Reference's own documentation
  of the ISPF case, not on this manual. Worth re-checking here first
  if a future session needs to settle any doubt about ISPF's REXX
  environment behavior specifically.

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
