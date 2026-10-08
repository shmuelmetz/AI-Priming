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
  a purchased ANSI copy. Directly checked for `PARSE LOWER`/`PARSE
  CASELESS`: neither appears anywhere in the text, confirming only
  `PARSE UPPER` is genuine ANSI syntax -- `LOWER` and `CASELESS` are
  ooRexx/Regina extensions, not ANSI-standard. No abbreviated form of
  `UPPER` is documented anywhere in the standard either. Separately
  stated absent from TRL-2 as well, per the Safe REXX paper's author
  directly -- no access here to TRL-2 (Cowlishaw's *The REXX Language*,
  2nd ed.) to check that half independently; it's a copyrighted
  Prentice-Hall book, not a freely hosted primary source the way this
  ANSI draft and the IBM manuals cited elsewhere in this file are.
  Also checked directly for `CHARS()`/`LINES()` exactness: the
  standard's own text describes `CHARS` as indicating "whether there
  are characters remaining" and *optionally* returning a count, and
  `LINES` similarly via a shared `Config_Stream_Count` abstraction --
  an exact count is implementation-permitted, not required, for
  either function, on any stream. See the z/VM REXX/VM Reference entry
  below for what CMS specifically chose.

### PC DOS 7 REXX User's Guide and Reference
- **Title:** PC DOS 7 REXX User's Guide and Reference, IBM Corp.,
  S83G-9228
- **URL:** https://raw.githubusercontent.com/knorrie/rexx-asm-archive/master/extra/PC_DOS_7_REXX_Reference.txt
  (full text; ibm.com has no working copy found)
- **Notes:** IBM's own classic REXX bundled with PC DOS 7 (1995) --
  distinct from the third-party Personal REXX (Quercus Systems) also
  available for DOS. Confirms PC-DOS's native host command environment
  is `COMMAND`, not `CMD` like OS/2's classic REXX -- the ADDRESS
  instruction discussion uses `ADDRESS COMMAND` throughout, in a
  passage structurally identical to the OS/2 manual's own `ADDRESS
  CMD` passage (same DIR-STARTUP example, same wording).

### IBM TSO/E REXX Reference
- **Title:** z/OS TSO/E REXX Reference (current form SA32-0972); three
  editions of this manual's lineage were fetched and text-extracted
  this session, since ibm.com/docs and its PDF URLs return 403 to
  direct HTTP requests here: *TSO Extensions Version 2 REXX Reference*
  SC28-1883-0 (December 1988) and SC28-1883-4 (August 1991) (TSO
  Extensions predates the z/OS/TSO-E rebrand), plus the current z/OS
  2.5 edition, SA32-0972-50 (2021).
- **URL (z/OS "latest" alias, i.e. the current release; release 3.2.0 docs:
  https://www.ibm.com/docs/en/zos/3.2.0):** https://www.ibm.com/docs/en/zos/latest?topic=rexx-tsoe-reference
  (current, blocked here); working sources used instead:
  https://vtda.org/docs/computing/IBM/Mainframe/SysSoft/TSO/SC28-1883-0_TSOExtensionsV2REXXReference_Dec88.pdf
  (1988),
  https://archive.org/stream/bitsavers_ibm370TSOESOExtensionsVersion2ProceduresLangageMVS_48315466/SC28-1883-4_TSO_Extensions_Version_2_Procedures_Langage_MVS_REXX_Reference_Aug1991_djvu.txt
  (1991, full text), and
  https://rexxinfo.org/reference/articles/tso_e_rexx_reference_v2r5.pdf
  (current, 2021)
- **Notes:** IBM mainframe REXX reference. Authoritative for `rc`,
  `address`, `outtrap`, and built-in functions. TSO/E REXX is the only
  REXX interpreter on z/OS -- TSO, ISPF, ISPF/PDF EDIT, the OMVS
  shell, batch (`IRXJCL`), and System REXX are the same interpreter
  run in different environments, not separate products. The host
  command environment table grew between 1988 and 1991, then stayed
  frozen: the 1988 edition's table (TSO/E READY: TSO default + MVS,
  LINK, ATTACH; non-TSO/E address space: MVS default + LINK, ATTACH;
  ISPF: TSO default + MVS, LINK, ATTACH, ISPEXEC, ISREDIT) is missing
  CONSOLE, the APPC family (CPICOMM, LU62), and the
  LINKMVS/LINKPGM/ATTCHMVS/ATTCHPGM forms, but the 1991 and the current
  2021 edition's SUBCOM sections are word for word identical -- see
  Rexx/RULES.md's ADDRESS environments table for the complete, current
  list and which environments are available in which address spaces.
  All three editions document ISPF's environment list from TSO/E's own
  side; see the ISPF Dialog Developer's Guide entry below for why that
  distinction matters. None of the three documents EDIT, TEST, or IPCS
  as SUBCOM-table host command environments, despite a full sweep of
  every `ADDRESS <name>` occurrence in all three texts -- see the TSO/E
  Command Reference and IPCS User's Guide entries below for where those
  three are actually documented, and why EDIT/TEST work by a genuinely
  different mechanism than a SUBCOM-table entry.

- **TSO/E program numbers and releases (announced / GA, letter; each
  letter checked 2026-10-07 at https://www.ibm.com/docs/en/announcements/archive/ENUSnnn-nnn):**
  - TSO Extensions for MVS/370: 5665-285; for MVS/XA: 5665-293. Both
    announced 21 Oct 1981 (European letter ZP81-0796); US MVS/XA
    overview 31 Mar 1983, 283-042.
    - Release 2 (adds the Information Center Facility): announced
      17 May 1983, 283-147; shipping as of 3 Jul 1984, 284-251
    - Release 2.1 (MVS/XA virtual storage constraint relief): announced
      17 Jul 1984, 284-255; available early, 18 Dec 1984, 284-487
    - Release 4: 19 May 1987 / 25 Sep 1987, 287-193
  - TSO/E Version 2: 5685-025, announced 19 Apr 1988, 288-191 (MVS/ESA
    feature available fourth quarter 1988; compare SC28-1883-0,
    December 1988); 288-694 (6 Dec 1988) sets GA of the MVS/ESA feature
    at 30 Dec 1988 and of the MVS/XA feature at 6 Jan 1989
    - V2R2: 5 Sep 1990 / 26 Oct 1990, 290-492
    - V2R3: 5 Sep 1990 / 29 Mar 1991, 290-493
    - V2R3.1: 25 Jun 1991 / 30 Aug 1991, 291-313 (adds TSO/E support for
      the Compiler and Library for REXX/370)
    - V2R5: 26 Sep 1995 / 29 Dec 1995, 295-406 (adds TSOLIB)
  - Since OS/390, TSO/E is a base element of the operating system and
    has no program number of its own (from recall, not from the letters).
  Program numbers first taken from the timeline at
  https://en.wikipedia.org/wiki/User:Chatul/References (ingested
  2026-10-07), then checked against the letters.

### TSO/E Command Reference
- **Title:** OS/390 IBM TSO/E Command Reference, SC28-1969-02 (Third
  Edition, March 1999)
- **URL:** https://www.informatik.uni-leipzig.de/cs/Literature/Textbooks/TSOreference.pdf
  (non-ibm.com mirror; full text)
- **Notes:** Documents `EDIT` and `TEST` as ordinary TSO commands with
  their own extensive subcommand families -- but, notably, contains no
  `SUBCOM` discussion at all (that's a REXX-specific concept, out of
  this manual's scope). Each command's own `EXEC` subcommand entry
  states the real host-command-routing mechanism directly and
  identically for both: "Specify only REXX statements in the REXX
  exec. Specify only EDIT [or TEST] subcommands and CLIST statements in
  the CLIST. You cannot specify TSO/E commands in the CLIST or REXX
  exec until you specify END [or RUN, for TEST] to terminate EDIT [or
  TEST]." This is an invocation-time restriction on what a bare clause
  can reach, not a registered `ADDRESS`-selectable environment in the
  SUBCOM table the way ISPEXEC/ISREDIT are -- confirmed absent from
  that table in all three TSO/E REXX Reference editions above.

### z/OS MVS IPCS User's Guide
- **Title:** z/OS MVS IPCS User's Guide, SA23-1384(-00, edition
  checked)
- **URL:** https://www.ibm.com/docs/en/SSLTBW_2.1.0/com.ibm.zos.v2r1.ieac600/iea3c6_ADDRESS_IPCS_Instruction.htm
  and .../climde.htm ("Modes of IPCS Operation") -- reachable through
  the browser pane even though ibm.com/docs returns 403 to direct HTTP
  requests here
- **Notes:** Confirms `IPCS` genuinely is a SUBCOM-table-style
  environment, unlike `EDIT`/`TEST` above: "ADDRESS IPCS changes the
  host command environment to IPCS. The IPCS host command environment
  is available only when you run the EXEC from an IPCS session." An
  IPCS session itself has multiple internal modes, and `ADDRESS IPCS`
  support is explicit per mode -- it works in IPCS mode (the session's
  normal mode) and during a trap stop, but "No ADDRESS IPCS support is
  intended" for the session's own separate TSO/E mode, where ordinary
  TSO commands and CLISTs run instead.

### ISPF Dialog Developer's Guide and Reference
- **Title:** z/OS ISPF Dialog Developer's Guide and Reference
  (SC34-4821, edition checked: z/OS V1R6.0, SC34-4821-03)
- **URL:** http://www.manmrk.net/tutorials/ISPF/ispzdg30.pdf (non-ibm.com
  mirror; ibm.com/docs PDF variants return 403 from this session)
- **URL (z/OS 3.2, current, checked 2026-10-07):** the ISPF element page
  linked from https://www.ibm.com/docs/en/zos/3.2.0?topic=documentation-pdf-files-zos-320-library
- **ISPF program numbers (from the Chatul timeline):** ISPF V2 for VM
  5664-282; ISPF V2.1 for MVS 5665-319, ISPF/PDF 5665-317; ISPF V3 for
  VM 5684-043; ISPF V3 for MVS 5685-054, later 5665-054, ISPF/PDF V3
  5665-402; ISPF for MVS V4 5655-042 (V4R1 GA 8 Apr 1994, 294-161;
  V4R2 GA 21 Jul 1995, 295-247). ISPF is now a z/OS base element.
- **Notes:** The authoritative ISPF-specific manual, distinct from the
  TSO/E REXX Reference. Does **not** independently restate the REXX
  host command environment list for ISPF -- that list, as used in
  Rexx/RULES.md, rests on the TSO/E REXX Reference's own documentation
  of the ISPF case, not on this manual. Worth re-checking here first
  if a future session needs to settle any doubt about ISPF's REXX
  environment behavior specifically.

### Compiler and Library for REXX/370
- **Products:** Compiler and Library for REXX/370, 5695-013; Library
  for REXX/370 (run-time only), 5695-014.
- **Releases (announced / GA, letter):**
  - Release 1: 25 Jun 1991 / 30 Aug 1991, 291-320
  - SAA REXX/370 Release 2: 9 Feb 1993 / 28 May 1993, 293-068
  - SAA REXX/370 Release 3: 25 Oct 1994 / 27 Oct 1994, 294-679
  - Compiler and Library for REXX on zSeries Release 4 (still 5695-013):
    29 Jul 2003 / 29 Aug 2003, 203-190
- **Related products (letters checked 2026-10-07):** VM REXX Compiler for
  CMS, 5664-390, 19 Sep 1989 / 17 Nov 1989, 289-455 (withdrawn
  3 Dec 1991, 291-700); REXX/VSE V1R1, 5686-058, 9 Feb 1993 /
  17 Sep 1993, 293-063.
- **URL (Release 1 announcement, checked 2026-10-07):** https://www.ibm.com/docs/en/announcements/archive/ENUS291-320
  (renders in the browser pane; 403 to curl). Current z/OS run-time:
  the "REXX Alternate Library" element in the z/OS 3.2 PDF library
  (see IBM Docs navigation below).
- **Notes:** The 1991 letter names the companion overview
  *REXX/370: Introducing the Next Step in REXX Programming* (G511-1430).
  Source for program numbers and announcement letters: the software
  timeline at https://en.wikipedia.org/wiki/User:Chatul/References
  (ingested 2026-10-07); letter URLs are https://www.ibm.com/docs/en/announcements/archive/ENUSnnn-nnn.

### z/OS Using REXX and z/OS UNIX System Services
- **Title:** z/OS Using REXX and z/OS UNIX System Services (SA23-2283)
- **Notes:** Documents the same TSO/E REXX interpreter's behavior when
  run from the OMVS shell -- a separate manual from the TSO/E REXX
  Reference above, not a different REXX. Confirms `SH` as the initial
  host command environment there.

### z/VM REXX/VM Reference
- **Title:** z/VM REXX/VM Reference (SC24-6314/SC24-5963, per release)
- **URL:** https://www.ibm.com/support/pages/zvm/library/ (per-release PDFs, e.g.,
  release 7.3.0: https://www.ibm.com/support/pages/zvm/library/730pdfs/73631400.pdf;
  release 7.4.0: https://www.ibm.com/support/pages/zvm/library/740pdfs/74631400.pdf; the old
  www.vm.ibm.com/library paths redirect there, checked 2026-10-07); current z/VM
  7.4.0 online equivalents (reachable via the browser pane; ibm.com/docs
  returns 403 to direct HTTP requests here):
  https://www.ibm.com/docs/en/SSB27U_7.4.0/com.ibm.zvm.v740.dmsb1/xenvir.htm
  ("Environment," confirms the CMS default),
  https://www.ibm.com/docs/en/SSB27U_7.4.0/com.ibm.zvm.v740.dmsb1/rexxgcs.htm
  ("z/VM REXX/VM Interpreter in the GCS Environment," confirms the GCS
  default and the missing `selector` argument), and
  https://www.ibm.com/docs/en/SSB27U_7.4.0/com.ibm.zvm.v740.gcta0/icon.htm
  ("Entering Commands to GCS," confirms `ADDRESS COMMAND` under GCS)
- **Notes:** Covers REXX/VM under CMS and, in Appendix E, under GCS
  (Group Control System) -- a distinct z/VM guest environment from
  CMS, with its own default `ADDRESS` environment (`GCS`) and lacking
  the `selector` third argument to `VALUE()` entirely. Also documents
  XEDIT macros defaulting to `ADDRESS XEDIT`, falling through to `CMS`
  then `CP` automatically. Fetched directly and text-extracted this
  session (unlike ibm.com, vm.ibm.com PDFs were reachable). The three
  current z/VM 7.4.0 online topics above were cross-checked
  word-for-word against this PDF's content for CMS, GCS, and XEDIT,
  confirming no drift between the archived edition and the
  currently-maintained documentation. Also documents `LINES()` and
  `CHARS()` exactness on CMS specifically: `LINES(myfile) -> 7 /* 7
  lines remain */` for a disk file (an exact count), but `CHARS` is
  defined unconditionally as "returns either 0 or 1 depending on
  whether there are characters available... 0 otherwise" -- no
  file-vs-transient-stream distinction for `CHARS` at all, unlike
  `LINES`, which explicitly does distinguish persistent files from the
  default/console stream. See Rexx/RULES.md's I/O portability section.

### VM/SP System Product Interpreter Reference (original REXX manuals)
- **Title:** VM/SP System Product Interpreter Reference, three editions
  consulted: SC24-5239-0 (Release 3, September 1983 -- the manual for
  REXX's first shipped release), SC24-5239-1 (Release 4, December
  1984), SC24-5239-2 (Release 5, December 1986)
- **URL:** https://bitsavers.trailing-edge.com/pdf/ibm/370/VM/SP/Release_3.0_Jul83/SC24-5239-0_VM_SP_Interpreter_Reference_Rel_3_Sep83.pdf ,
  http://www.bitsavers.org/pdf/ibm/370/VM/SP/Release_4_Dec84/SC24-5239-1_VM_SP_System_Product_Interpreter_Reference_Release_4_Dec84.pdf ,
  https://bitsavers.trailing-edge.com/pdf/ibm/370/VM/SP/Release_5_Dec86/SC24-5239-2_VM_SP_Release_5_System_Product_Interpreter_Reference_Dec1986.pdf
  (all fetched directly and text-extracted this session -- scanned
  documents with an OCR text layer, readable via PyMuPDF though not
  via WebFetch's own text extraction)
- **Notes:** Ground truth for what the original, as-shipped REXX
  actually had, requested explicitly by the Safe REXX paper's author
  rather than relying on the current z/VM 7.x reference (which
  reflects decades of accumulated additions). All three editions
  confirmed to lack `LINES`, `CHARS`, `LINEIN`, `LINEOUT`, `CHARIN`,
  `CHAROUT`, and `STREAM` entirely -- not just a keyword-search miss:
  each edition's own alphabetically-ordered built-in-function listing
  jumps `CENTER`/`CENTRE` straight to `COMPARE`, and `LENGTH` straight
  to `LINESIZE`, exactly where `CHARS` and `LINES` would sort if
  present. `EXECIO` and the program stack (`PUSH`/`QUEUE`/`PULL`,
  `QUEUED()`) were the only file I/O mechanisms available through at
  least Release 5 (1986). Stream I/O is part of the language as
  defined in TRL-2 (1990) -- a language specification, not a
  description of any particular product's implementation, so it does
  not by itself establish when any real CMS/VM/SP interpreter actually
  added stream I/O. Only that it postdates Release 5 (1986) is
  established here; no VM/SP edition between Release 5 and TRL-2's
  1990 publication was checked.

### z/VM EXECIO Command Reference
- **Title:** EXECIO (CMS command), part of z/VM CMS Commands and
  Utilities Reference
- **URL (release 7.4.0, current, checked 2026-10-07):** https://www.ibm.com/docs/en/zvm/7.4.0?topic=commands-execio
  (release 7.2.0: https://www.ibm.com/docs/en/zvm/7.2.0?topic=commands-execio)
  (reachable via the browser pane; ibm.com/docs returns 403 to direct
  HTTP requests here)
- **Notes:** Confirms CMS's `EXECIO` supports three destinations:
  the program stack (`FIFO`/`LIFO`), a stem (`STEM stem.`), or a
  single plain variable (`VAR name` -- restricted to exactly one line;
  the `lines` operand must be `1` with `VAR`, and combining `VAR` with
  `*` is explicitly disallowed). Cross-checked against the TSO/E REXX
  Reference's own `EXECIO` syntax diagram (Chapter 10), which lists
  only `FIFO`/`LIFO`/`STEM` -- no `VAR` option at all -- confirming
  TSO/E's `EXECIO` is genuinely a subset of CMS's, not just narrower
  documentation of the same options.

### Rexx brief history
- **Author:** Michael F. Cowlishaw
- **URL:** https://speleotrove.com/rexxhist/rexxhistory.html
- **Notes:** Cowlishaw's own history page. Confirms the language was
  conceived 1979-03-20 and initially named REX ("the name sounded
  nice"); it gained its second X by 1982 "to avoid any confusion with
  other products," becoming REXX before shipping in VM/SP Release 3
  (dated 1983 here).

### VM/370 Interfaces for REXX (RexxLA presentation)
- **URL:** https://www.rexxla.org/presentations/2020/VM%20Interfaces%20for%20REXX.pdf
  (fetched and text-extracted this session)
- **Notes:** Gives VM/SP Release 3 as REXX's first shipped release too,
  but dates it 1982 (the presentation itself hedges: "1982 (or 3?)")
  -- a minor discrepancy against the Cowlishaw history page's 1983,
  unresolved; cite the release number (VM/SP R3) rather than a specific
  year unless this gets pinned down further. Also states EXEC2 was
  introduced earlier, in VM/SP Release 1 (1980), with its own
  EXECCOMM variable interface, and that "the EXEC2 variable interface
  was first used by the new EXECIO" -- suggesting `EXECIO` itself
  arrived together with REXX in VM/SP R3, built on EXEC2's
  already-existing interface, rather than substantially predating
  REXX. Left out of Safe-REXX's own text per the author's explicit
  instruction (2026-09-03) to keep EXEC/EXEC2 mentioned only as what
  REXX replaced, not entangled with `EXECIO`'s own history -- recorded
  here as background in case a future session needs it.

### REXX Reference Summary Handbook
- **Author:** Richard K. Goran
- **Edition:** 4th edition
- **Notes:** Compact reference covering both classic Rexx and ooRexx.

### Regina REXX
- **URL:** https://regina-rexx.sourceforge.io/
- **Notes:** Open source classic Rexx interpreter. Documentation
  covers ANSI REXX compliance and extensions. Its `RegUtil` package
  (the RexxUtil equivalent) was fetched and text-extracted this
  session -- confirmed it lacks `SysFileCopy`/`SysFileMove` (uses
  `SysCopyObject`/`SysMoveObject` instead), the entire `SysIsFileXxx`
  family, the Workplace-Shell family, and the Unix process functions
  (`SysFork`/`SysWait`/`SysCreatePipe`), while it does have the
  semaphore family, the macro-space family, and the ordinary
  file/directory core -- see Rexx/RULES.md's OBJREXX section for the
  full OREXX/ooRexx/Regina RexxUtil comparison table.

### Object REXX for Windows Reference
- **Title:** Object REXX for Windows Reference, Version 2.1,
  SH12-6725-00 (IBM Object REXX for Windows Interpreter Edition
  5639-M69 and Development Edition 5639-M68)
- **URL:** https://public.dhe.ibm.com/ps/products/ad/obj-xx/rexxref.pdf
  (fetched and text-extracted this session)
- **Notes:** The Windows edition of IBM's original (pre-open-source)
  Object REXX, distinct from ooRexx. Its RexxUtil chapter (Chapter 9)
  lists exactly 63 functions -- confirmed it lacks `SysFileCopy`,
  `SysFileMove`, the entire `SysIsFileXxx` family, and the entire
  Workplace-Shell family (`SysCreateObject` and related): the last is
  consistent with Windows having no Workplace Shell, but the absence
  of `SysFileCopy`/`SysFileMove` specifically was not expected going
  in. Used as the "OREXX" column baseline in Rexx/RULES.md's RexxUtil
  comparison table, alongside the AIX edition below for the
  Unix-specific functions.

### Object REXX for AIX Reference
- **Title:** Object REXX for AIX Reference, Version 1.1.3,
  SH12-6386-01
- **URL:** https://publibfi.dhe.ibm.com/epubs/pdf/rxor9a00.pdf
  (fetched and text-extracted this session)
- **Notes:** Confirms the AIX edition additionally documents
  `SysFork`, `SysWait`, `SysCreatePipe`, `SysGetpid`, `SysAddCmdPkg`,
  `SysAddFuncPkg`, `SysDropCmdPkg`, `SysDropFuncPkg`, and
  `SysGetMessage`/`SysGetMessageX` (Unix message catalogs) -- none of
  which appear in the Windows 2.1 Reference above, and (like the
  Windows edition) no Workplace-Shell functions -- confirmed via a
  full-text search of all 414 pages: zero hits for `SysCreateObject`
  and the rest of that family, and, notably, zero mentions anywhere of
  a desktop environment, windowing system, CDE, or Motif at all. This
  is NOT evidence AIX lacks a desktop -- it has one (CDE, Common
  Desktop Environment, jointly developed by HP/IBM/Sun/Novell in 1993
  on X11 and OSF/Motif 1.2, later folded into OSF itself in 1994). The
  Workplace-Shell family is bound to OS/2's WPS/SOM object model
  specifically; Object REXX for AIX simply never implemented an
  equivalent binding into CDE/Motif -- an implementation gap, not a
  missing desktop. Not checked against this
  reference: whether `SysFileCopy`/`SysFileMove` or the `SysIsFileXxx`
  family are present on AIX -- those absences are confirmed for the
  Windows edition only (Rexx/RULES.md's RexxUtil table says so
  explicitly now).

## IBM Docs navigation
- ibm.com/docs returns 403 to direct HTTP requests (curl, WebFetch) in
  this environment, but is reachable via the Claude Code browser pane
  tool -- use that for anything on this site.
- Per-collection landing pages found useful for locating current
  editions directly, without going through site search first:
  https://www.ibm.com/docs/en/zos/3.2.0 (current z/OS manuals),
  https://www.ibm.com/docs/en/zvm/7.4.0 (current z/VM manuals).
- Broader site-wide entry points, not yet mined for REXX content:
  https://www.ibm.com/docs/en/products (index of all product
  documentation collections), https://www.ibm.com/docs/en/announcements
  (what's new/changed per collection), https://www.ibm.com/docs/en/redbooks
  (IBM Redbooks -- often has practical, example-driven REXX guidance
  the reference manuals don't).
- PDF library index for the current z/OS release (checked 2026-10-07,
  release 3.2, updated 2026-08-07): https://www.ibm.com/docs/en/zos/3.2.0?topic=documentation-pdf-files-zos-320-library
  -- per-element pages (TSO/E, ISPF, REXX Alternate Library, MVS, ...)
  each with a "PDF Link"; plus a quarterly Adobe indexed PDF/PDX .zip.
  (User:Chatul/References still points at the 3.1 page.)
- z/VM Library Overview (all releases; 200 to curl, checked 2026-10-07):
  https://www.vm.ibm.com/library/
- Other IBM Documentation collections (from User:Chatul/References#Resources,
  checked 2026-10-07): https://www.ibm.com/docs/en/zvm (z/VM),
  https://www.ibm.com/docs/en/developer-for-zos (IBM Developer for z/OS;
  see also the ISPF-IDz-mapping repo),
  https://www.ibm.com/docs/en/explorer-for-zos (IBM Explorer for z/OS),
  https://www.ibm.com/docs/en/hla-and-tf/1.6.0?topic=pdf-format-documentation
  (HLASM and Toolkit Feature 1.6 PDFs),
  https://www.ibm.com/docs/en/systems-hardware/zsystems (IBM Z hardware,
  including Principles of Operation).
- Jim Elliott's CMOS Processor Table (IBM Z models, dates and
  architecture levels, e.g. for ARCH(n) compiler options):
  https://jlelliotton.blogspot.com/p/cmos-processor-table.html
- IBM announcement letters: https://www.ibm.com/docs/en/announcements/archive/ENUSnnn-nnn (e.g. ENUS291-320);
  2022 onward at https://www.ibm.com/docs/en/announcements/<slug>.
  Search current AND archived letters from
  https://www.ibm.com/docs/en/announcements ("Search archived
  announcements"); in-browser API:
  /docs/api/v1/search/announcements?query=...&type=announcement,archivedAnnouncement
  (type=announcement alone misses the archive). Letter text:
  /docs/api/v1/content/announcement_archive?announcement=ENUSnnn-nnn&parsebody=true&lang=en.
  ENUSnnn-nnn is the US letter; AP, ZP, LP and A prefixes are the Asia
  Pacific, Europe, Latin America and Canada editions.
- Reference list with forms codes, program numbers, FMIDs and
  announcement dates for IBM mainframe software:
  https://en.wikipedia.org/wiki/User:Chatul/References (a Wikipedia user
  page -- a finding aid, not itself WP:RS; cite the IBM documents it lists)
- Site search: https://www.ibm.com/docs/en/search/<url-encoded-query>
  works well for finding a specific topic page when the collection
  landing page's own navigation doesn't turn it up directly.

## IBM Redbooks (checked 2026-10-07; 200 to curl)
Abstract pages: https://www.redbooks.ibm.com/abstracts/<sgnnnnnn>.html;
PDFs: https://www.redbooks.ibm.com/redbooks/pdfs/<sgnnnnnn>.pdf. The
ABCs of z/OS System Programming series (SG24-6981 to SG24-6990,
SG24-6327, SG24-7621, SG24-7717) is listed by volume at
https://en.wikipedia.org/wiki/User:Chatul/References#ABCs_of_z/OS_System_Programming_by_volume.
- *ABCs of z/OS System Programming Volume 1: Introduction to z/OS and
  storage concepts, TSO/E, ISPF, JCL, SDSF, and z/OS delivery and
  installation*, SG24-6981-04, fifth edition, November 2017 (updated
  22 January 2018), ISBN 9780738442761.
  https://www.redbooks.ibm.com/redbooks/pdfs/sg246981.pdf
- *ABCs of z/OS System Programming Volume 8: An introduction to z/OS
  problem diagnosis*, SG24-6988-01, second edition, 26 July 2012 (IPCS;
  see the IPCS User's Guide entry above).
  https://www.redbooks.ibm.com/redbooks/pdfs/sg246988.pdf
- *ABCs of z/OS System Programming Volume 9: z/OS UNIX System Services*,
  SG24-6989-05, sixth edition, updated 13 May 2011 (REXX under the OMVS
  shell). https://www.redbooks.ibm.com/redbooks/pdfs/sg246989.pdf
- *Introduction to the New Mainframe: z/OS Basics*, SG24-6366-02, third
  edition, updated 5 January 2012 (Chatul cites -01, 2009).
  https://www.redbooks.ibm.com/redbooks/pdfs/sg246366.pdf
- *Implementing REXX Support in SDSF*, SG24-7419-00, 26 June 2007 (z/OS
  V1.9; found by the Redbooks search, not on Chatul's page).
  https://www.redbooks.ibm.com/abstracts/sg247419.html
- Search Redbooks: https://www.ibm.com/docs/en/redbooks, or in the
  browser pane /docs/api/v1/search/redbooksDocs?query=...&offset=0&limit=20&index-doc-type=redbook,redbookWithToc&scope=

## RexxLA
- **URL:** https://www.rexxla.org/
- **Notes:** Hosts RexxLA Newsletter archives including
  "Practicing Safe REXX" (Metz, OS/2 Magazine, 1995).
