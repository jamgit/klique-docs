# Multi-Tenancy

> Klique's secure dynamic multi-tenancy architecture, including per-tenant isolation, k3k virtual clusters, identity, and RBAC.

Klique's multi-tenancy lets a single deployment and its underlying compute be shared across multiple isolated
tenants. A **tenant** is a logically separated entity, such as different customers for a cloud service provider (CSP) or
different business units within an enterprise, each with its own users, data, and workloads.

Klique implements **secure dynamic multi-tenancy**, which combines two properties:

* **Dynamic** - Compute is not statically partitioned between tenants. Resources can move between tenants according to
  demand, so each tenant can have both dedicated resources and dynamically allocated ones.
* **Secure** - Tenants are fully isolated. A tenant cannot see, access, or communicate with another tenant's workloads,
  data, or resources.

## Architecture

![Multi-Tenant Deployment Architecture](../img/multi_tenant_deployment_architecture_diagram.png#light-mode-only)
![Multi-Tenant Deployment Architecture](../img/multi_tenant_deployment_architecture_diagram_dark.png#dark-mode-only)

Klique's multi-tenancy relies on multiple layers of isolation between tenants. Each tenant is allocated its own
Kubernetes namespace and dedicated Klique resources, such as the Klique Orchestrator and Application Gateway. The
Klique Orchestrator enforces network isolation between tenants according to specific deployment needs. In addition,
each tenant can be
divided into multiple intra-tenant groups using Klique's RBAC, adding a further layer of isolation.

### Compute and Network

Klique acts as an abstraction layer between users and the underlying Kubernetes cluster. Users submit jobs through the
Klique platform rather than interacting with the Kubernetes API directly; only cluster administrators access Kubernetes
directly. Tenant workloads are isolated using:

* Distinct Kubernetes namespaces per tenant
* Software-defined networking that keeps each tenant on its own network
* Dedicated storage volumes

As a result, tenants cannot reach each other's workloads or data over the network or the shared cluster.

## Virtual Kubernetes clusters (k3k)

For stronger isolation, Klique can give each tenant its own **virtual Kubernetes cluster** using k3k, an open source SUSE
project that runs nested ("Kubernetes in Kubernetes") clusters. Each virtual cluster runs as a set of pods on a shared
parent host cluster, with its own API server and control plane:

* The **parent (host) cluster** is managed by IT, which controls the physical nodes, storage (CSI), networking (CNI), GPU
  access, and per-tenant resource quotas.
* Each **child (virtual) cluster** is handed to a tenant with full cluster-admin access inside its own isolated
  environment. To the tenant it behaves like a dedicated cluster; to IT it is a set of pods running in a namespace.

Klique uses k3k in virtual mode, so each tenant's workloads run in a fully encapsulated cluster with its own API server.
If a pod is compromised, it stays contained within that cluster's API server boundary, with no lateral movement to the
host and no visibility into neighboring tenants.

This gives each tenant autonomy without fragmenting the shared infrastructure. Within its virtual cluster, a tenant can
install its own operators and CRDs, manage namespaces and network policies, and run specific component versions, without
IT approval cycles. Klique autoscales the underlying k3k pods across physical nodes, while IT quotas set hard limits so no
single tenant can exhaust the shared GPUs. Compared with RBAC alone, which only controls actions within a shared
cluster, virtual clusters provide the isolation of dedicated infrastructure while keeping compute consolidated.

For more information, see [K3k Deployment UI application](../webapp/applications/apps_k3k.md). 

## Identity

* Each tenant has its own dedicated SSO / Identity Provider (IdP); authentication systems are not shared between tenants.
* A user belongs to a single tenant and cannot span multiple tenants.

See [Multi-Tenant SSO Login](../deploying_klique/sso_multi_tenant_login.md) and
[Identity Providers](../user_management/identity_providers.md) for configuration.

## Access Control

Within a tenant, [RBAC](../user_management/access_rules.md) governs what each user can see and do, controlling access to
all Klique entities, such as projects, datasets, and the queues that represent hardware resources. Permissions can be
managed at the [group](../user_management/user_groups.md) or user level. Together, these mechanisms provide a
multi-layered multi-tenancy architecture.

## Administration

Tenants are created and configured from the [Platform Management Center](../whats_klique/management_center.md), where
administrators set per-tenant quotas, generate credentials, and monitor per-tenant usage and cost. For cluster setup,
see [Multi-Tenant Deployment on Kubernetes](../deploying_klique/multi_tenant_k8s.md).
