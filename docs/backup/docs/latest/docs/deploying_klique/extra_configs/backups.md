# Backup

> Recommended backup practices for Klique's File Server, Elasticsearch, and MongoDB storage components on Kubernetes and on-premise deployments.

Klique stores its data in multiple storage components:
* [File Server](#file-server)
* [Elasticsearch](#elasticsearch)
* [MongoDB](#mongodb)

It is recommended to back these up periodically to ensure data consistency and to avoid corruption during restore operations.

The following describes recommended practices for backing up data used by Klique deployments on Kubernetes and on-premise 
Ubuntu servers. See [Backups on AWS](../vpc_aws.md#backups) for best practices for AWS VPC deployments.

## File Server

The File Server stores task artifacts, models, and other binary assets.

### Backup Recommendations
* Back up the entire File Server volume.
* Perform backups at least daily.
* Maintain a minimum retention period of 2 days, or longer based on operational and compliance requirements.

### Deployment Specific Notes
* Docker Compose - Back up the Docker volume or host directory mounted into the File Server container.
* Kubernetes - Back up the PersistentVolume (PV) used by the File Server using the cluster’s storage-level snapshot or backup solution.

Filesystem-level backups are sufficient for the File Server, as it does not maintain internal transactional state.

## Elasticsearch

Elasticsearch stores indexed metadata and task-related information that must remain internally consistent.

:::important  
Elastic cannot be safely backed up by copying data directories or underlying disks.  
Such approaches may capture partial shard states or inconsistent indices, leading to corrupted snapshots or failed restores.  
:::

### Recommended Approach

Use Elasticsearch snapshots, which are designed to safely and consistently back up indices and cluster state.

Refer to [ElasticSearch documentation](https://www.elastic.co/docs/deploy-manage/tools/snapshot-and-restore) 
for instructions for creating snapshots.

### Deployment Specific Notes

* Docker Compose - Configure a snapshot repository (for example, a filesystem path or S3-compatible object storage) and 
  trigger snapshot operations using the Elasticsearch API.
* Kubernetes - Use the same snapshot mechanisms, typically targeting external object storage. The exact setup depends on
  the Elasticsearch version, deployment method, and whether an operator is used.

## MongoDB

MongoDB stores Klique metadata and requires consistent, database-aware backups.

:::important  
Backing up MongoDB by copying data directories or disks while the database is running is unsafe.  
:::

### Recommended Approach

Use MongoDB-supported backup methods, such as:
* Filesystem snapshots coordinated with MongoDB
* Replica-set-aware or managed backup solutions

Refer to [MongoDB’s documentation](https://www.mongodb.com/docs/manual/core/backups/) for backing up and restoring instructions.

### Deployment Specific Notes
* Docker Compose - Execute backup commands using MongoDB tools against the running service, or run backups from a 
  dedicated backup container or scheduled job.
* Kubernetes - Use Kubernetes Jobs, database operators, or managed database services to perform backups in accordance 
  with MongoDB best practices.
