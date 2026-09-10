# Tuning Tasks

> How to tune and edit a cloned task's execution details, hyperparameters, artifacts, and dataviews before re-enqueuing it in the WebApp.

Tune task parameters and edit their execution details, then execute the tuned tasks on local or remote machines.

## To Tune a Task and Execute it Remotely:

1. Locate the task.

    * On the Project Dashboard:
      * Click on a task in **RECENT TASKS**
      * In RECENT PROJECTS **>** click on a project card **>** click task
      * In RECENT PROJECTS **> VIEW ALL** **>** click the project card **>** click task
      * In RECENT PROJECTS **>** click **VIEW ALL** **>** click project card or **All Tasks** card **>** click task

1. Clone the task. In the task table:

    1. Click **Clone**, which opens the **Clone task** modal.
    1. Provide new task's details:  
       * Project: Select the Klique project in which the new task will be created, or create a new one. To search for a 
         project, start typing the project name. To create a new project, type a new name and click **Create New**.
       * Name: New task's name
       * Description (Optional): Brief description of the task.
    1. Click **CLONE**.

    The new task is created and has a *Draft* status.

1. Edit the task. See [modifying tasks](#modifying-tasks).

1. Enqueue the task for execution. Right-click the task **>** **Enqueue** **>** Select a queue **>**
   **ENQUEUE**.

    The task's status becomes *Pending*. When the worker assigned to the queue fetches the task, its
   status becomes *Running*. The task can now be tracked and its results visualized.

## Modifying Tasks

Tasks whose status is *Draft* are editable (see the [user properties](#user-properties) exception). In the **Klique
Web UI**, edit any of the following

* [Source code](#source-code)
* [Output destination for artifacts](#output-destination)
* [Default container](#default-container)
* [Hyperparameters](#hyperparameters) - Parameters, TensorFlow Definitions, command line options, environment variables, and user-defined properties

:::note
User parameters are editable in any task, except those whose status is *Published* (read-only).
:::

* [Configuration objects](#configuration-objects) - Task model description
* [Initial weight input model](#initial-weights-input-model)
* [Output destination for artifacts storage](#output-destination)
* [Dataviews](#dataviews) - For tasks that use [Hyper-Datasets](https://clear.ml/docs/latest/docs/hyperdatasets/dataviews/), the selected Dataview, 
Dataset versions, frame filtering, label mapping, class label enumeration, and input frame iteration controls

### Execution Details

#### Source Code

Modify code execution by changing any of the following:

* Repository, commit (select by ID, tag name, or choose the last commit in the branch), script, working directory, 
and/or binary.
* The Python packages to be installed and/or their versions - Edit the package list, or clear it to have the 
  Klique Executor either not install any packages or use an existing repo `requirements.txt` file. If the task is based on a 
run in which the packages used were eventually different to the ones originally specified, you can easily or reset the 
packages to originally recorded values ("Original Pip").
* Uncommitted changes - Edit or clear all.

**To modify the source code**, hover over the relevant sections in the **EXECUTION** tab to access Edit, Clear/Discard, 
and/or Reset functions.

#### Default Container
Select a pre-configured container that the [Klique Executor](../orchestrator.md#execution) will use to remotely execute this task (see [Building Task Execution Environments in a Container](https://clear.ml/docs/latest/docs/getting_started/clearml_agent_base_docker/)).

**To add, change, or delete a default container:**

* In **EXECUTION** **>** **CONTAINER** **>** hover **>** **EDIT** **>**
  Enter the default container image.

:::important 
For `clearml-agent` to execute the task in a container, the agent must be running in 
[Docker Mode](https://clear.ml/docs/latest/docs/clearml_agent/clearml_agent_execution_env/#docker-mode):

```bash
clearml-agent daemon --queue <execution_queue_to_pull_from> --docker [optional default container image to use]
```

:::

#### Output Destination

Set an output destination for model checkpoints (snapshots) and other artifacts. Examples of supported types of destinations
and formats for specifying locations include:

* A shared folder: `/mnt/share/folder`
* S3: `s3://bucket/folder`
* Non-AWS S3-like services (e.g. MinIO): `s3://host_addr:port/bucket`. **Note that port specification is required**. 
* Google Cloud Storage: `gs://bucket-name/folder`
* Azure Storage: `azure://<account name>.blob.core.windows.net/path/to/file`

**To add, change, or delete an artifact output destination:**

* In **EXECUTION** **>** **OUTPUT** > **DESTINATION** **>** hover **>** **EDIT** **>** edit **>** **SAVE**.

:::note[Set Output Destination for Artifacts]
Also set the output destination for artifacts in code (see the `output_uri` parameter of the
[`Task.init`](https://clear.ml/docs/latest/docs/references/sdk/task#taskinit)
method), and in the `clearml.conf`
for all tasks (see [`default_output_uri`](https://clear.ml/docs/latest/docs/configs/clearml_conf#config_default_output_uri)).
:::

### Configuration

#### Hyperparameters

Add, change, or delete hyperparameters, which are organized in the **Web UI** in the following sections:

* **Args** - Automatically logged argument parser parameters (e.g. `argparse`, `click`, `hydra`).

* **TF_DEFINE** - TensorFlow definitions (from code, TF_DEFINEs automatic logging).

* **General** - Parameter dictionaries (from code, connected to the Task by calling [`Task.connect()`](https://clear.ml/docs/latest/docs/references/sdk/task#connect)).

* Environment variables - Tracked if variables were listed in the `CLEARML_LOG_ENVIRONMENT` environment variable 
or the [`sdk.development.log_os_environments`](https://clear.ml/docs/latest/docs/configs/clearml_conf#log_env_var) field of the `clearml.conf` file.

* Custom named parameter groups (see the `name` parameter in [`Task.connect`](https://clear.ml/docs/latest/docs/references/sdk/task#connect)).

**To add, change, or delete hyperparameters:**

* In the **CONFIGURATION** tab **>** **HYPERPARAMETERS** **>** parameter group **>** hover **>** click **EDIT** **>** add, change,
  or delete keys and/or values **>** click **SAVE**.

#### User Properties

User properties allow storing any descriptive information in key-value pair format. They are editable in any task,
except those whose status is *Published* (read-only).

**To add, change, or delete user properties:**

* In **CONFIGURATION** **>** **USER PROPERTIES** **>** **Properties** **>** hover **>** click **EDIT** **>** add, change, or delete
  keys and/or values **>** click **SAVE**.

#### Configuration Objects

**To add, change, or delete the Task model configurations:**

* In **CONFIGURATION** **>** **CONFIGURATION OBJECTS** **>** object name **>** hover **>** **EDIT** or **CLEAR** (if the
  configuration is not empty).

### Artifacts

#### Initial Weights Input Model

Edit model configuration and label enumeration, choose a different initial input weight model for the same project or any
other project, or remove the model.

:::note
The models are editable in the **MODELS** tab, not the **TASKS** tab. Clicking the model name hyperlink shows the
model in the **MODELS** tab.
:::

**To select a different model:**

1. In **ARTIFACTS** **>** **Input Model** **>** Hover and click **EDIT**.
1. If a model is associated with the task, click .
1. In the **SELECT MODEL** dialog, select a model from the current project or any other project.

**To edit a model's configuration or label enumeration:**

1. Click the model name hyperlink. The model details appear in the **MODELS** tab.
1. Edit the model configuration or label enumeration:

    * Model configuration - In the **NETWORK** tab **>** Hover and click **EDIT**. **>** CLick **EDIT** or **CLEAR** (to
      remove the configuration).

       Users can also search for the configuration (hover over the configuration textbox, the search box appears) and copy the
      configuration to the clipboard (hover and click ).

    * Label enumeration - In the **LABELS** tab **>** Hover and click **EDIT** **>** Add, change, or delete label
      enumeration key-value pairs.

**To remove a model from a task:**

* Hover and click **EDIT** **>** Click 

### Dataviews

For a task that uses [Hyper-Datasets](https://clear.ml/docs/latest/docs/hyperdatasets/dataviews/), modify its Dataviews in the **DATAVIEWS** tab (see 
[Task Dataviews](webapp_exp_track_visual.md#dataviews)).

#### Selecting Dataviews

**To choose a Dataview**, do any of the following:

* Create a new Dataview  
    
    * Click **+** and then follow the instructions below to select Hyper-Dataset versions, filter frames, map labels (label translation), 
      and set label enumeration and iteration controls.
      
* Select a different Dataview already associated with the task. 
    
    * In the **SELECTED DATAVIEW** list, choose a Dataview.  
  
* Import a different Dataview associated with the same or another project.  
    
    * Click  (**Import dataview**) and then 
      select **Import to current dataview** or **Import as aux dataview**.
      
:::note
After importing a Dataview, it can be renamed and/or removed. 
:::

#### Selecting Dataset Versions

To input data from a different data source or different version of a data source, select a different Dataset version used 
by the Dataview.

**To select Dataset versions for input data:**

1. In the **INPUT** area, click **EDIT**.
1. Do any of the following:

    * Add a Dataset version - Input frames from another version of another Dataset.
    
        * Click **+** 
    
        * Select a Dataset and a Dataset version 
        
    * Remove a Dataset version - Do not input frames from a Dataset version.
    
   Select frames from as many Dataset versions as are needed.

1. Click **SAVE**.
   
#### Filtering Frames

Filtering of SingleFrames iterated by a Dataview for input to the task is accomplished by frame filters. 
For more detailed information, see [Filtering](https://clear.ml/docs/latest/docs/hyperdatasets/dataviews/#filtering).

**To modify frame filtering:**

1. In the **FILTERING** area, click **EDIT**.
1. For each frame filter:

    1. Select the Hyper-Dataset version to which the frame filter applies.
    1. Add, change, or remove any combination of the following rules:
    
        * ROI rule - Include or exclude frames containing any single ROI with any combination of labels in the Dataset 
          version. Specify a range of the number of matching ROI (instances) per frame, and a range of confidence levels.
        * Frame rule - Filter by frame metadata key-value pairs, or ROI labels.
        * Source rule - Filter by frame `source` dictionary key-value pairs.
    
    1. Optionally, debias input data by setting ratios for frames returned by the Dataview for each frame filter. These 
       ratios allow adjusting an imbalance in input data.

1. Click **SAVE**.
    
#### Mapping Labels (Label Translation)

Modify the ROI label mapping rules, which translate one or more input labels to another label for the output model. Labels 
that are not mapped are ignored. 

**To modify label mapping:**

1. In the **MAPPING** section, click **EDIT**
    * Add (**+**) or edit a mapping:
      
        1. Select the Hyper-Dataset and version whose labels will be mapped.
      
        1. Select one or more labels to map.
      
        1. Select or enter the label to map to in the output model.
    
    * Remove () a mapping.

1. Click **SAVE**

#### Label Enumeration

Modify the label enumeration assigned to output models.

**To modify label enumeration:**

1. In the **LABELS ENUMERATION** section, click **EDIT**.

    * Add (**+**) or edit an enumeration:
    
        * Select a label and then enter an integer for it.
    
    * Remove ()  an enumeration.

1. Click **SAVE**.

#### Iteration Controls

Modify the frame iteration performed by the Dataview to control the order, number, timing, and reproducibility of frames 
for training.

For more detailed information, see [Iteration Control](https://clear.ml/docs/latest/docs/hyperdatasets/dataviews/#iteration-control).

**To modify iteration controls:**

1. In the **ITERATION** sections, click **EDIT**.

1. Select the **ORDER** of the SingleFrames returned by the iteration, either:

    * **Sequential** - Iterate SingleFrames in sorted order by context ID and timestamp.
    * **Random** - Iterate SingleFrames randomly using the random seed you can set (see Random Seed below).
    
1. Select the frame **REPETITION** option, either:

    * **Use Each Frame Once**  

    * **Limit Frames**    
         
    * **Infinite Iterations**
        
1. Select the **RANDOM SEED** - If the task is rerun and the seed remains unchanged, the frame iteration is the same.

1. For video, enter a **CLIP LENGTH** - For video data sources, in the number of sequential frames from a clip to iterate.

1. Click **SAVE**.
