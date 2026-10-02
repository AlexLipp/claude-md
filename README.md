# claude-md

A `CLAUDE.md` file that sets coding standards for [Claude Code](https://claude.com/claude-code), tuned for collaborative scientific computing rather than production software engineering. Use it to get consistent, well-tested, reproducible code out of Claude across projects — useful if you're a student or researcher using AI to help write code and want it to converge on good habits rather than whatever it feels like that day.

The standards are adapted from:

> Hunter-Zinck H, de Siqueira AF, Vásquez VN, Barnes R, Martinez CC (2021) "Ten simple rules on writing clean and reliable open-source scientific software." *PLoS Comput Biol* 17(11): e1009481. https://doi.org/10.1371/journal.pcbi.1009481

## What's in it

- Style and linting (ruff for Python, with a standard rule set)
- Function size, avoiding premature abstraction, no dead code
- Defensive programming, type hints, docstrings
- Testing conventions (write tests alongside code, run the full suite before finishing)
- Repository hygiene: README requirements, environment files, `inputs/`/`outputs/`/`plots/` folder layout, `.gitignore` for data, secrets handling
- Scientific-code priorities: minimal dependencies, reusable functions, reproducibility, seed handling, config files over hardcoded parameters
- Notebook conventions (`nbstripout`, logic lives in `.py` modules)
- An equivalent R section (`styler`/`lintr`, `testthat`, `roxygen2`, `renv`)

Where a rule involves creating a new file (a ruff config, a README, an environment file), Claude is instructed to ask before adding it rather than doing so silently.

## Install

Claude Code reads `CLAUDE.md` automatically from a few locations. For a file like this one that you want applied to *every* project, put it at the user level:

```bash
cp CLAUDE.md ~/.claude/CLAUDE.md
```

If you only want it to apply to one repository, put a copy at `<repo>/CLAUDE.md` instead — project-level and user-level files are both loaded and concatenated, so you can keep this as your global baseline and add repo-specific notes (e.g. a particular model architecture or dataset quirk) in a project-level file alongside it.

## Verify it's loaded

Run `/memory` inside a Claude Code session — it lists every context file that was loaded, including `~/.claude/CLAUDE.md`. If it's not there, check the file is named exactly `CLAUDE.md` (not `claude.md`, `AGENT.md`, etc. — Claude Code only auto-loads that exact filename).

## Other settings worth changing

A few things that pair well with this file but live in Claude Code's settings rather than in `CLAUDE.md` itself:

- **Output style — "Explanatory"**: makes Claude add short "Insight" notes explaining *why* it made a choice, not just what it did. Good for seeing the reasoning behind a change without having to ask for it separately. Set it per-session with `/output-style`, or make it your default everywhere by adding to `~/.claude/settings.json`:
  ```json
  { "outputStyle": "Explanatory" }
  ```
- **Output style — "Learning"**: a more hands-on teaching mode — Claude pauses at points in the task and leaves small `TODO(human)` markers for you to fill in yourself, rather than writing the whole thing end to end. Better than Explanatory when the goal is for a student to actually practice writing code, not just read explanations of code Claude wrote; weaker when you just need working code quickly. Also set via `/output-style`.
- **Plan mode**: for anything non-trivial, running Claude in plan mode (`shift+tab` to cycle modes, or `defaultMode: "plan"` in settings) makes it propose an approach before writing code, which is a good habit for checking it understood the task before it starts.

## Further reading

- [claude-code-for-hydrology](https://github.com/lorenliu13/claude-code-for-hydrology) — a tutorial specifically on using Claude Code for hydrology research workflows, worth a look alongside this file if that's your field.
- [Ten simple rules on writing clean and reliable open-source scientific software](https://doi.org/10.1371/journal.pcbi.1009481) — the paper these standards are adapted from.
