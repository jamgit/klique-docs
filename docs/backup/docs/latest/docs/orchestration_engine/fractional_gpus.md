# Fractional GPUs

> Klique's approaches to fractional GPU allocation, including static and dynamic MIG, container-level memory limits, and AMD GPU partitioning.

Some workloads need only a small amount of GPU compute and memory, yet reserving a whole GPU for each of them wastes
capacity. Fractional GPUs let a single physical GPU run multiple isolated workloads at once, each confined to the slice
of GPU memory allocated to it, so one device serves many jobs without them interfering with one another. This raises
effective capacity and lets compute be allocated at a finer grain, which matters most for inference,
interactive development, and lightweight jobs that would otherwise waste a full accelerator.

Klique supports several approaches to GPU partitioning across both NVIDIA and AMD hardware. Which one fits depends on
your GPUs and on whether you want a fixed layout or on-demand slicing.

![Fractional GPU diagram](../img/fractional_gpu_diagram.png#light-mode-only)
![Fractional GPU diagram](../img/fractional_gpu_diagram_dark.png#dark-mode-only)

## Static MIG

NVIDIA [MIG (Multi-Instance GPU)](https://www.nvidia.com/en-us/technologies/multi-instance-gpu/) can be configured with
static slices, and Klique schedules workloads onto the MIG profile they request. Each queue is associated with a MIG
slice, and jobs enqueued through it run on that slice. The partition layout is fixed up front, so workloads are never
evicted to repartition the GPU. Static MIG suits stable, well-understood workloads where a fixed partitioning of the GPU
is enough. The slices are set up through the NVIDIA device plugin; see
[K8s GPU Operator](../orchestrator/fractional_gpus/gpu_operator.md).

## Dynamic MIG (CDMO)

On MIG-capable NVIDIA GPUs, Klique can also partition the GPU dynamically, on demand per workload, rather than fixing the
layout ahead of time. As jobs are scheduled, MIG profiles are created and reclaimed to match what the queue actually
needs, so the GPU is repartitioned as demand changes. Because a GPU's MIG geometry can only be changed while the device
is free, this repartitioning is done by evicting the workloads currently running on the GPU, reconfiguring the MIG
partitions, and then rescheduling work onto the new layout. This is unlike static MIG, where the layout is fixed and
workloads are never evicted to repartition. On Kubernetes it is handled by the
[Cluster Dynamic MIG Operator (CDMO)](../orchestrator/fractional_gpus/cdmo.md).

## Dynamic container-level memory limits

Klique can share any GPU across workloads by enforcing a GPU-memory limit per container, whether or not the GPU supports
MIG. Each workload runs in a container capped to its allocated memory and cannot exceed it, so several isolated workloads
run side by side on the same GPU without one starving the others. Memory is hard-capped per container, while the GPU's
compute is shared across the containers through time slicing. Limits are applied dynamically per task, with no
pre-partitioned hardware required. On Kubernetes this is provided by the
[Cluster Fractional GPU Injector (CFGI)](../orchestrator/fractional_gpus/cfgi.md); on
[bare metal or VMs](../orchestrator/deployment_bare_metal.md) the agent applies the container memory limit
directly.

## AMD GPU partitioning

Klique also supports partitioning AMD Instinct GPUs based on the CDNA 3 and CDNA 4 architectures, such as the MI300X and
MI355X, into smaller independent GPU instances. AMD exposes two dimensions of partitioning:

- **Compute partitioning** splits the GPU's accelerator dies into 1, 2, 4, or 8 partitions (the SPX, DPX, QPX, and CPX
  modes), each behaving as an independent virtual GPU that runs its own workload.
- **Memory partitioning** presents the high-bandwidth memory either as a single uniform space (NPS1) or as four NUMA
  domains aligned to the compute partitions (NPS4) for better memory locality.

The partition layout is set at the hardware level, and Klique allocates the resulting partitions to workloads through
its agent.
