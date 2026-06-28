---
name: symfony-bootstrap
description: >-
  Drop a consistent quality, CI and security baseline into a Symfony (or any
  PHP/web) project using Labault's `bootstrap` CLI — pre-commit hooks, PHPStan
  level 9, Rector, PHP-CS-Fixer, gitleaks, GitHub Actions. Use this whenever the
  user starts a new Symfony repo, says a project "has no CI / no static analysis
  / no hooks", asks to standardize tooling across repos, mentions PHPStan,
  Rector, PHP-CS-Fixer, pre-commit, gitleaks, or `.bootstrap.yaml`, or asks to
  "set up the project properly" — even if they don't name the tool. Prefer this
  over hand-writing config files one by one.
---

# Symfony bootstrap (Labault baseline)

## Why this exists

Copy-pasting `phpstan.neon`, `.php-cs-fixer.dist.php`, `rector.php`,
`.pre-commit-config.yaml` and a stack of GitHub Actions from the last repo is how
every project ends up with a *slightly different, slowly drifting* baseline. The
`bootstrap` CLI deposits one standardized layer and then keeps it in sync, so the
quality bar is identical everywhere and updates are a single command — not an
archaeology session.

This skill teaches you to apply and maintain that baseline correctly. It does not
reinvent the config; it drives the tool that owns it.

## The one rule that explains everything

**`bootstrap` writes config files only. It never installs binaries.** The tools
that read those files (PHP, PHPStan, Rector, pre-commit, gitleaks, jq…) come from
the developer's machine — provisioned separately by
[`mac-dev-setup`](https://github.com/Labault/mac-dev-setup). So:

- If a deposited tool fails to run, the fix is *install the binary*, never *edit
  the config bootstrap produced*.
- `bootstrap doctor` is the source of truth for "what's missing on this machine".

Internalize this before touching anything — most confusion comes from expecting
`bootstrap` to behave like a project generator. It is a config layer.

## Prerequisites

- **bash 4+** — macOS ships 3.2; without it the CLI misbehaves. Check with
  `bash --version`; install via `brew install bash` if needed.
- **git** and **jq** — used by `apply` and `reconcile`.
- The per-profile binaries (see table). Run `bootstrap doctor` to confirm.

If `bootstrap` is not installed yet:

```sh
git clone https://github.com/Labault/bootstrap-web-setup.git
cd bootstrap-web-setup && ./install.sh
```

## Profiles

The profile decides **which files are deposited** and **which binaries are
required**. Profiles inherit, so each one is its parent plus more:

| Profile     | For                         | Adds on top of its parent |
| ----------- | --------------------------- | ------------------------- |
| `minimal`   | Any web repo (any language) | pre-commit, EditorConfig, commit-msg lint, gitleaks, shellcheck, markdownlint, actionlint, lychee, base CI + security workflows, Dependabot, transverse files |
| `symfony`   | PHP / Symfony               | PHPStan (level 9 + auto baseline), PHP-CS-Fixer (`@Symfony`), Rector, hadolint, PHP CI, PHP `make` targets |
| `fullstack` | Symfony + JS/TS front       | ESLint, Prettier, Husky + lint-staged, front CI |

**Auto-detection:** a `composer.json` selects `symfony`; an additional
`package.json` selects `fullstack`; otherwise `minimal`. Only pass `--profile`
to override the detection — don't pass it "to be safe", let detection do its job.

## Workflow

### 1. Always dry-run first

Every mutating command supports `--dry-run`, and anything it would overwrite is
backed up first. Preview before you write:

```sh
cd /path/to/project
bootstrap apply --dry-run
```

Read the summary back to the user: which files are created, which use a merge
strategy (e.g. `.gitignore`, `.vscode/extensions.json`), and that a
`.bootstrap.yaml` state file will be written. Then apply for real:

```sh
bootstrap apply            # auto-detects the profile; add --profile to force one
```

`apply` records `.bootstrap.yaml` (profile, version, files + hashes) and installs
the git hooks. Commit `.bootstrap.yaml` — it is the project's record of what was
deposited and at which version.

### 2. Run the quality gate

The `symfony` profile ships `make` targets. After applying, run the gate and
report results rather than assuming green:

```sh
make qa        # the full bar: PHPStan, PHP-CS-Fixer, Rector
make fix       # auto-fixable formatting (PHP-CS-Fixer, Rector)
make hooks     # (re)install pre-commit hooks
```

PHPStan runs at **level 9 with an auto-generated baseline** — existing violations
are baselined so the gate is green on day one, and *new* code is held to level 9.
Do not lower the level to make it pass; either fix the issue or, for legacy code
that genuinely can't move yet, let it sit in the baseline and note it.

### 3. Keep it in sync (existing repos)

For a repo that already has the baseline, don't blindly re-apply. Diagnose first:

```sh
bootstrap doctor       # missing binaries + configuration drift vs the templates
bootstrap reconcile    # 3-way merge of template updates, keeping local edits
```

`reconcile` is the safe path when the templates have moved on but the project has
local tweaks — it merges rather than clobbers. Use `--dry-run` here too.

## What the baseline actually enforces

So you can explain it when asked: EditorConfig; a local-mode
`.pre-commit-config.yaml` (editorconfig-checker, gitleaks, shellcheck,
markdownlint, actionlint, commit-msg lint); on the PHP profiles PHP-CS-Fixer
(`@Symfony` ruleset), PHPStan level 9, Rector; GitHub Actions (`ci.yml` lint +
links, `security.yml` gitleaks + dependency review, `php.yml` on PHP profiles);
`.gitleaks.toml`; `.github/dependabot.yml`; a `Makefile`, `SECURITY.md`,
`CONTRIBUTING.md`, a `CLAUDE.md`, PR/issue templates, and the `.bootstrap.yaml`
state file.

## Gotchas

- **macOS bash 3.2** is the most common failure — symptoms look random. Check the
  bash version first when the CLI acts up.
- **"Tool not found" is a machine problem, not a config problem.** Run
  `bootstrap doctor`, install the missing binary, retry. Never patch the deposited
  config to route around a missing tool.
- **Don't fight the baseline.** A failing PHPStan check on new code is the system
  working as designed. Fix the code; don't drop the level or stuff the baseline.
- **Commit `.bootstrap.yaml`.** Without it, `doctor` and `reconcile` can't tell
  what was deposited or detect drift.

## Example

> **User:** I just ran `symfony new` on a fresh app, can you set up CI and static
> analysis like my other projects?
>
> **You:** Confirm `bootstrap` is installed (`bootstrap --version`); if not, clone
> and run `install.sh`. Then `cd` into the new app and run
> `bootstrap apply --dry-run` — detection will pick the `symfony` profile from
> `composer.json`. Summarize the files it will create, apply for real, run
> `make qa`, and report the gate result. Commit the deposited config plus
> `.bootstrap.yaml`.
