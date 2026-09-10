# Klique S3 Presign Service

> The Klique Presign Service, which lets WebApp users access S3-like storage from the browser without configuring S3 credentials.

The Klique Presign Service is a secure service that allows Klique WebApp users to access data stored in S3-like storage 
from their browser seamlessly, without requiring S3 credentials to be configured in the Klique WebApp. 

Klique generates and redirects pre-signed storage URLs to the user's browser session, enabling direct access to S3 data without exposing credentials.

When configured, the WebApp automatically redirects requests for matching storage URLs (like `s3://...`) to the 
Presign Service. The service:

* Authenticates the user with Klique.
* Generates a temporary, secure (pre-signed) S3 URL.
* Redirects the user's browser to the URL for direct access.

This setup ensures secure access to S3-hosted data.

:::important
Please note, the Klique Presign service is an internal service and is not an alternative to AWS Presign and does not serve the same purpose.
:::

## Prerequisites

- A Klique Server is up and running.
- API credentials (`<ACCESS_KEY>` and `<SECRET_KEY>`) generated via 
  the Klique UI (**Settings > Workspace > API Credentials > Create new credentials**). For more information, see [API Credentials](../../webapp/settings/webapp_settings_profile.md#api-credentials).

  :::note
  Make sure these credentials belong to an admin user or a service account with admin privileges.
  :::
 
- The worker environment must be able to access the Klique Server over the same network.
- A DockerHub token to access the OCI enterprise Helm charts and Docker images

## Installation

### Log into the Klique OCI Registry

Log into the Klique OCI registry:

```bash
echo <DOCKERHUB_TOKEN> | helm registry login docker.io --username kliqueenterprise --password-stdin
```

### Prepare Configuration

Create a `presign-service.override.yaml` file (make sure to replace the placeholders):

```yaml
imageCredentials:
  password: "<DOCKERHUB_TOKEN>"
klique:
  apiServerUrlReference: "<CLEARML_API_SERVER_URL>"
  apiKey: "<ACCESS_KEY>"
  apiSecret: "<SECRET_KEY>"
ingress:
  enabled: true
  hostName: "<PRESIGN_SERVICE_URL>"
```

### Deploy the Helm Chart

Install the `presign-service` Helm chart in the same namespace as the Klique server:

```bash
helm upgrade -i -n klique presign-service oci://docker.io/kliqueai/presign-service -f presign-service.override.yaml
```

### Update Klique Server Configuration

Enable the Klique Server to use the Presign Service by editing your `control-plane-values.override.yaml` file. 
Add the following to the `apiserver.extraEnvs` section (make sure to replace `<PRESIGN_SERVICE_URL>`). 

```yaml
apiserver:
  extraEnvs:
    - name: KLIQUE__SERVICES__SYSTEM__COMPANY__DEFAULT__SERVICES
      value: "[{\"type\":\"presign\",\"url\":\"https://<PRESIGN_SERVICE_URL>\",\"use_fallback\":\"false\",\"match_sets\":[{\"rules\":[{\"field\":\"\",\"obj_type\":\"\",\"regex\":\"^s3://\"}]}]}]"
```

Apply the changes with a Helm upgrade command.
