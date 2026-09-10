# Klique UI

> The Klique WebApp structure, sidebar modules, role-based views, and top bar search and settings features.

The **Klique Web UI** is the graphical user interface for the Klique platform, which includes:
* ML workload automation
* Resource utilization monitoring and management 
* Live model endpoint monitoring
* ML experiment management and visualization 
* Model and Dataset viewing and management
* Pipeline creation and monitoring
* User and administrator settings

![WebApp screenshots gif](../img/gif/webapp_screenshots.gif#light-mode-only)
![WebApp screenshots gif](../img/gif/webapp_screenshots_dark.gif#dark-mode-only)

## UI Structure

The WebApp presents a [role-based UI](role_based_ui.md): the sidebar is filtered by the active 
view (AI Admin, AI Builder, or AI Consumer), which is selected from the user menu. The **Settings** 
page in the user menu shows personal settings only; administrator settings are accessed from a 
[Settings](admin_settings.md) sidebar item in the AI Admin view.

## UI Modules 
The UI's sidebar provides access to the following modules:

* [Applications](applications/apps_overview.md) - Klique's GUI applications for no-code workflow execution.

* [Orchestration](webapp_workers_queues.md) - Autoscaling, resource usage monitoring and allocation management.

* [Model Endpoints](webapp_model_endpoints.md) - Monitor your live model endpoints. 

* [Datasets](datasets/webapp_dataset_page.md) - View and manage your datasets.

* [Projects](webapp_projects_page.md) - The main experimentation page. Access your tasks and models as they are organized into projects. The tasks and models are displayed in tables which let you:

  * Track ongoing tasks and visualize their results
  * Reproduce previous task runs 
  * Tune task parameter values with no code change
  * Compare tasks and models
  * Share tasks and models with other users
  * Create and share rich content [Reports](webapp_reports.md)

* [Pipelines](pipelines/webapp_pipeline_page.md) - View and manage your pipelines.

## UI Top Bar 
### Settings Menu

Click the profile menu button 
to access the following:
* **Settings** - Navigate to Klique's [Settings](settings/webapp_settings_profile.md) page:
  * Set personal [WebApp preferences](settings/webapp_settings_profile.md)
  * Manage [workspace API credentials](settings/webapp_settings_profile.md#api-credentials) 
  * Manage [personal configuration vault](settings/webapp_settings_profile.md#configuration-vault)
  * Configure [cloud storage access credentials](settings/webapp_settings_profile.md#browser-cloud-storage-access) for the Klique Web UI
  * [Administrator settings](admin_settings.md)

  :::note
  Under the [role-based UI](role_based_ui.md), administrator settings are not shown in this menu. 
  They are accessed from the [Settings](admin_settings.md) sidebar item in the AI Admin view instead.
  :::

* Appearance - Select the UI color scheme:
  * Light: Klique will be in a light theme.
  * Dark: Klique will be in a dark theme.
  * System: Klique will follow your device’s theme.
* **Logout** of Klique 

### Finding What You're Looking for

The Klique UI provides two search options on most pages:
* **In-page search**: Each object (e.g. projects, tasks etc.) page includes its own search bar  
  for filtering the objects shown on that page. This search focuses on attributes relevant to that object type:
  * Projects: show projects whose name or ID match the searched text 
  * Tasks: show tasks whose name, ID, description, or input/output models match the searched text
  * Models: show models whose name, ID, or description match the searched text.
  * Dataviews: show dataviews whose name, ID, description, hyper-datasets, or hyper-dataset versions match the searched text.
  * Reports: show reports whose name, ID, tags, project, description, or content match the searched text.
  * Datasets: show datasets whose name, ID, or description match the searched text 
  * Pipeline Runs: show reports whose name, ID, or description match the searched text 
  

  :::tip[Additional filtering]
  Klique's object tables (e.g. [tasks](webapp_exp_table.md), [models](webapp_model_table.md), [pipelines](pipelines/webapp_pipeline_table.md), 
  and [datasets](datasets/webapp_dataset_page.md)) provide column filters to easily focus your search by object properties
  (e.g. status, creation/update time, metric values, etc.).
  :::

* **Global search**: The search bar  
  in the top banner, searches for any objects that match the queries as specified above and 
  returns results grouped by object type (projects, tasks, models, etc.).

  Use the Advanced Search () 
  to apply more elaborate and specific filters. Specify explicit API filters (e.g. `task_filter` in [`Task.query_tasks()`](https://clear.ml/docs/latest/docs/references/sdk/task#taskquery_tasks)) 
  in JSON format. For example:

  ```
  {"status": ["stopped"], "order_by": ["-last_update"], "_all_": {"fields": ["script.repository"], "pattern": "github.com/user"}})
  ```

To use regular expressions, click the `.*` icon in the search bar.

![WebApp Search options](../img/webapp_search_options.png#light-mode-only)
![WebApp Search options](../img/webapp_search_options_dark.png#dark-mode-only)

### Helpful Resources 
Click the help menu button  
in the top right corner of the web UI screen to access the self-help resources including:
* ClearML Python Package setup - Instruction to get started with the `clearml` Python package
* [Klique on YouTube](https://www.youtube.com/@KliqueAI)   - Instructional videos on integrating Klique into your workflow
* Online Documentation
* Pro Tips - Tips for working with Klique efficiently
* Contact Us - Quick access to contact form
