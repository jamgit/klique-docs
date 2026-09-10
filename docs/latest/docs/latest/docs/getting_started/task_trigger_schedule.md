# Scheduling and Triggering Task Execution

> Scheduling and event-based triggering of task execution using the Task Scheduler and Trigger Manager applications or SDK.

In Klique, tasks can be scheduled and triggered automatically, enabling seamless workflow automation. This section 
 provides an overview of the mechanisms available for managing task scheduling and event-based 
 triggering.

## Task Scheduling 
Task scheduling allows users to define one-shot or periodic executions at specified times and intervals. This 
is useful for:

* Running routine operations such as periodic model training, evaluation jobs, backups, and reports.
* Automating data ingestion and preprocessing workflows.
* Ensuring regular execution of monitoring and reporting tasks.

Klique offers the following scheduling solutions: 
* [**UI Application**](../webapp/applications/apps_task_scheduler.md) - The **Task Scheduler** app
  provides a simple no-code interface for managing task schedules. 

* [**Python Interface**](https://clear.ml/docs/latest/docs/references/sdk/scheduler) - Use the `TaskScheduler` class to programmatically manage
  task schedules. 
  
## Task Execution Triggering  

Klique's trigger manager enables you to automate task execution based on event occurrence in the platform, such as:
* Changes in task status (e.g. running, completed, etc.)
* Publication, archiving, or tagging of tasks, models, or datasets
* Task metrics crossing predefined thresholds

This is useful for:
* Triggering a training task when a dataset has been tagged as `latest` or any other tag
* Running an inference task when a model has been published 
* Retraining a model when accuracy falls below a certain threshold
* And more 

Klique offers the following trigger management solutions: 
* [**UI Application**](../webapp/applications/apps_trigger_manager.md) - The **Trigger Manager** app
  provides a simple no-code interface for managing task triggers .
* [**Python Interface**](https://clear.ml/docs/latest/docs/references/sdk/trigger) - Use the `TriggerScheduler` class to programmatically manage
  task triggers.
