# What is Klique?

> Klique, an enterprise AI platform built on its Orchestration, Governance, and AI engines, and who uses it.

Klique is an enterprise AI control plane designed for company-wide resource and cost optimization. Sitting above your 
entire stack, Klique dynamically orchestrates traffic, governs token and model consumption, and manages GPU compute 
across development and deployment. It provides infrastructure teams with centralized visibility and policy enforcement 
while delivering frictionless, self-serve access to every builder and consumer—operating natively in your cloud, hybrid, 
or air-gapped environment alongside Kubernetes and Slurm.

| ![AI Usage dashboard](../img/webapp_ai_usage_dashboard.png#light-mode-only) ![AI Usage dashboard](../img/webapp_ai_usage_dashboard_dark.png#dark-mode-only) Monitor LLM token consumption across your organization | ![Pipeline](../img/webapp_pipeline_DAG.png#light-mode-only) ![Pipeline](../img/webapp_pipeline_DAG_dark.png#dark-mode-only) Build Pipeline Workflows |
|:---:|:---:|
| ![Orchestration Dashboard](../img/webapp_orchestration_dash.png#light-mode-only) ![Orchestration Dashboard](../img/webapp_orchestration_dash_dark.png#dark-mode-only) Monitor compute resource availability and utilization | ![Resource Management](../img/resource_configuration.png#light-mode-only) ![Resource Management](../img/resource_configuration_dark.png#dark-mode-only) Manage workload priority and capacity |

## How to Read the Documentation

  
    Klique Documentation Map
    
      Overview
      
        What is Klique
        Orchestration Engine
        Governance Engine
        AI Engine
      
    
    
      Klique Deployment
      
        Klique Setup
        Klique Configuration
        Platform Administration
      
    
    
      Using Klique
      
        Use Cases
        Agentic Interface
        WebApp
        References
        FAQ
      
    
  

## The three engines

Klique is built on three engines that share one control plane.

### Orchestration Engine

The Orchestration Engine turns your compute into a managed service. You connect GPUs, CPUs, and cloud AI APIs once, and
every team draws self-serve access to the resources it is cleared for, without operating Kubernetes itself. See the
[Orchestration Engine](../orchestration_engine/orchestration_engine.md).

### Governance Engine

The Governance Engine delivers real-time policy enforcement, cost controls, and identity management for every AI request. 
It continuously tracks token and compute consumption, enforcing automated budgets, quota limits, and access policies 
down to the individual user, team, or agentic workflow. By setting unified boundaries across all models, tools, and 
environments, it prevents cost overruns without creating friction for consumers. See the
[Governance Engine](../governance_engine/governance_engine.md).

### AI Engine

The AI Engine functions as an enterprise LLM gateway and application environment. Featuring smart routing and 
model-level cost controls, it automatically routes prompts, balances token usage, and governs access across all model 
vendors and internal deployments. It powers everything from developer workbenches to autonomous agents, running on 
compute managed by the Orchestration Engine. See the [AI Engine](../ai_engine/ai_engine.md).

## Who uses Klique

Klique serves three kinds of users:

- [AI Admins](klique_for_ai_admins.md) configure the platform, set policies, and monitor usage.
- [AI Builders](klique_for_ai_builders.md) build, train, and deploy models and organizational applications.
- [AI Consumers](klique_for_ai_consumers.md) use the models, agents, and applications that builders
  publish.

## How it works

Connect your hardware and cloud APIs once, define your tenants, and set your policies. Klique then routes every workload
to the right compute, enforces policy at the platform level, and surfaces real-time visibility across everything running
in your environment, without requiring the teams that use it to operate Kubernetes.

## Platform capabilities

### Orchestration Engine capabilities

- **[Resource management and policies](../orchestration_engine/resource_policies.md)**: a
  real-time dashboard of available and in-use compute; resource policies that give each user group a guaranteed
  reservation and a burst limit; resource pools and profiles that define how much compute each queue draws; and spillover
  across pools when a preferred pool is full.
- **[Fractional GPUs](../orchestration_engine/fractional_gpus.md)**: share one physical GPU across
  isolated workloads. Klique supports static and dynamic NVIDIA MIG partitioning, container-level GPU memory limits, and compute and memory partitioning of AMD Instinct GPUs.
- **[Cloud autoscaling](../orchestration_engine/cloud_autoscaling.md)**: AWS and GCP autoscalers
   that attach to queues, provision matching instances against a budget you set, and shut idle instances down.
  On-premises demand that exceeds local capacity can spillover to the cloud.
- **[Multi-tenancy](../orchestration_engine/multi_tenancy.md)**: a single deployment shared across fully
  isolated tenants, each with its own identity provider, secure network, and storage, or even virtual Kubernetes cluster (k3k), with
  compute balancing between tenants.
- **[HPC integration](../orchestration_engine/hpc.md)**: submit, schedule, and track workloads on Slurm and PBS
  through Klique. Run jobs natively or inside Singularity or Pyxis containers, or run a complete Slurm cluster as an elastic
  service on Kubernetes.
- **[Remote development environments](../orchestration_engine/gpuaas_remote_ides.md)**:
  launch SSH, JupyterLab, VS Code, and full virtual-machine desktop sessions on governed compute, with workspaces that
  persist across sessions.
- **[Inference optimization](../orchestration_engine/unified_memory_technology.md)**: Klique's unified
  memory technology extends GPU VRAM with CPU RAM and disk, so one GPU serves more models than its VRAM would normally
  hold, swapping models in and out of VRAM based on real-time demand.
- **[Platform Management Center](management_center.md)**:
  administer tenants and monitor usage, cost, and cluster utilization across every tenant from one place.

### Governance Engine capabilities

- **[Role-based access control](../governance_engine/rbac.md)**: access rules that governs users,
  service accounts, and user groups access to compute resources, model endpoints and AI projects.
- **[Resource governance](../governance_engine/resource_governance.md)**: govern shared compute by setting who can use
  it, how much each group is guaranteed, how far it can burst, and how consumption is tracked and charged back, so the
  same GPUs and CPUs serve many teams predictably.
- **[Model governance](../governance_engine/model_governance.md)**: a dashboard of every live model
  endpoint with instances, uptime, request rate, and latency. Access keys govern model access with full RBAC rules on top
  of each endpoint. Per-user or AI model token usage metering and quotas, with per-user rate limiting.
- **[Usage metering and billing](../governance_engine/usage_metering_billing.md)**:
  event-based metering of compute, storage, tokens, and users, with associated pricing to produce cost, reported per tenant,
  group, or user and per resource type. Integrate with external billing systems for showback and chargeback.
- **[Tokenomics](../governance_engine/tokenomics.md)**: pre-defined LLM routing rules per user group or project that control
  who has access to which model. Token consumption quotas per user, group, project, and
  model. Admin dashboard of AI spend surfacing usage trends and bottlenecks.
- **[Guardrails](../governance_engine/guardrails.md)**: authenticated endpoints, RBAC-scoped model access,
  and content guardrails powered by NVIDIA NeMo Guardrails, with input, dialog, retrieval, execution, and output rails
  covering content and topic safety, jailbreak and prompt-injection detection, and fact-checking.

### AI Engine capabilities

- **[AI MLOps workbench](../ai_engine/klique_ai_workbench.md)**: automatic experiment tracking of code,
  environment, hyperparameters, metrics, artifacts, and output models. Projects that organize work, a model registry with
   lineage, side-by-side experiment comparison, hyperparameter optimization, and
  [pipelines](https://clear.ml/docs/latest/docs/pipelines/) that chain steps into repeatable workflows.
- **Data management**: versioned, shareable, and reproducible datasets with [ClearML Data](https://clear.ml/docs/latest/docs/clearml_data/);
  queryable, parameterized [Hyper-Datasets](https://clear.ml/docs/latest/docs/hyperdatasets/overview/); and detachable Milvus and Qdrant
  vector-database deployment for retrieval-augmented generation.
- **Model serving and deployment**: one-click deployment applications for LLMs and embedding models, including vLLM,
  Llama.cpp, and SGLang, fronted by the AI Application Gateway with authentication. NVIDIA NIM and Dynamo deployments;
  AMD AIMs deployment, a chat UI for deployed models.
  [ClearML Serving](https://clear.ml/docs/latest/docs/clearml_serving/) for framework models with canary updates, autoscaling, and
  monitoring; Gradio and Streamlit interfaces.
- **Agentic applications**: run agentic tools such as Claude Code and Codex in governed
  [remote agentic development environments](../ai_engine/remote_agentic_development_environment.md);
  [deploy desktop agentic applications](../ai_engine/desktop_agentic_applications.md); and package
  [custom applications](../ai_engine/agentic_generated_applications.md) that run on your compute with built-in
  observability and orchestration, persistent workspaces, configurable launch forms, and a service catalog others launch
  from.
- **[Agentic Interface](../agentic_interface/agentic_interface.md)**: the MCP server and Skills that connect an AI
  agent such as Claude Code to Klique over the Model Context Protocol, so natural-language requests can create
  projects and tasks, manage applications, and run installable skills.
- **[Framework integrations](https://clear.ml/docs/latest/docs/integrations/)**: automatic tracking for PyTorch,
  TensorFlow, Keras, Hugging Face, scikit-learn, XGBoost, and many other frameworks, with minimal code changes.

![Webapp gif](../img/gif/whats_klique.gif#light-mode-only)
![Webapp gif](../img/gif/whats_klique_dark.gif#dark-mode-only)
