# AI Application Gateway

> Authenticated, SSL-secured routing that turns workloads on governed compute into RBAC-scoped endpoints, and publishes them as URL Applications.

Cluster fabrics such as Kubernetes and cloud hyperscalers do not expose their networks to external users, so a workload
running on them is not reachable by the people meant to use it. Making each service reachable by hand means its own
ingress, certificates, and credentials, repeated per service.

The AI Application Gateway removes that work. Any workload running on governed compute is allocated an externally
accessible network route, and what a builder ran becomes something the organization can open.

## From workload to endpoint

Routes are allocated to a workload's instance and are SSL-secured, so access is encrypted without per-service
certificate handling. The gateway supports HTTP/S for web interfaces and APIs, and raw TCP for protocols that are not
HTTP, such as SSH sessions.

## Access under policy

Routes provide access in adherence to Klique RBAC privileges, so reaching a workload is governed by the same rules that
govern the rest of the platform. Access is authenticated at the gateway rather than delegated to each application, which
is why applications behind it do not hold their own credentials or implement their own login.

## What it fronts

The gateway fronts the Klique applications whose instances expose an endpoint, across the
[Applications](../webapp/applications/apps_overview.md) catalog's categories:

- **AI Dev** and **Agentic-AI**: remote development environments and agentic coding sessions, including SSH,
JupyterLab, VS Code, remote desktops, and agentic assistants.
- **UI Dev**: Gradio and Streamlit interfaces built over models.
- **Deploy**: model endpoints and containerized services, including vLLM, embedding model, and Llama.cpp deployments,
the containerized application launcher, chat interfaces to deployed models, and remote MCP servers.
- **NVAIE**: NVIDIA AI Enterprise workloads launched through Klique.
- **Databases**: detachable Milvus and Qdrant vector database sessions.

If a deployment does not have the App Gateway Router properly installed, instances of these applications may not be
accessible.

## Publishing to the organization

The gateway is also how a running instance becomes a durable, shareable entry point rather than a one-off URL. An
administrator assigns the instance a static route, defined in advance under
[Application Gateway settings](../webapp/settings/webapp_settings_app_gw.md#static-routes), and then publishes it as a
URL Application: a tile in the Applications catalog that opens the instance's static URL.

Access follows the catalog category the application is published into, so users reach only the published applications
their permissions cover. If the static route is disabled or removed, the URL Application is disabled until the route is
restored. See [Publish a URL Application](../webapp/applications/apps_overview.md#publish-a-url-application).

## Deployment

The AI Application Gateway requires an additional component alongside the Klique Server deployment: the Klique App
Gateway Router. It supports the following deployment options:

- [Kubernetes](../deploying_klique/appgw_install_k8s.md)
- [Docker Compose](../deploying_klique/appgw_install_compose.md)
- [Docker Compose for hosted servers](../deploying_klique/appgw_install_compose_hosted.md)

The deployment configuration specifies the external and internal address and port mappings for routing requests.
