# Platform Administration

> Platform Administration: the Platform Management Center, migration, tenant lifecycle, backup, security, debugging, and monitoring.

Platform Administration covers centralized administration of a Klique deployment through the Platform Management
Center, and the operational procedures you run against a live deployment as the need arises: backups, data
migrations, tenant removal, debugging, vulnerability scanning, and monitoring.

## Platform Management Center

The [Platform Management Center](platform_management_center/pmc_overview.md) is the administrative interface for a
multi-tenant Klique deployment: platform administrators create and configure tenants, view usage and cost across all
of them, and roll out shared storage configuration, template variables, and applications from a single place.

## Migration

- [Project Migration](deploying_klique/import_projects.md): transfer projects when migrating from a
  ClearML open source server to Klique.
- [Changing Klique Artifacts Links](deploying_klique/change_artifact_links.md): update artifact
  storage references after moving external storage to a different location or URL.

## Tenant Lifecycle

- [Deleting Tenants](deploying_klique/delete_tenant.md): remove a tenant (company, workspace) and
  its data.

## Backup, Security, and Debugging

- [Backup](deploying_klique/extra_configs/backups.md): back up the File Server, ElasticSearch, and
  MongoDB periodically to protect against data loss.
- [Accurate Vulnerability Scanning of Klique Images](deploying_klique/image_vulnerability_scanning.md):
  interpret third-party vulnerability scan results correctly against Klique's hardened images.
- [Container and Pod Debugging](deploying_klique/container_debug.md): inspect running containers
  and troubleshoot startup and runtime issues on Kubernetes or Docker Compose.

## Server Monitoring

Recommended metrics and practices for keeping a deployment reliable: [Kubernetes](deploying_klique/extra_configs/monitoring_k8s.md)
and [VM / Docker Compose](deploying_klique/extra_configs/monitoring_vm_docker.md).
