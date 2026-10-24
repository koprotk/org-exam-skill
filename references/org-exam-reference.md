# org-exam reference

Complete reference for authoring exams with the `org-exam` export backend.
`org-exam` inherits from the standard Org `latex` backend, so anything valid in
an Org → LaTeX export works here too (math with `$…$` / `\(…\)`, `#+LATEX_HEADER:`,
source blocks, tables, links, …).

## Preamble keywords

```org
#+LATEX_CLASS: exam
#+LATEX_CLASS_OPTIONS: answers
#+TITLE: Midterm Exam
#+AUTHOR: Prof. Example
#+DATE: Fall 2026
#+LANGUAGE: en
```

| Keyword | Notes |
| --- | --- |
| `#+LATEX_CLASS: exam` | **Required.** Selects the LaTeX `exam` document class. |
| `#+LATEX_CLASS_OPTIONS: answers` | Show solutions and bold correct answers. Brackets are optional (`answers` or `[answers]` both work). |
| `#+LANGUAGE:` | Loads `babel` with the right language. |
| `#+OPTIONS: toc:t` | Re-enable a table of contents (disabled by default for exams). |

## Structure: questions, parts, subparts

Headline depth maps directly to the exam class nesting:

```org
* Question 1 [10 pts]     ; \question[10]
** Part a [4 pts]          ; \part[4]
*** Subpart i [3 pts]      ; \subpart[3]
```

Rules:

- Only levels 1–3 exist. A level-4 headline is dropped (org-exam prints a
  warning).
- A part must be directly under a question; a subpart directly under a part.
  Skipping a level orphans the headline.
- Body text written directly under a headline (before any child headline) is
  emitted as the question/part/subpart text.

## Points

Two equivalent ways, both optional:

```org
* Question [15 pts]
```

```org
* Question
:PROPERTIES:
:POINTS: 15
:END:
```

- Inline form accepts `[15]`, `[15 pts]`, `[15 points]` (decimals allowed, e.g.
  `[1.5 pts]`).
- The property drawer is handy when the title is crowded.
- The inline `[N pts]` text is stripped from the rendered title automatically.

## Choices (multiple choice, single answer)

Unordered list with `-`. Mark the correct choice with a leading `@correct `:

```org
* What is the capital of France? [1 pts]
- London
- Berlin
- @correct Paris
- Madrid
```

- Choices render as `A.`, `B.`, `C.`, … (marker style follows the exam class
  options).
- `@correct` is recognized only at the **very start** of the item. A literal
  `@correct` later in the text is left as-is.
- Correct choices are printed bold only when `answers` is set.

## Checkboxes (select all that apply)

Unordered list with `+`. Mark each correct item with `@correct `:

```org
* Which are prime? [10 pts]
+ @correct 2
+ @correct 3
+ 4
+ @correct 5
```

## Solutions

Wrap the answer in a `:solution:` drawer:

```org
* Question [10 pts]
What is 2 + 2?

:solution:
4.
:end:
```

- The solution box prints only when the document is exported with the
  `answers` class option; otherwise it is hidden.
- The drawer name is case-insensitive (`:SOLUTION:` also works).

## Running headers and footers

Setting any of these switches the document to the `headandfoot` page style and
emits the corresponding `\header{}{}{}` / `\footer{}{}{}`:

```org
#+EXAM_HEADER_LEFT: CS 101
#+EXAM_HEADER_CENTER: Midterm
#+EXAM_HEADER_RIGHT: Spring 2026
#+EXAM_FOOTER_RIGHT: \thepage/\numpages
```

- Values are inserted verbatim, so LaTeX macros like `\thepage` and
  `\numpages` work.
- If you set a header but no footer, a default centre footer of `\thepage` is
  added so page numbers stay visible (disable via `org-exam-default-footer-center`).
- Images in a slot: use an Org file link, optionally sized:

  ```org
  #+EXAM_HEADER_LEFT: [[file:logos/university.png]]
  #+EXAM_HEADER_RIGHT: [[./logos/department.pdf]] :width 30mm
  #+EXAM_FOOTER_LEFT: [[./logos/footer-mark.png]] :height 8mm
  ```

  - The `file:` prefix is optional.
  - Append `:height SIZE` or `:width SIZE` (any LaTeX length). Default height is
    `org-exam-header-image-height` (1.5 cm).
  - Paths resolve relative to the exported `.tex` file.
  - A value that does not start with `[[…]]` is treated as plain text/LaTeX.

## Point label

The exam class prints ` point` / ` points` in the margin by default; org-exam
replaces it with the language-neutral ` pts`. Override per document:

```org
#+EXAM_POINT_NAME: puntos
```

The value is rendered with a leading space automatically. Set the
`org-exam-point-name` defcustom to `nil` to keep the exam class default.

## Configuration (defcustoms, group `org-exam`)

| Variable | Default | Effect |
| --- | --- | --- |
| `org-exam-point-name` | `" pts"` | Margin point label (`\pointname`). |
| `org-exam-default-footer-center` | `"\\thepage"` | Centre footer used when a header is set but no footer is. `nil` to skip. |
| `org-exam-header-image-height` | `"1.5cm"` | Default height for images in header/footer slots. |

## Export commands

| Command | Result |
| --- | --- |
| `M-x org-exam-export-to-latex` | Export to a `.tex` file |
| `M-x org-exam-export-as-latex` | Preview in a buffer |

Optional `C-c C-e` dispatch entry:

```emacs-lisp
(eval-after-load 'ox
  '(org-export-add-to-dispatch
    'org-export-dispatch
    '("Exam" "x" org-exam-export-to-latex)))
```

## Full keyword reference

`#+EXAM_HEADER_LEFT|CENTER|RIGHT` — running page header (text, LaTeX, or Org file link).
`#+EXAM_FOOTER_LEFT|CENTER|RIGHT` — running page footer (same value rules).
`#+EXAM_POINT_NAME` — per-document override of `org-exam-point-name`.
