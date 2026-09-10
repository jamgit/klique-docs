# Platform Management Center

> Platform Management Center modules for usage and cost dashboards, tenants, volume templates, configuration template variables, and applications.

The Platform Management Center provides platform administrators with centralized control over all tenants across a 
Klique deployment. It enables platform administrators to monitor tenant activity, usage, and costs.

## Modules
* [Platform Usage Dashboards](pmc_platform_overview.md) - Platform-wide visibility into compute resource utilization, hardware inventory, and metered usage and costs across all tenants.
* [Tenant Management](pmc_tenants.md) - Create and configure tenants: Set user quotas, generate admin credentials and configure tenant specific parameters. Each tenant's page provides a dashboard showing usage metrics and cost breakdowns, as well as information on that tenant's applications, volume templates, and template variable overrides 
* [Volume Templates](pmc_volumes.md) - Create and assign storage configuration templates to tenants, to configure storage for tenant workloads.
* [Configuration Template Variables](pmc_template_variables.md) - Define template variables for use in platform template specifications, with per-tenant override support.
* [Application Management](pmc_apps.md) - Install, deploy, and manage Klique applications across the deployment, with per-tenant availability control and [tenant-specific customizations](pmc_tenants.md#app-customization).
