# Platform Management Center

> Administering a multi-tenant Klique deployment, with isolated tenants sharing compute, platform-wide cost and utilization, and shared config.

Teams can share a single Klique tenant, or a deployment can be divided into isolated tenants, typically one per team or
business unit. The Platform Management Center is for the second case: it administers a multi-tenant deployment from one
place rather than tenant by tenant, so the compute behind those tenants stays shared across them and the cost of using it
is attributed back to whoever consumed it.

![Platform Management Center](../img/pmc_platform_usage.png#light-mode-only)
![Platform Management Center](../img/pmc_platform_usage_dark.png#dark-mode-only)

## One deployment, many tenants

Platform administrators create and configure tenants, set their user quotas, generate their admin credentials, and
track each tenant's usage and cost. Tenants are isolated from one another, yet compute balances between them rather than
being partitioned and left idle, so isolation does not cost utilization. For what isolation covers and how it is
achieved, see [Multi-Tenancy](../orchestration_engine/multi_tenancy.md).

## Cost and utilization across the organization

The Platform Usage dashboards aggregate the whole deployment: compute resource utilization, hardware inventory, and
metered usage and cost across every tenant. Platform-wide trends are therefore visible directly, instead of being
reconstructed from each tenant in turn, and spend can be attributed per tenant for showback or chargeback. See
[usage metering and billing](../governance_engine/usage_metering_billing.md).

## Configuration defined once

Storage configuration and template variables are defined once at the platform level and assigned to the tenants that
need them, with per-tenant overrides where a tenant differs. A change to a shared template reaches every tenant using
it, rather than being repeated per tenant and drifting between them.

## Applications across tenants

Klique applications are installed and rolled out across the deployment from one place, with per-tenant availability
control and tenant-specific customization. An application a team builds can be made available to the tenants that
should have it without being rebuilt or reinstalled for each.

## Reference and deployment

For the modules that make up the Platform Management Center and what each one covers, see the
[Platform Management Center reference](../platform_management_center/pmc_overview.md).

The Platform Management Center is deployed on Kubernetes, alongside an existing Klique control plane. See
[Platform Management Center deployment](../deploying_klique/extra_configs/platform_management_center_deploy.md).
