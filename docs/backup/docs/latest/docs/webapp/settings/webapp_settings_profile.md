# User Settings

> User Settings covers profile info, UI and storage access configuration, and workspace API credentials, gateway tokens, and configuration vault.

In the **User Settings** section, manage your personal account details, configure system behavior, and set up 
credentials for Klique services.

## Profile
The profile tab presents user information.

**To edit the username:**
1. Hover over the username
1. Click  
1. Change the name
1. Click  button

## Configuration
### Customizing UI Behavior

Under **USER PREFERENCES**, users can set a few web UI options:
* **Show Hidden Projects** - Show Klique infrastructure projects alongside your own projects. Disabled by default. When 
enabled, these projects are labeled with .
* **Don't show examples** - Hide the preloaded example content (project, pipeline, dataset, etc.). 
* **Disable HiDPI browser scale override** - Klique dynamically sets the browser scaling factor for an optimal page layout. 
Disable for default desktop scale. 
* **Don't show pro tips periodically** - Stop showing Klique usage tips on login. Disabled by default.
* **Block running user's scripts in the browser** - Block any user and 3rd party scripts from running anywhere in the 
WebApp. Note that if enabled, the WebApp will not display debug samples, [Hyper-Dataset frame previews](https://clear.ml/docs/latest/docs/hyperdatasets/previews/), 
and embedded resources in [reports](../webapp_reports.md).

  :::note
  Script blocking can be enforced by administrators through server-side configuration.  
  When set, the UI toggle is disabled and users cannot change the setting.
  :::

* **Hide specific container arguments** - Specify which container environment variable values should be hidden in logs. 
When printed, the variable values are replaced with `********`. By default, `CLEARML_API_SECRET_KEY`, `CLEARML_AGENT_GIT_PASS`,
`AWS_SECRET_ACCESS_KEY`, and `AZURE_STORAGE_KEY` values are redacted. To modify the hidden container argument list, click **Edit**.

### Browser Cloud Storage Access
Provide cloud storage access, so the browser will be able to display your cloud stored data, such as debug samples.

In the **Web App Cloud Access** section, enter the following credentials:
* **Bucket** - The name of a Cloud bucket.
* **Key** - The access key.
* **Secret / SAS** - The secret key or shared access signature if required.
* **Token** - For S3 services, session key for temporary credentials (if applicable).
* **AWS Region** - The region for AWS S3.
* **Host (Endpoint)** - The host for non-AWS S3 servers.

### Storage Cleanup Credentials

Provide personal credentials so Klique can delete your task artifacts from cloud storage providers when your tasks are deleted:

* [Google Cloud Storage](#google-cloud-storage)
* [AWS S3 Storage](#aws-s3-storage)
* [Azure](#azure)

These supplement company-wide credentials set by administrators. 

![Storage Cleanup page](../../img/webapp_settings_user_storage.png#light-mode-only)
![Storage Cleanup page](../../img/webapp_settings_user_storage_dark.png#dark-mode-only)

#### Google Cloud Storage
Set up credentials for Google Cloud buckets: 
* Default credentials - These credentials apply to all GCS buckets unless bucket-specific credentials are set.
  * Project - Default Google Cloud Storage project
  * Credentials JSON
* Bucket specific credentials:
  * Bucket 
  * Project
  * Credentials JSON

#### AWS S3 Storage
Set up credentials for S3 protocol storage (i.e. AWS S3, MinIO, etc.): 
* Default credentials - These credentials apply to all buckets unless bucket-specific credentials are set:
  * Access Key - Default access key for the storage service.
  * Secret -  Default secret access key.
  * Access token - Session key for temporary credentials (if applicable).
  * Region -  Default region for all unspecified buckets.
  * Credentials Chain - If selected, use boto3 default [credentials search](https://boto3.amazonaws.com/v1/documentation/api/latest/guide/credentials.html#configuring-credentials) 
  (i.e. look for credentials in environment variables, credential files, and instance metadata services).
* Bucket Specific Credentials: 
  * Bucket - Name of the specific bucket.
  * Region - Region for the bucket.
  * Host - For non-AWS endpoints, the host URL and port number of the specific bucket. Note that port specification 
  is *always* needed (e.g. `my-minio-host:9000`), even for standard ports like 433 for HTTPS (e.g. `my-minio-host:433`) 
  * Secure Host - Select in order to enable TLS. 
  * Verify SSL certificate - Select to enable SSL verification for secure hosts.
  * Access key - Access key for the bucket.
  * Secret - Secret key for the bucket.
  * Access token - The session key for your bucket. This is only needed when you are using temporary credentials.
  * Use Credentials chain - If selected, use boto3 default [credentials search](https://boto3.amazonaws.com/v1/documentation/api/latest/guide/credentials.html#configuring-credentials) 
  (i.e. looks for credentials in environment variables, credential files, and instance metadata services).

#### Azure
Set up credentials for Azure storage containers: 
* Account name - Azure storage account name.
* Account key - Azure storage account key.
* Container name - Name of the specific container.

## Workspace

### API Credentials

Generate API credentials, made up of an access and secret key pair, and insert them into your [configuration file](https://clear.ml/docs/latest/docs/configs/clearml_conf) 
or Jupyter Notebook to grant the ClearML SDK and the Klique Orchestrator API access to the server. 

You can create credentials for any workspace that you are a member of. 

**To create API credentials:**

1. In **WORKSPACE**, expand the desired workspace's panel (self-deployed Klique Server users have one workspace)

1. In **API Credentials**, click **+ Create new credentials**

1. In the dialog that pops up, you can input a label for the new credentials 

The dialog displays new credentials, formatted as a ready-to-copy configuration file section (including server configuration 
information).

![API credentials](../../img/settings_configuration_creation.png#light-mode-only)
![API credentials](../../img/settings_configuration_creation_dark.png#dark-mode-only)

You can edit the labels of credentials in your own workspace, or credentials that you created in other workspaces.

**To edit the credentials label:** hover over the desired credentials, and click  .

You can revoke any credentials in your own workspace, or credentials that you created in other workspaces. Once revoked, 
these credentials cannot be recovered.

**To revoke API credentials:** hover over the desired credentials, and click  .

### AI Application Gateway Tokens

The [AI Application Gateway](../../whats_klique/appgw.md) enables external access to Klique 
tasks and applications. The gateway is configured with an 
endpoint or external address (ingress), accessible from outside Klique.

Generate tokens providing API access to the AI Application Gateway endpoints:

1. Click **Generate a Token**
1. Under `Label`, enter a descriptive name for the token
1. Under `Expiration`, enter the number of days the token should remain valid 
1. Click `Generate`, which creates a token and copies it to your clipboard 

The **AI Application Gateway Table** displays all tokens available to the user, and the following details: 
* Token label
* Creation time 
* Expiration time

To revoke a token, hover over the token's row and click . 

![AI App Gateway Token Table](../../img/settings_token_management.png#light-mode-only)
![AI App Gateway Token Table](../../img/settings_token_management_dark.png#dark-mode-only)

### Changing Your Workspace Name
To change the name of your own workspace, click **Edit workspace name**   
(under API credentials) **>** modify the name **>** click . 

### Adding Users to Your Workspace

To invite a user to your workspace, in the **MEMBERS** section: 
1. Press the **INVITE USERS** button 
1. Input the email in the dialog that pops up
1. Click **ADD** 

A dialog box will appear with an invitation link to send to the invited users. Existing members will receive an in-app 
notification informing them that they can join your workspace. 

After inviting users, the page will redirect to the [Users & Groups](webapp_settings_users.md) section, where the
pending invitations are displayed. 

### Leaving a Workspace

You can leave any workspace you've previously joined (except your personal workspace).

When leaving a workspace, you lose access to its resources (tasks, models, etc.) and your previously created access 
credentials to that workspace are revoked. Tasks and associated artifacts that you logged to that workspace will remain 
in that workspace. You can rejoin the workspace only if you are re-invited.

**To leave a workspace:**

1. In **WORKSPACE**, expand the desired workspace's panel 
1. In **Members** **>** Click **LEAVE WORKSPACE**.

### Configuration Vault

Use the configuration vault to store global Klique configuration entries that can extend the `clearml` [configuration file](https://clear.ml/docs/latest/docs/configs/clearml_conf) 
of any Klique Executors or the ClearML SDK running with your credentials. Productivity tip: Keep the vault disabled while 
you edit your configuration, and enable it when the configuration is ready.

Vault entries will extend the configuration in the `clearml` [configuration file](https://clear.ml/docs/latest/docs/configs/clearml_conf) if they don't
exist, and override values for those already present in the file.

Fill in values using any supported configuration formats: HOCON / JSON / YAML.

**To edit vault contents:**
1. Click **EDIT** or double-click the vault box
1. Insert / edit the configurations in the vault 
1. Press **OK**

**To apply vault contents:**
* Click the toggle atop the vault to enable / disable the configurations
* Once enabled, the configurations will be merged to the configuration file during `clearml` and `clearml-agent` usage 

![Configuration vault](../../img/settings_configuration_vault.png#light-mode-only)
![Configuration vault](../../img/settings_configuration_vault_dark.png#dark-mode-only)
