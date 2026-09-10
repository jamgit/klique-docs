# FAQ

> Frequently asked questions covering what Klique is, deployment, security, access, orchestration, governance, and GenAI capabilities.

Answers to common questions about the Klique platform: what it is, how it is
deployed, how access and governance work, and how to get started. For a full
description of the platform, see the [platform overview](whats_klique/overview.md).

## About Klique

### What is Klique?

Klique is an end-to-end enterprise AI platform for developing, orchestrating,
and deploying AI workloads. It is organized into three layers: an
[Orchestration Engine](orchestration_engine/orchestration_engine.md) that
provisions and manages compute across cloud and on-premises environments, an
AI Development Center for training, tracking, and versioning models and data,
and a GenAI App Engine that deploys large language models and Retrieval
Augmented Generation (RAG) workloads. Each layer is cloud and on-premises
agnostic.

### How does Klique relate to ClearML?

Klique and ClearML are separate products with separate documentation sites.
Klique's capabilities form a superset of ClearML's, extending them with
enterprise orchestration, governance, and GenAI deployment. Klique reuses
shared building blocks such as the ClearML SDK and the ClearML Agent, which
keep the ClearML name. ClearML's open source and hosted offerings are
documented elsewhere and are outside the scope of these docs.

### Is there a Klique SDK?

No. Klique uses the [ClearML SDK](https://clear.ml/docs/latest/docs/clearml_sdk/) (installed with
`pip install clearml`) and the [ClearML Agent](https://clear.ml/docs/latest/docs/clearml_agent/), both of which
keep the ClearML name. The allegroai SDK is the Legacy SDK (allegroai) and
receives critical bug fixes only.

## Deployment

### How is Klique deployed?

Klique deploys entirely within your own infrastructure. The primary method is a
Helm chart on Kubernetes (the `control-plane` chart), which runs on managed
Kubernetes services such as EKS, GKE, AKS, and OpenShift, as well as on
self-managed clusters. A Docker Compose deployment is available for single-node
and evaluation setups. See [Klique Server](whats_klique/klique_server.md)
for the full set of options.

### Can Klique run on-premises, in the cloud, or in a hybrid setup?

Yes. Klique supports on-premises, cloud, and hybrid deployments, and a single
installation can orchestrate workloads across all of them. For cloud
installations, see the [AWS VPC guide](deploying_klique/vpc_aws.md);
for on-premises installations, see the
[Ubuntu on-premises guide](deploying_klique/on_prem_ubuntu.md).

### Does Klique support air-gapped (offline) environments?

Yes. Klique can be installed in air-gapped environments with no internet
access, using a private container registry to mirror images and locally hosted
Python packages. See [air-gapped deployment](deploying_klique/air_gapped_env.md).

### What are the infrastructure requirements?

A Kubernetes installation is typically sized for three or more nodes, each with
around 8 vCPUs, 32 GB RAM, and 500 GB of storage, and it includes MongoDB,
Elasticsearch, and Valkey as backing services. A single-node Docker Compose
setup runs on a server with 8 CPUs and 32 GB RAM plus dedicated data disks.
Exact requirements depend on scale; see the
[Kubernetes deployment guide](deploying_klique/k8s.md).

## Security and data

### Where does my data live?

Because Klique runs inside your own infrastructure, your data, models,
experiments, and deployed applications stay in your environment. Klique does not
require sending them to an external service, which is what makes air-gapped and
on-premises installations possible.

### How is access secured?

Klique authenticates users through your existing identity provider and controls
what each user can do with role-based access control. See the sections on
[identity](#identity-and-access) and [governance](#governance-and-cost) below. 

### What about compliance certifications?

Klique deploys within your own environment and integrates with your identity
provider, so data handling remains under your control. For questions about
specific certifications or contractual data-handling requirements, contact
[support@clearml.ai](mailto:support@clearml.ai).

## Identity and access

### Does Klique support SSO?

Yes. Klique integrates with external identity providers over OpenID Connect
(OIDC) and SAML, including Okta, Microsoft Entra ID (Azure AD), Microsoft AD,
Google, Keycloak, Amazon Cognito, and Ping Identity, as well as LDAP. See
[identity providers](user_management/identity_providers.md).

### How does role-based access control work?

Access is managed through users, user groups, and service accounts, each granted
Read Only or Read and Modify access to resources such as projects, tasks,
models, datasets, queues, and application categories. See
[access rules](user_management/access_rules.md) and
[user groups](user_management/user_groups.md).

### What are the AI Admin, AI Builder, and AI Consumer views?

These are the three UI views in the Klique WebApp. Switching views changes the
sidebar and landing page to suit administration, model building, or consuming
deployed applications. They are views, not permissions: switching a view does
not change what a user is allowed to do, which is set by role-based access
control.

### Does Klique support multiple tenants?

Yes. Klique supports multi-tenancy, where each tenant is isolated with its own
identity provider, Kubernetes namespaces, network isolation, and dedicated
storage. Compute is not statically partitioned and can move between tenants. See
[multi-tenancy](orchestration_engine/multi_tenancy.md).

## Orchestration and compute

### How does Klique manage GPUs and compute?

The [Orchestration Engine](orchestration_engine/orchestration_engine.md)
provisions and schedules GPU and CPU resources across on-premises, cloud,
hybrid, and multi-cloud environments through queues, presenting them as a
unified GPU-as-a-Service offering.

### Can multiple workloads share a GPU?

Yes. Klique supports [fractional GPUs](orchestration_engine/fractional_gpus.md),
including container-based memory limits and Kubernetes MIG slicing, so a single
GPU can be shared across workloads.

### Does Klique scale compute automatically?

Yes. Cloud autoscaling spins compute instances up and down based on demand and
budget on AWS and GCP. See [cloud autoscaling](orchestration_engine/cloud_autoscaling.md).
Klique also runs on HPC schedulers such as Slurm and PBS; see [HPC](orchestration_engine/hpc.md).

## Governance and cost

### What does the Governance Engine provide?

The [Governance Engine](governance_engine/governance_engine.md) covers
role-based access control, [model governance](governance_engine/model_governance.md)
for deployed endpoints, [guardrails](governance_engine/guardrails.md), and
[resource governance](governance_engine/resource_governance.md).

### Can I track and control usage and cost?

Yes. [Usage metering and billing](governance_engine/usage_metering_billing.md)
tracks consumption of compute, storage, and tokens across users, groups, and
tenants, and can feed external billing systems. Usage quotas and rate limiting
control per-user consumption.

## GenAI and models

### What can I deploy with the AI Engine?

The [AI Engine](ai_engine/ai_engine.md) deploys large language models into GPU
clusters and manages RAG workloads. Deployed services are reached through
secure, authenticated network endpoints and are governed by role-based access
control.

## Integrations

### Which frameworks and tools does Klique integrate with?

Klique integrates with common ML frameworks including PyTorch, TensorFlow,
Keras, scikit-learn, XGBoost, LightGBM, and CatBoost, with libraries such as
Hugging Face Transformers, PyTorch Lightning, LangChain, Optuna, and Hydra, and
with visualization tools including TensorBoard and Matplotlib. See the full
[integrations](https://clear.ml/docs/latest/docs/integrations) list.

### What storage backends are supported?

Klique works with AWS S3, Azure Storage, Google Cloud Storage, NFS, local
storage, and HTTP(S) sources. See [storage](https://clear.ml/docs/latest/docs/integrations/storage/).

## Getting started and support

### How do I get started?

Start with the [platform overview](whats_klique/overview.md). 
<!--TODO MAKE GETTING_STARTED PAGE and the [getting started guide](getting_started/main.md) --> 

Installation begins with the
`control-plane` Helm chart on Kubernetes as described in the
[deployment documentation](whats_klique/klique_server.md).

### How do I get support?

For support, contact [support@clearml.ai](mailto:support@clearml.ai).
