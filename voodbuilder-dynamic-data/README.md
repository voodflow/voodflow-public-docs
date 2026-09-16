# voodflow/voodbuilder-dynamic-data

> Public README mirror for Filament / marketing links. Canonical product docs: [docs.voodflow.com](https://docs.voodflow.com). Package source remains private/licensed.

FilamentPHP 5 plugin that adds **Dynamic Data** to [VoodBuilder](https://github.com/voodflow/voodbuilder): Model Integrations, single-record bindings (`.auth` / `.latest`), and Pro/Agency List repeat collections.

HTTP / static / eloquent / callback **API Data Sources** live in the separate companion [`voodflow/voodbuilder-dynamic-api`](https://github.com/voodflow/voodbuilder-dynamic-api).

## Why a separate package?

VoodBuilder Core does **not** include Dynamic Data. Install and register this plugin to unlock it — that is the commercial gate.

| Surface | Gate |
|---|---|
| Make dynamic / `.auth` / `.latest` / Model Integrations admin | Filament plugin registered |
| List repeat / `.item` / package list registry | Plugin **and** `dynamic-data.collections` (Professional+) |

## Install

```bash
composer require voodflow/voodbuilder-dynamic-data
```

```php
->plugins([
    \Voodflow\Voodbuilder\VoodbuilderPlugin::make(),
    \Voodflow\VoodbuilderDynamicData\VoodbuilderDynamicDataPlugin::make(),
])
```

Omitting the plugin disables Dynamic Data completely (no bindings routes, no Make dynamic catalog, no Model Integrations resource), even if Composer still lists the package.

Optional config (`config/voodbuilder-dynamic-data.php`):

- `enabled` — master switch (default `true`)
- `auto_register_module` — register without the Filament plugin (default `false`, Testbench/headless)

## Locales

`resources/lang/{en,it,es,fr,de}/dynamic-data.php` (`voodbuilder-dynamic-data::*`).

Scaffolded from [filamentphp/plugin-skeleton](https://github.com/filamentphp/plugin-skeleton) `5.x`.
