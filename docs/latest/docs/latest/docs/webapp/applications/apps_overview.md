# Klique Applications

> Klique's no-code Applications catalog, organized by category, with app deployment, deletion, launching, and publishing as URL Applications.

Use Klique Applications to launch, manage, and share AI and machine learning workloads without any coding.

Applications define reusable workloads that can be launched multiple times. Each time you launch an application, a new 
application instance is created. 

Tenant administrators can publish supported running application instances that expose a live endpoint as URL Applications. 
Users can then access the published application directly from the Applications catalog, subject to application category 
permissions.

![Apps page](../../img/apps_overview_page.png#light-mode-only)
![Apps page](../../img/apps_overview_page_dark.png#dark-mode-only)

## Available Applications

Applications are grouped into categories based on their purpose.

### General
Applications for automating and optimizing workflows, and monitoring project performance:
* [**Hyperparameter Optimization**](apps_hpo.md) - Find the parameter values that yield the best performing models
* **Nvidia Clara** - Train models using Nvidia's Clara framework
* [**Project Dashboard**](apps_dashboard.md) - High-level project monitoring with Slack alerts
* [**Task Scheduler**](apps_task_scheduler.md) - Schedule tasks for one-shot and/or periodic execution at specified times
* [**Trigger Manager**](apps_trigger_manager.md) - Define tasks to be run when predefined events occur
 
### AI Dev
Applications for deploying AI development environments on remote machines:
* [**SSH Session**](apps_ssh_session.md) - Launch a full development environment on a remote machine with a 
detached interactive SSH session
* [**Jupyter Lab**](apps_jupyter_lab.md) - Launch a Jupyter Lab session on a remote machine
* [**VS Code**](apps_vscode.md) - Launch a VS Code session on a remote machine
* [**Claude Desktop**](apps_claude_desktop.md) - Launch the Claude Desktop AI assistant in a browser-accessible remote desktop

### Databases
Applications for deploying standalone, detachable vector database sessions:
* [**Milvus DB Session**](apps_milvus.md ) - Launch a detachable [Milvus](https://github.com/milvus-io/milvus) database session
* [**Qdrant DB Session**](apps_qdrant.md) - Launch a detachable [Qdrant](https://github.com/qdrant/qdrant) database session

### UI Dev
Applications for deploying user interfaces for models:
* [**Gradio Launcher**](apps_gradio.md) - Create visual web interfaces for your models with Gradio
* [**Streamlit Launcher**](apps_streamlit.md) - Create visual web interfaces for your models with Streamlit

### Deploy
Applications for deploying machine learning models as scalable, secure services:
* [**Embedding Model Deployment**](apps_embed_model_deployment.md) - Deploy embedding models as networking services over a secure endpoint
* [**vLLM Model Deployment**](apps_model_deployment.md) - Deploy LLMs as networking services over a secure endpoint
* [**Llama.cpp**](apps_llama_deployment.md) - Deploy LLMs in GGUF format using [`llama.cpp`](https://github.com/ggerganov/llama.cpp) as networking services over a secure endpoint
* [**SGLang Model Deployment**](apps_sglang.md) - Deploy LLMs using [SGLang](https://docs.sglang.ai/) as networking services over a secure endpoint
* [**Containerized Application Launcher**](apps_container_launcher.md) - Launch and application containers with persistent workspaces and flexible networking
* [**LLM UI**](apps_llm_ui.md) - Launch a visual chat interface to a deployed model
* [**MCP Server Deploy**](apps_mcp_server.md) - Deploy remote MCP (Model Context Protocol) servers as containerized services over a secure network endpoint.

### Agentic-AI
Applications for agentic coding assistants running in a governed, remote environment:
* [**Claude Code**](apps_claude_code.md) - Launch a detachable remote VS Code session pre-configured with the Claude Code CLI
* [**Codex**](apps_codex.md) - Launch a detachable remote VS Code session pre-configured with the Codex CLI
* [**OpenCode**](apps_open_code.md) - Launch a detachable remote VS Code session pre-configured with the OpenCode CLI

### NVAIE

Applications utilizing NVIDIA AI Enterprise's (NVAIE) suite of software tools:
* [**NIM**](apps_nvidia_nim.md) - Launch [NVIDIA NIM](https://developer.nvidia.com/nim) models through their specific containers
* [**NVIDIA Dynamo**](apps_nvidia_dynamo.md) - Deploy NVIDIA Dynamo's distributed LLM inference pipeline as a multi-service stack for high-throughput model serving
* **NVIDIA NIM RAG** - Launch an Nvidia NIM RAG session

### Cluster
Applications for deploying and managing scalable compute clusters for distributed workloads:
* [**Deploy Slurm Cluster**](apps_deploy_slurm.md) - Deploy a dynamic Slurm cluster for HPC and distributed workloads
* [**K3s Deployment**](apps_k3k.md) - Deploy a Kubernetes cluster with a configurable number of worker nodes
* [**Multi-Node Trainer**](apps_multi_node_trainer.md) - Orchestrate distributed training across multiple nodes

## Application Catalog

The application catalog manages applications themselves, as opposed to their running instances, which are managed from
each application's own page (see [Application Instance Actions](#application-instance-actions)). Catalog actions are
available to administrators only.

### Deploy an Application

Admins can deploy an existing application to create an independent copy of it, with its own name, description, icon, and category.
Changes made to either application do not affect the other.

To deploy an application:

1. Click the action menu  of the app you want to deploy.
2. Select **Deploy application**.
3. Configure the new application:
   * Optionally upload a new icon.
   * Edit the application name.
   * Edit the description.
   * Select an existing category or create a new category.
4. Click **Create**.

The deployed application appears in the selected category and can be launched like any other application.

An application's visibility depends on its category:
* **My Apps** - Visible only to you and workspace admins.
* **Admins Only** - The default category for a new deployment. Visible only to workspace admins.
* Any other category - Visible to workspace members with access to that category.

Administrators also have access to **All Users Apps**, a category that aggregates all users' applications in **My Apps**
in one place. From here, an administrator can deploy an application to make it more broadly available in
another category, or delete it.

### Delete an Application

Administrators can delete custom applications only: applications created by cloning or through the API. Built-in
applications cannot be deleted.

To delete an application:

1. Click the action menu  of the app you want to delete.
2. Select **Delete application**.
3. Confirm the deletion.

## Application Instance Pages
Each application has its own page, split into two sections:
* **Instance List** - Launch new instances of the application and view previously launched ones. Click an instance to
  open its dashboard. Hover over an instance to access its [instance actions](#application-instance-actions).
* **Instance Dashboard** - The main section of the page: displays the selected instance's status and results.
  Use the search bar  
  to quickly find an instance by name.

![App format](../../img/apps_format_overview.png#light-mode-only)
![App format](../../img/apps_format_overview_dark.png#dark-mode-only)

## Launching an App Instance

1. Select an application.
2. Click **Launch New** .
3. Configure the application.
4. Click **Launch**.

:::tip[Configuration shortcuts]
You can also launch an app instance with the configuration of a previously launched instance:
* Cloning a previously launched app instance will open the instance launch form with the original instance's configuration 
  prefilled.
* Importing an app configuration file. You can export an existing app instance's configuration as a JSON file when 
  viewing its configuration.

The prefilled instance launch form can be edited before starting the new app instance. 
:::

## Application Instance Actions

Hover over an application instance and click the action menu , or right-click the instance.

![App context menu](../../img/app_context_menu.png#light-mode-only)
![App context menu](../../img/app_context_menu_dark.png#dark-mode-only)

| Action | Description |
| --- | --- |
| **Rename** | Rename the application instance. |
| **Configuration** | View the application instance configuration. |
| **Export Configuration** | Export the application instance configuration as a JSON file. |
| **Stop** | Stop the running application instance. |
| **Clone** | Launch a new application instance using the same configuration. |
| **Relaunch** | Stop the running instance and launch a fresh one with its configuration, removing the original. |
| **Delete** | Delete the application instance. |
| **Assign static route** (administrators only) | Assign a static route to the running application instance. See [Publish a URL Application](#publish-a-url-application). |
| **Publish as URL Application** (administrators only) | Publish the application instance as a URL Application. Requires a static route. See [Publish a URL Application](#publish-a-url-application). |

## Publish a URL Application

:::important[Admins only]
Publishing a URL application is available to administrators only.
:::

Publishing turns a running application instance into a URL Application: a tile in the Applications catalog that opens
the instance's static URL in a new browser tab.

Publishing requires a static route, [defined in advance under Application Gateway settings](../settings/webapp_settings_app_gw.md#static-routes).

To publish an instance:

1. Hover over the running application instance and click Menu .
2. Select **Assign static route**.
3. Select an existing static route and click **Assign**. The instance relaunches using that route.
4. Hover over the instance again, and click Menu .
5. Select **Publish as URL Application**.
6. Optionally edit the application name and icon, and select or create a category for the app.
7. Click **Publish**.

The published URL Application appears in the selected category. Users can only access the app in categories they
have permission to access. If its static route is disabled or removed, the URL Application is disabled until the route
is restored.

## Instance List Actions

Access the instance list actions by clicking the action menu () 
on the instance list header:

![Instance list actions](../../img/apps_instance_list_actions.png#light-mode-only)
![Instance list actions](../../img/apps_instance_list_actions_dark.png#dark-mode-only)

| Action | Description |
| --- | --- |
| **Import Configuration** | Import an app instance's configuration file. This opens the app instance launch form prefilled according to the imported file. You can modify the configuration before launching the instance.  |
| **Clear Completed** | Delete all app instances that have completed their execution. This action only deletes instances in the current instance list view (i.e. My instances / All). |
