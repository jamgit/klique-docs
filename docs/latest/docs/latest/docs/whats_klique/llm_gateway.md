# LLM Gateway

> Klique's centralized entry point for LLM traffic, covering model routing, load balancing, governed access, token quotas, and cost visibility.

The LLM Gateway is Klique's centralized point of control for LLM traffic. Model calls from Klique's terminal-based
applications, and from other services that route through it, reach their provider through the gateway. Instead of each
application holding its own provider credentials and configuration, the LLM Gateway consolidates model access and
governance into one place, giving administrators control over what models are available, who can reach them, and
visibility into what they cost.

![LLM Gateway](../img/webapp_ai_usage_dashboard.png#light-mode-only)
![LLM Gateway](../img/webapp_ai_usage_dashboard_dark.png#dark-mode-only)

## One endpoint for every model

Klique's terminal-based applications, such as [Claude Code](../webapp/applications/apps_claude_code.md) and
[Codex](../webapp/applications/apps_codex.md), reach their
models through the gateway rather than holding their own provider credentials. Adding, swapping, or removing a model
behind the gateway changes what every connected application can reach, without touching application code or
configuration.

Each model is defined by a logical gateway name, the upstream model identifier sent to the provider, and the provider
that determines how the gateway communicates with it, such as Anthropic or an OpenAI-compatible provider. A model can
have several upstream endpoints, and the gateway load-balances requests across them according to per-endpoint weights,
so traffic can be biased between endpoints or spread for resilience.

## Governed access

Administrators decide which models exist behind the gateway and who can reach them. Access can be scoped per user or
user group, so routing changes as roles and projects change rather than through credentials handed out and revoked by
hand. If a model's allowed lists are empty, every user in the workspace may reach it; once users or groups are added,
only those users and group members can. See [Model Governance](../governance_engine/model_governance.md).

## Cost control

Token usage is metered per user and per model as it passes through the gateway, and administrators set quotas that cut
off a user's access to a model once their usage crosses a limit within a rolling window.

Quotas are layered rather than uniform. A gateway-wide default applies unless a model overrides it, and a model's limit
can in turn be overridden for individual users or for a user group, whose members each receive that quota. This is how
teams get differentiated budgets on shared models.

Metered usage surfaces in the
[AI Usage dashboard](../webapp/webapp_admin_dashboard_ai_usage.md), broken down by model, user, provider, user group,
or project, and feeds the platform's cost visibility and
[usage metering and billing](../governance_engine/usage_metering_billing.md). See [Tokenomics](../governance_engine/tokenomics.md).

## Secure routing

The gateway inherits the [AI Application Gateway](appgw.md)'s authenticated,
SSL-secured routing and Klique RBAC, so LLM traffic reaching it is authenticated and authorized the same way as any
other gateway-fronted service.

## Deployment

The LLM Gateway is deployed as part of the standard AI Application Gateway deployment. 

The App Gateway Router supports the following deployment options:

* [Kubernetes](../deploying_klique/appgw_install_k8s.md)
* [Docker Compose](../deploying_klique/appgw_install_compose.md)  
* [Docker Compose for hosted servers](../deploying_klique/appgw_install_compose_hosted.md)  

Once deployed, administrators manage its models, quotas, and access from the
[LLM Gateway](../webapp/settings/webapp_settings_llm_gateway.md) UI settings page.
