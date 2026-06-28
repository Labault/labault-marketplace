# labault marketplace

**Claude Code plugins for people who write PHP on purpose.**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-plugin%20marketplace-7c5cff.svg)](https://code.claude.com/docs/en/plugins)

A small set of Claude Code plugins for Symfony developers. You install them once,
and from then on Claude Code already knows your stack and your conventions instead
of guessing them every session.

The part that trips people up: **you don't run these plugins. Claude does**, while
you work. You install a plugin, then keep coding as usual; the right piece kicks in
on its own when the task calls for it.

## Why

Every Symfony project drifts. You copy `phpstan.neon`, `rector.php`,
`.php-cs-fixer.dist.php` and a pile of GitHub Actions from the last repo, tweak them
a little, and six months later no two projects share the same baseline. On top of
that, every new Claude session starts from zero: you re-explain your tooling, your
standards, your conventions, again.

This marketplace removes both. The plugins carry the conventions, so Claude *applies*
them instead of asking, and your quality bar is identical everywhere by construction.

And the positioning, plainly: most "awesome Claude" repos ship a few hundred skills
generated in an afternoon. This is the opposite. A small, slow-grown set pulled from
real production work. If it's in the repo, I actually run it. Quantity is not the flex.

## Who it's for

You, most likely, if you write Symfony with a strict quality setup (PHPStan, Rector,
PHP-CS-Fixer, pre-commit hooks) and live in Claude Code day to day.

Less so if your stack looks different. These tools are personal and opinionated: they
encode *my* conventions, not a neutral standard. That's the point, and also the limit.
This isn't a universal must-have, it's a sharp tool for a specific way of working.

## Install

```sh
# inside Claude Code
/plugin marketplace add Labault/labault-marketplace
/plugin install symfony-forge@labault
```

Everything an installed plugin adds is namespaced under the plugin name
(`/symfony-forge:quality-gate`), so nothing collides with your own setup.

## What using it looks like

Three real moments, so you can picture it rather than infer it from a feature list:

### Setting up a fresh project

```text
You      ▸ Just ran `symfony new`. Set this project up like my others.
Claude   ▸ Recognizes a bootstrap task. Runs `bootstrap apply --dry-run`, picks the
           symfony profile from composer.json, and previews the baseline it will add:
           pre-commit hooks, PHPStan level 9, Rector, PHP-CS-Fixer, gitleaks, CI.
         ▸ Applies it, runs `make qa`, reports the gate is green. Done.
```

### Checking whether you can commit

```text
You      ▸ /symfony-forge:quality-gate
Claude   ▸ Runs PHPStan + PHP-CS-Fixer + Rector. "Green except one PHPStan error in
           OrderService.php:42 — a nullable repository result used without a guard.
           Fix that and you're good."
```

### A real review before the PR

```text
You      ▸ Feature's done, review it before I open the PR.
Claude   ▸ (symfony-reviewer) Flags an N+1 in the loop, a missing CSRF check on the
           delete action, and an unhandled empty-cart case. Skips the type hints —
           PHPStan already owns those.
```

## What's inside

### `symfony-forge`

Scaffold and harden Symfony projects the way I do it, without the copy-paste config
drift.

- **Skill — `symfony-bootstrap`** · drops a consistent quality / CI / security
  baseline into a project via the [`bootstrap`](https://github.com/Labault/bootstrap-web-setup)
  CLI: pre-commit hooks, PHPStan level 9, Rector, PHP-CS-Fixer, gitleaks, GitHub
  Actions. Knows the one rule that trips everyone up (it writes config, it never
  installs binaries) and the workflow to keep that baseline in sync.
- **Agent — `symfony-reviewer`** · a Symfony-aware reviewer that ignores what PHPStan
  already catches and goes after logic, security, idioms and the edge cases you
  skipped. Short, honest reviews, no manufactured findings.
- **Command — `/quality-gate`** · runs PHPStan + PHP-CS-Fixer + Rector and tells you,
  in one line, whether you can commit, and if not, exactly what to touch.

More plugins will land here as the workflows prove themselves. No roadmap theater.

## Project vs. global vs. this

A fair question once you start collecting Claude config: there are three homes for it,
and they are not interchangeable.

| Where | Path | Scope | Travels with |
| --- | --- | --- | --- |
| **Project** | `.claude/` in a repo | that one repo | the repo (commit it) |
| **Global** | `~/.claude/` | every project on *your* machine | nothing, it's yours alone |
| **Plugin** (this) | a marketplace | anyone who installs it | the marketplace |

Rule of thumb: repo-specific setup goes in `.claude/`. Personal habits you want
everywhere go in `~/.claude/`. Anything you'd want to share or reuse across machines,
package as a plugin and put it here. Global config never leaves your laptop; a plugin
is how a "global-feeling" workflow becomes installable.

## Contributing

These are personal, opinionated tools. Issues and ideas welcome; just know the bar is
"do I run this in anger", not "is it theoretically useful".

## License

MIT, see [LICENSE](LICENSE).

---

<sub>Maintained by [Thibault (Labault)](https://github.com/Labault) · made on days
that are not Friday · one duck of approval 🦆</sub>
