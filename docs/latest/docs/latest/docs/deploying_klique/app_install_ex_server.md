# External Applications Server Installation

> How to install and configure an external server to host Klique application instances using the apps agent, separate from the Klique Server.

Klique supports applications, which are extensions that allow additional capabilities, such as cloud auto-scaling, 
Hyperparameter Optimizations, etc. For more information, see [Klique Applications](../webapp/applications/apps_overview.md).

Applications run inside Docker containers, which can either reside on the Klique Server side, or on an external server. 
The `apps-agent` service polls an internal applications queue, and spawns additional Docker containers for application 
instances that are launched using the Web UI. 

This guide provides instructions on how to configure an external applications server.

## Requirements

* A server, as described in [Server Requirements](#server-requirements)
* `docker-compose.yml` file provided by Klique
* `constants.env` - Environment file with required credentials 
* Credentials to access Klique's Dockerhub registry

### Server Requirements

* Operating system: Linux-based
* CPU: Since applications do not produce a high CPU load, we recommend 2-4 virtual CPUs, assuming around 10 concurrent 
  applications are required
* Memory: Around 1 GiB of RAM is required per each concurrent application instance
* Storage: About 100 GB of storage is recommended for the system volume, with an additional 100 GB of storage for 
  application caching. In AWS, `m6a.xlarge` can be used for running up to 10 applications in parallel.

## Installation

:::note
Installing an external server requires removing the applications’ agent from the Klique Server. This 
is done by Klique in hosted environments, or by removing the `apps-agent` service from the `docker-compose` override 
file in VPC and on-premises installations. For K8s environments, please consult the Klique team.
:::

1. Install Docker. See [Docker documentation](https://docs.docker.com/engine/install/ubuntu/) 
1. Copy the `docker-compose.yml` and `constants.env` files to `/opt/allegro`. The
   `constants.env` file should contain following definitions:
    
   * `APISERVER_URL_FOR_EXTERNAL_WORKERS` - URL of the Klique API server
   * `WEBSERVER_URL_FOR_EXTERNAL_WORKERS` - URL of the Klique WebApp
   * `FILESERVER_URL_FOR_EXTERNAL_WORKERS` - URL of the Klique files server
   * `APPS_AGENT_USER_KEY` - Provided by Klique
   * `APPS_AGENT_USER_SECRET` - Provided by Klique
   * `APPS_AGENT_GIT_USER` - Provided by Klique (required up to Klique Server 1.8)
   * `APPS_AGENT_GIT_PASSWORD` - Provided by Klique (required up to Klique Server 1.8)
   * `APPS_WORKER_DOCKER_IMAGE` - Provided by Klique (required up to Klique Server 1.8)
   * `APPS_DAEMON_DOCKER_IMAGE` - Provided by Klique
   
1. Log in to the Docker registry:
    
   ```
   sudo docker login --username kliqueenterprise
   ```

1. Pull the container:

   ```
   docker compose -env-file constants.env pull
   ```

1. Start the service:

   ```
   docker compose -env-file constants.env up -d
   ```

## Clearing Stopped Containers
Containers of running applications that are stopped are not automatically deleted. Therefore, it is recommended to 
periodically delete stopped containers. This can be done by adding the following to the cron file:

```
0 0 * * * root docker container prune --force --filter "until=96h" --filter "label=allegro-type=application"
```

## Monitoring
We recommend monitoring the following:
* Available memory
* CPU usage
* Remaining Storage

For more information contact Klique's support team.
