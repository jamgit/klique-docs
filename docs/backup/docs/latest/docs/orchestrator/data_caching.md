# Data Cache

> How to configure the task artifact and dataset download cache directory, including a Kubernetes pod template example.

The ClearML Agent maintains a cache for the files downloaded while executing tasks (task artifacts, datasets etc.). The location 
of the cache folder can be controlled with the `CLEARML_CACHE_DIR` environment variable.

On Kubernetes, the Kubernetes-Glue is Klique's Orchestrator, and it applies this volume through its pod template
overrides, so it is mounted for the ClearML Agent process (the Klique Executor) running in each task pod. For example:

```yaml
orchestratork8sglue:
  basePodTemplate:
    env:
      - name: CLEARML_CACHE_DIR
        value: "/root/.clearml/cache"
    volumes:
      - name: clearml-cache
        hostPath:
          path: /root/clearml-cache
          type: DirectoryOrCreate
    volumeMounts:
      - name: clearml-cache
        mountPath: /root/.clearml/cache
```
