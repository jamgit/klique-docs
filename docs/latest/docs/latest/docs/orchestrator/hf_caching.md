# HuggingFace Cache

> Configuring the HF_HOME cache directory and Kubernetes volume mounts for HuggingFace models used by the Klique Orchestrator.

HuggingFace libraries provide their own cache mechanism for downloaded files. The HuggingFace cache location is controlled 
by the `HF_HOME` environment variable.

When deploying the Klique Orchestrator on Kubernetes, the volume information can be set through the overrides file. For example: 
```yaml
orchestratork8sglue:
  basePodTemplate:
    env:
      - name: HF_HOME
        value: "/root/.cache/huggingface"
    volumes:
      - name: hf-cache
        hostPath:
          path: /root/.clearml/cache/hf
          type: DirectoryOrCreate
    volumeMounts:
      - name: hf-cache
        mountPath: /root/.cache/huggingface
```
