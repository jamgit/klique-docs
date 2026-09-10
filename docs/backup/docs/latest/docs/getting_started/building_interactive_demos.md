# Building Interactive Model Demos

> Building interactive model demos with the Gradio and Streamlit apps, including app instance setup and the resulting externally accessible link.

Klique supports creating web-based interfaces directly from a script, serving a model interactively without
standing up your own web server.

Klique provides the following applications for building an interactive model interface:
* [Gradio](../webapp/applications/apps_gradio.md)
* [Streamlit](../webapp/applications/apps_streamlit.md)

![Streamlit Dashboard](../img/apps_streamlit.png#light-mode-only)
![Streamlit Dashboard](../img/apps_streamlit_dark.png#dark-mode-only) 

## Launch the Demo

When you create a new app instance, configure the following:
* **Git Repository and Branch** - Where the demo script lives.
* **Script Name** - The Gradio or Streamlit script to execute.
* **Docker Image** - The image the Klique Executor uses to run it.
* **Arguments** - Passed to the script at launch.
* **Queue** - The queue serviced by the Klique Orchestrator that will run the app; make sure an agent is assigned
  to it.

With the app instance configured, launching enqueues an app instance task to that queue, and the [Klique Orchestrator](../orchestrator.md) servicing
the queue builds an execution environment and runs the Gradio or Streamlit Launcher in it. The launcher then serves your 
app through an externally accessible link, authenticated and access-controlled by the
[AI Application Gateway](../whats_klique/appgw.md).

This is a front end for trying a model interactively, not a production inference endpoint; to serve a model as a
scalable, governed endpoint, see [Deploying Model Endpoints](deploying_model_endpoints.md).
