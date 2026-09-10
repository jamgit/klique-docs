# Klique for HPC

> Native Slurm and PBS support, the login-node orchestrator install, container execution modes, user impersonation, and Slurm on Kubernetes.

Klique supports HPC systems natively, integrating with the schedulers that already manage the cluster rather than
replacing them. Slurm and PBS, including PBS Professional, keep their own allocation and accounting behavior, and
Klique submits work to them through the Klique Orchestrator.

Jobs are written the way they always were. Each Klique queue is associated with a batch script template, and when a
task is pushed to the queue, the orchestrator renders it through that template and submits it with the scheduler's own
client command: `sbatch` on Slurm, `qsub` on PBS. The template starts the Klique Executor on the allocated node, which
reproduces the task's environment and runs it.

## Installation overview

The Klique HPC daemon is installed once, from a Python package, on a machine that can run the scheduler's client
commands, typically the login node. It does not need to be installed on every compute node, and it does not require
changes to the scheduler configuration.

## Slurm

Slurm deployments differ in how each job runs on the allocated node, and in the account it runs under:

- [Slurm (Native)](../orchestrator/deployment_slurm.md) runs the executor directly on the host, and covers installing
  the Slurm Glue and building batch templates with dynamic variables.
- [Slurm with Singularity](../orchestrator/deployment_slurm_singularity.md) runs each job inside a Singularity
  container.
- [Slurm with Pyxis](../orchestrator/deployment_slurm_pyxis.md) runs each job inside an OCI image through Pyxis,
  typically backed by Enroot.
- [Slurm User Impersonation](../orchestrator/slurm_impersonation.md) submits each job as the Linux user mapped to the
  Klique user who enqueued it, instead of as the account running the glue process.

## PBS

On PBS, the orchestrator maps queues to PBS job templates and submits them with `qsub`, following the same queue and
template model as Slurm.

## Capabilities on HPC clusters

Beyond submission, Klique adds queue visibility and reordering, environment provisioning, experiment tracking, and
GPU sharing on top of the scheduler, and can run Slurm itself as an elastic service inside Kubernetes. For what these
provide, see [HPC](../orchestration_engine/hpc.md).
