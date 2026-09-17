---
name: mc-style-hiphop
description: Hip-hop and trap as a style layer (嘻哈风格层): beat-first music where the producer is a programmer, not a drummer. Covers the third time-feel from Dilla Time (straight and swung at once) with usable numbers (rushed snare ~65 ms, per-voice swing, shift-timing ticks), the tempo bands for standard / slow / half-time, the 808 as harmony carrier versus the kick, sample-based beat making from Schloss's Making Beats (digging, looping vs chopping, sampling ethics, locking up a beat, sample layers in the roster), and rap vocals from How to Rap 1 & 2 (flow on the grid, rests and breath, delivery dimensions of pitch / volume / timbre / staccato, 16-bar verse and 4- or 8-bar hook, ad-libs, the rap-versus-sung boundary). Use when writing or arranging hip-hop, trap, boom-bap, lo-fi or a rap section inside another style, when a beat sounds stiff, when the 808 fights the kick, or when deciding what to sample and how to chop it. 嘻哈、说唱、trap、boom bap、808、Dilla、半拍、律动、beat、采样、切片、chop、loop、digging、flow、delivery、气口、ad-lib。
---

# 嘻哈风格层（Hip-hop / Trap）

> **这个风格里，制作者是程序员，不是"碰巧在打鼓机的鼓手"。**
> 《Dilla Time》对 J Dilla 的定论正是这一句——
> *"contrary to the trope that he was simply a great drummer who happened to play the drum machine, he was actually a programmer."*（《Dilla Time》块 1；块 16 又从反面论证：关掉量化只是复制设备，有鼓手反射的人多得是，做出那种律动的只有他一个）

所以本 skill 的立场和别的风格层不同：**律动是被设计的，不是被演奏的。**
"松"、"醉"、"拖"都是**参数**，不是失误。

## 本 skill 覆盖什么

| 覆盖 | 不覆盖 |
|---|---|
| 《Dilla Time》——律动理论（§3） | trap 的专门做法（hat 滚奏语法、build/drop 结构） |
| Schloss《Making Beats》——采样型 hip-hop 的制作逻辑与规则（§6） | 中文说唱的编曲实务 |
| Edwards《How to Rap》1 & 2——rap 人声在编曲里的位置与 delivery（§7） | 词本身（押韵密度、多音节韵、内容）→ 作词库 |
| 语料 hiphop-rnb **10 首**实测 ARR-SPEC（§6.1–6.2） | 采样的版权与清样 |
| Drummer's Bible 的鼓型与速度区间（p0089–0090、p0109）；DeSantis、野崎 | |

★ 语料的 10 首是 **hiphop-rnb 混装**，R&B 一侧的特征已在 `mc-style-citypop-rnb` §7。

## 按任务读哪几节

| 任务 | 读 |
|---|---|
| **判断这些手法能不能搬走 / 搬进来** | ★ §2 移植表 |
| beat 听着僵、像量化过 | ★ §3 第三种时间感；数字在 §3.6 |
| 定速度 | §4；club / 个人聆听的差异在 §6.7 |
| 808 和底鼓打架 | §5 |
| 编制怎么排（采样型） | §6；roster 怎么写见 §6.6 |
| **能不能采这个、怎么切** | ★ §6.4 规则、§6.5 搭 loop；步骤表在 `reference.md` §1–§2 |
| rap 和唱怎么分 | §7.1、§7.6 |
| rap 段的 `push_pull` / 气口 / ad-lib 怎么写 | §7.2–§7.5；参数表在 `reference.md` §3–§5 |
| **和旋律型风格混合**（rap + 中国风、rap + city pop） | ★ §8 |

## 边界

| 不归这里 | 归哪 |
|---|---|
| 量化/velocity 的通用原理、幽灵音符、fill | `mc-rhythm-section` |
| 节奏本身是什么（切分、先现、bounce） | `mc-rhythm-groove` |
| 808/合成贝斯的**音色设计** | `mc-sound-design`（配方在其 reference §1.1 技法 31） |
| rap 的**词**：押韵密度、多音节韵的写法、内容与题材 | **作词库 `lw-rap`**。★ 本 skill 只管人声的**编曲位置、flow 与拍的关系、delivery 分配、和伴奏的关系** |
| 演唱的通用语汇（辅音/元音、音准哲学、和声四型） | `mc-vocal-direction`；本 skill §7.3 只写 rap 特有的维度 |
| R&B 的人声堆叠与和声 | `mc-style-citypop-rnb` §7 |
| 采样的版权与清样 | 不做。§6.4 写的是制作人社群的**手艺规则**，不是法律 |

---

## 1. 动笔前必填

| 必填 | 问法 |
|---|---|
| **① 哪一支** | boom-bap / trap / lo-fi / drill / 旋律型说唱。**速度区间和鼓组语法完全不同**（§4） |
| **② 直的、摇的、还是"第三种"** | §3。这是本风格最重要的一个决定 |
| **③ 低频归谁** | 808 是贝斯还是底鼓？（§5）**不答这条低频一定糊** |
| **④ rap 段和唱段的分界** | §7。全曲都 rap 和全曲都唱是两种歌 |
| **⑤ 采样型还是合成型** | 决定 §6 的编制思路；采样型还要答"采什么、切多碎"（§6.4–6.5） |

---

## 2. ★ 移植表

| 手法 | 可搬出去吗 | 说明 |
|---|---|---|
| **第三种时间感**（直拍与摇摆并存） | ✅ ★ **本风格给别的风格最值钱的东西** | neo-soul、lo-fi、甚至流行抒情都在用。见 §3 |
| **逐件乐器决定量化** | ✅ 可搬 | 它就是 `mc-rhythm-section` §2.2 的极端形态 |
| **808 作为和声载体** | ⚠ 半搬 | 搬进旋律型风格时**只留低频功能**，滑音和长尾会立刻被认出来 |
| **半拍感（half-time）** | ⚠ 半搬 | 强标记。搬进流行副歌可做"落ちサビ"式的落差 |
| **采样切片作为织体** | ⚠ 半搬 | 在别的风格里是"lo-fi 味"的来源，一搬就被认出来 |
| **trap hi-hat 的三连/32 分滚奏** | ❌ **不可搬** | 最强的风格标记 |
| **rap 的 delivery** | ⚠ 半搬 | 作为一个段落（主歌）可以搬进任何风格——这正是 §8 的做法；作为全曲则整首就是 hip-hop |
| **减法结构**（退场最多） | ✅ ★ **该搬** | §6.2 实测 |

★ **判据**：
> **时间感与减法可以自由借；808 的尾巴、trap hat、采样颗粒是标记，借了就会被认出来。**

---

## 3. ★ 第三种时间感：本 skill 的核心

《Dilla Time》引文按 epub 转出的**块号**定位（无页码；全书 16 章的目录见 `reference.md` §6）。

### 3.1 定义

> *"Before J Dilla, our popular music essentially had two common 'time-feels'—**straight time and swing time**—meaning that musicians felt and expressed time as either even or uneven pulses. What Dilla created was a third path of rhythm, **juxtaposing those two time-feels, even and uneven simultaneously**, creating a new, pleasurable, disorienting rhythmic friction and a new time-feel: Dilla Time."*
> ——Dan Charnas《Dilla Time》（块 1）

**不是"介于直和摇之间"，是"同时是直的和摇的"。**

书在第 8 章（块 16）把这句话说得更硬：
> *"It is the deliberate juxtaposition of multiple expressions of straight and swing time simultaneously, a conscious cultivation of rhythmic friction for maximum musicality and maximum surprise. It is conflicted time."*
> 它**不是**直拍和摇摆之间的中点，也不是渐变。

### 3.2 它怎么做出来

书里拆了一个具体案例（块 16，Slum Village《Fantastic, Vol. 2》）：

> 底鼓与军鼓在网格上本该等距，现在变成**短–长–短–长**——这是摇摆；
> **而 hi-hat 同时保持均匀**——这是直拍。
> *"So now there were two time-feels fighting each other: straight and swung."*

★ **落到操作上就一句话：不同鼓件用不同的时间感。**

| 鼓件 | 时间感 | 怎么做 |
|---|---|---|
| **hi-hat** | 直（守网格） | 量化 100% 或 swing 0 |
| **kick / snare** | 摇（或提前/拖后） | 不量化，或 swing 55–65% |
| （或反过来） | | |

这与 `mc-rhythm-section` §2.2 "只量化部分乐器"是同一个原理，**但 hip-hop 把它推到极端：两套时间感故意打架。**

书里还有一个感知细节（块 16）：**大脑把 kick 和 snare 当稳定基底**，所以军鼓一抢，**均匀的 hi-hat 反而听起来像摇的**——它其实是直的。这就是为什么"哪一件守网格"要写清楚。

### 3.3 "松"是程序出来的

> 书里描述 Dilla 律动的六个词：*"Sloppy. Drunken. Limping. Lazy. Dragging. Off."*
> 但它们全是**关掉 Timing Correct** 之后一件一件手打出来的：
> *"he programmed it without timing correct, increasing the sense of the unexpected."*（块 12，Pharcyde《Runnin'》）

**MPC 的 Timing Correct** 就是量化（块 8，Amp Fiddler 教 MPC60 时的原话）：
> *"If you hit a drum sound too late, or too early, it will automatically put the sound right on time. You can use it on all your sounds, or some of them, or none of them."*

★ **"some of them"就是全部秘密。** 见 §3.2 的表。

同一块还记了另一句：*"You don't have to make every measure four beats. You can loop around every three beats, or five, or six, or seven if you want."*——奇数小节的 loop 从一开始就是这台机器的正常用法（§6.5、§7.5 会回到它）。

这些形容词后来成了行话：Robert Glasper 第一次听《Fantastic, Vol. 2》时叫它 *"woozy, drunken rhythms"*（块 21）；Kendrick Lamar 在 Knxwledge 的 beat 上直接喊 *"I need that sloppy!"*——把形容词当名词用，指那种还没有名字的时间感（块 42）。

### 3.4 一个反直觉的听感

> *"The hyperactive kick drum raced ahead of the samba sample, which in turn seemed to be racing ahead of the snare drum—which gave the paradoxical illusion that **the snare was somehow late, making the beat feel oddly relaxed, tumbling endlessly forward**."*（块 12，《Runnin'》）

**底鼓抢、军鼓不动 → 听起来是军鼓拖 → 整体"放松地往前翻滚"。**
所以 `push_pull` 里**要有一件守网格当参照系**（`mc-rhythm-section` §3）——在 hip-hop 里通常是军鼓。

同一首歌的另一个细节（块 12）：底鼓不是 2 小节或 4 小节循环，而是**线性编到第 20 小节才重复**，每小节都不一样——模拟真人鼓手的自然变化。这是 `mc-rhythm-section` §4 线性鼓在采样型里的形态。

### 3.5 与佐证表的关系

「不该全声部严格对齐网格」**62/62**。hip-hop 是这条规则的**发源地**之一——
DeSantis：*"在大多数 Hip-hop 中，更松散、有机的感觉更合适，因为该风格常直接采样真实鼓手的录音"*（p0154）。
★ 但 **trap 是例外**：hat 滚奏必须精确量化，松的只有 808 和采样层。**又回到"逐件决定"。**

### 3.6 ★ 把第三种时间感写成数字

`push_pull` 只写"稍微靠前"等于没写。《Dilla Time》给了几组可以直接抄进 `offset_ms` 的量：

| 手法 | 数值 | 出处 |
|---|---|---|
| **抢拍军鼓**（rushed snare） | 军鼓比反拍**提前约 65 ms**——比失误或 flam 的偏差大两倍多。hi-hat 守直拍像"55 英里/小时"，军鼓像"70 英里/小时"，两个速度感同时存在 | 块 16 |
| 同一件事按网格算 | 一小节切成 **192** 份，军鼓提前 **5** 份（记谱等于五个 128 分音符三连音）。鼓手数不了这么细，所以真鼓手会退到每拍 5、6、7 等分（七连音）去逼近 | 块 17 |
| **鼓机的 shuffle**（Roger Linn） | 把每个偶数十六分音符延后：50/50 是直拍，**66/34** 是完美三连音摇摆，**58/42、54/46** "不直不摇、在两者之间"，才是真有 groove 的量 | 块 9 |
| 逐件 swing | SP-1200 的 swing 是全序列一根橡皮筋一起拉；MPC 允许**每个声音单独设 swing**，再用 **Shift Timing** 按 tick 把某件整体前后挪 | 块 16 |

另外几组量——Jazzy Jeff 的配方、"kick is late, snare is early"、两个脉冲相差约 85 ms、人声整体后挪、用 11 ms 延迟做 flam——见 `reference.md` §6.1。

**怎么写进 ARR-SPEC**：`groove.quantize_policy` 逐鼓件写百分比；`groove.push_pull` 至少三行——一行守网格（0 ms，写明"参照系"）、一行抢（负值，量级参考上表）、一行拖；`vocal.phrasing` 给 rap 段单独一行（§7.2）。

★ **它是编程不是反射**（块 16、块 46）：书专门反驳"Dilla = 关掉量化"的传说——关掉校正的音序器只是在复制设备；抢拍军鼓的**规律性**来自 MPC 的特定用法，不是手快。所以你写的偏移必须**在整段里保持恒定**（块 17："这些微小调整必须随时间保持恒定，且与其他元素冲突共存"），不是每小节随机抖。

### 3.7 Schloss 一侧：采样型制作人怎么看量化

Schloss 记的是 1990 年代末采样型制作人的共识，和《Dilla Time》**互补而不是对立**，两边并排放：

| 议题 | Schloss《Making Beats》 | 《Dilla Time》 |
|---|---|---|
| 为什么不量化 | Domino：采样里是真人鼓手本来就不精确，量化反而让你的鼓"不准"；他只敲军鼓、需要时手动调（p0155–0156） | 为了让两套时间感打架；底鼓徒手、军鼓抢拍是**设计**（块 11、16） |
| 松到什么程度 | "机械精确"与"过于松散"之间有一个**很窄的窗口**，偏向任一端都会被同行说；RZA 不量化被批为 sloppy，Prince Paul 却说"但它听起来太好了"（p0156–0158） | 书把 RZA 列为先例，但 Dilla 的位移是**刻意且严重的**，不是微妙效果（块 16） |
| 判据 | "感觉对"（Stein）；**能不能跳**——跳舞多的人知道什么会让人动（p0158）；club 里要有 bounce（p0198） | Jazzy Jeff（块 1）：*"You can follow the method and you won't have the feeling. You can have the feeling but no success because you have no method."* |
| 适用场合 | DJ 要把这首接进下一首，**节奏不稳会妨碍衔接**（p0201）——club 用途的 beat 要稳一些 | 传统乐手复现它的办法：**鼓手戴掉耳机尽量打直、贝斯手滞后**，张力才保得住（块 17） |

★ 读法：**Schloss 讲的是"别太松"的行规，《Dilla Time》讲的是"松也要松得有规律"。** 两者都在说同一件事——偏移是写下来的量，不是没管。

---

## 4. 速度区间

Drummer's Bible（p0109）：

| feel | BPM |
|---|---|
| **Standard Hip Hop** | **60–108** |
| **Slow feel** | 60–80 |
| **Half-time feel** | **132–172**（军鼓落在 3 拍，听感速度减半） |

> *"there is no one standard Hip Hop/Rap groove … each groove can be played with a straight or swung feel, though the swung feel has become more commonly employed."*

★ **半拍感的写法**：`meta.tempo` 写实际 BPM（如 140），`groove.feel: halftime`。**不要把 140 BPM 的 trap 写成 70 BPM**——hat 的细分会算错，后端也会理解错。《How to Rap 2》p0031 的说法一致：70 bpm 和 140 bpm 是一回事，区别只在 hi-hat 的速度。

**另外两组数**：club 里当代 hip-hop 常态 **88 bpm 或 90 出头**，放 **75–85 bpm** 舞池会清空（Schloss p0200–0201）；按 MC 的感受，**90–100 是中速**、**100 以上算快**（《How to Rap 2》p0025–0027）。

**语料**：hiphop-rnb 10 首检测 BPM 中位 112，⚠ beat tracking 在半拍感上有倍频误判，**不要引用这个数**。

---

## 5. ★ 低频归谁：808 与底鼓

**hip-hop 最常见的技术失败是 808 和 kick 都在 40–80 Hz 里，谁也听不清。**

三种解法，**必须选一种写进 `bass_kick_relation`**：

| 解法 | 做法 | 对应 |
|---|---|---|
| **808 就是 kick** | 808 的起音当底鼓用，没有独立 kick | trap 的标准做法 |
| **kick 短、808 长** | kick 只负责 20–60 ms 的敲击，808 在它衰减后接管 | boom-bap / 旋律型说唱 |
| **互答式** | 808 避开 kick 的击点（`mc-rhythm-section` §7.1） | 采样型 |

### 5.1 808 是和声载体

**在没有和弦乐器的段落里，808 的音高就是和声。**
所以 808 线要写进 `form[].harmony`——它不是"低频"，它是**低音声部**。

★ **差音警告**（`mc-sound-design` §6）：纯正弦的 808 在手机上会消失。
野崎技法 31 的解法：**OSC2 正弦 +19 半音（第三泛音，十二度）**——*"此泛音大约正好位于低音感与粗厚感的分界点上"*，但**"切忌为了展现气势而过度强调泛音的音量"**（p0113）。

### 5.2 采样型的低频：Schloss 的两条

- **kick 最难从采样里剥出来**：军鼓、hi-hat 上面叠的东西可以滤掉，kick 是低频，"你不能在它上面放低通"（Schloss p0160–0161）。所以采样型也常常**kick 单独找**（老唱片或鼓机），loop 只出军鼓和 hat。
- **纯采样在车里会散**：好车载系统上纯采样的低频撑不住，加一个 **808 kick 或键盘贝斯线**会"极大地厚起来"（Schloss p0206–0207）。这就是三选一里"kick 短、808 长"在采样型里的由来。

---

## 6. 编制：采样型的织体

### 6.1 实测：织体轨远多于律动轨

语料 10 首的 roster 角色分布：

| 角色 | 轨数 |
|---|---|
| **织体** | **171** |
| 色彩 | 100 |
| 旋律 | 77 |
| 律动 | 27 |
| 低频 | 15 |

**读法**：hip-hop 的"厚"不来自更多的鼓，来自**大量短的、循环的织体层**（采样切片、vocal chop、pad 片段）；律动轨只有 27——**鼓组本身很简**。
★ **对 `mc-texture-layering` 的后果**：这些织体层大多是**同一个采样的不同切片**，频段天然重叠——**交替出现**（§4.2 解法③）是主要手段，不是让频。

### 6.2 ★ 实测：最"减法"的风格

| 指标 | hiphop-rnb | pop | jazz |
|---|---|---|---|
| **提前退场的乐器比例（中位）** | ★ **71%** | — | — |
| 提前退场的乐器数（中位） | **28** | 16 | 10 |
| 开场就在场的比例 | 24% | 21% | 16% |
| 段落数中位 | 9 | 10 | 9 |

**读法**：开场铺得最满，但**七成的层会在结束前撤掉**。结构感来自**不断撤东西**，不是加东西。
→ **`subtraction_events` 在这个风格里要写得比任何风格都多。**

Schloss 把这写成了这个流派的定义（p0151）：制作人的理想是"一个可重复的图形，通过在不同时间**添加或减去**各种元素来改变它"（p0051）。**减法不是修饰，是它唯一的结构手段。**

### 6.3 采样的编曲逻辑

> Dilla 的日常（《Dilla Time》块 2）：*"He listened to entire songs, listened and listened. His vigilance was almost always rewarded by an element deep within a track."*

**采样不是找 loop，是找一个"元素"**——一个和弦、一个人声尾音、一小节鼓。然后**慢放**：把一段吉他放慢一半，"微小的不完美"变成了律动；六拍的采样撞在四拍的 beat 上（块 14）；慢到"无法辨认"也不要紧（块 29）。★ 这与 `mc-sound-design` §2 "回零点"同理：**素材的不完美是原料，不是要修的东西。**

Schloss 从另一头说同一件事（p0083）：采样自带**和声方向感、节奏感、音色氛围**，而现场乐器"可以演奏任何东西"反而没有可依托的线索——**采样的价值是它带着约束进来**。所以选采样时先问它带来了什么约束（速度、调性、房间感），再决定其余层怎么让。

### 6.4 ★ 采样的规则：制作人社群的手艺伦理

Schloss 第 5 章记的是采样型制作人之间**默认遵守、违反会被同行嘲笑**的一套规则（Vitamin D："They're rules! It's all following rules."，Schloss p0116）。它们不是法律也不是审美——伦理是"入场费"，遵守只保证被尊重，不保证被赞美（Schloss p0147）。两个底层价值：**体力上的努力**（自己去找唱片）与**智力上的努力**（不用现成的、不用已经很好的）（Schloss p0146）。

七条是：**no biting**（不重复别人 loop 过的素材或做过的组合）、**鼓可以重用而采样不行**、**只从黑胶采**、**不采其他 hip-hop 唱片**、**不采你尊敬的唱片**（除非能"以完全惊人的方式 flip 它"）、**不用 breakbeat 合辑**、**同一张唱片不采两个声音**。逐条内容、公认例外与页号见 `reference.md` §2.3（Schloss p0116–0147、p0220）；选材怎么找、目标乐器清单见 §2.1–§2.2。

★ **对写规格单的意义**：`roster` 里每条采样层都要说得出**它从哪类素材来、和同曲其他采样层是不是同源**（§6.6）；同源的层只能是 chop 出的连续 loop 或鼓件——**艺术的一部分是把通常不会被组合的元素组合起来**（Schloss p0145）。伦理的落点不是"能不能采"，是"用得恰不恰当"（Schloss p0218）。

### 6.5 ★ 一个 loop 怎么搭：从 break 到 beat

**术语先钉死**（Schloss p0121、p0166、p0179）：**looping** = 采一或多小节几乎不改地重复；**chopping** = 切成更小片段重新排序；**flipping** = 任何实质性的创造性改变。1990 年代末 chopping 已取代整段 loop 成为主流（Schloss p0166）——要的是采样的**纹理和声音**而不是原曲的**作曲**（p0179）。

**loop 本身会长出新东西**（Schloss p0152–0153）：乐句结尾反复接到开头是原乐手从未打算的并置；含一次和弦变化的采样在 loop 点上**产生原作曲家从未写过的和弦进行**；旋律变成节奏；**call-and-response 是 loop 自带的**。切碎同样能造新和声（块 11）。四条的展开见 `reference.md` §1.3。

**搭建顺序**（十步的完整表、判据与常见失败见 `reference.md` §1.2、§1.6）：

> ① **先鼓**（鼓是 backbone，Schloss p0154）→ ② 用唱机**试装**（p0069）→ ③ **truncate** 定死起止点 → ④ **loop & layer** → ⑤ 用**小节数**定各段长度（块 8）→ ⑥ **切碎重排**（p0157）→ ⑦ ★ **锁住**（"locking up"，**锁没锁上是有无的问题**，p0154）→ ⑧ 查采样里**自带的 hi-hat** 有没有在和你的鼓打架（p0157–0158）→ ⑨ **统一年代与音色**（p0161）→ ⑩ 按段落**加减层**（§6.2）。

**几条判断**：
- **snare 是审美规范不是道德规则**：Mr. Supreme——"hip-hop 没规则，不需要副歌、不需要桥段"，但军鼓是普遍偏好；MC 在无鼓贝斯线上 freestyle 那种"你等 beat 进来、它一直不进"的歌，意义正在于违背这个预期（Schloss p0169–0170）。
- **拍号可以拗**：5/4 的爵士片段可以 loop 进 4/4 鼓型（Schloss p0181）；同类手法还有 3 拍转 4 拍、6 拍采样撞 4 拍鼓、**7 小节 loop 对 4 小节 beat 使 verse 和 chorus 在不可预测的位置开始**、loop 点不在强拍（块 14、16、17；见 `reference.md` §1.5）。
- **不为 chop 而 chop**：Jake One——"我在乎的是听起来怎样，不管他 loop 还是 chop"（Schloss p0182）。

### 6.6 采样型 vs 演奏型：roster 怎么写

Schloss 的核心提醒（p0029–0030）：把一段 hip-hop 录音按乐器分成"鼓、贝斯、钢琴、小号"各一个声部，**是对制作人实际动作的误读**——更可能是：一个采样里带着 upright bass + piano，另一个带着鼓 + Rhodes + 小号，第三个只有萨克斯。**被采样和编排的是整个乐句，不是音高。**

所以采样型的 `roster` 按**采样层**列，不按乐器列：

| 字段 | 采样型的写法 |
|---|---|
| `id` / `instrument` | **一个采样层一行**：`loopA: 1970s soul 采样，Rhodes+bass 同源，2 小节，切 4 片`；层里有几件乐器写在 `note`，不拆行 |
| `role` | 采样层多为 `织体`；**鼓件单独成行**（kick/snare/hat 常来自不同唱片），role 写 `律动` |
| `band` | 写**处理后**的频段：滤掉高频让爵士采样变得"无形而阴郁"（块 11）——写 `中低（低通后）` |
| `timbre` | ★ 必写**来源年代与介质感**：70 年代唱片的压缩与失真、不同采样不同房间感、被截断的 attack/release、循环接缝处的不连续（Schloss p0085–0086）。混音意图可以就一句：*"I want this to sound like a cassette."*（块 31） |
| 演奏层 | 必须**支持采样里已有的主题**，且**听起来得像采样**（"it's gotta blend in"，Schloss p0085–0087） |
| `entry` / `exit` | 织体层多、退场多（§6.2）；stabs 之类按段进出 |
| 同源检查 | ★ 同一张唱片来的两层，只能是 chop 出的连续片段或纯鼓件（§6.4），否则改素材或合并成一层 |

`spectrum_budget` 的 `resolution` 默认写**交替出现**（§6.1）——追求音色来源的异质而不是频段分工的整洁（`reference.md` §1.4）；`depth_map` 里采样层的前景/背景**由滤波决定**。

### 6.7 给 MC 和 DJ 留的空

采样型 beat 的最终用户是 MC 和 DJ，他们的要求直接影响 `form` 和 `roster`（Schloss 第 7 章）：

- **MC**：速度**既不能快到听不清 MC，也不能慢到没能量**；**采样数量不能太多**，否则 MC 会"在混音里迷失"（p0185–0186）。
- **club DJ**：四条筛选是速度、节奏一致性、**能不能接进下一首**、压片质量（p0200）；★ **8 或 16 小节的纯器乐 intro、16 小节 outro** 是 DJ 最爱的做法——"那就是衔接机会"，loop 也"留得很开"方便 beat-juggling（p0202）。
- **耳机听众**：允许**更复杂的节奏结构、不同速度、全频段**；不为跳舞服务的歌放这里（p0206）。

★ **写进规格**：`form` 的 Intro/Outro 至少一处是 8 或 16 小节纯器乐；同时在场的采样层数量对着 MC 的密度算（§7.5）。

---

## 7. 人声：rap 与唱的分界

### 7.1 分界表

| | rap 段 | 唱段（hook） |
|---|---|---|
| 音高 | 无固定音高（语调） | 有 |
| 与网格 | ★ **flow 本身是律动的一部分**，`push_pull` 要给人声一行 | 按 `mc-vocal-direction` |
| 倒字 | **不适用**（无旋律音高）——但**重音位置**要和拍对齐 | 适用（中文） |
| 密度 | 高，一小节 8–16 音节 | 低 |
| 谁管词 | **作词库 `lw-rap`** | 作词库 |

★ **本 skill 只管**：哪几段 rap、哪几段唱、人声在 `roster` 里的进退场、rap 段的 `push_pull`、delivery 怎么分配、人声和伴奏的关系。**flow 的具体节奏只示意**（占位音节 da-da / X 标记，记法见 `reference.md` §3.1）；要不要出词按 `mc-workflow` 出库表的三种情况。

《How to Rap》对"rap 是什么"的定义正好是编曲侧的定义：**flow = 歌词里的节奏 + 押韵**，与朗读诗的区别在于"歌词的节奏必须契合音乐的基本节奏"（p0076）；《How to Rap 2》更进一步："**节奏是 MCing 唯一完全不可或缺的元素**"——没节奏、跟不上拍，内容再好也不算 MC（p0018）。Schloss 从制作人侧说了同一件事：gangsta rap 对听众而言不是由歌词定义的，是由合成器和 **MC 的 flow** 定义的（p0214）。

### 7.2 ★ flow 的要素：它怎么落在网格上
这一小节写的是 flow **与拍的关系**——它决定 `vocal.phrasing` 和 `groove.push_pull` 里人声那一行怎么写。押韵密度、多音节韵怎么写归作词库。

**两条地基**：

★ 全文在 [reference-vocal.md](reference-vocal.md) §8–§11。

### 7.3 ★ delivery 的维度：分配给哪一段
《How to Rap 2》把 delivery 拆成可以逐段分配的维度。**声音是听众第一个听到、据此决定听不听下去的东西**（p0072）。八个维度是**总体音高、音高变化量、逐词音高、音量、音色、长短、口吃/重复、气息**——每个维度的档位、效果与例子在 `reference.md` §4.1；个性类型（狂野 / 弹跳 / 权威 / 放松 / 内省 / 愤怒，即 `vocal.persona` 的选项集）在 §4.2；口音与角色声在 §4.3。分配逻辑：

★ 全文在 [reference-vocal.md](reference-vocal.md) §8–§11。

### 7.4 rap 段的结构：verse、hook、bridge、ad-lib
**默认数字**：verse **16 小节**（"a 16"，公式是三段 16；story rap 上限 32，《How to Rap》p0206–0209），verse 里最难的是**第 1–2、9–10、15–16 小节**（p0210）；hook **4 或 8 小节**，词比 verse 简单、密度低、拖长一点，**旋律化的 hook 比说唱 hook 更受欢迎**（p0200–0202）；hook 旋律常是第一句升调"提问"、第二句降调"回答"（《How to Rap 2》p0162–0164）；ad-lib 更常见的是**只叠关键词**——Twista 只重叠**每小节最后一个词**（《How to Rap》p0295）。


★ 全文在 [reference-vocal.md](reference-vocal.md) §8–§11。

### 7.5 ★ 人声和伴奏的关系
这一小节回答"rap 段的 `roster` 怎么配"。

**① beat 领着写。** 多数 MC 对着最终的 beat 写（Dray："像跳舞，beat 是领舞的"）；"那是你要在上面说的一切的模板"（《How to Rap》p0181–0183）。beat 里若已有**人声采样**，它会直接引导内容（p0183）。**换 beat 就换了重音**——录完人声再换 beat，"重音和词是按原来那首音乐强调的"（p0187）。


★ 全文在 [reference-vocal.md](reference-vocal.md) §8–§11。

### 7.6 rap 与唱之间的过渡地带

分界不是一刀切，《How to Rap 2》p0155–0167 给了一个谱系，写规格时要说清落在哪一格（每格的做法与例子见 `reference.md` §5.4）：**按段落分** → **半唱吟诵段**（重叠的短句反复吟唱，打破"三段 verse 一个 chorus"的常规）→ **verse 内部切换**（只半唱某几个词）→ **半唱作为整体风格**（接近真唱 → 常半唱 → 声音本身有旋律感但不唱）→ **唱别人的歌**。

两条约束：半唱常配 **matching patterns**（每小节相同的节奏型，重复强化旋律，p0165–0166）；**半唱的旋律要在 beat 的调里**（§7.5 ⑤），组内分工按音域给（p0155）。

★ **写规格**：`vocal.delivery` 逐段写 `rap` / `半唱` / `唱`；`style_layers` 里 `vocal_delivery_verse` 与 `vocal_delivery_chorus` 分开签字（§8）。半唱段落**同时**要写 `push_pull`（它仍是 rap 的时间感）和 `melody_register`（它已经有音高）。

---

## 8. ★ 和旋律型风格混合

**hip-hop 是最常被拿来混合的风格**（rap + 中国风、rap + city pop、rap + 摇滚）。
用 `mc-workflow` §2.4 的分工表，**本 skill 通常是 overlay**，拥有：

| 维度 | 归 hip-hop 时意味着 |
|---|---|
| `groove` | 第三种时间感（§3）、半拍感 |
| `low_end` | 808 承担低音声部（§5.1） |
| `vocal_delivery_verse` | 主歌 rap |

**通常让出去的**：`harmony`、`melody`、`color_instruments`、`vocal_delivery_chorus`、`form`。

### 8.1 《桃花扇》式（rap + 中国风）的分工

| 维度 | Owner | 注意 |
|---|---|---|
| harmony / melody（副歌五声） | 中国风 | 808 的音高**要跟五声旋律的低音走**，不能自己走一套 |
| color_instruments（古筝/笛） | 中国风 | ★ **只在人声句缝里，不占律动位**——否则和采样织体抢 |
| groove / low_end | hip-hop | 半拍感 + 808 |
| vocal_delivery_verse | hip-hop | rap |
| vocal_delivery_chorus | 中国风 | 唱 |
| form | 中国风 | 仍是主歌–副歌，不是 trap 的 build/drop |

`fusion_note` 的写法：**说清两边在接缝上共有的东西**（例："民乐的句尾花和 rap 的句尾押韵落在同一个位置"）。

### 8.2 一条实测的边界

实测中 agent 在没有本 skill 的情况下做过这个混合，
用 `citypop-rnb` 的 R&B 半边 + `rhythm-section` 顶上——**能做，但 §3 的第三种时间感和 §5 的 808 分工都没提到**。
**这就是本 skill 存在的理由。**

---

## 9. 与 ARR-SPEC 的字段对应

| 字段 | 本 skill |
|---|---|
| `meta.style_layer` / `style_layers` | `mc-style-hiphop`，混合时通常是 `overlay`（§8） |
| `meta.tempo` + `groove.feel` | §4——半拍感写实际 BPM + `halftime` |
| `groove.quantize_policy` | ★ §3.2——**逐鼓件**写（hat 100 / kick 0 / snare 0…） |
| `groove.push_pull` | §3.4、§3.6——军鼓守网格做参照，抢/拖用 §3.6 的量级；**rap 段人声要有一行**（§7.2） |
| `arrangement.bass_kick_relation` | ★ §5——三选一；采样型注意 §5.2 |
| `form[].harmony` | §5.1——808 线写进去 |
| `form[].bars` / `form[].note` | §7.4——16/8/4 是默认；奇数小节 loop 与非 4/4 写进 `note`（§6.5、§7.2） |
| `form` 的 Intro/Outro | §6.7——至少一处 8 或 16 小节纯器乐 |
| `subtraction_events` | §6.2——比别的风格多 |
| `roster[]` 各字段 | §6.1、§6.6——按**采样层**列，`timbre` 必写年代与介质感 |
| `spectrum_budget[].resolution` | §6.1——采样层默认**交替出现** |
| `vocal.persona` / `delivery` | §7.3、§7.6——个性类型与音高档；逐段写 rap / 半唱 / 唱 |
| `vocal.phrasing` / `breath_points` | §7.2——逐句抢/拖/lazy tail/staccato；换气写进休止 |
| `vocal.ad_libs` / `stacking` / `chorus_variation` | §7.4、§7.3——关键词叠、每小节末词叠、hook 层数、每遍换哪个维度 |
| `mix_intent.mood` | §6.6——采样型常是一句"像磁带" |

---

## 10. 写完后必过

- [ ] 说清是**哪一支**，速度落在 §4 的区间里，半拍感写的是实际 BPM
- [ ] ★ **量化是逐鼓件写的**，且至少一件直、一件摇（§3.2）
- [ ] 有一件守网格做参照系（通常是军鼓）；抢/拖的 `offset_ms` 有量级，且**整段恒定**（§3.6）
- [ ] ★ **低频归属三选一**写进 `bass_kick_relation`；808 的音高写进了和声，纯正弦补了第三泛音
- [ ] `subtraction_events` **不少于三处**；织体层的频段冲突用**交替出现**解决（§6.1）
- [ ] 采样型：`roster` 按采样层列，每层写得出来源年代与处理后的频段；**同源的两层只能是连续 chop 或纯鼓件**（§6.4、§6.6）
- [ ] 采样型：鼓是先搭的、loop 锁上了、采样里没有别人的 hi-hat 在和你的鼓打架（§6.5）；Intro 或 Outro 有一处 8/16 小节纯器乐（§6.7）
- [ ] rap 段与唱段分界清楚，半唱段落标了格（§7.6）；**词的取舍符合 `mc-workflow` 出库表的三种情况**
- [ ] rap 段：`push_pull` 有人声一行，写明跟哪一件乐器；换气写进了休止（§7.2）
- [ ] rap 段：delivery 按段分配了音高档 / 音量档 / 音色，hook 与 verse 不同（§7.3）
- [ ] hook 4 或 8 小节，verse 16（或写明为什么不是）；ad-lib 没有均匀撒（§7.4）
- [ ] 混合时 `style_layers` 里 hip-hop 拥有的维度**不与 base 重叠**（lint #17）

---

## 附：来源

- **Dan Charnas《Dilla Time: The Life and Afterlife of J Dilla》**（按块号引用；16 章目录与块号索引见 `reference.md` §6）——§3 全部引文，§6.3、§6.5、§6.6、§7.3、§7.5。
- **Joseph G. Schloss《Making Beats: The Art of Sample-Based Hip-Hop》**——§3.7、§5.2、§6.2–§6.7、§7.1；采样处理与选材细则见 `reference.md` §1–§2。
- **Paul Edwards《How to Rap》**与 **《How to Rap 2》**——§4、§7.1–§7.6；flow 记谱法、delivery 参数表与人声音效清单见 `reference.md` §3–§5。
- **Drummer's Bible**（p0089–0090、p0109）——§4。**DeSantis《Making Music》** p0154——§3.5。**野崎貴朗** 技法 31（p0111–0113）——§5.1。仓库实测语料（hiphop-rnb 10 首）——§6.1–6.2。
- ⚠ 本 skill 不覆盖 trap 的专门做法与中文说唱编曲实务。
