# Klique REST API Reference

> The Klique Server exposes a comprehensive REST API.

The Klique Server exposes a comprehensive REST API.

The API reference pages are split into two main sections:

* [Object definitions](#object-definitions) - Detailed specification of available objects
* [Service definitions](#service-definitions) - Specification of the available REST services

## Object Definitions

See the REST API object definitions reference page to view descriptions of the objects used in API requests:
* [Object definitions](definitions.md)

## Service Definitions

* [apps](apps.md) - Klique UI Application templates and instance management.
* [auth](auth.md) - User authentication, credential management, and token generation.
* [datasets](datasets.md) - Hyper-Dataset management API
* [debug](debug.md) - Debugging utilities
* [events](events.md) - Event reporting and retrieval (e.g., metrics, debug samples)
* [frames](frames.md) - Hyper-Dataset frame (entry) retrieval
* [login](login.md) - Authentication management, authorization and administration for the entire system.
* [models](models.md) - Model management: create, edit, update metadata and tags, move, publish, archive, set visibility, and retrieve models.
* [organization](organization.md) - Manage organizations, users, invites, and company metadata.
* [permissions](permissions.md) - Manage user groups, access rules, and entity-level permissions.
* [pipelines](pipelines.md) - Pipeline management: create, update, execute, monitor, and query pipelines.
* [projects](projects.md) - Project management API for creating, updating, organizing, querying, and controlling access to Projects
* [queues](queues.md) - [Queue](../../fundamentals/agents_and_queues.md#what-is-a-queue) management (see [workers](workers.md) service).
* [reports](reports.md) - Report management API for creating, editing, publishing, archiving, moving, and querying Reports.
* [resources](resources.md) - Resource policies, configurations, and allocation management.
* [routers](routers.md) - Request routing rules and task router configuration.
* [server](server.md) - Server configuration, health, status, and version information retrieval.
* [serving](serving.md) - Model serving configuration and endpoint management.
* [sso](sso.md) - Single sign-on (SSO) provider management and authentication testing.
* [storage](storage.md) - Storage settings and configuration management.
* [system](system.md) - Companies, datasets, and system-wide configuration.
* [tasks](tasks.md) - [Task](../../fundamentals/task.md) management API.
* [tenants](tenants.md) - Tenant information retrieval
* [users](users.md) - User accounts, preferences, service users, and access-related information.
* [variables](variables.md) - Global variables and counters management.
* [workers](workers.md) - Worker registration, status reporting, and task execution

## Request Format
An API request is formatted in the following way:

```console
base_url/endpoint
```

Where the `base_url` is the `api_server` configured in your `clearml.conf` file and the
endpoint is as specified for the available services. The content-type is `application/json`.

Requests need to be authenticated with a bearer token that identifies the workspace that you want to work with.
An initial request of `GET /auth.login`, using the basic authorization scheme generates this token to be used with all
subsequent API requests. The authorization header contains `Basic <credentials>` where `credentials`
is `base64("<key>:<secret>")` using your credentials (access key and secret key).

```console
curl -u "<access_key>:<secret_key>" -X GET https://<base_url>/auth.login
```

This call will return the token. By default, the token expires in 30 days. Generate a token with a shorter expiration
time by specifying the `expiration_sec` field (as a query parameter or as a JSON payload).
