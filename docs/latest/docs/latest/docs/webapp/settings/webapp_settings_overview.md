# Settings Page

> The Settings page links to user profile, configuration, and workspace sections for managing personal account settings.

Use the **Settings** page to manage your personal Klique account and workspace settings.

To navigate to the **Settings** page, click the  
button in the top right corner of the web UI screen, then click **Settings**. 

The Settings page consists of the following sections:

* User Settings
  * [Profile](webapp_settings_profile.md#profile) - Your basic user information
  * [Configuration](webapp_settings_profile.md#configuration) - Control general system behavior settings and input storage access credentials
  * [Workspace](webapp_settings_profile.md#workspace)  
      * [API credentials](webapp_settings_profile.md#api-credentials) - Create client credentials for `clearml` and Klique Orchestrator to use 
      * [Configuration vault](webapp_settings_profile.md#configuration-vault) - Define global Klique client settings
        that are applied to all `clearml` and `clearml-agent` instances (which use the workspace's access 
        credentials)

  :::note[Administrator Settings]
  Under the Klique [role-based UI](../role_based_ui.md), administrator settings are
  managed from the **Settings** sidebar item in the AI Admin view, not from this page. See
  [Administrator Settings](../admin_settings.md).
  :::
