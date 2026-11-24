# ComfyUI Tag Prompt Engineer

An agent skill for building and reviewing Danbooru-style anime prompts for ComfyUI. It turns an image brief into positive and negative prompts with grouped labels for review and clean text for ComfyUI nodes.

## Quick start

1. Add this repository as a skill in your agent's skill manager, or point your agent to [SKILL.md](SKILL.md).
2. Identify the model or exact checkpoint and make your local tag CSVs available through the active ComfyUI environment profile.
3. Ask the agent for a prompt pair, a review of an existing pair, or an actual image generation.

```text
Use $comfyui-tag-prompt-engineer to draft a positive and negative prompt for
an anime café scene with WAI Illustrious SDXL. Give me the grouped review
version and clean ComfyUI-node version of each.
```

Prompt drafting uses your local tag library and the [bundled prompt rules](references/tags_knowledge.md). For live image generation, also configure ComfyUI and the companion `comfyui-api-workflow-runner` and `comfyui-final-output` skills. Artist-tag research has its own [verification workflow](references/artist-research-workflow.md).

For changes to the skill, update the relevant rule or reference and check the effect on both grouped and clean prompt output.
