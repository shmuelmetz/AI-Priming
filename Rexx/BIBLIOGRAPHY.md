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
- **URL:** https://www.ibm.com/docs/en/zos/latest?topic=rexx-tsoe-reference
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
  https://www.vm.ibm.com/library/730pdfs/73631400.pdf); current z/VM
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
  currently-maintained documentation.

### z/VM EXECIO Command Reference
- **Title:** EXECIO (CMS command), part of z/VM CMS Commands and
  Utilities Reference
- **URL:** https://www.ibm.com/docs/en/zvm/7.2.0?topic=commands-execio
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
  Windows edition) no Workplace-Shell functions, consistent with AIX
  having no Workplace Shell either.

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
- Site search: https://www.ibm.com/docs/en/search/<url-encoded-query>
  works well for finding a specific topic page when the collection
  landing page's own navigation doesn't turn it up directly.

## RexxLA
- **URL:** https://www.rexxla.org/
- **Notes:** Hosts RexxLA Newsletter archives including
  "Practicing Safe REXX" (Metz, OS/2 Magazine, 1995).
