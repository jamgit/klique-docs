# Usage Metering & Billing

> Klique's usage metering and billing, covering tracked categories, cost attribution, and integration with external billing systems.

Klique meters usage across the platform and can attach costs to it, giving per-tenant and platform-wide visibility into resource consumption and cost. The metered data can be used for showback and chargeback, and can also be exported to external billing
systems.

## How metering works

A metering service records usage as events, grouped into categories such as compute, storage, tokens, and users.
Administrators define which categories are tracked and can attach pricing to them; the service then aggregates the
reported usage and, where a price is set, the estimated cost. For more information on configuration options, see 
[Event Metering](../deploying_klique/extra_configs/event_metering.mdx).

## What is metered

Built-in reporters cover the main categories, and additional metrics can be tracked by reporting custom events:

* **Compute** - Compute usage is metered from your clusters. Metering can be customized at the queue level, associating a
  queue with a specific resource type and count, so each queue is metered according to the resources it represents.
* **Storage** - Any storage consumption can be metered, across storage types such as local, NFS, and object storage.
* **Tokens** - Model token usage is metered per model, and separately for input and output tokens.
* **Users** - The number of tenant users and service accounts.

## Connecting external billing systems

The metering service exposes its aggregated data through an API, so usage and cost can be fed into third-party
billing systems, BI platforms, and chargeback tooling. The `get_usages` endpoint returns the aggregated data for a given
tenant (or all tenants) and date range, optionally filtered by category and returned with a per-label breakdown. See
[Accessing Aggregated Data](../deploying_klique/extra_configs/event_metering.mdx#accessing-aggregated-data).

## Cost and pricing

Each metered category can be assigned a unit price and currency. When pricing is configured, the service calculates and reports both the
recorded usage and the estimated cost.

## Billing granularity

Usage and cost can be broken down at different levels of granularity: by tenant, group, or user. This makes it
possible to attribute consumption and cost to the responsible part of the organization.

## Viewing usage and cost

* **Tenant administrators** view usage and spend for their own tenant in the
  [Metering](../webapp/webapp_admin_dashboard_metering.md) tab of the Admin Dashboard.
* **Platform maintainers** view usage and cost across all tenants in the Platform Management Center's
  [Platform Usage](../platform_management_center/pmc_platform_overview.md) dashboard, with breakdowns by category or
 tenant, with detailed usage information available on each [tenant page](../platform_management_center/pmc_tenants.md).

![Platform usage dashboard](../img/pmc_platform_usage.png#light-mode-only)
![Platform usage dashboard](../img/pmc_platform_usage_dark.png#dark-mode-only)

## Setup

The metering service runs as part of the Klique control plane. For information about enabling the service, configuring built-in reporters, defining
categories and pricing, and using the reporting and query APIs, see
[Event Metering](../deploying_klique/extra_configs/event_metering.mdx).
