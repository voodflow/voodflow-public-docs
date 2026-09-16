# voodflow/vgen

> Public README mirror for Filament / marketing links. Canonical product docs: [docs.voodflow.com](https://docs.voodflow.com). Package source remains private/licensed.

Plugin Laravel/Filament per generazione contenuti AI: personaggi, battute, prompt, immagini, strip fumetto, albi da colorare, asset social e POD.

Generico e riutilizzabile — **non** legato a un singolo brand. Include solo dati demo opzionali (Pettosetti Demo).

## Requisiti

- PHP 8.2+
- Laravel 11 / 12 / 13
- Filament 4 / 5
- Estensione PHP GD (per Intervention Image)

## Installazione

```bash
composer require voodflow/vgen
php artisan vgen:install
```

Registra il plugin nel `PanelProvider`:

```php
use Voodflow\Vgen\VgenPlugin;

public function panel(Panel $panel): Panel
{
    return $panel
        ->plugin(VgenPlugin::make());
}
```

## Configurazione

Pubblica la config:

```bash
php artisan vendor:publish --tag=vgen-config
```

### Variabili `.env`

```env
VGEN_STORAGE_DISK=local
VGEN_TEXT_PROVIDER=openai
VGEN_IMAGE_PROVIDER=openai
VGEN_PDF_PROVIDER=browsershot
VGEN_DEFAULT_MODEL_TEXT=gpt-4o-mini
VGEN_DEFAULT_MODEL_IMAGE=dall-e-3
VGEN_QUEUE=default
VGEN_ENABLE_API=false
VGEN_ENABLE_VOODFLOW=true

OPENAI_API_KEY=sk-...
```

Le API key **non** vengono salvate nel database del plugin. Usa `.env` o, se presente, il credential store cifrato di Voodflow.

### Sicurezza contenuti

Con `vgen.safety.require_manual_approval_before_publish=true` (default), automazioni e pubblicazione devono usare solo contenuti `approved`.

## Comandi Artisan

| Comando | Descrizione |
|---------|-------------|
| `php artisan vgen:install` | Pubblica config/migrations, migrate opzionale, demo opzionale |
| `php artisan vgen:generate-jokes {project}` | Accoda generazione battute |
| `php artisan vgen:generate-characters {project}` | Accoda generazione personaggi |
| `php artisan vgen:generate-assets {project}` | Accoda generazione immagini |
| `php artisan vgen:make-coloring-book {project}` | Accoda PDF albo da colorare |
| `php artisan vgen:demo-data` | Crea progetto demo Pettosetti |

## API (opzionale)

Abilita con `VGEN_ENABLE_API=true`.

Tutte le route usano `config('vgen.api_middleware')` (default `auth:sanctum`).

| Metodo | Endpoint |
|--------|----------|
| POST | `/api/vgen/projects/{project}/generate-jokes` |
| POST | `/api/vgen/projects/{project}/generate-characters` |
| POST | `/api/vgen/projects/{project}/generate-comic-strip` |
| POST | `/api/vgen/projects/{project}/generate-coloring-book` |
| POST | `/api/vgen/assets/{asset}/approve` |
| POST | `/api/vgen/assets/{asset}/reject` |

## Integrazione Voodflow

Se `voodflow/voodflow` è installato e `VGEN_ENABLE_VOODFLOW=true`, il package registra automaticamente:

### Eventi Laravel (trigger)

- `VgenProjectCreated`
- `VgenJokesGenerated`
- `VgenCharacterGenerated`
- `VgenAssetGenerated`
- `VgenAssetApproved` / `VgenAssetRejected`
- `VgenComicStripGenerated`
- `VgenBookGenerated`
- `VgenSocialPostReady`
- `VgenGenerationFailed`

In Voodflow usa un nodo **Event Trigger** e seleziona il gruppo **Vgen**.

### Nodi custom PHP

Classi in `Voodflow\Vgen\Voodflow\Nodes\`:

- `VgenGenerateJokesNode`
- `VgenGenerateImageNode`
- `VgenComposePostNode`
- `VgenGenerateColoringBookNode`

Per UI canvas completa, con Voodflow installato:

```bash
php artisan voodflow:make-node VgenGenerateJokesNode
php artisan voodflow:build-node VgenGenerateJokesNode
```

Punta il `manifest.json` alla classe PHP del package.

Se Voodflow **non** è installato, il plugin funziona normalmente (`class_exists` + provider condizionale).

## Esempi workflow Voodflow

### 1. 100 battute ogni lunedì

1. **Schedule** → cron `0 9 * * 1`
2. **Data Model** o **Set Variable** → `project_id`
3. **PHP Code** o nodo `VgenGenerateJokesNode` → `count: 100`
4. Opzionale **Send Webhook** verso review tool

### 2. 10 pagine da colorare

1. **Manual Trigger** o **Receive Webhook**
2. `VgenGenerateImageNode` con `type: coloring` in loop **For Each**
3. **Model Update** → status `approved` dopo review

### 3. Quando asset approvato → post social

1. **Event Trigger** → `VgenAssetApproved`
2. **If** → `asset.type == image`
3. `VgenComposePostNode` → format `instagram_post`
4. **System Notification** al team

### 4. Quando libro generato → webhook outbound

1. **Event Trigger** → `VgenBookGenerated`
2. **Send Webhook** con HMAC e payload `book.*`
3. Usa credential store Voodflow per URL/secret

## Estensioni future

Interfacce pronte per integrazioni esterne:

- `SocialPublisherInterface` — pubblicazione social (es. Postiz)
- `ProductPublisherInterface` — POD (es. Printful)
- `WebhookDispatcher` — dispatch webhook custom

## Testing

```bash
cd packages/voodflow/vgen
composer install
vendor/bin/phpunit
```

Usa provider fake (`text/image/pdf` = `fake` o ambiente `testing`).

## Licenza

Proprietary — package vendibile.
