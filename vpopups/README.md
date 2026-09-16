# voodflow/vpopups

> Public README mirror for Filament / marketing links. Canonical product docs: [docs.voodflow.com](https://docs.voodflow.com). Package source remains private/licensed.

FilamentPHP 5 companion for popup campaigns: admin CRUD, optional page-editor manager, public runtime and analytics.

Works **standalone**. With `voodflow/voodbuilder` installed, the Popups resource nests under the VoodBuilder navigation group by default and the visual editor + toolbar become available.

## Install

```bash
composer require voodflow/vpopups
```

```php
->plugins([
    \Voodflow\Vpopups\VpopupsPlugin::make(),
])
```

```bash
php artisan vendor:publish --tag=vpopups-config
php artisan vendor:publish --tag=vpopups-migrations
php artisan migrate
```

### Authorization

| Setup | Filament CRUD | Editor toolbar |
|-------|---------------|----------------|
| No Shield | Panel users | Panel users |
| Shield | `ViewAny:BuilderPopup`, … | `vpopups.builder` |

Assign **both** when using Shield.

## Configuration

`config/vpopups.php` (`VPOPUPS_*`):

| Key | Purpose |
|-----|---------|
| `enabled` | Master switch |
| `navigation.group` | Empty → auto **VoodBuilder** when present; else `VoodPopups` |
| `routes.*` | HTTP prefix / names |
| `integrations.voodbuilder.legacy_routes` | Keep `/voodbuilder/popups/*` aliases |
| `authorization.driver` | `auto` / `permissions` / `panel` |
| `authorization.builder_ability` | Default `vpopups.builder` |
| `auto_register` | Activate without Filament plugin |
| `tables.*` | `voodbuilder_popups`, … |

## Docs

Public documentation: [docs.voodflow.com](https://docs.voodflow.com)

## License

Proprietary commercial software. See [LICENSE](https://docs.voodflow.com).
