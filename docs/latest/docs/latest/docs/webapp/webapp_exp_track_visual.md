# Tracking Tasks and Visualizing Results

> The WebApp task tabs for tracking execution, configuration, artifacts, and dataviews, and visualizing scalars, plots, and debug samples.

While a task is running, and any time after it finishes, track it and visualize the results in the Web UI,
including:

* [Execution details](#execution) - Code, the container image used for [Klique Executor](../orchestrator.md#execution), output destination for artifacts, and the logging level.
* [Configuration](#configuration) - Hyperparameters, user properties, and configuration objects.
* [Artifacts](#artifacts) - Input model, output model, model snapshot locations, other artifacts.
* [Dataviews](#dataviews) - For tasks that use [Hyper-Datasets](https://clear.ml/docs/latest/docs/hyperdatasets/dataviews/), the input data selection, filtering, ROI mapping, label enumeration, and iteration controls.
* [Info](#info) - Extended task information, such as the start, create, and last update times and dates, user creating the task, and its description.
* [Console](#console) - stdout, stderr, output to the console from libraries, and `clearml` explicit reporting.
* [Scalars](#scalars) - Metric plots.
* [Plots](#plots) - Other plots and data, for example: Matplotlib, Plotly, and `clearml` explicit reporting.
* [Debug samples](#debug-samples) - Images, audio, video, and HTML.

You can download the task information (execution details, configuration parameters etc.) as a JSON file by clicking  .

## Viewing Modes

The Web UI provides two viewing modes for task details:

* [Info panel](#info-panel)
* [Full screen details mode](#full-screen-details-view)

Both modes contain all task details. When either view is open, switch to the other mode by clicking 
(**View in task table / full screen**), or clicking  (**menu**) > **View in tasks
table / full screen**.

### Info Panel

The info panel keeps the task table in view so that [task actions](webapp_exp_table.md#task-actions)
can be performed from the table (as well as the menu in the info panel).

![Info panel](../img/webapp_tracking_40.png#light-mode-only) 
![Info panel](../img/webapp_tracking_40_dark.png#dark-mode-only)

Click  to 
hide details in the task table, so only the task names and statuses are displayed

![Compressed info panel](../img/webapp_tracking_41.png#light-mode-only) 
![Compressed info panel](../img/webapp_tracking_41_dark.png#dark-mode-only)

### Full Screen Details View

The full screen details view allows for easier viewing and working with task tracking and results. The task
table is not visible when the full screen details view is open. Perform task actions from the menu.

![Full screen view](../img/webapp_tracking_33.png#light-mode-only) 
![Full screen view](../img/webapp_tracking_33_dark.png#dark-mode-only)

## Execution
A task's **EXECUTION** tab of lists the following:
* Source code
* Uncommitted changes
* Installed Python packages
* Container details
* Output details

In full-screen mode, the source code and output details are grouped in the **DETAILS** section.

### Source Code

The Source Code section of a task's **EXECUTION** tab includes:
* The task's repository - Click link to open the repository at the specified revision 
* Commit ID
* Script path
* Working directory
* Binary (Python executable)

![Source code section](../img/webapp_exp_source_code.png#light-mode-only) 
![Source code section](../img/webapp_exp_source_code_dark.png#dark-mode-only)

### Uncommitted Changes

Klique displays the git diff of the task in the **Uncommitted Changes** section. 

![Uncommitted changes section](../img/webapp_exp_uncommitted_changes.png#light-mode-only) 
![Uncommitted changes section](../img/webapp_exp_uncommitted_changes_dark.png#dark-mode-only)

### Python Packages

The Python Packages section lists the task's installed Python packages and their versions.

![Python packages section](../img/webapp_exp_installed_packages.png#light-mode-only) 
![Python packages section](../img/webapp_exp_installed_packages_dark.png#dark-mode-only)

When a `clearml-agent` executing a task ends up using a different set of Python packages than was originally 
specified, both the original specification (`original pip` or `original conda`), and the packages the agent ended up 
using to set up an environment (`pip` or `conda`) are available. Select which requirements to view in the dropdown menu.  

![Packages used by agent](../img/webapp_exp_installed_packages_2.png#light-mode-only)
![Packages used by agent](../img/webapp_exp_installed_packages_2_dark.png#dark-mode-only)

### Container
The Container section list the following information:
* Image - a pre-configured container that Klique Executor will use to remotely execute this task (see [Building Task Execution Environments in a Container](https://clear.ml/docs/latest/docs/getting_started/clearml_agent_base_docker/))
* Arguments - add container arguments
* Setup shell script - a bash script to be executed inside the container before setting up the task's environment

:::important 
To [rerun](webapp_exp_tuning.md) a task through the UI in the listed container, the `clearml-agent` executing the task must be running in 
Docker mode:

```bash
clearml-agent daemon --queue <execution_queue_to_pull_from> --docker [optional default docker image to use]
```

For more information, see [Docker Mode](https://clear.ml/docs/latest/docs/clearml_agent/clearml_agent_execution_env/#docker-mode).
:::

![Container section](../img/webapp_exp_container.png#light-mode-only)
![Container section](../img/webapp_exp_container_dark.png#dark-mode-only)

### Output
The Output details include:
* The output destination used for storing model checkpoints (snapshots) and artifacts (see also, [default_output_uri](https://clear.ml/docs/latest/docs/configs/clearml_conf#config_default_output_uri)
  in the configuration file, and `output_uri` in [`Task.init`](https://clear.ml/docs/latest/docs/references/sdk/task#taskinit) parameters).

![Execution details section](../img/webapp_exp_output.png#light-mode-only)
![Execution details section](../img/webapp_exp_output_dark.png#dark-mode-only)

## Configuration

All parameters and configuration objects appear in the **CONFIGURATION** tab.

### Hyperparameters

Hyperparameters are grouped by their type and appear in **CONFIGURATION** **>** **HYPERPARAMETERS**. Once a task 
is run and stored in Klique Server, any of these hyperparameters can be [modified](webapp_exp_tuning.md#modifying-tasks).

#### Command Line Arguments

The **Args** group shows automatically logged argument parser parameters (e.g. `argparse`, `click`, `hydra`). 
Hover over  (menu) on a 
parameter's line, and the type, description, and default value appear, if they were provided.

![Command line arguments configuration group](../img/webapp_tracking_22.png#light-mode-only)
![Command line arguments configuration group](../img/webapp_tracking_22_dark.png#dark-mode-only)

#### Environment Variables

If environment variables were listed in the `CLEARML_LOG_ENVIRONMENT` environment variable or the [`sdk.development.log_os_environments`](https://clear.ml/docs/latest/docs/configs/clearml_conf#log_env_var) 
field of the `clearml.conf` file, the **Environment** group displays the listed environment variables.

:::note
The `CLEARML_LOG_ENVIRONMENT` variable always overrides the `clearml.conf` file. 
:::

![Environment variables configuration group](../img/webapp_tracking_23.png#light-mode-only)
![Environment variables configuration group](../img/webapp_tracking_23_dark.png#dark-mode-only)

#### Custom Parameter Groups

Custom parameter groups show parameter dictionaries if the parameters were connected to the Task, using 
[`Task.connect()`](https://clear.ml/docs/latest/docs/references/sdk/task#connect) with a `name` argument provided. `General` is the default section
if a name is not provided.

![Custom parameters group](../img/webapp_tracking_25.png#light-mode-only)
![Custom parameters group](../img/webapp_tracking_25_dark.png#dark-mode-only)

#### TensorFlow Definitions

The **TF_DEFINE** parameter group shows automatic TensorFlow logging.

![TF_DEFINE parameter group](../img/webapp_tracking_26.png#light-mode-only)
![TF_DEFINE parameter group](../img/webapp_tracking_26_dark.png#dark-mode-only)

### User Properties

User properties allow to store any descriptive information in a key-value pair format. They are editable in any task,
except *Published* ones (read-only).

![User properties section](../img/webapp_tracking_21.png#light-mode-only)
![User properties section](../img/webapp_tracking_21_dark.png#dark-mode-only)

### Configuration Objects

`clearml` tracks a task's model configuration objects, which appear in **Configuration Objects** **>** **General**.
These objects include those that are automatically tracked, and those connected to a Task in code (see [`Task.connect_configuration`](https://clear.ml/docs/latest/docs/references/sdk/task#connect_configuration)).

![Configuration objects](../img/webapp_tracking_24.png#light-mode-only)
![Configuration objects](../img/webapp_tracking_24_dark.png#dark-mode-only)

`clearml` supports providing a name for a Task model configuration object (see the `name`
parameter in [`Task.connect_configuration`](https://clear.ml/docs/latest/docs/references/sdk/task#connect_configuration)).

![Custom configuration objects](../img/webapp_tracking_28.png#light-mode-only)
![Custom configuration objects](../img/webapp_tracking_28_dark.png#dark-mode-only)

## Artifacts

Task artifacts, including models, appear in the **ARTIFACTS** tab. 

Each non-model artifact entry displays: 
* File path
* File size
* Hash
* Metadata (if set)

Artifact location is displayed in the `FILE PATH` field. To access model and other artifact files:
* **Local Files**: Use the 'copy to clipboard' action ()
to obtain the file path to facilitate local storage access since web applications are prohibited from accessing the local disk for security reasons.
* **Remote Files**  (e.g. network-hosted artifacts with `https://`, `s3://`, etc. URIs): Use the download action () 
to retrieve the file.

![Other artifacts section](../img/webapp_tracking_30.png#light-mode-only)
![Other artifacts section](../img/webapp_tracking_30_dark.png#dark-mode-only)

### Models

The task's input and output models appear in the **ARTIFACTS** tab. Each model entry shows:
* Model name
* ID
* Configuration. 

Input models also display their creating task, which on-click navigates you to the task's page. 

![Models in Artifacts tab](../img/webapp_exp_artifacts_01.png#light-mode-only)
![Models in Artifacts tab](../img/webapp_exp_artifacts_01_dark.png#dark-mode-only)

To view more model details, including design, label enumeration, and general information, click the model name
to navigate to its page in the **MODELS** tab (see [Model Details](webapp_model_viewing.md)). 

## Dataviews

For a task that uses [Hyper-Datasets](https://clear.ml/docs/latest/docs/hyperdatasets/dataviews/), the **DATAVIEWS** tab shows the task's Dataview details, including:
* Input data [selection](#input) and [filtering](#filtering)
* ROI [mapping](#mapping) (label translation)
* [Label enumeration](#label-enumeration)
* [Iteration controls](#iteration-control)

![Dataview tab](../img/hyperdatasets/dataview_tab.png#light-mode-only)
![Dataview tab](../img/hyperdatasets/dataview_tab_dark.png#dark-mode-only)

### Input

SingleFrames are iterated from the Dataset versions specified in the **INPUT** area, in the **SELECTED DATAVIEW** drop-down 
menu.

### Filtering

The **FILTERING** section lists the SingleFrame filters iterated by a Dataview, applied to the task data. 

Each frame filter is composed of: 
* A Dataset version to input from 
* ROI Rules for SingleFrames to include and/or exclude certain criteria.
* Weights for debiasing input data. 
  
Combinations of frame filters can implement complex querying. 

For more detailed information, see [Filtering](https://clear.ml/docs/latest/docs/hyperdatasets/dataviews/#filtering).

### Mapping

ROI label mapping (label translation) applies to the new model. For example, use ROI label mapping to accomplish the following: 

* Combine several labels under another more generic label.
* Consolidate disparate datasets containing different names for the ROI.
* Hide labeled objects from the training process.

For detailed information, see [Mapping ROI labels](https://clear.ml/docs/latest/docs/hyperdatasets/dataviews/#mapping-roi-labels).

### Label Enumeration

Assign label enumeration in the **LABELS ENUMERATION** area.

### Iteration Control

The input data iteration control settings determine the order, number, timing, and reproducibility of the Dataview iterating 
SingleFrames. Depending upon the combination of iteration control settings, all SingleFrames may not be iterated, and some may repeat. 

For detailed information, see [Iteration control](https://clear.ml/docs/latest/docs/hyperdatasets/dataviews/#iteration-control).

Modifying a task's Dataviews is covered in [Modifying Tasks](webapp_exp_tuning.md#dataviews).

## Info

The **INFO** tab shows extended task information:
* [Latest task events log](#latest-events-log)
* [Task description](#description)
* [Task details](#task-details)

### Latest Events Log

The **INFO** tab includes a detailed history of task activity: 
* Task action (e.g. status changes, project move, etc.)
* Action time
* Acting user
* Action source (i.e. Klique Executor, SDK, or UI)
* Action source version 

To download the task history as a CSV file, hover over the log and click .   

![Task audit log](../img/webapp_info_audit_log.png#light-mode-only)
![Task audit log](../img/webapp_info_audit_log_dark.png#dark-mode-only)

:::note[Limited persistency]
Klique maintains a system-wide, large but strict limit for task history items. Once the limit is reached, the oldest entries are purged to make room for fresh entries.
:::

### Description
Add descriptive text to the task in the **Description** section. To modify the description, hover over the 
description box and click **Edit**.

### Task Details
The **Task Details** section lists information describing the task:

* The parent task
* Project name
* Creation, start, and last update dates and times
* User who created the task
* Task state (status)
* Whether the task is archived
* Runtime properties - Information about the machine running the task:
  * Operating system
  * CUDA driver version
  * Number of CPU cores
  * Number of GPUs
  * CPU / GPU type
  * Memory size
  * Host name 
  * Processor
  * Python version
* Task Progress    
    
![Info tab](../img/webapp_tracking_31.png#light-mode-only)
![Info tab](../img/webapp_tracking_31_dark.png#dark-mode-only)

## Task Results

:::tip[Embedding Visualizations]
You can embed plots from the app instance dashboard into [Reports](webapp_reports.md). Klique 
also supports embedding resources in third-party platforms that support embedded content (e.g. Notion). These visualizations 
are updated live as the app instance(s) updates. Hover over the plot and click  
to copy the embed code, and navigate to a report to paste the embed code.
:::

### Console

The complete task log containing everything printed to stdout and stderr appears in the **CONSOLE** tab. The full log
is downloadable. To view the end of the log, click **Jump to end**.

![Console tab](../img/webapp_tracking_32.png#light-mode-only)
![Console tab](../img/webapp_tracking_32_dark.png#dark-mode-only)

### Scalars

All scalars that `clearml` automatically logs, as well as those explicitly reported in code, appear in
**SCALARS**. 

Scalar series can be displayed in [graph view](#graph-view) (default) or in [metric values view](#metric-values-view):

#### Graph View
Scalar graph view () 
shows scalar series plotted as a time series line chart. By default, a single plot is shown for each scalar metric, 
with all variants overlaid within.

The series are subsampled for 
display efficiency. For high resolution, view a series in full screen mode by hovering over the graph and clicking .

:::note[Full Screen Refresh]
Scalar graphs in full screen mode do not auto-refresh. Click  
to update the graph. 
:::

Single value scalars (see [`Logger.report_single_value`](https://clear.ml/docs/latest/docs/references/sdk/logger#report_single_value)) are shown in 
a `Summary` table.

![Single value scalar plot](../img/webapp_single_scalar_plot.png#light-mode-only)
![Single value scalar plot](../img/webapp_single_scalar_plot_dark.png#dark-mode-only)

##### Scalar Plot Tools

Use the scalar tools to improve analysis of scalar metrics. In the info panel, click  to access the tools. In the full-screen details view, the tools
are on the left side of the window. The tools include:

* **Group by** - Select one of the following:
  * **Metric** - Displays all variants for a metric on the same plot. For example, if you have a "Test" metric with 
    "loss" and "accuracy" variants, both variants will appear on the same plot that is titled "Test".

    ![Plots grouped by metric](../img/webapp_tracking_33.png#light-mode-only)
    ![Plots grouped by metric](../img/webapp_tracking_33_dark.png#dark-mode-only)
  
  * **None** -  Displays individual plots for each metric-variant combination, grouped into sections by metric. For 
    example, a "Test" metric with "loss" and "accuracy" variants will have a separate plot for each variant under the 
    "Test" group.

    ![Plots groups my metric and variant](../img/webapp_tracking_34.png#light-mode-only)
    ![Plots groups my metric and variant](../img/webapp_tracking_34_dark.png#dark-mode-only)

* Horizontal axis - Select the x-axis units:
  * Iterations
  * Time from start - Time since task began
  * Wall time - Local clock time
* Curve smoothing - Choose which smoothing algorithm to use from the dropdown menu: Exponential moving average, Gaussian, 
  or Running Average. Use the sliders to configure smoothing factors or specify a value manually.  Use the `Show Originals` 
  toggle control to show both original and smoothed plots, or smoothed plots only.
* Show / hide plots - Click  to control which 
  plots to display. For example, to display specific plots, click **HIDE ALL**, and then click  
  on each plot you want to view.

:::tip[Default scalar display]
After adjusting the scalar display (e.g. grouping, axis, smoothing), click **Set as default** to save the current 
configuration as the default view for the project’s task and model scalars.
:::

See additional [plot controls](#plot-controls) below.

##### Embedding Plots

To embed scalar plots in your [Reports](webapp_reports.md), hover over a plot and click Embed , 
which will copy to clipboard the embed code to put in your Reports. To quickly get the embed codes for all plots of a 
specific metric, click Embed  
on the group section header (available when plots are [grouped by](#group_by) `None`).

![Embed metric group](../img/webapp_tracking_34b.png#light-mode-only)
![Embed metric group](../img/webapp_tracking_34b_dark.png#dark-mode-only)

In contrast to static screenshots, embedded resources 
are retrieved when the report is displayed allowing your reports to show the latest up-to-date data.

#### Metric Values View

The metric values view ()
shows a table summary of your metrics with a row per metric/variant:

* First - The metric/variant series' initial value
* Last - The metric/variant series' last value
* Min -  The metric/variant series' minimum value
* Max - The metric/variant series' maximum value
* Mean - The metric/variant series' mean value

If all the values of a specific metric/variant are the same, the row will display a  sign.

![Plots tab](../img/webapp_tracking_34a.png#light-mode-only)
![Plots tab](../img/webapp_tracking_34a_dark.png#dark-mode-only)

#### Select Metrics and Variants to View

Choose which metrics to view using one of the following options:
* Quick filter bar () - Show 
  metrics/variants whose name fit a partial-string match 
* Filter menu ( in Graph View,  in Metric 
  Values View) - Select which metrics to view by clicking their show/hide button (). 
  Click **Hide/Show all** to quickly hide/show all metrics (when the selection is focused through search, the Hide/Show action applies to the matching metrics only).

### Plots
Non-time-series plots appear in **PLOTS**. These include data generated by libraries, visualization tools, and 
explicitly reported using `clearml.Logger`. These may include 2D and 3D plots, tables (Pandas and CSV files), and 
Plotly plots. Individual plots can be shown / hidden or filtered by title.

![Plots tab](../img/webapp_tracking_35.png#light-mode-only)
![Plots tab](../img/webapp_tracking_35_dark.png#dark-mode-only)

Plots are grouped into sections by metric. To quickly get the embed codes for all plots of a specific metric, click Embed  
on the group section header.

For each metric/variant combination, the latest reported plot is displayed.

When viewing a plot in full screen (), 
older iterations are available through the iteration slider (or using the up/down arrow keyboard shortcut). Go to the 
previous/next plot in the current iteration using the  /  
buttons (or using the left/right arrow keyboard shortcut).

![Plots maximize tab](../img/webapp_tracking_35a.png#light-mode-only)
![Plots maximize tab](../img/webapp_tracking_35a_dark.png#dark-mode-only)

Choose which plots to display using the following options:
* Quick filter bar () - Show 
  metrics/variants whose name fit a partial-string match 
* Filter menu - Select which metrics to view by clicking their show/hide button (). 
  Click **Hide/Show all** to quickly hide/show all metrics (when the selection is focused through search, the Hide/Show 
  action applies to the matching metrics only).

#### Plot Controls

The table below lists the plot controls which may be available for any plot (in the **SCALARS** and **PLOTS** tabs). 
These controls allow you to better analyze the results. Hover over a plot, and the controls appear.

|Icon|Description|
|---|---|
|  | Download plots as PNG files. |
|  | Pan around plot. Click , click the plot, and then drag. |
|  | To examine an area, draw a dotted box around it. Click  and then drag. |
|  | To examine an area, draw a dotted lasso around it. Click  and then drag. |
|  | Zoom into a section of a plot. Zoom in - Click  and drag over a section of the plot. Reset to original scale - Click . |
|  | Zoom in. |
|  | Zoom out. |
|  | Reset to autoscale after zooming (, , or ). |
|  | Reset axes after a zoom. |
|  | Show / hide spike lines. |
|     | Set data hover mode: Closest - Show the (X, Y) data point closest to the cursor, including horizontal and vertical axes values  X - Show labels for points with the same x value as the cursor    X unified - Show a single label for the points with the same x value as the cursor |
|  | Switch to logarithmic view. |
|  | Hide / show the legend. |
| | Switch between original and auto-fitted plot dimensions. The original layout is the plot's user-defined dimensions. |
|  | Download plot data as a JSON file. |
|  | Download **table** plot data as a CSV file. |
|  | Expand plot to entire window. When used with scalar graphs, full screen mode displays plots with all data points, as opposed to an averaged plot |
|  | Refresh scalar graphs in full screen mode to update it.  | 
|  | Copy to clipboard the resource embed code. This opens the following options: **Embed in External tool** - Copy code to add to third-party platforms that support embedded content (e.g. Notion). **Embed in report** - Copy code to add to a [report](webapp_reports.md) In contrast to static screenshots, embedded resources are retrieved when the tool/report is displayed allowing your tools/reports to show the latest up-to-date data. |

 

#### 3D Plot Controls 
|Icon|Description|
|---|---|
| | Switch to orbital rotation mode - rotate the plot around its middle point. |
| | Switch to turntable rotation mode - rotate the plot around its middle point while constraining one axis |
| | Reset axes to default position. |

### Debug Samples

Task outputs such as images, audio, and videos appear in **DEBUG SAMPLES**. These include data generated by 
libraries and visualization tools, and explicitly reported using the [`clearml.Logger`](https://clear.ml/docs/latest/docs/fundamentals/logger). 

You can view debug samples by metric in the reported iterations. Filter the samples by metric by selecting a metric from the 
dropdown menu above the samples. The most recent iteration appears first.

![Debug Samples tab](../img/webapp_tracking_43.png#light-mode-only)
![Debug Samples tab](../img/webapp_tracking_43_dark.png#dark-mode-only)

For each metric, the latest reported debug sample is displayed.

Click a sample to view it in full screen. If the sample is video or audio, the full screen mode includes a player. 

When viewing a sample in full screen, older iterations are available through the iteration slider (or using the up/down 
arrow keyboard shortcut). Go to the previous/next sample in the current iteration using the  /  
buttons (or using the left/right arrow keyboard shortcut).

![Debug Samples image viewer](../img/webapp_tracking_44.png#light-mode-only)
![Debug Samples image viewer](../img/webapp_tracking_44_dark.png#dark-mode-only)

## Tagging Tasks

Tags are user-defined, color-coded labels that can be added to tasks (and pipelines, datasets, and models), 
allowing to easily identify and group tasks. Tags can help in organizing, querying, and automating tasks.
For example, tag tasks by the machine type used to execute them, label versions, team names, or any other
category.

You can use tags to filter the tasks in your task table (see [Filtering Columns](webapp_exp_table.md#filtering-columns))
or when querying tasks in your code (see [Tag Filters](https://clear.ml/docs/latest/docs/clearml_sdk/task_sdk#tag-filters)). You can trigger 
task execution according to their tags (see [TriggerScheduler](https://clear.ml/docs/latest/docs/references/sdk/trigger)) or automatically
deploy models according to their tags (see [ClearML Serving](https://clear.ml/docs/latest/docs/clearml_serving/clearml_serving_extra)).

**To add tags:**
1. Click the task **>** Hover over the tag area **>** **+ADD TAG** or  (menu)
1. Do one of the following:
    * Add a new tag - Type the new tag name **>** **(Create New)**.
    * Add an existing tag - Click a tag.
    * Customize a tag's colors - Click **Tag Colors** **>** Click the tag icon **>** **Background** or **Foreground** **>** Pick a color **>** **OK** **>** **CLOSE**.

**To remove a tag** - Hover over the tag and click **X**.

## Locating the Task ID

The task ID appears in the task page's header.
