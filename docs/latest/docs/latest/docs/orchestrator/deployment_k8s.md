# Kubernetes

> Deploying the Kubernetes-Glue as Klique's Orchestrator via the `orchestrator` Helm chart, including installation and workload customization.

On Kubernetes, the Kubernetes-Glue is Klique's Orchestrator: a controller pod that adds missing scheduling
capabilities to Kubernetes, mapping queued jobs directly to Kubernetes jobs so they land on the right compute. The
Klique Executor then runs inside each job pod as a `clearml-agent` process, reproducing the task's environment and
running it.

## How It Works
The orchestrator performs the following:
- Pulls jobs from the Klique execution queue.
- Prepares a Kubernetes job based on a provided YAML template.

Inside each job pod, the Klique Executor (the `clearml-agent` process):
- Installs the required environment for the task.
- Executes and monitors the task process.
- Logs task data to the Klique Server
   
## Deployment Options
You can deploy the orchestrator onto Kubernetes using one of the following methods:

* **Kubernetes-Glue Script**:
  Run a [Kubernetes-Glue script](https://github.com/clearml/clearml-agent/blob/master/examples/k8s_glue_example.py) on a Kubernetes CPU node. This approach is less scalable and typically suited for simpler use cases.

* **Orchestrator Helm Chart (Recommended)**:
  Use the `orchestrator` Helm chart to spin up the Kubernetes-Glue as a controller pod. This is the recommended and 
  scalable approach. See more details below. 

## Orchestrator Helm Chart
The Kubernetes-Glue is installed on Kubernetes using a Helm chart. This sets up a controller pod that listens to Klique queues and launches jobs as needed.

### Installing the Orchestrator

Klique adds advanced Kubernetes features, such as:
- **Multi-Queue Support**: Service multiple Klique queues within the same Kubernetes cluster.
- **Pod-Specific Templates**: Define resource configurations per queue using pod templates.

#### Prerequisites

- A running [Klique Server](../deploying_klique/k8s.md)
- API credentials (`<ACCESS_KEY>` and `<SECRET_KEY>`) generated via 
  the Klique UI (**Settings > Workspace > API Credentials > Create new credentials**). For more information, see [API Credentials](../webapp/settings/webapp_settings_profile.md#api-credentials). 

  :::note
  Make sure these credentials belong to an admin user or a service account with admin privileges.
  :::
 
- The worker environment must be able to access the Klique Server over the same network.
- A DockerHub token to access the Klique OCI Helm charts and Docker images
- To support **GPU** queues, you must deploy the **NVIDIA GPU Operator** on your Kubernetes cluster. For more information, see [GPU Operator](fractional_gpus/gpu_operator.md).

#### Installation

1. Log into the Klique OCI Registry:

   ```bash
   echo <DOCKERHUB_TOKEN> | helm registry login docker.io --username kliqueenterprise --password-stdin
   ```

1. Create an `orchestrator-values.override.yaml` file with the following content:

   :::note
   Replace the `<ACCESS_KEY>` and `<SECRET_KEY>` with the API credentials you generated earlier. 
   Set the `<api|file|web>ServerUrlReference` fields to match your Klique 
   Server URLs.
   :::

   ```yaml
   imageCredentials:
     password: "<DOCKERHUB_TOKEN>"
   controlPlane:
     apiKey: "<ACCESS_KEY>"
     apiSecret: "<SECRET_KEY>"
   orchestratork8sglue:
     apiServerUrlReference: "<API_SERVER_URL>"
     fileServerUrlReference: "<FILE_SERVER_URL>"
     webServerUrlReference: "<WEB_SERVER_URL>"
     createQueues: true
     queues:
       exampleQueue:
         templateOverrides: {}
         queueSettings: {}
   ```

1. Install the `orchestrator` Helm chart:

   ```bash
   helm upgrade -i -n <WORKER_NAMESPACE> orchestrator oci://docker.io/kliqueai/orchestrator --create-namespace -f orchestrator-values.override.yaml
   ```

#### Bootstrap
ClearML Agent Bootstrap can be enabled to allow tasks to run in minimal container images without pre-installed 
dependencies. For details, see [ClearML Agent Bootstrap](https://clear.ml/docs/latest/docs/clearml_agent/clearml_agent_bootstrap/#kubernetes). 

#### Workload Customization

The orchestrator monitors [Klique queues](https://clear.ml/docs/latest/docs/fundamentals/agents_and_queues/) for tasks that are scheduled for execution.

Klique supports specifying custom definitions for individual queues for fine-grained control of workload parameters; you 
can set Kubernetes overrides such as pod resources and labels, as well as runtime definitions like environment variables, 
container images, or worker ID formats.

For more information, see [Custom Workload Configuration](custom_workload.md). 

#### Orchestrator Configuration for the Orchestration Dashboard

The orchestrator can be configured to report resource availability and control how workers appear in the [Orchestration Dashboard](../webapp/webapp_orchestration_dash.md).

For configuration details, see [Orchestration Dashboard Customization](orch_dash_k8s.md).

#### Additional Configuration Options

To view available configuration options for the Helm chart, run the following command:

```bash
helm show readme oci://docker.io/kliqueai/orchestrator
# or
helm show values oci://docker.io/kliqueai/orchestrator
```
