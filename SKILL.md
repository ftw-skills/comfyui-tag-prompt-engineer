---
name: comfyui-tag-prompt-engineer
description: Build, review, and use ComfyUI Danbooru-style anime prompts from the user's local tag databases and tags_knowledge rules. Use instead of comfyui-prompt-engineer when the user wants to generate an image, run a ComfyUI generation, or create/review/correct/format ComfyUI prompts, especially for WAI-illustrious-SDXL, RIN Flanime Illustrious, tag-based anime checkpoints, positive/negative prompt pairs, kaomoji/emoticon tags, prompt weights, grouped [XX词] formatting, local Danbooru tag selection, or handoff to comfyui-api-workflow-runner and comfyui-final-output.
---

# ComfyUI Tag Prompt Engineer

Use this skill for ComfyUI anime/tag prompts. Prefer it over any older prompt-engineering skill for tag-based ComfyUI work.

## Core Rule

Always follow this skill's local prompt rules file as the source of truth for prompt structure, tag formatting, conflict checks, expression fixed-pair rules, model quality fragments, and negative prompt policy:

```text
references/tags_knowledge.md
```

Load `references/tags_knowledge.md` whenever a user asks you to generate, refine, debug, or review ComfyUI prompts.

This skill is the only prompt construction, correction, and review entry point for WAI/RIN ComfyUI prompt pairs. `comfyui-api-workflow-runner` may gate prompts before submission, but failed prompt gates must come back to this skill for correction instead of being rewritten inside the runner.

## Skill Collaboration

This skill is the prompt and generation-intent entry point. When the user asks to generate an image, do not stop after drafting prompts. Use this sequence:

```text
comfyui-tag-prompt-engineer
-> comfyui-api-workflow-runner
-> comfyui-final-output
```

Responsibilities:

- `comfyui-tag-prompt-engineer`: understand the image request, select local tags, build positive/negative prompts, run prompt checks, and produce both grouped/labeled prompts and clean ComfyUI-node prompt text.
- `comfyui-api-workflow-runner`: select the saved workflow, replace only prompt/negative prompt runtime values or placeholders with clean API prompt text, submit to ComfyUI `/prompt`, preserve grouped/labeled prompt strings in the handoff, and return a `prompt_id`.
- `comfyui-final-output`: inspect `/history/{prompt_id}`, resolve the actual final saved/preview image, and return the absolute path plus embedded image.

Generation workflow:

1. Build grouped positive prompt and grouped negative prompt using this skill. These grouped versions must preserve the `[XX词]` labels for PNG metadata, JSON sync copies, review, and viewer display.
2. Produce API/ComfyUI-node versions by removing only `[XX词]` labels while preserving commas, spaces, and line breaks.
3. Hand off all four prompt strings together: `prompt_labeled`, `negative_prompt_labeled`, clean `positive_prompt`, and clean `negative_prompt`.
4. Invoke or follow `comfyui-api-workflow-runner` to run the selected saved workflow with only the clean API prompt strings. Do not edit saved workflow defaults for one generation.
5. After the runner returns `prompt_id`, invoke or follow `comfyui-final-output` to locate the final image.
6. Return the final image path and embed the image in the Codex app.

If the user only asks for prompt text, do not run ComfyUI. If the user asks to generate, make an image, run one, call/run workflow, or otherwise asks for an image result, continue through the runner and final-output steps.

## Local Tag Sources

Choose tags from the user's local data before inventing tags:

```text
G:\Tools\ComfyUI\user\default\prompt-assistant\tags\*.csv
```

This is the akkki environment's Prompt Assistant library. Search every CSV in this directory so newly added user tag libraries are included automatically. The default `默认标签.csv` stores the Chinese display name in `标签名`, the copyable prompt text in `标签值`, and its hierarchy in `一级分类` through `四级分类`. Use `标签值` in prompts.

Use the existing tag finder script first when searching tags:

```powershell
python C:\Users\lenovo\.codex\skills\comfyui-tag-finder\scripts\find_tags.py --root G:\Tools\ComfyUI --query "maid" --query "holding sword"
```

Tag values may use underscores. Output prompt tags with spaces, except keep underscores inside kaomoji/emoticons and proper work/character/special names when the name itself requires them.

## User Prompt Examples

Optional mutable user examples live at:

```text
G:\Tools\ComfyUI\my\tags_example.md
```

Read this file when the user asks to reference examples, reuse a common writing style, or generate a ComfyUI tag prompt similar to the user's examples.

Use `tags_example.md` as style and structure reference only, not as a mandatory rule source. `references/tags_knowledge.md` remains the highest-priority source for prompt structure, negative prompts, quality fragments, and conflict checks. Local tag databases remain the source for tag lookup and validation.

Do not copy the examples into this skill. Read the current file content whenever examples are needed so user edits take effect without updating the skill.

## Output Format

When presenting prompts to the user, always use grouped lines:

```text
[主体词] 1girl, solo,
[身份词] maid,
[外观词] long hair, white hair,
[服装/道具词] maid headdress, maid apron, black dress, white thighhighs, sword,
[姿势词] standing, fighting stance,
[动作词] holding sword,
[表情词] (>_<:1.2), closed eyes, d:, crying,
[背景词] indoors, castle,
[镜头词] full body, dynamic angle,
[光线词] dramatic lighting, rim light,
[质量词] masterpiece, best quality, ultra-detailed,
```

When giving an API/ComfyUI-node prompt version, remove only the `[XX词]` labels and preserve commas, spaces, and line breaks.

## Mandatory Prompt Review

Before finalizing any prompt, run these checks from `references/tags_knowledge.md`:

1. Group format is correct.
2. Tags are selected from local databases where possible and converted to space style.
3. Subject is single-person only: `1girl, solo,` or `1boy, solo,` unless the user explicitly changes the rule.
4. Repeated or redundant tags are removed.
5. Expression fixed pairs are completed: for example `>_<` requires `closed eyes`; `@_@`, `+_+`, and `<|>_<|>` require `open eyes`.
6. Expression-eye conflicts are removed: for example `>_<, closed eyes` cannot coexist with `open eyes`, eye-color tags such as `red eyes`, or `detailed eyes`.
7. Pose/action/camera/background conflicts are removed.
8. `cowboy shot` adds `cowboy,` to `[补充负面提示词]`.
9. Negative prompts must contain only `[模型负面提示词]` and `[补充负面提示词]`. The model part comes from the fixed model table. The supplemental part is empty by default and only contains mapped terms required by the positive tags currently being used.
10. Weights use ComfyUI syntax, e.g. `(>_<:1.2)`, `(:d:1.2)`, `(d::1.2)`.

When handing off to `comfyui-api-workflow-runner`, include prompt gate metadata:

```text
prompt_source = comfyui-tag-prompt-engineer
tags_knowledge_path = C:\Users\lenovo\.agents\skills\comfyui-tag-prompt-engineer\references\tags_knowledge.md
prompt_review_passed = true
model_family = wai | rin
prompt_labeled = ...
negative_prompt_labeled = ...
positive_prompt = ...
negative_prompt = ...
```

If the prompt was corrected after a runner gate failure, include a short `prompt_review_notes` entry naming the failed check and the fix, for example `split sunlit classroom into classroom + sunlight` or `replaced generic SDXL negative with WAI fixed negative`.

## Negative Prompt Policy

Negative prompts have exactly two groups:

```text
[模型负面提示词] ...
[补充负面提示词] ...
```

Separate forbidden tags from negative prompts.

- Forbidden tags are tags you should not choose, such as `2girls, 2boys, multiple girls, multiple boys, group, crowd,` under the single-person rule.
- `[模型负面提示词]` is the fixed negative prompt fragment for the selected model.
- `[补充负面提示词]` is empty by default. Add terms only when an active positive prompt tag can cause semantic misunderstanding.

Example:

```text
[镜头词] cowboy shot,
[补充负面提示词] cowboy,
```

Known supplemental negative mapping:

| Positive prompt tag | When to add supplemental negative | Supplemental negative prompt | Notes |
| --- | --- | --- | --- |
| `cowboy shot` | Use this camera framing, but avoid cowboy hats, boots, western clothing, or cowboy-themed elements | `cowboy,` | Add the minimal correction term first. Expand only if repeated generations prove it insufficient. |

During generation, self-improve this mapping: if an active positive tag repeatedly causes an unwanted semantic element in the generated image, add the smallest effective mapping to `references/tags_knowledge.md` and use it in later prompts.

## Model Quality Fragments

Use the user's fixed workflow fragments when known.

WAI-illustrious-SDXL positive quality, usually at the end:

```text
[质量词] masterpiece, best quality, amazing quality,
```

WAI-illustrious-SDXL negative:

```text
[模型负面提示词] bad quality, worst quality, worst detail, sketch, censor,
[补充负面提示词] 
```

RIN Flanime Illustrious positive quality, usually at the start:

```text
[质量词] masterpiece, best quality,
```

RIN Flanime Illustrious negative:

```text
[模型负面提示词] worst quality, bad quality, username, signature, long neck, twitter username, text, watermark, logo,
[补充负面提示词] 
```
