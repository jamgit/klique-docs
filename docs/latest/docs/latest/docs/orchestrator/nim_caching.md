# NIM Cache

> Configuring the NIM_CACHE_PATH cache directory and Kubernetes volume mounts for NVIDIA NIM containers used by the Klique Orchestrator.

NVIDIA NIM containers use a local cache directory for downloaded models. The cache location is controlled by the 
`NIM_CACHE_PATH` environment variable.

When deploying the Klique Orchestrator on Kubernetes, the volume information can be set through the overrides file. For example:

```
orchestratork8sglue:
  basePodTemplate:
    env:
      - name: NIM_CACHE_PATH
        value: /opt/nim/.cache
    volumeMounts:
      - name: nim-cache
        mountPath: /opt/nim/.cache
    volumes:
      - name: nim-cache
        persistentVolumeClaim:
          claimName: nim-cache-pvc
```
