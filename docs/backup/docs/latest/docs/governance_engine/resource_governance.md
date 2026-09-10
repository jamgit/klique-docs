# Resource Governance

> Klique's resource governance over shared compute, including quotas, resource policies, spillover, and usage cost accountability.

Klique's resource governance controls shared compute: who can use it, how much each team is guaranteed, how far they can
burst beyond that, and how consumption is tracked and paid for. It sets and enforces the rules for allocation so the
same GPUs and CPUs serve many teams predictably, without manual gatekeeping or stranded capacity.

Resource governance is part of Klique's [Governance Engine](governance_engine.md), which centralizes security, access,
and compliance controls. It defines the policies; the [Orchestration Engine](../orchestration_engine/orchestration_engine.md)
enforces them, routing each workload to compute within the limits set here. Quotas and queues apply to the user groups
defined in [access management](rbac.md), so every allocation is tied to a known identity.

## Orchestration dashboard

The [orchestration dashboard](../webapp/webapp_orchestration_dash.md) gives administrators a real-time view of available
and in-use compute across the tenant, reported as a global total and per resource category, along with utilization over
time, an event log, and per-worker performance metrics. Idle and over- or under-utilized resources are surfaced
directly, so allocation decisions are grounded in what the hardware is actually doing rather than what was requested.
Visibility is the foundation of every other control: you cannot allocate or cap what you cannot see.

![Orchestration Dashboard](../img/webapp_orchestration_dash.png#light-mode-only)
![Orchestration Dashboard](../img/webapp_orchestration_dash_dark.png#dark-mode-only)

## Platform Management Center

The [Platform Management Center](../whats_klique/management_center.md) extends the orchestration
dashboard from a single tenant to the whole platform. Where the orchestration dashboard shows compute availability and
utilization within one tenant, the Platform Management Center gives platform administrators the same visibility across
every tenant as well as a combined view of the entire cluster along with the ability to track aggregated
usage and cost.

![PMC orchestration dashboard](../img/pmc_overview_orch_dash.png#light-mode-only)
![PMC orchestration dashboard](../img/pmc_overview_orch_dash_dark.png#dark-mode-only)

## Resource policies and quotas

[Resource policies](../webapp/resource_policies.md) allocate a tenant's compute across
[user groups](../webapp/settings/webapp_settings_users.md#user-groups). Each policy sets a **reservation**, the amount
of compute guaranteed to the group, and a **limit**, the maximum it can use concurrently. Administrators expose that
allocation through execution queues: each queue is tied to a **resource profile** that defines how much compute a job
enqueued through it receives, and profiles draw from **resource pools** configured for the deployment. A group submits
jobs to its queue and automatically receives governed resources within its policy, while non-administrators can see the
quotas, reservations, limits, and queues that apply to them. See
[Resource Management](../orchestration_engine/resource_policies.md) for how policies, profiles, and pools fit together.

The gap between reservation and limit is where shared compute stays efficient. A group is always guaranteed its
reservation, but when capacity is idle it can burst above that reservation up to its limit, so unused GPUs are put to
work instead of sitting reserved and empty.

![Resource policies](../img/resource_policies_dashboard.png#light-mode-only)
![Resource policies](../img/resource_policies_dashboard_dark.png#dark-mode-only)

## Spillover

Spillover routes work across resource pools when a preferred pool is full. A resource profile can list several pools in
routing priority order, and when its preferred pool is full, jobs spill over to the next pool in the list. With
[cloud autoscaling](../orchestration_engine/cloud_autoscaling.md), spillover reaches beyond your own hardware, so
on-premises demand that exceeds local capacity overflows to the cloud and scales back down once the spike passes.

## Role-based access control

[Role-based access control](rbac.md) (RBAC) determines which users, service accounts, and user groups can reach each
resource, including the queues that represent compute. Administrators define access rules granting Read Only or
Read & Modify access, and because a queue is a compute resource, those rules decide who can run workloads on which
hardware, not just who can view them. Service accounts give agents and automation their own scoped permissions rather
than borrowed credentials, so non-human workloads are governed by the same rules as people. RBAC pairs with resource
policies: policies set how much compute a group receives, while RBAC sets which groups and identities can use the queues
that expose it.

## Usage metering and cost accountability

Governing resources also means accounting for them. Klique's [usage metering](usage_metering_billing.md) records
consumption per tenant and per user and turns it into cost breakdowns, so allocation decisions can be tied to spend and,
where needed, charged back to the teams that consume compute. This closes the loop between the quotas you set and the
cost they produce.
