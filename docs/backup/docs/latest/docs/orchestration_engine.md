# Orchestration Engine

> The Orchestration Engine, which connects compute resources, defines tenants, and enforces policies for self-serve access.

The Orchestration Engine turns the compute you already own into a managed service. You connect your GPUs, CPUs, and cloud
AI APIs once, and every team gets self-serve access to the compute they are assigned, whether for a remote development
environment, a multi-GPU training run, a data processing job, or a live model deployment. Engineering, research, and
business teams all draw from the same governed compute.

It runs as a single control plane on top of your existing infrastructure. It works alongside Kubernetes but does not
require it, and it is hardware and environment agnostic: any accelerator (GPU, CPU, or other silicon) across on-premises
(including air-gapped), cloud, hybrid, and multi-cloud environments.

The Orchestration Engine is one of Klique's three engines, alongside the
[Governance Engine](../governance_engine/governance_engine.md), which unifies security, policy, and identity, and the
[AI Engine](../ai_engine/ai_engine.md), which puts every model behind a single gateway.

## How it works

1. **Connect resources** - Register your GPU clusters, cloud accounts, on-premises machines, and cloud AI API endpoints.
2. **Define tenants** - Partition access into isolated tenants, for example one per team or business unit.
3. **Set policies** - Configure the quotas, priorities, and access rules that determine who can use which resources.

Once connected, the engine routes each workload to appropriate compute, enforces the policies you set at the platform
level, and provides real-time visibility across everything running in your environment. The people requesting compute do
not need to operate the underlying Kubernetes, scheduling, or provisioning.

## Managed, self-serve access

Users and agents request compute through Klique instead of provisioning infrastructure themselves:

* **Builders** get self-serve access to governed compute and persistent [remote development environments](gpuaas_remote_ides.md),
  without configuring Kubernetes or waiting on tickets.
* **Agents** get scoped, non-human identities rather than borrowed credentials.
* **Business teams** consume governed applications and endpoints.

Each consumer sees only the resources and data its role permits. Identity and access rules are defined in the
[Governance Engine](../governance_engine/governance_engine.md) and enforced by the platform.

## Resource management and utilization

The engine schedules workloads and optimizes how the underlying hardware is used, so idle capacity is put to work rather
than reserved and left unused:

* Unified policies govern resource allocation, quota management, and hierarchical prioritization across tenants.
* Fractional GPU allocation lets multiple workloads share a single GPU instead of each reserving a whole device.
* Multi-cloud autoscaling adds and removes cloud capacity based on demand.
* Multi-tenant isolation keeps tenants separate while allowing resources to move between them, with per-tenant cost and
  usage tracking.

## In this section

* [Resource Management](resource_policies.md) - Monitor available compute and control how it is allocated across teams using resource policies, pools, and platform-wide dashboards.
* [Fractional GPUs](fractional_gpus.md) - Share a single GPU across multiple isolated workloads instead of reserving a whole device per job.
* [Cloud Autoscaling](cloud_autoscaling.md) - Automatically add and remove cloud compute based on demand.
* [Multi-Tenancy](multi_tenancy.md) - Securely share one deployment and its compute across isolated tenants, including per-tenant virtual Kubernetes clusters.
* [HPC](hpc.md) - Run high-performance computing workloads on Slurm and PBS clusters through Klique queues and tracking.
* [Inference Optimization](unified_memory_technology.md) - Serve more models per GPU by offloading inactive models to CPU RAM.
