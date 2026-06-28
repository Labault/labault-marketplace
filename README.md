# labault marketplace

**Claude Code plugins for people who write PHP on purpose.**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-plugin%20marketplace-7c5cff.svg)](https://code.claude.com/docs/en/plugins)

Most "awesome Claude" repos ship a few hundred skills generated in an afternoon.
This is the opposite: a small, slow-grown set of plugins extracted from real
production Symfony work. Every skill here is one I actually run — if it's in the
repo, it earned its place. Quantity is not the flex.

## Add it

```sh
# inside Claude Code
/plugin marketplace add Labault/labault-marketplace
/plugin install symfony-forge@labault
```

Skills, agents and commands from an installed plugin are namespaced under the
plugin name (e.g. `/symfony-forge:quality-gate`), so nothing collides with your
own setup.

## What's inside

### `symfony-forge`

Scaffold and harden Symfony projects the way I do it, without the copy-paste
config drift.

- **Skill — `symfony-bootstrap`** · drops a consistent quality / CI / security
  baseline into a project via the [`bootstrap`](https://github.com/Labault/bootstrap-web-setup)
  CLI: pre-commit hooks, PHPStan level 9, Rector, PHP-CS-Fixer, gitleaks, GitHub
  Actions. Knows the one rule that trips everyone up (it writes config, never
  installs binaries) and the workflow to keep it in sync.
- **Agent — `symfony-reviewer`** · a Symfony-aware reviewer that ignores what
  PHPStan already catches and goes after logic, security, idioms and the edge
  cases you skipped. Short, honest reviews — no manufactured findings.
- **Command — `/quality-gate`** · runs PHPStan + PHP-CS-Fixer + Rector and tells
  you, in one line, whether you can commit — and if not, exactly what to touch.

More plugins will land here as the workflows prove themselves. No roadmap theater.

## Project vs. global vs. this

A fair question when you start collecting Claude config — there are three homes,
and they're not interchangeable:

| Where | Path | Scope | Travels with |
| --- | --- | --- | --- |
| **Project** | `.claude/` in a repo | that one repo | the repo (commit it) |
| **Global** | `~/.claude/` | every project on *your* machine | nothing — it's yours alone |
| **Plugin** (this) | a marketplace | anyone who installs it | the marketplace |

Rule of thumb: repo-specific setup → `.claude/`. Personal habits you want
everywhere → `~/.claude/`. Anything you'd want to *share or reuse across machines*
→ package it as a plugin and put it here. Global config never leaves your laptop;
a plugin is how a "global-feeling" workflow becomes installable.

## Contributing

These are personal, opinionated tools. Issues and ideas welcome; just know the bar
is "do I run this in anger", not "is it theoretically useful".

## License

MIT — see [LICENSE](LICENSE).

---

<sub>Maintained by [Thibault (Labault)](https://github.com/Labault) · made on days
that are not Friday · one duck of approval 🦆</sub>
