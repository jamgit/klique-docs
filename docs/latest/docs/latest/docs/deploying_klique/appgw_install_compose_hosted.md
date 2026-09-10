# Docker-Compose - Hosted Server

> Docker-compose installation of the AI Application Gateway router and LLM Gateway for a Klique hosted control plane with self-hosted workload nodes.

The AI Application Gateway enables external access to tasks and applications running on workload nodes that
require HTTP or TCP access. The gateway is configured with an endpoint or external address, making these services
accessible from the user's machine, outside the workload nodes’ network. It also fronts the [LLM Gateway](#llm-gateway),
a single OpenAI-compatible endpoint for Klique-managed models.

This guide details the installation of the App Gateway Router for Klique users who use Klique's hosted control
plane while hosting their own workload nodes.

## Requirements

* Linux OS (x86) machine with root access
* The machine needs to be reachable from your user network
* The machine needs to have network reachability to workload nodes
* Credentials for the Klique docker repository
* A valid Klique Server installation

Additionally, for a secure connection, it is recommended to have a DNS entry and a valid SSL Certificate assigned to the machine IP.

## Host Configuration

### Docker Installation

Installing `docker` and `docker-compose` might vary depending on the specific operating system you're using. Here is an
example for AmazonLinux:

```
sudo dnf -y install docker
DOCKER_CONFIG="/usr/local/lib/docker"
sudo mkdir -p $DOCKER_CONFIG/cli-plugins
sudo curl -SL https://github.com/docker/compose/releases/download/v2.17.3/docker-compose-linux-x86_64 -o $DOCKER_CONFIG/cli-plugins/docker-compose
sudo chmod +x $DOCKER_CONFIG/cli-plugins/docker-compose
sudo systemctl enable docker
sudo systemctl start docker

sudo docker login
```

Use the Klique docker hub credentials when prompted by `docker` login.

### Docker-compose File

This is an example of the `docker-compose` file you will need to create. Alongside the `ai-gateway-proxy` and
`ai-gateway-router` services, it runs the two [LLM Gateway](#llm-gateway) containers, `ai-gateway-llm` and
`ai-gateway-secrets-init`:

```yaml
version: "3.5"
services:
  ai-gateway-proxy:
    image: clearml/ai-gateway-proxy:${PROXY_TAG:?err}
    restart: unless-stopped
    container_name: ai-gateway-proxy
    ports:
      - "${ROUTER__WEBSERVER__SERVER_PORT}:${ROUTER__WEBSERVER__SERVER_PORT}"
      - "${STREAM_PORT_START}-${STREAM_PORT_END}:${STREAM_PORT_START}-${STREAM_PORT_END}"
    volumes:
      - ./application-gateway/config/nginx:/etc/nginx/conf.d:ro
      - ./application-gateway/config/lua:/usr/local/openresty/nginx/lua:ro
    environment:
      - ROUTER__WEBSERVER__AUTO_RELOAD_CONFIG=true
  ai-gateway-router:
    image: clearml/ai-gateway-router:${ROUTER_TAG:?err}
    restart: unless-stopped
    container_name: ai-gateway-router
    depends_on:
      ai-gateway-secrets-init:
        condition: service_completed_successfully
      ai-gateway-llm:
        condition: service_started
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./application-gateway/config/nginx:/etc/nginx/conf.d:rw
      - ./application-gateway/config/lua:/usr/local/openresty/nginx/lua:rw
      - ./application-gateway/secrets:/etc/app-gateway/secrets:ro
    environment:
      - ROUTER__WEBSERVER__AUTO_RELOAD_CONFIG=true
      - ROUTER_NAME=${ROUTER_NAME:?err}
      - LISTEN_QUEUE_NAME=${LISTEN_QUEUE_NAME:-}
      - ROUTER__WEBSERVER__SERVER_PORT=${ROUTER__WEBSERVER__SERVER_PORT:?err}
      - ROUTER_URL=${ROUTER_URL:?err}
      - CLEARML_API_HOST=${CLEARML_API_HOST:?err}
      - CLEARML_API_ACCESS_KEY=${CLEARML_API_ACCESS_KEY:?err}
      - CLEARML_API_SECRET_KEY=${CLEARML_API_SECRET_KEY:?err}
      - AUTH_COOKIE_NAME=${AUTH_COOKIE_NAME:?err}
      - ROUTER__HTTP__AUTHORIZATION__COOKIE__SECURE=${AUTH_SECURE_ENABLED}
      - ROUTER__STREAM__EXTERNAL_URL=${STREAM_ROUTER_ADDRESS}
      - ROUTER__STREAM__PORT_RANGE__START=${STREAM_PORT_START}
      - ROUTER__STREAM__PORT_RANGE__END=${STREAM_PORT_END}
  ai-gateway-secrets-init:
    image: apache/apisix:${LLM_GATEWAY_TAG:-3.17.0-debian}
    container_name: ai-gateway-secrets-init
    user: "0:0"
    restart: "no"
    volumes:
      - ./application-gateway/secrets:/etc/app-gateway/secrets
    entrypoint:
      - /bin/sh
      - -c
      - |
        set -e
        secrets_dir=/etc/app-gateway/secrets
        admin_file="$$secrets_dir/apisix_admin_key"
        internal_file="$$secrets_dir/internal_key_secret"
        mkdir -p "$$secrets_dir"
        gen() { od -An -tx1 -N32 /dev/urandom | tr -d ' \n'; }
        write_atomic() { tmp="$$1.tmp"; cat > "$$tmp"; chmod 0644 "$$tmp"; mv "$$tmp" "$$1"; }
        if [ -n "$$APISIX_ADMIN_KEY" ]; then
          printf '%s' "$$APISIX_ADMIN_KEY" | write_atomic "$$admin_file"
        elif [ ! -s "$$admin_file" ]; then
          gen | write_atomic "$$admin_file"
        fi
        [ -s "$$internal_file" ] || gen | write_atomic "$$internal_file"
        echo "gateway secrets ready in $$secrets_dir"
  ai-gateway-llm:
    image: apache/apisix:${LLM_GATEWAY_TAG:-3.17.0-debian}
    restart: unless-stopped
    container_name: ai-gateway-llm
    depends_on:
      ai-gateway-secrets-init:
        condition: service_completed_successfully
    volumes:
      - ./application-gateway/secrets:/etc/app-gateway/secrets:ro
    configs:
      - source: apisix_config
        target: /usr/local/apisix/conf/config.yaml
    entrypoint:
      - /bin/sh
      - -c
      - |
        set -e
        APISIX_ADMIN_KEY="$$(cat /etc/app-gateway/secrets/apisix_admin_key)"
        export APISIX_ADMIN_KEY
        exec /docker-entrypoint.sh docker-start

# Static configuration for the ai-gateway-llm data plane, materialized into the container by Compose.
configs:
  apisix_config:
    content: |
      # Static configuration for the AI Gateway LLM data plane.
      apisix:
        node_listen: 9080
        enable_ipv6: false
      deployment:
        role: traditional
        role_traditional:
          config_provider: yaml
        admin:
          admin_listen:
            ip: 0.0.0.0
            port: 9180
          allow_admin:
            - 0.0.0.0/0
          admin_key:
            - name: admin
              key: $${{APISIX_ADMIN_KEY}}
              role: admin
      nginx_config:
        error_log_level: warn
      plugin_attr:
        prometheus:
          export_addr:
            ip: 0.0.0.0
            port: 9091
          export_uri: /apisix/prometheus/metrics
```

### Configuration File

You will be provided with a prefilled `runtime.env` file containing the following entries:

```
# PREFILLED SECTION, PROVIDED BY KLIQUE
PROXY_TAG=
ROUTER_TAG=
LLM_GATEWAY_TAG=3.17.0-debian
CLEARML_API_HOST=https://api.
AUTH_COOKIE_NAME=

# TO BE FILLED BY USER
ROUTER_NAME=main-router
ROUTER__WEBSERVER__SERVER_PORT=8010
ROUTER_URL=http://<ROUTER-HOST-PUBLIC-IP>:8010
CLEARML_API_ACCESS_KEY=
CLEARML_API_SECRET_KEY=
AUTH_SECURE_ENABLED=true
STREAM_ROUTER_ADDRESS=<ROUTER-HOST-PUBLIC-IP>
STREAM_PORT_START=
STREAM_PORT_END=
```

**Configuration Options:**

* `LLM_GATEWAY_TAG`: [LLM Gateway](#llm-gateway) data plane tag. The image tag used by the `ai-gateway-llm`
  and `ai-gateway-secrets-init` containers, provided as part of the prefilled section.
* `ROUTER_NAME`: In the case of [multiple routers on the same tenant](#multiple-router-in-the-same-tenant), each router
   needs to have a unique name.
* `ROUTER__WEBSERVER__SERVER_PORT`: Webserver port used by the router. The default port is 8080, but it can be adjusted to meet specific network requirements.
* `CLEARML_API_ACCESS_KEY`, `CLEARML_API_SECRET_KEY`: API credentials for Admin user or Service Account with admin privileges
  created in the Klique web UI. Make sure to label these credentials clearly, so that they will not be revoked by mistake.
* `ROUTER_URL`: External address to access the router. This can be the IP address or DNS of the node where the router
   is running, or the address of a load balancer if the router operates behind a proxy/load balancer. This URL is used
   to access AI workload applications (e.g. remote IDE, model deployment, etc.), so it must be reachable and resolvable for them.
* `STREAM_ROUTER_ADDRESS`: Router external address, can be an IP or the host machine or a load balancer hostname, depends on network configuration.
* `STREAM_PORT_START`: Start port for the TCP-UDP Session feature, chosen by the customer. Ensure that ports are open and can be allocated on the host.
* `STREAM_PORT_END`: End port for the TCP-UDP Session feature, chosen by the customer. Ensure that ports are open and can be allocated on the host.

### Installation

Run the following command to start the router:

```
sudo docker compose --env-file runtime.env up -d
```

## LLM Gateway

The App Gateway includes the LLM Gateway, a single OpenAI-compatible endpoint that fronts your Klique-managed model
endpoints. Users call it with their Klique credentials, and the gateway enforces per-user and per-group model access,
applies token quotas, and meters usage, without users ever handling provider API keys. For its models, access rules, and
quotas, see [LLM Gateway](../whats_klique/llm_gateway.md).

The LLM Gateway is part of the standard deployment. The `docker-compose` file above runs it as two additional containers
alongside the proxy and router:

* `ai-gateway-llm`: the LLM Gateway data plane. It holds the model routes, access rules, and quotas in memory and
  forwards requests to the configured model endpoints. Clients reach it through the proxy at
  `<ROUTER_URL>/llm/<model-name>/v1/...`.
* `ai-gateway-secrets-init`: a one-shot container that generates the gateway's shared secrets on first start and then
  exits. The router and the data plane read these secrets from the shared `./application-gateway/secrets` volume, so no
  keys need to be set by hand.

Because the gateway containers share the same `docker-compose` network, the proxy can forward LLM traffic to the data 
plane by container name. Models, model access, and token quotas are managed in the Klique WebApp under
**Settings > LLM Gateway**. The gateway polls this configuration and keeps itself in sync, so there is nothing to edit on
the gateway host. Usage is metered per user and model and surfaced in the Admin Dashboard's
[AI Usage](../webapp/webapp_admin_dashboard_ai_usage.md) tab.

## Advanced Configuration

### Using Open HTTP

To deploy the App Gateway Router on open HTTP (without a certificate), set the `AUTH_SECURE_ENABLED` entry
to `false` in the `runtime.env` file.

### Multiple Router in the Same Tenant

 If you have workloads running in separate networks that cannot communicate with each other, you need to deploy multiple
 routers, one for each isolated environment. Each router will only process tasks from designated queues, ensuring that
 tasks are correctly routed to agents within the same network.

 For example:
 * If Agent A and Agent B are in separate networks, each must have its own router to receive tasks.
 * Router A will handle tasks from Agent A’s queues. Router B will handle tasks from Agent B’s queues.

 To achieve this, each router must be configured with:
 * A unique `ROUTER_NAME`
 * A distinct set of queues defined in `LISTEN_QUEUE_NAME`.

 #### Example Configuration
 Each router's `runtime.env` file should include:

 * Router A:

   ```
   ROUTER_NAME=router-a
   LISTEN_QUEUE_NAME=queue1 queue2
   ```

 * Router B:

   ```
   ROUTER_NAME=router-b
   LISTEN_QUEUE_NAME=queue3 queue4
   ```

 The `docker-compose` file above already passes `LISTEN_QUEUE_NAME` to the router, so setting it in each router's
 `runtime.env` is enough.

## Monitoring and Testing the Gateway

Once your gateway is deployed, you can monitor its status, view routed tasks, and run connectivity tests in the
WebApp, under **Settings > Application Gateway**.

![App Gateway Test](../img/settings_app_gateway_test.png#light-mode-only)
![App Gateway Test](../img/settings_app_gateway_test_dark.png#dark-mode-only)
