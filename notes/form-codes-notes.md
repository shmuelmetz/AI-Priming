# IBM publication form codes (order numbers): what IBM itself documents

Question (his to-do list, 1 Nov 2026): "is there a description of IBM form codes?
partially explained in, e.g., GC20-0001-1".

Answer so far (2 Nov 2026): yes, partially, in the preface of the System/370
bibliography, pages i-ii (PDF pages 3-4), sections "Subject Identification and
Filing" and "Order Number". Read in the browser from bitsavers.org; paraphrased
below. No other IBM description has been found yet.

Source:

    {{cite book
    | title     = IBM System/370 Bibliography
    | id        = GC20-0001-1
    | edition   = Second
    | date      = June 1975
    | at        = Preface, pp. i-ii
    | url       = https://bitsavers.org/pdf/ibm/370/bibliography/GC20-0001-1_370biblio_Jun75.pdf
    | publisher = [[IBM]]
    }}

(Edition inferred from the -1 suffix and the bibliography's own rule that each
reprint gets the next higher suffix; check the edition notice on PDF page 2
before using this citation. File No. S370-00.)

## What the preface explains

**Order number (form code), e.g. GC28-6421-3**

- First character: the availability ("use") key.
  - G: available to customers through the IBM representative without charge, in
    normal quantities.
  - S: for sale through IBM branch offices.
  - L: available only to licensees of the program product or licensed program.
- Second character: only some values are explained here.
  - Y: a program logic manual (title includes "Logic"), e.g. SY28-1234-5;
    available under SLSS by order-number subscription only.
  - T or Q: a temporary order number for a back edition supporting an earlier
    release of an SCP, e.g. GT00-0112-0, ST26-1234-0.
- Third character alphabetic (not a digit): the item is on microfiche, e.g.
  S2C0-0301-4, LYB0-5432-1. Exception: a bill of forms, form GBOF-1234, a single
  order number for a package of separately orderable documents.
- Suffix (after the last hyphen): the edition. The order is sequenced by the
  second character. Technical Newsletters (TNLs) update a base publication; the
  next reprint incorporates the TNLs and gets the next higher suffix, but it
  need not be reordered because its content equals the prior edition plus TNLs.
- Flags printed around the order number in the bibliography:
  - `**` before: a change (such as a new edition) or a new item;
  - `#` after the suffix: more than one edition is current;
  - `*` after: IBM World Trade use only;
  - `+` after: U.S.A. use only.

**File number, e.g. S370-36** (a separate code printed on the publication)

- Prefix: the system library (S370; or S360/S370, S360/S370/S3 when two or three
  libraries apply). With more than three libraries, a component number (one or
  two components) or GENL (more than two, or no specific component).
- Suffix: the subject code, e.g. 22 = APL, 15 = physical planning manuals and
  templates, 36 = control program. Codes 00-40 are printed on publications and
  used for SLSS (System Library Subscription Service); codes above 40 are used
  only to categorize items within system bibliographies. Part 1 of each
  bibliography starts with the index of subject codes.

## From him (2 Nov 2026, from his own knowledge)

- An order number of the form ZZxx-xxxx-ee is for IBM internal use only.
- N in the second position marks a Technical Newsletter (TNL): a page-replacement
  package for a base publication (e.g. GN.. numbers).

## What it does not explain

- The meaning of the second letter in general (A, B, C, D, H, ...), beyond Y, T
  and Q.
- The meaning of the two digits after the letters (e.g. the 20 in GC20, the 28
  in GC28), beyond their being part of the base order number.
- The rule for Redbooks (SG24-nnnn-nn) and later numbering schemes.

## IBM System/360 Bibliography, GA22-6822-14 (checked 2 Nov 2026)

https://bitsavers.org/pdf/ibm/360/bibliography/GA22-6822-14_System_360_Bibliography_Dec69.pdf
(cover: File No. S360-00, Order No. GA22-6822-14).

- Its preface (p. 2) explains file numbers and subject codes much as the 370 bibliography does,
  but says nothing about the letters or digits of the form number.
- Edition notice: "Fifteenth Edition (December, 1969). This is a major revision of, and
  obsoletes, A22-6822-13 and all associated SRL Newsletters." So:
  - the suffix is the edition minus one (-14 = fifteenth), which also supports reading
    GC20-0001-1 as the second edition;
  - the same publication moved from A22-6822 to GA22-6822 between the 14th and 15th editions:
    the leading availability letter (G) was added to the old one-letter prefix in 1969.
- The SRL Newsletter of that time was Form N20-0360, with N as the first letter (cf. his note
  that N in second position marks a TNL under the later scheme).

## Later editions (checked 2 Nov 2026)

Editions on bitsavers: S/360 GA22-6822 -4 (Aug65), -10, -12 (Dec68), -14 (Dec69), -16 (Jul71),
-19 (Aug72), -20 (Jul73), -21 (Sep74, now "System/360 and System/370 Bibliography"); S/370
GC20-0001 -1 (Jun75), -2 (Jan78), -3 (Oct79 4th ed.; also an Jan80 update), -6 (May87), -7
(Mar88); GC20-0370-3 (Nov82, industry systems and application programs); GN20-0360-75 (TNL).

- GA22-6822-21 (Sep 1974), preface p. i: the same text as GC20-0001-1 -- order numbers sequenced
  by the second character, first character (G, S or L) is the use key; file numbers and subject
  codes. Its cover notes that OS/VS, DOS/VS and VM/370 publications had moved to "Virtual Storage
  Supplement ... GC20-0001", i.e. GC20-0001 began as that supplement before becoming the S/370
  bibliography.
- GC20-0001-3 (Oct 1979, "4th ed" -- again suffix = edition minus one), preface p. i: adds that
  back editions are identified by T or Q order numbers listed under the current edition, with
  details "see 'Pseudonumbers'", and points to a section "Availability" for the G/S/L use key;
  subject codes 60-83 now apply mainly to GC20-0370. The rest of the preface (p. ii, where
  "Availability" and "Pseudonumbers" should be) is a blank page in this scan.
- Still to check: GC20-0001-2 (1978), -6 (1987), -7 (1988) for the "Availability" and
  "Pseudonumbers" sections.

## GC20-0001-7 (March 1988) -- the fullest explanation found (read 2 Nov 2026)

IBM System/370, 30xx, 4300, and 9370 Processors Bibliography, GC20-0001-7, Preface pp. iii-iv
(PDF pp. 4-5): https://bitsavers.org/pdf/ibm/370/bibliography/GC20-0001-7_370_30xx_4300_9370_Processors_Bibliography_Mar1988.pdf
Paraphrased:

- The order number has three parts, shown in a diagram: use key, form number, suffix --
  G | H20-1234 | -0 (or 00).
- Use key (first character): G = available to customers through the IBM representative without
  charge in normal quantities; S = for sale through branch offices (in some cases the first copy of
  an S publication is free; extra copies at the selling price); L = only for licensees of the
  program product or licensed program. When ordering, the suffix is not needed; the bibliography
  lists in form-number sequence, ignoring the use key.
- Form number: seven characters (H20-1234) in two parts, a prefix (H20) and a base number (1234).
  "IBM designates unique prefixes to identify various facts about an item", including:
  - Yxx: logic (program logic manuals), e.g. SY28-1234-5, LY30-1234-0;
  - BOF: bill of forms, e.g. GBOF-1234;
  - xXx (alphabetic third character): microfiche, e.g. S2C0-1234-4, LYB0-1234-1; the only
    exception is a bill of forms;
  - Txx or Qxx: pseudonumbers, e.g. GT00-1234-0, SQ26-1234-0 -- issued for back editions so that
    users of back-level programs can keep ordering them while the latest edition keeps the
    unchanged base number (only the suffix changes from release to release);
  - Nxx: Technical Newsletters (TNLs), e.g. SN20-9876, GN20-1302 -- page-replacement updates,
    shipped automatically with the manual they apply to (confirms his note on N);
  - Dxx: System Library Supplements, e.g. GD23-1234-0, SD30-1234-0 -- replacement pages that
    add function; unlike TNLs, not updates.
- Suffix: a one- or two-digit number identifying the revision level: -0 first edition, -10
  eleventh edition; "in rare instances, the first edition will bear a suffix of -1 or higher".
  (So "edition minus one" is the rule, with rare exceptions.)
- Flags: ** before = changed or newly added; * after = IBM World Trade only; + after = U.S.A. only.
- File number: e.g. S370-36, system in the prefix, subject code in the suffix; see "Organizing and
  Maintaining a System Library", GE20-0731.
- Still not explained here: the meaning of the other second letters (A, B, C, E, F, H ...) and of
  the two digits, beyond "unique prefixes to identify various facts about an item".

## Older machines

Bibliographies on bitsavers (found 2 Nov 2026): 1401/1460 A24-1495-4 (Oct67), -5 (Jun68),
GA24-1495-6; 1410 A22-6826-4; 7090/7094 A28-6306; 1620 A26-5692-1 (Aug 1963).
- 1620 Bibliography, A26-5692-1 (Aug 1963), preface: explains file numbers (1620-00; suffix = subject
  code) and the SRL subject codes (00 condensed, 01-15 machines, 20-48 programming, 80 installation
  supplies), and SRL Newsletters; nothing on what the A or the 26 mean. Already a one-letter prefix
  by 1963, so the letterless xx-xxxx-ee era is earlier still.
- Not checked: the 1401, 1410 and 7090 bibliographies; GF20-8172 (Data Processing Bibliography,
  Dec 1971; Systems Management Bibliography, Aug 1980) in pdf/ibm/generalInfo; GE20-0731.

## From him (2 Nov 2026), continued

- For really old manuals the number has the form xx-xxxx-ee (no letter prefix).

## Next places to look

- The System/360 bibliography, GA22-6822 (e.g. -14, Dec 1969) on bitsavers: an
  earlier edition of the same preface may explain more.
- IBM "Bibliography of Data Processing Techniques", GF20-8172, subject code 00,
  cited in the same preface's References.
- IBM Data Processing Glossary, GC20-1699.

## 650 and 700 series (searched 2 Nov 2026)

bitsavers IndexByDate.txt has no bibliography for the 650, 701, 702, 704, 705 or 709. Nearest:
- ibm/7090/A28-6306_7090_7094_Bibliography.pdf
- ibm/7090/N28-6306-2_7090_Pub_Softw_Index_Jun65.pdf (publications and software index)
- ibm/7094/S23-4001_7094suppl_Indexing_Oct62.pdf
- ibm/generalInfo/320-1621-15_IBM_Marketing_Publications_KWIC_Index_Jun67.pdf and G320-1621-39
  (Jan 1981). CORRECTION (his, 2 Nov 2026): 320-1621 is not letterless -- "3 is the prefix" (a
  numeric prefix in the letter's place, keeping it through G320 in 1981).
- ibm/generalInfo/C20-8062_Index_Organization_for_Information_Retrieval_1961.pdf
Nothing local on disk.

## Also checked 2 Nov 2026
- GE20-0731 "Organizing and Maintaining a System Library" (cited by GC20-0001-7): not on bitsavers.
- GF20-8172-7, Bibliography of Data Processing Techniques (Dec 1971), preface: about systems
  techniques and subject codes 50/60; nothing on order-number structure.

## Pre-360 bibliographies, downloaded and read 3 Nov 2026

Downloaded (bitsavers.org refuses curl/WebFetch with 403; the mirror works) from
https://www.mirrorservice.org/sites/www.bitsavers.org/pdf/ibm/ into
Documents\IBM\bibliographies\:
- A28-6306_7090_7094_Bibliography.pdf (7090/7094 Bibliography, File 7090-00, no suffix, undated; scan, no text layer)
- N28-6306-2_7090_Pub_Softw_Index_Jun65.pdf (SRL Newsletter N20-7090.25, Re: A28-6306-2, 14 Jun 1965; scan)
- A24-1495-4_1401_and_1460_Bibliography_Oct67.pdf (Fifth Edition, Oct 1967; has text layer)
- A22-6826-4_1410_biblio.pdf (1410/7010 Bibliography, Fifth Edition; scan)

Findings:
- 1401/1460 (A24-1495-4) and 1410/7010 (A22-6826-4) share one preface, "Systems
  Reference Library": file numbers (system or component type prefix, or GENL;
  suffix = subject code, e.g. 15 physical planning, 33 sort/merge, 60 application
  programs, 80 installation supplies), TNLs, SRL Newsletter, and the form-number
  revision suffix (a reprint incorporating replacement pages gets a new suffix, so
  more than one edition can be current). Nothing on the prefix letter or the
  two-digit group.
- 7090/7094 (A28-6306): cover only; subject-code listing; no preface on numbers.
- N20-7090.25 newsletter: lists forms with suffixes (A22-6535-0 etc.: -0 written
  out) and TNLs in N22-/N24-/N26- form; no explanation. Note the newsletter's own
  number, N20-7090.25, uses a dotted sequence rather than -ee.
- Scope of the check: the 1401 book's whole text layer was grepped (prefix, form
  number, letter); the three scans were then read in full, every page (3 Nov 2026).
  None explains the prefix letter or the two-digit group anywhere.
- Indirect evidence, 7090/7094 A28-6306 Part 3: each abstract ends with a type tag,
  and the tag tracks the letter: A and C = "Ref. Man." (A also "OEM" for original
  equipment manufacturers' information); D and F = "GI Man." (general information);
  G and J = "Bulletin"; L = RPQ/special-feature bulletins (1410 book); R = "Educ"
  (order through District Education Centers); X = installation supplies (reference
  cards, diagram pads, templates, coding forms); the bibliography itself = "SRL".
  Numeric prefixes also occur: 220-8045 (CODASYL COBOL report, sold by the GPO),
  328-1219 ... 328-1624 (SHARE SOS manual distributions, looseleaf). The scheme is
  never stated; this is only the observed correlation.
- 1410 A22-6826-4, abstract of A26-5754 (1011 Paper Tape Reader): it "replaces and
  renders obsolete" D24-1044-1 and TNL N24-0032 -- the same manual reissued with a
  different letter (D, GI Man. -> A, Ref. Man.) and a different group (24 -> 26).
- N20-7090.25 (SRL Newsletter, 14 Jun 1965): "* Minor Revision, Does Not Obsolete
  Prior Edition Also Listed" -- a starred suffix marks a minor revision that leaves
  the prior edition current; a "superseded as indicated" list maps replaced items
  (often TNLs, N22-/N28-) to the new suffix (e.g. C28-6248-3). Section 3 lists
  program numbers in a different scheme, 7090-CB-806, 0709-SM-066 (machine-type
  letters-number). Nothing on form-number structure.
- Letters seen in use in the 1960s lists: A, C, D, F, G, J, L, N (D22-6508,
  G22-6647, J22-6690, L24-1408, F20-8161, C20-1607). Their meanings are given only
  by the later GC20-0001-7 preface, not by these books.

## From him (3 Nov 2026)

- "AFAIK, xx-xxxx is just six characters except for special cases like BOF and
  microfiche." That is, apart from those special cases, the xx-xxxx part is one
  six-character identifier; the two-digit group and the four-digit base carry no
  separate meaning.
- "xHyy-yyyy-ee is also common" (3 Nov 2026): a letter in the second position after
  the use key, as in GH20-1234 (the GC20-0001-7 diagram's own example, G | H20-1234),
  then two digits, the four-digit base and the suffix.

## Read 12 Nov 2026 (his "yes" to reading the remaining sources)

Downloaded from https://www.mirrorservice.org/sites/www.bitsavers.org/pdf/ibm/ into
Documents\IBM\bibliographies\ (text layers extracted beside them as .txt):
- generalInfo/GC20-1699-4_IBM_Data_Processing_Glossary_5th_ed_197212.pdf (146 pp., 3.8 MB)
- 370/bibliography/GC20-0001-2_System_370_Bibliography_Jan78.pdf (452 pp., 46 MB)
- 370/bibliography/GC20-0001-6_370_30xx_4300_9370_Processors_Bibliography_May1987.pdf (1132 pp., 160 MB)

**GC20-1699-4, IBM Data Processing Glossary (Fifth Edition, Dec 1972):** no entry for form
number, order number or publication number (whole text layer searched). Not a source.

**GC20-0001-2, IBM System/370 Bibliography (Jan 1978; revised 20 Jan 1978 by TNL GN20-0001-1),
Preface pp. i-ii.** Paraphrased:
- "The publication order number identifies various facts about a publication", then sections
  Availability (use key G/S/L, as in -7), Logic (Y as the second character), microfiche
  (alphabetic third character; bill of forms the only exception) and Pseudonumbers.
- Pseudonumbers: T or Q in the second position marks a temporary order number for a back
  edition that supports an earlier release of an SCP or licensed program (GT00-0112-0,
  ST26-1234-0). Purpose: users of back levels keep ordering the matching publications, while
  the latest edition keeps its base order number from release to release (only the suffix
  changes), so SLSS quantities carry over.
- **The assignment is schematic** (new in this edition, not in -1 or -7): GC28-1234-0 becomes
  GT28-1234-0 when GC28-1234-1 is issued for a new release; GC28-1234-1 in turn becomes
  GQ28-1234-0 when -2 is issued for a still newer release; after that, GT00-xxxx-0 numbers are
  issued to hold stock of the -2, -3 and later editions. **For PLMs (program logic manuals) the
  scheme is the same "except for an addition of 40 to the prefix to prevent duplications."**
  This is the first IBM statement found that operates on the two digits: the pseudonumber of a
  logic manual adds 40 to them. (No worked PLM example is given; the exact result, e.g. which
  part of the prefix the 40 is added to, is not shown.)
- Flags: ** before (change or new item), # after the suffix (more than one edition current),
  * after (World Trade only), + after (U.S.A. only). "Maximum case: **GC19-0001-5*".
- SL Supplements: "In future, all SL Supplements are to be uniquely identified by a D prefix
  just as TNLs are uniquely identified by N" -- the D prefix that -7 (1988) describes as in use.
- Revisions and consolidations: a new edition incorporating TNLs and Supplements gets the next
  higher suffix, and the prior edition is given a pseudonumber prefix (T or Q).

**GC20-0001-6 (May 1987), Preface:** the same Order Number description as -7, plus
pseudonumbers in the SLSS-key scheme for VM and VSE Pregenerated Packages, with a worked
example: a manual's individual pseudonumber moves from ST28-1234 to SQ28-1234 at one release
and to ST00-0765 at the next. Nothing further on the second letter or the two digits.

Still unexplained by any IBM source found: the general meaning of the second letter (A, B,
C, E, F, H ...) and of the two digits, apart from the +40 rule for logic-manual pseudonumbers.

## Read 18 Nov 2026

Downloaded 18 Nov 2026 into Documents\IBM\bibliographies\:
- GN20-0360-75, SRL Newsletter, Accumulative Index of Publications and Programs, IBM
  System/360 and System/370 (June 1971), p. 1: "The order number consists of eight
  characters"; the first is the use key (G, L, S as above); the last seven are the form
  number. Same wording as later prefaces; nothing on the second letter or the digits.
  http://bitsavers.trailing-edge.com/pdf/ibm/360/bibliography/GN20-0360-75_360-370TNL.pdf
- GH30-0232-1, IBM 5110/5120 Computing Systems Bibliography (Sep 1980), Preface p. iii:
  use key G/S/L; Y second character = logic manual (SY21-1234); GBOF bill of forms; file
  number S5110-15 = system prefix + subject code (15 physical planning, 23 BASIC); codes
  00-40 for SLSS, above 40 only for bibliography classification. Nothing new beyond -1/-7.
  http://bitsavers.trailing-edge.com/pdf/ibm/5110/GH30-0232-1_IBM_5110_5120_Computing_Systems_Bibliography_Sep1980.pdf
- GC20-0001-1 (June 1975) full PDF, 34 MB, from bitsavers.org by curl (worked on 18 Nov):
  preface confirms the paraphrase above.
