# Model Governance

> Model governance in Klique, covering the Model Endpoints dashboard, access controls on endpoints, and per-user usage quotas.

Model governance covers how deployed models are seen, secured, and controlled. Klique provides a single view of every
live model endpoint, layered access control over those endpoints, and per-user limits on how models are consumed.

![LLM Gateway config](../img/webapp_settings_llm_gateway.png#light-mode-only)
![LLM Gateway config](../img/webapp_settings_llm_gateway_dark.png#dark-mode-only)

## Deployment visibility

The [Model Endpoints](../webapp/webapp_model_endpoints.md) dashboard lists every live endpoint deployed through the Klique
deploy applications, giving one place to see what is running and how it is performing. For each endpoint it shows the
model, endpoint URL, number of instances, uptime, total requests, average request rate, and average latency, with a
separate view for endpoints that are still loading.

Opening an endpoint shows per-instance detail, including the requests served, request rate, CPU and GPU count, and
latency for each model instance. Endpoints can be compared in a table view or inspected individually, and the table can
be filtered, searched, and exported to CSV.

![Model endpoints dashboard](../img/webapp_model_endpoints_active_table.png#light-mode-only)
![Model endpoints dashboard](../img/webapp_model_endpoints_active_table_dark.png#dark-mode-only)

## Access and security

Access to deployed models is controlled at several layers:

* **RBAC** - [Access rules](rbac.md) determine which users, service accounts, and groups can see and manage models and
  their endpoints, following the same role-based model used across the platform.
* **Access tokens** - Endpoints are authenticated with JWT tokens. User tokens grant access according to the caller's identity
  and privileges, while endpoint tokens grant access to specific endpoints regardless of the caller's identity. See
  [Access Tokens](../webapp/settings/webapp_settings_app_gw.md#access-tokens).
* **Static routes** - A [static route](../webapp/settings/webapp_settings_app_gw.md#static-routes) is a fixed, externally
  accessible endpoint that routes to a deployed model or models, decoupled from the specific serving instance behind it (which can
  change or restart). Routes can be either unauthenticated or require authentication using group-based permissions.

## Usage quotas and rate limiting

Klique can enforce per-user or group limits on model consumption, so a shared model endpoint stays available and its cost stays
predictable:

* **Quotas** - Cap how many tokens each user or group can consume over a specific period of time. This combines both input and output tokens
* **Rate limiting** - Throttle the request rate per user to protect endpoints from spikes and prevent any single user
  from monopolizing capacity.

For more information, see the [LLM Gateway](../webapp/settings/webapp_settings_llm_gateway.md) documentation.
