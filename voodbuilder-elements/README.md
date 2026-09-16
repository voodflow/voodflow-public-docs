# voodflow/voodbuilder-elements

> Public README mirror for Filament / marketing links. Canonical product docs: [docs.voodflow.com](https://docs.voodflow.com). Package source remains private/licensed.

FilamentPHP 5 companion that extends [VoodBuilder](https://github.com/voodflow/voodbuilder) with the **Elements** library (premium section templates).

> Not to be confused with `voodflow/voodbuilder-components` (user/team saved reusable pieces).

## Install

```bash
composer require voodflow/voodbuilder-elements
```

Register next to Core:

```php
->plugins([
    \Voodflow\Voodbuilder\VoodbuilderPlugin::make(),
    \Voodflow\VoodbuilderElements\VoodbuilderElementsPlugin::make(),
])
```

Omitting the plugin leaves Core Community blocks only.

## Remote catalog only

This package **does not** ship HTML templates. The library is loaded exclusively from:

`https://api.voodflow.com/voodbuilder/elements/` (plus `voodflow/` and `templates/` bundles)

(and `catalog.bundle.json` when available).

The CDN tree is **token-gated** (`X-VoodBuilder-Catalog-Token`). Only your Laravel app should know the secret — the browser never receives catalog origin URLs or the token. Source UI talks to same-origin `/voodbuilder/editor/elements/*`.

Author and deploy the static tree from the sibling folder:

`packages/voodflow/voodbuilder-elements-catalog/` → rsync to `api.voodflow.com/public/voodbuilder/{elements,voodflow,templates}/`.

```bash
VOODBUILDER_CATALOG_TOKEN=… ./packages/voodflow/voodbuilder-elements-catalog/deploy.sh
# or: ./deploy.sh templates
```

### Config

| Key | Default | Role |
|-----|---------|------|
| `catalog_url` | `https://api.voodflow.com/voodbuilder/elements/index.json` | Remote index |
| `catalog_token` | — | Shared secret for CDN (`VOODBUILDER_CATALOG_TOKEN` / `VOODBUILDER_ELEMENTS_CATALOG_TOKEN`) |
| `cache_ttl` | `3600` | Cache for remote index/bundle |
| `register_sidebar_blocks` | `false` | Also inject into classic BlockManager categories |
| `require_full_library_capability` | `false` | Gate on `blocks.official.complete` |

```env
VOODBUILDER_ELEMENTS_ENABLED=true
VOODBUILDER_ELEMENTS_CATALOG_URL=https://api.voodflow.com/voodbuilder/elements/index.json
VOODBUILDER_ELEMENTS_CATALOG_TEMPLATES_URL=https://api.voodflow.com/voodbuilder/templates/index.json
VOODBUILDER_CATALOG_TOKEN=your-shared-secret
VOODBUILDER_ELEMENTS_CACHE_TTL=300
```

## SOURCE UI

The editor mounts a Bricks-inspired **SOURCE** panel (category + search + card previews + import).  
API: `GET /voodbuilder/editor/elements/source` (auth) — proxies the remote catalog server-side and returns only same-origin preview routes (no `api.voodflow.com` URLs).

## JS tiles

Tabs extras, forms, code, animated counter, logo cloud BlockManager tiles are registered by this companion.  
GrapesJS component **types** stay in Core so existing pages keep editing.

Scaffolded from [filamentphp/plugin-skeleton](https://github.com/filamentphp/plugin-skeleton) `5.x`.
