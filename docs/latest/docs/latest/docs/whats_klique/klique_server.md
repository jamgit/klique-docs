# Control Plane

> Klique Server as the control plane, what it holds, how people, code, and agents reach it, its usage and observability, services, and deployment.

The Klique Server is Klique's control plane, the single authority for the platform's state and configuration. The
ClearML SDK in workload code, the AI Application Gateway, the Klique Orchestrator and Executor, and external
integrations all read the configuration they need from the server and report their state back to it. A policy or
setting defined once therefore takes effect across the platform, and one view covers everything running in the
environment.

The control plane is deployed once per environment, either as a managed service or self-hosted. This page describes
what it holds, how it is reached, and what it is made of. For where it sits in the platform, see
[Klique Platform](klique_platform.md).

## What the control plane holds

The API server is Klique's backend. It serves the REST API and holds the configuration that governs the rest of the
platform, backed by the datastores installed alongside it. It is the source of truth for projects, tasks and their
configuration, experiment state, dataset versions, models, queues, users, and the applications available to them.

It also holds the data workloads produce. The file server is Klique's built-in object storage for artifacts, models,
and debug samples, with token-authenticated access. It is the default location for that data rather than the only one:
when an external object store such as Amazon S3, Google Cloud Storage, Azure Blob Storage, or MinIO is configured,
artifacts are written there directly and the file server is left out of the storage path.

## Access for people, code, and agents

Three paths reach the control plane, and all three resolve against the same authority, so the same policy applies
whichever one is used.

- **People** work in the Klique WebApp, the single-page application served by the web server, where AI Admins, AI
Builders, and AI Consumers each get their own view. It relies on the API server for all platform data, and can proxy
the file server so that file access stays on the WebApp's own domain.
- **Code** reaches the REST API directly or through the [ClearML SDK](../clearml_sdk.md), which instruments training
and application code to create and control platform entities.
- **Agents** reach it through the MCP Server, a translation layer between MCP-compatible AI agents such as Claude,
Cursor, and Codex and the API server. It presents Klique operations, such as creating projects and tasks, querying
queues, retrieving task information, and managing applications, as tools an agent can call, so the platform can be
driven from natural-language requests. See [Agentic Interface](../agentic_interface/agentic_interface.md).

## Usage and observability

Because every client reports back to the control plane, it is also where platform activity and cost become visible.

The Usage Aggregator collects and aggregates usage events covering compute, storage, tokens, and users into the
metering data behind [usage metering and billing](../governance_engine/usage_metering_billing.md). Its aggregation
categories are configurable from the Klique UI.

The OTEL service is an authenticated OpenTelemetry endpoint exposing orchestration metrics, covering the orchestration
dashboard, tasks, workers, and models, in a form compatible with Grafana and other OpenTelemetry consumers. Platform
activity can therefore be monitored alongside an existing observability stack rather than only inside Klique.

## Services and datastores

The control plane is packaged and installed as a unit. Its services:

- **API server**: serves the REST API and holds the platform's governing configuration.
- **Web server**: serves the Klique WebApp.
- **File server**: built-in object storage for artifacts, models, and debug samples.
- **Usage Aggregator**: aggregates usage events into metering data.
- **MCP Server**: exposes Klique operations to AI agents over the Model Context Protocol.
- **OTEL service**: exposes orchestration metrics to OpenTelemetry consumers.

Three databases back it and are installed alongside it:

- **MongoDB** holds the platform's metadata: projects, tasks and their configuration, models, datasets, users, and
queues.
- **Elasticsearch** stores experiment and Hyper-Datasets information and makes it searchable, including experiments'
scalar metrics, console logs, and Hyper-Datasets' frames.
- **Valkey** is used for caching and intra-server service communication.

![Klique Server](../img/klique_server_diagram.png#light-mode-only)
![Klique Server](../img/klique_server_diagram_dark.png#dark-mode-only)

## Deployment

The Klique Server is deployed once per environment. It is delivered as a managed service or self-hosted, with several
self-hosting targets:

- **Kubernetes**: install with the `control-plane` Helm chart. See
[Kubernetes](../deploying_klique/k8s.md), and
[OpenShift](../deploying_klique/openshift.md) for OpenShift clusters.
- **VM or bare metal**: see [On-Premises on Ubuntu](../deploying_klique/on_prem_ubuntu.md).
- **Cloud**: see [AWS VPC](../deploying_klique/vpc_aws.md).

Once deployed, the server is configured for web login authentication, subdomains, and load balancers.
