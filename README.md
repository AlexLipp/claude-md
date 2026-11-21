# claude-md

A `CLAUDE.md` file that sets coding standards for [Claude Code](https://claude.com/claude-code), tuned for collaborative scientific computing rather than production software engineering. Use it to get consistent, well-tested, reproducible code out of Claude across projects — useful if you're a student or researcher using AI to help write code and want it to converge on good habits rather than whatever it feels like that day.

The standards are adapted from:

> Hunter-Zinck H, de Siqueira AF, Vásquez VN, Barnes R, Martinez CC (2021) "Ten simple rules on writing clean and reliable open-source scientific software." *PLoS Comput Biol* 17(11): e1009481. https://doi.org/10.1371/journal.pcbi.1009481

## What's in it

- Style and linting (ruff for Python, with a standard rule set; lint codes always named in plain language)
- Function size, avoiding premature abstraction, no dead code
- Defensive programming, type hints, docstrings
- Testing conventions (write tests alongside code, run the full suite before finishing)
- Repository hygiene: README requirements, environment files, `inputs/`/`outputs/`/`plots/` folder layout, `.gitignore` for data, secrets handling
- Scientific-code priorities: minimal dependencies, progress bars and "this may take a while" messages for slow steps, reusable functions, reproducibility, seed handling, config files over hardcoded parameters
- Figures: data not conclusions, sized for half or full A4 width, `quantity / unit` axis labels with exponents (m s$^{-1}$), lettered panels, colourblind-safe ColorBrewer, perceptually uniform and cyclic colour maps (with Fabio Crameri's Scientific colour maps proposed as a strong option), log axes spanning at least a decade. The colour-map guidance draws on:

  > Crameri F (2018) Scientific colour maps. Zenodo. https://doi.org/10.5281/zenodo.1243862
  >
  > Crameri F, Shephard GE, Heron PJ (2020) "The misuse of colour in science communication." *Nat Commun* 11: 5444. https://doi.org/10.1038/s41467-020-19160-7
- Notebook conventions (`nbstripout`, logic lives in `.py` modules, `working.ipynb` treated as a personal scratch pad)
- An equivalent R section (`styler`/`lintr`, `testthat`, `roxygen2`, `renv`)

Where a rule involves creating a new file (a ruff config, a README, an environment file), Claude is instructed to ask before adding it rather than doing so silently.

## Install

Claude Code reads `CLAUDE.md` automatically from a few locations. For a file like this one that you want applied to *every* project, put it at the user level:

```bash
cp CLAUDE.md ~/.claude/CLAUDE.md
```

If you only want it to apply to one repository, put a copy at `<repo>/CLAUDE.md` instead — project-level and user-level files are both loaded and concatenated, so you can keep this as your global baseline and add repo-specific notes (e.g. a particular model architecture or dataset quirk) in a project-level file alongside it.

### Optional: keep it linked to this repo

Copying the file means your copy and this repo drift apart. To keep them in step, clone the repo once and make `~/.claude/CLAUDE.md` a symbolic link to the clone's file (macOS/Linux):

```bash
git clone https://github.com/AlexLipp/claude-md.git ~/claude-md       # or your fork
mv ~/.claude/CLAUDE.md ~/.claude/CLAUDE.md.backup 2>/dev/null         # keep any existing file
ln -s ~/claude-md/CLAUDE.md ~/.claude/CLAUDE.md
ls -l ~/.claude/CLAUDE.md   # should show: CLAUDE.md -> .../claude-md/CLAUDE.md
```

Claude Code reads the linked file exactly as before. Then:

- **Get updates:** `cd ~/claude-md && git pull`.
- **Make changes:** edit `~/.claude/CLAUDE.md` (or the clone's file — it's the same file), then commit and push from `~/claude-md`. To keep your own changes, fork the repo first and clone your fork.
- **Another machine:** repeat the three commands above.

Version only this file, not the whole `~/.claude/` folder: that also holds login tokens, conversation transcripts and local settings, which shouldn't go into git.

## Verify it's loaded

Run `/memory` inside a Claude Code session — it lists every context file that was loaded, including `~/.claude/CLAUDE.md`. If it's not there, check the file is named exactly `CLAUDE.md` (not `claude.md`, `AGENT.md`, etc. — Claude Code only auto-loads that exact filename).

## Other settings worth changing

A few things that pair well with this file but live in Claude Code's settings rather than in `CLAUDE.md` itself:

- **Output style — "Learning" (recommended)**: the best setting if you want to get better at coding while you use Claude, rather than just collecting code. Claude explains its choices as it goes and, at sensible points, leaves small `TODO(human)` markers for you to write yourself instead of doing the whole task end to end. You practise the parts worth learning, and Claude handles the rest. It is slower when you just need working code quickly, so switch styles for those tasks. Try it for one session with `/output-style`, or make it your default everywhere by adding to `~/.claude/settings.json`:
  ```json
  { "outputStyle": "Learning" }
  ```
- **Output style — "Explanatory"**: a lighter alternative. Claude writes all the code itself but adds short "Insight" notes explaining *why* it made each choice. Useful when you want to understand the reasoning without stopping to write code yourself. Also set via `/output-style`, or `"outputStyle": "Explanatory"` in settings.
- **Plan mode**: for anything non-trivial, running Claude in plan mode (`shift+tab` to cycle modes, or `defaultMode: "plan"` in settings) makes it propose an approach before writing code, which is a good habit for checking it understood the task before it starts.

## Further reading

- [claude-code-for-hydrology](https://github.com/lorenliu13/claude-code-for-hydrology) — a tutorial specifically on using Claude Code for hydrology research workflows, worth a look alongside this file if that's your field.
- [Ten simple rules on writing clean and reliable open-source scientific software](https://doi.org/10.1371/journal.pcbi.1009481) — the paper these standards are adapted from.
