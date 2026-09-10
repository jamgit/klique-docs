# Agentic Generated Applications

> How Klique onboards user-built applications, including agentic coding tool output, as managed, catalog-ready applications.

Agentic Generated Applications are Klique's way for users to build their own
Klique applications, including applications built with agentic coding tools, and
onboard them into Klique as managed applications. Once onboarded, an application
runs on your compute, gains Klique's built-in observability and orchestration,
and becomes a catalog item that others can launch on their own leveraging Klique's 
orchestration engine.

The goal is to let customers bring their own applications and logic into Klique
rather than being limited to the built-in applications, and to assemble a
service catalog of many items that teams across the organization can use.

## What a custom application is

A custom application is onboarded from a template you upload: a single
configuration file that describes the application, together with its code and
assets. Klique stores the template and registers the application, which then
appears in the [app catalog](../webapp/applications/apps_overview.md) under **My
Apps**. Installing a custom application is a self-service action
available to AI Builders, not only administrators.

Under the hood, each running instance is a Klique task of type "application"
created from the template. Its lifecycle, including status, idle auto-stop, and
external endpoint, is managed by the platform, so you do not run a separate
controller process.

## Delivering your code

An application's code can reach the compute node three ways, chosen per
application:

- **Docker**: the code is baked into a container image.
- **Git**: Klique clones your repository and installs its requirements.
- **Code**: the files are uploaded with the application and written to disk when
  it starts.

This is what lets applications produced with agentic tooling be onboarded as
they are. Whatever an agent generates, whether a repository, a script, or a
container, fits one of these modes.

## From agentic environment to catalog item

Klique's built-in agentic development environments, such as
[Claude Code and Codex](remote_agentic_development_environment.md), include tools that turn an application a user writes
into a Klique catalog item automatically. The user builds the application in the agentic environment as usual, and the
tooling packages it, onboards it as a custom application, and registers it in the catalog, so it appears under My
Apps ready to launch, without anyone assembling the template or configuration by hand.

## Built-in observability and orchestration

Once onboarded, a custom application gets the same capabilities as Klique's
built-in applications, without additional work:

- **Orchestration**: instances are enqueued to a
  [queue](https://clear.ml/docs/latest/docs/fundamentals/agents_and_queues/#what-is-a-queue) and run by the
  [Klique Orchestrator](../orchestrator.md) on the compute you make available, with
  the [Orchestration Engine](../orchestration_engine/orchestration_engine.md)
  handling scheduling and scaling.
- **Endpoints**: when an application exposes a port, Klique provisions a secure,
  authenticated external endpoint through the
  [AI Application Gateway](../whats_klique/appgw.md), with
  an optional readiness probe.
- **Observability**: each instance has a dashboard with status, resource metrics
  (CPU, GPU, network), and console logs, and its state is monitored
  continuously.
- **Persistence**: a designated workspace directory is persisted across
  restarts, so a stopped instance can be cloned and resumed.
- **A launch form**: a wizard defines the parameters users fill in when
  launching an instance, so others can run the application without editing code.

## A service catalog

Because every onboarded application becomes a catalog item with its own launch
form, customers can offer a service catalog of many applications that teams
launch on their own. This turns internal applications and logic into shared,
governed services rather than one-off scripts, and it is how an organization
offers its AI capabilities as self-service catalog items.

For how to build and install a custom application, see
[Building Custom Applications](../deploying_klique/app_custom.md).
