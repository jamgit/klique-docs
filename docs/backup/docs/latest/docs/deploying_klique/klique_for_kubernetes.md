# Klique for Kubernetes

> Klique's Kubernetes components across control plane, workload plane and platform services, plus configuration and validated distributions.

On Kubernetes, Klique installs as a set of Helm charts: a control plane that serves the platform and stores its data,
a workload plane that places and runs jobs on cluster compute, an application gateway that proxies between pods with
ephemeral networking and external networks and includes an LLM Gateway, a platform management center, and a usage
metering component. Each is installed separately, so a deployment can start with the Klique Server alone and add
orchestration, GPU fractioning, and application access as they are needed.

## Control plane

The control plane holds the platform itself: its API, web interface, file storage, and tenant administration.

- [Klique Server](k8s.md) installs the `control-plane` chart, which provides the `apiserver`, `fileserver`, and
  `webserver` components.
- [Klique Applications](apps_k8s.md) installs the application packages that add workload management, remote IDEs,
  and model serving to the WebApp.
- [Platform Management Center](extra_configs/platform_management_center_deploy.md) installs the `platform-management`
  chart for administering tenants, volumes, template variables, and applications across the deployment.

## Workload plane

The workload plane runs jobs on cluster compute and makes that capacity available to users through queues.

- [Klique Orchestrator](../orchestrator/deployment_k8s.md) installs the `orchestrator` chart. The Kubernetes-Glue
  controller pod maps queued jobs to Kubernetes jobs, and the Klique Executor runs inside each job pod to reproduce the
  task's environment and run it.
- Fractional GPUs let several workloads share one physical GPU. The
  [Cluster Dynamic MIG Operator](../orchestrator/fractional_gpus/cdmo.md) reconfigures NVIDIA MIG geometries
  dynamically as workload demand changes, and the
  [Cluster Fractional GPU Injector](../orchestrator/fractional_gpus/cfgi.md) caps the VRAM available to each workload
  and time-slices the GPU across processes. The two can
  [run on the same cluster](../orchestrator/fractional_gpus/cdmo_cfgi_same_cluster.md) when nodes carry different GPU
  types.
- [Klique Application Gateway](appgw_install_k8s.md) installs the `gateway` chart, which proxies browser access to
  workloads running in the cluster, such as remote IDEs and model endpoints.

## Platform services

Three additional services extend a Kubernetes deployment:

- [Klique S3 Presign Service](extra_configs/presign_service.md) installs the `presign-service` chart, letting WebApp
  users reach S3-like storage from the browser without holding S3 credentials.
- [Klique K8s Collector](extra_configs/k8s_collector.md) scrapes cluster metrics and forwards them to the Klique
  Server's Hardware Dashboard.
- [Event Metering](extra_configs/event_metering.mdx) turns on the usage metering service through the
  `usageAggregator` setting in the `control-plane` chart, and deploys the standalone `compute-resources-exporter`
  chart on the workload cluster to report its compute usage.

## Configuring the deployment

Once the cluster is running, [Klique Configuration](../klique_configuration.md) covers the settings that shape how
workloads execute on Kubernetes:

- Queue and workload scheduling, through [Custom Workload Configuration](../orchestrator/custom_workload.md),
  [Orchestration Dashboard Customization](../orchestrator/orch_dash_k8s.md), and
  [Multi-Node Scheduling](../orchestrator/multi_node_training.md).
- Pod template customization, using [string](../orchestrator/string_template.md),
  [conditional](../orchestrator/conditional_template.md), and
  [dynamic](../orchestrator/dynamic_edit_task_pod_template.md) templates to control the manifests the orchestrator
  submits.
- File caching for [datasets](../orchestrator/data_caching.md), [HuggingFace models](../orchestrator/hf_caching.md),
  and [NIM containers](../orchestrator/nim_caching.md), so repeated jobs do not re-download the same content.
- The [K8s GPU Operator](../orchestrator/fractional_gpus/gpu_operator.md) configuration recommended alongside Klique's
  fractional GPU features.
- [Multi-tenancy](multi_tenant_k8s.md), hosting multiple isolated tenants on one server, covering tenant creation,
  SSO sign-in rules, and per-tenant feature and group settings.

## Validated distributions

Klique runs on any Kubernetes distribution. The following have been validated:

- [Vanilla Kubernetes](https://kubernetes.io/)
- [OpenShift](https://www.redhat.com/en/technologies/cloud-computing/openshift)
- [RKE (Rancher Kubernetes Engine)](https://rke.docs.rancher.com/)
- [RKE2](https://docs.rke2.io/)
- [k3s](https://k3s.io/)
- [VKS (VMware vSphere Kubernetes Service)](https://www.vmware.com/products/cloud-infrastructure/vsphere-kubernetes-service)
- [NKP (Nutanix Kubernetes Platform)](https://www.nutanix.com/products/kubernetes-management-platform)
