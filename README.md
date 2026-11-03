# Proxmotion module for WHMCS

The WHMCS server module of [Proxmotion](https://github.com/proxmotion-devs/proxmotion), the
open-source VPS management panel for Proxmox VE. WHMCS orders, suspends and terminates VPS
on Proxmotion, and customers manage their server in the WHMCS client area. The module is a
thin client of Proxmotion's billing API v1; Proxmotion does the work.

- **WHMCS 8 and 9**, PHP 7.2 or newer, no Composer dependencies at run time.
- Installation, products, configurable options, webhooks and every client area feature:
  [the module's README](modules/servers/proxmotion/README.md).
- The API it uses: [`docs/api/openapi.yaml`](https://github.com/proxmotion-devs/proxmotion/blob/main/docs/api/openapi.yaml)
  in the Proxmotion repository.

> **Not run in a real WHMCS yet.** The module is tested with WHMCS and the Proxmotion API
> faked. Please report what does not work.

## Installation

Copy `modules/servers/proxmotion` to `modules/servers/` of your WHMCS, then follow
[Installation](modules/servers/proxmotion/README.md#installation).

## Development

```bash
composer install
composer ci:check     # PHPStan + PHPUnit, like CI
```

The tests (`tests/`) load the module outside WHMCS: `tests/bootstrap.php` stubs the WHMCS
functions it calls and `FakeTransport` fakes the Proxmotion API. `Php72CompatibilityTest`
checks that the module uses no syntax or function newer than PHP 7.2, and CI lints it on
PHP 7.2.

A change to Proxmotion's API v1 that the module uses is made in the Proxmotion repository
first; the matching module change goes here.

## Licence

[AGPL-3.0](LICENSE), like Proxmotion. Security issues: see
[SECURITY.md](https://github.com/proxmotion-devs/proxmotion/blob/main/SECURITY.md) of the
Proxmotion repository.
