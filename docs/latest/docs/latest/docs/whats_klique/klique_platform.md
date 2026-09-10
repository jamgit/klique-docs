# Klique Platform

> How Klique enables an organization to build and consume AI, through self-serve compute, governed access, publishing, and measured consumption.

Klique sits between the compute an organization owns and the people who want to use it, and takes on the infrastructure
work in between. Teams build and consume AI with the tools they already use, inside the boundaries IT sets, without each
team operating clusters, networking, and credentials for itself.

The platform does four things to make that work, and each draws on several of its parts together rather than on a single
component.

## Enable

Compute is the first thing teams need and the hardest for them to get on their own. Klique turns what the organization
already owns into a service they draw from: Kubernetes clusters, Slurm and PBS schedulers, virtual machines, bare metal,
cloud instances, and cloud AI APIs all come under [one set of controls](governed_compute.md). A builder submits work to a
queue and receives the compute their group's policy allows, with no cluster to configure and no ticket to file.

That covers interactive work as much as batch: SSH, JupyterLab, VS Code, and remote desktop sessions run on the same
governed compute with workspaces that persist, and agents get scoped identities instead of borrowed credentials. What
runs there becomes reachable because the [AI Application Gateway](appgw.md) gives each instance an authenticated route,
so a job turns into a working URL rather than something stranded inside a cluster.

## Control

Self-serve access depends on boundaries that hold everywhere, so policy is defined once and enforced on every request
rather than configured per team or per application. The [control plane](klique_server.md) is the authority: every other
part of the platform reads its configuration from there and reports state back, so a rule set once applies wherever work
lands.

Identity and access run through it too. Role-based access rules cover users, service accounts, and groups across
compute, projects, and model endpoints, and applications behind the gateway are authenticated at the gateway rather than
each implementing its own login. Teams can share one tenant, or be separated into isolated tenants where a deployment
calls for it; the [Platform Management Center](management_center.md) administers deployments that take the second route.

## Publish

Publishing is part of the platform rather than something each team arranges for itself. A running instance is given a
static route by the [AI Application Gateway](appgw.md) and published as a URL Application: a tile in the
[Applications](../webapp/applications/apps_overview.md) catalog that opens it, reachable by exactly the users whose
permissions cover that category.

Models follow the same pattern through the [LLM Gateway](llm_gateway.md), which puts every model behind one entry point
with its own routing, access rules, and quotas, so adding or swapping a model changes what every connected application
can reach without touching application code. Where a deployment runs several tenants, the Platform Management Center
rolls applications out across them with per-tenant availability.

## Measure

Governing consumption requires measuring it, so the platform meters its own use.

The [Admin Dashboard](../webapp/webapp_admin_dashboard.md) covers running tasks and app instances, metered usage and
spend, and LLM token consumption, while the
[Orchestration Dashboard](../webapp/webapp_orchestration_dash.md) covers compute availability and utilization. Where a
deployment is divided into tenants, the Platform Management Center aggregates compute utilization, hardware inventory,
and metered cost across all of them.

The numbers come from the control plane, which aggregates usage events for compute, storage, tokens, and users into
[metering data](../governance_engine/usage_metering_billing.md) and exposes orchestration metrics over OpenTelemetry, so
platform activity can be monitored alongside an existing observability stack.

## In this section

* [Control Plane](klique_server.md) - The Klique Server, the authority for platform state and configuration, and how
  people, code, and agents reach it.
* [Governed Compute](governed_compute.md) - Aggregating compute fabrics into pools and applying resource policy to
  them for self-serve access.
* [AI Application Gateway](appgw.md) - Authenticated, RBAC-scoped routing that makes workloads reachable and publishes
  them to the organization.
* [LLM Gateway](llm_gateway.md) - One entry point for LLM traffic, with model routing, governed access, and token
  quotas.
* [Platform Management Center](management_center.md) - Administering a deployment divided into isolated tenants, with
  platform-wide cost and utilization.

![Klique Platform](../img/klique_components_diagram_2.png#light-mode-only)
![Klique Platform](../img/klique_components_diagram_2_dark.png#dark-mode-only)
