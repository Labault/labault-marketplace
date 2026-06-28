---
description: Run the full quality gate (PHPStan level 9, PHP-CS-Fixer, Rector) and report what fails, with the fix for each.
argument-hint: "[optional path to check, defaults to the whole project]"
allowed-tools: Bash(make:*), Bash(composer:*), Bash(vendor/bin/phpstan:*), Bash(vendor/bin/php-cs-fixer:*), Bash(vendor/bin/rector:*), Read, Grep
---

Run the project's quality gate and report the result. Target: $ARGUMENTS (if
empty, check the whole project).

Prefer the project's `make` targets when present, since they encode the agreed
configuration:

- If a `Makefile` with a `qa` target exists, run `make qa`.
- Otherwise run the tools directly: `vendor/bin/phpstan analyse`,
  `vendor/bin/php-cs-fixer fix --dry-run --diff`, and
  `vendor/bin/rector process --dry-run`.

Then:

1. Summarize in one line whether the gate passes.
2. For each failure, give: the file and line, what rule it breaks, and the
   concrete fix — or note that `make fix` / `php-cs-fixer fix` will auto-resolve
   it.
3. PHPStan runs at level 9 with a baseline. Do **not** suggest lowering the level
   or adding to the baseline to make it pass — fix the new code. Only mention the
   baseline if a failure is genuinely pre-existing legacy.

Keep it tight: the developer wants to know "can I commit?" and, if not, exactly
what to touch.
