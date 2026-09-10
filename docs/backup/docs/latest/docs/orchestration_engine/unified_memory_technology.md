# Inference Optimization

> Klique's Unified Memory Technology, which offloads inactive models to CPU RAM or disk to serve more models per GPU.

Klique's **Unified Memory Technology** lets a single GPU serve more models than its VRAM would normally hold. It treats
CPU RAM (and, optionally, disk) as an extension of GPU VRAM: models that are not actively serving requests are offloaded
from GPU and loaded back in on demand. In the model deployment apps this capability is configured as
**Automatic CPU Offloading**, and it is sometimes referred to as CPU offloading.

## Background

Serving each model on its own GPU makes compute cost scale with the number of models. Packing several models onto one GPU
is limited by the available VRAM, and launching a new GPU instance on demand typically takes minutes, which is impractical for
interactive inference. In practice, deployed models are idle much of the time but still hold VRAM that could serve
other workloads.

Unified Memory keeps only the active models in VRAM and caches the rest in CPU RAM, so idle models release GPU memory
without being unloaded entirely.

## How it works

Active models live in GPU VRAM, where they execute. Models that are not actively serving requests are offloaded to system
RAM, which is typically 10 to 100 times larger than GPU memory, or to disk when configured. When a request arrives for an offloaded
model, Klique loads it from CPU RAM back into VRAM and swaps out an inactive model to make room.

Swapping is dynamic: models move in and out of VRAM based on real-time usage, with no need to predefine which models stay
resident. This differs from static approaches such as vLLM or llama.cpp, where the memory layout is fixed up front.

![Klique Unified Memory Technology](../img/cpu_gpu_swap_diagram.png#light-mode-only)
![Klique Unified Memory Technology](../img/cpu_gpu_swap_diagram_dark.png#dark-mode-only)

You can configure the deployment engine with more CUDA memory than the GPU physically has. The surplus is backed by CPU RAM,
virtually increasing the VRAM available to your models. For example, a 24 GB GPU configured with 10 GiB of CPU offload
appears as a 34 GB device. Because models move across the CPU-GPU boundary during operation, a fast CPU-GPU interconnect
is recommended.

### Offload options

Two tiers of overflow memory are available:

- **CPU RAM offloading** - Enabled via `Automatic CPU Offloading`. Set a `Max CUDA Memory` (or `CPU Offload GiB`) to a value larger
  than the GPU's physical VRAM, and the surplus is offloaded to CPU RAM. This is the recommended option for sharing a GPU across
  multiple models.
- **Disk swapping** - Offloads inactive models to disk instead of RAM (via `Disk Swapping` / `Swap Space`), used when
  `Automatic CPU Offloading` is disabled. It is a fallback for when system RAM is also constrained.

These options are configured per deployment in the model serving applications. See the
[One-click Inference Service](../deploying_models.md) for setup details.

## Supported applications

Unified Memory is supported in the following model deployment applications:

- [vLLM](../webapp/applications/apps_model_deployment.md)
- [Llama.cpp](../webapp/applications/apps_llama_deployment.md)
- [SGLang](../webapp/applications/apps_sglang.md)

## Benefits

- **More models per GPU** - Serve multiple models from a single GPU instead of one GPU per model.
- **Higher GPU utilization** - Idle models release VRAM for active workloads.
- **Lower inference cost** - Consolidating models onto fewer GPUs reduces compute spend.
- **Faster model switching** - Cached models load from RAM faster than a cold start.
- **No manual tuning** - Swapping is automatic and driven by real-time demand.

:::note
Automatic CPU Offloading currently runs on `amd64` machines. The `Max CUDA Memory` value may exceed the actual hardware
memory; the surplus is offloaded to CPU memory.
:::
