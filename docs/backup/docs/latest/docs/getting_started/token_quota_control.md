# Token Limit and Quota Control

> How an AI Admin sets gateway-wide, model, user, and group-level token quotas and rate limits through the LLM Gateway, and what happens at the limit.

An AI Admin can cap how much a user or [group](../webapp/settings/webapp_settings_users.md#user-groups) spends on tokens per model, enforced centrally at the
[LLM Gateway](../whats_klique/llm_gateway.md).

For the underlying quota model (how gateway-wide, model, user, and group limits layer together), see
[Tokenomics](../governance_engine/tokenomics.md).

:::important[Prerequisite]
This page assumes the LLM Gateway is already deployed as part of your AI Application Gateway. If not, see the
deployment guides for:

* [Kubernetes](../deploying_klique/appgw_install_k8s.md)
* [Docker Compose](../deploying_klique/appgw_install_compose.md)
* [Docker Compose for hosted servers](../deploying_klique/appgw_install_compose_hosted.md)
:::

## Set a Quota

Quotas apply to model calls that reach the gateway: from Klique's terminal-based applications, such as
[Claude Code](../webapp/applications/apps_claude_code.md), [Codex](../webapp/applications/apps_codex.md), and
[OpenCode](../webapp/applications/apps_open_code.md), and from other services that route through it. Each model
behind the gateway is registered with a provider, Anthropic or an OpenAI-compatible provider, so a quota applies
regardless of which one serves it.

A quota can be configured at two levels:
* **Gateway-wide default** - In the LLM Gateway settings' Token Quota section, enable enforcement and set a token
  limit, a rolling window, and whether the limit applies to total tokens or tracks prompt and completion tokens
  separately.
* **Per-model override** - Each model listed under LLM Gateway Models can carry its own quota, overriding the
  gateway-wide default for that model alone, and can itself be narrowed further with overrides for an individual
  user or a user group.

This is how a group's usage of an expensive model can be capped more tightly than the gateway default, or a
single user given a higher limit than their group's standard one, while everyone else stays on the default.

Quota is tracked per user and per model; for a model with multiple endpoints, it's tracked per endpoint. Once a
user's usage crosses the quota that applies to them (whether set individually or inherited from their group)
within its rolling window, Klique rejects further requests for that model from that user until their usage falls
back within the window.

![LLM Gateway](../img/webapp_settings_llm_gateway.png#light-mode-only)
![LLM Gateway](../img/webapp_settings_llm_gateway_dark.png#dark-mode-only)

See the [LLM Gateway settings page](../webapp/settings/webapp_settings_llm_gateway.md) for the full set of
fields, including the access list that separately governs who can reach a model at all.

## Monitor Usage

Track consumption against quota in the [AI Usage dashboard](../webapp/webapp_admin_dashboard_ai_usage.md), broken
down by model, user, provider, user group, or project. This feeds the platform's broader
[usage metering and billing](../governance_engine/usage_metering_billing.md).
