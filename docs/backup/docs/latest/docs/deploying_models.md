# One-click Inference Service

> Klique's self-service model deployment applications, which turn trained models into monitored inference endpoints.

A model delivers value only once applications can use it. Klique turns trained models into secure, scalable inference
endpoints that applications call over the network, with versioning, automatic updates, and performance monitoring built
in so deployments keep serving reliably as models and traffic change.

Deployment is self-service: the end user deploys the model they need for themselves, rather than requesting that an
administrator deploy it for them. You can go from model to endpoint without writing serving code. Klique's model
deployment applications stand up an inference service from the [Klique WebApp](webapp/webapp_model_endpoints.md),
handling the runtime, networking, and secure access for you:

* [vLLM Deployment](webapp/applications/apps_model_deployment.md) - Serve large language models with vLLM.
* [Embedding Model Deployment](webapp/applications/apps_embed_model_deployment.md) - Deploy embedding models for search,
  retrieval, and RAG.
* [Llama.cpp Model Deployment](webapp/applications/apps_llama_deployment.md) - Serve GGUF models with Llama.cpp.
* [SGLang Model Deployment](webapp/applications/apps_sglang.md) - Serve models with the SGLang runtime.
* [NVIDIA NIM](webapp/applications/apps_nvidia_nim.md) - Deploy models packaged as NVIDIA NIM microservices.

For code-driven and CI/CD-oriented workflows, `clearml-serving` provides a command-line interface for deploying and
orchestrating model endpoints across Kubernetes clusters or custom container-based infrastructure. For more
information, see [ClearML Serving](https://clear.ml/docs/latest/docs/clearml_serving).

## Model Endpoint Monitoring

Every deployed model appears in a unified **Model Endpoints** list in the WebApp, so you can track endpoint activity,
watch performance, and manage deployments from one place. For more information, see
[Model Endpoints](webapp/webapp_model_endpoints.md).

![Model Endpoints](img/webapp_model_endpoints_monitor.png#light-mode-only)
![Model Endpoints](img/webapp_model_endpoints_monitor_dark.png#dark-mode-only)
