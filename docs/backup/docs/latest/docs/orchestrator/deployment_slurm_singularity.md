# Slurm with Singularity

> Deploying the Slurm Glue orchestrator with Singularity containers, including batch template setup and default container configuration.

The Klique Orchestrator can deploy [`Singularity`](https://docs.sylabs.io/guides/3.5/user-guide/introduction.html) containers in Linux clusters managed by Slurm.

This integration works similarly to the [Native Slurm](deployment_slurm.md) workflow, with the following differences:
* The batch script must prefix the agent execution command (`${CLEARML_AGENT_EXECUTE}`) with the `srun singularity exec` command 
* The orchestrator must be launched with `--singularity-mode`.

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

Additionally, make sure your `sbatch` agent execution command is in the following form:

```commandline
srun singularity exec ${CLEARML_AGENT_EXECUTE}
```

You can also add additional Singularity arguments as needed. For example:

```commandline
srun singularity exec --uts ${CLEARML_AGENT_EXECUTE}
```

Here is a complete example template:

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

echo whoami $(whoami)

srun singularity exec ${CLEARML_AGENT_EXECUTE}

${CLEARML_POST_SETUP}
```

:::note[Template variables]
For details on available template variables, see [Slurm (Native)](deployment_slurm.md#dynamic-template-variables).
:::

## 3. Set the Default Container 

Set the default container to use in the [clearml.conf](https://clear.ml/docs/latest/docs/configs/clearml_conf) file where the orchestrator will be run:
```
agent.default_docker.image="shub://repo/hello-world"
```

Or

```
agent.default_docker.image="docker://ubuntu"
```

## 4. Launch the Slurm Glue
Launch the Slurm Glue and assign the Slurm configuration to a Klique queue. Make sure to add `--singularity-mode` 
to the command line. 

For example, the following associates the `default` queue to the `slurm.example_singularity.template` 
script, so any jobs pushed to this queue will be executed according to the definitions in that script.

```commandline
clearml-agent-slurm --singularity-mode --template-files slurm.example_singularity.template --queue default
```

## User Impersonation

By default, Slurm jobs are submitted as the Linux user running the orchestrator process.

You can configure the orchestrator to submit jobs as different Linux users based on the Klique user who enqueued the task. 
See [Slurm User Impersonation](slurm_impersonation.md).
