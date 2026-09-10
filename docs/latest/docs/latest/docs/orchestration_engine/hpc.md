# HPC

> Klique's HPC integration with Slurm and PBS schedulers through the Klique Orchestrator, and running Slurm as an elastic Kubernetes service.

High-performance computing (HPC) clusters are commonly managed with workload schedulers such as
[Slurm](https://slurm.schedmd.com/) and PBS. Klique integrates with both through the Klique Orchestrator, allowing HPC workloads to be
submitted, scheduled, and tracked using the same queues, experiment tracking, and resource controls as the rest of the
platform, without changing how jobs are written.

## Scheduler integration

The Klique Orchestrator schedules tasks on Linux clusters managed by **Slurm** or **PBS**. It maps Klique queues to the
scheduler's batch script templates: each queue is associated with a template, and when a task is pushed to the queue,
the orchestrator converts it into a scheduler job and submits it using that template (`sbatch` on Slurm, `qsub` on PBS).
The template starts the Klique Executor on the allocated node, which reproduces the task's environment and runs it.

The orchestrator glue is installed once, on a machine that can run the scheduler's client commands (typically the login
node). It does not need to be installed on every node.

### Slurm

On Slurm, the Slurm Glue maps queues to `sbatch` templates, and jobs can run directly on the host or inside
Singularity or Pyxis containers. For Slurm deployment and configuration instructions, see:

- [Slurm (Native)](../orchestrator/deployment_slurm.md)
- [Slurm with Singularity](../orchestrator/deployment_slurm_singularity.md)
- [Slurm with Pyxis](../orchestrator/deployment_slurm_pyxis.md)
- [User Impersonation](../orchestrator/slurm_impersonation.md)

### PBS

On PBS, the orchestrator maps queues to PBS job templates and submits jobs with `qsub`, following the same queue and
template model as Slurm.

## What Klique adds on top of Slurm

Slurm schedules and runs jobs; however, it provides limited visibility and automation. Klique adds a management layer
around it:

- **Queue visibility and control**:  View the contents of each queue, and reorder, stop, or remove jobs before they run.
- **Environment provisioning**: Each job is launched with its configured execution environment, including
  data connections, drivers, and container images, so users do not configure these manually per job.
- **Experiment tracking**: Logs, metrics, resource utilization, and artifacts are captured for every job. Compare runs using Klique's experiment tracking and visualization tools. 
- **Automation without shell scripts**: Define pipelines and automations through the platform instead of batch
  scripts.
- **Resource limits**: Jobs are held to their allocated memory rather than being terminated without notice when a quota is
  exceeded.
- **GPU sharing**: Multiple jobs can run on the same GPU using [Fractional GPUs](fractional_gpus.md).
- **Self-serve access**: Users submit work through Klique queues instead of needing direct cluster access and scripting
  knowledge.

## Running Slurm on Kubernetes

Klique can also run Slurm as an elastic service inside Kubernetes rather than as a separate standalone cluster. The Slurm
components run as Kubernetes pods: a login node pod exposes the standard Slurm interfaces (`sbatch`, `srun`), and worker
pods scale up and down with demand. Klique manages pod-to-pod networking, including full bidirectional connectivity between
nodes for MPI and other multi-node HPC workloads.

Existing `sbatch` scripts and job semantics continue to work unchanged. Running Slurm this way adds:

- **Autoscaling** of worker capacity, so nodes are provisioned on demand instead of staying permanently allocated.
- **Cross-cluster scheduling**, routing jobs to Slurm or Kubernetes based on priority, GPU type, or cost.
- **Hybrid spillover** from on-premises capacity to the cloud.
- **[Multi-tenancy](multi_tenancy.md)** with per-tenant quotas and billing, so teams share the same physical infrastructure
  while staying isolated.
