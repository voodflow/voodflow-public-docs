# voodflow/voodbuilder-dynamic-api

> Public README mirror for Filament / marketing links. Canonical product docs: [docs.voodflow.com](https://docs.voodflow.com). Package source remains private/licensed.

FilamentPHP 5 **paid companion** for [VoodBuilder](https://github.com/voodflow/voodbuilder): **API Data Sources** (HTTP / static / eloquent / callback) bound into page fields and List repeat.

Without this package (or without registering the Filament plugin), Core and Dynamic Data have **no** API admin, outbound fetch, or `{slug}.remote.*` registration.

## Requirements

| Dependency | Role |
|---|---|
| `voodflow/voodbuilder` | Core page builder + binding renderer |
| `voodflow/voodbuilder-dynamic-data` (recommended) | Bindings catalog UI + List repeat (`{slug}.list` / `{slug}.item.*`) |

Published `{slug}.remote.*` values still resolve if sources are registered even without Dynamic Data; authoring List repeat needs Dynamic Data + `dynamic-data.collections`.

## Install

```bash
composer require voodflow/voodbuilder-dynamic-api
php artisan migrate
```

```php
->plugins([
    \Voodflow\Voodbuilder\VoodbuilderPlugin::make(),
    \Voodflow\VoodbuilderDynamicData\VoodbuilderDynamicDataPlugin::make(), // optional but usual
    \Voodflow\VoodbuilderDynamicApi\VoodbuilderDynamicApiPlugin::make(),
])
```

Admin: `/admin/voodbuilder/api-data-sources`

## Bindings

| Binding | Meaning |
|---|---|
| `{slug}.remote.*` | First/primary row of the source (page-level) |
| `{slug}.list` | List repeat catalog id |
| `{slug}.item.*` | Current List row (Core rewrites `.remote.` → `.item.` inside repeats) |

## Drivers

- **HTTP** — SSRF-hardened `HttpUrlGuard`, no redirects, absolute `http(s)` only by default, `{{token}}` in URL/headers
- **Static** — JSON rows in admin
- **Eloquent** — via Model Integration id
- **Callback** — `VoodbuilderDynamicApi::registerCallback('key', …)` or `config('voodbuilder-dynamic-api.callbacks')`

## Config

Publish: `php artisan vendor:publish --tag=voodbuilder-dynamic-api-config`

| Key | Default | Notes |
|---|---|---|
| `enabled` | `true` | Master switch |
| `auto_register_module` | `false` | Testbench / headless |
| `max_limit` | `25` | Cap rows per resolve |
| `http.block_ssrf` | `true` | Reject private/metadata hosts |
| `http.allow_relative_urls` | `false` | Keep off in production |
| `http.timeout` / `connect_timeout` | `10` / `5` | Seconds |

## Security

See the `tests/Security` suite (SSRF, relative URLs, redirect refusal, header tokens). Public docs: [docs.voodflow.com](https://docs.voodflow.com).

## Locales

Mirrored onto `voodbuilder::api_data_sources.*` at boot (`resources/lang/{en,it}/api_data_sources.php`).
