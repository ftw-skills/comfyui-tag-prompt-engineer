---
name: comfyui-tag-prompt-engineer
description: Build, review, and use ComfyUI Danbooru-style anime prompts from the user's local tag databases and tags_knowledge rules. Use instead of comfyui-prompt-engineer when the user wants to generate an image, run a ComfyUI generation, create/review/correct/format ComfyUI prompts, or research content-matched Anima artist tags with verified accounts and work links, especially for WAI-illustrious-SDXL, RIN Flanime Illustrious, tag-based anime checkpoints, positive/negative prompt pairs, grouped [XX词] formatting, local Danbooru tag selection, or handoff to comfyui-api-workflow-runner and comfyui-final-output.
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

## Model Type Gate

Before creating, refining, reviewing, or generating from a prompt, determine the model type because prompt ordering, quality fragments, artist syntax, and fixed negative prompts are model-dependent.

Treat the model as known when it is explicitly identified by the user, an attached model-specific document, the current conversation, or the selected workflow/checkpoint. Do not ask again when that evidence is unambiguous.

If the model type cannot be determined:

1. Ask the user which model or exact checkpoint they are using, with examples such as `Anima-2.9B`, `WAI Illustrious SDXL`, `RIN Flanime Illustrious`, or another named checkpoint.
2. Do not assume a generic SDXL model and do not choose model-specific quality or negative fragments on the user's behalf.
3. Do not output the four final prompt blocks or run a generation until the model is confirmed.

For a model not covered by the fixed model table, use the user's model-specific documentation or workflow fragments when available. If those rules are unavailable, say which model-specific information is missing and ask the user for the checkpoint or its recommended positive and negative fragments.

## Local Tag Sources

Choose tags from the user's local data before inventing tags:

```text
the CSV files under the active environment profile's `paths.tag_csv_dir`
```

This is the akkki environment's Prompt Assistant library. Search every CSV in this directory so newly added user tag libraries are included automatically. The default `默认标签.csv` stores the Chinese display name in `标签名`, the copyable prompt text in `标签值`, and its hierarchy in `一级分类` through `四级分类`. Use `标签值` in prompts.

Use the existing tag finder script first when searching tags:

```powershell
python <comfyui-tag-finder>\scripts\find_tags.py --config C:\Users\lenovo\.config\comfyui-codex\environment.json --query "maid" --query "holding sword"
```

Tag values may use underscores. Output prompt tags with spaces, except keep underscores inside kaomoji/emoticons and proper work/character/special names when the name itself requires them.

## Artist Tag Fallback

For content-matched artist recommendations, artist identity resolution, Danbooru-to-Anima mapping, platform-account verification, or representative/recent work links, read `references/artist-research-workflow.md`. Follow its independent evidence statuses and stopping rules. Use `references/artist-research-example-rucarachi.yaml` only as a structural example; re-check volatile links, recent works, dates, and configured model samples at response time.

### Optional Anima-2.9B Artist Suggestions

For Anima-2.9B, artist tags are optional rather than a gate. If the user has not specified an artist, continue building or generating the no-artist version. Do not silently inject a default artist and do not pause the task only because the artist is unspecified.

When presenting prompt text, append a concise notice after the four required prompt blocks that covers all of the following:

- State that the current artist is unspecified.
- Explain that artist tags can influence linework, coloring, composition, detail density, and the overall visual aesthetic, but are not required.
- Suggest comparing the no-artist version with an artist-tag version using the same seed and generation settings when practical.
- Say that the user may select one or multiple artists. Multiple artists can blend stylistic tendencies, while too many may weaken consistency or create conflicting cues.
- Offer these three default candidates, collected from the official Anima-2.9B sample prompts at configured `paths.artist_prompt_samples`: `@rucarachi`, `@far side`, and `@luozhou pile`.

Use wording equivalent to:

```text
当前未指定画师。画师标签可以影响画面的线条、上色、构图、细节密度和整体审美，但并非必需。你可以先使用无画师版本生成，再加入画师标签并使用相同种子和参数进行对比。可尝试 @rucarachi、@far side、@luozhou pile；你可以选择一个或多个画师，多个画师会混合风格倾向，但数量过多可能降低风格一致性。
```

These are suggestions only. Add none of them to `[画师/风格词]` unless the user selects them. The official sample JSON is the verification source for these three defaults. When the user selects one or more candidates, preserve the selected order and use Anima's `@artist name` syntax. Do not add weights unless the user requests weights or a model-specific prompt requires them.

### Artist Source and Verification Boundary

Artist `@tags` are not currently stored in the CSV files under the active environment profile's `paths.tag_csv_dir`. Those CSV files are the source for ordinary prompt tags, not the current source of truth for Anima artist tags.

`comfyui-tag-finder` is a separate lookup skill for ordinary tags and Chinese/English tag translations in the Prompt Assistant CSV library. Use it for subject, appearance, clothing, pose, action, expression, scene, camera, lighting, and similar tags. Do not use a no-result from `comfyui-tag-finder` to declare an artist `@tag` invalid.

Apply this artist verification order:

1. For `@rucarachi`, `@far side`, and `@luozhou pile`, treat configured `paths.artist_prompt_samples` as the verifying source.
2. For another artist requested by the user, check an available model-specific artist source, official sample metadata, or user-provided documentation before claiming that the exact `@tag` is verified.
3. If no artist-specific source confirms the exact spelling, do not invent, normalize, replace, or claim verification. Tell the user that the artist tag is not locally verified and offer either using their exact text verbatim or choosing one or more of the three verified defaults.
4. If the user chooses verbatim use, preserve the exact artist text they supplied and proceed. Artist verification is optional for prompt construction; accurate disclosure is required.

The absence of an artist tag from the ordinary local tag CSV does not block prompt output or generation. If the user does not select an artist, continue with the no-artist version and show the optional Anima artist notice after the four prompt blocks.

## User Prompt Examples

Optional mutable user examples live at:

```text
the active environment profile's `paths.tag_examples`
```

Read this file when the user asks to reference examples, reuse a common writing style, or generate a ComfyUI tag prompt similar to the user's examples.

Use `tags_example.md` as style and structure reference only, not as a mandatory rule source. `references/tags_knowledge.md` remains the highest-priority source for prompt structure, negative prompts, quality fragments, and conflict checks. Local tag databases remain the source for tag lookup and validation.

Do not copy the examples into this skill. Read the current file content whenever examples are needed so user edits take effect without updating the skill.

## Output Format

Whenever prompts are presented to the user, output all four blocks below in this exact order. Do not omit the clean copyable blocks, even when the user only asks for prompt refinement or review.

1. `带分组的正面提示词`: the positive prompt with `[XX词]` labels.
2. `可直接复制的正面提示词`: the same positive prompt with only the `[XX词]` labels removed; preserve all prompt content, commas, spaces, and line breaks.
3. `带分组的负面提示词`: the negative prompt with exactly `[模型负面提示词]` and `[补充负面提示词]` labels.
4. `可直接复制的负面提示词`: the same negative prompt with only those two labels removed; preserve all prompt content, commas, spaces, and line breaks.

Use four clearly titled `text` code blocks:

### 带分组的正面提示词

```text
[主体词] 1girl, solo,
[身份词] maid,
[外观词] long hair, white hair,
[服装/道具词] maid headdress, maid apron, black dress, white thighhighs, sword,
[姿势词] standing, fighting stance,
[动作词] holding sword,
[表情词] (>_<:1.2), closed eyes, d:, crying,
[背景词] indoors, castle,
[镜头词] full body, from side,
[光线词] dramatic lighting, rim light,
[质量词] masterpiece, best quality, ultra-detailed,
```

### 可直接复制的正面提示词

```text
1girl, solo,
maid,
long hair, white hair,
maid headdress, maid apron, black dress, white thighhighs, sword,
standing, fighting stance,
holding sword,
(>_<:1.2), closed eyes, d:, crying,
indoors, castle,
full body, from side,
dramatic lighting, rim light,
masterpiece, best quality, ultra-detailed,
```

### 带分组的负面提示词

```text
[模型负面提示词] bad quality, worst quality, worst detail, sketch, censor,
[补充负面提示词]
```

### 可直接复制的负面提示词

```text
bad quality, worst quality, worst detail, sketch, censor,
```

The two positive blocks must contain identical prompt content apart from the group labels. The two negative blocks must also contain identical prompt content apart from their group labels. Explanations may follow the four prompt blocks, but must not interrupt their required order.

## Mandatory Prompt Review

Before finalizing any prompt, run these checks from `references/tags_knowledge.md`:

1. Model type is confirmed from the user, context, documentation, or selected workflow/checkpoint.
2. Group format is correct.
3. Tags are selected from local databases where possible and converted to space style.
4. Subject is single-person only: `1girl, solo,` or `1boy, solo,` unless the user explicitly changes the rule.
5. Repeated or redundant tags are removed.
6. Expression fixed pairs are completed: for example `>_<` requires `closed eyes`; `@_@`, `+_+`, and `<|>_<|>` require `open eyes`.
7. Expression-eye conflicts are removed: for example `>_<, closed eyes` cannot coexist with `open eyes`, eye-color tags such as `red eyes`, or `detailed eyes`.
8. Pose/action/camera/background conflicts are removed.
9. `cowboy shot` adds `cowboy,` to `[补充负面提示词]`.
10. Negative prompts must contain only `[模型负面提示词]` and `[补充负面提示词]`. The model part comes from the confirmed model's fixed table or supplied model-specific rules. The supplemental part is empty by default and only contains mapped terms required by the positive tags currently being used.
11. Weights use ComfyUI syntax, e.g. `(>_<:1.2)`, `(:d:1.2)`, `(d::1.2)`.

When handing off to `comfyui-api-workflow-runner`, include prompt gate metadata:

```text
prompt_source = comfyui-tag-prompt-engineer
tags_knowledge_path = <this skill>/references/tags_knowledge.md
prompt_review_passed = true
model_family = wai | rin | anima | other
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
