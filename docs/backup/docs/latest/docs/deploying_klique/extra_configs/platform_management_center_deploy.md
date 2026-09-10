# Platform Management Center

> How to deploy the Platform Management Center on Kubernetes, including control plane credentials and admin authentication setup.

This guide describes how to deploy the [Platform Management Center](../../whats_klique/management_center.md) on 
Kubernetes.

This procedure assumes you have already set up the [Klique control plane](../k8s.md). 

## Step 1: Add Dedicated Control Plane Access Credentials 

Configure the Klique Server with credentials that the platform management center will use for secure access. 
If `openssl` is available, you can use the following command to generate suitable key and secret:
```
openssl rand -hex 16
```

Add the following environment variables to your Klique Server overrides file (`control-plane-values.override.yaml`):

```yaml
apiserver:
  extraEnvs:
    - name: KLIQUE__secure__credentials__platform_management__user_key
      value: "<PLATFORM_MANAGEMENT_USER_KEY>"

    - name: KLIQUE__secure__credentials__platform_management__user_secret
      value: "<PLATFORM_MANAGEMENT_USER_SECRET>"
```

Then apply the new configuration:

```
helm upgrade -n klique control-plane \
  oci://docker.io/kliqueai/control-plane \
  -f control-plane-values.override.yaml
```

## Step 2: Platform Management Center Setup Configuration
The Platform Management Center needs to:
1. Connect to the Klique API server
2. Authenticate administrators using it ([SSO or fixed users](../../user_management/identity_providers.md))

Create an overrides file, for example: 
```
platform-management-values.override.yaml.
```

### Configure Container Repository Access

Add the following configuration to the overrides file: 
```
global:
  imageCredentials:
    password: "<DOCKERHUB_TOKEN>"
```
Where `<DOCKERHUB_TOKEN>` is the token provided by Klique for its container repository.

### Configure Klique Control Plane Connection Parameters

Add the following configuration to the overrides file: 

```
klique:
  apiServerUrlReference: "<CLEARML_APISERVER_URL>"
  apiKey: "<PLATFORM_MANAGEMENT_USER_KEY>"
  apiSecret: "<PLATFORM_MANAGEMENT_USER_SECRET>"
```
Where
* `<CLEARML_APISERVER_URL>` is the URL for the Klique control plane API server 
* `<PLATFORM_MANAGEMENT_USER_KEY>` and `<PLATFORM_MANAGEMENT_USER_SECRET>` are the values created in Step 1

### Configure Admin Authentication
To configure how administrators log in to the Platform Management Center, fill in the appropriate identity provider 
information in the overrides file.

For complete information on identity provider options, see the [SSO Setup Guide](../../user_management/identity_providers.md).

#### Example: OAuth

```
platformManagement:
  extraEnvs:
    - name: KLIQUE__secure__login__sso__oauth_client__auth0__client_id
      value: "<AUTH0_AUTH_CLIENT_ID>"
    - name: KLIQUE__secure__login__sso__oauth_client__auth0__client_secret
      value: "<AUTH0_AUTH_CLIENT_SECRET>"
    - name: KLIQUE__services__login__sso__oauth_client__auth0__base_url
      value: "<AUTH0_TENANT_BASE_URL>"
    - name: KLIQUE__services__login__sso__oauth_client__auth0__authorize_url
      value: "<AUTH0_TENANT_AUTHORIZE_URL>"
    - name: KLIQUE__services__login__sso__oauth_client__auth0__access_token_url
      value: "<AUTH0_TENANT_ACCESS_TOKEN_URL>"
    - name: KLIQUE__services__login__sso__oauth_client__auth0__audience
      value: "<AUTH0_TENANT_AUDIENCE_URL>"

    # Optional: restrict access by email or domain
    - name: KLIQUE__services__login__email_filters__allowed
      value: ["example.com", "my-domain.ai"]
```

#### Example: Fixed Users
You can enable fixed users instead of an external identity provider:
```
platformManagement:
  extraEnvs:
    - name: KLIQUE__platform_management__auth__fixed_users__enabled
      value: "true"
    - name: KLIQUE__platform_management__auth__fixed_users__users
      value: "[{\"username\":\"<USERNAME_PLACEHOLDER>\",\"password\":\"<PASSWORD_PLACEHOLDER>\",\"name\":\"<USER_NAME_PLACEHOLDER>\"}]"
```

## Step 3: Install the Platform Management Center

Install the `platform-management` Helm chart in the same namespace as the Klique control plane:

```
helm upgrade -i platform-management oci://docker.io/kliqueai/platform-management -n klique -f platform-management-values.override.yaml
```

:::note
This installs the Platform Management Center service and webserver, but does not expose it externally.
:::

## Step 4: Accessing the Platform Management Center UI

By default, the UI is only accessible from inside the cluster using `kubectl port-forward`.

If external access is required, you can expose the webserver using an Ingress resource in your overrides file. For example:
```
platformManagementWebserver:
  ingress:
    enabled: true
    ingressClassName: ""
    annotations: {}
    hostName: "<HOSTNAME_PLACEHOLDER>"
    tlsSecretName: "<TLS_SECRETNAME>"
```
