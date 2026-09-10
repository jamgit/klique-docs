# RBAC

> Klique's role-based access control, including access rules, user groups, and service accounts within a tenant.

Klique uses role-based access control (RBAC) to govern what each user, service account, and user group can see and do
across the platform. RBAC operates within a tenant, on top of the isolation that
[Multi-Tenancy](../orchestration_engine/multi_tenancy.md) enforces between tenants.

## Access rules

Administrators use **access rules** to specify which users, service accounts, and user groups can access each workspace
resource:

* Projects
* Tasks
* Models
* Dataviews
* Datasets
* Queues
* Application categories

Each rule grants either **Read Only** or **Read & Modify** access. By default, all users have Read & Modify access to all
resources, and administrators narrow this by adding rules. Application categories always apply Read & Modify.

Access is inherited by resource hierarchy: granting access to a project also grants access to its contents (tasks,
models, and so on), while access to a specific task does not extend to other tasks in the project unless granted
explicitly. Because queues represent compute resources, access rules also control who can run workloads on which hardware.

![Access rule creation dialog](../img/settings_access_rules.png#light-mode-only)
![Access rule creation dialog](../img/settings_access_rules_dark.png#dark-mode-only)

For creating, editing, and filtering rules, see [Access Rules (RBAC)](../webapp/settings/webapp_settings_access_rules.md).

## User groups

Administrators can define [user groups](../user_management/user_groups.md) and assign access rules, and administrator
vaults, at the group level rather than to each user or service account individually. Members can be added or removed as
needed. On multi-tenant deployments,
[cross-tenant groups](../deploying_klique/multi_tenant_k8s.md#configuring-groups) can also be
configured.

## Service accounts

[Service accounts](../webapp/settings/webapp_settings_users.md#service-accounts) are non-human identities used by agents
and automation. They are assigned access rules in the same way as users, so agents run with their own scoped permissions
rather than borrowed or shared credentials.

## Multi-layered access

Within a tenant, RBAC controls what each user and service account can access. Between tenants,
[Multi-Tenancy](../orchestration_engine/multi_tenancy.md) keeps workloads, data, and resources isolated. Together they
provide a multi-layered access model across the platform.
