# Platform Management Center

> How to deploy the Platform Management Center on Kubernetes, including control plane credentials and admin authentication setup.

This guide describes how to deploy the [Platform Management Center](../../whats_klique/management_center.md) on 
Kubernetes.

This procedure assumes you have already set up the [Klique control plane](../k8s.md). 

The Platform Management Center consists of two parts:
1. The platform management server, deployed by the `control-plane` chart next to the API server
2. The UI webserver and a proxy that bridges it to the server, deployed by the `platform-management-center` chart

## Step 1: Enable the Platform Management Server in the Control Plane

Configure the credentials that the platform management server uses for secure access to the Klique API server. 
If `openssl` is available, you can use the following command to generate suitable key and secret:
```
openssl rand -hex 16
```

Add the credentials and enable the server in your Klique Server overrides file (`control-plane-values.override.yaml`):

```yaml
controlPlane:
  platformManagementKey: "<PLATFORM_MANAGEMENT_USER_KEY>"
  platformManagementSecret: "<PLATFORM_MANAGEMENT_USER_SECRET>"

platformManagementServer:
  enabled: true
```

The control-plane chart configures the API server with the same credential pair so it recognizes the platform 
management server. By default, the platform management server authenticates against the API server deployed by the 
same chart; set `platformManagementServer.apiServerUrlReference` to point it at an API server living elsewhere.

### Configure Admin Authentication
To configure how administrators log in to the Platform Management Center, fill in the appropriate identity provider 
information under `platformManagementServer.extraEnvs` in the same overrides file.

For complete information on identity provider options, see the [SSO Setup Guide](../../user_management/identity_providers.md).

#### Example: OAuth

```yaml
platformManagementServer:
  enabled: true
  extraEnvs:
    - name: CLEARML__secure__login__sso__oauth_client__auth0__client_id
      value: "<AUTH0_AUTH_CLIENT_ID>"
    - name: CLEARML__secure__login__sso__oauth_client__auth0__client_secret
      value: "<AUTH0_AUTH_CLIENT_SECRET>"
    - name: CLEARML__platform_server__services__login__sso__oauth_client__auth0__base_url
      value: "<AUTH0_TENANT_BASE_URL>"
    - name: CLEARML__platform_server__services__login__sso__oauth_client__auth0__authorize_url
      value: "<AUTH0_TENANT_AUTHORIZE_URL>"
    - name: CLEARML__platform_server__services__login__sso__oauth_client__auth0__access_token_url
      value: "<AUTH0_TENANT_ACCESS_TOKEN_URL>"
    - name: CLEARML__platform_server__services__login__sso__oauth_client__auth0__audience
      value: "<AUTH0_TENANT_AUDIENCE_URL>"

    # Optional: restrict access by email or domain
    - name: CLEARML__platform_server__services__login__email_filters__allowed
      value: '["example.com", "my-domain.ai"]'
```

#### Example: Fixed Users
You can enable fixed users instead of an external identity provider:
```yaml
platformManagementServer:
  enabled: true
  extraEnvs:
    - name: CLEARML__platform_management__auth__fixed_users__enabled
      value: "true"
    - name: CLEARML__platform_management__auth__fixed_users__users
      value: "[{\"username\":\"<USERNAME_PLACEHOLDER>\",\"password\":\"<PASSWORD_PLACEHOLDER>\",\"name\":\"<USER_NAME_PLACEHOLDER>\"}]"
```

Then apply the new configuration:

```
helm upgrade -n klique control-plane \
  oci://docker.io/kliqueai/control-plane \
  -f control-plane-values.override.yaml
```

## Step 2: Platform Management Center Setup Configuration

The `platform-management-center` chart deploys the UI webserver and a proxy through which the webserver reaches the 
platform management server.

Create an overrides file, for example: 
```
platform-management-center-values.override.yaml
```

### Configure Container Repository Access

Add the following configuration to the overrides file: 
```yaml
imageCredentials:
  password: "<DOCKERHUB_TOKEN>"
```
Where `<DOCKERHUB_TOKEN>` is the token provided by Klique for its container repository.

### Configure the Platform Management Server Connection

Add the following configuration to the overrides file: 

```yaml
proxy:
  upstream:
    url: "<PLATFORM_MANAGEMENT_SERVER_URL>"
```
Where `<PLATFORM_MANAGEMENT_SERVER_URL>` is the full URL of the platform management server enabled in Step 1. When 
installing in the same cluster as the control plane, use the control plane's internal service including the local 
domain, for example `http://control-plane-platform-management-server.klique.svc.cluster.local:5210`. When the 
platform management server runs in another cluster, expose it there with `platformManagementServer.ingress` or 
`platformManagementServer.httpRoute` in the `control-plane` chart, and use that published URL.

## Step 3: Install the Platform Management Center

Install the `platform-management-center` Helm chart in the same namespace as the Klique control plane:

```
helm upgrade -i platform-management-center oci://docker.io/kliqueai/platform-management-center -n klique -f platform-management-center-values.override.yaml
```

:::note
This installs the Platform Management Center webserver and proxy, but does not expose the UI externally.
:::

## Step 4: Accessing the Platform Management Center UI

By default, the UI is only accessible from inside the cluster using `kubectl port-forward`.

If external access is required, you can expose the webserver using an Ingress resource in your overrides file. For example:
```yaml
webserver:
  ingress:
    enabled: true
    ingressClassName: ""
    annotations: {}
    hostName: "<HOSTNAME_PLACEHOLDER>"
    tlsSecretName: "<TLS_SECRETNAME>"
```
