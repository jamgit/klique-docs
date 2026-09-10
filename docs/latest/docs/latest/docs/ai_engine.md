# AI Engine

> Klique's AI Engine, the gateway and toolset spanning model building, deployment, serving, and custom application publishing.

The AI Engine is where models and AI applications are built, deployed, and consumed. It puts every model, whether
in-house, open-source, or a third-party API, behind a single gateway that routes each request to the right model
according to policy, and it provides the tools to take AI from development to production and share it across the
organization.

The AI Engine is one of Klique's three engines, alongside the
[Orchestration Engine](../orchestration_engine/orchestration_engine.md), which turns your compute into a managed
service, and the [Governance Engine](../governance_engine/governance_engine.md), which unifies security, policy, and
identity. The AI Engine builds and serves the models and applications; the Orchestration Engine runs them on governed
compute, and the Governance Engine enforces who can use what.

## What it covers

The AI Engine spans the full lifecycle of models and AI applications:

- **Build and train** models in a managed MLOps workbench.
- **Develop** in remote environments running on managed compute.
- **Deploy** models as endpoints with one-click serving, fronted by a single, governed gateway.
- **Build and publish** custom applications and agents for others to use.

## In this section

* [AI MLOps Workbench](klique_ai_workbench.md) - Run and track experiments, manage models and data, and build
  pipelines, from research to production.
* [One-click Inference Service](../deploying_models.md) - Deploy models as secure, scalable endpoints without writing
  serving code.
* [Remote Development Environment](../orchestration_engine/gpuaas_remote_ides.md) - Work in JupyterLab, VS Code, SSH, or
  a remote desktop on managed compute.
* [Vector DB and Data Management](../hyper_datasets.md) - Version and query unstructured data with Hyper-Datasets, and
  deploy vector databases on demand.
* [Agentic Desktop Applications](desktop_agentic_applications.md) - Connect agentic tools such as Claude Desktop, Claude
  Code, Codex, and ChatGPT to governed enterprise endpoints.
* [Remote Agentic Development Environment](remote_agentic_development_environment.md) - Build with agentic coding tools
  in a remote environment on managed compute.
* [Agentic Generated Applications](agentic_generated_applications.md) - Onboard the applications users build into Klique
  as managed, catalog-ready applications.
