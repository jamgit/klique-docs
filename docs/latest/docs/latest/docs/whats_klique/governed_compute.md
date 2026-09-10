# Governed Compute

> Klique bringing Kubernetes, HPC, VM, bare metal, and cloud fabrics under one set of controls, with resource pools, policies, and self-serve access.

Klique brings the compute an organization already owns under one set of controls, whatever fabric it runs on. Teams
reach it through queues and receive what their group's policy allows, without operating the infrastructure underneath.

## Many fabrics, one way in

Klique manages compute across Kubernetes clusters, HPC schedulers such as Slurm and PBS, virtual machines, bare metal,
cloud instances, and cloud AI API endpoints. It is hardware and environment agnostic: any accelerator, across
on-premises including air-gapped, cloud, hybrid, and multi-cloud environments.

Klique works alongside each fabric rather than replacing it. Each supported fabric has its own orchestrator
implementation that integrates with the fabric's native scheduling, so jobs land on the right compute and the existing
cluster keeps its own scheduler. What a team sees is the same either way: a queue to submit to. Which fabric serves that
queue is a matter of how administrators configure the resources behind it.

## Policy that holds wherever work lands

Administrators organize compute into **resource pools** and expose them through execution queues. Each queue is tied to
a resource profile defining how much compute a job enqueued through it receives, and a profile can list several pools in
routing priority order, so work spills over to the next pool when a preferred one is full. When local capacity runs out
entirely, [cloud autoscaling](../orchestration_engine/cloud_autoscaling.md) provisions matching cloud instances against
a budget and shuts them down when they go idle.

Resource policies sit above that routing and govern user groups rather than individual pools. Each policy gives a group
a **reservation**, the compute guaranteed to be available to it, and a **limit**, the maximum its jobs can use
concurrently. A group's entitlement therefore holds whichever pool or fabric ends up running its work.

Utilization is improved on the compute already available rather than by adding hardware:

- **[Fractional GPUs](../orchestration_engine/fractional_gpus.md)** and
[Dynamic GPU Allocation](https://clear.ml/docs/latest/docs/clearml_agent/clearml_agent_dynamic_gpus): share one
physical GPU across isolated workloads, and vary how many GPUs a queue draws.
- **[Multi-Node Scheduling](../orchestrator/multi_node_training.md)**: run a single job across multiple nodes.
- **[Services Mode](https://clear.ml/docs/latest/docs/clearml_agent/clearml_agent_services_mode/)**: run several
resource-light tasks in parallel rather than one at a time, for services such as cleanup jobs and pipeline
controllers.

For the dashboards and settings behind policies, pools, and profiles, see
[Resource Management](../orchestration_engine/resource_policies.md) and
[Resource Governance](../governance_engine/resource_governance.md).

## Compute without operating it

A builder submits work to a named queue and receives the resources its profile defines, within the policy that governs
their group. There is no cluster to configure and no ticket to file, and non-administrator users can see the policies
that apply to them, including current usage, reservations, limits, and the queues available to them.

The same access covers interactive work: [remote development environments](../orchestration_engine/gpuaas_remote_ides.md)
run SSH, JupyterLab, VS Code, and full desktop sessions on governed compute, with workspaces that persist across
sessions. Agents receive scoped identities of their own rather than borrowed credentials.

## Reaching what runs

Compute alone does not make a workload usable. The [AI Application Gateway](appgw.md) gives each workload Klique places
an authenticated, SSL-secured route scoped by Klique RBAC, which is what turns a running job into something a person or
another service can reach.

## Adding compute

Compute is brought under Klique by installing the Klique Orchestrator on it, which also covers how jobs are placed and
executed once they land. See [Klique Orchestrator](../orchestrator.md) and its per-fabric installation pages:

- [Bare Metal and Virtual Machines](../orchestrator/deployment_bare_metal.md)
- [Kubernetes](../orchestrator/deployment_k8s.md)
- [Slurm](../orchestrator/deployment_slurm.md), and the rest of the [HPC](../orchestration_engine/hpc.md) options
