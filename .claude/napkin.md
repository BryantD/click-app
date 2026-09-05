# Napkin Runbook

## Curation Rules
- Re-prioritize on every read.
- Keep recurring, high-value notes only.
- Max 10 items per category.
- Each item includes date + "Do instead".

## Repo Shape & Git Topology (Highest Priority)
1. **[2026-09-05] This is a FORK of simonw/click-app, with two remotes**
   `origin` = BryantD/click-app (yours), `upstream` = simonw/click-app.
   Do instead: before any merge/pull, run `git log --oneline origin/main..main`
   AND `main..origin/main` to see which side owns which commits. "Ahead N" here
   usually means upstream commits you pulled, not your own work.
2. **[2026-09-05] `git merge upstream/main` on a stale clone silently fast-forwards past your fork's work**
   A 2022-era local clone fast-forwarded straight to upstream's 2025 head,
   bypassing 11 fork commits made via the GitHub web UI. Reflog showed only
   `clone` + `merge ... Fast-forward`, so nothing looked wrong.
   Do instead: `git fetch --all` and confirm `git status` says up to date with
   origin/main BEFORE merging upstream. If behind origin, pull that first.
3. **[2026-09-05] `origin/merger` is a backup ref, not a stale branch**
   It pins f011f01, the pre-reset fork tip (black/flake8/isort era). Local tag
   `backup/fork-main-pre-reset` points at the same commit.
   Do instead: leave both alone; they are the only copy of the old fork history.

## Toolchain Gotchas
1. **[2026-09-05] Homebrew `cookiecutter` is broken (ModuleNotFoundError: certifi)**
   Do instead: always `uvx cookiecutter --no-input <path> key=value ...`.
   Never the `/opt/homebrew/bin/cookiecutter` on PATH.
2. **[2026-09-05] ruff-pre-commit hook id is `ruff-check`, not `ruff`**
   Bare `ruff` still resolves but is a documented legacy alias.
   Do instead: use `ruff-check` + `ruff-format`; verify ids against
   `https://raw.githubusercontent.com/astral-sh/ruff-pre-commit/<tag>/.pre-commit-hooks.yaml`.
3. **[2026-09-05] macOS `cat -A` does not exist**
   Do instead: `od -c` to inspect trailing bytes/whitespace.
4. **[2026-09-05] Template dirs contain `{{...}}`, which bash brace-expands**
   Do instead: single-quote every path, e.g. `'{{cookiecutter.hyphenated}}/README.md'`.

## Validating Template Changes
1. **[2026-09-05] Never trust a template edit without rendering it**
   Do instead: `uvx cookiecutter --no-input . hyphenated=demo-tool underscored=demo_tool
   description=Demo author_name=Test github_username=testuser`, then in the output
   `git init -q . && git add -A && pre-commit run --all-files` and confirm
   `git diff --exit-code` is clean. A generated project must pass its own hooks
   on the FIRST run.
2. **[2026-09-05] Test the generated project with uv, not bare python**
   Do instead: `uv run --with '.[test]' --with pytest python -m pytest -q`.

## Repo Facts
1. **[2026-09-05] The template repo itself is unversioned**
   No root `pyproject.toml`/`setup.py`; `version = "0.1"` lives only in the
   generated project's pyproject as a placeholder.
   Do instead: skip the CLAUDE.md version-bump step for commits here.
2. **[2026-09-05] Known upstream wart: generated test uses deprecated `isolated_filesystem`**
   Emits a DeprecationWarning (removed in Click 9.0). Upstream's, not ours.
   Do instead: leave it unless explicitly asked; fix belongs upstream.

## User Directives
1. **[2026-09-05] Lint/format stack is ruff, not black/flake8/isort**
   Do instead: any new lint config in this repo uses ruff via pre-commit.
