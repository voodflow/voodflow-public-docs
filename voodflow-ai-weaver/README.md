# Voodflow AI Weaver

> Public README mirror for Filament / marketing links. Canonical product docs: [docs.voodflow.com](https://docs.voodflow.com). Package source remains private/licensed.

Commercial add-on for [Voodflow](https://voodflow.com): **Flow Weaver** — an AI chat panel inside the flow editor to design and refine automations.

## Requirements

| Package | Role |
|---------|------|
| [voodflow/voodflow](https://github.com/voodflow/voodflow) | Workflow engine + canvas |
| [voodflow/voodflow-ai-core](https://github.com/voodflow/voodflow-ai-core) | Shared AI engine (MIT) |
| [voodflow/voodflow-ai](https://github.com/voodflow/voodflow-ai) | Workflow AI nodes + executor |
| [laravel/ai](https://github.com/laravel/ai) | Laravel AI SDK |

## Features

- **Flow Weaver chat** — sidebar in the workflow canvas (`?edit=1`)
- **Weaver Agents** — agent integrations scoped to domain `weaver` (separate from workflow node agents)
- **Agent selector** — pick the active agent in the chat composer
- **Blueprint apply** — AI-generated graph patches applied to the canvas
- **Filament resource** — manage Weaver agents under *Flow Weaver → Weaver Agents*

## Environment

```env
VOODFLOW_AI_LICENSE_ACTIVE=true
VOODFLOW_AI_WEAVER_LICENSE_ACTIVE=true

# Optional: pre-select an agent in chat (integration_key from Weaver Agents)
VOODFLOW_AI_WEAVER_DEFAULT_AGENT=

# Optional limits
VOODFLOW_AI_WEAVER_MAX_MESSAGES=50
```

## Host app (Filament)

Register the plugin on the admin panel:

```php
use Voodflow\AiWeaver\VoodflowAiWeaverFilamentPlugin;

->plugins([
    // ...
    VoodflowAiWeaverFilamentPlugin::make(),
])
```

## Agent domains

Weaver agents use `AgentIntegration::DOMAIN_WEAVER` and do **not** appear in AI Prompt / AI Agent workflow nodes. Workflow agents remain on domain `workflow`.

## Related

- **Core (MIT):** `voodflow/voodflow-ai-core`
- **Workflow AI:** `voodflow/voodflow-ai`
- **Feature flag:** `Voodflow::feature('ai_weaver')` (requires `ai`)

## License

Proprietary — requires `VOODFLOW_AI_WEAVER_LICENSE_ACTIVE=true`.

## Documentation

Public documentation: [docs.voodflow.com](https://docs.voodflow.com)
