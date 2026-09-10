# Multi-Node Scheduling

> Configuring the Klique Orchestrator on Kubernetes to run a single Task across multiple pods and nodes for distributed training.

On Kubernetes, the Klique Orchestrator supports running a single
Task across multiple pods on different Kubernetes nodes.

This is useful for distributed training where the training job needs to span multiple GPUs and potentially multiple nodes.

## Prerequisites

Multi-node scheduling requires Klique Server and the following values set in the ClearML Agent Helm overrides file:

```yaml
orchestratork8sglue:
  # Cluster access is required to run multi-node Tasks
  serviceAccountClusterAccess: true
  multiNode:
    enabled: true
```

## Per-task Multi-Node Configuration
Tasks define their GPU and node requirements through the following [Task user properties](https://clear.ml/docs/latest/docs/fundamentals/hyperparameters#user-properties):

| Property | Required | Allowed Values | Description | Default |
|---|---|---|---|---|
| `multi_node_request` |  | JSON array of integers | GPU requirements (see layouts below) | — |
| `multi_node_request_layout` |  | `gpus_per_node_count`, `gpus_per_node_list` | How to interpret `multi_node_request` | `gpus_per_node_count` |
| `multi_node_type` |  | `physical`, `logical` | Node allocation strategy | `physical` |

* **`multi_node_request`** - An array of integers that defines the GPU resources for the training job.Its values are 
  interpreted according to `multi_node_request_layout` below. This property is required for the task to make use of 
  multi-node scheduling.
* **`multi_node_request_layout`** - Controls how `multi_node_request` is interpreted out of the following options:
  * `gpus_per_node_count` (default) - The array has exactly **two items**: `[gpus_per_node, number_of_nodes]`. 
    For example, `multi_node_request = [2, 6]` means:  2 GPUs per node and 6 nodes, so 12 GPUs in total. Internally 
    expanded to `[2, 2, 2, 2, 2, 2]`.
  * `gpus_per_node_list` - Each value in the array represents the number of GPUs assigned to a specific node. 
    For example:

    ```
    multi_node_request = [2, 2, 4]
    multi_node_request_layout = gpus_per_node_list
    ```

    This means:
    * 3 nodes in total
    * Node 0 gets 2 GPUs
    * Node 1 gets 2 GPUs
    * Node 2 gets 4 GPUs
    
    In total, 8 GPUs.
    To schedule the workload, Klique splits GPU requests into pods, each assigned an equal number of GPUs. The number 
    of GPUs per pod is determined by the greatest common divisor (GCD) of the requested GPU counts. 

    In this example, the GCD of `[2, 2, 4]` is 2, so every pod gets 2 GPUs. 
    * Node 0 → 1 pod (2 GPUs), 
    * Node 1 → 1 pod (2 GPUs)
    * Node 2 → 2 pods (2 GPUs each). 

    In total, 4 pods. 

  Use `gpus_per_node_list` when you need heterogeneous GPU allocation across nodes.

* **`multi_node_type`**- Controls how workloads are placed on physical nodes:
  * `physical` (default) - Ensures workloads are distributed across distinct Kubernetes nodes. Use this when you want 
    exclusive node access.
  * `logical` - Allows multiple workloads to share the same physical node. Use this to maximize cluster utilization when 
    workloads don't need exclusive node access.

### Task Configuration Example

![Multi-node scheduling example](../img/multi_node_scheduling.png#light-mode-only)
![Multi-node scheduling example](../img/multi_node_scheduling_dark.png#dark-mode-only)

## Queue Level Multi-Node Configuration 

Instead of specifying GPU requirements in each specific Task, you can have the orchestrator apply the resource 
configuration based on the queue a task was serviced through so that all Tasks enqueued on that queue will use the same 
multi-node configuration.  

```yaml
orchestratork8sglue:
  serviceAccountClusterAccess: true
  multiNode:
    enabled: true
  createQueues: true
  queues:
    multi-node-queue:
      queueSettings:
        # Defines GPU needs per worker (e.g., 4 GPUs and 2 GPUs). Multiple Pods will be spawned respectively based on the greatest-common-divisor defined.
        multiNode: [4, 2]
      templateOverrides:
        resources:
          limits:
            # Note you will need to use the greatest-common-divisor of the GPUs distribution defined in `queueSettings.multiNode`.
            nvidia.com/gpu: 2
```

The `multiNode` list defines GPU requirements per node, similar to `gpus_per_node_list`. `multiNode: [4, 2]` means:
* Node 0 gets 4 GPUs
* Node 1 gets 2 GPUs

Klique splits each node's GPU requirement into pods using the GCD of the list. The GCD of `[4, 2]` is 2, so every pod 
gets 2 GPUs — enforced by `limits: nvidia.com/gpu: 2`. 

* Node 0 → 2 pod (2 GPUs each), 
* Node 1 → 1 pod (2 GPUs)

In total, 3 pods. 

## Environment Variables

Each pod created by multi-node scheduling has the following environment variables set,
compatible with standard distributed training frameworks (PyTorch DDP, DeepSpeed, etc.):

| Variable | Description |
|---|---|
| `NODE_RANK` | Rank of this pod (0 = master) |
| `WORLD_SIZE` | Total number of GPUs across all pods |
| `MASTER_ADDR` | DNS address of the master pod (via headless Service) |
| `MASTER_PORT` | Communication port on the master pod (default: 29500) |
| `CLEARML_MULTI_NODE_SINGLE_TASK` | Set to `1` to indicate this pod is part of a multi-node Task |

## Example: Multi-Node Training with Hyper-Datasets

See multi-node scheduling in action in the [finetune_qa_lora.py](https://github.com/clearml/clearml/blob/master/examples/hyperdatasets/finetune_qa_lora.py)
example script. This example demonstrates using multi-node distributed training to fine-tune a base LLM with LoRA adapters.
