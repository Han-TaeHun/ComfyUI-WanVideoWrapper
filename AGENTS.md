# Codex Working Notes

Before working in this repository, read `docs/CODEX_CONTEXT.md` for the compact project map.

This repository is a ComfyUI custom node package for WanVideo and related models. The root
`__init__.py` imports required and optional node modules, then merges each module's
`NODE_CLASS_MAPPINGS` and `NODE_DISPLAY_NAME_MAPPINGS` into the package-level mappings used by
ComfyUI.

Work carefully around existing local changes. Do not revert user edits or unrelated workspace
state. This package lives inside a larger ComfyUI checkout, so avoid changing parent ComfyUI
folders, model placeholders, model files, caches, or generated assets unless the task explicitly
requires it.

For code changes, prefer the existing node patterns: each ComfyUI node class declares
`INPUT_TYPES`, `RETURN_TYPES`, `FUNCTION`, and `CATEGORY`, then is registered in the module-level
mapping dictionaries.
