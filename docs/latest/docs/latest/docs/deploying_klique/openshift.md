# OpenShift

> Klique Server and Application Gateway installation on OpenShift with Ingress or Route networking, restricted security contexts, and troubleshooting.

This guide provides instructions for installing Klique Server in an OpenShift environment, focusing on network 
configuration and security contexts.

## Installation

To install Klique on OpenShift, start with the [Klique Server Kubernetes Deployment guide](k8s.md). 
After completing the standard installation, extend it with the OpenShift-specific networking and security configurations 
outlined below. 

## Networking Configuration

You can expose the Klique services using one of the following: 
* Standard Kubernetes Ingress objects 
* OpenShift's native Route resources. 

#### Cluster Application Domain and TLS

Every OpenShift cluster ships with a router that provides wildcard DNS and a wildcard TLS certificate for the
cluster's application domain (`*.apps.<CLUSTER_DOMAIN>`). Retrieve it with:

```bash
oc get ingresses.config/cluster -o jsonpath='{.spec.domain}'
```

Hostnames placed directly under this domain (e.g. `api.apps.rosa.<CLUSTER_DOMAIN>`) resolve automatically and are
covered by the router's default certificate, so no DNS records or TLS secrets need to be provided. Hostnames
outside this domain (e.g. a company domain) require you to provide DNS records and a matching certificate
yourself (for example, with `external-dns` and `cert-manager`).

:::note
Set `controlPlane.cookieDomain` to the common parent domain of the three Klique hostnames (e.g.
`klique.<YOUR_DOMAIN>` when using `api./app./files.klique.<YOUR_DOMAIN>`), otherwise logins will not persist.
:::

### Klique Server

#### Option 1: Using Kubernetes Ingress

The `control-plane` Helm chart supports Ingress creation out-of-the-box. On OpenShift, Ingress objects are served by the
built-in router, which converts them to Routes automatically, so no extra Ingress Controller is needed. Adding the
`route.openshift.io/termination: "edge"` annotation makes the generated Route terminate TLS at the router; when
the hostnames are under the cluster application domain, the router's default wildcard certificate is used and
`tlsSecretName` can be left empty.

```yaml
# values.yaml
controlPlane:
  cookieDomain: "klique.<YOUR_DOMAIN>"

apiserver:
  ingress:
    enabled: true
    ingressClassName: ""  # Specify your ingress class if needed
    hostName: "api.klique.<YOUR_DOMAIN>"
    tlsSecretName: ""      # Optionally provide a secret for TLS
    annotations:
      # TLS termination at the OpenShift router (uses the cluster wildcard certificate)
      route.openshift.io/termination: "edge"
      # Redirect plain HTTP requests to HTTPS
      haproxy.router.openshift.io/insecure_edge_termination_policy: "Redirect"

fileserver:
  ingress:
    enabled: true
    ingressClassName: ""
    hostName: "files.klique.<YOUR_DOMAIN>"
    tlsSecretName: ""
    annotations:
      route.openshift.io/termination: "edge"
      haproxy.router.openshift.io/insecure_edge_termination_policy: "Redirect"
      # This translates the NGINX proxy-read-timeout and proxy-send-timeout (large artifact uploads).
      haproxy.router.openshift.io/timeout: 600s

webserver:
  ingress:
    enabled: true
    ingressClassName: ""
    hostName: "app.klique.<YOUR_DOMAIN>"
    tlsSecretName: ""
    annotations:
      route.openshift.io/termination: "edge"
      haproxy.router.openshift.io/insecure_edge_termination_policy: "Redirect"
```

#### Option 2: Using OpenShift Routes

To use Routes, you need to disable the default Ingress creation in the Helm chart, and then create the Route objects manually.

##### Step 1: Disable Ingress in Helm Chart

Set `enabled: false` for all ingresses in your `values.yaml` file:

```yaml
# values.yaml
apiserver:
  ingress:
    enabled: false

fileserver:
  ingress:
    enabled: false

webserver:
  ingress:
    enabled: false

```

##### Step 2: Create the Route Objects

Create a YAML file (e.g., `klique-routes.yaml`) with the following definitions to configure Routes for the Klique
services. This single file defines all three required routes.

```yaml
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: control-plane-apiserver
  namespace: klique
spec:
  host: api.klique.<YOUR_DOMAIN>
  path: /
  to:
    kind: Service
    name: control-plane-apiserver
    weight: 100
  port:
    targetPort: 8008
  tls:
    termination: edge
    insecureEdgeTerminationPolicy: Redirect

---

apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: control-plane-fileserver
  namespace: klique
  annotations:
    # This translates the NGINX proxy-read-timeout and proxy-send-timeout.
    haproxy.router.openshift.io/timeout: 600s
spec:
  host: files.klique.<YOUR_DOMAIN>
  path: /
  to:
    kind: Service
    name: control-plane-fileserver
    weight: 100
  port:
    targetPort: 8081
  tls:
    termination: edge
    insecureEdgeTerminationPolicy: Redirect

---

apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: control-plane-webserver
  namespace: klique
spec:
  host: app.klique.<YOUR_DOMAIN>
  path: /
  to:
    kind: Service
    name: control-plane-webserver
    weight: 100
  port:
    targetPort: 8080
  tls:
    termination: edge
    insecureEdgeTerminationPolicy: Redirect
```

Apply the configuration to your cluster:

```bash
oc apply -f klique-routes.yaml
```

### AI Application Gateway

#### Option 1: Using Kubernetes Ingress

Enable the Ingress for the Application Gateway with the following `values.yaml` snippet. Make sure to replace the example `hostname`
with your desired hostname.

```yaml
# values.yaml
ingress:
  enabled: true
  ingressClassName: ""
  hostName: "appgw.klique.<YOUR_DOMAIN>"
  tlsSecretName: "" # Optionally provide a secret for TLS
```

#### Option 2: Using OpenShift Routes

To use Routes, you need to disable the default Ingress creation in the Helm chart, and then create the Route objects manually.

##### Step 1: Disable Ingress in Helm Chart

Set `enabled: false` for all ingresses in your `values.yaml` file:

```yaml
# values.yaml
ingress:
  enabled: false
```

##### Step 2: Create the Route Object

The following Route definition uses a wildcard host, which securely exposes both the primary gateway URL and any
subdomains it requires. Create a file named `klique-app-gateway-route.yaml`. Make sure to replace the example `spec.host`
with the desired hostname.

:::important
The OpenShift router rejects wildcard Routes by default (`wildcardPolicy: WildcardsDisallowed`). Enable wildcard
admission on the ingress controller before applying this Route:

```bash
oc -n openshift-ingress-operator patch ingresscontroller/default \
  --type=merge -p '{"spec":{"routeAdmission":{"wildcardPolicy":"WildcardsAllowed"}}}'
```
:::

```yaml
# klique-app-gateway-route.yaml
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: gateway
  namespace: klique-tenant-a
spec:
  host: '*.appgw.klique.<YOUR_DOMAIN>'
  path: /
  to:
    kind: Service
    name: gateway
    weight: 100
  port:
    targetPort: 8080
  tls:
    termination: edge
    insecureEdgeTerminationPolicy: Redirect
```

Apply the file to your cluster:
```bash
oc apply -f klique-app-gateway-route.yaml
```

## Security Context Configuration for Restricted Environments

If your OpenShift cluster enforces a restrictive security context (requiring containers to run as non-root users) with 
randomized UID, you must add specific security configurations to your `values.yaml`.

### Understanding Admission Warnings and Errors

OpenShift evaluates pods with two distinct mechanisms, and telling them apart avoids chasing the wrong problem:

* **Pod Security Admission warnings**: messages like `Warning: would violate PodSecurity "restricted:latest"`
  printed during `helm install`/`upgrade`. On OpenShift these are **informational only**: admission is decided by
  SCCs, not by the PodSecurity profile, and SCCs mutate pods to inject most of the missing fields
  (`allowPrivilegeEscalation: false`, dropped capabilities, seccomp profile).
* **SCC errors**: events like `pods "..." is forbidden: unable to validate against any security context
  constraint` on a Deployment/StatefulSet/ReplicaSet. These are **real failures**: the pod was rejected and will
  not be created. The event lists, per SCC provider, the exact field that failed (e.g.
  `.containers[0].runAsUser: Invalid value: 65532: must be in the ranges: [1000840000, 1000849999]`).

The Klique server images (`apiserver`, `fileserver`, `webserver`, `applications`, `usageAggregator`) run
with a fixed non-root UID (`65532`), and their internal filesystem permissions are built for that user. They
are **not** designed to run with an arbitrary UID. The default `restricted-v2` SCC only accepts UIDs from the
namespace's assigned range, so these pods are rejected. The resolution differs by component type:

#### Klique server components: grant the `nonroot-v2` SCC

`nonroot-v2` keeps all `restricted-v2` guarantees (no root, no privilege escalation, dropped capabilities) but
accepts any explicit non-root UID, letting the Klique images keep the UID they were built for:

```bash
oc adm policy add-scc-to-group nonroot-v2 system:serviceaccounts:<NAMESPACE>
```

This grant also covers pods created by operators (e.g. the MongoDB operator) that cannot be adjusted through
Helm values. Do **not** instead unset the Klique components' `runAsUser` in values: the pod would pass admission
with a random namespace-range UID, but the process would then fail at runtime on the image's internal file
permissions. Avoid granting `anyuid` unless a workload genuinely requires running as root: it also allows UID 0
and, having a higher SCC priority, silently takes over admission for every pod in the namespace.

#### Bundled dependencies: automatic adaptation

The bundled Valkey and Elasticsearch dependency charts detect OpenShift and adapt their security contexts
automatically (`global.compatibility.openshift.adaptSecurityContext: auto`): the pinned `runAsUser`,
`runAsGroup`, and `fsGroup` values are removed, OpenShift assigns values from the namespace range, and the pods
pass the default `restricted-v2` SCC without any grant. No values changes are needed, with one exception: the
Elasticsearch sysctl init container runs privileged as root and must be disabled (see
[Elasticsearch Notes](#elasticsearch-notes)).

MongoDB is managed by the MongoDB Controllers for Kubernetes operator: its pods are created by the operator and
are not configurable through chart values; the `nonroot-v2` grant covers them.

### Klique Server Components

This is a comprehensive example configuration for the core Klique Server services. It includes
* Disabling the default ingresses
* Setting the correct external URLs
* Applying the adjustments needed for the Klique components and Elasticsearch to run in a non-root environment.

```yaml
apiserver:
  ingress:
    enabled: false

fileserver:
  ingress:
    enabled: false

webserver:
  ingress:
    enabled: false
  displayedServerURLs:
    apiserver: "https://api.klique.<YOUR_DOMAIN>"
    fileserver: "https://files.klique.<YOUR_DOMAIN>"

applications:
  enabled: true
  maxPods: 20
  webServerUrlReferenceOverride: "http://control-plane-webserver:8080"
  fileServerUrlReferenceOverride: "http://control-plane-fileserver:8081"
  apiServerUrlReferenceOverride: "http://control-plane-apiserver:8008"
  containerCustomBashScript: |
    export HOME=/tmp
    declare LOCAL_PYTHON
    [ ! -z $LOCAL_PYTHON ] || for i in {{20..5}}; do (which python3.$i 2> /dev/null || command -v python3.$i) && python3.$i -m pip --version && export LOCAL_PYTHON=$(which python3.$i 2> /dev/null || command -v python3.$i) && break ; done
    [ ! -z $LOCAL_PYTHON ] || export LOCAL_PYTHON=python3
    {extra_bash_init_cmd}
    [ ! -z $CLEARML_AGENT_NO_UPDATE ] || $LOCAL_PYTHON -m pip install clearml-agent{agent_install_args}
    {extra_docker_bash_script}
    $LOCAL_PYTHON -m clearml_agent execute {default_execution_agent_args} --id {task_id}
  extraEnvs:
    - name: CLEARML_K8S_GLUE_START_AGENT_SCRIPT_PATH
      value: /tmp/__start_agent__.sh
    - name: HOME
      value: /tmp

elasticsearch:
  sysctlImage:
    enabled: false
```

:::note
The Klique components' container security contexts already default to a restricted non-root setup (fixed UID
`65532`, `allowPrivilegeEscalation: false`, dropped capabilities, `RuntimeDefault` seccomp profile), and the
bundled Valkey and Elasticsearch charts adapt their UIDs to OpenShift automatically, so no security context
overrides are needed in the values.
:::

### Elasticsearch Notes

#### Disabled sysctl init container and `vm.max_map_count`

Setting `elasticsearch.sysctlImage.enabled: false` is required on OpenShift: that init container runs privileged
as root to raise `vm.max_map_count` on the node, which no default SCC allows. Without it, if the node's
`vm.max_map_count` is too low, Elasticsearch fails its bootstrap check with
`max virtual memory areas vm.max_map_count [65530] is too low` and crash-loops. In that case, configure
Elasticsearch to avoid mmap entirely by adding `node.store.allow_mmap: false` to `elasticsearch.extraConfig`
(the entry merges with the chart's default `extraConfig` settings), or raise the sysctl at the node level with a
tuning configuration:

```yaml
elasticsearch:
  sysctlImage:
    enabled: false
  # When vm.max_map_count cannot be raised on the node:
  extraConfig:
    node.store.allow_mmap: false
```

### Klique Orchestrator

The orchestrator's controller pod already runs with a restricted non-root security context by default (fixed UID
`65532`, covered by the `nonroot-v2` grant). The task pods it creates do not, so apply a restrictive security
context to them through `basePodTemplate`, along with the non-root workarounds for the task container:

```yaml
orchestratork8sglue:
  containerCustomBashScript: |
    export HOME=/tmp
    declare LOCAL_PYTHON
    [ ! -z $LOCAL_PYTHON ] || for i in {{20..5}}; do (which python3.$i 2> /dev/null || command -v python3.$i) && python3.$i -m pip --version && export LOCAL_PYTHON=$(which python3.$i 2> /dev/null || command -v python3.$i) && break ; done
    [ ! -z $LOCAL_PYTHON ] || export LOCAL_PYTHON=python3
    {extra_bash_init_cmd}
    [ ! -z $CLEARML_AGENT_NO_UPDATE ] || $LOCAL_PYTHON -m pip install clearml-agent{agent_install_args}
    {extra_docker_bash_script}
    $LOCAL_PYTHON -m clearml_agent execute {default_execution_agent_args} --id {task_id}
  extraEnvs:
    - name: CLEARML_K8S_GLUE_START_AGENT_SCRIPT_PATH
      value: /tmp/__start_agent__.sh

  basePodTemplate:
    env:
      - name: HOME
        value: /tmp
    containerSecurityContext:
      runAsNonRoot: true
      allowPrivilegeEscalation: false
      capabilities:
        drop: ["ALL"]
      seccompProfile:
        type: RuntimeDefault
```

## Troubleshooting

### `Warning: would violate PodSecurity "restricted:latest"` during install

Informational only on OpenShift (see [Understanding Admission Warnings and Errors](#understanding-admission-warnings-and-errors)).
No action needed unless pods actually fail to start.

### `unable to validate against any security context constraint`

Found in namespace events (`oc get events`) or on the owning Deployment/StatefulSet. The pod was rejected by SCC
admission. Read the `restricted-v2` lines in the error to find the offending field:

* `runAsUser: Invalid value: <UID>: must be in the ranges: [...]`: a fixed non-root UID. For Klique server
  components, grant `nonroot-v2` (the images must keep their built-in UID). The bundled Valkey and Elasticsearch
  charts adapt their UIDs to OpenShift automatically; if the error appears for one of them, check that
  `global.compatibility.openshift.adaptSecurityContext` has not been set to `disabled` in your values.
* `.initContainers[0].privileged: Invalid value: true` on the Elasticsearch pod: the sysctl init container is
  still enabled; set `elasticsearch.sysctlImage.enabled: false`. Note that StatefulSets do not update
  retroactively: verify the change reached the cluster with
  `oc get sts <name> -o jsonpath='{.spec.template.spec.initContainers[*].name}'` and confirm the Helm release
  actually received your values (`helm get values <release>`).

### Elasticsearch crash-loops with `vm.max_map_count [65530] is too low`

See [Disabled sysctl init container and `vm.max_map_count`](#disabled-sysctl-init-container-and-vmmax_map_count):
add `node.store.allow_mmap: false` to `elasticsearch.extraConfig`.

### Web UI loads but cannot reach the API after enabling TLS

URL references rendered into the services must match the external scheme and hostnames. Check
`webserver.displayedServerURLs` and, if applications are enabled, the
`applications.*UrlReferenceOverride` values: they must use `https://` and the externally resolvable
hostnames once TLS termination is in place. Also confirm `controlPlane.cookieDomain` matches the parent domain of the
hostnames, otherwise the login session cookie is not stored.
