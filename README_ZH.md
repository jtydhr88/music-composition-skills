# music-composition-skills（作曲编曲技能集）

[English](README.md) · 中文版 · [日本語](README_JA.md) · [한국어](README_KO.md)

29 个 agent skill（用于 [Claude Code](https://docs.anthropic.com/en/docs/claude-code/skills) 与 [OpenAI Codex](https://developers.openai.com/codex/)），做流行音乐的作曲与编曲。底本是 50 本配器、和声、曲式、复调、混音与风格实务的书，外加 62 首从多轨录音实测出来的编曲。

**产出的是一份规格单，不是几句建议。** 你要一首歌，agent 填的是一份 ARR-SPEC，内容是调、速度、精确到小节的段落表、字母和弦与罗马数字级数、谁什么时候进什么时候退、能量曲线、演唱方式、混音意图，然后把这一份文档编译成 Suno 提示词、给 YuE2 的 ABC 谱，或者 MIDI。验收时对照的还是它。整个设计就是这句话：**一份东西，四处用**，分别是对外交接、可控 AB、编译源、验收基准。

**用你自己的语言问。** skill 正文是中文写的，因为大部分底本是中文原著或中译本。你用什么语言问，agent 就用什么语言答。

**这套 skill 不会让生成模型跑得更快，它是在用时间换质量。** 完整走一遍专业的音乐工作流程，一首歌常常要二十分钟以上（agent 要读判据、填规格单、过自检），换来的是专业级的规格，不是几句提示词。

**本库不提供任何作品，作者自己的或别的歌手的都没有，编曲和歌词都没有。** 音乐作品的好坏是很主观的判断，请自行使用、自己判断这套Skills的价值，以及它在耗时上的代价。

## 这一套的四个库

一条音频线，切成四个各自独立的库。每一个单独就能用；库与库之间的接缝是写下来的，所以也能搭配着用。

| 库 | 干什么 | 状态 |
|---|---|---|
| **[music-composition-skills](https://github.com/jtydhr88/music-composition-skills)** | 作曲与编曲。一句 brief 变成一份 ARR-SPEC：调、速度、段落表、和声、谁什么时候进退、能量曲线、演唱与混音意图，再编译成 Suno、YuE2 或 MIDI 的输入 | **本库** |
| **[lyric-writing-skills](https://github.com/jtydhr88/lyric-writing-skills)** | 作词。动笔前的门禁变成一份 LYR-SPEC：结构、意象、押韵、叙事，加上普通话、粤语、英文、日文、韩文的语言层 | 已发布 |
| **配乐库** | 为画面写音乐：spotting（哪里该有音乐、从哪一帧进）、与剪辑点的关系、cue 怎么写怎么命名、主题在全片怎么发展 | 待建 |
| **混音与声音设计库** | 把混音意图变成实际操作：EQ、压缩、混响、自动化、母带 | 待建 |

**各自独立。** 给现成旋律填词，只要作词库。编一首器乐曲，只要编曲库。

**能组合，靠的是有名字的交接物。** ARR-SPEC 与 LYR-SPEC 是双向的：先曲后词时交过来段落表与小节数，先词后曲时交回去每句字数与重音位置，有六个字段必须两边一致。ARR-SPEC 里的 `mix_intent` 就是将来递给混音库的简报。配乐库出的 cue 表就是递给编曲库的 brief。

**接缝已经写好了。** 每个 skill 都带一张边界表，不归自己的问题按名字指到对应的库，而不是在原地勉强作答。所以那两个待建的库现在已经被处处引用：EQ 与压缩的实操出库到混音库，spotting 与 cue 的写法出库到配乐库。

## 为什么有了生成模型还要这套 skill

编曲是一门有几十年积累的手艺。什么时候该做减法，副歌为什么要更高，低频归谁，第二段主歌该比第一段空还是满，这些判断写在几十本书里，散在不同的语言、体系和年代中。**这套 skill 把它们整理成一个专业编曲会做的决定，并写成生成模型能被告知的形态。**

Suno 和 YuE2 已经能把一句话变成一首听起来完整的歌，它们缺的正是这些决定。你没指定的部分，模型会用自己的默认值补上，所以不加引导的产出听起来总像同一个制作人做的。

所以这套 skill 交出去的是决定本身：吉他在第几小节退场，哪一段在起来之前先撤掉一层，能量在哪里下落，第三遍副歌的人声要有什么不一样。**它做的是用专家级的规则去指导生成模型，不去重做一个生成模型。**

这样换来的是提示词工程够不到的那部分。提示词是一句话，一句话没法逐字段核对。规格单可以拿回来逐字段比对，可以一次只改一个字段重跑，做出可控的对照。里面每个决定都带理由和出处，半年之后你还说得出第二遍副歌为什么那样编。

模型仍然是演奏者和录音棚。这份东西是递给它的编曲谱。

`SKILL.md` 遵循开放的 [agentskills.io](https://agentskills.io) 规范，两个 agent 共用同一套文件：装一次，两边都能用。

## 安装

### Claude Code

#### 插件市场（推荐）

```
/plugin marketplace add jtydhr88/music-composition-skills
/plugin install music-composition@music-composition-skills
```

之后用 `/music-composition:<skill>` 调用，例如 `/music-composition:mc-workflow`。

#### 个人级（所有项目）

```bash
git clone https://github.com/jtydhr88/music-composition-skills.git
cp -r music-composition-skills/plugins/music-composition/skills/* ~/.claude/skills/
```

#### 项目级

```bash
mkdir -p .claude/skills
cp -r music-composition-skills/plugins/music-composition/skills/* .claude/skills/
```

### Codex（CLI / ChatGPT 桌面端 / IDE 扩展）

```bash
codex plugin marketplace add jtydhr88/music-composition-skills
# 然后：Plugins → 选 "Music Composition Skills" → Install
```

或者把同一批文件复制到 `~/.agents/skills/`（个人级）或 `.agents/skills/`（项目级）。同样的 29 个 `SKILL.md` 通过两边的插件目录发布，不重复、不改写。

#### 确认装上了

agent 判断到相关上下文时会自动加载。列出全部：`/skills`。

## 用法示例

```
# "写一首 city pop，主题是末班电车，三分半，把要贴进 Suno 的东西都给我"
# → mc-workflow → mc-style-citypop-rnb → mc-arrangement-arch → mc-render-compile

# "副歌上不去，改编曲，别动旋律"
# → mc-arrangement-arch（能量曲线、上去之前先做减法）+ mc-texture-layering

# "主歌 rap、副歌中国风，像某首歌那样"
# → mc-workflow §2.4 风格混合 → mc-style-hiphop（基底）+ mc-style-chinese-pop（叠加）

# "生成出来的听着像 AI，但我说不出哪里不对"
# → mc-ai-tell-audit：十四条可枚举的机器指纹，逐条数

# "bossa nova 该用什么鼓型、多少速度？"
# → mc-rhythm-section：十一张风格表，再走五步选型
```

## 常见用例

这套东西比看上去复杂。一句话的 brief 会牵动作曲、编曲、作词三边十几个 skill，agent 读什么、出什么，取决于你那句话里有没有把几个关键项说到。下面是实际跑过的几种常见组合，每条写清楚怎么说、会走哪些 skill、出什么。带歌词的用例要同时装作曲编曲库和作词库。

**1. 参照某位歌手的风格写一首带词的歌**（最常见）

> 写一首某位歌手某首歌那种风格的华语流行歌，女声，要在 Suno 上生成，带歌词。

走：lw-workflow → lw-song-intent → lw-structure → lw-mandarin → lw-tone-check → lw-suno-interface；mc-workflow → mc-style-chinese-pop → mc-melody → mc-development → mc-arrangement-arch → mc-vocal-direction → mc-render-compile
出：LYR-SPEC.yaml、ARR-SPEC.yaml、suno-pack.md（标题、风格描述、带段落标签的歌词、排除词）
说明：参照歌手只借编制、速度、人声人设、段落走法，不借词曲。

**2. 抖音口水歌，不要叙事**

> 写一首抖音口水歌，女生，甜美，不要叙事。Suno 生成，带歌词。

走：lw-song-intent（态度型）→ lw-structure（重复预算、hook 位置）→ lw-rhyme → lw-mandarin；mc-style-chinese-pop → mc-rhythm-groove → mc-arrangement-arch → mc-render-compile
说明："不要叙事"四个字决定歌词类型。不说的话，默认会写成情境。

**3. 伤感情歌，要直接说的，不要场景**

> 写一首某位歌手某首歌那种的伤感情歌，男声，Suno 生成，带歌词。要直接说的那种，不要场景描述。

走：同 1，但 lw-song-intent 走态度型，锚是一句话（副歌段首反复的那句）而非物件；mc-vocal-direction 管贴麦男声与末句留白
说明：语料里四成的态度型歌没有任何可拍照的物件，副歌就是对"你"说的一句话，主歌说我的状态，开头是独白。想要这种，就在 brief 里说"直接说，不要场景不要物件"。

**4. 一种心情，不写成故事**

> 写一首国语流行歌，三分半，女声，主题是搬家那天把充电线留在了墙上的插座里，走了两条街才想起来，没回去拿。不要写成故事，我要的是想起来又算了的心情。

走：lw-song-intent（情境型，锚物件是充电线）→ lw-imagery → lw-structure → lw-mandarin → lw-tone-check；曲侧同 1
说明：情境型与叙事型的分界在这里：有物件，没有情节推进。

**5. 器乐曲，从零作曲**

> 写一首两分四十秒的器乐曲，给短视频当背景，主奏是一把尼龙弦吉他，配一点打击和贝斯，可以有一件键盘。感觉是"下午三点，在等一个人，不着急"。不要人声。

走：mc-workflow → mc-development（动机、性格、发展手法）→ mc-melody → mc-form → mc-harmony → mc-rhythm-section → mc-orchestration → mc-render-compile
出：ARR-SPEC.yaml（material 块与每段的 development 字段填满）、suno-pack.md、一页说明
说明：不带词，作词库不参与。判据在 mc-development：新材料不过半，有再现。

**6. 已有词曲，只做编曲**

> 给一首已经写好旋律和歌词的国语情歌做编曲，要 salsa 的感觉，有铜管，速度中快。只要规格单，不用 Suno prompt。

走：mc-workflow（编曲路线）→ mc-style-latin → mc-rhythm-section → mc-orchestration（铜管）→ mc-arrangement-arch → mc-texture-layering
出：只有 ARR-SPEC.yaml 和一页说明

**7. 风格融合：主歌 rap，副歌中国风**

> 写一首像某首歌那样 rap 加中国风的歌，要完整的中文歌词，用在 Suno 上。

走：mc-workflow §2.4 风格混合 → mc-style-hiphop（基底）+ mc-style-chinese-pop（叠加）；lw-rap（主歌）+ lw-chinese-style（副歌）+ lw-mandarin

**8. 粤语填词**

> 给这条旋律填粤语词，小节数和每句字数在这里。

走：lw-workflow（先曲后词）→ lw-cantonese（先按音高类别算，再选字）→ lw-tone-check
说明：粤语的写作顺序是反的，普通话那层顶替不了。

**9. 不用 Suno，用本地开源模型（YuE2）**

> 同一首歌，不走 Suno，用本地 YuE2 生成，把规格单编译成 ABC 谱给我。

走：同 1，再加 mc-symbolic-score（把 ARR-SPEC 编译成 ABC，人声线写在小写音名区、带 w: 歌词行）
说明：实验性。词唱出来的比例目前低于直接给 style 与歌词的方式，细则见 mc-symbolic-score §6b。

brief 里真正起作用的几句话：

| 你说的 | 它决定什么 |
|---|---|
| 歌手名＋歌名 | 只借编制、速度、人声人设、段落走法；不借词曲 |
| 女声／男声，甜美／贴麦 | mc-vocal-direction 的人设与音域 |
| Suno ／ 本地 YuE2 ／ 只要规格单 | 出口：suno-pack、ABC 谱、只出 ARR-SPEC |
| 不要叙事 ／ 直接说不要场景 ／ 不写成故事 | 歌词类型：态度型 ／ 态度型无物件 ／ 情境型。不说，默认情境 |
| 时长 | 段落数与小节数从这里反推 |
| 产物清单 | 写明要哪些文件，agent 就按清单出，少一份都算没做完 |

以上每条完整走完通常二十分钟以上，见上文"用时间换质量"。

## 不需要装任何工具

插件是纯文本。没有脚本要跑，不需要 Python，不需要 Node，没有 lint 这一步。库里每一项检查都有可以用眼睛走一遍的清单版本，比如 `mc-workflow` §3.0 的十七条规格自查、`mc-ai-tell-audit` 的十四条 AI 味旗标、每个 skill 末尾的「写完必过」。

**规则是判据，不是铁律。** 某条不过，要么改规格，要么写一句为什么这首歌就该这样。写了理由的偏离就是合法的选择；库里没有任何一条会卡住你去编译。

## 多语言支持

**一套源文件，运行时出任何语言。** skill 只写一遍，用中文写，你用什么语言问，agent 就用什么语言答。不需要开关，不需要另装，也没有分语言的插件。

skill 正文不会再做其他语言的版本，这是一个决定，不是疏漏。姊妹项目 [screenwriting-skills](https://github.com/jtydhr88/screenwriting-skills) 做过完整的英文版，合进去过，后来删掉了，当时说服大家的理由在这里同样成立：如果一个读不了中文的人该有一套译本，那读不了英文的人也该有，接下来就是日语、韩语、法语。本包 52 份文件，四种语言就是 208 份，各自独立漂移，而且没有任何东西告诉你哪一份已经过期。翻译本身是便宜的，真正的代价是分叉一次之后，再拒绝第二次分叉就没有站得住的理由了。

运行时是这样覆盖的：

- **输出语言跟随提问语言。** 用日语问就得到日语，编曲谱、段落名称、推理过程都用那种语言回来。
- **术语锚定原词。** 这个领域的词汇本来就是英语、意大利语、德语、日语混着的：*tension*、*avoid note*、*comping*、*build*、*drop*、落ちサビ、打ち込み。中文书里的说法本身也是译名，不同译者选的还不一样，所以原词跟着概念一起走。
- **没有对应译法的术语保留原形加注解。** 落ちサビ、符割り、類音階、十三辙 都是原词加一句简短解释。
- **你的谱和你的词保持它们自己的语言。** 用英语讨论一首中文歌是正常的。换的是对话的语言，作品不换。

这样放弃掉的是可审计性：除非你读中文，否则你读不到指令文件本身，只能读到 agent 对它的转述。这是真实的代价，也正是一套译本唯一能换来的东西。对一个价值建立在"每条规则都带可核对的出处与页码"之上的库来说，它不值四套永久维护负担。

**README 是另一回事**，它们做了翻译，因为篇幅短、内容稳定，而且是新来的人最先看到的东西。

## 分层

六层。上层只知道下层，每个 skill 都带一张边界表写明它**不管**什么，正是那张表挡住了风格层去悄悄改写通用和声。

```
L0 工作流   路由、ARR-SPEC 契约、混合分工、阶段表
L1 通用层   旋律 · 和声 · 进行 · 转调 · 节奏 · 曲式 · 对位
L2 编曲层   结构学 · 配器 · 织体 · 节奏组 · 音色设计 · 演唱 · 混音意图
L3 风格层   日式 · 都会流行与 R&B · 爵士 · 华语 · 电子舞曲 · 乐队 · 影视管弦 · 嘻哈
L4 执行层   编译到各后端 · 符号乐谱 · AI 味诊断
L5 语料层   用实测唱片给教材规则作证或证伪
```

### L0 入口

| Skill | 管什么 |
|---|---|
| `mc-workflow` | 路由与阶段表（立意 → 骨架 → 旋律和声 → 编制 → 演唱混音 → 编译 → 生成 → 验收）。它持有 **ARR-SPEC** 规范、空模板、后端能力档案、三份示例、十七条自查，以及**风格混合**规则：和声归谁签字、律动归谁、低频归谁 |

### L1 通用层（与风格无关）

| Skill | 管什么 | 主要来源 |
|---|---|---|
| `mc-melody` | 一条旋律在表达什么、怎么唱得上去：音域与常用音区、动机与展开、最高点的位置、副歌为什么要更高 | 勋伯格、卡库里斯、杨儒怀 |
| `mc-harmony` | 功能与色彩：三功能、终止式、重配和声而不过火、可用张力音与 avoid note，Levine 的和弦－音阶判据与日式流行实务并排保留 | 斯波索宾、Levine、卡库里斯、任达敏 |
| `mc-progressions` | 122 条进行速查，字母与级数双记，八大著名进行各自暗示什么曲式 | 日式进行语料、任达敏 |
| `mc-modulation` | 换调而接缝听起来是故意的：共同和弦、缓冲和弦路线、直上直下什么时候是偷懒 | 黄虎威、日式转调实务 |
| `mc-rhythm-groove` | 还没人演奏之前，节奏本身是什么：细分、切分、先现、推拉 | DeSantis、日式基础 |
| `mc-form` | 段落设计与常见模板、日式段落命名，以及"每段都是 8 的倍数"为什么是最容易被听出来的默认值 | 吴祖强、李吉提、杨儒怀 |
| `mc-counterpoint` | 真正独立的第二条线：四条互补轴、五种对位、声部进行检查、二重对位用来变化重复段 | 于苏贤、勋伯格、赵晓生 |
| `mc-development` | 一个动机怎么长成乐句、段落和整首：十种发展手法（重复、模进、扩充、减缩、裁截、变奏、宽放紧收、对位、新材料、再现），发展与堆砌的分界，以及它拥有的 `material` 与 `form[].development` 字段 | 杨儒怀、勋伯格《作曲基本原理》、李吉提 |

### L2 编曲层

| Skill | 管什么 | 主要来源 |
|---|---|---|
| `mc-arrangement-arch` | 一首歌在时间上的形状：能量曲线、减法事件、进退场、编曲记忆点。五条规则在 62 首实测里验过 | 文海良、实测语料 |
| `mc-orchestration` | 哪件乐器扛哪条线、纯音色还是混合音色、按音区与数量配平，**西洋管弦与中国民族管弦乐都有**（四个乐器组、分组结合、总谱顺序、移调乐器） | Adler、杨立青、杨春林、朱晓谷、李民雄 |
| `mc-texture-layering` | 把编曲当频谱预算来做：前景/中景/背景的纵深、临界频带才是掩蔽的真实单位、低音区两件乐器隔五度为什么还打架 | Izhaki、Howard & Angus |
| `mc-rhythm-section` | 鼓、贝司、和声乐器当一个整体：五步选鼓型（风格与 feel → 速度区间 → backbeat 位置 → 底鼓贝司关系 → 镲手细分），十一张带速度区间的风格表 | Drummer's Bible、Groove Essentials、DeSantis |
| `mc-sound-design` | 碰合成器之前先定这个声音必须是什么：99 条技法配方、先写简报的纪律 | 野崎貴朗 |
| `mc-vocal-direction` | 人声怎么唱、怎么堆：角色设定、逐段力度、音准策略（哪里允许不准）、和声类型与声像 | 日式人声制作实务 |
| `mc-mix-intent` | 一版混音必须达成什么，写成人类工程师、DAW 和生成后端都能执行的形态，不规定 EQ 和压缩怎么调 | Izhaki、Howard & Angus |

### L3 风格层

每个都带一张**移植表**：这个风格能借出去什么、什么搬走会变味。

| Skill | 它特有的是什么 | 主要来源 |
|---|---|---|
| `mc-style-jpop` | 以副歌为中心的设计纪律、落ちサビ、段落对比规则 | 日式编曲实务 |
| `mc-style-citypop-rnb` | 借来的爵士和声架在流行曲式上，加一组特定音色；示例规格单就是这个风格 | 实测语料、L1 和声 |
| `mc-style-jazz` | 什么是爵士特有的而不是通用乐理：head 形式、标准曲、comping、调式思维 | Levine、Valerio、爵士和声 |
| `mc-style-latin` | 拉丁是三个不能混的家族（Afro-Cuban、巴西、加勒比）：clave 这套语法（son／rumba、2-3／3-2）、谁守网格、bossa 的轻从哪来、打击件的音区纪律、移植表 | 《The Drummer's Bible》、Groove Essentials、藤谷、Jazz Guitar Groove |
| `mc-style-chinese-pop` | 华语与粤语流行：声调体系对编曲的约束、粤语层、年代差异 | 同一首歌语料、黄志华 |
| `mc-style-edm` | 靠加层减层撑起来的曲式：build、drop、张力装置 | 竹內一弘、野崎、实测语料 |
| `mc-style-rock-band` | 五个人在一间屋里要能演出来，什么就变了：编制是硬约束、riff 当主记忆点（Rooksby 的 30 型 riff、3+1 重复模式、riff 与人声的关系）、双吉他、drop D | Rooksby《Riffs》、实测语料 |
| `mc-style-cinematic` | 编曲层面的 hybrid 管弦、纵深与色彩设计 | Adler、杨立青 |
| `mc-style-hiphop` | 以 beat 为先的音乐：第三种时间感（Dilla）、逐件乐器量化、808 与底鼓、采样型织体与制作人手艺伦理、rap 与唱的分界 | Charnas《Dilla Time》、Schloss《Making Beats》、《How to Rap》1&2 |

### L4 执行与检验

| Skill | 管什么 |
|---|---|
| `mc-render-compile` | 一个编译器，多个后端。每个后端做得到什么做不到什么，作为**数据**放在 `backends.yaml` 里，它做不到的字段不会算在它头上。加一个模型是加一块数据，不用加 skill |
| `mc-symbolic-score` | ARR-SPEC 编译成 ABC、MusicXML、MIDI，同一个发射器。这条路上规格是被**执行**的，不是被"提示"的 |
| `mc-ai-tell-audit` | 十四条可枚举的机器指纹，逐条数。**两个问题不许混为一谈**：后端照着规格做了没有，和成品有没有机器指纹。前一个全过的作品照样可能满是 AI 味 |

### L5 语料层

| Skill | 管什么 |
|---|---|
| `mc-case-studies` | 怎么让唱片作证。62 首从多轨实测并反写成 ARR-SPEC，于是一条教材主张可以对着命中率核，而不是对着观点。**自然抽样不做策展**，策展会把语料偏向你已经相信的那些规则 |

## 包里有什么

只有纯文本：29 个 `SKILL.md`、放长表的 `reference.md`，以及 `mc-workflow/` 目录下六份规格文件：

| 文件 | 是什么 |
|---|---|
| `ARR-SPEC.schema.md` | 规格单定义，每个字段是什么、为什么存在、在对抗哪个默认值 |
| `ARR-SPEC.template.yaml` | 空模板，复制了就填 |
| `backends.yaml` | 各后端的能力声明 |
| `example-01-citypop.yaml` | 完整填好的规格单，十七条自查全过 |
| `example-02-fusion.yaml` | 风格混合：rap 架在中国风基底上，带分工表 |
| `example-00-ai-default.yaml` | 反面样本：没有引导时模型会写成什么样，以及它在哪八条上不过 |

## 底本书目

**配器与乐器法（10）**：阿德勒《配器法教程》上下；杨立青《管弦乐配器教程》上中下；Pejrolo & DeRosa《Acoustic and MIDI Orchestration for the Contemporary Composer》英文版与中译本《现代音乐人编曲手册》；杨春林《中国民族管弦乐配器法教程》；朱晓谷《民族管弦乐队乐器法》；李民雄《民族管弦乐总谱写法》

**和声·复调·乐理（11）**：斯波索宾《和声学教程》；勋伯格《和声学》《作曲基本原理》；Mark Levine《The Jazz Theory Book》；任达敏《流行音乐与爵士乐和声学》；于苏贤《复调音乐教程》；黄虎威《转调法》；理查生《调式及其和声法》；赵晓生《巴赫平均律键盘曲集结构分析版》；李重光《基本乐理》；《爵士和声》

**曲式与歌曲写作（9）**：吴祖强《曲式与作品分析》；李吉提《曲式与作品分析》；杨儒怀《音乐的分析与创作》上下；中央音乐学院《曲式与作品分析课程谱例集》；《论曲式与音乐作品分析》；朱敬修《歌曲写作基础》；卡库里斯《流行歌曲写作·旋律》《·和声》

**编曲·制作·混音（9）**：文海良《编曲》；野崎貴朗《圖解合成器音樂創作法》；竹內一弘《圖解電子音樂創作法》；藤谷一郎《圖解樂風編曲入門》；熊川浩孝等《图解编曲入门》；Izhaki《混音指南》；黄承箱、刘希望《音乐制作人白金手册》；DeSantis《Making Music》；Allen《Music Theory for Electronic Music Producers》

**节奏·风格·类型（9）**：Berry & Gianni《The Drummer's Bible》；Tommy Igoe《Groove Essentials》；Rooksby《Riffs》；Charnas《Dilla Time》；Schloss《Making Beats》；Edwards《How to Rap》1&2；Valerio《Bebop Jazz Piano》；《爵士吉他即兴演奏·节奏律动》

**声学（1）**：Howard & Angus《音乐声学与心理声学》

**粤语与华语流行史（1）**：黄志华《粤语流行曲四十年》

**语料**：Cambridge-MT 多轨库（62 首实测并反写成 ARR-SPEC）、《同一首歌》与中文吉他谱集（做曲式与进行统计）

## 体例

- `SKILL.md` frontmatter：`name`（短横线小写，与目录名一致）＋一段较长的英文 `description`，以 "Use when …" 收尾，并附中文关键词，两种语言都能路由。
- 正文中文；带编号的原则、表格、清单。输出语言跟随提问语言。
- **每条规则都带失效条件或自检动作。** 测不了的规则只是意见。
- 两家说法不一致时并排保留，并注明各自适用场合，例如 Levine 的和弦－音阶判据对日式流行实务，Drummer's Bible 给速度区间对 Groove Essentials 故意不标数字。
- 和弦一律双记：字母记号**＋**罗马数字级数，默认 Key=C。
- 引用标出处与页码。只靠单一来源的论断会写明。
- `reference.md` 放长表与详例，让 `SKILL.md` 保持在 40 KB 以内。
- 设计上与 agent 无关：skill 文件不提任何 agent 的名字，不用任何 agent 专有语法。

## 授权

skill 本身是 MIT，见 [LICENSE](LICENSE)。底本引文不在其列，版权仍属原作者与译者，见 [NOTICE](NOTICE)。

---

姊妹项目，同一套做法用在编剧上：[screenwriting-skills](https://github.com/jtydhr88/screenwriting-skills)。

本库取代 [japanese-composition-skills](https://github.com/jtydhr88/japanese-composition-skills)，那 11 个 skill 的内容已并入这里的通用层与风格层。
