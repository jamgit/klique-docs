# Managing Experiments

> Instrumenting, tracking, comparing, reproducing, and organizing training runs in Klique, then registering the resulting models.

Training a model is rarely a single run: it's a sequence of experiments, each changing a parameter, a dataset, or a
line of code to chase a better result. This page walks through that workflow end to end, from instrumenting your
training script to serving the model it produces.

## Instrumenting Your Code

Klique tracks experiments through the [ClearML SDK](../clearml_sdk.md), the `clearml` Python package. Install it
with:

```bash
pip install clearml
```

Connect the SDK to your Klique server:
1. In the Klique UI, go to **Settings > Workspace** and click **+ Create new credentials** to generate an access and
   secret key pair (see [API Credentials](../webapp/settings/webapp_settings_profile.md#api-credentials)).
2. Run `clearml-init` and paste the copied configuration when prompted.

This writes the server addresses and credentials to your local `clearml.conf` file (see
[Client Configuration](../configs/client_configuration.md) for the rest of its settings), so the SDK knows where to
report.

Adding a single call to your training script creates a task and starts tracking it:

```python
from clearml import Task

task = Task.init(project_name="Image Classification", task_name="ResNet50 baseline")
```

From this point on, the SDK automatically  collects the scripts' entire execution information, including:

* Git repository (branch, commit ID, and uncommitted changes)
* Working directory and entry point
* Installed Python packages
* Hyperparameters exposed through `argparse`, `click`, or a dictionary passed to
[`Task.connect()`](https://clear.ml/docs/latest/docs/references/sdk/task#connect)
* Initial weights model

See [Task Creation](https://clear.ml/docs/latest/docs/clearml_sdk/task_sdk#task-creation) for the full set of options.

## Running Experiments

Run the instrumented script the same way you always have, locally or on any remote machine. If you enqueue the task
instead, the Klique Orchestrator places it on managed compute and the
[Klique Executor](../orchestrator.md#execution) reproduces the environment and runs it there.

As the script runs, `clearml` keeps logging automatically:

- Output models trained with common frameworks (PyTorch, TensorFlow, scikit-learn, and others) are captured with no
  extra code (see [Automatic Logging](https://clear.ml/docs/latest/docs/clearml_sdk/task_sdk#automatic-logging) for the
  full framework list).
- Metrics, plots, and debug samples reported through [`Logger`](https://clear.ml/docs/latest/docs/fundamentals/logger) or
  captured from libraries like Matplotlib and TensorBoard.

## Finding and Tracking Your Runs

Once a task starts, it appears in its project's [task table](../webapp/webapp_exp_table.md) in the Klique UI, where you
can search, filter, and [tag](../webapp/webapp_exp_track_visual.md#tagging-tasks) it alongside every other run in the
project.

Open a task to [track its execution and visualize results](../webapp/webapp_exp_track_visual.md): source code and
package versions, hyperparameters and configuration objects, console output, and the scalars, plots, and debug samples
reported during the run. All of this updates live while the task is still running.

![Info panel](../img/webapp_tracking_40.png#light-mode-only) 
![Info panel](../img/webapp_tracking_40_dark.png#dark-mode-only)

## Comparing Experiments

From the task table, switch to [comparison view](../webapp/webapp_exp_table.md#comparing-tasks) for a quick overlay of
selected tasks' scalar and plot results. For a more in-depth comparison, select the tasks and click **Compare** to open
the [dedicated comparison page](../webapp/webapp_exp_comparing.md): side-by-side execution details and hyperparameter
values, scalar and plot overlays, and debug samples at matching iterations. Parallel coordinates and scatter plot views
show how a hyperparameter correlates with a chosen metric across the compared runs, which is often the quickest way to
decide what to change next.

![Merged comparison plots](../img/webapp_compare_view_2.png#light-mode-only)
![Merged comparison plots](../img/webapp_compare_view_2_dark.png#dark-mode-only)

## Reproducing and Tuning Runs

To try a variation, [clone or reset](../webapp/webapp_exp_reproducing.md) a task, [edit its hyperparameters or other
configuration](../webapp/webapp_exp_tuning.md#modifying-tasks) in the UI, and re-enqueue it. The change is injected
into your original code.

Repeating this manually across a whole parameter search doesn't scale well. The
[Hyperparameter Optimization app](../webapp/applications/apps_hpo.md) automates it: give it a base task, a parameter
search space, and an objective metric, and it clones and launches the trials for you, using strategies such as random
search, grid search, or Bayesian optimization. 

The same optimization is available programmatically through the
[`HyperParameterOptimizer`](https://clear.ml/docs/latest/docs/clearml_sdk/hpo_sdk) SDK class.

## Organizing and Sharing Results

As the number of runs grows, [projects and subprojects](../webapp/webapp_projects_page.md) keep them organized, and
tags let you classify and filter across a project. Turn the task table into a
[leaderboard](../webapp/webapp_exp_table.md#creating-a-task-leaderboard) by adding metric and hyperparameter columns,
filtering, and sorting; the resulting view can be bookmarked or shared with a link. 

A project's [Overview tab](../webapp/webapp_project_overview.md) can also show a metric snapshot across all of a project's tasks.

To write up findings, use [Reports](../webapp/webapp_reports.md): Markdown documents that can embed live plots and
debug samples from any task or comparison, so the numbers stay current as the underlying runs update.

![Reports](../img/webapp_report.png#light-mode-only)
![Reports](../img/webapp_report_dark.png#dark-mode-only)

## From Experiment to Model

Once a run produces a model you want to keep, publish the task to lock its data, code, and outputs against further
changes. Its output model can be found and compared alongside others associated with the project in the
[model table](../webapp/webapp_model_table.md) and [model comparison](../webapp/webapp_model_comparing.md) views. 
Once a model is registered to Klique, reuse it as the input model for another task, for example to fine-tune or continue
training it further (see [Working with Models](https://clear.ml/docs/latest/docs/model_registry/#working-with-models)).

From here, you can serve the model: turn it into a live network endpoint that applications call for inference.
See Klique's model serving solutions in [One-click Inference Service](../deploying_models.md).
