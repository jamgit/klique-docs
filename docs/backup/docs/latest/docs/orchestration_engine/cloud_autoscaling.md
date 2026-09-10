# Cloud Autoscaling

> Klique's cloud autoscaling, including its benefits and the AWS and GCP autoscaler applications that provision instances on demand.

Cloud autoscaling extends your compute with elastic cloud capacity, so demand that exceeds your on-premises
hardware is met automatically, and you pay only for what you use. Instead of manually provisioning virtual machines and
tearing them down when they go idle, Klique brings cloud instances up and down for you, against a budget you set.

## Klique's cloud autoscaling benefits
- **Cloud Spillover** - Resource policies allow you to define cloud spillovers when on-prem capacity is exhausted.
- **Elastic capacity** - Cloud instances are added when there is pending work and removed when they sit idle, so fixed
  on-premises capacity can absorb demand spikes without permanent over-provisioning.
- **Predictable cost** - Each autoscaler runs against a resource budget you define, the instance types and the maximum
  number of instances it may run, and idle instances are shut down automatically, so you pay only for the time instances
  actually run.
- **No manual infrastructure** - Scaling is automatic, with no code to write and no machines to provision or tear down
  by hand.
- **Your cloud account** - Autoscalers run in your own cloud account with credentials you provide, so compute and data
  stay under your control.

## Available options

Klique provides GUI autoscaler applications for the major clouds:

- [AWS Autoscaler](../webapp/applications/apps_aws_autoscaler.md) manages instances in your AWS account.
- [GCP Autoscaler](../webapp/applications/apps_gcp_autoscaler.md) manages instances in your GCP account.

Each autoscaler is attached to a [queue](https://clear.ml/docs/latest/docs/fundamentals/agents_and_queues/#what-is-a-queue) or queues and provisions the
matching instance type on demand: when work is queued it brings instances up, up to the maximum number of instances you
set, and when instances go idle it shuts them down.

![Autoscaler diagram](../img/autoscaler_single_queue_diagram.png#light-mode-only)
![Autoscaler diagram](../img/autoscaler_single_queue_diagram_dark.png#dark-mode-only)

To serve more than one resource type, for example CPU and GPU, attach autoscalers to different queues so each queue
draws the instance type it needs.

![Autoscaler diagram](../img/autoscaler_diagram.png#light-mode-only)
![Autoscaler diagram](../img/autoscaler_diagram_dark.png#dark-mode-only)

For setup and the full set of configuration options, including budget and instance limits, the default container image,
Git credentials, and cloud storage access, see the [AWS Autoscaler](../webapp/applications/apps_aws_autoscaler.md) and
[GCP Autoscaler](../webapp/applications/apps_gcp_autoscaler.md) pages.
