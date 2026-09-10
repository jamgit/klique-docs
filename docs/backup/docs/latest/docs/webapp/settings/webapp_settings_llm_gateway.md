# LLM Gateway

> LLM Gateway settings configure token quotas and register models, endpoints, and access controls for gateway-routed LLM requests.

The LLM Gateway provides a centralized entry point for LLM access within the workspace, governing model access for
terminal-based Klique applications, such as [Claude Code](../applications/apps_claude_code.md) and [Codex](../applications/apps_codex.md), 
and other services that route through it.

The [AI Usage](../webapp_admin_dashboard_ai_usage.md) tab of the Admin Dashboard uses gateway metrics collected from the LLM Gateway.

![LLM Gateway](../../img/webapp_settings_llm_gateway.png#light-mode-only)
![LLM Gateway](../../img/webapp_settings_llm_gateway_dark.png#dark-mode-only)

## Token Quota

The Token Quota section defines the default token limits enforced for requests routed through the gateway. Quotas
are tracked per user and per model. For models with multiple
endpoints, quota is tracked per endpoint. When enabled, the gateway enforces a sliding window quota for each
user/model pair. Once a user's usage for a given model exceeds the configured limit within the configured window,
further requests for that model from that user are rejected until usage falls back within range.

Configure the following fields:
* Enabled - Enables quota enforcement.
* Limit - Maximum number of tokens permitted within the window.
* Window in seconds - Size of the rolling quota window.
* Strategy - Determines whether the quota applies to total, In (prompt), or Out (completion) tokens.

## LLM Gateway Models

The LLM Gateway Models section lists all models available through the gateway, each showing the model's gateway name,
model, provider, and its configured endpoints.

Click **Add Model** to define a new model. Configure the following:
* **Model** - Model details:
  * Gateway model name - The model's logical name: its public route, and the value reported for it in
    usage.
  * Model - The upstream model identifier sent to the provider, for example `claude-opus-4-8` or
    `Qwen/Qwen2.5-0.5B-Instruct`
  * Provider - Determines how the gateway communicates with the model endpoint, for example Anthropic or an
    OpenAI-compatible provider
* **Endpoints** - One or more upstream endpoints the gateway load-balances requests across. Weights let administrators 
  bias traffic distribution between them.
  * Endpoint URL - The provider API endpoint
  * API Key - Credentials used to authenticate with the provider
  * Weight - Relative traffic distribution across multiple endpoints; if multiple endpoints are configured, requests
    are distributed according to endpoint weight
* **Quotas** - Configure a token quota for a specific model that overrides the gateway-wide default
  `Token Quota` settings.
  * Limit (optional) – Maximum number of tokens permitted within the window for this model. Leave empty to use the gateway default.
  * Window in seconds - Size of the rolling quota window.
  * User overrides - Assign a specific token quota to individual users for this model, overriding the model-level limit.
  * Group overrides - Assign a specific token quota to a user group for this model, overriding the model-level limit.
    Each member of the group receives this quota individually.
* **Access** - Restrict which users and user groups can access the model. If the allowed lists are empty, all users
  in the workspace may access the model. Once one or more users or groups are added, only those users and the
  members of those groups may access the model through Klique applications and services using the gateway.
