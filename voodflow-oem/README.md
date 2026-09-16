# Voodflow OEM

> Public README mirror for Filament / marketing links. Canonical product docs: [docs.voodflow.com](https://docs.voodflow.com). Package source remains private/licensed.

Commercial add-on for multi-tenant OEM deployments: **usage metering**, **tenant quotas**, **operator console**, and a **token-authenticated platform API** for external billing systems (e.g. Sellero).

Pricing (PLN, Stripe, etc.) stays in the OEM host application — this package exposes neutral metrics only.

## Installation

```bash
composer require voodflow/voodflow-oem
```

```env
VOODFLOW_OEM_LICENSE_ACTIVE=true
VOODFLOW_OEM_API_ENABLED=true
VOODFLOW_OEM_PLATFORM_TOKEN=your-secret-server-token
# Local dev only — auto-assign new workflows to this tenant:
VOODFLOW_OEM_DEFAULT_TENANT_ID=demo-tenant
```

## Documentation

Public documentation: [docs.voodflow.com](https://docs.voodflow.com)


## Operator console (summary)

When licensed, **Tenant insights** and **Tenants** appear under the **VoodFlow** Filament navigation group (default panel: `admin`):

- **Tenant insights** — tenant status overview, billing-month usage, top tenants, expiring quotas, at-risk accounts, global activity log
- **Tenants** — CRUD, user mapping, quota editing, per-tenant insights with date-range filter

Demo data for local QA:

```bash
php artisan voodflow-oem:seed-demo
```

Disable UI only: `VOODFLOW_OEM_OPERATOR_PANEL_ENABLED=false`.

## Tenancy

OEM metering keys off the string **`tenant_id`** on workflows and executions.

When licensed, the package registers `OemTenantResolver` as Voodflow's `TenantResolver`. Resolution order:

1. Core context (`TenantContext`, user `tenant_id`, `X-Tenant-ID` header)
2. Session key `voodflow_oem_tenant_id` (set by host SSO)
3. OEM registry `metadata.auth_user_ids` / `auth_emails` (synced via Platform API)
4. `VOODFLOW_OEM_DEFAULT_TENANT_ID` in `local` / `testing` only (unless `VOODFLOW_OEM_ALLOW_DEFAULT_TENANT=true`)

## Features enabled

Registers `OemFeatureProvider` → Core flags **`oem`** and **`tenancy`**, plus `OemWorkflowGate` for quota/status enforcement.

## License expiry

When `VOODFLOW_OEM_LICENSE_ACTIVE=false`:

- Existing workflows and executions continue to run
- `GET` usage endpoints remain available
- `PUT` quota/tenant sync returns **403**
- Operator panel and feature flags are disabled

## Related packages

- **Whitelabel:** `voodflow/voodflow-whitelabel` — branding add-on (orthogonal to OEM)
