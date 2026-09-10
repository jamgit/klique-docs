# Kubernetes

> Step-by-step instructions for installing the Klique Server control plane on Kubernetes using the Klique Helm chart.

This guide provides step-by-step instructions for installing the Klique Server (control-plane) in a Kubernetes cluster.

The Klique Server includes the `apiserver`, `fileserver`, and `webserver` components. 
The package also includes MongoDB, ElasticSearch, and Valkey as Helm dependencies.

## Prerequisites

To deploy a Klique Server, ensure the following components and configurations are in place:

- Kubernetes Cluster: A standard Kubernetes cluster is recommended for optimal GPU support.
- CLI Tools: `kubectl` and `helm` must be installed and configured.
- Ingress Controller: An Ingress controller (e.g., `nginx-ingress`) is required. If exposing services externally, configure 
  a LoadBalancer-capable solution (e.g. `MetalLB`).
- Server and workers that communicate on HTTP/S (ports 80 and 443). Additionally, the TCP session feature requires a 
  range of ports for TCP traffic based on your configuration (see [AI App Gateway installation](appgw_install_k8s.md)).
- DNS Configuration: A domain with subdomain support is required, ideally with trusted TLS certificates. All entries must 
  be resolvable by the Ingress controller. Example subdomains:
  - Server:
    - `api.<BASE_DOMAIN>`
    - `app.<BASE_DOMAIN>`
    - `files.<BASE_DOMAIN>`
  - Worker:
    - `router.<BASE_DOMAIN>`
    - `tcp-router.<BASE_DOMAIN>` (optional, for TCP sessions)
- Storage: A configured StorageClass and an accessible storage backend.
- A DockerHub token to access the Klique OCI Helm charts and Docker images (`<DOCKERHUB_TOKEN>`)

### Recommended Cluster Specifications

For optimal performance, a Kubernetes cluster with at least 3 nodes is recommended, each provisioned with:

- 8 vCPUs
- 32 GB RAM
- 500 GB storage

## Installation

### Log into the Klique OCI Registry

Login to the Klique OCI registry:

```bash
echo <DOCKERHUB_TOKEN> | helm registry login docker.io --username kliqueenterprise --password-stdin
```

### Prepare Values

Create a `control-plane-values.override.yaml` file with the following content:

:::note
In the following configuration, replace the `<BASE_DOMAIN>` placeholders with a valid domain that will have records 
pointing to the cluster's Ingress Controller. This will be the base domain for reaching your Klique installation.
:::

```yaml
imageCredentials:
  password: "<DOCKERHUB_TOKEN>"
controlPlane:
  cookieDomain: "<BASE_DOMAIN>"
apiserver:
  ingress:
    enabled: true
    hostName: "api.<BASE_DOMAIN>"
  service:
    type: ClusterIP
fileserver:
  ingress:
    enabled: true
    hostName: "files.<BASE_DOMAIN>"
  service:
    type: ClusterIP
webserver:
  ingress:
    enabled: true
    hostName: "app.<BASE_DOMAIN>"
  service:
    type: ClusterIP
applications:
  enabled: true
```

### Install the Chart

Install the `control-plane` Helm chart using the previous values override file.

```bash
helm upgrade -i -n klique control-plane oci://docker.io/kliqueai/control-plane --create-namespace -f control-plane-values.override.yaml 
```

:::important[CRDs on upgrade]
The chart installs its Custom Resource Definitions (CRDs) on the initial installation, but Helm does not update
them on `helm upgrade`. Before upgrading an existing installation, apply the latest CRDs from the chart:

```bash
helm show crds oci://docker.io/kliqueai/control-plane --version <CHART_VERSION> | kubectl apply -f -
```
:::

## Additional Configuration Options

:::note
You can view the full set of available and documented values of the chart by running the following command:

```bash
helm show readme oci://docker.io/kliqueai/control-plane
# or
helm show values oci://docker.io/kliqueai/control-plane
```
:::

### Default Secret Values

For improved security, all the internal credentials are auto-generated randomly and stored in a Secret in 
Kubernetes.

If you need to define your own credentials to be used instead, replace the default key and secret values in `control-plane-values.override.yaml`.

```yaml
controlPlane:
  # Replace the following values to use custom internal credentials.
  apiserverKey: "<generated_key>"
  apiserverSecret: "<generated_key>"
  fileserverKey: "<generated_key>"
  fileserverSecret: "<generated_key>"
  secureAuthTokenSecret: "<generated_key>"
  secureAuthTokenAppGatewaySecret: "<generated_key>"
  testUserKey: "<generated_key>"
  testUserSecret: "<generated_key>"
```

In a shell, if `openssl` is installed, you can use this simple command to generate random strings suitable as keys and secrets:

```bash
openssl rand -hex 16
```

### Fixed Users

Enable and configure simple login with username and password in `control-plane-values.override.yaml`. This is useful for simple PoC 
installations. This is an optional step in case the SSO (Identity provider) configuration is not performed.

Please note that this setup is not ideal for multi-tenant setups as fixed users will only be associated with the default tenant.

```yaml
apiserver:
  additionalConfigs:
    apiserver.conf: |
      auth {
        fixed_users {
          enabled: true
          pass_hashed: false
          users: [
            {
              username: "my_user"
              password: "my_password"
              name: "My User"
              admin: true
            },
          ]
        }
      }
```

### Internal Database Authentication

The `control-plane` Helm chart deploys its internal DB components (MongoDB, ElasticSearch, and Valkey) through 
dependency charts. This section applies to these bundled databases (see 
[Using External Databases with Klique](#using-external-databases-with-klique) for external instances).

MongoDB is managed by the MongoDB Controllers for Kubernetes operator and is always authenticated: the chart 
creates a `mongodb-root` Secret with a random password for the `root` user. To use your own password instead, 
create the Secret in the release namespace before installing the chart:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mongodb-root
stringData:
  connectionString: "mongodb://root:<PASSWORD>@mongodb-replica-set-svc"
  password: "<PASSWORD>"
```

ElasticSearch and Valkey deploy with authentication disabled by default. Enable it in 
`control-plane-values.override.yaml`; the Klique services pick up the credentials automatically, and a random 
password is generated when one is not set:

```yaml
# Enable ElasticSearch Auth
elasticsearch:
  security:
    enabled: true
    elasticPassword: "MyStrongPassword123"
# Enable Valkey Auth
valkey:
  auth:
    enabled: true
    password: "MyStrongPassword123"
```

### Using External Databases with Klique

The Klique Server can be configured to use external services for the ElasticSearch, MongoDB, and Valkey databases, 
instead of those included in the server bundle. Note that if you use external database instances, Klique will not manage 
the database's lifecycle or version. For MongoDB specifically, Klique disables internal version checks (`CLEARML__apiserver__mongo__ensure_db_version_on_startup=false`).

As a result, you are fully responsible for:

* Provisioning and maintaining the database instance
* Applying security updates and version upgrades
* Ensuring availability, backups, and disaster recovery

Before upgrading Klique, always consult the Klique release notes and documentation to verify which DB versions 
are supported. Running unsupported versions may cause Klique to fail or behave unexpectedly.

To connect external databases, configure the `externalServices` section in the `control-plane-values.override.yaml` file:

```yaml
externalServices:
  # Existing ElasticSearch connection string if elasticsearch.enabled is false
  elasticsearchConnectionString: "[{\"host\":\"es_hostname1\",\"port\":9200},{\"host\":\"es_hostname2\",\"port\":9200},{\"host\":\"es_hostname3\",\"port\":9200}]"
  # Existing MongoDB connection string for AUTH to use if mongodb-kubernetes.enabled is false
  mongodbConnectionStringAuth: "mongodb://mongodb_hostname:27017/auth"
  # Existing MongoDB connection string for BACKEND to use if mongodb-kubernetes.enabled is false
  mongodbConnectionStringBackend: "mongodb://mongodb_hostname:27017/backend"
  # Existing Valkey Hostname to use if valkey.enabled is false
  valkeyHost: "valkey_hostname"
  # Existing Valkey Port to use if valkey.enabled is false
  valkeyPort: 6379
```

### Using External Storage Instead of the Fileserver

The Klique Server can be configured to use external object storage (e.g. AWS S3, MinIO, GCS, Azure Blob) 
instead of the bundled file server. In this setup, artifact and model URLs reference the external storage directly. The 
Klique control plane, the [Klique Orchestrator](../orchestrator/deployment_k8s.md#installing-the-orchestrator), 
and other Klique clients must all be configured to use the same new external storage URL. 

#### Control Plane Configuration

In the server's `control-plane-values.override.yaml`, disable the bundled fileserver and set the external storage URL 
references used by the control plane components:

```yaml
fileserver:
  enabled: false

applications:
  fileServerUrlReferenceOverride: "<EXTERNAL_STORAGE_URL>"

webserver:
  displayedServerURLs:
    apiserver: "<APISERVER_URL>"
    fileserver: "<EXTERNAL_STORAGE_URL>"
  extraEnvs:
    - name: WEBSERVER__fileBaseUrl
      value: '"<EXTERNAL_STORAGE_URL>"'
```

#### Orchestrator Configuration

In the orchestrator's `orchestrator-values.override.yaml`, point its fileserver reference to the same external storage 
URL:

```yaml
orchestratork8sglue:
  fileServerUrlReference: "<EXTERNAL_STORAGE_URL>"
```

#### Client Configuration

To ensure `clearml` clients (SDK and `clearml-agent`-launched tasks) upload artifacts, models, and debug samples to the correct 
location, configure the credentials and default output URI. The recommended approach is via an [Administrator Vault](../user_management/admin_vaults.md),
though credentials can also be set directly in the `clearml.conf` file on each client machine. 

For example, the following configures credentials and sets a default output URI for an S3-compatible endpoint:  

```hocon
sdk {
    aws {
        s3 {
            credentials: [
                {
                    host: "s3://<EXTERNAL_STORAGE_URL>"
                    key: "<EXTERNAL_STORAGE_KEY>"
                    secret: "<EXTERNAL_STORAGE_SECRET>"
                    multipart: false
                    secure: true
                    verify: false
                }
            ]
        }
    }
}

sdk.development.default_output_uri = "s3://<EXTERNAL_STORAGE_URL>"
```

For configuration examples covering other storage backends (Azure, GCS, non-AWS S3 endpoints, and more), see [Storage](https://clear.ml/docs/latest/docs/integrations/storage/). 

## Monitoring

Monitoring your Klique deployment is recommended to ensure service availability and detect performance or resource 
issues early. For monitoring guidelines and recommended metrics, see [Monitoring](extra_configs/monitoring_k8s.md).

## Next Steps

After installing the Klique Server, you can enhance your deployment by enabling optional services that 
extend Klique's capabilities for scheduling, interactivity, authentication, and more.

### Applications Installation
Applications are plugins that extend the functionality of the Klique Server. They enable users 
to manage ML workloads and automate recurring workflows--no code required

Applications are installed on top of the Klique Server and are provided by the Klique team.

For more information, see the [Application Installation guide](apps_k8s.md).

### Klique Orchestrator and Executor

Klique Orchestrator and Executor enable scheduling and execution of distributed workloads (Tasks) on your Kubernetes cluster.

See the [installation guide](../orchestrator/deployment_k8s.md#installing-the-orchestrator).

### AI Application Gateway

The AI App Gateway enables secure, authenticated access to Klique application endpoints such as model serving or IDE workloads
based on Klique user permissions. It routes HTTPS traffic from users to running pods on the cluster.

See the [AI Application Gateway installation guide](appgw_install_k8s.md).

## Advanced Options
### GPU Operator

Deploy the NVIDIA GPU Operator to use NVIDIA GPUs in Klique.

See the [GPU Operator guide](../orchestrator/fractional_gpus/gpu_operator.md) in Klique Configuration.

### Fractional GPU Support

Share physical GPUs across workloads. Available GPU fractioning methods:

* Cluster Dynamic MIG Operator (CDMO): Manage GPU fractions using NVIDIA MIGs. See the [CDMO guide](../orchestrator/fractional_gpus/cdmo.md).
* Cluster Fractional GPU Injector (CFGI): Use fractional (non-MIG) GPU slices for efficient resource sharing. See the [CFGI guide](../orchestrator/fractional_gpus/cfgi.md).
* Mixed Deployments: Deploy both CDMO and CFGI in clusters with diverse GPU types. Use the NVIDIA GPU Operator to handle
  mixed hardware setups. See the [CDMO and CFGI guide](../orchestrator/fractional_gpus/cdmo_cfgi_same_cluster.md).

### Multi-Tenant Setup

Run multiple isolated tenants on a single Klique Server deployment, each with its own configuration and user namespaces.

See the [Multi-Tenant Service guide](multi_tenant_k8s.md).

### Identity Provider Integration

Configure at least one authentication method before onboarding real users: an identity provider for SSO, or
[fixed users](#fixed-users) for simple deployments.

See [Identity Providers](../user_management/identity_providers.md) for provider-specific
setup steps.

### Klique Presign Service

The Klique Presign Service securely generates pre-signed storage URLs for authenticated users. This is an optional
service you can add at any time.

See [Klique S3 Presign Service](extra_configs/presign_service.md).

### Install with a Non-Root User

In some Helm charts, you will find a values file called `values-enterprise-non-root-privileged.yaml` to be used for a 
non-root installation.

These values are for Enterprise versions only, and they need to be adapted to specific infrastructure needs. The 
`containerSecurityContext` is related to the Kubernetes distribution used/configuration and will need to be customized accordingly.
