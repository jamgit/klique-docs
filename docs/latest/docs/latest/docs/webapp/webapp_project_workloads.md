# Project Workloads

> A project's Workloads tab, which breaks down compute execution time by resource, project, and user.

A project’s **Workloads** tab summarizes compute resources usage for that project.

This dashboard helps you understand resource consumption by breaking down project tasks execution time according to:
* **Resource** – Aggregation by the queue tasks were executed through.
* **Project** – Aggregation by task project
* **User** – Aggregation by task owner.

For each breakdown, the tab shows:
* Daily execution time over time
* Period Total

The dashboard time span can be modified through the menu at the top right corner.

By default, the dashboard displays execution time for tasks run by a [Klique Executor](../orchestrator.md#execution). To also 
include tasks run locally, enable `Include Local Runs`.

![Workloads](../img/webapp_workloads.png#light-mode-only)
![Workloads](../img/webapp_workloads_dark.png#dark-mode-only)
