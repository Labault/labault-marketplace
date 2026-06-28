# symfony-forge

**A consistent Symfony baseline, enforced — not copy-pasted.**

Part of the [labault marketplace](../../README.md). This plugin teaches Claude
Code to set up and maintain Symfony projects to a fixed production standard, and
gives it a reviewer that thinks like someone who's shipped PHP for a living.

## Install

```sh
/plugin marketplace add Labault/labault-marketplace
/plugin install symfony-forge@labault
```

## Components

| Type | Name | What it does |
| --- | --- | --- |
| Skill | `symfony-bootstrap` | Deposits and syncs a quality/CI/security baseline via the `bootstrap` CLI (PHPStan L9, Rector, PHP-CS-Fixer, pre-commit, gitleaks, Actions). |
| Agent | `symfony-reviewer` | Reviews PHP/Symfony changes for logic, security, idioms and edge cases — the things static analysis misses. |
| Command | `/quality-gate` | Runs the full gate and reports, in one line, whether the change is commit-ready. |

Once installed, these are namespaced: `/symfony-forge:quality-gate`, and the
agent is available to the `Task` tool as `symfony-reviewer`.

## How they fit together

```text
symfony new my-app
   │
   ├─ symfony-bootstrap  →  bootstrap apply   (baseline + hooks + CI)
   │                        make qa           (gate is green on day one)
   │
   ├─ …build the feature…
   │
   ├─ /quality-gate      →  fast "can I commit?" check
   └─ symfony-reviewer   →  deeper pass before the PR
```

## Requirements

The skill drives the [`bootstrap`](https://github.com/Labault/bootstrap-web-setup)
CLI, which **writes config only — it never installs binaries**. The tools it
configures (PHP, PHPStan, Rector, PHP-CS-Fixer, pre-commit, gitleaks, jq) must
exist on the machine; provision them with
[`mac-dev-setup`](https://github.com/Labault/mac-dev-setup). When something can't
run, the fix is installing the binary, not editing the deposited config.

## License

MIT.
