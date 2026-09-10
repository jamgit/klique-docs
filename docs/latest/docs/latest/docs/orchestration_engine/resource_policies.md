# Resource Management

> The resource dashboard, resource policies and pools, and the Platform Management Center for allocating and monitoring compute.

Klique gives administrators and users a consistent way to see, allocate, and govern compute across an organization.
Resource management works at three levels:

- The **resource dashboard** shows the compute available to a single tenant and how it is being used.
- **Resource policies and pools** control how that compute is allocated across user groups.
- The **Platform Management Center** provides visibility and control across all tenants in a deployment.

## Resource dashboard

The orchestration dashboard gives a tenant a real-time view of its available and in-use compute. It reports available and
utilized resources as a global total and per category, resource utilization over time, a resource event log, and detailed
performance metrics.

The top of the dashboard summarizes current availability and utilization: total and idle GPUs, CPUs, and workers, so it is
clear how much of the tenant's capacity is actually being used. Workers are organized by category and group using the
naming policy `<category>:<group>:<name>`, and each group can be expanded to inspect individual workers, apply filters, and
set threshold values that highlight over- or under-utilized resources.

![Resource dashboard](../img/webapp_orchestration_dash.png#light-mode-only)
![Resource dashboard](../img/webapp_orchestration_dash_dark.png#dark-mode-only)

For a full description of the dashboard, see the [Orchestration Dashboard](../webapp/webapp_orchestration_dash.md).

## Resource policies and pools

Resource policies control how a tenant's compute is allocated across user groups. Each policy defines, for a
[user group](../webapp/settings/webapp_settings_users.md#user-groups):

- **Reservation** - the amount of compute guaranteed to be available to the group.
- **Limit** - the maximum amount of compute the group's jobs can use concurrently.

Administrators expose allocated compute to users through execution queues. Each queue is tied to a **resource profile**,
which defines how much compute a job enqueued through it receives. Profiles draw from **resource pools** defined in the
[Resource Configuration](../webapp/settings/webapp_settings_resource_configs.md) settings, and a profile can list several
pools in routing priority order. This lets a group submit jobs to a named queue and automatically receive the resources
defined by its profile, within the policy's reservation and limit.

Non-administrator users can see the policies that apply to them, including current usage, reserved resources, limits, and
the queues available to them.

![Resource policies](../img/resource_policies_dashboard.png#light-mode-only)
![Resource policies](../img/resource_policies_dashboard_dark.png#dark-mode-only)

For details on creating and managing policies, see [Resource Policies](../webapp/resource_policies.md). Multiple jobs can
also share a single GPU using [Fractional GPUs](fractional_gpus.md).

## Platform Management Center

The Platform Management Center provides platform administrators with visibility and control across all tenants in a
deployment, rather than within a single tenant.

From a single interface, administrators can:

- **Manage tenants** - create and configure tenants, set user quotas, generate admin credentials, and track per-tenant
  usage and cost.
- **Monitor platform-wide usage and cost** - view aggregated metered usage and cost breakdowns across all tenants.
- **View platform-wide orchestration** - see compute availability, utilization, and resource groups across every tenant.
- **Configure shared resources** - define volume templates and template variables that tenants can apply to their
  workloads, and manage applications across the platform.

![Platform Management Center](../img/pmc_platform_usage.png#light-mode-only)
![Platform Management Center](../img/pmc_platform_usage_dark.png#dark-mode-only)

For mre information, see:
* [Platform Management Center](../whats_klique/management_center.md) for an overview
* [Platform Management Center reference](../platform_management_center/pmc_overview.md) for full details
* [Multi-Tenancy](multi_tenancy.md) for how tenants share compute.
