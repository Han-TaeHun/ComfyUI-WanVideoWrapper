# Codex Context for ComfyUI-WanVideoWrapper

## Purpose

This project provides ComfyUI wrapper nodes for WanVideo and related video generation models. It
focuses on model loading, text/image conditioning, sampling, latent encode/decode helpers, and
optional integrations for related model families.

The package is installed as a ComfyUI custom node. Runtime registration happens through
`NODE_CLASS_MAPPINGS` and `NODE_DISPLAY_NAME_MAPPINGS`.

## Entry Structure

- `__init__.py` is the package entrypoint.
- `REQUIRED_MODULES` registers core nodes from `nodes.py`, `nodes_sampler.py`,
  `nodes_model_loading.py`, `nodes_utility.py`, and `cache_methods.nodes_cache`.
- `OPTIONAL_MODULES` registers feature-specific modules such as S2V, FlashVSR, Mocha, ControlNet,
  MultiTalk, ReCamMaster, SkyReels, FantasyTalking, Qwen, Ovi, WanMove, LongCat, and others.
- `register_nodes()` imports each module and merges its node mappings. Required module failures
  raise; optional module failures are logged as warnings.

## Core Files

- `nodes.py`: text encoding, clip vision encoding, image-to-video conditioning, image embed
  composition, context/options nodes, VAE encode/decode wrappers, and latent helpers.
- `nodes_model_loading.py`: transformer/model loading, VAE and tiny VAE loaders, LoRA selection and
  application, VRAM management, block swapping, compile settings, attention mode settings, and
  weight loading.
- `nodes_sampler.py`: WanVideo sampler implementations, scheduler nodes, sampler settings, extra
  sampler args, and shared sampling flow.
- `nodes_utility.py`: resize helpers, VACE start/end frame tools, latent rescale, sigma-to-step
  utility, saved-image passthrough, and embed preview utilities.
- `utils.py`: shared helpers for logging, model paths, duplicate node detection, tensor/module
  utilities, and related support code.

## Main Type Flow

Common ComfyUI types used across nodes:

- `WANVIDEOMODEL`: loaded WanVideo model patcher/model wrapper.
- `WANVIDEOTEXTEMBEDS`: text conditioning produced by WanVideo text encode nodes.
- `WANVIDIMAGE_EMBEDS`: image, video, control, or auxiliary conditioning payloads.
- `LATENT`: ComfyUI latent payloads used for encode/decode and sampling.
- `IMAGE`: ComfyUI image tensors.
- `BLOCKSWAPARGS`: block swap configuration for memory management.
- `VRAM_MANAGEMENTARGS`: alternate VRAM/offload configuration.
- `WANVIDCONTEXT`: context-window settings for long video sampling.

Typical workflow shape:

1. Load model and VAE with nodes from `nodes_model_loading.py`.
2. Build text conditioning with text encode nodes from `nodes.py`.
3. Add image/video/control conditioning through `WANVIDIMAGE_EMBEDS` nodes when needed.
4. Configure sampler, scheduler, context, LoRA, VRAM, block swap, or compile settings.
5. Sample to latents in `nodes_sampler.py`.
6. Decode or encode latents/images through helper nodes in `nodes.py` or `nodes_utility.py`.

## Task Navigation

- Adding a new node: follow an existing node class pattern, then update the module's
  `NODE_CLASS_MAPPINGS` and `NODE_DISPLAY_NAME_MAPPINGS`.
- Changing model loading: start at `WanVideoModelLoader`, `load_weights()`, LoRA selection/application
  code, and nearby VRAM/block swap handling in `nodes_model_loading.py`.
- Changing VAE behavior: start at `WanVideoVAELoader`, `WanVideoTinyVAELoader`, and encode/decode
  nodes.
- Changing sampling behavior: start at `WanVideoSampler`, `WanVideoSamplerv2`,
  `WanVideoScheduler`, `WanVideoSchedulerv2`, and sampler settings classes in `nodes_sampler.py`.
- Changing optional model features: start with the module listed in `OPTIONAL_MODULES` inside
  `__init__.py`, then inspect that feature folder's `nodes.py` or equivalent entry file.
- Changing utility behavior: start in `nodes_utility.py` unless the utility is clearly tied to a
  specific optional model folder.

## Cautions

- Optional module import failures should stay non-fatal unless the feature is intentionally promoted
  to a required dependency.
- VRAM, offload, block swap, compile, dtype, and LoRA changes can significantly affect memory use.
  Check `readme.md` memory notes before changing those paths.
- LoRA weights are attached to modules/buffers in current behavior, so changes can affect VRAM use
  and block swapping.
- This directory is inside a larger ComfyUI workspace. Treat parent-directory deletions, model
  placeholders, model files, caches, and unrelated custom nodes as separate workspace state.
- Keep documentation compact. This file is a fast orientation map, not a full node catalog.
