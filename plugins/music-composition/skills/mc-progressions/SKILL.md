---
name: mc-progressions
description: Chord progression lookup library (和弦进行速查库) - the eight famous named progressions with their variants and what each one actually does, a mood reverse-lookup, the eight variation techniques that turn one standard progression into ten, and an explicit warning list of the progressions generative models default to. This is a lookup skill, not a teaching skill - it answers "give me a progression that does X". Harmonizing a melody, choosing chords bar by bar, or reharmonizing is mc-harmony, and a full track needs mc-harmony first and this table second. Use when you need a progression for a specific mood or section, when a loop feels stale, when varying a standard progression, or when checking whether a progression is too defaulted-to. 和弦进行、王道进行、卡农、小室、丸サ、循环、套路、换个进行。
---

# 和弦进行速查库（Progressions）

**这是一个查表 skill，不是教学 skill。**

| 你问 | 去哪 |
|---|---|
| "给我一个能做出 X 感觉的进行" | **本 skill** |
| "为什么这个进行成立" | `mc-harmony` |
| "怎么把这个进行改得更好听" | 本 skill §4 ＋ `mc-harmony` §7 |

## 按任务读哪几节

| 任务 | 读 |
|---|---|
| 按情绪找进行 | §1 情绪反查表 |
| 要一个"就是那个味"的进行 | §2 八大名进行 |
| 按类别翻 | [reference.md](reference.md) |
| 手上的进行用腻了 | §4 八种变形 |
| **担心进行太套路** | ★ §3 |
| 移调 | [reference.md](reference.md) §5 |

## 边界

| 不归这里 | 归哪 |
|---|---|
| 和声的原理、功能、为什么 | `mc-harmony` |
| 重配和声的九步流程 | `mc-harmony` §7 |
| 转调 | `mc-modulation` |
| 某个风格特有的进行语汇 | `mc-style-*`（L3）。★ 本 skill 的语汇偏 **J-POP**；中文流行的和声语汇未单列；**K-POP 与欧美流行没有对应的 L3 skill**，用本 skill 的通用表并在规格单里声明（见 reference.md §11） |
| 分类全表、名曲索引、移调表 | [reference.md](reference.md) |

---

## 1. 情绪反查表

| 情绪 | 首选 |
|---|---|
| **明亮／有力** | I–IV–V–I ／ IV–V–I–VIm ／ 流行朋克进行 |
| **切ない／emo（揪心）** | **王道进行 4536** ／ C–E7–F–Fm ／ IVm 类（C–Fm–C） |
| **温暖／踏实** | C–Dm–Em–F ／ C–Am–F–G ／ C–Gm7·C7–F |
| **时髦／都会** | **丸サ进行** ／ 裏和弦进行 ／ 二五一 ／ F/G 属代理 |
| **忧郁／演歌／歌谣曲** | Am–G–F–E ／ Am–Dm–E7–Am ／ 小调二五 |
| **壮大／史诗抒情** | C–Bm7(♭5)·E7–Am 类 ／ 8 小节卡农 ／ 下行低音クリシェ |
| **急转／复杂** | Em–F–Am–G ／ F–Dm–G–Em·E7 ／ C–B7–Em7–E♭ |
| **可跳舞** | F△7–E7–Am–C7 + four-on-the-floor ／ C7(9,13)–F7(9,13) riff |

---

## 2. 八大名进行

以下一律 **Key = C**（小调类 Key = Am）。级数是思考用的，字母是交接用的，**双记**。

### 2.1 王道进行（4536）

```
IV△7 – V7 – IIIm7 – VIm7      F△7 – G7 – Em7 – Am7
```
名字就是级数 4-5-3-6。**4 和 5 是大和弦、3 和 6 是小和弦——
"由明转暗"这个落差正是切なさ的来源。** 与卡农进行并列为两大王牌。

| 变体 | 效果 |
|---|---|
| `IV△7–V7–III7–VIm7`（F△7–G7–E7–Am7） | III 变属七 → **哭腔更强**，往 VIm 的拉力更强 |
| `IV△7–V(onIV)–IIIm7–VI7`（F△7–G/F–Em7–A7） | 第 2 小节的分数和弦是**"キュン"（心动）点** |

### 2.2 卡农进行

```
I – V – VIm – IIIm – IV – I – IIm – V      C – G – Am – Em – F – C – Dm – G
```
**转位形** `C–G/B–Am–Em/G–F–C/E–Dm–G` 让低音**沿音阶级进下行**（C-B-A-G-F-E-D-G）
= 低音线クリシェ。

- 四和弦简化形 `C–G–Am–Em` 同样标准；**结尾 Em→G 更好循环**（末尾的属和弦顺畅流回主和弦）
- 半音化版本（低音 C–B–B♭–A–A♭–F#–G）见 [reference.md](reference.md) §4

### 2.3 小室进行（6451）

```
VIm – IV – V – I      Am – F – G – C
```
**小调色彩起头 → 稳定的大调解决**。1990 年代 J-POP 黄金期的声音。
近亲 `Am–G–F–C`（6541，低音级进下行，更抒情）。

### 2.4 丸サ进行 / Just The Two Of Us（4361）

```
IV – III7 – VIm – I      F – E7 – Am – C
```
**时髦、都会、emo——city pop / neo-soul 的同义词。**
E7 是指向 Am 的**副属和弦**，即"emo 点"。
既可一次性做重音，也可整首循环，**"几乎任何旋律放上去都显得时髦"**。

七和弦强化版 `F△7–E7–Am–C7` 配 four-on-the-floor 底鼓 → techno/dance。

### 2.5 流行朋克进行（1564）

```
I – V – VIm – IV      C – G – Am – F
```
明亮、有力、全球通用。
**同一组和弦换起点**得到常见变体 `4156`（F–C–G–Am）与 `6415`（Am–F–C–G）；
**首尾和弦是大是小，决定整体明暗。**

### 2.6 循环进行（1625）

```
I – VIm – IIm – V      C – Am – Dm – G
```
七和弦版 `C–Am7–Dm7–G7` 同样标准。**今天听来略复古，故意用这份复古是有效的选择。**
后半的 2→5 就是**二五**：IIm 是 IV 的代理，且低音**上行四度大跳**，比 4→5 更有力。

> **典型用法**：`C–Am–F–G` 循环三次，**第四次换成这个进行**，做进入下一段的扳机。

### 2.7 逆循环（2516）

```
IIm – V – I – VIm      Dm – G – C – Am
```
★ **关键理解**：它不是把和弦倒着排，而是**两组结构**——
"运动功能组（2→5）在前，主功能组（1→6）在后"。

七和弦版 `Dm7–G7–C△7–Am7`：**不从主和弦起头**，二五从反方向绕回主和弦。

### 2.8 ドリカム进行

```
I△7 – VIIm7 – IIIm7 – IIIm7(onII)      C△7 – Bm7 – Em7 – Em7/D
```
**性格随速度和旋律剧变**。变体 `C△7–B7–Em7–G7`（VIIm7→VII7）更催泪，
末尾的属七强化了回主的运动。

---

## 3. ★ 套路警戒线：哪些进行生成模型会默认给你

**这一节是本库特有的，日式底本里没有。**

生成后端在"pop / emotional / ballad"这类提示下，**高概率默认输出下面这几条**：

| 进行 | 为什么危险 |
|---|---|
| `I–V–VIm–IV`（1564） | **全球最泛滥**的四和弦。模型的第一直觉 |
| `VIm–IV–I–V`（6415） | 同一组的旋转，一样泛滥 |
| `I–VIm–IV–V`（1645） | 50 年代起就是模板 |
| `IV–V–IIIm–VIm`（4536） | J-POP 语境下的第一直觉 |

**这不代表不能用。** 判据是：

> **你是"选择"了它，还是"默认"落进了它？**

**三条对策**（强度递增）：

1. **保留进行，改变形**——用 §4 的八种变形至少改两处，尤其是**换起点**和**低音线设计**
2. **保留进行，改和声节奏**——不要一小节一个。挪动换和弦的位置比换和弦本身更有效
3. **换掉它**——从 [reference.md](reference.md) 的 §3 感伤类 / §4 爵士都会类里挑一个功能等价的

★ **最有效的其实是第 2 条**，因为模型在"和弦是什么"上被大量训练，
在"和弦什么时候换"上反而弱——**和声节奏是更廉价的差异化手段**。

**自检动作**：把你的进行放进上表查一遍。
命中了就在 ARR-SPEC 的 `form[].note` 里写一句"为什么仍然选它"。

---

## 4. 八种变形：把一个进行变成十个

**这些技巧可以套用到本库任何一个进行上。**

| # | 技巧 | 做法 |
|---|---|---|
| 1 | **换起点** | 同一组和弦从不同和弦起（1564→4156→6415；1625→2516）。★ **首尾是大是小决定明暗** |
| 2 | **七和弦化 / add9 化** | 全部加七（Am–Dm–Em→Am7–Dm7–Em7）立刻现代流行感；add9 化（Amadd9–Fadd9–Gadd9）更轻更柔 |
| 3 | **III→III7（副属化）** | ★ **最快的"哭腔"开关**。任何自然音和弦前面都可以放一个副属（A7/B7/C7/D7/E7） |
| 4 | **分数和弦化（低音线设计）** | 用转位让低音级进（卡农 C–G/B–Am–Em/G…），或半音化（C–G/B–B♭–F/A–Fm/A♭–D/F#–F/G）。★ 属和弦可换成 **F/G**（= G7sus4(9)），更含蓄精致 |
| 5 | **插 sus4** | 段末的 V 前插 Vsus4（F→Gsus4→G），强调"这一章结束了"。**只在最后一遍加**也行 |
| 6 | **二五化** | V 前拆出 IIm7（E7→Bm7(♭5)–E7；G7→Dm7–G7）。指向 IV 的 Gm7–C7 带来温暖的意外 |
| 7 | **拍数调整与拼接** | 4 小节型可压缩成每和弦 2 拍；**挑合口味的 4 小节型拼成 8 小节，旋律会自然浮现** |
| 8 | **同主调借用** | IV→IVm，或加 ♭VI/♭VII/♭III，立刻得到切なさ或摇滚粗粝 |

> ★ 第 7 条是本节最被低估的：**拼接两个 4 小节型比发明一个 8 小节型容易得多，效果还好。**

---

## 5. 与 ARR-SPEC 的字段对应

| 字段 | 说明 |
|---|---|
| `form[].harmony` | 级数记号。**从本 skill 抄的进行要转成级数，不要抄字母** |
| `form[].harmony_letters` | 字母记号。按 `meta.key` 移调，表见 [reference.md](reference.md) §5 |
| `form[].note` | ★ 若用了 §3 警戒线上的进行，**在这里写为什么仍然选它** |
| lint #9 | 至少一段要有调外和弦——§4 的第 3、6、8 条都能满足 |

---

## 6. 用完后必过

- [ ] 进行转成了**级数**，不是直接抄字母
- [ ] 字母记号按实际的 `meta.key` 移调过了
- [ ] 查过 §3 警戒线；命中的话写了理由
- [ ] **和声节奏不是全曲一小节一个**
- [ ] 至少用了 §4 的一种变形（除非刻意要那份原始感，且写明）
- [ ] 副歌和主歌**不是同一个进行原样重复**（同进行不同和声节奏也算变）

---

## 附：来源与偏向声明

- **日式包 `jp-chord-progressions`**——§1、§2、§4 与全部分类表。
  底本为三本日本和弦进行实务书。
- ★ **偏向声明**：这份语汇**偏 J-POP**（王道、小室、丸サ、ドリカム 都是日本业界的叫法）。
  它在**华语流行与 city pop 上高度适用**，在 EDM、嘻哈、欧美摇滚上覆盖不足。
  那些由 **L3 风格层**各自补齐——**不要把这份表当成"全部的流行和声"。**
- 中文流行的进行语汇：→ `mc-style-chinese-pop`
- 爵士标准曲的进行： → `mc-style-jazz`
- 分类全表（王道 J-POP 类 / 感伤类 / 爵士都会类 / 小调类 / クリシェ线条类 / 8 小节大型）、
  名曲→进行索引、移调表，全部在 [reference.md](reference.md)。
