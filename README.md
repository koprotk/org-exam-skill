# org-exam-skill

An [OpenCode](https://opencode.ai) skill for authoring exams, quizzes, and
problem sets in Org mode using the
[`org-exam`](https://github.com/koprotk/org-exam) package — an Org export
backend for the LaTeX `exam` document class.

The skill teaches the agent the org-exam markup (questions/parts/subparts,
points, choices, checkboxes, solutions, headers/footers) and ships a linter
that validates `.org` exam files against the rules org-exam actually enforces.

## Contents

```
.
├── SKILL.md                          # skill instructions + workflow
├── references/
│   └── org-exam-reference.md         # full feature & keyword reference
├── examples/
│   └── sample-exam.org               # complete, runnable example
├── scripts/
│   └── validate-org-exam             # Emacs Lisp linter + export smoke test
├── README.md
└── LICENSE
```

## Installation

Install the skill so OpenCode discovers it (the directory name becomes the
skill ID `org-exam`):

```sh
# Symlink (recommended — stays in sync with your checkout):
ln -s "$HOME/Projects/personal/org-exam-skill" "$HOME/.config/opencode/skills/org-exam"

# Or copy:
cp -r org-exam-skill "$HOME/.config/opencode/skills/org-exam"
```

Restart OpenCode (or reload configuration) and the skill is available. Ask for
"an exam in org-exam" and the agent will load it.

## Validator

`scripts/validate-org-exam` lints `.org` exam files. Structural and style
checks need only built-in Org; the `--export` smoke test additionally loads
`org-exam`.

```sh
scripts/validate-org-exam examples/sample-exam.org          # lint only
scripts/validate-org-exam --export examples/sample-exam.org # also export to .tex
scripts/validate-org-exam --org-exam-path ~/src/org-exam --export exam.org
```

Checks performed:

- `#+LATEX_CLASS: exam` present (warn if missing/wrong).
- Headline depth: levels 1–3 only; a level-4 headline is dropped (error).
- Nesting: parts `**` under questions `*`, subparts `***` under parts `**`; no
  skipped levels (error).
- Points: `:POINTS:` property and `[N pts]` brackets must be numeric (warn).
- `@correct`: only at the very start of a `-` (choices) or `+` (checkboxes)
  item, never in prose or other lists (warn).
- `:solution:` drawers present but `answers` class option missing (warn).

The script exits `0` when clean (warnings allowed) and `1` on any error. The
`--export` path defaults to `~/Projects/personal/org-exam`; override with
`ORG_EXAM_PATH` or `--org-exam-path DIR`.

## Exporting from Emacs

With `org-exam` installed and loaded:

| Command                         | Result                |
| ------------------------------- | --------------------- |
| `M-x org-exam-export-to-latex`  | Export to a `.tex`    |
| `M-x org-exam-export-as-latex`  | Preview in a buffer   |

## License

[GPL-3.0](LICENSE)
