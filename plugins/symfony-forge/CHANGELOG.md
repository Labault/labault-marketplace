# Changelog

All notable changes to `symfony-forge` are documented here.
Format follows [Keep a Changelog](https://keepachangelog.com/); versioning is [SemVer](https://semver.org/).

## [0.1.1] : 2026-10-13

### Fixed

- Align `symfony-bootstrap`, `/quality-gate` and the plugin documentation with
  PHPStan level 9 without automatic baselines. Legacy exceptions require explicit
  project approval, documented debt and a removal plan. Preview existing-project
  adoption and report the actual gate result.

## [0.1.0] — 2026-06-28

### Added

- Skill `symfony-bootstrap` — apply and sync a Symfony quality/CI/security baseline via the `bootstrap` CLI.
- Agent `symfony-reviewer` — Symfony-aware code review focused on logic, security, idioms and edge cases.
- Command `/quality-gate` — run PHPStan + PHP-CS-Fixer + Rector and report commit-readiness.
