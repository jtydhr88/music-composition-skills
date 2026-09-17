# ARR-SPEC v1.0 —《编曲规格单》规范

> 本库唯一的交接物。一物四用：对外交接、AB 测试对象、编译源（→Suno／YuE／MusicXML／MIDI／ChordPro）、生成后校验基准。
> 配套文件：`ARR-SPEC.template.yaml`（空模板）、`example-*.yaml`（填好的示例）、20 条自查见 `mc-workflow` §3.0（仓库内部另有同名 lint 脚本，不随插件发布）
>
> **每个字段都有去向**（可执行／桥接／仅意图，见 §4），可执行字段的取值用行业格式能直接读的写法：和弦拼写全 ASCII、时间位置结构化、音色带 GM 号、段落名封闭集合。原因只有一个：规格单写完要能不经人手变成 MusicXML 领奏谱、MIDI 骨架、ChordPro 和 ABC，交给乐手、DAW、吉他手和模型。

## 0. 使用纪律

1. **`intent` 四项不填齐，不许往下写 `form`。** 这是动笔前必填表，作用等于剧本包的"这场戏的价值转折是什么"。
2. 写完**逐条过 20 条自查**（§2），不过的条目改掉或写明理由再编译。这些是判据不是铁律——写了理由的偏离是合法选择。
3. 生成回来**先逐字段对照规格**看照做率（仓库内部有度量工具可算成分数）。照做率明显低 → 改规格重生成，**不靠听觉返工**。
4. 耳朵只在最后一小步介入：照做率过关后，A/B 打乱、成对比较、同一对听两遍看结论稳不稳。
5. ★ **后端做不到的项不算它的分。** 每个后端在 `backends.yaml` 里声明 `honors: exact|hint|none`，算照做率时把 `none` 的项**排除计分**，不是给 0 分。
6. ★ **可执行字段只用 ASCII。** `△ ♭ ♯ ° ø ×` 这些符号人看着舒服，但 MusicXML、ChordPro、ABC 全都不认。规格单存 `maj7 b # dim m7b5 x2`，人看的美观版由渲染层生成。

---

## 1. 字段规范

### 1.1 `meta`

| 字段 | 类型 | 必填 | 说明 |
|---|---|---|---|
| `spec_version` | str | ✅ | `"1.0"` |
| `title` | str | ✅ | 导出为 MusicXML work-title、MIDI 轨名、ChordPro `{title}`、ABC `T:` |
| `style_layer` | str | ✅ | **基底风格**的 L3 skill id（如 `mc-style-citypop-rnb`）；L3 未建时写 `none` |
| `style_layers` | list | ○ | **风格混合时**填，见 §1.1.1。单风格不填 |
| `key` | str | ✅ | 语法固定：`<主音> <major|minor>`，主音用 ASCII 升降号：`F# minor`、`Bb major`、`D major`。**所有和声记号以此为准**。导出为 MusicXML 的 fifths+mode、MIDI 调号、ChordPro `{key}`、ABC `K:` |
| `mode` | str | ○ | 非自然大小调时填，枚举锁定为 MusicXML 的取值：`major` `minor` `ionian` `dorian` `phrygian` `lydian` `mixolydian` `aeolian` `locrian`。填了 `mode`，`key` 的主音仍是调中心（`D major` + `mode: mixolydian` = D 混合利底亚） |
| `meter` | str | ✅ | 如 `4/4`。变拍写在 `meter_map`，不写在 `form[].note` 的句子里 |
| `meter_map` | list | ○ | `[{bar: 1, meter: "4/4"}, {bar: 33, meter: "3/4"}, …]`，与 `tempo_map` 同形，有则 `meter` 取首项。导出为逐小节拍号 |
| `tempo` | int | ✅ | BPM |
| `tempo_map` | list | ○ | `[{bar: 1, bpm: 96}, …]`，有则 `tempo` 取首项。导出为 MIDI set_tempo 事件与 MusicXML 速度记号 |
| `target_duration` | str | ✅ | `mm:ss`。**受 lint 第 14 条约束**：必须与小节数×拍速推算一致（±10%） |
| `deliverable` | list | ✅ | `full_mix` / `stems` / `midi` / `musicxml` / `chordpro` / `abc`，后四个对应 §4 的四个导出目标 |

#### 1.1.1 `style_layers` —— 风格混合的分工表 ✦

**混合不是把两种风格调匀，是把每个维度分给一种风格。**

`owns` 的取值是一个**封闭集合**，只能从下面十四个里选：

`harmony`　`melody`　`groove`　`low_end`　`fill_policy`　`instrumentation`　`color_instruments`
`timbre`　`arrangement_hook`　`vocal_delivery`（或拆成 `vocal_delivery_verse` / `vocal_delivery_chorus`）　`form`　`mix`

需要分配的东西不在表里时，归到最接近的那一个，并在 `fusion_note` 里写一句说明；不要自造名字。
污染与混合的区别只有一条：**混合是写下来的选择，污染是没人选**。

```yaml
meta:
  style_layer: mc-style-chinese-pop        # 基底
  style_layers:
    - {skill: mc-style-chinese-pop, role: base,    owns: [harmony, melody, color_instruments]}
    - {skill: mc-style-hiphop,      role: overlay, owns: [groove, low_end, vocal_delivery_verse]}
  fusion_note: "副歌唱、主歌 rap；民乐采样只做色彩层不做律动"
```

| 字段 | 说明 |
|---|---|
| `skill` | L3 skill id |
| `role` | `base`（恰好一个）／`overlay`（一个或多个） |
| `owns` | 该风格拥有的维度，取值固定：`harmony` `melody` `groove` `low_end` `instrumentation` `color_instruments` `timbre` `vocal_delivery` `vocal_delivery_verse` `vocal_delivery_chorus` `form` `mix` |
| `fusion_note` | 一句话说这个组合为什么成立 |

**受 lint 第 17 条约束**：多于一层时，`base` 恰好一个；**每个维度只有一个 owner**（两层都声称 `groove` = 没做选择）；
每层至少拥有一个维度（否则它只是被读了，没起作用）。

**怎么用各 L3 的移植表**：overlay 风格搬进来的正是它移植表里标"❌ 不可搬／会被认出来"的那些项——
**那就是你要的辨识度**。但每搬一项，都要在 `owns` 里签字，并检查 base 风格的移植表是否允许让出那个维度。

### 1.2 `intent` —— 动笔前必填表（★ 门禁）

| 字段 | 类型 | 必填 | 说明 |
|---|---|---|---|
| `one_thing` | str | ✅ | **这首歌只干一件事**。一句话，不许两句，不许用"既…又…"。写不出来说明还没想清楚 |
| `reference_pair` | list | ✅ | **恰好 2 项**，每项 `{track, borrow}`。`borrow` 必须写清"借哪一点"（如"只借前奏的空"），**只写歌名视为未填** |
| `listener_situation` | str | ✅ | 在哪听、听第几遍、注意力有多少。决定 hook 出现的早晚和信息密度 |
| `emotional_arc` | list | ✅ | `[{section, mood, value}]`。**必须有转折**，一路向上等于没写 |

> 为什么设门禁：Suno 的 AI 味不是音色问题，是**没有人做过选择**。这四项就是强迫做选择的地方，等价于作词侧的"只说一件事／对谁说／在哪个时刻／一个贯穿的物件"。
> 这一块整体是**仅意图**：行业里没有任何格式存它，导出时整块作为文本随文件走（MusicXML 的 miscellaneous、MIDI 第一小节的 text 事件），不丢"为什么"。

### 1.2b `material` —— 主题材料（★ `mc-development` 拥有）

| 字段 | 类型 | 必填 | 说明 |
|---|---|---|---|
| `motif` | str | ✅ | 主题动机的文字描述：音程轮廓、节奏型、长度。写到能让另一个人认出来 |
| `motif_abc` | str | ○ | 同一个动机用一到两小节 ABC 写出来（只要 `L:` 和音符，不要头部）。有了它，MusicXML 和 MIDI 能把动机当实际音符导出，符号路径拿它当种子 |
| `character` | str | ○ | 一句话性格（"急促、往上顶"） |
| `stated_in` | list | ✅ | 原始陈述所在的段落 id，至少一个 |
| `development_mode` | str | ○ | 这首歌靠什么推进：`加工处理`／`展衍`／`对比`（`mc-development` §5.1，李吉提的三路） |

### 1.3 `form` —— 曲式表，一段一行

| 字段 | 类型 | 必填 | 说明 |
|---|---|---|---|
| `id` | str | ✅ | 全局唯一，如 `I`/`A1`/`B1`/`C1`/`D`/`O`。导出为 MusicXML 排练记号、MIDI marker、ChordPro 段落 label |
| `name` | str | ✅ | 封闭集合：`Intro` `Verse` `Pre-Chorus` `Chorus` `Post-Chorus` `Bridge` `Instrumental` `Solo` `Breakdown` `Drop` `Outro`。Suno 段落标签、ChordPro 的 `{start_of_verse}`/`{start_of_chorus}`/`{start_of_bridge}` 都由它派生，不另存。术语的准确定义见 `mc-arrangement-arch/reference.md` §1 |
| `bars` | int | ✅ | 小节数。**受 lint 第 3 条约束**：全曲不许全是 8 的倍数 |
| `start_bar` | int | ✅ | 起始小节。**受 lint 第 2 条约束**：必须首尾相接，不许有缝或重叠 |
| `harmony` | str | ✅ | 罗马数字级数，ASCII：`"|VIm7 |IVmaj7 |I |V |"`。变化音前缀 `b`/`#`（`bVI`、`#IVm7b5`），副属 `V/V`。分析与教学用；MusicXML 4.0 的 numeral 能存，软件几乎不读 |
| `harmony_letters` | str | ✅ | 同一进行的字母记号：`"|F#m7 |Dmaj7 |A |E |"`。**双记是本库硬约定**。拼写见 §1.3.1，**受 lint 第 19 条约束**。导出为 MusicXML harmony、ChordPro `[F#m7]`、ABC `"F#m7"`、MIDI 和弦轨 |
| `melody_register` | str | ○ | `低`/`中低`/`中`/`中高`/`高`。段落间要有对比。可加实际音域 `melody_range: "A3-E5"`（ASCII 音名加八度），有则导出为分谱音域提示 |
| `development` | str | ✅ | ★ 这一段拿主题材料做了什么，封闭集合：`repeat`（原样或换尾重复）/`sequence`（模进）/`extend`（扩充）/`contract`（减缩）/`fragment`（裁截）/`vary`（变奏）/`augment`（宽放紧收）/`counterpoint`（对位式）/`new`（新材料）/`recap`（再现）。可写 `"vary+extend"` 表示主次两种。**受 lint 第 18 条约束**。手法定义见 `mc-development` |
| `energy` | int | ✅ | 0–10。与 `arrangement.energy_curve` 同源，此处冗余便于阅读 |
| `events` | list | ○ | 该段发生的编制事件，取值固定为 `<roster_id>_in` / `<roster_id>_out`，如 `[drums_in, bass_in, gtr_out]`。与 `roster[].entry/exit` 互为交叉校验 |
| `note` | str | ○ | **非对称设计写在这里**，如"第二句 6 小节，打破 8 的整数"。变拍不写这里，写 `meter_map` |

#### 1.3.1 和弦拼写（★ lint 第 19 条）

一对竖线一小节。一小节里多个和弦用空格分隔、按拍等分；不等分时在和弦后写拍数：`|Em7(3) A(1) |`。`%` 重复上一小节。整段反复在串尾写 `x2`。

根音：`A`–`G` 加 `#` 或 `b`。斜线低音：`/E`。种类后缀是封闭集合，右列是导出到 MusicXML 时的 kind：

| 写法 | 含义 | MusicXML kind |
|---|---|---|
| （空） | 大三 | major |
| `m` | 小三 | minor |
| `5` | 强力 | power |
| `dim` | 减三 | diminished |
| `aug` | 增三 | augmented |
| `sus2` `sus4` | 挂二／挂四 | suspended-second / suspended-fourth |
| `6` `m6` | 大六／小六 | major-sixth / minor-sixth |
| `69` | 六九 | major-sixth + degree 9 |
| `7` | 属七 | dominant |
| `maj7` | 大七 | major-seventh |
| `m7` | 小七 | minor-seventh |
| `mMaj7` | 小大七 | major-minor |
| `m7b5` | 半减 | half-diminished |
| `dim7` | 减七 | diminished-seventh |
| `aug7` | 增属七 | augmented-seventh |
| `7sus4` | 属七挂四 | suspended-fourth + degree 7 |
| `9` `maj9` `m9` | 九和弦 | dominant-ninth / major-ninth / minor-ninth |
| `11` `13` | 十一、十三 | dominant-11th / dominant-13th |
| `add9` `madd9` | 加九 | major / minor + degree add 9 |
| `7b9` `7#9` `7#11` `7b13` | 变化属七 | dominant + degree alter |

不在表里的写法（`△`、`ø`、`°`、`M7`、`-7`、`Δ`）一律视为拼错。想用的音响表里没有时，选最近的一个并在 `note` 里说明。

### 1.4 `arrangement`

#### `roster` —— 全曲乐器清单

每件乐器都要说得出：占哪个频段、什么时候进、什么时候退。**说不出"什么时候退"的乐器，通常就是让编曲变平的那一件。**

| 字段 | 必填 | 说明 |
|---|---|---|
| `id` | ✅ | 唯一，`mix_intent`、`form[].events`、`render.midi` 都按此 id 引用。导出为 MusicXML part-name、MIDI 轨名、ABC `V:` 名 |
| `instrument` | ✅ | 具体乐器／音色，不许写"合成器"这种类别词。人读的名字 |
| `midi` | ✅（`deliverable` 含 `midi`/`musicxml` 时） | `{program: 0-127, bank: 0, channel: 1-16}`，GM 音色号。鼓组 `channel: 10`。这是导出用的一处为准，`render.midi.instrument_map` 只放 VST 名等备注 |
| `role` | ✅ | `律动`/`低频`/`和声铺底`/`织体`/`旋律`/`色彩`/`记忆点` |
| `band` | ✅ | 主要占据的频段。**填四段之一**（`低频`/`中低`/`中高`/`高`，分割点 250 Hz / 2 kHz / 6 kHz）或具体区间如 `"1-3 kHz"`。与 `spectrum_budget` 交叉校验 |
| `entry` | ✅ | 进场段落 id |
| `exit` | ✅ | 退场段落 id，或 `end`。**受 lint 第 7 条约束**：至少一件乐器的 `exit` 不是 `end` |
| `timbre` | ○ | **给文本后端看的**音色描述：材质、年代、参照乐器。不写旋钮数值 |
| `synth_params` | ○ | **给参数后端看的**同一音色。★ 必须与 `timbre` **同源**（都从"音色四问"的答案生成），不同源会导致文本后端和 MIDI 后端听起来是两首歌。能映到 MIDI 控制器的四项按 GM2 编号写：截止 `cc74`、共鸣 `cc71`、起音 `cc73`、释放 `cc72`，取值 0–127 |
| `note` | ○ | |

#### `depth_map` —— 深度三层 ✦

`{roster_id: 前景|中景|背景}`。听众凭「高／响／快／变化率快」四条线索判断谁是前景，
**其中只有"响"是混音能调的**——所以深度必须在编曲阶段规划。
详见 `mc-texture-layering` §2–§3。导出时粗略映到 MIDI 混响发送 cc91（前景低、背景高）。

#### `spectrum_budget` —— 频段预算

`[{band, owner, competitors, resolution}]`。写清哪两件抢频段、怎么分工：**让频**／**错开音区**／**交替出现**。
**`resolution` 必须是这三者之一**——这是"编曲＝频谱预算"这个观念的落点，也是 `mix_intent` 之所以能只写意图的前提。

> 验收标准：画出来的频段归属应当**既不交叠、又连续**（Izhaki 图 6.2 的 b 图）。
> 只做到不交叠会留下空洞，只做到连续会糊。

#### `bass_kick_relation` —— 贝司与底鼓的关系 ✦

`{mode: 结合式|互答式|交织式, detail}`。三选一，必须写明是哪一种。

- **结合式**：贝司节奏随底鼓走、完全重合 → 音头强、结实
- **互答式**：贝司避开底鼓的节奏点 → 适合底鼓覆盖低频的电子舞曲
- **交织式**：需要时重合、不需要时拆开 → **默认推荐**，但要求逐小节说得出哪里合哪里分；说不出就退回结合式

#### `energy_curve`

`{段落 id: 0–10}`，key 集合必须等于 `form` 的 id 集合（lint 第 4 条）。
**受 lint 第 5 条约束：必须至少有一次下降。** 单调递增是 Suno 最强的默认值。
（实测：62/62 首真实编曲都有下降，**强规则**。）参考渲染时折成力度缩放或 cc11。

#### `subtraction_events` —— 减法事件（★ 至少 1 个）

`[{at, what, why}]`。典型：第二段副歌前撤掉鼓、只剩人声＋一件乐器、断掉低频一小节。
**受 lint 第 6 条约束。** 真唱片靠减法制造冲击，AI 只会加。

`at` 是结构化的时间位置（§1.9），不是句子：`at: {section: C2, bar_offset: -1, beat: 4}` 表示 C2 前一小节第四拍；`at: {section: D}` 表示整段。"进入前最后半拍"这种描述放 `what`，位置放 `at`。

#### `hooks`

| 字段 | 必填 | 说明 |
|---|---|---|
| `vocal_hook` | ○ | `{where, shape}` |
| `arrangement_hook` | ✅ | `{what, first_at, recurs_at, abc}`。**非人声的记忆点**——riff／标志性音色／反复出现的音响事件。**受 lint 第 8 条约束**。缺这一项是"听着还行但记不住"的主因。`abc` 可选，同 `motif_abc`，有则能导出成谱 |

#### `groove`

| 字段 | 必填 | 说明 |
|---|---|---|
| `feel` | ✅ | `straight`/`swing16`/`shuffle`/`halftime`/`2:1`/`3:1`。MusicXML 4.0 的 swing 元素能存前三种；MIDI 无标准，靠参考渲染的时值实现 |
| `quantize_policy` | ○ | `{part: 百分比}`。★ **量化是逐乐器的决策，不是全局开关**——至少一件守网格、至少一件不守 |
| `push_pull` | ✅ | `[{part, offset_ms, note}]`。**受 lint 第 11 条约束**：至少一项且 `offset_ms ≠ 0`。全部对齐网格＝机器感的根源（实测 62/62，**强规则**）。参考渲染时按速度折成 tick 偏移 |
| `fill_policy` | ✅ | `{bars: [int], rationale}`。**受 lint 第 10 条约束**：`bars` 不许是公差为 4 的等差数列 |

### 1.5 `vocal` —— 演唱设计

对应 `mc-vocal-direction`。**AI 人声最稳定的破绽是"完美"**：音准全中、力度平直、
每遍副歌一模一样。这一段有一半是在**要求"不完美"**。

| 字段 | 必填 | 说明 |
|---|---|---|
| `persona` | ✅ | **这是谁在唱**：性别、音区、质感、年代感。写"女声"视为未填 |
| `delivery` | ✅ | 气声／实声／半声；咬字松紧。**可以是单串，也可以 `{段落 id: 描述}` 逐段写**。用可执行的词：`亮`/`气声`/`辅音用力`——★ **不许写"大声点"**（音量是推子的事，不是演唱的事） |
| `dynamics_by_section` | ✅ | `{段落 id: 0–10}`。**受 lint 第 15 条约束**：不许全程同一力度。导出到 MusicXML 力度记号按此映射：0–1 `ppp`、2 `pp`、3 `p`、4 `mp`、5–6 `mf`、7 `f`、8 `ff`、9–10 `fff`；MIDI 参考渲染按 `40 + 8×值` 取力度 |
| `phrasing` | ○ | `[{line, timing, why}]` 哪几句往后拖（laid back）、哪句抢在拍前。★ 判断抢／拖看的是**元音**的位置，不是音节起点 |
| `breath_points` | ○ | `[{section, line, after_syllable, note}]`：段落 id、第几行（1 起）、在该行第几个音节之后换气。换气也是留白，位置要和歌词的断句对上。导出为 MusicXML 的 breath-mark |
| `ad_libs` | ○ | `[{at, what}]`，`at` 用 §1.9 的时间位置；★ 禁止均匀撒 |
| `chorus_variation` | ✅ | ★ 副歌每一遍怎么各不相同。**受 lint 第 16 条约束**。（词可以一样，编曲不能一样） |
| `stacking` | ○ | 和声／加倍：堆几层、在哪几句进、用**四型**中的哪型（全声上方／贴唱上方／内敛下方／合いの手）。做 double 要求的是**二次演唱**，不是复制一轨。写了实际音高的层导出为 MusicXML 额外声部 |
| `pitch_policy` | ○ | ★ 写**哪些位置保留音准偏移**，而不是"修准"。唱低的人声推子推到天上也出不来；**略偏高的穿透力更好**。失效条件：编曲极密的段落该修，判据是**伴奏密度**不是段落名 |
| `register_arc` | ○ | 音区走向，与 `form[].melody_register` 一致 |

### 1.6 `mix_intent`

只写**意图**，不写 EQ 频点／压缩比／母带链（那些归库三）。
**参数之间互相牵动**（提人声高频 → 混响音尾变长 → 推子要调 → 压缩参数要调），
在规格单里钉死一个数值等于替混音师锁死后面四步。这一块除 `sound_stage` 外全是仅意图。

| 字段 | 必填 | 说明 |
|---|---|---|
| `mood` | ✅ | ★ 一句话说这首混音要传达什么。**对其余所有决定有否决权**，且**在生成提示词里要靠前**（生成模型也是"只擅长某种风格的混音师"，会把输入往它见得最多的平衡上拉） |
| `sound_stage` | ○ | `[{id, pan, depth}]` 逐乐器的左右＋前后。`pan` 取 `-100..100`（负左正右，0 居中），导出线性折到 MIDI cc10 的 0–127 和 MusicXML pan 的 -180..180；`depth` 取 `前景/中景/背景`，与 `depth_map` 同一枚举。★ **同组乐器的深度必须一致**（深度要"一致"不要"平衡"）；鼓组**内部**的方位归 `mc-rhythm-section`，这里只定鼓组整体坐在哪 |
| `focus_by_section` | ○ | `{段落 id: roster_id}` 每段谁必须是最清楚的元素。★ **先写最密的那一段**，其余相对它定义 |
| `ducking_map` | ○ | `[{section, who_yields, band, to}]` 哪一段谁让出哪个频段给谁 |
| `deliberate_imbalance` | ○ | `[{from_bar, to_bar, what, why}]`。★ **必须带起止小节**——不平衡是**事件**不是状态，没写小节范围的一律当失误 |
| `reference_tracks` | ○ | `[{track, aspect}]`。★ `aspect` **必须写明参考它哪一方面**，只写歌名视为未填。注意母带影响：参考曲的动态值会系统性偏紧 |
| `dynamics` | ✅ | `{target_DR_dB, notes}`。`notes` 里写"哪一段必须比副歌小 x dB" |
| `width_map` | ✅ | `{段落 id: narrow/medium/wide}`。**受 lint 第 12 条约束**：至少两个不同值 |
| `loudness_target` | ✅ | 如 `"-9 LUFS integrated"` |

### 1.7 `render`

#### `render.suno`

| 字段 | 必填 | 说明 |
|---|---|---|
| `style_prompt` | ✅ | 风格描述串。**不许出现 `epic`/`emotional`/`beautiful` 这类无信息形容词**（lint 第 13 条软警告） |
| `section_tags` | ○ | 由 `form[].name` 派生（`[Intro]` `[Verse]` …），编译时生成，不必手填。手填了则**数量与顺序必须与 `form` 一致**（lint 第 13 条） |
| `exclude` | ○ | 负面提示 |
| `knobs` | ○ | `{model, weirdness, style_influence}` |

#### `render.yue`

`{genre_tags, structure, ref_audio, abc}`。YuE2 吃 genre tag 串 ＋ 结构化段落，
**并且可以直接吃 ABC 乐谱**（`abc=` 参数）——这是唯一能让规格被"执行"而不是"提示"的路径。
`form[].harmony_letters` / `key` / `meter` / `tempo` 直接编译成 ABC。编译规则见 `mc-symbolic-score`。

#### `render.midi`

`{instrument_map: {roster_id: {name, program, bank, channel}}}`。`program/bank/channel` 与 `roster[].midi` 同源，以 roster 为准；`name` 放 VST 或采样库名这类 GM 表达不了的备注。只有 VST 名时也要给 `program` 作回退，否则参考渲染没有声音。

#### `render.musicxml`

`{lead_sheet: true, parts: [roster_id, …]}`。不填默认只出人声领奏谱：旋律、和弦标记、歌词音节、段落记号、速度、调号拍号、力度、换气。`parts` 列了哪些 roster id，就多出哪些分谱（需要该声部有实际音符，来自符号路径）。

#### `render.chordpro`

`{transpose: 0}`。段落由 `form[].name` 派生，和弦由 `harmony_letters` 派生，歌词来自 LYR-SPEC。

### 1.8 `checks`

`self_audit: [ … ]` —— 作者逐条打勾的人工项（自动项由 lint 跑）。人工项固定这几条：

- [ ] `one_thing` 念出来是一句话，不是两件事拼的
- [ ] 两个参照各借的点不重叠
- [ ] `arrangement_hook` 在不看谱的情况下能哼出来
- [ ] 至少一段的 `melody_register` 与相邻段落明显不同
- [ ] 全曲有一处"该说的地方没说"（留白），写明在哪
- [ ] 每段的"最高、最响、最快、变化最剧烈"分别是谁，列得出来；**主角至少占两条**
- [ ] 低频主体若是正弦波／极暗音色，补了泛音并**在手机外放验证过**（差音问题）

`exemptions: [{rule, why}]` —— 豁免项。**豁免必须写理由，不许静默跳过。**

### 1.9 时间位置的写法

所有"在哪发生"的字段（`subtraction_events[].at`、`ad_libs[].at`、`deliberate_imbalance`、`phrasing[].line` 若指位置）只认两种形式：

- 绝对：`{bar: 40}` 或 `{bar: 40, beat: 3}`
- 相对段落：`{section: C2}`（整段）、`{section: C2, bar_offset: -1}`（C2 前一小节）、`{section: C2, bar_offset: 0, beat: 4.5}`（C2 第一小节第四拍半）

`beat` 从 1 起，可带小数。这两种形式都能无损换算成 MIDI tick 和 MusicXML 小节号，句子换算不了。

---

## 2. 20 条自查（SPEC-LINT）

人工逐条过；仓库内部有同名脚本做同样的检查，不随插件发布。**不过 = 改或写理由**，不是禁止编译。

| # | 检查 | 对抗的默认值 |
|---|---|---|
| 1 | `intent` 四项非空；`reference_pair` 恰好 2 项且 `borrow` 非空 | 没做过选择 |
| 2 | `form` 的 `start_bar` 首尾相接无缝无叠 | 规格自相矛盾 |
| 3 | 至少一个段落的 `bars` 不是 8 的倍数 | 段落全是 8 的整数倍 |
| 4 | `energy_curve` 的 key 集合 == `form` 的 id 集合 | 规格不完整 |
| 5 | `energy_curve` 至少有一次下降 | 能量单调递增 |
| 6 | `subtraction_events` ≥ 1 | 只会加不会减 |
| 7 | `roster` 每项有 `entry`；至少一项 `exit != end` | 编制从头到尾不变 |
| 8 | `arrangement_hook.what` 非空 | 没有非人声记忆点 |
| 9 | 至少一个非 Intro/Outro 段落的 `harmony` 含调外和弦或转调标记 | 一个循环到底 |
| 10 | `fill_policy.bars` 不是公差为 4 的等差数列 | fill 刻板 |
| 11 | `groove.push_pull` 至少一项且 `offset_ms != 0` | 网格化 |
| 12 | `mix_intent.width_map` 至少两个不同值 | 立体声宽度恒定 |
| 13 | `render.suno.style_prompt` 非空；`section_tags` 若手填则与 `form` 段数、顺序一致 | 编译不完整 |
| 14 | `target_duration` 与 `Σbars × meter ÷ tempo` 偏差 ≤ 10% | 时长永远 3:30 |
| **15** | `vocal.dynamics_by_section` 至少两个不同值 | 人声全程一个力度 |
| **16** | `vocal.chorus_variation` 非空且说得出每遍改了什么 | 副歌逐遍原样复制 |
| **17** | `style_layers` 多于一层时：恰好一个 base；每个维度只有一个 owner；每层至少一个维度 | 风格"调匀"——两种默认值叠在一起，谁也没选 |
| **18** | `material.motif` 与 `stated_in` 非空；`form[].development` 全部填了；填 `new` 的段数不过半；全曲至少一段 `repeat` 或 `recap`；副歌各遍不全是 `new` | 每段都写新东西，不发展只堆 |
| **19** | `harmony_letters` 每个和弦的根音、后缀、斜线低音都在 §1.3.1 的封闭集合里，全 ASCII；`harmony` 同样 ASCII | 拼写各写各的，导不出去 |
| **20** | `key` 合 `<主音> <major|minor>` 语法；`mode` 在枚举内；`meter` 与 `meter_map`/`tempo_map` 的小节号落在曲内；`form[].name` 在封闭集合内；`subtraction_events[].at` 是 §1.9 的结构 | 可执行字段写成了句子 |

**失效条件（必须写进 `checks.exemptions`）**：

- 第 9 条：极简主义作品（ambient／drone／单和弦 vamp）可豁免
- 第 11 条：`feel: straight` 的硬网格电子舞曲可把量级写为 0，说明"网格化是本风格的语言"
  （注意这与 62/62 那条强规则不矛盾——强规则说的是**不许全曲每一件都在网格上**，
  鼓组绝对量化是允许的，见 `mc-rhythm-section` §2.0）
- 第 15／16 条：**无人声的纯器乐作品整段 `vocal` 可缺省**，此时两条自动跳过
- 第 18 条：**通篇即兴或 ambient／drone 这类没有主题材料的作品**，`material.motif` 写"无主题材料"并在这里说明，`development` 可全填 `new` 或留空
- 第 19／20 条：没有失效条件。这两条查的是语法，不是判断

---

## 3. 与实测量的对应（照做率怎么来）

| ARR-SPEC 字段 | 生成音频侧的实测量 | 判定 |
|---|---|---|
| `form[].start_bar` | 段落边界检测（秒 → 小节） | 边界命中率，容差 ±1.5 小节 |
| `form[].harmony` | 每小节 chroma → 和弦模板匹配 | 和弦序列编辑距离 |
| `meta.tempo` | beat tracking | 相对误差（容差 4%） |
| `groove.push_pull` | onset 相对网格偏移的均值 | 符号与量级是否一致 |
| `energy_curve` | 分段 RMS | 与规格曲线的 Spearman 相关 |
| `roster[].entry/exit` | 分轨 onset 密度 | 事件命中率 |
| `dynamics.target_DR_dB` | crest factor / LRA | 绝对差 |
| （AI 味通用项） | 频谱质心时序方差 | 低于阈值即判"全程一个样" |
| `width_map` | 分段 M/S 比与相关系数 | 是否随段落变化 |
| `target_duration` | 实际时长 | 相对误差 |

加权得出 **0–100 照做率**（仓库度量工具实现；用户侧按同一张表逐字段对照即可，不必算成数）。

> ★ **零点校准**：不相关的、制作精良的成品对本表打分落在 **63–66**。
> 所以我们把 **82／70** 当参考线，不是 60——这是实验标定值，不是交付门槛。
> 这个校准值必须随权重改动一起重测——**改权重后没重测零点 = 数据不可比**。
>
> ★ 一个踩过的坑：把权重从**有区分度**的项（`boundaries`）挪到**没区分度**的项
> （`energy_shape`，不相关曲目也能拿 0.8）会**抬高所有人的分**，包括零点基线。
> 加权重要加在能区分的项上。

> ★ **后端能力**：`honors: none` 的项**排除计分**，不是给 0 分。
> 否则一个诚实声明"我做不到声像"的后端，会比一个假装做得到的后端得分更低。

---

## 4. 字段去向：哪些能导出，导到哪

规格单不是 MusicXML 也不是 MIDI，它们存结果不存原因。但每个字段都要说得出去向，三选一：

- **可执行**：无损导出到至少一种行业格式，取值语法按目标格式写。
- **桥接**：行业格式没有对应字段，用它现有的机制近似（力度、控制器、静音、文字标记），有损，规格单仍是权威。
- **仅意图**：行业里没有格式存它。导出时整块作为文本随文件走，不丢"为什么"。

| 去向 | 字段 |
|---|---|
| 可执行 | `title` `key` `mode` `meter` `meter_map` `tempo` `tempo_map` `form[].id/name/bars/start_bar/harmony_letters` `melody_range` `motif_abc` `arrangement_hook.abc` `roster[].id/instrument/midi` `breath_points` `stacking`（写了音高时） `render.midi/musicxml/chordpro/yue.abc` |
| 桥接 | `form[].energy/events` `roster[].entry/exit/synth_params` `depth_map` `energy_curve` `subtraction_events` `groove.*` `dynamics_by_section` `phrasing` `sound_stage` |
| 仅意图 | `style_layer(s)` `fusion_note` `intent.*` `material.motif/character/stated_in/development_mode` `form[].harmony/melody_register/development/note` `roster[].role/band/timbre` `spectrum_budget` `bass_kick_relation` `hooks.*.what` `fill_policy` `vocal` 其余各项 `mix_intent` 其余各项 `render.suno.*` |

四个导出目标与各自的受众：

| 目标 | 给谁 | 带什么 |
|---|---|---|
| MusicXML 领奏谱 | 乐手、记谱软件 | 旋律、和弦标记、歌词音节、段落记号、速度、调号拍号、力度、换气 |
| MIDI 骨架 | DAW | 速度表、拍号、段落 marker、各轨音色号、进退场、声像、参考渲染 |
| ChordPro | 吉他手、排练 | 段落、和弦、歌词 |
| ABC | 大模型、YuE2 | 旋律、和弦、歌词行，见 `mc-symbolic-score` |

DAW 私有工程格式与 DAWproject 不对接。`mix_intent` 不找格式，混音意图在行业里就是文字。
