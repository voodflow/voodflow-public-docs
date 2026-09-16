# voodflow/voodbuilder-templates

> Public README mirror for Filament / marketing links. Canonical product docs: [docs.voodflow.com](https://docs.voodflow.com). Package source remains private/licensed.

FilamentPHP 5 plugin that unlocks **page template authoring** for [VoodBuilder](https://github.com/voodflow/voodbuilder).

## Commercial boundary

| Capability | Core (no plugin) | With this plugin |
|---|:---:|:---:|
| List / apply local templates | Yes | Yes |
| Install from marketplace URL | Yes | Yes |
| Save page as template | No | Yes |
| Import JSON files | No | Yes |
| Export JSON | No | Yes |
| Multi-select manage | No | Yes |

Marketplace purchases must install without a second paid product. Authoring is the paid surface.

## Install

```bash
composer require voodflow/voodbuilder-templates
```

Register the Filament plugin next to VoodBuilder:

```php
->plugins([
    \Voodflow\Voodbuilder\VoodbuilderPlugin::make(),
    \Voodflow\VoodbuilderTemplates\VoodbuilderTemplatesPlugin::make(),
])
```

Omitting `VoodbuilderTemplatesPlugin` from the panel leaves Core consume-only (list + install-from-URL), even while the Composer package remains installed.

Optional config (`config/voodbuilder-templates.php`):

- `enabled` — master switch (default `true`)
- `auto_register_module` — activate authoring without the Filament plugin (default `false`, for Testbench/headless)

Scaffolded from [filamentphp/plugin-skeleton](https://github.com/filamentphp/plugin-skeleton) `5.x`.
