---
name: mc-workflow
description: Entry point and router for the music composition library (作曲编曲工作流与路由). The S0-S7 stage table from brief to delivered audio, which skill to load for which task, the full-library boundary table that prevents two skills claiming the same decision, the ARR-SPEC gate rules, and the three testing devices (routing, text-layer A/B, compliance rate). Load this FIRST for any request that will produce or change an ARR-SPEC (a whole track, a section rewrite, a generation run). A single knowledge question or a single-part task goes straight to the owning skill without this one. Use when starting a new track, when unsure which skill owns a question, when a spec fails lint, when a generated result sounds wrong and you need the diagnostic path, or when handing work to another library. 作曲、编曲、工作流、路由、该读哪个 skill、ARR-SPEC、出歌流程。
---

# 作曲编曲工作流与路由（Workflow & Routing）

**任何作曲/编曲请求先读这个 skill，再决定读哪个别的。**

这个库的产出**不是音频，是一份 ARR-SPEC**（编曲规格单）。
音频由后端生成，规格单决定它长什么样、以及怎么验收它。

> **本库的根本立场**：AI 味不是音色问题，是**没有人做过选择**。
> 整套流程就是一台**强迫做选择的机器**。

---

## 0. 三十秒定位：我该去哪

| 你现在要做的事 | 去 |
|---|---|
| 从零开始一首歌 | §1 阶段表，从 S0 走。★ S2a 必须先读 `mc-development` 再填 `material` 与 `form[].development`，只照 schema 的字段说明填会把每段写成一个新画面 |
| 已有 brief，要出规格单 | §1 的 S1–S4（S2 现分 S2a 材料与发展、S2b 和声） |
| 规格单写完了，要生成 | §1 的 S5 ＋ `mc-render-compile` |
| 生成回来了，不对劲 | §4 诊断路径 |
| 不知道某个问题归哪个 skill | §2 全库边界表 |
| lint 不过 | §3.2 |
| 要交给别的库 / 别的人 | §5 跨库接口 |
| 要跑测试 | §6 三个测试装置 |
| **要混合两种风格**（rap + 中国风、city pop + EDM…） | ★ §2.4 |

---

## 1. 阶段表 S0–S7

**每个阶段有明确的产出物和门禁。门禁不过不许进下一阶段。**

| 阶段 | 做什么 | 产出 | 门禁 | 主用 skill |
|---|---|---|---|---|
| **S0 立意** | 把 brief 变成"只干一件事" | `intent` 四项 | **四项齐全**，`borrow` 不许只写歌名 | 本 skill §3.1 |
| **S1 骨架** | 定调、拍、速度、曲式表 | `meta` + `form` | 段落首尾相接；**不许全是 8 的倍数** | `mc-form`、`mc-harmony`、`mc-arrangement-arch` |
| **S2a 材料与发展** | 定主题动机、原始陈述在哪段、每段拿它做什么 | `material` + `form[].development` | **先读 `mc-development` 再填**；motif 与 stated_in 非空；每段 development 都填；填 new 的段不过半 | `mc-development`、`mc-melody` |
| **S2b 旋律与和声** | 写主旋律、和声进行、必要的对位 | `form[].harmony` + `harmony_letters` | **双记号都要有**；至少一段有调外和弦 | `mc-melody`、`mc-harmony`、`mc-progressions`、`mc-modulation`、`mc-counterpoint` |
| **S3 编制** | 谁在场、什么时候进退、占什么频段 | `arrangement` 全段 | 能量曲线**必须有下降**；至少一件乐器提前退场；至少一个减法事件 | `mc-arrangement-arch`、`mc-orchestration`、`mc-texture-layering`、`mc-rhythm-section`、`mc-sound-design` |
| **S4 演唱与混音意图** | 怎么唱、声场怎么摆 | `vocal` + `mix_intent` | 力度**逐段不同**；副歌**逐遍有变化**；宽度至少两种取值 | `mc-vocal-direction`、`mc-mix-intent` |
| **S5 编译** | 规格单 → 各后端输入 | `render.*` | 按 §3.0 的 20 条自查一遍；不过的条目改掉或写明理由 | `mc-render-compile`、`mc-symbolic-score` |
| **S6 生成** | 跑后端，取多版 | 音频 | 按 `backends.yaml` 的 `honors` 决定哪些字段可指望 | `mc-render-compile` |
| **S7 验收** | 先算再听 | 照做率 + AI 味旗标 | 先分开看两件事：后端照做了没有、成品有没有机器指纹；耳朵在这之后介入 | `mc-ai-tell-audit` |

### 1.1 ★ 三条不许跳的顺序

1. **`intent` 不齐不许写 `form`** —— 这是整套流程唯一的硬门禁
2. **17 条不过不许编译** —— 不过说明规格没写完，不是脚本挑刺。★ 没有 Python 就按 §3.0 逐条自查，**不要重写 lint**
3. **照做率不过不许用耳朵返工** —— 先改规格重生成，耳朵是最后一小步

> 第 3 条最容易破。"我听着不对，我调一下混音"——
> 那一刻你就从**规格驱动**掉回**手感驱动**了，
> 而手感驱动的产物无法复现、无法 AB、无法积累。

### 1.2 耳朵怎么介入（S7 之后）

照做率过关后才听，而且要：**A/B 打乱 → 成对比较 → 同一对听两遍看结论稳不稳。**
两遍不是多余的谨慎：听觉系统会在重复暴露里改变自己的处理策略，同一段声音第二次听可能得到不同的结论，这类判断天然容易不可重复（p0251-p0252）。结论两遍不一致 = 这个差异不存在，别改。

如果两次听中间隔的时间较长，还要防一种相反的偏差：耳朵会对持续暴露的音色变化逐渐适应，越听越觉得两版差不多，容易把真实存在的差异误判成"不存在"（p0342）。**两次判断之间留出间断，别来回连着切**，能同时防住这两头。

---

## 2. 全库边界表

**每一个决策只有一个 owner。** 拿不准就查这张表。

### 2.1 按问题查

| 问题 | Owner |
|---|---|
| 这首歌要干什么 | **本 skill** S0 |
| 用什么曲式、段落多长 | `mc-form`（乐理） ＋ `mc-arrangement-arch`（非对称与能量） |
| 旋律怎么写、可唱性 | `mc-melody` |
| 和弦怎么选、怎么配 | `mc-harmony` |
| 查一个现成进行 | `mc-progressions` |
| 怎么转调 | `mc-modulation` |
| 节奏型是什么 | `mc-rhythm-groove` |
| 声部怎么独立、怎么避免平行 | `mc-counterpoint` |
| 一个动机怎么长成一段、一段怎么长成一首 | `mc-development`（L1）；旋律本身在 `mc-melody`，段落功能在 `mc-form` |
| 乐器什么时候进、什么时候退、能量曲线 | `mc-arrangement-arch` |
| 用哪件乐器、混合音色 | `mc-orchestration` |
| 频段怎么分、谁和谁抢、前中后景 | `mc-texture-layering` |
| 打得像不像人、贝司和底鼓怎么配 | `mc-rhythm-section` |
| 合成器音色怎么做 | `mc-sound-design` |
| 人声怎么唱、和声怎么加 | `mc-vocal-direction` |
| 声场怎么摆、谁给谁让路 | `mc-mix-intent` |
| 这个风格该怎么做 | `mc-style-*`（L3） |
| 规格单怎么变成 prompt | `mc-render-compile` |
| 要出 ABC / MIDI / MusicXML | `mc-symbolic-score` |
| 生成的东西哪里像 AI | `mc-ai-tell-audit` |
| 找一个真实作品的拆解 | `mc-case-studies` |

### 2.2 六组最容易混的分界（★ 逐条记住）

| A ⇄ B | 分界线 |
|---|---|
| `mc-form` ⇄ `mc-arrangement-arch` | **form 管曲式是什么（乐理），arrangement-arch 管这首歌为什么这么切（决策）** |
| `mc-rhythm-groove` ⇄ `mc-rhythm-section` | **groove 管节奏本身是什么，rhythm-section 管怎么把它打出来像人** |
| `mc-texture-layering` ⇄ `mc-mix-intent` | **texture 的解法是"改写"（换音区/减音符/交替），mix-intent 的解法是"改混"（让频/侧链/动态）。能改写解决的不许留给混音** |
| `mc-orchestration` ⇄ `mc-sound-design` | **orchestration 管真实乐器与混合，sound-design 管合成音色的设计** |
| `mc-sound-design` ⇄ 库三 | 本库的 sound-design 是**乐音音色**；影视意义的"声音设计"（音效/拟音/空间）是**另一个库**，同名不同事 |
| 本库 ⇄ 作词库 | **词的一切归作词库**，包括词曲对齐（符割り、字余り、倒字、协音）——那是词的工序 |

### 2.4 ★ 风格混合：不是调匀，是分维度签字

> ★ **边界是分工，不是禁令。** 隔离的目的是让每个选择都有人签字，不是把风格锁死。写 J-Pop 规格时顺手借一个 city pop 的裏和弦、写中国风时低音走了 hip-hop 的 808——只要在 `style_layers` / `fusion_note` 里写一句为什么，就是合法的混合；**轻微的串音完全可以接受**，真正要防的是「没人选、默认长成那样」。

**污染隔离 ≠ 禁止混合。** 两者的区别只有一条：

> **混合是写下来的选择；污染是没人选。**
> 一首 rap + 中国风的歌（如汪苏泷《桃花扇》）完全成立——前提是规格单说得出**哪个维度归谁**。

**做法**：`meta.style_layers` 填一张分工表（schema §1.1.1），一个 `base` + 若干 `overlay`，
每层写明它 `owns` 哪些维度：

| 维度 | 《桃花扇》式的分法（示意） |
|---|---|
| `harmony`、`melody`、`color_instruments` | 中国风（五声旋律、民乐采样做色彩层） |
| （完整分工见 `mc-style-hiphop` §8.1） | |
| `groove`、`low_end`、`vocal_delivery_verse` | hip-hop（律动、808、主歌 flow） |
| `vocal_delivery_chorus`、`form` | 中国风副歌唱、结构仍是主副歌 |

★ `owns` 是**封闭集合**，只能从这十四个里选：
`harmony`　`melody`　`groove`　`low_end`　`fill_policy`　`instrumentation`　`color_instruments`　`timbre`　`arrangement_hook`　`vocal_delivery`（可拆成 `vocal_delivery_verse` / `vocal_delivery_chorus`）　`form`　`mix`。
要分配的东西不在表里，归到最接近的那个，并在 `fusion_note` 里写一句说明，**不要自造名字**。

**三条规则**（第 18 条自查）：
1. **每个维度只有一个 owner**——两层都声称 `groove` = 两种默认值叠在一起，谁也没选
2. **每层至少拥有一个维度**——否则它只是被读了，没起作用
3. **`fusion_note` 一句话说这个组合为什么成立**——说不出就是硬凑

**怎么用各 L3 的移植表**：overlay 搬进来的，正是它移植表里标"不可搬／会被认出来"的项——
**那就是你要的辨识度**。但每搬一项都要在 `owns` 里签字，并看 base 风格的移植表是否允许让出那个维度
（例如 city pop 的移植表说"抢拍贝司可搬"，那 base 是 city pop 时 `groove` 让给别人就要三思）。

**路由上**：混合 brief 应**读全部被点名的 L3 skill**，这不算污染；
读了**没被点名**的风格才是。测试集从 v3 起区分这两种情况。

### 2.3 一律出库的东西

| 内容 | 去哪 |
|---|---|
| EQ 频点、压缩比、混响参数、总线链、母带 | **库三：声音设计与混音库** |
| 影视音效、Foley、环境声、空间设计 | **库三** |
| 跟画面走的配乐结构、cue sheet、spotting | **库二：配乐库** |
| 歌词写作、押韵、意象、叙事人称 | **作词库 `lyric-writing`**。★ **词的三种情况**：用户**没提词** → 可出可不出，出了就在 `checks.exemptions` 记一条"词未走作词库工序"；用户明确要**纯音乐 / 轻音乐 / 器乐** → 一个字都不加，`vocal` 留空；用户**明确要词** → 必须出词，作词库在场就走它，不在场就按本库的 `mc-vocal-direction` 写并记豁免。只示意人声节奏（rap 的 flow、副歌字数）时用占位音节（da-da / 拉拉）或 X 标记 |
| 商业流程（compe、仮歌、版权、分成） | 不做 |

---

## 3. ARR-SPEC：本库唯一的交接物

**完整规范在本 skill 目录下的 [ARR-SPEC.schema.md](ARR-SPEC.schema.md)（v1.0），空模板 [ARR-SPEC.template.yaml](ARR-SPEC.template.yaml)，后端能力档案 [backends.yaml](backends.yaml)。**
★ **写规格单前先 Read 模板，照它的字段名填**——实测不看模板的话 agent 会自己发明字段（`intent.scene/borrow`、`push_pull` 写成字符串）。
（这三个文件就是本库随包交付的全部"格式"——除了 Read 它们，不需要任何工具。）

### 3.1 一物四用

| 用途 | 怎么用 |
|---|---|
| **对外交接** | 给混音师/乐手/别的库看的，是这一份 |
| **AB 测试对象** | 改一个字段 → 重生成 → 比对。**变量可控** |
| **编译源** | `mc-render-compile` 把它编译成 Suno prompt / YuE 输入 / MIDI |
| **验收基准** | 生成回来的东西逐字段对照它，看后端照做了多少 |

★ 第二和第四条是关键：**没有规格单就没有可控实验，没有可控实验就只能靠感觉。**

### 3.0 ★ 写完的 20 条自查

**本库不需要用户机器上有任何工具**——没有 lint、没有脚本、不预设 Python 或 Node。
写完规格单，自己把下面 20 条逐条过一遍，结果写进 `checks.self_audit`。
★ **这是判据，不是铁律**：某条不过，要么改，要么一句话写明为什么这首歌就该这样。
写了理由的偏离就是合法的选择；**没有任何一条会"卡住"后面的编译**。
★ 不要为了检查去写脚本（实测有 agent 这么干过，白费很多轮）——20 条全部用眼睛就能判。

| # | 自查问法 | 对照字段 |
|---|---|---|
| 1 | `intent` 四项都填了？`reference_pair` 恰好 2 项且每项写了"借哪一点"？ | `intent` |
| 2 | 各段 `start_bar` 首尾相接？（上一段 start + bars = 下一段 start） | `form` |
| 3 | 至少一段的 `bars` 不是 8 的倍数？ | `form[].bars` |
| 4 | `energy_curve` 的 key 集合 == `form` 的 id 集合？（不多不少） | `energy_curve` |
| 5 | 能量曲线至少下降一次？ | `energy_curve` |
| 6 | `subtraction_events` ≥ 1？ | `subtraction_events` |
| 7 | 每件乐器有 `entry`；至少一件 `exit` 不是 `end`？ | `roster` |
| 8 | `arrangement_hook.what` 非空？ | `hooks` |
| 9 | 至少一个非 Intro/Outro 段有调外和弦或转调标记？ | `form[].harmony` |
| 10 | `fill_policy.bars` 不是公差 4 的等差数列？ | `groove` |
| 11 | `push_pull` 至少一项 `offset_ms ≠ 0`，且每项是 `{part, offset_ms, note}` 字典？ | `groove` |
| 12 | `width_map` 至少两种取值？ | `mix_intent` |
| 13 | `style_prompt` 非空且无 epic/emotional/beautiful；`section_tags` 若手填则与 `form` 段数、顺序一致（由 `form[].name` 派生，可不填）？ | `render.suno` |
| 14 | `Σbars × 每小节拍数 ÷ tempo × 60` 与 `target_duration` 偏差 ≤ 10%？ | `meta` |
| 15 | 有人声时 `dynamics_by_section` 至少两种取值？（器乐作品跳过） | `vocal` |
| 16 | 有人声时 `chorus_variation` 非空？（器乐作品跳过） | `vocal` |
| 17 | 多风格时：恰好一个 base；每个维度只有一个 owner；每层至少一个维度；`fusion_note` 非空？ | `meta.style_layers` |
| 18 | `material.motif` 与 `stated_in` 非空；每段 `development` 填了；`new` 不过半；至少一段 `repeat` 或 `recap`；副歌各遍不全是 `new`？ | `material` `form[].development` |
| 19 | 和弦拼写全 ASCII，后缀在封闭集合里（`maj7` `m7b5` `dim`，不写 △ ø ° ♭ ♯ ×）？ | `form[].harmony_letters` `harmony` |
| 20 | `key` 是"主音 major/minor"；`mode` 在枚举内；段名在封闭集合内；`subtraction_events[].at` 是 `{section, bar_offset}` 这样的结构不是句子？ | `meta` `form[].name` `subtraction_events` |

**YAML 本身的自查**（agent 最常写坏的地方）：
- 列表字段（`roster` / `sound_stage` / `push_pull` / `emotional_arc` …）的 `- {…}` 条目下面**不能挂 `note:` 键**——要注释用同级 `xxx_note:` 或 `#`
- 冒号后有空格；值里有冒号的加引号；缩进统一两格

### 3.2 自查不过怎么办

**不过的条目通常指向一个没读的 skill**，而不是需要硬填：

| 不过的条目 | 多半是哪个 skill 没读 |
|---|---|
| 1（intent 不齐） | 本 skill §1 的 S0——**回去想清楚，不要硬填** |
| 2、3、14（段落与时长） | `mc-arrangement-arch` §4 |
| 4、5（能量曲线） | `mc-arrangement-arch` §2 |
| 6（减法事件） | `mc-arrangement-arch` §3 |
| 7（编制进退场） | `mc-arrangement-arch` §5 |
| 8（arrangement_hook） | `mc-arrangement-arch` §7 |
| 9（和声单调） | `mc-harmony`、`mc-modulation` |
| 10、11（fill 与律动） | `mc-rhythm-section` §3、§8 |
| 12（宽度恒定） | `mc-mix-intent` §2 |
| 13（编译不完整） | `mc-render-compile` |
| 15、16（人声） | `mc-vocal-direction` §1、§4 |

**豁免的规矩**：写进 `checks.exemptions`，**必须带理由**。
静默跳过 = 没写完。

### 3.3 ★ 生成后端的默认值对照表

**后端在无指令时会滑向左列。ARR-SPEC 的对应字段就是为了把它拉回来。**

| 后端默认倾向 | 对抗字段 | lint |
|---|---|---|
| 段落等长、都是 8 的倍数 | `form[].bars` | #3 |
| 能量单调上升到底 | `energy_curve` | #5 |
| 开场就把乐器铺满 | `roster[].entry` | — |
| 所有乐器从头响到尾 | `roster[].exit` | #7 |
| 只加不减 | `subtraction_events` | #6 |
| 每 4 小节一个过门 | `fill_policy.bars` | #10 |
| 全声部严格对齐网格 | `groove.push_pull` | #11 |
| 人声全程一个力度 | `vocal.dynamics_by_section` | #15 |
| 副歌逐遍原样复制 | `vocal.chorus_variation` | #16 |
| 人声音准全中 | `vocal.pitch_policy` | 人工项 |
| 立体声宽度全曲不变 | `mix_intent.width_map` | #12 |
| 动态被压平 | `mix_intent.dynamics` | 人工项 |
| 没有非人声记忆点 | `hooks.arrangement_hook` | #8 |
| 时长永远 3:30 | `meta.target_duration` | #14 |

---

## 4. 诊断路径：生成回来不对劲

**按这个顺序查，不要凭直觉跳。**

```
听着"像 AI"
 └─ 先分开两个问题：后端照做了没有（照做率）、成品有没有机器指纹（AI 味旗标）
     ├─ 照做率低  → 后端没照做
     │   ├─ 查 backends.yaml：这个字段该后端 honors 是 none 吗？
     │   │   └─ 是 → 不是它的错。换后端（YuE2 控制力最强）或接受
     │   └─ 否 → 编译问题，查 mc-render-compile
     └─ 照做率高但仍像 AI → 规格本身不够
         └─ 走 mc-ai-tell-audit 的清单
```

### 4.1 具体症状 → skill

| 症状 | 先查 |
|---|---|
| 平、没起伏 | `mc-arrangement-arch` §2 能量曲线 |
| 糊、浑 | `mc-texture-layering` §4 → **先查持续音，不是先怪鼓** |
| 主角不突出 | `mc-texture-layering` §3 四条深度线索 |
| 机械、太准 | `mc-rhythm-section` §2 ＋ `mc-vocal-direction` §4 |
| 听着还行但记不住 | `mc-arrangement-arch` §7 —— `arrangement_hook` 是不是空的 |
| 第二遍副歌和第一遍一样 | `mc-arrangement-arch` §6 ＋ `mc-vocal-direction` §1④ |
| 人声埋在伴奏里 | **先查音准**（`mc-vocal-direction` §4.1），再查频段 |
| 贝斯在手机上消失 | `mc-sound-design` §6 差音 |
| 结尾很突兀 / 淡出了事 | `mc-arrangement-arch` §8 三种结尾 |
| 技术上干净但情绪不对 | `mc-mix-intent` §3.1 —— `mood` 是不是没写 |

### 4.2 ★ 两个数不许混为一谈

| | 问什么 | 怎么算 |
|---|---|---|
| **照做率** | 后端照着规格做了多少 | 逐字段对照规格听/看；仓库内部的度量工具能算成 0–100，不随包发布 |
| **AI 味旗标** | 成品有没有机器指纹 | `mc-ai-tell-audit` 的清单，独立计数 |

**照做率 100 的东西完全可能满是 AI 味**——那说明规格写得像 AI。
反过来，照做率 40 但好听，说明后端自己干得不错，但你**不可复现**。

---

## 5. 跨库接口

| 对方 | 交接物 | 方向 |
|---|---|---|
| **作词库** | LYR-SPEC ⇄ ARR-SPEC | **双向**：先曲后词走 ARR→LYR，先词后曲走 LYR→ARR。共享 `form[]` 的段落 id 与小节数 |
| **配乐库**（库二） | ARR-SPEC + 时间码表 | 单向：配乐要跟画面，段落由 spotting 决定而非曲式 |
| **声音设计与混音库**（库三） | `mix_intent` 段 | 单向：我们只给意图，参数归他们 |
| **影视线** | 成品音频 + stems | 单向 |

### 5.1 与作词库共享的字段（必须一致）

`form[].id` / `form[].bars` / `form[].name` / `meta.tempo` / `meta.key` / `vocal.persona`

**任何一边改了这六项，另一边必须同步。** 这是双向耦合唯一的代价。

---

## 6. 三个测试装置

### 6.1 路由／污染测试（回归测试）

本库用 20 条典型请求做回归（仓库内，不随包发布），**每加一个 skill 就重跑一次**，看：

1. **路由正确率**：每条 brief 该读的 skill 有没有被选中
2. **污染率**：不该被选中的 skill 有没有混进来
   —— 尤其是 L3 风格层，**加一个风格 skill 最容易污染别的风格**

> 这就是为什么每个 skill 都必须有「边界：什么不归这个 skill 管」表——
> 那张表是给路由看的，不只是给人看的。

### 6.2 文本层 AB

**评判表在看结果之前定稿。** 改一个字段 → 重生成 → 盲比。
定稿之后再看结果，否则你会不自觉地把评判标准调向你已经看到的那个。

比响度前先对齐电平，再比内容：声音只要更响，耳朵就倾向觉得它更好，这条偏差会把"哪个字段起了作用"的判断，悄悄换成"哪个版本更响"（p0299-p0300）。两版生成结果如果响度或整体密度对不齐，先拉平这两项再做成对比较，不然字段本身的效果会被响度差盖过去。

### 6.3 先算再听（照做率）

见 §1 的 S7 与 §4.2。

★ **零点校准**（仓库实验数字，供理解量级，不是交付门槛）：不相关的、制作精良的成品在度量工具上得 **63–66**，
所以我们把 **82 / 70** 当"照做得好 / 勉强"的参考线，不是 60。
用户侧没有度量工具时，照做率就是逐字段对照后的一个判断，不必算成数。

---

## 7. 目录地图

```
随包发布（用户拿到的就是这些，全是文本）：
  skills/<27 个 skill>/SKILL.md, reference.md
  skills/mc-workflow/ARR-SPEC.schema.md      ★ 规格单定义（v1.0）
  skills/mc-workflow/ARR-SPEC.template.yaml  空模板
  skills/mc-workflow/backends.yaml           ★ 各后端的 honors 声明
  skills/mc-workflow/example-*.yaml          填好的示例（citypop / 混合 / 反面样本）

仓库内、不随包发布（skill 里提到它们只是标注来源）：
  本库架构文档、读书笔记（40+ 本）、实验记录与实测语料、度量与实验工具
```

---

## 8. 写完一首歌的自检

- [ ] `intent` 四项是**想出来的**，不是为了过 lint 填的
- [ ] §3.0 的 20 条逐条过了，不过的都写了理由
- [ ] 每个字段都能说出**是哪个 skill 的哪一节**决定的
- [ ] 逐字段对照过生成结果，且知道哪些项因 `honors: none` 本来就不指望
- [ ] 跑过 `mc-ai-tell-audit`
- [ ] 耳朵是**最后**介入的，且 A/B 结论两遍一致

---

## 附：这个库为什么这样分层

**分库/分 skill 的判据是四件事全不同**：交接物、裁判、语料、评判方法。
**按工序切，不按知识领域切。**

所以：
- "和声"是**知识领域**，但它被切进 `mc-harmony`（写的时候）和 `mc-progressions`（查的时候）——
  因为这是两道工序
- "city pop"是**风格**，它的鼓型不进 `mc-rhythm-section`，进 `mc-style-citypop-rnb`——
  因为"打得像人"和"打得像 city pop"是两道工序
- "混音"横跨三层：意图在 `mc-mix-intent`，改写型解法在 `mc-texture-layering`，
  参数在**库三**——因为它们的裁判不同（作曲判断 / 编曲判断 / 工程判断）

详见 本库架构文档。

## 附：来源

- **Howard & Angus《音乐声学与心理声学》**（Acoustics and Psychoacoustics）——§1.2 两遍复听的两条心理声学依据：重复听音会改变听觉系统的处理策略、以及持续暴露带来的听觉适应。页码为中译本页。
- **Roey Izhaki《Mixing Audio》**（混音指南）——§6.2 提醒的响度偏差：更响的版本容易被判定"更好"，与内容本身的差异无关。页码为该书页。
本库其余各 skill 引用的书目见各自的「附：来源」。
