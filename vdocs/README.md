# VoodDocs (`voodflow/vdocs`)

> Public README mirror for Filament / marketing links. Canonical product docs: [docs.voodflow.com](https://docs.voodflow.com). Package source remains private/licensed.

<img class="filament-hidden" src="https://raw.githubusercontent.com/voodflow/voodflow-public-docs/main/vdocs/images/promo.png" alt="VoodDocs — Filament Docs Engine by VoodFlow" />

**Version 0.0.16** · **Paid — source-available (not Open Source)**

Filament 5 package for documentation sites: markdown pages, VitePress-style sidebar navigation, table of contents, locales, doc templates, section versioning, and optional integration with [voodflow/voodbuilder](https://github.com/voodflow/voodbuilder). Requires a **Filament 5** admin panel — it does not run as a plain Laravel package without Filament.

Production use requires a **paid Voodflow license** via [Anystack](https://anystack.sh). Source is available for audit and local development under the [Voodflow Source-Available License](https://docs.voodflow.com) — this is **not** MIT/Apache Open Source. See [docs.voodflow.com](https://docs.voodflow.com).

## Requirements

- PHP 8.4+
- Laravel 12 or 13
- Filament 5
- [voodflow/vmedia](https://github.com/voodflow/vmedia) — Media Vault (markdown editor uploads / vault roots)
- [ralphjsmit/laravel-filament-seo](https://github.com/ralphjsmit/laravel-filament-seo)
- [spatie/laravel-markdown](https://github.com/spatie/laravel-markdown)

**Optional**

- [voodflow/voodbuilder](https://github.com/voodflow/voodbuilder) — site shell (header nav, footer, theme) + Integration typography preview

## Installation

### Anystack (private Composer repository)

Licensed customers install from the Anystack private registry (see [Anystack: private PHP packages](https://anystack.sh/docs/guides/private-php-packages)). Add the repository to your Laravel app `composer.json`:

```json
{
    "repositories": [
        {
            "type": "composer",
            "url": "https://vdocs.composer.sh"
        }
    ]
}
```

Then require the package:

```bash
composer require voodflow/vdocs
php artisan vdocs:install
```

Composer will prompt for HTTP Basic auth against `vdocs.composer.sh`:

```text
Loading composer repositories with package information
Authentication required (vdocs.composer.sh):
Username: [licensee-email]
Password: [license-key]
```

**Credentials**

- **Username:** the email address on your Anystack license, or `unlock` if the license is not assigned to a specific licensee.
- **Password:** your license key from Anystack.

If your license policy requires a **fingerprint**, append it to the license key with a colon:

```text
Password: 8c21df8f-6273-4932-b4ba-8bcc723ef500:your-fingerprint.example
```

If no fingerprint is required, use only the license key (no colon).

You can store the same credentials in `auth.json` or the `COMPOSER_AUTH` environment variable so `composer update` does not prompt every time.

`vdocs:install` publishes configs and migrations for vdocs and its dependencies, runs migrations, and — when [voodflow/voodbuilder](https://github.com/voodflow/voodbuilder) is installed — updates `config/vdocs.php` to use voodbuilder layouts.

Options:

```bash
php artisan vdocs:install --force          # overwrite published config files
php artisan vdocs:install --skip-migrate   # publish only, run migrate yourself
```

### From GitHub (Composer)

```json
{
    "repositories": [
        {
            "type": "vcs",
            "url": "https://github.com/voodflow/vdocs.git"
        }
    ],
    "require": {
        "voodflow/vdocs": "^0.0.15"
    }
}
```

Then run:

```bash
composer update voodflow/vdocs
php artisan vdocs:install
```

### Local path (development)

```json
{
    "repositories": [
        { "type": "path", "url": "packages/voodflow/vdocs" }
    ],
    "require": {
        "voodflow/vdocs": "*"
    }
}
```

## Filament setup

Register the plugin in your panel provider:

```php
use Voodflow\Vdocs\VdocsPlugin;

$panel->plugins([
    VdocsPlugin::make(),
]);
```

After installation, open your Filament panel. The **Vdocs** navigation group contains:

| Resource / page | Purpose |
|---------------|---------|
| **Sections** | Top-level documentation areas (`guide`, `api`, …) |
| **Pages** | Markdown pages, assigned to a section or standalone |
| **Settings** | Site title, docs home, sidebar behaviour |

## Using the plugin

### Content model

vdocs stores documentation in three database tables:

- **Sections** — logical groups with a URL slug and sort order.
- **Pages** — markdown content with layout, locale, publication status, and sidebar metadata.
- **Settings** — global documentation options (cached).

### Creating content manually

1. **Create a section** (e.g. slug `guide`, title `Guide`).
2. **Create pages** inside that section:
   - `slug`: URL segment (`getting-started`, or `index` for the section landing page)
   - `layout`: `doc` (default article), `home` (hero landing), or `page` (root-level article without section)
   - `status`: must be **Published** to appear on the public site
   - `sidebar_group`: optional group label in the left sidebar (e.g. `Getting Started`)
   - `sidebar_order` / `sort_order`: control navigation order

3. Write markdown in the **Content** field. Headings (`##`, `###`, …) automatically feed the on-page table of contents.

### Page layouts

| Layout | Use for | Public URL |
|--------|---------|------------|
| `home` | Documentation landing with hero + feature grid | `/docs` (when selected as home) |
| `doc` | Standard documentation article inside a section | `/docs/{section}/{slug}` |
| `page` | Standalone page at the docs root (no section) | `/docs/{slug}` |

The **home** layout reads structured data from `home_data` (hero name/text/tagline, image, action buttons, feature cards). This is populated automatically when importing VitePress `layout: home` frontmatter, or can be edited in the database.

### Documentation settings

Open **Vdocs → Settings**:

| Setting | Description |
|---------|-------------|
| **Site title / description** | Used in SEO metadata |
| **Show table of contents on docs home** | Right-hand outline on `/docs` when using a doc home page |
| **Docs home source** | `Documentation page` or `Site page` (requires voodbuilder) |
| **Documentation home page** | Which published `home` layout page is served at `/docs` |
| **Site page** | A voodbuilder site page served at `/docs` instead of the doc home |
| **Show section sidebar on doc pages** | Toggle the left navigation column |

**Home resolution order for `/docs`:**

1. If *Site page* mode is enabled and a valid published voodbuilder page is selected → render that page.
2. Otherwise → render the selected (or first available) **home** layout doc page.
3. If no home page exists → redirect to the first section.
4. If there are no sections either → 404.

### Public routes

All routes use the `vdocs.locale` middleware. Default prefix is `docs` (configurable).

| URL | Description |
|-----|-------------|
| `/docs` | Documentation home (see settings above) |
| `/docs/{section}` | Section landing page (`index` slug) |
| `/docs/{section}/{slug}` | Documentation article |
| `/docs/{slug}` | Root-level page (`layout: page`, no section) |

When voodbuilder is present, a **Docs** dropdown can appear in the site header and mobile menu:

- **Preferred:** menu item type **Documentation** (Admin → VoodBuilder → Menus). Children are resolved live from **navigable topics** (product docs with a published home page and/or sections). Empty topics like legacy `default` are skipped. Add a topic → it appears in the dropdown without editing the menu.
- With **multiple** products: Overview (`/docs`) + one link per product home (`/docs/{topic}`).
- With a **single** product: only that product home (no duplicate Overview).
- Legacy sites with no navigable topics still list Overview + sections.

**Legacy auto-inject:** when `nav_show_in_header` is enabled and no Documentation menu item exists in `main`, the package still injects `<x-voodbuilder::docs-menu />` after the Admin menu (same topic/section rules). Auto-inject is skipped when a Documentation item is already present (avoids duplicates).

By default `vdocs.nav.show_in_header` is `false`. To enable auto-inject:

1. Set `'show_in_header' => true` in `config/vdocs.php` (host override of the package default), **or**
2. Use **Admin → Vdocs settings** and enable the `nav_show_in_header` toggle (persisted in `DocSettings`; overrides the config default).

**Precedence:** once a `DocSettings` row exists, `nav_show_in_header` in the DB wins over `config/vdocs.php`. Changing config to `false` alone will not hide the menu if Admin (or a previous save) left `nav_show_in_header: true`. Disable the toggle in Admin, or:

```bash
php artisan tinker --execute 'Voodflow\Vdocs\Models\DocSettings::saveData(["nav_show_in_header" => false]);'
```

Hiding the dropdown does not disable documentation routes — only the automatic nav injection.

### Configuration

Publish and edit `config/vdocs.php`:

| Key | Purpose |
|-----|---------|
| `prefix` | URL prefix (default: `docs`) |
| `force_standalone` / `VDOCS_FORCE_STANDALONE` | Keep package shell instead of voodbuilder (default: `false`) |
| `doc_layout` / `home_layout` | Blade layouts for doc and home pages |
| `layouts` | Named layout map (`doc`, `home`, `page`) |
| `features.public_routes` | Enable public `/docs` routes |
| `features.localization` | Enable locale-prefixed URLs |
| `locales` / `default_locale` | Supported languages |
| `sidebar.show_on_doc_pages` | Default for left sidebar visibility |
| `home.show_toc` | Default for home page outline |
| `home.mode` | Default home source (`doc_page` or `site_page`) |
| `nav.show_in_header` | Docs dropdown in voodbuilder site nav (default: `false`) |

When `voodflow/voodbuilder` is installed and `force_standalone` is `false`, Vdocs uses its **own** reading layouts inside Voodbuilder chrome (`vdocs::layouts.voodbuilder`) and registers the docs URL prefix plus an Integration-tab reading preview. Set `VDOCS_FORCE_STANDALONE=true` to keep the package standalone shell (`vdocs::layouts.doc`).

### Morph map (required with `HasSEO`)

If your application uses `Relation::enforceMorphMap()`, register the doc page model:

```php
'doc_page' => \Voodflow\Vdocs\Models\DocPage::class,
```

### Blade components

| Component | Purpose |
|-----------|---------|
| `<x-vdocs::outline />` | On-page table of contents |
| `<x-vdocs::doc-sidebar-nav />` | Multi-group section sidebar |
| `<x-vdocs::language-switcher />` | Locale switcher (when localization is enabled) |

## Importing from VitePress

vdocs includes an Artisan command to migrate an existing VitePress **content folder** (the directory that contains `index.md` and section subfolders) into the database.

### Command

```bash
php artisan vdocs:import-vitepress /path/to/docs [--locale=en|it] [--topic=voodbuilder] [--docs-version=1.x] [--force]
```

| Argument / option | Description |
|-------------------|-------------|
| `path` | Root folder with `.md` files (e.g. `cosmolab-docs/` or `docs/`) |
| `--locale` | Import into this locale (defaults to `default_locale`; prompts when omitted in interactive mode) |
| `--topic` | Product topic slug (one plugin = one topic). Create with `--create-topic` when missing |
| `--docs-version` | Tag imported **sections** with this version (`1.x`). Omit / `latest` = current tree (`docs_version` null) |
| `--force` | Delete existing pages for the selected topic/locale (and version when `--docs-version` is set) before importing |

Without `--force`, the command aborts if conflicting pages already exist.

**Version folders:** paths like `1.x/guide/foo.md`, `v1/guide/foo.md`, or frontmatter `version: 1.x` / `docs_version: 1.x` map into parallel section trees under the same topic. Do **not** invent a second topic (`voodbuilder-v1`) for older releases.

**Locale-aware paths:** if markdown files live under a locale prefix (`it/guide/foo.md`), they are imported only when `--locale=it` matches that prefix. Files without a prefix use the selected `--locale`.

### Seed section translations (one-shot)

After importing English content (or when upgrading an existing single-locale site), create linked section rows for another locale and fix pages that still point at the source sections:

```bash
# Preview
php artisan vdocs:seed-section-translations --dry-run

# Create IT sections from EN (default source/target from config/vdocs.php)
php artisan vdocs:seed-section-translations --source=en --target=it
```

| Option | Description |
|--------|-------------|
| `--source` | Source locale (default: `default_locale`, usually `en`) |
| `--target` | Target locale (default: first non-default locale, e.g. `it`) |
| `--dry-run` | Print planned creates/remaps without writing |

The command:

1. Creates missing **section** translations in the target locale (same slug, localized title from `SectionCatalog`).
2. **Remaps** target-locale pages whose `section_id` still points at a source-locale section.

Typical Cosmolab workflow:

```bash
php artisan vdocs:import-vitepress ../cosmolab-docs --locale=en --force
php artisan vdocs:seed-section-translations --source=en --target=it
# later, when Italian markdown exists:
php artisan vdocs:import-vitepress ../cosmolab-docs/it --locale=it --force
```

Known section titles (`guide` → `Guida`, `api` → `Riferimento API`, …) live in `src/Support/SectionCatalog.php` — update that file for custom projects (also used by the VitePress importer).

### What the importer does

1. **Scans markdown files** recursively, skipping `.vitepress/` and `node_modules/`.
2. **Parses YAML frontmatter** (`---` blocks at the top of each file).
3. **Creates or updates sections and pages** in the database, all marked as published.
4. **Rewrites internal links** in markdown from VitePress paths (`/guide/foo`) to vdocs paths (`/docs/guide/foo`).
5. **Rewrites hero action links** in home frontmatter the same way.
6. **Optionally copies images** from `.vitepress/dist/assets/` into `public/images/` (see below).

### Expected folder structure

A typical VitePress docs tree:

```
docs/
├── index.md              → home page (layout: home)
├── guide/
│   ├── index.md          → /docs/guide
│   ├── getting-started.md
│   └── first-project.md
├── api/
│   ├── index.md
│   └── daisy-cosmolab.md
└── resources.md          → root-level page (layout: page)
```

**Path → URL mapping:**

| File | Section | Slug | Result URL |
|------|---------|------|------------|
| `index.md` | — | `index` | `/docs` (home layout) |
| `guide/index.md` | `guide` | `index` | `/docs/guide` |
| `guide/getting-started.md` | `guide` | `getting-started` | `/docs/guide/getting-started` |
| `resources.md` | — | `resources` | `/docs/resources` |

### Frontmatter support

The importer recognises VitePress frontmatter keys:

```yaml
---
title: Custom Page Title
layout: home        # home | page (default: doc)
hero:
  name: Product Name
  text: Tagline
  tagline: Subtitle
  image:
    src: /images/logo.png
    alt: Logo
  actions:
    - theme: brand
      text: Get Started
      link: /guide/getting-started
features:
  - icon: 🎛️
    title: Feature title
    details: Feature description
---
```

| `layout` value | vdocs layout | Notes |
|----------------|--------------|-------|
| `home` | `home` | Hero and features stored in `home_data` |
| `page` | `page` | Root-level article, no section |
| *(absent)* | `doc` | Standard section article |

If `title` is omitted, the importer derives it from the VitePress sidebar label or the section/file name.

### Sidebar groups and order

During import, each page receives `sidebar_group`, `sidebar_order`, and `sort_order` from a **sidebar index** built at import time.

The default sidebar definitions in `ImportVitePressCommand` mirror the Cosmolab VitePress project (`guide`, `api`, `examples`, `hardware`, `ai`). Page titles and sidebar groups match the original VitePress `config.mjs` sidebar.

**For a different VitePress site**, update `sidebarDefinitions()` in `ImportVitePressCommand.php` and section titles in `SectionCatalog.php`.

Copy the `sidebar` object from your `.vitepress/config.mjs` into the same structure, then re-run the import with `--force`. Alternatively, import once and adjust sidebar groups manually in Filament.

### Link rewriting

Markdown links are rewritten on import:

| Original (VitePress) | After import |
|----------------------|--------------|
| `/guide/getting-started` | `/docs/guide/getting-started` |
| `./other-page` | `/docs/{section}/other-page` |
| `../api/foo` | `/docs/api/foo` |
| `https://…`, `#anchor`, `mailto:` | unchanged |

The prefix follows `config('vdocs.prefix')` (default `docs`).

### Images and assets

If you have already built the VitePress site (`npm run docs:build`), the importer copies matching files from:

```
{path}/.vitepress/dist/assets/
```

into `public/images/`. By default this applies to files whose names start with `cosmolab_` or `lasercut_`.

For other projects, either:

- place images in `public/images/` before import and reference them as `/images/...` in markdown, or
- extend `copyDistAssets()` in `ImportVitePressCommand.php`.

Hero images referenced in frontmatter (`hero.image.src`) are kept as absolute paths (e.g. `/images/logo.png`).

### Example: Cosmolab

```bash
# From the Laravel app container or project root
php artisan vdocs:import-vitepress /path/to/cosmolab-docs --force
```

Then open `/docs` in the browser. With voodbuilder installed you should see the hero landing; section pages appear under `/docs/{section}/{slug}`.

### After import

1. Visit **Filament → Vdocs → Settings** and confirm the **Documentation home page** points to the imported `index.md` home page.
2. Review **Pages** for any missing sidebar groups or titles.
3. Run `npm run build` in your Laravel app if voodbuilder frontend assets changed.
4. Clear rendered content cache if you edit pages programmatically: each page clears its own cache on save.

### Re-importing

`--force` deletes **pages (and orphan sections) for the selected locale only** before importing. Other locales and global settings are preserved. Use this when refreshing content from an updated VitePress tree for one language at a time.

## Testing

```bash
cd packages/voodflow/vdocs
composer install
vendor/bin/phpunit
```

## License

**Voodflow Source-Available License** — the source is visible for audit and local development, but this is **not Open Source**. Production use requires a **paid license** from Voodflow. See [LICENSE](https://docs.voodflow.com) and [docs.voodflow.com](https://docs.voodflow.com).

The site shell [voodflow/voodbuilder](https://github.com/voodflow/voodbuilder) is **Community free** (source-available; not OSI Open Source).
