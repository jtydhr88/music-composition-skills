---
name: mc-render-compile
description: The compiler from ARR-SPEC to any generation backend (编译层). One compiler, many backends - the backend differences live in backends.yaml as data, never as separate skills. Covers the compile order, section-tag mapping, turning structured fields into prose style prompts without losing information, lyric chunking, the honors contract that decides which fields are scorable, degradation strategy when a backend cannot execute a field, seeds and best-of-N, and what to hand over so the user can run it in whatever tool hosts the backend. Use when turning a finished ARR-SPEC into Suno / YuE2 / MiniMax / ACE-Step / symbolic output, when choosing a backend, when a generation ignored the spec, or when adding a new backend. 编译、后端、Suno、YuE2、MiniMax、ACE-Step、提示词生成、段落标签、honors。
---

# 编译层（Render Compile）

**本库只有一个编译器。后端是数据，不是 skill。**

```
ARR-SPEC（唯一 IR）
    ↓  mc-render-compile   ← 只有这一个，管共性
    ├─→ backends.yaml: yue2      ★ 可直接吃 ABC 乐谱，控制力最强
    ├─→ backends.yaml: suno         散文 prompt ＋ 段落标签，成品最润
    ├─→ backends.yaml: minimax-music-3
    ├─→ backends.yaml: ace-step-1.5
    ├─→ backends.yaml: stable-audio-3   （无人声）
    └─→ backends.yaml: symbolic         MIDI/MusicXML，完全确定
```

> **加一个新模型 = 在 `backends.yaml` 加一个块，不动任何 skill。**
> 这是编译器前端/后端分离那一套。

## 按任务读哪几节

| 任务 | 读 |
|---|---|
| 选哪个后端 | §2 能力对照 |
| 编译一份规格 | §3 编译顺序（**按顺序做，不要跳**） |
| 写 style_prompt | §4 |
| 段落标签对不上 | §5 |
| 后端不理我的规格 | §6 honors 契约 ＋ §7 降级 |
| 要出 ABC/MIDI | `mc-symbolic-score` |
| 把编译结果交给后端执行 | §8 |
| 加一个新后端 | §9 |
| 生成完了 | `mc-ai-tell-audit` |

## 边界

| 不归这里 | 归哪 |
|---|---|
| ARR-SPEC 里的内容怎么想出来 | L1／L2 各 skill |
| ABC / MusicXML / MIDI 怎么生成 | `mc-symbolic-score` |
| 生成回来像不像 AI | `mc-ai-tell-audit` |
| 照做率怎么算 | 定义在 `mc-ai-tell-audit` §3.1；本 skill §6 只管 honors 三档怎么影响计分 |
| 具体某个后端的权重放哪、节点叫什么 | [`backends.yaml`](../mc-workflow/backends.yaml)（包内副本）（**数据**） |
| 风格描述里该写什么风格词 | `mc-style-*`（L3） |

---

## 1. 动笔前必填

| 必填 | 问法 |
|---|---|
| **① 目标后端** | 至少一个。**多个就要做 §7 的降级表** |
| **② 这次要验证什么** | 是出成品，还是跑 AB？**AB 就必须锁定其他变量** |
| **③ 种子策略** | 固定种子做对照，还是 best-of-N 挑 |
| **④ 哪些字段这个后端做不到** | 查 `honors`。**这决定你别在那些字段上浪费提示词预算** |

---

## 2. 后端能力对照

| | **YuE2** | Suno | MiniMax | ACE-Step | Stable Audio | 符号 |
|---|---|---|---|---|---|---|
| 输入 | 风格 + 词 + **可选 ABC** | 散文 + 段落标签 | 散文 + 词 + 参考 | 标签 + 散文 | 散文 | 乐谱 |
| **调性/和声** | ★ **精确**（ABC） | 只能提示 | 提示 | 提示 | — | 精确 |
| **曲式/小节数** | ★ **精确** | **说不动** | 提示 | 提示 | — | 精确 |
| 人声 | 有 | 有 | 有 | 有 | **无** | **无** |
| 可复现 | seed + best-of-8 | 弱 | — | seed | seed | **完全** |
| 音色成品度 | 高 | **最高** | 高 | 中 | 高 | 看音源 |
| 商用 | ★ **权重 CC-BY-NC**，不可商用 | 看订阅档 | 待核 | 看许可 | 看许可 | 可 |

**选择建议**：

| 目的 | 选 |
|---|---|
| **验证规格是否被执行** | **YuE2**（唯一能"执行"而非"提示"的） |
| **要成品质感** | **Suno** |
| **要完全可复现的对照基准** | **符号路径** |
| **纯器乐/氛围** | Stable Audio |
| **出街商用** | ★ **先解决许可**——YuE2 权重是 CC-BY-NC-4.0，不可商用 |

---

## 3. ★ 编译顺序

**按顺序做。跳步会产生"提示词里互相矛盾"的问题。**

| # | 步骤 | 说明 |
|---|---|---|
| **0** | **过一遍 `mc-workflow` §3.0 的 20 条自查** | 不过的条目改掉或写明理由再编译——**不是准入门槛**，是最后一次发现"规格没写完"的机会 |
| **1** | **读 `backends.yaml` 的 `honors`** | 标出 `none` 的字段——**它们不进提示词，也不计分** |
| **2** | **`mix_intent.mood` 放最前** | 它对其余决定有否决权，必须在提示词靠前的位置 |
| **3** | **编译 style_prompt** | §4 |
| **4** | **编译段落标签序列** | §5。★ **数量必须等于 `form` 段落数**（lint #13） |
| **5** | **编译歌词分块** | §5.3 |
| **6** | **符号路径**（若后端支持） | → `mc-symbolic-score` |
| **7** | **编译 exclude / 负面提示** | §4.4 |
| **8** | **定种子与批量** | §7.3 |
| **9** | **写回 `render.<backend>`** | ARR-SPEC 自己记录这次编译的结果 |

★ **第 9 步别漏**：`render` 段是规格的一部分，
**没写回去的话，下一轮 AB 就不知道上次到底送了什么进去。**

---

## 4. style_prompt：把结构化字段压成散文而不丢信息

### 4.1 信息来源的优先顺序

**从下面这些字段抽取，按这个顺序排列**（越靠前越容易被模型采纳）：

1. `mix_intent.mood`（一句话）
2. `meta.style_layer` 对应的风格词（→ L3 skill）
3. `meta.tempo`、`meta.key`
4. `arrangement.roster[].instrument` ＋ `timbre`
5. `groove.feel` ＋ push_pull 的**方向**（不写毫秒，写 "bass playing ahead of the beat"）
6. `vocal.persona` ＋ `vocal.delivery`
7. 年代/制作质感词

### 4.2 ★ 三条硬规则

1. **禁用无信息形容词**：`epic` / `emotional` / `beautiful` / `amazing` / `masterpiece` / `perfect`
   （lint #13 软警告）。它们不携带任何可执行信息，只占位置
2. **数值要转成可听的描述**：
   `push_pull: bass −12ms` → `bass playing slightly ahead of the beat`（**不要写 −12ms**）
3. **每个词都要能指向一个 roster 项或一个 spec 字段**。
   ★ **自检动作**：把 style_prompt 逐词圈一遍，圈不出来源的词删掉

### 4.3 一个实测样本（City Pop）

```
1980s Japanese city pop, 112 BPM, D major, Rhodes electric piano,
16th-note muted clean guitar, electric bass playing ahead of the beat,
gated reverb snare, analog saw synth lead riff,
tenor saxophone solo in the bridge and outro,
female vocal with breathy tone mixed back, warm tape saturation
```

**注意它的构造**：年代+风格 → 速度调性 → **逐件乐器带演奏法** → 人声 → 制作质感。
**没有一个形容词是空的。**

### 4.4 exclude（负面提示）

**从"这首歌不是什么"来写**，不是从"不要难听"来写。

实测样本：
```
["EDM drop", "trap hi-hats", "modern sidechain pumping",
 "orchestral epic", "fade out ending"]
```

★ 最后一项 `fade out ending` 值得注意——
**淡出是生成模型的默认结尾**，不排除它就一定会得到淡出
（→ `mc-arrangement-arch` §8 三种结尾）。

---

## 5. 段落标签与歌词

### 5.1 映射表

`form[].name` → 后端标签：

| ARR-SPEC | Suno / 多数后端 |
|---|---|
| Intro | `[Intro]` |
| Verse | `[Verse]` |
| Pre-Chorus | `[Pre-Chorus]` |
| Chorus | `[Chorus]` |
| **Bridge** | `[Bridge]` |
| Instrumental | `[Instrumental]` |
| Outro | `[Outro]` |

★ **两个映射陷阱**：
1. **大サビ 映射成 `[Bridge]`，不是 `[Chorus]`**（→ `mc-form` §7）。
   映射错了，后端会在内省的位置给你一个高能量副歌
2. **落ちサビ（稀薄编制的副歌）仍然映射成 `[Chorus]`**，
   稀薄的要求写进 style_prompt 或段落注释，**不要靠改标签来表达**

### 5.2 数量必须相等

这条规则的完整表述见 §3 步骤 4（数量必须等于 `form` 段落数，lint #13），§11 必过清单里也有一遍。单独留一节，是因为它是编译时最容易出错的一处：手动改动歌词段落之后，标签序列很容易忘记同步增删，少一个后端就会自己决定那一段是什么，出错时优先查这里。

### 5.3 歌词分块

- 歌词按段落切开，**与标签一一对应**
- ★ 段落内的换行**会影响断句**——它不只是排版，后端会当成乐句边界读
- 与**作词库**的接口：`LYR-SPEC` 的段落 id 必须与 `form[].id` 一致（→ `mc-workflow` §5.1）

---

## 6. ★ honors 契约：本 skill 最重要的一条

每个后端在 `backends.yaml` 里声明它对 ARR-SPEC 各字段的执行能力：

| 取值 | 含义 | 验收时怎么算 |
|---|---|---|
| **exact** | 能精确执行 | **计分**，不达标就是它没做到 |
| **hint** | 只能作为提示影响生成 | **计分，但权重打折** |
| **none** | 根本不接受这个字段 | ★ **排除计分**，标注"本后端不支持" |

### 6.1 为什么 none 必须排除而不是给 0

> **Suno 从来没被告知精确和弦，拿"和弦没跟上"扣它的分等于惩罚它做不到的事。**

★ **更要命的后果**：如果 none 给 0 分，那么
**一个诚实声明"我做不到声像"的后端，会比一个假装做得到的后端得分更低**——
评分体系会奖励谎报能力。**这是一个正确性 bug，不是口径问题。**

### 6.2 编译时怎么用

1. `none` 的字段**不进提示词**——写了也没用，还挤占了有用信息的位置
2. `hint` 的字段**要写，但别指望**——配合 best-of-N
3. `exact` 的字段**必须写准**，这是你花钱买控制力的地方

---

## 7. 降级策略

### 7.1 一份规格，多个后端

**不要为每个后端各写一份规格。** 写一份，按 honors 降级：

| 字段 | YuE2 (exact) | Suno (none) | 怎么降级 |
|---|---|---|---|
| `form.harmony` | 编译成 ABC | — | **降级为风格描述里的调式/色彩词**（"含大量下属小调色彩"） |
| `form.bars` | 编译成 ABC 小节 | — | **降级为段落标签序列的长度暗示**，并接受它做不到 |
| `groove.push_pull` | — | hint | 降级为 "playing ahead of the beat" |
| `mix_intent` | none | none | ★ **两边都做不到 → 走后期，或走符号路径** |

### 7.2 降级要记录

记录格式见 §10（写进 `checks.exemptions`），交付清单见 §8.1（写回 `render.<backend>`）。这里要说清楚的是动机：★ 在 `render.<backend>` 里记下这次降了哪些字段，否则下一轮看到照做率低，会分不清是规格写错了，还是后端本来就做不到。降级造成的低分和规格错误造成的低分，在照做率上表现得一样，只有当时编译的人知道区别，所以必须当场记下来。

### 7.3 种子与批量

| 目的 | 做法 |
|---|---|
| **AB 对照** | **固定种子**，只改一个字段。改两个就什么都证明不了 |
| **出成品** | **best-of-N**（YuE2 建议 8），按照做率排序（有度量工具就算，没有就逐字段对照），**再用耳朵在前 3 里挑** |
| **探索** | 放开种子，但要记录哪个种子出了什么 |

---

## 8. 交给后端执行

本 skill 的产出是**各后端的输入值**：Suno 的风格描述与段落标签、YuE2 的 ABC 与参数、
ACE-Step / MiniMax / Stable Audio 的 prompt 与参数。
★ **用什么工具把这些值送进模型，由用户的环境决定**——命令行、Python API，
还是 ComfyUI 这类节点式载体，本 skill 不预设、不管理。
`backends.yaml` 的 `transport` 只记录"这个后端通常跑在哪"，是数据，不是前提。

### 8.1 交付时给用户什么

| 后端 | 交出去的东西 |
|---|---|
| Suno | style_prompt、exclude、带段落标签的歌词或占位音节（§4、§5） |
| YuE2 | ABC 文本（`mc-symbolic-score`）＋ genre / tempo 参数；**并注明：把这份 ABC 直接喂给合成步骤，跳过模型自己的符号规划** |
| ACE-Step / MiniMax / Stable Audio | prompt 与时长等参数，按各自的 `accepts` 列表 |

每一项都写回 `render.<backend>`；用户拿着它到自己的工具里填就行。

### 8.2 YuE2 的关键接法

YuE2 分两步：**符号规划（模型自己写 ABC）→ 从 ABC 合成音频**。
★ **把第一步旁路掉**，把 `mc-symbolic-score` 编译出来的 ABC 直接送进第二步。
这是本库控制力的最高点——规格在这里不再是"提示"，而是被执行。
凡是支持 YuE2 的载体都能这样接（官方仓库有脚本，ComfyUI 有原生节点）；具体怎么接线是用户环境的事。

> YuE2 官方仓库自带 `skills/yue2-music/`（含"检查音乐不变量"）。
> ★ **不要重写它**——我们只写 ARR-SPEC→ABC 的适配器，模型操作的知识引用它们的。

---

## 9. 加一个新后端

1. 在 `backends.yaml` 加一个块：`id / name / transport / accepts / honors / license`
2. ★ **`honors` 要诚实**——宁可标 `none` 也不要标 `hint` 充数（理由见 §6.1）
3. 填 `transport`（hosted / local）——只是记录它通常跑在哪，不是要求
4. **跑一遍 `example-01-citypop.yaml`**，看编译产物是否合理
5. **不改任何 skill**

---

## 10. 与 ARR-SPEC 的字段对应

| 字段 | 本 skill |
|---|---|
| `render.suno` | §4、§5 |
| `render.yue` | §4、§5、§8.3 |
| `render.midi` | → `mc-symbolic-score` |
| `checks.exemptions` | ★ 降级记录也写这里 |

---

## 11. 编译完必过

- [ ] `mc-workflow` §3.0 的 20 条过了一遍，不过的写了理由
- [ ] 查过目标后端的 `honors`，`none` 的字段**没有进提示词**
- [ ] `mood` 在提示词**靠前**
- [ ] style_prompt 里**每个词都指向一个 spec 字段**
- [ ] **没有 `epic`/`emotional`/`beautiful` 这类空词**
- [ ] 数值参数**转成了可听的描述**，没有把毫秒写进提示词
- [ ] 段落标签**数量 == `form` 段落数**
- [ ] **大サビ 映射成了 `[Bridge]`**
- [ ] `exclude` 里**排除了淡出结尾**（除非确实要淡出）
- [ ] 种子策略明确；做 AB 的话**只改了一个变量**
- [ ] **降级记录写回了 `render.<backend>`**
- [ ] 生成后先逐字段对照规格（照做率），**再用耳朵**

---

## 附：来源

- 设计依据：本库架构文档 §2.3（编译器 ＋ 后端能力档案）
- 后端数据：[`backends.yaml`](../mc-workflow/backends.yaml)（包内副本）
- ⚠ **MiniMax Music 商业 API 已对新用户关闭**，见 backends.yaml 的档案
- ⚠ 未确认：YuE2 合成步骤接受的 ABC 方言；它的 musical invariants 与 20 条自查的对应关系
- **分轨**：人声 AI 味审计要先把人声轨分出来，任何分轨工具都行（demucs 一类，或用户载体自带的），见 `mc-ai-tell-audit` §6.1
