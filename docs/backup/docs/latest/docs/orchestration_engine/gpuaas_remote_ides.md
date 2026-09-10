# Remote Development Environment

> Klique's remote development environment applications, which provide interactive IDE and desktop sessions on managed compute.

Model development often needs more compute than a local machine can offer. A sizeable dataset, a GPU-hungry training
run, or heavy preprocessing quickly outgrows a laptop, and moving that work to a remote server usually means giving up
the fast, interactive loop of developing in your own editor.

Klique's remote development environments remove that tradeoff. They launch a full interactive development environment on
governed cloud or on-premises compute and connect it to the tools you already use, so you get the power of a remote GPU
machine with the responsiveness of local development. Every session draws from the same
[managed compute](orchestration_engine.md) as the rest of the platform, with no infrastructure to provision or
Kubernetes to configure.

## Development applications

Klique's remote development applications start an environment on a remote machine and connect to it over a secure,
encrypted connection, letting you work against remote resources from your preferred editor:

* [SSH Session](../webapp/applications/apps_ssh_session.md) - A detached, interactive SSH session that provides a full
  development environment on a remote machine.
* [JupyterLab](../webapp/applications/apps_jupyter_lab.md) - A JupyterLab session running on remote compute, ready in
  the browser.
* [VS Code](../webapp/applications/apps_vscode.md) - A VS Code session served from a remote machine.
* [Virtual Machine Remote Desktop](../webapp/applications/apps_vm_desktop.md) - A virtual machine on remote compute with
  a full remote desktop or SSH session, including workspace storage and sync across sessions.

![VS code app](../img/apps_vs_code.png#light-mode-only)
![VS code app](../img/apps_vs_code_dark.png#dark-mode-only)
