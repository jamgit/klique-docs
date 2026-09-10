# Monitoring (K8s)

> Recommended practices for monitoring a Kubernetes-based Klique deployment, including API server logging and the orchestration metrics exporter.

The following are general recommendations for monitoring your Klique deployment on Kubernetes. These practices help 
identify performance bottlenecks and maintain the system's reliability.

## API Server Logging and Analysis
Collect Klique API Server logs using your preferred log aggregation solution. These logs can provide operational 
insights, including:
* HTTP activity - Status codes, errors, and warning messages.
* API call details - Request ID, duration, version, and associated task for each endpoint.
* Usage tracking - API activity by caller
* Database performance - Latency measurements for APIs, including time spent per database call.

For more details on configuring API auditing, log adapters, and customizing which API endpoints and fields are logged, 
see [Setting up API Auditing](../api_audit.md).

## Kubernetes Metrics and Infrastructure Monitoring
Monitor Klique components at the Kubernetes level using standard metrics exporters and dashboards, such as Prometheus 
and Grafana, to ensure all components are performing reliably under load.

Commonly tracked metrics include:
* Pod-level CPU, memory, and network utilization for:
  * API Server
  * File Server
  * Klique Orchestrators
  * Application Gateways
* Storage utilization of the File Server or external object storage.
* Task pod metrics for pods prefixed with `clearml-id-*`.
* Ingress performance - Track Ingress Controller metrics using common exporters and dashboards (e.g., NGINX).
* Database performance - Track database performance using community-supported exporters and dashboards.

## Klique Orchestration Metrics

:::important[Available from Klique Server v3.31]
The metrics exporter is available from Klique Server v3.31 and later.
:::

Klique Server provides an optional **metrics exporter** that exposes the
live orchestration state (tasks, workers, queues, and models) as Prometheus metrics, scoped per
company (tenant). The exporter runs as a dedicated K8s Deployment that serves metrics through the `/metrics` endpoint.
The Helm chart includes a prebuilt Grafana dashboard.

### Enabling the exporter

Enable the exporter by setting `metricsExporter.enabled` to `true` in the
`control-plane` Helm chart:

```yaml
metricsExporter:
  enabled: true
  port: 8090
  # Cache the gathered snapshot for N seconds (0 = recompute on every scrape)
  cacheTtlSec: 30
  # Scrape via the Prometheus Operator (kube-prometheus-stack); requires the ServiceMonitor CRD
  serviceMonitor:
    enabled: true
    interval: 30s
  # Optional bearer-token auth on /metrics (off by default; keep the Service internal)
  auth:
    enabled: false
```

When `serviceMonitor.enabled` is `true`, the chart creates a `ServiceMonitor` so a Prometheus Operator
discovers the exporter’s endpoint automatically. 
Otherwise, point your own Prometheus at the exporter's Service (`<release>-metrics-exporter`, port `8090`, path `/metrics`).

The `/metrics` endpoint is unauthenticated by default, so keep the Service internal (`ClusterIP`). To
require a bearer token instead, set `metricsExporter.auth.enabled` to `true` (see the chart values for token options).

### Exposed metrics

All metrics are prefixed `clearml_` and carry a `company` label (the tenant).

Aggregate gauges:

| Metric | Labels | Description |
|---|---|---|
| `clearml_tasks` | `company`, `status` | Number of tasks by status |
| `clearml_workers` | `company`, `status` | Registered workers by status (`running`/`idle`) |
| `clearml_queue_length` | `company`, `queue_id`, `queue` | Tasks currently enqueued, per queue |
| `clearml_models` | `company`, `framework` | Number of models by framework |

Per-entity info metrics (the value is always `1`; the information is carried in the labels):

| Metric | Labels | Description |
|---|---|---|
| `clearml_task_info` | `company`, `task_id`, `task_name`, `status`, `type`, `project`, `queue_id`, `queue`, `worker`, `resource_group`, `user` | One series per live (queued or running) task |
| `clearml_worker_info` | `company`, `worker_id`, `resource_group`, `category`, `ip`, `status`, `current_task`, `user` | One series per live worker |
| `clearml_queue_info` | `company`, `queue_id`, `queue` | One series per queue |
| `clearml_worker_queue_info` | `company`, `worker_id`, `queue_id`, `queue` | Worker-to-queue subscriptions (which queues each worker serves) |

Exporter health:

| Metric | Description |
|---|---|
| `clearml_companies_total` | Number of companies/tenants |
| `clearml_exporter_scrape_duration_seconds` | Time taken to gather the snapshot on the last scrape |
| `clearml_exporter_up` | `1` if the last scrape succeeded, `0` otherwise |

:::note
The per-entity `*_info` metrics are emitted only for naturally bounded, live sets (active workers,
queues, and queued/running tasks) to keep metric cardinality under control.
:::

### Grafana dashboard

The chart ships a prebuilt dashboard, **Klique Orchestration**
(`charts/control-plane/dashboards/klique-orchestration.json`), covering tasks, workers, queues,
models, resource groups, and worker-to-queue mappings, with per-company filtering. The chart does not
auto-provision it; import it whichever way fits your setup:

* **Grafana UI** - Go to **Dashboards > New > Import**, then upload `klique-orchestration.json` and select your
  Prometheus data source.
* **Grafana sidecar** (`kube-prometheus-stack`) - Create a ConfigMap from the JSON labeled
  `grafana_dashboard=1`.
* **grafana-operator** - Reference the JSON from a `GrafanaDashboard` resource.

The dashboard provides two template variables:
* `Company` - Filter all panels by tenant.
* `View` - Toggle visibility of system-level queues:
  * `User view - hide system` (default) - Hides system queues such as `k8s_scheduler`.
  * `Debug - show all` - Shows all queues.
