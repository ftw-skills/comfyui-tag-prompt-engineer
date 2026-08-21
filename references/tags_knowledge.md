# 提示词标签知识库

这份文档用于整理 ComfyUI / Danbooru 系模型常用提示词的结构、写法和组合原则。标签优先来自本地 tag 数据库：

```text
custom_nodes\WeiLin-ComfyUI-prompt-all-in-one\local_complete_tags\danbooru-0-zh.csv
custom_nodes\WeiLin-ComfyUI-prompt-all-in-one\autocomplete\autocomplete.txt
custom_nodes\WeiLin-Comfyui-Tools\user_data\userdatas_zh_CN_tags.db
custom_nodes\WeiLin-Comfyui-Tools\user_data\userdatas_zh_CN_danbooru.db
```

## 固定书写格式

我和你整理提示词时，始终使用这种格式：

```text
[主体词] 1girl, solo,
[身份词] maid,
[外观词] long hair, white hair,
[服装/道具词] maid headdress, maid apron, black dress, white thighhighs, sword,
[姿势词] standing, fighting stance, dynamic pose,
[动作词] holding sword,
[表情词] closed eyes, :|, serious,
[背景词] indoors, castle, night, moonlight,
[光线词] dramatic lighting,
[镜头词] close-up, depth of field,
[质量词] masterpiece, best quality, amazing quality,
```

实际作为 ComfyUI 提示词使用时，删除 `[XX词]` 分组名，只保留英文标签、逗号、空格与换行：

```text
1girl, solo,
maid,
long hair, white hair,
maid headdress, maid apron, black dress, white thighhighs, sword,
standing, fighting stance, dynamic pose,
holding sword,
closed eyes, :|, serious,
indoors, castle, night, moonlight,
dramatic lighting,
close-up, depth of field,
masterpiece, best quality, amazing quality,
```

## 使用原则

- 优先从本地数据库选择 Danbooru 标签，但在文档和实际提示词中把标签下划线改为空格，例如数据库里的 `maid_headdress` 写成 `maid headdress`，`holding_sword` 写成 `holding sword`。
- 只有颜文字和作品名、角色名、专有名词中的下划线保留原样。
- 不主动重新制造提示词。找不到合适标签时，先查本地库，再考虑自然语言短语。
- 同一含义不要重复写。例如 `holding sword` 已经包含 `holding` 和 `sword` 的关系，通常不用再重复 `holding, holding weapon, holding sword,`。
- 禁止多人生成。正面主体只使用单人主体；多人相关标签属于禁用词，不等于默认要写进负面提示词。
- 镜头词放在后半段，通常在背景词之后，避免和主体词重复。
- 表情、姿势、动作、镜头要避免互相冲突，并补齐固定搭配。例如 `>_<` 要搭配 `closed eyes`，不要再配 `open eyes`、`red eyes`、`detailed eyes`。

## 正面提示词结构

推荐顺序：

```text
[主体词] ...
[身份词] ...
[外观词] ...
[服装/道具词] ...
[姿势词] ...
[动作词] ...
[表情词] ...
[背景词] ...
[镜头词] ...
[光线词] ...
[质量词] ...
```

## 主体词

主体词用于确定画面里最核心的人物。禁止多人主体。

| 用途 | 推荐标签 | 说明 |
| --- | --- | --- |
| 单人女性 | `1girl, solo,` | 最常用 |
| 单人男性 | `1boy, solo,` | 男性主体 |
| 单人焦点 | `solo focus,` | 复杂背景或画面中有非主体元素时可用 |
| 无人物 | `no humans, scenery,` | 风景或物品画面 |

默认：

```text
[主体词] 1girl, solo,
```

禁用主体词，不在正面提示词中使用，也不要默认放进负面提示词：

```text
2girls, 2boys, multiple girls, multiple boys, group, crowd,
```

## 身份词

身份词描述人物身份、职业、种族或设定。

| 类型 | 标签示例 |
| --- | --- |
| 职业/身份 | `maid, nurse, teacher, student, idol, knight, witch, nun, samurai, warrior, princess, queen,` |
| 女仆相关 | `maid, housemaid, wa maid, victorian maid, cafe maid,` |
| 幻想设定 | `elf, demon girl, angel, vampire, kemonomimi, cat girl, fox girl,` |

示例：

```text
[身份词] maid,
```

## 外观词

外观词描述人物身体、发型、发色、眼睛、体型等特征。

| 类型 | 标签示例 |
| --- | --- |
| 发色 | `black hair, white hair, blonde hair, pink hair, blue hair, silver hair,` |
| 发型 | `long hair, short hair, twintails, ponytail, braid, bangs, blunt bangs,` |
| 眼睛 | `red eyes, blue eyes, green eyes, yellow eyes, heterochromia,` |
| 体型 | `small breasts, medium breasts, large breasts, petite,` |
| 特征 | `animal ears, horns, wings, tail, halo, pointy ears, slit pupils,` |

示例：

```text
[外观词] long hair, white hair,
```

## 服装与道具词

服装与道具词描述画面中可见的单品、装备和物体。

总体原则：

```text
[服装/道具词] 物体A, 物体A附属, 物体B, 物体B附属,
```

| 类型 | 标签示例 |
| --- | --- |
| 女仆装 | `maid headdress, maid apron, black dress, white apron, mary janes, white thighhighs,` |
| 校服 | `school uniform, serafuku, sailor collar, pleated skirt, loafers,` |
| 战斗装备 | `armor, gauntlets, boots, cape, weapon, sword, katana, spear, shield, gun,` |
| 剑相关 | `sword, holding sword, planted sword, sword on back, sword over shoulder, greatsword, glowing sword,` |
| 配饰 | `hair ornament, ribbon, bow, choker, necklace, earrings, gloves,` |

示例：

```text
[服装/道具词] maid headdress, maid apron, mary janes, black dress, white thighhighs, sword,
```

## 姿势词

姿势词描述人物身体的静态状态，更偏“摆出什么姿态”。

| 类型 | 标签示例 |
| --- | --- |
| 站姿 | `standing, standing on one leg, fighting stance,` |
| 坐姿 | `sitting, sitting on chair, sitting on bed, sitting on stairs, butterfly sitting,` |
| 躺姿 | `lying, on back, on side, on stomach,` |
| 动态姿势 | `dynamic pose, fighting stance,` |
| 手部姿势 | `arms behind back, hands on hips, hand on own chest, crossed arms,` |
| 视线/朝向 | `looking at viewer, looking away, looking back, profile,` |

示例：

```text
[姿势词] standing, fighting stance, looking at viewer,
```

## 动作词

动作词描述人物正在做什么，更偏“发生了什么行为”。

| 类型 | 标签示例 |
| --- | --- |
| 持有 | `holding sword, holding staff, holding umbrella, holding book,` |
| 战斗 | `attacking, slashing, aiming, casting spell, running, jumping,` |
| 日常动作 | `walking, reading, drinking, eating, sleeping, stretching,` |
| 情绪动作 | `crying, laughing, waving, reaching out, covering mouth,` |
| 动态 | `hair flowing, wind, cape fluttering, floating,` |

示例：

```text
[动作词] holding sword,
```

## 表情词

表情词建议拆成三部分：

```text
[表情词] 眼文字, 嘴文字, 情绪描述,
```

示例：

```text
[表情词] (>_<:1.2), closed eyes, d:, crying,
```

### 眼文字

| 眼文字 | 名称 | 建议权重 | 固定组合 | 禁止组合 |
| --- | --- | --- | --- | --- |
| `>_<` | 不等号眼 | `1.1~1.4` | `>_<, closed eyes` | `open eyes`、眼部颜色词 |
| `>_o` | Wink 眼 | `1.0~1.3` | `>_o` | `open eyes`、`closed eyes` |
| `@_@` | 圈圈眼 | `1.1~1.4` | `@_@, open eyes` | `closed eyes` |
| `u_u` | 悠悠眼 | `1.3+` | `u_u, closed eyes` | `open eyes`、眼部颜色词 |
| `^_^` | 微笑眼 | `1.0~1.3` | `^_^, closed eyes` | `open eyes`、眼部颜色词 |
| `=_=` | 等号眼 | `1.1~1.4` | `=_=, closed eyes` | `open eyes`、眼部颜色词 |
| `+_+` | 星星眼 | `1.1~1.4` | `+_+, open eyes` | `closed eyes` |
| `<|>_<|>` | 猫眼颜文字 | `1.1~1.3` | `<|>_<|>, open eyes` | `closed eyes` |

猫眼说明：

- 使用反引号引用标签时，直接写原始标签 `<|>_<|>`，不额外添加反斜杠转义。
- 实际作为提示词标签时，也使用同一个原始标签：

```text
[表情词] <|>_<|>, :3, happy,
```

- 如果不想使用颜文字，也可以用数据库里的语义标签表达猫眼特征：

```text
[外观词] slit pupils,
[表情词] :3, happy,
```

### 嘴文字

| 嘴文字 | 含义 | 常见组合 |
| --- | --- | --- |
| `:d` / `xd` | 大笑、开心 | `happy, laughing, excited` |
| `d:` | 哭、崩溃、惊慌 | `crying, scared, distressed` |
| `:o` | 惊讶、尖叫 | `surprised, shocked, scared` |
| `:p` | 调皮、吐舌 | `playful, happy` |
| `:3` | 可爱、猫系、得意 | `happy, smug, cute` |
| `:<` / `:c` | 难过、不安 | `sad, nervous, angry` |
| `:|` | 平静、无表情、严肃 | `expressionless, calm, serious` |
| `c:` | 温和微笑 | `smile, calm` |

### 表情组合例子

下面这张表是可直接使用的加权组合。奇数行写表情名称，偶数行写对应提示词组合；主要眼文字按上方“建议权重”体现权重，嘴文字通常不需要加权。

猫眼在引用格式中直接写成 `(<|>_<|>:1.2)`；实际作为提示词时也使用 `(<|>_<|>:1.2)`。

| 表情组 1 | 表情组 2 | 表情组 3 |
| --- | --- | --- |
| 大笑 | 大哭 | 尖叫 |
| `(>_<:1.2), closed eyes, xd, happy` | `(>_<:1.2), closed eyes, d:, crying` | `(>_<:1.2), closed eyes, :o, unhappy` |
| 调皮 | 高兴 | 顽皮 |
| `(>_o:1.2), :p, happy` | `(>_o:1.2), :d, happy` | `(>_o:1.2), :3, happy` |
| 混乱 | 哈气 | 诧异 |
| `(@_@:1.2), open eyes, :o, confused` | `(@_@:1.2), open eyes, :3, angry` | `(@_@:1.2), open eyes, :3, confused` |
| 沉思 | 沉稳 | 休憩 |
| `(u_u:1.3), closed eyes, :|, expressionless` | `(u_u:1.3), closed eyes, c:, smile` | `(u_u:1.3), closed eyes, :o, sleep` |
| 严肃 |  |  |
| `closed eyes, :|, serious` |  |  |
| 开怀大笑 | 开朗活泼 | 强压怒火 |
| `(^_^:1.2), closed eyes, :d, happy` | `(^_^:1.2), closed eyes, :p, happy` | `(^_^:1.2), closed eyes, :d, angry` |
| 轻松惬意 | 坐立难安 | 疑惑不解 |
| `(=_=:1.2), closed eyes, :3, happy` | `(=_=:1.2), closed eyes, :<, nervous` | `(=_=:1.2), closed eyes, :<, confused` |
| 迫不及待 | 开朗活泼 | 游刃有余 |
| `(+_+:1.2), open eyes, :d, excited` | `(+_+:1.2), open eyes, :3, happy` | `(+_+:1.2), open eyes, :d, ;d, happy` |
| 猫里猫气 | 坐立难安 | 休憩 |
| `(<|>_<|>:1.2), open eyes, :3, happy` | `(<|>_<|>:1.2), open eyes, :o, nervous` | `(<|>_<|>:1.2), open eyes, :c, angry` |

## 背景词

背景词描述人物所在环境。背景越明确，画面越稳定。

| 类型 | 标签示例 |
| --- | --- |
| 室内 | `indoors, bedroom, classroom, library, cafe, church, castle, kitchen,` |
| 室外 | `outdoors, street, city, forest, garden, beach, mountain, field,` |
| 时间 | `day, night, sunset, sunrise, dusk,` |
| 天气 | `rain, snow, fog, cloudy sky, starry sky,` |
| 氛围 | `ruins, battlefield, fantasy, cyberpunk, traditional japanese, gothic,` |

示例：

```text
[背景词] indoors, castle, night,
```

## 镜头词

镜头词决定构图、视角和人物占画面比例。镜头词不要放进主体词，也不要和主体词重复。

| 类型 | 标签示例 | 说明 |
| --- | --- | --- |
| 景别 | `portrait, upper body, cowboy shot, full body, close-up,` | 控制人物截取范围 |
| 视角 | `straight-on, from side, from above, from below, dutch angle,` | 控制观察角度 |
| 构图 | `depth of field,` | 控制画面组织 |

审查结论：

- 本节标签已按本地库核对；数据库原始下划线标签在文档和实际提示词中写为空格。
- `from front` 没有主库精确匹配，使用主库存在的 `straight-on`。
- `dynamic angle` 没有主库精确匹配，不再作为镜头词；`dynamic pose` 是主库存在的姿势词，放在 `[姿势词]`。

`cowboy shot` 使用规则：

```text
[镜头词] cowboy shot,
[补充负面提示词] cowboy,
```

原因：`cowboy shot` 是七分身镜头，但 `cowboy` 也是本地库里的独立标签，可能引入牛仔帽、牛仔靴、西部风格等误解。

示例：

```text
[镜头词] full body, depth of field,
```

## 光线词

光线词用于控制照明方式、光源方向、画面光效和明暗氛围。它是可选分组，默认模板不主动加入；只有用户明确指定光照，或场景强依赖光照氛围时才使用。

光线词与背景词、镜头词保持分开：

- 背景词描述环境、时间、天气和场景光源，例如 `sunlight, moonlight, neon lights,` 在描述“阳光房间、月夜、霓虹街道”时优先放进 `[背景词]`。
- 镜头词描述取景、视角和景别，例如 `full body, from below, depth of field,`。
- 光线词描述照明效果，例如 `soft lighting, backlighting, rim light, lens flare,`。

| 类型 | 常用标签 | 来源说明 |
| --- | --- | --- |
| 照明方式 | `soft lighting, natural lighting, bright lighting, dramatic lighting, warm lighting, cold lighting,` | 用户经验光线词，已加入用户标签库 |
| 方向/轮廓光 | `side lighting, backlighting, rim light, strong rim light,` | `backlighting` 主库存在；其余来自用户标签库 |
| 电影/体积光 | `cinematic lighting, volumetric lighting,` | 用户标签库存在，实际提示词统一写小写空格 |
| 场景光源 | `sunlight, moonlight, neon lights,` | 主库存在；描述场景时优先放入 `[背景词]` |
| 画面光效 | `glowing, glowing neon lights, outer glow, lens flare,` | 发光、霓虹和镜头光晕效果 |
| 明暗状态 | `shadow, intense shadows,` | 阴影或强阴影氛围 |

示例：

```text
[光线词] backlighting, rim light, lens flare,
```

## 质量词

质量词与使用的模型有关，一般较为固定。

| 模型 | 正面提示词 | 推荐位置 |
| --- | --- | --- |
| WAI-illustrious-SDXL | `masterpiece, best quality, amazing quality,` | 末尾 |
| RIN Flanime Illustrious - High-Quality Flat Anime | `masterpiece,` | 开头 |
| 通用 SDXL 动漫模型 | `masterpiece, best quality, high quality, ultra-detailed,` | 开头或末尾 |

示例：

```text
[质量词] masterpiece, best quality, high quality, ultra-detailed,
```

## 权重写法

ComfyUI 核心 CLIP 文本解析支持括号权重。根据本地源码 `comfy\sd1_clip.py` 的解析逻辑：

- `(tag)` 会自动乘以 `1.1`。
- `(tag:1.2)` 会把 `tag` 的权重设为 `1.2`。
- 嵌套括号会继续增强，例如 `((tag))` 约等于连续乘以 `1.1`。
- 需要字面量括号时，使用反斜杠转义：`\(`、`\)`。

推荐写法：

```text
[表情词] (>_<:1.2), closed eyes, d:, crying,
[表情词] (:d:1.2), happy,
[外观词] (detailed eyes:1.3), white hair,
[光线词] (backlighting:1.2), rim light,
```

颜文字或嘴文字里包含冒号时，也可以直接使用同一套权重写法：

```text
[表情词] (:d:1.2), happy,
[表情词] (d::1.2), crying,
[表情词] (:3:1.2), happy,
```

解释：ComfyUI 会使用括号内最后一个冒号作为权重分隔。因此 `(:d:1.2)` 会把 `:d` 作为标签，把 `1.2` 作为权重。

不推荐滥用：

```text
[质量词] (((masterpiece))), (((best quality))),
```

### tag 包含冒号时是否需要转义

结论：ComfyUI 核心权重解析只把括号内最后一个冒号后的内容当作“可能的权重数值”。如果最后一个冒号后面不是数字，通常不会被当作权重。

```text
[身份词] series:character,
```

如果放进括号并且最后一段是数字，会被误认为权重：

```text
[身份词] (tag:1.2),
```

上面会被解析为 `tag` 权重 `1.2`，不是字面量 `tag:1.2`。

因此：

- 普通 Danbooru 标签一般不需要转义冒号；文档和实际提示词里按空格形式书写。
- 需要加权时，使用 `(标签:权重)`。
- 颜文字加权可以直接写成 `(:d:1.2)`、`(d::1.2)`、`(:3:1.2)`。
- 如果某个字面标签本身以 `:数字` 结尾，不要放进括号权重语法里。
- ComfyUI 核心解析只明确支持转义括号 `\(`、`\)`；不要假设 `\:` 一定能转义冒号。

## 正面提示词模板

```text
[主体词] 1girl, solo,
[身份词] maid,
[外观词] long hair, white hair,
[服装/道具词] maid headdress, maid apron, black dress, white thighhighs, sword,
[姿势词] standing, fighting stance, dynamic pose,
[动作词] holding sword,
[表情词] closed eyes, :|, serious,
[背景词] indoors, castle, night, moonlight,
[光线词] dramatic lighting,
[镜头词] close-up, depth of field,
[质量词] masterpiece, best quality, amazing quality,
```

实际投喂 ComfyUI 时：

```text
1girl, solo,
maid,
long hair, white hair,
maid headdress, maid apron, black dress, white thighhighs, sword,
standing, fighting stance, dynamic pose,
holding sword,
closed eyes, :|, serious,
indoors, castle, night, moonlight,
dramatic lighting,
close-up, depth of field,
masterpiece, best quality, amazing quality,
```

## 生成前检查流程

每次整理或生成提示词时，按下面流程检查一遍，再给出最终提示词。

1. 格式检查：确认使用 `[XX词] tag, tag,` 分组换行格式；实际投喂 ComfyUI 时删除 `[XX词]` 分组名。
2. 标签来源检查：优先从本地数据库选词；数据库下划线标签在文档和实际提示词中改为空格，颜文字和作品名/专名除外。
3. 主体检查：正面主体只能是单人，例如 `1girl, solo,` 或 `1boy, solo,`；不要使用多人主体词。
4. 去重检查：删除重复含义标签，例如已有 `holding sword` 时，不要再无意义重复 `holding, holding weapon, holding sword,`。
5. 表情-眼睛固定搭配检查：先看表情词是否隐含睁眼或闭眼，补齐它的固定搭配，例如 `>_<` 必须补 `closed eyes`。
6. 表情-眼睛冲突检查：补齐固定搭配后，再删除冲突的眼睛相关标签，例如 `>_<, closed eyes` 下删除 `open eyes, red eyes, detailed eyes`。

表情-眼睛固定搭配与冲突规则：

| 表情/眼文字 | 必须补齐 | 禁止同用 | 可选处理 |
| --- | --- | --- | --- |
| `>_<` | `closed eyes` | `open eyes, red eyes, blue eyes, green eyes, yellow eyes, detailed eyes` | 删除眼睛颜色/细节词，或换成睁眼表情 |
| `u_u` | `closed eyes` | `open eyes, red eyes, blue eyes, green eyes, yellow eyes, detailed eyes` | 删除眼睛颜色/细节词 |
| `^_^` | `closed eyes` | `open eyes, red eyes, blue eyes, green eyes, yellow eyes, detailed eyes` | 删除眼睛颜色/细节词 |
| `=_=` | `closed eyes` | `open eyes, red eyes, blue eyes, green eyes, yellow eyes, detailed eyes` | 删除眼睛颜色/细节词 |
| `@_@` | `open eyes` | `closed eyes` | 可以保留眼睛颜色，但不建议再加复杂眼部动作 |
| `+_+` | `open eyes` | `closed eyes` | 可以保留眼睛颜色 |
| `<|>_<|>` | `open eyes` | `closed eyes` | 引用格式和实际提示词都直接使用原始标签 |

继续检查：

7. 姿势-动作检查：确认姿势和动作不互相打架，例如 `sitting` 不要和不必要的 `running` 同时出现。
8. 镜头检查：镜头词放在后面，不放进主体词；使用 `cowboy shot` 时，`[补充负面提示词]` 加入 `cowboy,`。
9. 权重检查：只给真正需要强调的标签加权；眼文字建议加权，例如 `(>_<:1.2)`。
10. 负面提示词检查：负面提示词只允许两组：`[模型负面提示词]` 使用当前模型固定部分；`[补充负面提示词]` 只写正面标签触发的语义误解词，默认留空。
11. 最终一致性检查：确认正面提示词和负面提示词没有互相抵消，也没有遗漏逗号、空格和换行格式。

## 禁用词与负面提示词的区别

禁用词是不应该主动使用的标签；负面提示词只由 `[模型负面提示词]` 和 `[补充负面提示词]` 两部分组成。

`[模型负面提示词]` 是当前模型固定负面部分。

`[补充负面提示词]` 是某个已采用正面标签容易引发语义误解时，对应加入的修正词；默认留空。

例如：

```text
[主体禁用词] 2girls, 2boys, multiple girls, multiple boys, group, crowd,
```

这些词只表示不要生成多人，通常不要写进负面提示词。

只有当正面提示词中的某个标签容易引入误解时，才把对应误解词放进负面提示词。例如使用 `cowboy shot` 时：

```text
[镜头词] cowboy shot,
[补充负面提示词] cowboy,
```

## 负面提示词

负面提示词只使用两类，不再拆成质量负面、结构负面、画面污染等临时分类：

推荐结构：

```text
[模型负面提示词] ...
[补充负面提示词] ...
```

`[模型负面提示词]` 必须来自模型固定表。

`[补充负面提示词]` 必须来自补充负面提示词对应表，或来自本次生成过程中明确观察到的语义误解经验；没有需要补充时留空。

### 模型负面提示词表

| 模型 | 固定写法 |
| --- | --- |
| WAI-illustrious-SDXL | `bad quality, worst quality, worst detail, sketch, censor,` |
| RIN Flanime Illustrious - High-Quality Flat Anime | `worst quality, bad quality, username, signature, long neck, twitter username, text, watermark, logo,` |
| 通用 SDXL 动漫模型 | `low quality, worst quality, bad anatomy, bad hands, text, watermark, logo, signature,` |

示例：

```text
[模型负面提示词] bad quality, worst quality, worst detail, sketch, censor,
[补充负面提示词] 
```

### 补充负面提示词对应表

| 正面提示词 | 需要添加补充负面提示词的情况 | 补充负面提示词 | 经验说明 |
| --- | --- |
| `cowboy shot` | 需要七分身镜头，但画面容易出现牛仔帽、牛仔靴、西部服装、牛仔元素时 | `cowboy,` | `cowboy shot` 是镜头词；`cowboy` 是独立语义标签，会污染角色设定 |

使用示例：

```text
[镜头词] cowboy shot,

[模型负面提示词] bad quality, worst quality, worst detail, sketch, censor,
[补充负面提示词] cowboy,
```

自我完善规则：

- 生图前：检查正面提示词是否命中“补充负面提示词对应表”。命中时，把对应词加入 `[补充负面提示词]`。
- 生图后：如果画面出现由某个正面提示词引发的稳定语义误解，记录为新经验，并补充到“补充负面提示词对应表”。
- 更新表格时只加入最小必要词。例如 `cowboy shot` 的补充词是 `cowboy,`，不要扩展成一串不必要的 `cowboy hat, cowboy boots, western,`，除非多次生成证明单个词不足。
- 知道什么时候需要添加补充负面提示词：只有“我采用的某个正面提示词”可能或已经引入了不想要的语义元素时才添加；普通禁用词、多人物限制、未被采用的概念不放入负面提示词。

## 快速检查清单

- 是否保持 `[XX词] tag, tag,` 分组换行格式。
- 实际投喂 ComfyUI 前，是否删除了 `[XX词]` 分组名。
- 主体是否只有单人：`1girl, solo,` 或 `1boy, solo,`。
- 是否没有重复含义，例如 `sword` 和 `holding sword` 是否都真的需要。
- 姿势和动作是否冲突。
- 表情是否补齐固定搭配，例如 `>_<` 要有 `closed eyes`。
- 表情是否存在眼睛冲突，例如 `>_<, closed eyes` 不要再加 `open eyes`、`red eyes`、`detailed eyes`。
- 猫眼是否在引用格式和实际提示词里都直接写成 `<|>_<|>`。
- 背景是否足够具体。
- 镜头词是否放在后面，而不是主体词里。
- 使用 `cowboy shot` 时，`[补充负面提示词]` 是否加入 `cowboy,`。
- 权重是否只用于确实需要加强的标签。
- 负面提示词是否只有 `[模型负面提示词]` 和 `[补充负面提示词]` 两组；补充负面默认是否为空；如有补充，是否来自对应表或本次明确经验。

