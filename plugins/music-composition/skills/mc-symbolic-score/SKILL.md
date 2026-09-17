---
name: mc-symbolic-score
description: Compiling ARR-SPEC into symbolic notation (符号乐谱输出) - ABC, MusicXML and MIDI from one emitter. Why the symbolic path matters (it is the only fully reproducible output and the only way YuE2 executes rather than guesses), the field-by-field mapping from spec to ABC headers and body, roman numerals to letter chords, multi-voice and per-part output, what each target format can and cannot carry, MIDI CC and velocity for expression, and the verification loop that catches a bad emit before it reaches a synth. Use when producing ABC for YuE2, when exporting MIDI or MusicXML for a DAW, when a deterministic reference render is needed for A/B, or when an LLM should write notation directly. 符号乐谱、ABC、MusicXML、MIDI、打ち込み、直出乐谱、确定性渲染。
---

# 符号乐谱输出（Symbolic Score）

**一个发射器，四个目标格式，两个用途。**

```
ARR-SPEC（+ LYR-SPEC 的歌词）
   ↓  mc-symbolic-score
   ├─→ ABC        → YuE2 的 abc= 输入（★ 让规格被"执行"而不是"提示"）；大模型的母语
   ├─→ MusicXML   → 记谱软件 / 交接给乐手（领奏谱：旋律、和弦、歌词、段落、力度、换气）
   ├─→ MIDI       → DAW ＋ 音源，确定性渲染
   └─→ ChordPro   → 吉他手、排练（段落、和弦、歌词）
```

**为什么是同一个发射器**：ABC ↔ MusicXML ↔ MIDI 可互转，ChordPro 是前三者的子集，
四者承载的是同一套信息（音高、时值、小节、调号、拍号、力度、和弦、歌词）。
**分成四个 skill 会让同一份规格产生四份不一致的乐谱。** 规格单可执行字段的拼写就是按这四种格式定的（schema §1.3.1、§4），发射器不需要翻译符号。

## 按任务读哪几节

| 任务 | 读 |
|---|---|
| 给 YuE2 供谱 | §3 ABC ＋ §7 |
| 出 MIDI 给 DAW | §5 |
| 出 MusicXML 交给乐手 | §4 |
| 要一个 AB 的确定性基准 | §2.2 |
| 级数转字母 | §3.3 |
| 表情/力度怎么写进 MIDI | §5.2 |
| 发射完怎么验 | §8 |

## 边界

| 不归这里 | 归哪 |
|---|---|
| 提示词、段落标签、后端选择 | `mc-render-compile` |
| 旋律/和声/对位的内容本身 | L1 各 skill |
| 用什么音源、采样库选型、keyswitch | 出库 → 制作工具库（不在本包）；MIDI CC 在配器上的含义归 `mc-orchestration` reference §5 |
| MIDI CC 在配器上的含义 | `mc-orchestration` reference §5 |
| 量化、velocity 的**律动**设计 | `mc-rhythm-section` |
| 记谱法的完整规范（连音线、装饰音写法） | [reference.md](reference.md) |

---

## 1. 动笔前必填

| 必填 | 问法 |
|---|---|
| **① 目标格式** | ABC / MusicXML / MIDI / ChordPro。**能承载的不一样**（§6）；`meta.deliverable` 里列了哪些就发哪些 |
| **② 声部数** | 只发主旋律+和弦？还是全谱？ |
| **③ 谁来读** | YuE2 / DAW / 人。**决定详细程度** |
| **④ 这次是不是做基准** | 是的话**所有随机性都要关掉** |

---

## 2. 为什么要走符号路径

### 2.1 它是唯一能让规格被"执行"的路

其余后端都只能"提示"。YuE2 的合成步骤**直接吃 ABC**——
`form[].harmony_letters` / `meta.key` / `meta.meter` / `meta.tempo`
编译成 ABC 之后，**它们就不再是建议了**。

> 把模型自己的符号规划那一步**整个旁路掉**，把我们的 ABC 直接送进合成步骤。
> 支持 YuE2 的载体都能这么接（见 `mc-render-compile` §8.2）；怎么接线是用户环境的事。

### 2.2 它是唯一完全可复现的输出

> **同一份规格渲染出来永远一样。**

这让它成为 **AB 实验的控制变量基准**：
想知道"改这一个字段到底带来多大差别"，用符号路径渲染两版，差别 100% 来自那个字段。
生成后端做不到这一点（即使固定种子，改提示词也会改变别的东西）。

### 2.3 日式打ち込み 工作流本来就是这个形态

LLM 直出 MIDI/MusicXML，进 DAW，配音源。
★ **这条路没有人声**，人声要另接（或走生成后端做人声、符号路径做伴奏）。

---

## 3. ABC 的映射

### 3.1 头部（Header）

| ABC 字段 | 来自 | 例 |
|---|---|---|
| `X:` | 固定 1 | `X:1` |
| `T:` | `meta.title` | `T:Night Drive` |
| `M:` | `meta.meter` | `M:4/4` |
| `L:` | 默认单位时值（通常 1/8 或 1/16） | `L:1/8` |
| `Q:` | `meta.tempo` | `Q:1/4=112` |
| `K:` | `meta.key` | `K:D` |

★ **`tempo_map` 有多项时**：`Q:` 取首项，后续变速写成小节内的 `[Q:...]` 内联域。

### 3.2 主体

| 内容 | 来自 | 写法 |
|---|---|---|
| 小节线 | `form[].bars` / `start_bar` | `|` |
| 段落标记 | `form[].id` + `name` | ★ **写成注释 `%` 或 `[P:A1]` 分段标记**，见 §7.2 |
| 和弦 | `form[].harmony_letters` | `"F#m7"` 放在音符前的引号里 |
| 旋律 | L1 写的旋律 | 标准 ABC 音高记号 |
| 反复 | 段落重复 | `|:` `:|`。★ **慎用**——见 §3.4 |

### 3.3 ★ 级数 → 字母

**ARR-SPEC 里 `harmony` 是级数、`harmony_letters` 是字母，两者双记。
发射 ABC 时用字母，但必须校验两者一致。**

**校验动作**：把 `harmony_letters` 按 `meta.key` 反推成级数，
与 `harmony` 逐和弦比对。**对不上就是规格里有笔误**——
这是 ARR-SPEC 双记号约定的实际用处，不只是为了可读。

移调表见 `mc-progressions` [reference.md](../mc-progressions/reference.md) §8。

### 3.4 ★ 反复记号的陷阱

ABC 的 `|: :|` 会让**两遍完全一样**。
但本库的 lint #16 要求**副歌逐遍有变化**——
**所以多数情况下不要用反复记号，要把每一遍展开写。**

★ **例外**：确实完全一样的段落（比如纯器乐的 vamp）可以用反复。
但**副歌永远展开写**。

---

## 4. MusicXML

**用途**：交给记谱软件、交给乐手。

**它比 ABC 多能承载的**：
- 完整的谱表布局、声部分配
- 演奏法记号（弓法、连音线、articulation）
- 力度记号（p / mf / f 及渐变）
- 歌词（逐音节对齐）

**它比 MIDI 多能承载的**：**记谱意图**（同一个音高时值，是升记号还是降记号、是三连音还是附点）。

★ **什么时候必须用它**：`mc-orchestration` 的产出要交给真人演奏时。
ABC 和 MIDI 都表达不了弓法和分谱。

---

## 5. MIDI

### 5.1 基本映射

| 内容 | 来自 |
|---|---|
| 音符 | 旋律与和声 |
| 轨道/通道 | `arrangement.roster[]`，一件一轨 |
| 音源映射 | `roster[].midi`（`{program, bank, channel}`，GM 号，鼓组 channel 10）；`render.midi.instrument_map` 只放 VST 名等备注 |
| 速度 | `meta.tempo` / `tempo_map` |

### 5.2 ★ 表情：velocity 与 CC

**MIDI 相对 ABC 的最大优势是能写连续控制。**

| 来自 ARR-SPEC | 写成 |
|---|---|
| `arrangement.energy_curve` | **CC11（表情）曲线** ＋ velocity 层次 |
| `vocal.dynamics_by_section` | 人声轨的 CC11（若有人声音源） |
| `groove.push_pull` | ★ **音符位置的实际偏移**（这是唯一能精确执行 push_pull 的路径） |
| `groove.quantize_policy` | 逐轨的量化程度 |
| `roster[].synth_params` | 音源参数或 CC74（亮度）等 |

★ **CC7 与 CC11 的分工不能混**（→ `mc-orchestration` reference §5.1）：
- **CC7** 定这一轨在配器里的**位置**
- **CC11** 定**这一句怎么呼吸**

混用的结果是"推了推子但乐句还是死的"。

### 5.3 ★ 符号路径上 push_pull 是 exact

`backends.yaml` 里 symbolic 后端的 `groove` 相关项是 **exact**——
因为音符位置可以精确写。
**这是验证"push_pull 到底有没有用"的唯一可靠手段**：
同一份谱，一版对齐网格、一版按 push_pull 偏移，直接对比。

---

## 6. 四种格式能承载什么

| | ABC | MusicXML | MIDI | ChordPro |
|---|---|---|---|---|
| 音高/时值/小节 | ✅ | ✅ | ✅ | ❌ 只有段落 |
| 调号/拍号/速度 | ✅ | ✅ | ✅ | ✅ 头部指令 |
| 和弦符号 | ✅ | ✅ | ⚠ 只能作为文本事件 | ✅ |
| 歌词 | ✅ 基本 | ✅ 完整 | ⚠ 只能作为文本事件 | ✅ |
| **记谱意图**（升/降、三连音写法） | 部分 | ✅ | ❌ | ❌ |
| **演奏法记号**（弓法、articulation） | ❌ | ✅ | ⚠ 只能靠 CC/keyswitch 近似 | ❌ |
| **连续控制**（CC 曲线） | ❌ | ❌ | ✅ | ❌ |
| **微时间偏移**（push_pull） | ❌ | ❌ | ✅ | ❌ |
| **YuE2 直接吃** | ✅ | ❌ | ❌ | ❌ |

★ **结论**：
- **要 YuE2 执行 → ABC**
- **要交给人 → MusicXML**
- **要表情与律动 → MIDI**
- **要给吉他手和排练 → ChordPro**
- **几个都要就几个都发**，但**从同一份规格发**，不要各写各的

---

## 6b. YuE2 方言：实测出来的四条（2026-09-17）

同一份从 ARR-SPEC 编译的谱，三种写法送进 YuE2 的 abc 口，结果差得远：`L:1/16` 单声部出 2:18（谱面 4:00，模型提前停），删掉段落注释更短（1:58），**改成 `L:1/32` 跑满 3:56**。所以给 YuE2 的 ABC 照它自己写谱的样子来：

1. **`L:1/32`**：所有时值按三十二分音符计，四分音符写 8、附点二分写 24、整小节休止 `z32`。用 `L:1/16` 它会把谱当成一半长，提前结束。
   ⚠ 但只改这一条不够：同一份谱改成 `L:1/32` 后时长跑满，whisper 却一个字都识别不出（直出版 76%，模型自写谱 65%，这版 2%）。原因在旋律本身：每个字的时值太长、音太少、音区比模型自写的谱低一个八度。给 YuE2 的人声线要像它自己写的那样：小写音名区（c 到 a 一带）、每小节四到六个音、单字很少超过一拍。实测：同一份谱整体升八度到小写音名区，whisper 识别率从 2% 到 47%；再加上 w: 歌词行到 56%（直出是 76%）。这就是第 5、6 条的来历：
2. 带 `w:` 歌词行（见第 6 条）。
3. **声部头写 `V: Vocal clef=treble name="Vocal Melody" snm="Vocal"`**，段落用 `% intro`／`% verse`／`% pre-chorus`／`% chorus`／`% bridge`／`% outro` 注释；它自写的谱还有一条 `V: Ins` 声部放器乐句，单声部也能跑满。
4. 标题行 `T:` 可空；调用 `K:D` 这类标准写法，模型自写时倾向关系小调（同一首它写成 `K:Dm`），不影响。

5. **人声线写在小写音名区**（ABC 的 c 到 a，即 C5 到 A5 一带），大写区它会唱成含混的元音；这与真人音域无关，是模型的习惯。
6. **带 `w:` 歌词行**（每个音符一个字，字数与音符数逐行相等）比只在 lyrics 口给词识别率高；w: 行照带，lyrics 口也照给。
7. 待做：旋律密度。编译器目前给每个字配长音（附点二分、全音符），模型自写谱每小节四到六个音、单字很少超过一拍；识别率剩下的差距多半在这里。

ABC 表达不了的规格字段（减法事件、残响切断、逐件进退场）只能靠 style 文本，见 `mc-render-compile`。

## 7. 给 YuE2 供谱的实操

### 7.1 接法

```
[我们的 ABC]  ──→  YuE2GenerateMusic  ──→  音频
                        ↑
   （YuE2GenerateABC 被旁路掉）
```

本 skill 只管左边那一格：把 ARR-SPEC 编译成 ABC 文本。
右边这条线为什么要接、接了之后规格为何不再只是"提示"、
用什么载体（官方脚本、ComfyUI 节点）接线，见 `mc-render-compile` §8.2。

### 7.2 方言已经摸清

`YuE2GenerateMusic` 的 abc 输入端吃什么，§6b 已按实测写死：段落用 `%` 注释，和弦用引号里的标准拼写（`"F#m7"`），`V: Vocal` 加可选的 `V: Ins` 两个声部都能跑。仍不确定的东西只剩旋律密度那一刀（§6b 第 7 条）。

### 7.3 上游有官方 skill

YuE2 官方仓库自带 `skills/yue2-music/`（SKILL.md 格式，含"检查音乐不变量"）。

> ★ **不要重写它。** 我们写 ARR-SPEC→ABC 的适配器，模型操作的知识引用它们的。
> 它的 musical invariants 应该与我们的 20 条自查 **对齐或互补**——

---

## 8. ★ 发射后的验证回路

**不要把没验过的乐谱送进合成器。** 三步：

| # | 检查 | 怎么做 |
|---|---|---|
| **1** | **语法** | ABC → 用解析器过一遍；MusicXML → schema 校验；MIDI → 读回来看轨数与时长 |
| **2** | **与规格一致** | ★ **小节总数 == `Σ form[].bars`**；调号拍号速度对得上；**字母和弦反推级数 == `harmony`** |
| **3** | **听一遍最简渲染** | 用任意音源快速渲染，**只听结构对不对**，不听音色 |

★ **第 2 步的小节总数校验最值钱**——
发射器最常见的 bug 是段落边界算错，而那个错误在音频里表现为"结构乱了"，
很容易被误判成后端不听话。

**这三步用眼睛就能查**；仓库内部若做自动化也不随包发布。

实测过的一条路（2026-09）：本 skill 写法的 ABC（含 `w:` 行、引号和弦、`%` 段落注释、`V:` 头）用 music21 解析零警告，能直接转出 MIDI 与 MusicXML，MIDI 配任意 GM 音色就是第 3 步的最简渲染，不经过生成模型，倒字与旋律密度在这一步就能听到。两个注意：music21 不读 `w:` 行，交给乐手的 MusicXML 要用会读 `w:` 的转换器（abc2xml、abcm2ps 一类）或按"每个音符领一个音节"自行贴回；引号和弦默认不发声，想听和声垫底要自己把它们展开成一条钢琴声部。

---

## 9. 与 ARR-SPEC 的字段对应（四个导出目标）

规格单每个字段都有去向（schema §4）。可执行的字段按下表落到四种格式；本 skill 负责其中的发射规则。

| ARR-SPEC 字段 | ABC | MusicXML | MIDI | ChordPro | 本 skill |
|---|---|---|---|---|---|
| `meta.title` | `T:` | work-title | 轨名 | `{title}` | §3.1 |
| `meta.key` / `mode` | `K:`（`K:Ddor`） | key 的 fifths + mode | 调号 meta | `{key}` | §3.1 |
| `meta.meter` / `meter_map` | `M:`，行内变拍 | time，逐小节 | 拍号 meta | `{time}` | §3.1 |
| `meta.tempo` / `tempo_map` | `Q:` | metronome / sound tempo | set_tempo | `{tempo}` | §3.1 |
| `form[].id` / `name` | `P:` 或 `%` 注释 | rehearsal 记号 + words | marker | `{start_of_verse}` 等 | §3.2 |
| `form[].bars` / `start_bar` | 小节线 | measure number | tick | | §3.2；§8 第 2 步 |
| `form[].harmony_letters` | `"F#m7"` | harmony 的 root / kind / bass，映射见 schema §1.3.1 | 和弦轨或 marker 文本 | `[F#m7]` | §3.3 |
| `material.motif_abc` / `arrangement_hook.abc` | 原样嵌入 | 实际音符 | 实际音符 | | §3 |
| `roster[].id` / `midi` | `V:` 名 | part-name + midi-instrument | 轨名 + program change | | §5.1 |
| `roster[].entry` / `exit`、`form[].events` | 段前 `Z` 多小节休止 | 多小节休止 | 轨内容范围 | | §5.1 |
| `energy_curve`、`vocal.dynamics_by_section` | `!p!` `!f!` 力度记号 | dynamics（映射见 schema §1.5） | 力度缩放 / cc11 | | §5.2 |
| `groove.push_pull` | 无 | 无 | tick 偏移（★ 唯一 exact 的路径） | | §5.3 |
| `vocal.breath_points` | `!breath!` | breath-mark | 无 | | §4 |
| `mix_intent.sound_stage[].pan` | 无 | midi-instrument 的 pan | cc10 | | §5.1 |
| `depth_map` | 无 | 无 | cc91 | | §5.2 |
| LYR-SPEC `lyrics[].lines` | `w:` 行 | lyric，syllabic=single，中文一字一音节 | 无 | 歌词行 | §3 |
| `render.yue.abc` | 本体 | | | | §7 |
| `render.musicxml` / `render.midi` / `render.chordpro` | | 目标开关 | 目标开关 | 目标开关 | §4 §5 |

仅意图的块（`intent`、`material` 的文字、`mix_intent` 与 `vocal` 的大部分）整块作为文本随文件走：ABC 用 `%` 注释，MusicXML 用 miscellaneous，MIDI 用第一小节的 text 事件。四个目标各给谁、带什么，见 schema §4。

---

## 10. 发射完必过

- [ ] 目标格式是按 §6 选的，不是习惯性选 MIDI
- [ ] **小节总数 == `Σ form[].bars`**
- [ ] 调号、拍号、速度与 `meta` 一致
- [ ] ★ **字母和弦反推级数，与 `harmony` 逐和弦对得上**
- [ ] **副歌各遍是展开写的**，没有用反复记号（lint #16）
- [ ] MIDI 里 **CC7 与 CC11 分工正确**
- [ ] 若要验证 push_pull，**做了对齐版和偏移版两份**
- [ ] 送进合成器前**跑过 §8 三步**
- [ ] 给 YuE2 供谱时，用的是已确认的 ABC 方言子集

---

## 附：来源

- §8 的三步校验是人工的；其中"小节总数"与"和弦级数反推"两项是纯数据校验，不需要听。
- ⚠ **未确认**：YuE2 的 ABC 方言（§7.2）
- 记谱法细则、ABC 语法速查、GM 音色表在 [reference.md](reference.md)。
