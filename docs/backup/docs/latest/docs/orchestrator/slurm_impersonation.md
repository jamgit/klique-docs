# Slurm User Impersonation

> Configuring a user_map in clearml.conf or a vault so the Slurm Orchestrator submits jobs as the Linux user mapped to each Klique user.

The Slurm Glue is Klique's Orchestrator for Slurm and, by default, submits jobs to Slurm as the Linux user running the
glue process. 

User impersonation allows the orchestrator to submit each job as the Linux user that corresponds to the Klique user who 
enqueued the task.

This is configured by mapping Klique user IDs to Linux usernames.

## Configuration

Add a `user_map` section under `agent.slurm` in your `clearml.conf`:

```yaml
agent {
  slurm {
    user_map {
      "9848dbd7715a4ecba0ef232cd2e3dca3" = "alice"
      "b3f1c2d4e5a6789012345678abcdef01" = "bob"
      "default" = "defaultlinuxuser"
    }
  }
}
```

* Each key is a Klique user ID. To find a Klique user's ID, go to the WebApp under **Settings > Users**.
* Each value is a Linux username on the cluster.
* The `"default"` key defines a fallback for any Klique user not explicitly listed.

### Behavior

* If `user_map` is empty or not provided, no impersonation occurs. Jobs are submitted as the orchestrator process user.
* If a Klique user ID is not in the map and no `"default"` is defined, the task is **rejected and not submitted to slurm**.
* If a task has no associated Klique user ID, the `"default"` entry is used. If a `default` was not provided, the task 
  is rejected.

## Vault Override

You can define `agent.slurm.user_map` in a [Configuration Vault](../webapp/settings/webapp_settings_admin_vaults.md).

A vault’s `user_map` takes precedence over a `clearml.conf` map, including an empty vault map. An empty vault map 
disables impersonation even if `clearml.conf` has a non-empty map. This lets you update user mappings without restarting 
the orchestrator process, and enables per-user overrides by configuring individual personal vaults.
