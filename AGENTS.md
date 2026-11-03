# AGENTS.md — Proxmotion WHMCS module

Rules for every AI coding agent working in this repository. `CLAUDE.md` imports it.

## What this is

The WHMCS server module of [Proxmotion](https://github.com/proxmotion-devs/proxmotion)
(AGPL-3.0), a VPS management panel for Proxmox VE. Proxmotion has no client panel: customers
manage their servers only through billing modules like this one. The panel's own rules,
architecture and API live in its `AGENTS.md` and `docs/api/openapi.yaml`.

## Layout

```
modules/servers/proxmotion/   # copied as-is into WHMCS's modules/servers/
  proxmotion.php              # WHMCS entry points (MetaData, CreateAccount, ClientArea, UsageUpdate, …)
  hooks.php                   # AddonActivation / AddonCancelled / AddonTerminated (paid apps)
  webhook.php                 # webhook receiver (bootstraps init.php)
  lib/                        # namespace Proxmotion\Whmcs, loaded by lib/autoload.php
  lang/, templates/           # client area strings (english.php, turkish.php) and overview.tpl
  README.md                   # setup and features for administrators
phpstan/                      # stubs of WHMCS functions and Capsule for PHPStan
tests/                        # PHPUnit: WHMCS stubbed (bootstrap.php), API faked (FakeTransport)
```

## Rules

- **PHP 7.2 syntax and functions** (WHMCS 8.0): no typed properties, arrow functions, `match`,
  `?->`, `??=`, `str_contains`, … `Php72CompatibilityTest` checks this with php-parser, CI
  lints on PHP 7.2; PHPStan's `phpVersion` alone does not catch it.
- No Laravel, no Composer dependencies at run time (Composer is only for the tests).
- The module is a thin client of the panel's API v1: business logic stays in the panel.
  Found by `GET /servers?external_id=<service id>`; writes carry an `Idempotency-Key`.
- Secrets (API token, root passwords, app inputs and outputs, webhook secret) are never stored
  in the WHMCS database beyond what WHMCS itself stores, and are masked in `logModuleCall()`.
- Webhooks: signature compared with `hash_equals`, timestamps more than 300 s off refused,
  event ids kept to drop repeats.
- Client area strings go into both `lang/english.php` and `lang/turkish.php`.
- Code, comments, commit messages and docs in English; keep `modules/servers/proxmotion/README.md`
  current when behaviour changes. Conventional Commits (`feat: …`, `fix: …`).
- Never run anything against a live WHMCS or a real Proxmotion; tests fake both.
- Do not report behaviour as working in a real WHMCS unless it was run there.

## Commands

```bash
composer install
composer ci:check     # PHPStan (level 6, phpVersion 7.2) + PHPUnit
```

A task is done when `composer ci:check` passes.
