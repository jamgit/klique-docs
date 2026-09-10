# K3k Deployment

> A K3s Kubernetes cluster with autoscaling worker nodes, kubeconfig access, and SSH login to the control plane.

The K3k Deployment application enables users to provision and manage a fully functional K3s Kubernetes cluster. 
The cluster consists of a server node acting as the control plane and one or more worker nodes, all orchestrated through 
Klique queues. The cluster can be deployed inside an existing Kubernetes environment or on standalone compute such as
cloud instances.

Once an app instance is running, users interact with the cluster using standard Kubernetes tooling, such as `kubectl` 
and Helm charts. Klique manages the infrastructure; users manage their workloads. 

The cluster environment can be customized at launch with additional server and worker arguments. When autoscaling is 
enabled, worker nodes automatically scale up or down based on configurable resource utilization thresholds. 

Once you start a K3k Deployment instance, you can view the following information in its dashboard:

* Status indicator
  *  - App instance is running and cluster is available  
  *  - App instance is setting up
  *  - App instance is idle
  *  - App instance is stopped
* Idle time – Time elapsed since last activity  
* Login Node IP – The IP address of the server node  
* Server Task – Task ID of the server node and a link to its Console Log  
* Workers – Number of worker nodes  
* SSH connection details  
  * SSH Host:Port  
  * SSH Login user name  
  * SSH Login password  
  * SSH Connect Command – The full command line to initiate an SSH connection to the login node  
* Kubeconfig – The kubeconfig file for the cluster. Click to select all, then copy the contents and save them to a file. You can then use it with commands such as: `kubectl --kubeconfig=<file> get nodes`  
* Traefik Ingress URL - Base URL for services exposed via Traefik IngressRoute on the cluster  
* Node Summary - Per-node breakdown of rank, task ID, and current status  
* Node Status - Count of worker nodes by current state: Pending, Running, and Failed  
* Resource utilization plots over time for all nodes:  
  * CPU Compute Utilization  
  * GPU Compute Utilization  
  * CPU Memory Utilization  
  * GPU Memory Utilization  
* Console Log - The app instance's console output: setup progress, status changes, error messages, etc.

:::tip[Embedding Visualizations]
You can embed plots from the app instance dashboard into [Reports](../webapp_reports.md). Klique 
also supports embedding resources in third-party platforms that support embedded content (e.g. Notion). These visualizations 
are updated live as the app instance(s) updates. Hover over the plot and click  
to copy the embed code, and navigate to a report to paste the embed code.
:::

## K3k Deployment Instance Configuration

When configuring a new K3k Deployment instance, you can fill in the required parameters or reuse the 
configuration of a previously launched instance. 

Launch an app instance with the configuration of a previously launched instance using one of the following options:
* Cloning a previously launched app instance will open the instance launch form with the original instance's 
configuration prefilled.
* Importing an app configuration file. You can export the configuration of a previously launched instance as a JSON file 
when viewing its configuration.

The prefilled configuration form can be edited before launching the new app instance.

To configure a new app instance, click `Launch New`  
to open the app's configuration form.

### Configuration Options 

:::note
Administrators can [customize](../../deploying_klique/app_launch_form_custom) the launch form and 
modify field names and/or available options and defaults.

This section describes the default configuration provided by Klique.  
:::

* **Import Configuration** - Import an app instance configuration file. This will fill the instance launch form with the values from the file, which can be modified before launching the app instance.   
* **Instance Name** - Name for the app instance. This will appear in the instance list. If not set, defaults to `K3K Deployment_<timestamp>`.   
* **Service Project - Access Control** - The project where the app instance is created. App instance network access is determined by project-level permissions (i.e. users with read access can access app endpoints).   
* **Server**  
  * **Queue** - The [Queue](https://clear.ml/docs/latest/docs/fundamentals/agents_and_queues/#what-is-a-queue) serviced by the Klique Orchestrator that will run the server node.    
  * **Extra Server Args** - Additional arguments appended to the server startup command (e.g. `--disable=metrics-server`).   
* **Workers**  
  * **Queue** - The [Queue](https://clear.ml/docs/latest/docs/fundamentals/agents_and_queues/#what-is-a-queue) serviced by the Klique Orchestrator that will run the worker nodes.   
  * **Number of Workers** - Number of worker nodes to deploy.  
  * **Extra Worker Args** - Additional arguments appended to the worker startup command.   
* **SSH Access**   
  * **SSH Password** - Password for SSH access to the cluster nodes. If not set, a password is automatically generated   
  * **SSH Public Key** - Public key for SSH access to the cluster nodes.   
* **Idle Options**  
  * **Idle Time Limit (Hours)** - Maximum idle time before the cluster is automatically shut down.  
* **Autoscaling**  
  * **Enable Autoscaling** - Enable automatic scaling of worker nodes based on resource utilization thresholds.  
  * **Minimum Workers** - Minimum number of worker nodes to maintain at all times.  
  * **Maximum Workers** - Maximum number of worker nodes allowed when scaling up.  
  * **Scale-Up Step** - Number of worker nodes to add in each scale-up event.  
  * **Scale-Up Timeout (seconds)** - Time to wait before triggering a scale-up event after thresholds are exceeded.  
  * **Scale-Down Timeout (seconds)** - Time to wait before triggering a scale-down event after thresholds drop below minimum.  
  * **CPU Max %** - CPU utilization upper threshold to trigger scale-up.  
  * **CPU Min %** - CPU utilization lower threshold to trigger scale-down.  
  * **RAM Max %** - RAM utilization upper threshold to trigger scale-up.  
  * **RAM Min %** - RAM utilization lower threshold to trigger scale-down.  
  * **GPU Max %** - GPU utilization upper threshold to trigger scale-up.  
  * **GPU Min %** - GPU utilization lower threshold to trigger scale-down.  
  * **VRAM Max %** - VRAM utilization upper threshold to trigger scale-up.  
  * **VRAM Min %** - VRAM utilization lower threshold to trigger scale-down.  
  * **Disk Read Max (MB/s)** - Disk read speed upper threshold to trigger scale-up.  
  * **Disk Read Min (MB/s)** - Disk read speed lower threshold to trigger scale-down.  
  * **Disk Write Max (MB/s)** - Disk write speed upper threshold to trigger scale-up.  
  * **Disk Write Min (MB/s)** - Disk write speed lower threshold to trigger scale-down.  
* **Advanced**  
  * **Full Snapshot** - Snapshot the entire cluster directory including container images and layers on shutdown. Restores faster but produces larger snapshots. If unchecked, only server state (etcd, certs) is saved — pods will re-pull images on restore.  
  * **Restore Cluster State** - Select a previous k8s Deployment instance. All Kubernetes resources (pods, deployments, services, secrets, etc.) will be restored.  
  * **Cluster Token** - Shared secret used by all nodes to join the cluster. Leave blank to auto-generate a secure random token.  
* **Export Configuration** – Export the current configuration as a JSON file for later reuse
