# voodflow/voodbuilder-analytics

> Public README mirror for Filament / marketing links. Canonical product docs: [docs.voodflow.com](https://docs.voodflow.com). Package source remains private/licensed.

FilamentPHP 5 plugin that extends [VoodBuilder](https://github.com/voodflow/voodbuilder) with VoodBuilder Analytics.

## Install

```bash
composer require voodflow/voodbuilder-analytics
```

Register the Filament plugin next to VoodBuilder:

```php
->plugins([
    \Voodflow\Voodbuilder\VoodbuilderPlugin::make(),
    \Voodflow\VoodbuilderAnalytics\VoodbuilderAnalyticsPlugin::make(),
])
```

Omitting `VoodbuilderAnalyticsPlugin` from the panel disables VoodBuilder Analytics admin and runtime features, even while the Composer package remains installed.

When enabled, Filament shows an **Analytics** item under the **Voodbuilder** navigation group (dashboard scaffold). Tracking IDs stay in Core **Settings → Analytics**.

Public tracking must wait for analytics consent via the `vcookiebar:consent` event from optional [`voodflow/vcookiebar`](https://github.com/voodflow/vcookiebar) — do not couple removed third-party cookie packages.

Optional config (`config/voodbuilder-analytics.php`):

- `enabled` — master switch (default `true`)
- `auto_register_module` — register the module without the Filament plugin (default `false`, for Testbench/headless)

Public documentation: [docs.voodflow.com](https://docs.voodflow.com).

Scaffolded from [filamentphp/plugin-skeleton](https://github.com/filamentphp/plugin-skeleton) `5.x`.
