# voodflow/voodbuilder-components

> Public README mirror for Filament / marketing links. Canonical product docs: [docs.voodflow.com](https://docs.voodflow.com). Package source remains private/licensed.

FilamentPHP 5 companion that extends [VoodBuilder](https://github.com/voodflow/voodbuilder) with **Components** (saved reusable sections + global CSS classes).

> Not to be confused with `voodflow/voodbuilder-elements` (official remote section catalog).

## Install

```bash
# Private VCS (same pattern as Elements / Dynamic Data)
composer config repositories.voodbuilder-components vcs git@voodflow-git:voodflow/voodbuilder-components.git
composer require voodflow/voodbuilder-components
```

Register the Filament plugin next to VoodBuilder (required — Composer alone does **not** enable the module):

```php
->plugins([
    \Voodflow\Voodbuilder\VoodbuilderPlugin::make(),
    \Voodflow\VoodbuilderComponents\VoodbuilderComponentsPlugin::make(),
])
```

Omitting `VoodbuilderComponentsPlugin` from the panel keeps the editor Components tab on the soft upsell, even while the package remains installed.

### Migrate & config

```bash
php artisan migrate
php artisan vendor:publish --tag=voodbuilder-components-config
```

Migrations create `voodbuilder_components` and `voodbuilder_global_classes` (and related columns). The package uses `runsMigrations()` so `migrate` picks them up automatically after install.

Optional config (`config/voodbuilder-components.php`):

| Key | Env | Default | Role |
|-----|-----|---------|------|
| `enabled` | `COMPONENTS_ENABLED` | `true` | Master switch |
| `auto_register_module` | `COMPONENTS_AUTO_REGISTER` | `false` | Register without Filament plugin (Testbench/headless only) |

Also gated by `config('voodbuilder.modules.components.enabled')` when present.

```env
COMPONENTS_ENABLED=true
```

## What you get

- Editor **Components** library (save / reuse / import / export) — replaces the Core upsell card
- Models: `BuilderComponent`, `BuilderGlobalClass`
- Authenticated editor APIs via `ComponentsModule`
- Global classes + component CSS applied on published pages through Core `ComponentRuntimeBridge`

## What moves here from core

- `ComponentsModule` (GrapesJS components + global-classes API routes)
- Controllers: `GrapesJsComponentsController`, `GrapesJsGlobalClassesController`
- Component library support classes (import/export, CSS sync, public render helpers)
- Database migrations for `voodbuilder_components` and `voodbuilder_global_classes`

Scaffolded from [filamentphp/plugin-skeleton](https://github.com/filamentphp/plugin-skeleton) `5.x`.

## Authorization

- **Without Filament Shield:** any Filament panel user who can open the page builder may use Components (save, import, export).
- **With Filament Shield:** page-builder access still applies; optional named abilities `voodbuilder.components.import` / `export` / `code-import` are honoured **only if** those permissions exist in Spatie. Missing abilities do not block.
- Edition matrix (`components.export` Agency, etc.) is a soft upsell when the companion is **not** installed — it does **not** block API once this plugin is registered.
