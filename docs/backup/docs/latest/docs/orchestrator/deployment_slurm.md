# Slurm (Native)

> Installing the Slurm Glue orchestrator, creating batch templates with dynamic variables, and launching it against Klique queues.

Klique runs tasks on Linux clusters managed by Slurm. The Slurm Glue is Klique's Orchestrator for Slurm: associate a
Klique queue to a batch script template, and when a Task is pushed into the queue, the orchestrator converts it into
an `sbatch` job and submits it according to the template attached to that queue. The template's
`${CLEARML_AGENT_EXECUTE}` starts the Klique Executor on the allocated node, which reproduces the task's environment
and runs it.

:::note
The orchestrator can also run within **Singularity** or **Pyxis** containers in Linux clusters managed with Slurm. See 
[Slurm with Singularity](deployment_slurm_singularity.md) and [Slurm with Pyxis](deployment_slurm_pyxis.md) for details.
:::

## 1. Install the Slurm Glue

Install the Slurm Glue on a machine where you can run Slurm commands such as `sbatch` and `squeue` (typically the login node).
One instance is sufficient for the entire cluster; there is no need to install it on every node.

```
pip3 install -U --extra-index-url https://*****@*****.allegro.ai/repository/clearml_agent_slurm/simple clearml-agent-slurm
```

  
:::tip[Python repository credentials]
Your credentials for `--extra-index-url` are available in the WebApp under the **Help** menu   **>** 
**Enterprise Packages** **>** **Klique HPC daemon**.
:::

## 2. Create a Batch Template

Create a batch template. Make sure to set the `SBATCH` variables to the resources you want to attach to the queue. 
For example, the script below sets up an agent to run on a bare-metal setup, creating a virtual environment per job:

```
#!/bin/bash
# example
#SBATCH --job-name=clearml_task_${CLEARML_TASK.id}       # Job name DO NOT CHANGE
#SBATCH --ntasks=1                    # Run on a single CPU
# #SBATCH --mem=1mb                   # Job memory request
# #SBATCH --time=00:05:00             # Time limit hrs:min:sec
#SBATCH --output=task-${CLEARML_TASK.id}-%j.log
#SBATCH --partition debug
#SBATCH --cpus-per-task=1
#SBATCH --priority=5
#SBATCH --nodes=${CLEARML_TASK.hyperparams.properties.num_nodes.value:1}
${CLEARML_PRE_SETUP}
# control how multi node resource monitoring is reported
export CLEARML_MULTI_NODE_SINGLE_TASK=1

echo whoami $(whoami)

${CLEARML_AGENT_EXECUTE}

${CLEARML_POST_SETUP}
```

### Dynamic Template Variables

Use dynamic variables to control the values passed to a Slurm job through its runtime parameters. 

Dynamic variable assignment supports specifying a default value (separated by `:`) in case the task being run does not 
have the specified value.

You can use the following parameters:
* Job information
  * `${CLEARML_QUEUE_NAME}`
  * `${CLEARML_QUEUE_ID}`
  * `${CLEARML_WORKER_ID}`
* Task parameters
  * `${CLEARML_TASK.id}`
  * `${CLEARML_TASK.name}`
  * `${CLEARML_TASK.project}`
  * `${CLEARML_TASK.hyperparams.properties.user_key.value}`
  * `${CLEARML_TASK.container.image}`
  * `${CLEARML_TASK.container.arguments}`

For example, in the above template, the following command was used to set the nodes value to be the ClearML Task's `num_nodes` 
user property (defaulting to `1` if the task has no such parameter):

```
#SBATCH --nodes=${CLEARML_TASK.hyperparams.properties.num_nodes.value:1}
```

The user property can be set in the UI, in the task's **CONFIGURATION > User Properties** section, and when the task is 
executed that value will be used.

### Controlling Klique Orchestrator Behavior
To customize the Klique Orchestrator's behavior for a specific setup, set any [ClearML Agent environment variables](https://clear.ml/docs/latest/docs/clearml_agent/clearml_agent_env_var) 
in the template file before the execution command (`${CLEARML_AGENT_EXECUTE}`).

For example, in the above template, the `CLEARML_MULTI_NODE_SINGLE_TASK` variable was set:

```
export CLEARML_MULTI_NODE_SINGLE_TASK=1
```

## 3. Launch the Slurm Glue

Launch the Slurm Glue and assign the Slurm configuration to a Klique queue. For example, the following 
associates the `default` queue to the `slurm.example.template` script, so any jobs pushed to this queue will use the 
resources set by that script.  

```commandline
clearml-agent-slurm --template-files slurm.example.template --queue default
```
   
You can also pass multiple templates and queues. For example: the following associates the `queue1` queue to the 
`slurm.example.template1` script and `queue2` queue to the `slurm.example.template2` script:

```commandline
clearml-agent-slurm --template-files slurm.template1 slurm.template2 --queue queue1 queue2
```
   
:::tip[Debug mode]
To enable debug logging for the Slurm Glue, set the `CLEARML_SLURM_GLUE_DEBUG=1` environment variable 
before launching.
:::

## User Impersonation

By default, Slurm jobs are submitted as the Linux user running the orchestrator process.

You can configure the orchestrator to submit jobs as different Linux users based on the Klique user who enqueued the task. 
See [Slurm User Impersonation](slurm_impersonation.md).
