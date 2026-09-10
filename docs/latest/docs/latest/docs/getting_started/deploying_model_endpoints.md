# Deploying Model Endpoints

> The workflow for deploying a model as a live endpoint with Klique's vLLM, Embedding, Llama.cpp, SGLang, and NVIDIA NIM apps, or clearml-serving.

Model deployment makes trained models accessible for real-world applications. You can deploy a model as a live endpoint through one of 
Klique's [Deployment Applications](../webapp/applications/apps_overview.md):
a no-code launcher that stands up the serving runtime, networking, and secure access for you, so you don't write
serving code yourself. 

Klique provides the following apps for model deployment:
* [NVIDIA NIM](../webapp/applications/apps_nvidia_nim.md)
* [Embedding Model Deployment](../webapp/applications/apps_embed_model_deployment.md)
* [vLLM Model Deployment](../webapp/applications/apps_model_deployment.md)
* [Llama.cpp Model Deployment](../webapp/applications/apps_llama_deployment.md)
* [SGLang Model Deployment](../webapp/applications/apps_sglang.md)

To deploy with NVIDIA NIM, provide a **NIM Container Image** from NVIDIA's catalog: a vendor-packaged model container
(requires an NGC API key).

For the rest of the apps, provide a model from one of two sources:
* A **Klique Model ID**, for a model already in your
  [model registry](https://clear.ml/docs/latest/docs/model_registry/#registering-models) (for example, the output
  of a training task).
* A **Hugging Face model name**, fetched directly by the app with no separate registration step (Llama.cpp
  additionally requires the model in GGUF format).

Once launched, each app serves its model behind a secure, publicly accessible endpoint, which then appears alongside
every other live deployment in [Model Endpoints](../webapp/webapp_model_endpoints.md) for monitoring.

## ClearML Serving

For code-driven and CI/CD-oriented workflows, you can use `clearml-serving`: a command-line interface for
deploying and orchestrating model endpoints on Kubernetes clusters or custom container-based infrastructure. See
[ClearML Serving](https://clear.ml/docs/latest/docs/clearml_serving) for more information.
