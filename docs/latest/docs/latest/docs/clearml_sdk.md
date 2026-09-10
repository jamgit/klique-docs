# ClearML SDK

> Core ClearML SDK concepts Klique uses, including projects, tasks, hyperparameters, artifacts, models, pipelines, and datasets.

Klique uses the [ClearML SDK](https://clear.ml/docs/latest/docs/clearml_sdk/) to instrument training and application code. 
The SDK is the Python package that creates and controls the entities below, provides automatic logging that documents your tasks,
and captures execution details such as the git repository and uncommitted changes, installed packages, hyperparameters,
and outputs. This page summarizes the core concepts. Follow any link for the full documentation.

## Entities

[**Projects**](https://clear.ml/docs/latest/docs/fundamentals/projects/) are containers that organize tasks and models (and dataviews when Hyper-Datasets
are enabled), similar to folders in a file system. They can be nested into subprojects, which makes tasks, models, and
dataviews easier to find and query.

![Projects page](img/webapp_project_page.png#light-mode-only)
![Projects page](img/webapp_project_page_dark.png#dark-mode-only)

[**Tasks**](https://clear.ml/docs/latest/docs/fundamentals/task) are the core unit of the experiment manager. A task is a single code execution session,
such as an experiment, a workflow step, or a controller. Calling `Task.init()` in a script creates a task that
automatically captures the code, environment, parameters, and outputs and reports them to the server.

![Experiment table](img/webapp_experiment_table.png#light-mode-only)
![Experiment table](img/webapp_experiment_table_dark.png#dark-mode-only)

[**Hyperparameters**](https://clear.ml/docs/latest/docs/fundamentals/hyperparameters) are a script's configuration values. Klique tracks them per task
so runs can be reproduced and compared, lets you override them from the web UI without changing code, and provides a
dedicated hyperparameter optimization module.

[**Artifacts**](https://clear.ml/docs/latest/docs/fundamentals/artifacts) are objects associated with a task and logged to Klique for later access,
including NumPy arrays, Pandas DataFrames, images, files, folders, and Python objects. They make a task's inputs and
outputs reproducible and shareable.

[**Models**](https://clear.ml/docs/latest/docs/fundamentals/models) are tracked, versioned, and visualized by Klique. Models are stored as artifacts,
but unlike other artifacts they are independent entities with their own ID, so they can be accessed directly or through
the task that created them.

[**Logger**](https://clear.ml/docs/latest/docs/fundamentals/logger) reports task results such as scalars, metrics, plots, and debug samples. It is
provided by the task object, complements the SDK's automatic reporting, and controls logging configuration such as
upload destinations.

## Pipelines

[Pipelines](https://clear.ml/docs/latest/docs/pipelines/) connect multiple steps so the output of one becomes the input of the next. A
controller task holds the pipeline logic and launches steps in order, locally or on any machine through the ClearML
Agent, and steps can reuse each other's artifacts and parameters.

![Pipeline run](img/webapp_pipeline_DAG.png#light-mode-only)
![Pipeline run](img/webapp_pipeline_DAG_dark.png#dark-mode-only)

## ClearML Data

[ClearML Data](https://clear.ml/docs/latest/docs/clearml_data/) is a file-based data management solution. It makes datasets accessible from
any machine and keeps them versioned, shareable, reproducible, and tracked, so an experiment can always be tied to the
exact data it used.

![Dataset preview](img/webapp_dataset_preview.png#light-mode-only)
![Dataset preview](img/webapp_dataset_preview_dark.png#dark-mode-only)

## Hyper-Datasets

[Hyper-Datasets](https://clear.ml/docs/latest/docs/hyperdatasets/overview) are a queryable, MLOps-oriented abstraction of your data. They decouple
metadata from the raw files so a dataset is defined by a query, add metadata version control, and enable workflows such
as data-level hyperparameter optimization and continuous training.

![Hyper-Dataset frame viewer](img/hyperdatasets/dataset_example_frame_editor.png#light-mode-only)
![Hyper-Dataset frame viewer](img/hyperdatasets/dataset_example_frame_editor_dark.png#dark-mode-only)
