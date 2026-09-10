# Governance Engine

> The Governance Engine, Klique's unified security, policy, and identity layer spanning access, cost, and compliance controls.

The Governance Engine centralizes security, access, policy, and cost control across your Klique deployment. Policies are
defined centrally and enforced everywhere, for every human and every agent, across models, tenants, and tools, so teams can
move quickly while nothing runs outside the boundaries you set. Every action is tied to a known identity, and consumption
can be audited, attributed, and costed.

The Governance Engine is one of Klique's three engines, alongside the
[Orchestration Engine](../orchestration_engine/orchestration_engine.md), which turns your compute into a managed
service, and the [AI Engine](../ai_engine/ai_engine.md), which puts every model behind a single gateway. The Governance
Engine defines the rules; the other engines enforce them as they route workloads and serve models.

## How it works

1. **Establish identity** - Connect your identity provider so every user, user group, and agent has a governed identity
   with scoped permissions rather than shared or borrowed credentials.
2. **Set policies** - Define the access rules, resource quotas, and token budgets that apply across models, tenants, and
   tools.
3. **Enforce and account** - The platform enforces those policies on every request, meters what is consumed, and
   attributes usage and cost to the responsible team.

## Every actor governed

Governance applies to people and autonomous agents alike. Users get access scoped to their role, agents get their own
non-human identities instead of borrowed credentials, and the same access rules, quotas, and audit trails cover both.
Teams can build and consume AI freely inside a governed boundary, without access being opened wider than intended.

## In this section

* [Usage Metering & Billing](usage_metering_billing.md) - Meter consumption across compute, storage, tokens, and users,
  attach costs, and export to external billing systems for showback and chargeback.
* [Role-Based Access Control](rbac.md) - Control what each user, service account, and user group can see and do, with
  Read Only or Read & Modify access to platform resources.
* [Model Governance](model_governance.md) - See every live model endpoint in one place, secure access to it, and set
  per-user limits on how models are consumed.
* [Resource Governance](resource_governance.md) - Govern shared compute with reservations, limits, spillover,
  and platform-wide visibility into utilization and cost.
* [Tokenomics](tokenomics.md) - Improve your AI tokenomics and keep AI costs under control, with per-user model routing,
  quotas, and spend visibility through the LLM Gateway.
* [Guardrails](guardrails.md) - Protect deployed models with authenticated endpoints, role-based model access, and
  content guardrails through NeMo Guardrails.
