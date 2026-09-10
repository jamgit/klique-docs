# Launching a Remote IDE

> Launching an SSH, JupyterLab, VS Code, or virtual machine remote desktop session on managed compute, its launch configuration, and returning to a previous session's workspace.

You can launch an interactive development session on managed compute, with the resources a laptop can't offer,
using the following Klique apps:

* [SSH Session](../webapp/applications/apps_ssh_session.md) - A detached, interactive SSH session.
* [Jupyter Lab](../webapp/applications/apps_jupyter_lab.md) - A JupyterLab server, reachable in the browser.
* [VS Code](../webapp/applications/apps_vscode.md) - A VS Code server, reachable in the browser.
* [Virtual Machine Remote Desktop](../webapp/applications/apps_vm_desktop.md) - A virtual machine with a full remote
  desktop or SSH session.

For the underlying capability, see
[Remote Development Environment](../orchestration_engine/gpuaas_remote_ides.md).

![Jupyter Lab Dashboard](../img/apps_jupyter_lab.png#light-mode-only)
![Jupyter Lab Dashboard](../img/apps_jupyter_lab_dark.png#dark-mode-only)

## Launch a Session

Each app instance is set up using a [Klique Orchestrator](../orchestrator.md): select a queue and the
orchestrator servicing that queue builds the remote environment. From there, the launch form covers the
environment to build (a git repository to clone, a container image or VM type, packages to install) and the
queue and idle timeout to run it under. 

## Work in the Session

Once the environment is ready, the app instance dashboard displays how to connect: an SSH command, a link to open
the IDE, or a browser link to the remote desktop. From there, the dashboard tracks resource utilization (CPU, GPU)
and shows the console log of the session's setup and activity, and shuts the session down after a configured
maximum idle time.

## Return to a Session

After a session shuts down, you can launch another one that returns to its previous state instead of starting from
a blank environment. That new instance doesn't resume the old session: it's a fresh environment loaded with the old
one's files, restored from its persistent workspace.

Point a new session at a previous one's workspace in one of the following ways:

* When configuring a new instance, enter the previous session's ID under the launch form's restore-workspace field.
* **Clone** the instance using its context menu, which opens a new launch form prefilled with its configuration,
  including the workspace to restore.
* **Relaunch** the instance using its context menu, which stops the running instance and launches a fresh one with its
 configuration directly, removing the original once the new instance reaches a terminal status.
