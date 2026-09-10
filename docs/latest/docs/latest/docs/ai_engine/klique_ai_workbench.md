# AI MLOps Workbench

> Klique's AI MLOps Workbench for experiment tracking, hyperparameter optimization, model registry, and dataset versioning with Hyper-Datasets.

The AI MLOps Workbench is where builders develop, train, and manage models on Klique. It combines a full experiment
manager with data versioning and workflow automation, so every run is tracked, reproducible, and comparable without
changing how you write code. You keep working in your own scripts and frameworks, and the Workbench records the 
surrounding context.

The Workbench captures each experiment automatically and stores it centrally, turning scattered scripts and local runs
into an organized, queryable history of your work. It is paired with
[Hyper-Datasets](#hyper-datasets), so you version and track both the models you build and the data they train on.

The Workbench is part of Klique's [AI Engine](ai_engine.md) and runs on compute managed by the
[Orchestration Engine](../orchestration_engine/orchestration_engine.md), drawing training and experimentation from the
same governed compute as the rest of the platform.

![Experiment table](../img/webapp_experiment_table.png#light-mode-only)
![Experiment table](../img/webapp_experiment_table_dark.png#dark-mode-only)

## Experiment management

### Experiment Tracking
Add a couple of lines to your code and each execution becomes a [task](https://clear.ml/docs/latest/docs/fundamentals/task) that records everything
needed to understand and rerun it. The Workbench automatically logs source code, the git repository and uncommitted
changes, installed packages, command-line arguments and hyperparameters, console output, scalars and plots, artifacts, and output
models. Because capture is automatic, your experiment history stays complete even as the pace of experimentation
increases.

### Projects
[Projects](https://clear.ml/docs/latest/docs/fundamentals/projects) organize tasks and models into a structure that mirrors how your teams work,
similar to folders in a file system. They can be nested into subprojects, which keeps large volumes of experiments and
models easy to find, filter, and query. Each project gives a team a shared, searchable view of everything it has run.

![Projects](../img/webapp_project_page.png#light-mode-only)
![Projects](../img/webapp_project_page_dark.png#dark-mode-only)

### Hyperparameters
[Hyperparameters](https://clear.ml/docs/latest/docs/fundamentals/hyperparameters) are tracked per task, so every run is tied to the exact
configuration that produced it. You can inspect and compare them across experiments, and override them from the
[Klique WebApp](../webapp/webapp_overview.md) without editing code, which makes it straightforward to relaunch a task
with new values on remote compute.

### Metrics and Visualization
The [Logger](https://clear.ml/docs/latest/docs/fundamentals/logger) reports scalars, plots, images, audio, and other debug samples, complementing
the metrics captured automatically. Results stream to the server in real time and render as interactive dashboards in
the WebApp, so you can watch a run as it trains and revisit the full picture afterward.

### Artifacts
[Artifacts](https://clear.ml/docs/latest/docs/fundamentals/artifacts) are objects a task produces or consumes, including NumPy arrays, Pandas
DataFrames, images, files, folders, Python objects or model checkpoints. Logging them makes a task's inputs and outputs
reproducible and shareable, so results can be retrieved and reused by later tasks or by teammates.

### Model Registry
[Models](https://clear.ml/docs/latest/docs/fundamentals/models) are tracked and versioned as first-class entities with their own identity, lineage,
and stored weights. The Workbench records which task produced each model and the data and parameters behind it, and
every registered model is available from a central [model registry](https://clear.ml/docs/latest/docs/model_registry) for search, comparison, and
handoff to deployment.

### Experiment Comparison
Any set of runs can be [compared](../webapp/webapp_exp_comparing.md) side by side across scalars, plots, hyperparameters,
artifacts, and execution environment. Differences are surfaced directly, so you can see what changed between two runs
and why results moved, which shortens the path from observation to the next experiment.

![Experiment comparison](../img/webapp_compare_view_1.png#light-mode-only)
![Experiment comparison](../img/webapp_compare_view_1_dark.png#dark-mode-only)

## Automation and optimization

### Hyperparameter Optimization
The [hyperparameter optimization](../webapp/applications/apps_hpo.md) module turns a single task into a search over
many, launching and orchestrating trials with strategies such as random search, grid search, and Bayesian optimization
through Optuna or BOHB. Trials run in parallel on managed compute, and their results report back for comparison
like any other experiment.

### Pipelines
[Pipelines](https://clear.ml/docs/latest/docs/pipelines/) connect multiple steps so the output of one becomes the input of the next. A
controller holds the pipeline logic and launches each step in order, locally or on remote compute through the ClearML
Agent, and steps reuse each other's artifacts and parameters. This formalizes an experiment into a repeatable workflow
that can be scheduled or triggered.

## Data management

### Dataset Versioning
[ClearML Data](https://clear.ml/docs/latest/docs/clearml_data/) is a file-based data management solution that makes datasets accessible
from any machine and keeps them versioned, shareable, and reproducible. Datasets are tied to the experiments that use
them, so a run can always be traced back to the exact data it consumed.

### Hyper-Datasets
[Hyper-Datasets](../hyper_datasets.md) are a queryable abstraction over unstructured data such as text, audio, and
visual data, built for the same traceability as the rest of the Workbench. They decouple metadata from raw data
files, so a dataset's contents are defined by queries and parameters rather than hard-coded values, and those
queries are tracked by the experiment manager alongside the task that used them. Datasets can inherit from one
another to form data lineages, and a dataset's version history, contents, and annotations are browsable in the
[Klique UI](../hyperdatasets_webapp/datasets.md).

## Integrations
Klique's AI Workbench [integrates](https://clear.ml/docs/latest/docs/integrations/) with the frameworks and tools teams already use, including
PyTorch, TensorFlow, Keras, scikit-learn, XGBoost, Hugging Face, and reporting libraries such as TensorBoard and
Matplotlib. In most cases tracking begins with no code changes beyond initializing a task, so existing projects gain
full experiment management immediately.
