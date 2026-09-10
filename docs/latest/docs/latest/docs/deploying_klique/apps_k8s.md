# Application Installation on Kubernetes

> Installing Klique applications on a Kubernetes-based Klique Server, including air-gapped image registry conversion and uploading application packages.

Klique Applications are plugins that extend the functionality of the Klique. They enable users 
to: 
* Manage ML workloads 
* Automate recurring workflows--no code required

Applications are installed on top of the Klique Server and are provided by the Klique team.

## Requirements

- Python 3 installed on your local machine to run the provided installation scripts
- A Klique Server is up and running with `applications.enabled` set to `true` in the server's `control-plane-values.override.yaml` file.
- Applications package provided by Klique, including the following scripts:
  - `convert_image_registry.py`
  - `upload_apps.py`
- API credentials (`<ACCESS_KEY>` and `<SECRET_KEY>`) generated via 
  the Klique UI (**Settings > Workspace > API Credentials > Create new credentials**). Make sure these credentials 
  belong to an admin user or a service user with admin privileges. For more information, see [API Credentials](../webapp/settings/webapp_settings_profile.md#api-credentials). 

## Installation

To install the Klique Applications on a newly installed Klique Server: 

### Download and Extract

Download the applications package using the URL provided by Klique:

```bash
wget -O apps.zip "<Klique applications configuration download url>"
unzip apps.zip
```

### Adjust Application Docker Images Location (Air-Gapped Systems)

Klique Applications use pre-built Docker images from the Klique DockerHub repository. If you are 
installing in an air-gapped system, these images must be available in your internal docker registry. You must specify 
the docker images location before installing the applications.

Use the provided script to modify the application zip files to reference your internal registry:

```bash
python convert_image_registry.py \
--apps-dir "<PATH_TO_APPS_DIR>" \
--artifactory <LOCAL_REGISTRY>/clearml_apps
```

The script will:
* Update the application zip files to point to the new registry
* Output the list of images that need to be copied to the local registry. For example:

   ```
   > make sure `allegroai/clearml-apps:hpo-1.10.0-1062` was added to `local_registry/clearml-apps`
   ```

### Upload Applications to Klique Server

Use `upload_apps.py` to upload the application packages to the Klique Server.

To see available options, run `python3 upload_apps.py --help`.

**Upload a Single application:**

```bash
python3 upload_apps.py --host <APISERVER_URL> --key <ACCESS_KEY> --secret <SECRET_KEY> --command upload --files "YOUR_APP.zip"
```

**Upload Multiple applications:**

```bash
python3 upload_apps.py --host <APISERVER_URL> --key <ACCESS_KEY> --secret <SECRET_KEY> --command upload --dir "<PATH_TO_APPS_DIR>" -ml
```

## Application Instance Visibility 

By default, all users can view all application instances in the Klique UI. You can configure different visibility 
policies globally or for specific applications, for example to restrict access to the application owner or allow sharing 
only with selected user groups.

For more information, see [Application Instance Visibility Policy](app_visibility_policy.md).
