# Klique K8s Collector

> The k8s-collector Helm chart, which scrapes Kubernetes cluster metrics and forwards them to the Klique Server's Hardware Dashboard.

The `k8s-collector` Helm chart scrapes cluster metrics and forwards them to the Klique Server for display in the 
Platform Management Center's [**Hardware Dashboard**](../../platform_management_center/pmc_platform_overview.md#hardware-dashboard). 
It's an OpenTelemetry Collector that pushes filtered cluster-wide data to the Klique Server's `hardware.ingest` endpoint 
using standard exporters:

* `node-exporter`  
* `kube-state-metrics`  
* `cAdvisor` (via the `kubelet` API-server proxy)

It collects: 

* Host-level node statistics, such as CPU and memory usage  
* Pod and container runtime usage, such as CPU and memory usage  
* Kubernetes object state, such as Pod and Node identity, labels, and resources

## Prerequisites

* A Klique apiserver accessible from the cluster where the collector runs  
* A credential authorized for `hardware.*` endpoints. One of the following
  * A root/system role
  * A `platform_management` credential  
* `helm` and Kubernetes cluster admin role

## Configuration

The following values are mandatory:

| Value | Description |
| ----- | ----- |
| `collector.ingestEndpoint` | The apiserver's `hardware.ingest` URL. |
| `collector.clusterId` | Unique, stable per-cluster custom identifier (e.g. `prod-eu-west-1`). |
| `collector.auth.*` | One of the following: `token`, `accessKey`+`secretKey`, or `fromSecret`. |

### Authentication

Use one of the following methods for authentication:

* **Access-key / secret-key (recommended)** - Access keys don't expire unless you revoke the credentials.

   ```
   collector:
      auth:
         accessKey: "<access_key>"
         secretKey: "<secret_key>"
   ```

* **Bearer token** - Use this method only if your Klique server's authentication endpoint doesn't accept access-key/secret-key 
   credentials. Generate the token with `auth.login`. Unlike access-key/secret-key, tokens expire and must be refreshed periodically. 

   ```
   collector:
     auth:
       token: "<jwt>"
   ```

## Installation

Create a `my-cluster.overrides.yaml` values override file (per-cluster):

```
# my-cluster.overrides.yaml
collector:
  ingestEndpoint: "https://api.<your-server>/hardware.ingest"
  clusterId: "<unique-cluster-id>"
  auth:
    accessKey: "<access_key>"
    secretKey: "<secret_key>"
```

Install / upgrade:

```shell
helm upgrade -i -n k8s-collector --create-namespace k8s-collector \
  oci://docker.io/kliqueai/k8s-collector \
  -f my-cluster.overrides.yaml
```

## Filtering and Customization

By default, the collector scrapes and reports on the entire cluster. You can narrow the collectors scope using these mechanisms:

* **Tolerations** control whether the collector's own pods can be *scheduled* onto tainted nodes. If every node in the 
  cluster is tainted, the collector needs tolerations just to run there.  
* **Filters** control what collected data is sent to the Klique server, once the collector is already running. Use filters 
  to exclude specific nodes, namespaces, or pods from reporting.

### Tainted Nodes

If your cluster has no untainted nodes, add tolerations so the collector's components are scheduled everywhere: 

```
# Optional: if nodes may all be tainted with no untainted node, you can tolerate
# everything so node-exporter covers every node.
prometheus-node-exporter:
  tolerations:
    - operator: Exists
kube-state-metrics:
  tolerations:
    - operator: Exists
opentelemetry-collector:
  tolerations:
    - operator: Exists
```

### **Filters**

All filters live under `collector.filters`. Multiple rules combine with OR logic — a target/sample is dropped if **any** rule matches.

| Filter | Default | Notes |
| ----- | ----- | ----- |
| `excludeNodeByLabel` | `{node-role.kubernetes.io/control-plane: ".*"}` | Map of label-key → value-regex. Only applies to Node-level hardware metrics. |
| `excludeNodeByRegex` | `""` | Drop nodes by name. Filters ALL metrics. |
| `excludeNamespacesByRegex` | ```kube-system\|…\|clearml``` | Drops pod/container metrics by namespace. System namespaces are dropped by default. Check that the namespace you installed the control plane into is matched: if it is not, Klique's own pods are reported as workload. |
| `excludePodsByRegex` | `""` | Drop pod/container metrics by pod name. |
