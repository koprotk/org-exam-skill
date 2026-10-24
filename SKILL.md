---
name: Org Exam
description: Author exams, quizzes, tests, and problem sets in Org mode using the org-exam package, an Org export backend for the LaTeX exam document class. Use when creating or editing an .org exam file with questions, parts, subparts, points, multiple-choice choices, checkboxes, solutions, and running headers/footers.
---

# Org Exam

Author exam documents in Org mode and export them to LaTeX with the
[`org-exam`](https://github.com/koprotk/org-exam) package. `org-exam` is a
derived Org export backend for the LaTeX `exam` document class, so every
top-level Org headline becomes an exam question, and all standard Org syntax
(math, lists, links, tables, source blocks) keeps working.

## When to use

Use this skill when asked to create or modify an exam, quiz, test, worksheet,
problem set, or answer key as an `.org` file.

## Workflow

1. Read `references/org-exam-reference.md` for the full feature reference and
   `examples/sample-exam.org` for a complete, runnable example.
2. Write the document preamble (title, class, options, headers/footers).
3. Write the questions as headlines, obeying the structural rules below.
4. Run `scripts/validate-org-exam FILE.org` and fix every `ERROR:`.
5. Export from Emacs with `M-x org-exam-export-to-latex` (to a `.tex` file) or
   `M-x org-exam-export-as-latex` (preview in a buffer).

## Structural model

| Org markup              | Exam element | LaTeX      |
| ----------------------- | ------------ | ---------- |
| `*` headline (level 1)  | Question     | `\question` |
| `**` headline (level 2) | Part         | `\part`     |
| `***` headline (level 3)| Subpart      | `\subpart`  |

Hard rules — breaking them silently drops or misplaces content:

- A question is **exactly one `*`**. The first headline must be a `*` question.
- A part `**` must be nested directly under a question `*`; a subpart `***`
  must be nested directly under a part `**`. Never skip a level.
- Nothing deeper than `***` is supported — level 4+ is dropped with a warning.
- Non-headline content placed before the first `*` (instructions, name/section
  fields, formula sheets) is emitted automatically above the questions.

## Quick reference

- Preamble: `#+LATEX_CLASS: exam` (required), plus optional `#+TITLE:`,
  `#+AUTHOR:`, `#+DATE:`, and `#+LANGUAGE:`.
- Answer key / solutions visibility: `#+LATEX_CLASS_OPTIONS: answers` (brackets
  optional). Without it, solutions and bold correct answers are hidden.
- Points: inline `* Question [15 pts]` or a `:POINTS: 15` property drawer.
- Multiple choice: unordered list with `-`; mark the correct one with a leading
  `@correct `.
- Checkboxes (select-all-that-apply): unordered list with `+`; mark correct
  ones with a leading `@correct `.
- Solutions: wrap the answer in a `:solution:` drawer.
- Running headers/footers: `#+EXAM_HEADER_LEFT|CENTER|RIGHT` and
  `#+EXAM_FOOTER_LEFT|CENTER|RIGHT`.
- Point label: `#+EXAM_POINT_NAME: puntos` (default is ` pts`).

Read `references/org-exam-reference.md` for exact syntax, examples, and edge
cases. See `examples/sample-exam.org` for a complete exam.
