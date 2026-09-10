# Tokenomics

> Klique's tokenomics capabilities for controlling LLM model access, quotas, and cost visibility through the LLM Gateway.

Klique's tokenomics capabilities govern the consumption and cost of large language models (LLMs) across the
organization. Using the [LLM Gateway](../whats_klique/llm_gateway.md), administrators decide who can
use which models, how much, and at what cost, and they get the visibility to attribute AI spend and build services on
top of it.

## Model routing and access

Administrators build LLM routing rules per user group or project. A rule controls who can reach external models and who
is limited to internal models, and who can use large models versus only smaller ones. Because the rules are enforced at
the gateway, access and cost decisions are centralized rather than configured application by application.

## Quotas

Administrators set token quotas per user, group, or project, and per model, to cap how much each can consume. This helps
manage tokenomics and prevent runaway AI costs.

![LLM Gateway](../img/webapp_settings_llm_gateway.png#light-mode-only)
![LLM Gateway](../img/webapp_settings_llm_gateway_dark.png#dark-mode-only)

## Cost visibility and billing

Klique offers a dashboard that shows AI spend per user, group, or project, helping organizations understand LLM usage
and cost trends, plan budgets, and set quotas to prevent excessive spend. Token metering can also be connected to
external billing systems, which supports building an AI-as-a-Service offering, chargebacks, and showbacks. This is
especially important for cloud service providers and telcos building an extensive AI offering alongside
GPU-as-a-Service. Together, these capabilities help control AI costs and provide clear cost visibility across the
organization, or across managed tenants.

![AI dashboard](../img/webapp_ai_usage_dashboard.png#light-mode-only)
![AI dashboard](../img/webapp_ai_usage_dashboard_dark.png#dark-mode-only)

## How it works

Routing is transparent to users. A single endpoint can front multiple backend endpoints: consumers always call the same
endpoint, while the backend behavior, including which model serves the request, changes according to the administrator's
rules. Nothing changes on the user's side, and administrators retain full control over routing, access, and cost.
