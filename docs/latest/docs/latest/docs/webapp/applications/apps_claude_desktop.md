# Claude Desktop

> The Claude Desktop application, which launches the Claude Desktop AI assistant in a browser-accessible remote desktop.

The Claude Desktop application allows you to launch the Claude Desktop AI assistant in a browser-accessible remote desktop, 
letting you use it as if it were running locally.

The Claude Desktop app offers persistent workspace support, allowing you to store and restore your session's workspace 
across sessions, as well as multi-file drag-and-drop upload for bringing files into the session.

The Claude Desktop session is set up using a [Klique Orchestrator](../../orchestrator.md). When configuring an app instance,
select a queue, and the Klique Orchestrator servicing that queue will set up the remote environment. When setup is complete, the
app instance dashboard displays the session ID and a link to open the Claude Desktop session ready to use.

The application monitors the session's activity and shuts down if it is inactive for a specified maximum idle time.

Once you start a Claude Desktop instance, you can view the following information in its dashboard:

* App status indicator
  *  - Claude Desktop session is active
  *  - Claude Desktop session is setting up
  *  - Claude Desktop session is idle
  *  - Claude Desktop session is stopped
* Restored workspace - If a previous session's workspace was restored, this will display that session's ID
* Current session ID
* Open Claude - Link to the Claude Desktop session
* Token utilization over time:
  * Tokens (input)
  * Tokens (output)
  * Tokens cumulative (input)
  * Tokens cumulative (output)
  * Tokens cumulative (total)
* Resource utilization over time:
  * CPU Utilization
  * GPU Utilization
* Console - The console log shows the instance's activity, including setup progress, status changes, and error messages

:::tip[Embedding Visualizations]
You can embed plots from the app instance dashboard into [Reports](../webapp_reports.md). Klique 
also supports embedding resources in third-party platforms that support embedded content (e.g. Notion). These visualizations 
are updated live as the app instance(s) updates. Hover over the plot and click  
to copy the embed code, and navigate to a report to paste the embed code.
:::

## Claude Desktop Instance Configuration
When configuring a new Claude Desktop instance, you can fill in the required parameters or reuse the configuration of 
a previously launched instance.  

Launch an app instance with the configuration of a previously launched instance using one of the following options:
* Cloning a previously launched app instance will open the instance launch form with the original instance's 
configuration prefilled.
* Importing an app configuration file. You can export the configuration of a previously launched instance as a JSON file 
when viewing its configuration.

The prefilled instance launch form can be edited before starting the new app instance. 

To configure a new app instance, click `Launch New`  
to open the app's instance launch form.

### Configuration Options

:::note
Administrators can [customize](../../deploying_klique/app_launch_form_custom.md) the launch form and 
modify field names and/or available options and defaults. 

This section describes the default configuration provided by Klique.
:::

* **Compute Resource (Queue)** – The [Queue](https://clear.ml/docs/latest/docs/fundamentals/agents_and_queues/#what-is-a-queue) serviced by the 
  Klique Orchestrator that will run the Claude Desktop session.
* **Project** - The project where the app instance is created. Access follows project-level 
  permissions: users with read access to the project can use the app.
* **Advanced Options**
  * Session Name - Name for the app instance. This will appear in the instance list
  * Continue from Previous Session - Select the ID of a previous Claude Desktop session to restore
  * Idle Shutdown (Hours) - Maximum idle time after which the app instance will shut down
  * Environment Variables - One `KEY=value` per line. Injected into the session environment. Lines starting with `#` are ignored.
  * Idle Network Threshold (MB/s) - Throughput under which the app instance will be considered idle
  * Idle CPU Threshold (percentage) - CPU utilization under which the app instance will be considered idle
  * Idle GPU Threshold (percentage) - GPU utilization under which the app instance will be considered idle
  * VNC Idle Disconnect Timeout (seconds) - Seconds of no connected VNC clients before the server stops streaming. Default is 600 (10 minutes).
  * Auto-enable Audio & Microphone - Start desktop audio and microphone automatically on load instead of requiring their toggle buttons to be clicked.
