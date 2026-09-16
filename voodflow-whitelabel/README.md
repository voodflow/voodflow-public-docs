# Voodflow Whitelabel

> Public README mirror for Filament / marketing links. Canonical product docs: [docs.voodflow.com](https://docs.voodflow.com). Package source remains private/licensed.

Commercial add-on for [Voodflow](https://voodflow.com). When licensed, enables Core **`whitelabel`** feature flags and exposes branding configuration for the host application.

Workflows and executions are **never blocked** when the license expires — only branding/feature behaviour reverts.

## Installation

```bash
composer require voodflow/voodflow-whitelabel
```

```env
VOODFLOW_WHITELABEL_LICENSE_ACTIVE=true
VOODFLOW_WHITELABEL_PRODUCT_NAME="Sellero Automation"
VOODFLOW_WHITELABEL_HIDE_POWERED_BY=true
VOODFLOW_WHITELABEL_LOGO_URL=https://example.com/logo.svg
VOODFLOW_WHITELABEL_SUPPORT_URL=https://help.example.com
```

## Documentation

Public documentation: [docs.voodflow.com](https://docs.voodflow.com)


## How it works

1. `WhitelabelFeatureProvider` registers on boot when licensed.
2. Core resolves `Voodflow::feature('whitelabel')` without embedding license logic in Core.
3. Host apps read `WhitelabelBranding` for product name, logo, support URL, and powered-by visibility.


## License expiry

Set `VOODFLOW_WHITELABEL_LICENSE_ACTIVE=false` (or remove Composer credentials). Core reverts to standard Voodflow UI labels — existing automations keep running.

## Related

- **OEM add-on:** `voodflow/voodflow-oem` — tenant metering and operator console (orthogonal to whitelabel)
