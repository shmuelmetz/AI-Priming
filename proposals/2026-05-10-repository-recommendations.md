<!-- Provenance: received as Downloads\ai_priming_repository_recommendations.md
     (dated 10 May 2026); its author is not recorded in the file. Filed
     1 Nov 2026. Status as of filing: none of the proposed files
     (ANTI_PATTERNS.md, STYLE.md, TESTS.md, MISCONCEPTIONS.md,
     LANGUAGE_COMPARISON.md) exist, and there are no HLASM or JCL rule sets;
     the repo has RULES/IDIOMS/KEYWORDS/BIBLIOGRAPHY per language,
     CONVENTIONS.md and LaTeX-RULES.md. Not adopted; for evaluation. -->

# Recommendations for the AI-Priming Repository

## Overview

The repository already has a strong conceptual structure. The most valuable next step is improving:

1. AI reliability
2. Contrastive learning support
3. Evaluation capability
4. Cross-language consistency
5. Machine-readable guidance

This document proposes additions intended specifically to improve large-language-model behavior when generating code for historically underrepresented languages.

---

# Recommended Repository-Level Additions

## 1. `ANTI_PATTERNS.md`

### Purpose
Document common LLM mistakes, hallucinations, and non-native coding patterns.

### Why It Matters
LLMs often produce:
- invented built-ins
- hybrid syntax from other languages
- modern-language assumptions
- incorrect keyword usage
- dialect confusion

A dedicated anti-pattern document directly addresses these failure modes.

### Suggested Structure

```text
Wrong:
...

Correct:
...

Reason:
...
```

### Example Topics
- Confusing ooRexx with classic Rexx
- Importing Java idioms into NetRexx incorrectly
- Invented parser behavior
- Misusing SIGNAL and CALL
- Hallucinated object methods

---

## 2. `STYLE.md`

### Purpose
Separate stylistic guidance from strict correctness rules.

### Suggested Contents
- indentation conventions
- capitalization conventions
- naming standards
- comment formatting
- continuation-line style
- preferred idioms
- discouraged but legal constructs

### Benefit
Produces code that appears native and idiomatic rather than merely syntactically valid.

---

## 3. `TESTS.md`

### Purpose
Create a conformance and evaluation suite for LLMs.

### Example Structure

```text
Prompt:
Write a parser for fixed-column input.

Expected Properties:
- Uses PARSE VALUE
- Avoids manual token slicing
- Handles NOVALUE correctly
```

### Benefit
Allows objective evaluation of model improvement after priming.

---

## 4. `MISCONCEPTIONS.md`

### Purpose
Document assumptions programmers from other ecosystems commonly make.

### Suggested Sections
- Assumptions from C/C++
- Assumptions from Python
- Assumptions from Java
- Assumptions from shell scripting
- Assumptions from modern dynamic languages

### Benefit
Prevents cross-language contamination in generated code.

---

## 5. `LANGUAGE_COMPARISON.md`

### Purpose
Provide a high-level comparison matrix across supported languages.

### Suggested Topics
| Feature | Rexx | ooRexx | NetRexx | PL/I | HLASM | JCL |
|---|---|---|---|---|---|---|
| Typing Model | | | | | | |
| Object Support | | | | | | |
| Parsing Model | | | | | | |
| Numeric Semantics | | | | | | |
| Exception Handling | | | | | | |
| Macro Facilities | | | | | | |
| Execution Environment | | | | | | |

### Benefit
Improves cross-language prompting and reduces dialect confusion.

---

# Recommended Content-Level Improvements

## 6. Negative Examples

### Purpose
Teach by contrast.

### Format

```text
Wrong:
...

Correct:
...

Reason:
...
```

### Benefit
LLMs learn extremely effectively from explicit contrastive examples.

---

## 7. Canonical Task Examples

### Suggested Tasks
- file I/O
- string parsing
- report generation
- error handling
- subprocess invocation
- record processing
- data transformation
- batch control

### Benefit
Provides reusable archetypes for code generation.

---

## 8. Version Boundary Sections

### Purpose
Prevent generation of mixed-version syntax.

### Example

```text
## ooRexx 5.x only
...
```

### Benefit
Reduces dialect blending and historical confusion.

---

# AI-Specific Enhancements

## 9. Machine-Readable Metadata

### Suggested Format

```yaml
dialect: ooRexx
strict_keywords: true
supports_objects: true
case_sensitive_keywords: false
supports_exceptions: true
```

### Benefit
Supports future automation, validation, and tooling.

---

## 10. Priority Tagging

### Example

```text
[CRITICAL]
Do not invent keywords.

[IMPORTANT]
Prefer canonical parsing idioms.
```

### Benefit
Helps models weight instructions appropriately.

---

## 11. Standard Prompt Preamble

### Suggested Template

```text
You are acting as an expert ooRexx programmer.
Follow RULES.md as authoritative.
Do not invent syntax.
Prefer canonical idioms from IDIOMS.md.
```

### Benefit
Improves consistency across sessions and models.

---

# Tooling and Maintenance Recommendations

## 12. Validation Scripts

### Suggested Checks
- duplicate keyword detection
- malformed Markdown detection
- missing required sections
- inconsistent terminology
- broken internal links

### Benefit
Improves repository quality and maintainability.

---

## 13. Benchmark Sessions

### Suggested Content
- unprimed model output
- primed model output
- error analysis
- measurable improvement notes

### Benefit
Demonstrates practical value of the repository.

---

# Additional Recommended Languages

The repository concept is especially valuable for historically important languages with sparse modern training data.

The following additions are recommended:

## HLASM

### Why
- historically important
- extremely underrepresented in modern training corpora
- syntax-sensitive
- high hallucination risk for LLMs
- valuable for enterprise modernization and maintenance

### Suggested Files
- `RULES.md`
- `IDIOMS.md`
- `ANTI_PATTERNS.md`
- `MACROS.md`
- `INSTRUCTIONS.md`
- `REGISTER_USAGE.md`

### Important Topics
- base register conventions
- save-area handling
- macro invocation structure
- condition-code handling
- packed decimal operations
- DSECT conventions
- assembler directives

---

## JCL

### Why
- operationally critical in many enterprise environments
- highly syntax-sensitive
- extremely easy for LLMs to hallucinate incorrectly
- sparse high-quality public examples

### Suggested Files
- `RULES.md`
- `IDIOMS.md`
- `ANTI_PATTERNS.md`
- `DATASET_CONVENTIONS.md`
- `PROC_USAGE.md`

### Important Topics
- DD statement structure
- dataset naming conventions
- DISP semantics
- symbolic parameters
- PROC expansion
- condition-code handling
- concatenation rules
- cataloged procedures

---

# Highest-Value Recommendation

If only one addition is implemented initially:

## `ANTI_PATTERNS.md`

This provides the greatest direct improvement to LLM reliability because it explicitly targets hallucinations and non-native code generation patterns.

Traditional documentation teaches what is correct.

Anti-pattern documentation teaches what models are most likely to get wrong.

