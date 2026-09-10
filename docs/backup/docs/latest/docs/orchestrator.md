# Klique Orchestrator

> The Orchestrator and Executor roles, how they map onto each compute fabric, their scheduling and execution behavior, and installation per fabric.

The Klique Orchestrator runs the platform's workloads on governed compute. It monitors queues, places each job on the
compute it manages, and reports system metrics back to the Klique Server, viewable on the
[Orchestration](webapp/webapp_workers_queues.md) page. The Klique Executor then reproduces the job's environment and
runs it, streaming results and logs back to the server.

Klique makes use of the [ClearML Agent](https://clear.ml/docs/latest/docs/clearml_agent/) for both roles. How the two
map onto running processes depends on the fabric:

* On Kubernetes and other orchestrated fabrics, a dedicated orchestrator schedules queued jobs onto the cluster, and
  the agent inside each task pod executes them.
* On virtual machines and bare metal, a single agent fills both roles, pulling from its queues and running each job
  on the machine it occupies.

For how the orchestrator fits the rest of the platform, see [Governed Compute](whats_klique/governed_compute.md).

## Scheduling

The orchestrator monitors its queues and places each queued job onto available compute. One orchestrator services one or
more queues. Each supported fabric has its own orchestrator implementation that integrates with the fabric's native
scheduling, so jobs land on the right compute; the installation pages below cover the implementation for each fabric.

As it places work, the orchestrator also makes shared compute more efficient:

* **Fractional GPUs** and
  [Dynamic GPU Allocation](https://clear.ml/docs/latest/docs/clearml_agent/clearml_agent_dynamic_gpus): share a
  physical GPU across workloads, and vary how many GPUs a queue draws. See
  [Fractional GPUs](orchestration_engine/fractional_gpus.md).
* **[Multi-Node Scheduling](orchestrator/multi_node_training.md)**: run a single job across multiple nodes.
* **Services Mode**: an agent in services mode moves on to the next task as soon as one is launched, instead of
  waiting for it to finish, suiting resource-light, long-running tasks such as cleanup jobs or pipeline controllers.
  See [Services Mode](https://clear.ml/docs/latest/docs/clearml_agent/clearml_agent_services_mode/) in the ClearML
  documentation.

## Execution

The Klique Executor reproduces the task's run on the compute the orchestrator selected: it clones the code, applies any
uncommitted changes, restores the Python environment and packages, and runs the workload, streaming results and logs
back to the Klique Server.

* **Execution Environments**: the environment is reproduced in Virtual Environment Mode, installing packages with
  `pip`, `conda`, `uv`, or `poetry`, or in Docker Mode, running the task inside a container. See
  [Execution Environments](https://clear.ml/docs/latest/docs/clearml_agent/clearml_agent_execution_env/) in the
  ClearML documentation.
* **Caching**: virtual environments are cached so repeated tasks with the same dependencies reuse an existing
  environment instead of reinstalling packages, and data caches let tasks start faster. See
  [Python Virtual Environments](https://clear.ml/docs/latest/docs/clearml_agent/clearml_agent_env_caching/) in the
  ClearML documentation and [Data Caching](orchestrator/data_caching.md).
* **Bootstrap**: a self-contained initialization system that installs Python, Git, and the agent itself into
  containers that lack them, reducing task cold-start time. See
  [ClearML Agent Bootstrap](https://clear.ml/docs/latest/docs/clearml_agent/clearml_agent_bootstrap/) in the
  ClearML documentation.
* **NVCR Access**: configuring the Docker infrastructure with NGC credentials so it can pull NVIDIA-provided
  containers from `nvcr.io`. See
  [NVCR Access](https://clear.ml/docs/latest/docs/clearml_agent/clearml_agent_nvcr/) in the ClearML documentation.

## Deployment

The orchestrator runs on bare metal, virtual machines, cloud instances, Kubernetes, and HPC schedulers such as Slurm and
PBS. Because the Klique install differs from the open-source ClearML Agent, installation is documented here:

* [Bare Metal and Virtual Machines](orchestrator/deployment_bare_metal.md)
* [Kubernetes](orchestrator/deployment_k8s.md)
* [Slurm](orchestrator/deployment_slurm.md), and the rest of the [HPC](orchestration_engine/hpc.md) options
