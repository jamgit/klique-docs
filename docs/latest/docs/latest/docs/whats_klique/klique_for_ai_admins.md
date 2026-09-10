# Klique for AI Admins

> Klique overview for AI Admins, covering resource policies, vaults, access control, LLM governance, and usage monitoring.

AI Admins configure Klique so AI builders and consumers can use it. Working in the AI Admin view of the WebApp, they set
up the compute, policies, access, and applications that everyone else works within, and they monitor how the platform
is used to keep it efficient and under control.

![Infrastructure control plane](../img/gif/ai_admins.gif#light-mode-only)
![Infrastructure control plane](../img/gif/ai_admins_dark.gif#dark-mode-only)

## Configure resources and policies

AI Admins set up resource configurations and the policies that govern them. They define spillover rules between resource
types, so workloads move from one pool to another when capacity runs out, and set quotas per team so each gets its share
of compute. See [resource governance](../governance_engine/resource_governance.md) and
[Resource Management](../orchestration_engine/resource_policies.md).

![Resource Policy dashboard](../img/resource_policies_dashboard.png#light-mode-only)
![Resource Policy dashboard](../img/resource_policies_dashboard_dark.png#dark-mode-only)

## Set up Administrator Vaults

AI Admins manage configuration and secrets centrally so they can be used by multiple users throughout the organization.
[Administrator vaults](../user_management/admin_vaults.md) hold configuration and secrets, such as third-party service
credentials, that Klique applies centrally to workloads run by different users within the organization. AI Admins also
set the central storage configuration, defining where artifacts, models, and data are stored and the credentials used
to reach it.

## Control access

AI Admins set role-based access control (RBAC) rules on compute resources, projects, and model endpoints, so
each user and group can reach only what its role permits. See
[role-based access control](../governance_engine/rbac.md) and
[model governance](../governance_engine/model_governance.md).

![Access rules](../img/settings_access_rules.png#light-mode-only)
![Access rules](../img/settings_access_rules_dark.png#dark-mode-only)

## Govern LLM usage

AI Admins define LLM Gateway rules and quotas: which models each user or group can reach, and how many tokens they can
consume. See [Tokenomics](../governance_engine/tokenomics.md).

## Manage application catalog

AI Admins decide which applications are available to which users and groups. They also manage the enterprise
applications that builders create, publishing them so other users of the platform, such as AI consumers, can use them.
See [application visibility](../deploying_klique/app_visibility_policy.md) and
[Agentic Generated Applications](../ai_engine/agentic_generated_applications.md).

## Monitor and manage

AI Admins monitor platform usage and make informed decisions from it. They can track the activity of compute resources,
users, and agents to find bottlenecks, usage patterns, and under-utilized resources, and follow usage and cost across
teams. See
[usage metering and billing](../governance_engine/usage_metering_billing.md) and the
[Platform Management Center](management_center.md).

## What's next

1. Deploy the Klique Server on Kubernetes with the
   [Kubernetes installation instructions](../deploying_klique/k8s.md).
2. Add organizational compute resources by installing the [Klique Orchestrator](../orchestrator.md).
3. Integrate your [identity provider](../user_management/identity_providers.md) with Klique.
4. Set up [resource policies and quotas](../orchestration_engine/resource_policies.md).
5. Set up LLM quotas with Klique's LLM Gateway. See [Tokenomics](../governance_engine/tokenomics.md).
