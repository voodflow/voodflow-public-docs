# Voodflow AI Agents

> Public README mirror for Filament / marketing links. Canonical product docs: [docs.voodflow.com](https://docs.voodflow.com). Package source remains private/licensed.

Commercial add-on for [Voodflow](https://voodflow.com): **Agent Integrations**, **AI Prompt**, and **AI Agent** workflow nodes.

Built on **[voodflow-ai-core](https://github.com/voodflow/voodflow-ai-core)** (MIT) for the shared executor, registry, and database schema.

Requires `laravel/ai` in the host application. API keys are stored via Voodflow **Credentials** (BYOK).

## Installation

```bash
composer require voodflow/voodflow-ai
```

```env
VOODFLOW_AI_LICENSE_ACTIVE=true
VOODFLOW_AI_DEFAULT_PROVIDER=openai
VOODFLOW_AI_DEFAULT_MODEL=gpt-4o-mini
```

Register the Filament plugin in your panel provider (alongside `VoodflowPlugin`):

```php
use Voodflow\Ai\VoodflowAiFilamentPlugin;

$panel->plugins([
    VoodflowPlugin::make(),
    VoodflowAiFilamentPlugin::make(),
]);
```

Build node JS bundles after install:

```bash
php artisan voodflow:build-node AiPromptNode
php artisan voodflow:build-node AiAgentNode
```

## Testing

```bash
composer install
vendor/bin/phpunit
```

## Documentation

Public documentation: [docs.voodflow.com](https://docs.voodflow.com)


## License

When `VOODFLOW_AI_LICENSE_ACTIVE=false`, AI nodes are not registered and workflows containing AI steps fail at execution with a clear upgrade message. Other Voodflow workflows continue to run.

## Related

- **Core (MIT):** [voodflow/voodflow-ai-core](https://github.com/voodflow/voodflow-ai-core)
- **Workflow engine:** `voodflow/voodflow`
- **OEM:** may depend on this package (AI Agents included with OEM license — configure separately)
