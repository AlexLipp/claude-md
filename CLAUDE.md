<!--
  Coding standards adapted from:
  Hunter-Zinck H, de Siqueira AF, Vásquez VN, Barnes R, Martinez CC (2021)
  "Ten simple rules on writing clean and reliable open-source scientific software"
  PLoS Comput Biol 17(11): e1009481. https://doi.org/10.1371/journal.pcbi.1009481
-->

# Coding standards

These apply to all code written in this and every project, unless a
project-level CLAUDE.md overrides something below.

If a specific request seems to conflict with a guideline here (e.g.
asking for an abstraction, dependency, or class with no current
second use case), say so briefly and confirm before proceeding,
rather than silently complying or silently overriding. Go ahead with
what was asked once confirmed. The same applies to creating any
repo-level file this document references (ruff config, README, environment file) when one doesn't exist yet — ask
first, referencing the relevant section below, rather than creating
it silently.

## Style and linting
- All Python code follows the ruff config in pyproject.toml.
- Ruff only picks up rules from a pyproject.toml/ruff.toml it finds in
  the repo — it does not read this file. If a project has none, add
  the standard rule set below (per the top-level rule, ask first).
- After any code change, run `ruff check --fix .` and `ruff format .`
  before considering the task done.
- Do not silence a rule with `# noqa` without a one-line comment
  explaining why.
- Standard rule set to add if missing:
  ```toml
  [tool.ruff]
  line-length = 88
  target-version = "py311"

  [tool.ruff.lint]
  select = [
    "E", "F",      # pycodestyle + pyflakes
    "I",           # isort — import order
    "UP",          # pyupgrade — modern syntax
    "B",           # bugbear — common bug patterns
    "SIM",         # simplify — redundant code
    "C4",          # comprehension cleanups
    "NPY",         # numpy-specific gotchas
    "PTH",         # use pathlib over os.path
    "RUF",         # ruff's own extra checks
  ]
  ```

## Simplicity and structure
- Keep functions single-purpose, under ~40 lines, and under ~5
  arguments. Beyond that, group related arguments into a dataclass
  or dict.
- Avoid premature abstraction: write the direct version first. Only
  introduce a class, config system, or generic interface once a
  second real use case actually needs it — don't build for
  hypothetical future cases.
- Delete dead code. Remove commented-out code, unused functions, and
  unused imports rather than leaving them "just in case" — that's
  what version control is for.
- Favor plain, explicit code over clever one-liners. A reader
  unfamiliar with the codebase should be able to follow the logic
  without extra context.
- Separate data loading, computation, and plotting/output into
  distinct functions so each can be tested, reused, or swapped out
  independently.

## Defensive programming
- Validate function inputs (types, value ranges) at the top of a
  function when they come from outside the codebase (files, user
  input, external APIs).
- Raise clear exceptions with a message that tells the user how to
  fix the problem, rather than letting the code fail with an opaque
  error further downstream.

## Type hints and documentation
- Add type hints to all function signatures.
- Give every public function a numpy-style docstring: one-line
  summary, Parameters, Returns.

## Testing
- Write unit tests alongside new code, not after. Each test checks
  one behavior, is isolated from other dependencies, and runs fast.
- Use fixtures for shared/reusable test setup rather than repeating
  setup code.
- Before refactoring, make sure tests pass; refactor in small
  increments; rerun tests after each increment.
- Run the full test suite (`pytest`) before considering any task
  done, not just tests for the code just touched. If a test fails,
  fix it or explain why before finishing — don't leave a task
  complete with a known-failing suite.
- New modules should have meaningful test coverage (~60%+ as a
  floor, not a target).

## Repository structure and hygiene
- Every repo has a README covering: brief purpose (what this does
  and why), how to install dependencies, how to set up the
  environment, how to run the code, and how to run the tests.
- Environment: use a `environment.yml` (conda) listing pinned
  dependencies. README includes the setup commands, e.g.:
  ```
  conda env create -f environment.yml
  conda activate <env-name>
  ```
- Folder layout: raw/external data goes in `inputs/`, generated
  results (files, numbers, processed data) go in `outputs/`, and
  figures go in `plots/`. Code should read from `inputs/` and write
  to `outputs/`/`plots/` — never the reverse.
- Data hygiene: don't commit anything in `inputs/`, `outputs/`, or
  `plots/`, or other large/generated files, to git — add them to
  `.gitignore`. Note in the README where the actual data lives (e.g.
  a shared drive, an external DOI, a database) if it isn't trivial to
  regenerate.
- Secrets: never commit API keys, credentials, or tokens. Use a
  `.env` file (git-ignored) or environment variables, and note the
  required variable names in the README rather than their values.

## Scientific-code priorities (collaborative research code, not production software)
- Prefer the standard library, numpy, pandas, and scipy over niche
  packages. Only add a new dependency when it saves substantial,
  non-trivial code — state the reason when you do.
- Write functions to be reusable outside the specific script that
  first needed them: no hardcoded file paths or magic numbers inside
  function bodies — pass them as arguments with sensible defaults.
- Optimize for a collaborator (or future-you) reading the code once,
  months later, without a walkthrough — not for raw performance or
  enterprise-style extensibility.
- Scripts that produce a result (figure, number, output file) should
  run end-to-end from a single entry point with no manual steps in
  between, so results are reproducible by someone else.
- When a piece of code embodies a specific research/method decision
  (a threshold, a formula, an assumption), say so in a comment —
  future readers need to know it's a choice, not an accident.
- Where a genuine, profiled bottleneck exists (not a guess), consider
  whether a Cython module for the hot loop would help, and propose
  it rather than applying it — this is a bigger structural change
  than routine code, so it goes through the same confirm-first
  approach as new config files.
- Set and document a fixed random seed for anything stochastic
  (train/test splits, bootstrapping, simulations, shuffling). Don't
  default to 42 — it's used so ubiquitously that a result depending
  on it can look cherry-picked or untested against other seeds; pick
  a different fixed value, or better, run across a few seeds when
  the result should be robust to the choice.
- Keep parameters and thresholds that might reasonably change between
  runs (file paths, cutoffs, model settings) in a small config file
  (YAML/JSON) rather than hardcoded — so re-running with different
  settings doesn't require editing code.

## Notebooks
- Strip output before committing (`nbstripout`, set up once per repo
  via `nbstripout --install`) so diffs stay readable and outputs
  don't bloat the repo.
- Keep reusable logic in `.py` modules that the notebook imports,
  rather than writing functions only inside notebook cells — a
  notebook should read like a short script that calls into tested
  code, not contain the logic itself.

## R
Most of the above applies regardless of language. R-specific tooling:
- Style: follow the tidyverse style guide; use `styler` to
  auto-format and `lintr` for linting, mirroring how ruff is used for
  Python.
- Testing: use `testthat`, following the same principles as the
  Python testing section above (one behavior per test, fixtures via
  `setup()`/`test_that()` helpers, run the full suite before
  finishing a task).
- Documentation: use `roxygen2` comments for functions, equivalent to
  the numpy-style docstrings required for Python.
- Dependencies: pin package versions with `renv` so environments are
  reproducible; same minimal-dependency preference as Python.
- If a project has no `.lintr` or styler config yet, ask before
  adding one, same as for ruff.
