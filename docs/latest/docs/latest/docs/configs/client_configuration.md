# Client Configuration

> The configuration precedence order (CLI arguments, environment variables, configuration vault, clearml.conf) used by the ClearML SDK and Agent.

Klique makes use of the ClearML SDK, ClearML Agent, and their CLI utilities, and these tools share a common
configuration system that can be tailored to your requirements. 

These configuration methods take precedence in the following order (higher overrides lower):

* **Command-line arguments** passed to CLI tools such as [clearml-task](https://clear.ml/docs/latest/docs/apps/clearml_task/),
  [clearml-agent](https://clear.ml/docs/latest/docs/clearml_agent/clearml_agent_ref),
  [clearml-session](https://clear.ml/docs/latest/docs/apps/clearml_session#command-line-options), and
  [clearml-data](https://clear.ml/docs/latest/docs/clearml_data/clearml_data_cli).
* **Environment variables** set in the OS environment.
* **Configuration vault** entries, inserted through the Klique WebApp and applied on top of `clearml.conf`. See
  [Configuration Vault](../webapp/settings/webapp_settings_profile.md#configuration-vault).
* **Configuration file** (`clearml.conf`), the baseline configuration for the ClearML SDK and ClearML Agent.

For more information about configuring ClearML, see
[Configuring ClearML](https://clear.ml/docs/latest/docs/configs/configuring_clearml/) in the ClearML documentation.
