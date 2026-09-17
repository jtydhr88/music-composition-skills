# music-composition-skills

English · [中文版](README_ZH.md) · [日本語](README_JA.md) · [한국어](README_KO.md)

29 agent skills (for [Claude Code](https://docs.anthropic.com/en/docs/claude-code/skills) and [OpenAI Codex](https://developers.openai.com/codex/)) for composing and arranging popular music, distilled from 50 books on orchestration, harmony, form, counterpoint, mixing and style practice, plus 62 arrangements measured from multitrack recordings.

The `SKILL.md` files follow the open [agentskills.io](https://agentskills.io) standard and are shared by both agents: install once and it works in both.

**The output is a spec sheet, not advice.** Ask for a song and the agent fills in an ARR-SPEC — key, tempo, bar-accurate section map, chord letters and roman numerals, who enters and leaves when, the energy curve, vocal delivery, mix intent — and then compiles that one document into a Suno prompt, an ABC score for YuE2, or MIDI. The same document is what you check the result against. That is the whole design: **one artifact, four uses** — handoff, controlled A/B, compile source, acceptance baseline.

**Ask in your own language.** The skill bodies are written in Chinese, because most of the sources are Chinese originals or Chinese translations. The agent answers in whatever language you ask in.

**These skills do not make the generation model faster; they trade time for quality.** A full pass through a professional music workflow often takes twenty minutes or more for a single song (the agent reads the criteria, fills in the spec, runs the checklists). What you get in return is a professional-grade spec, not a few lines of prompt.

**This library ships no musical works at all, neither the author's own nor any other artist's, no arrangements and no lyrics.** Whether a piece of music is good is a subjective call. Use it yourself and judge for yourself what it is worth, and what it costs you in time.

## The four libraries in this series

One audio line, cut into four independent libraries. Each one is usable on its own; the seams between them are written down, so they also combine.

| Library | What it does | State |
|---|---|---|
| **[music-composition-skills](https://github.com/jtydhr88/music-composition-skills)** | Composing and arranging. A brief becomes an ARR-SPEC: key, tempo, section map, harmony, who enters and leaves when, the energy curve, vocal and mix intent, compiled into input for Suno, YuE2 or MIDI | **This library** |
| **[lyric-writing-skills](https://github.com/jtydhr88/lyric-writing-skills)** | Lyric writing. The intent gate becomes a LYR-SPEC: structure, imagery, rhyme, narrative, plus the language layer for Mandarin, Cantonese, English, Japanese and Korean | Available |
| **Film scoring** | Music written to picture: spotting, the relation to cuts, how a cue is written and named, how a theme develops across a film | Planned |
| **Mixing and sound design** | Turning mix intent into actual moves: EQ, compression, reverb, automation, mastering | Planned |

**Independent.** Filling words to an existing melody needs the lyric library only. Arranging an instrumental needs the composition library only.

**Combinable, through named handoffs.** ARR-SPEC and LYR-SPEC are bidirectional: music-first hands over the section map and bar counts, lyrics-first hands back line lengths and stress positions, and six fields must agree in both directions. `mix_intent` inside an ARR-SPEC is what the mixing library will take as its brief. A cue sheet from the scoring library is what the composition library will take as a brief.

**The seams are already written.** Every skill carries a boundary table, and questions that belong to another library are routed there by name rather than answered badly in place. That is why the two planned libraries are already referred to throughout: EQ and compression practice leaves for the mixing library, spotting and cue writing leave for the scoring library.

## Why this exists alongside a generative model

Arranging is a craft with decades of accumulated practice behind it. When to subtract before a lift, why a chorus sits higher, who owns the low end, whether the second verse should be emptier or fuller than the first: these judgements are written down across dozens of books, in several languages and traditions. **This library turns them into the decisions a professional arranger would make, in a form a generation model can be told.**

Suno and YuE2 will already turn one sentence into a finished-sounding track. What they are missing is those decisions. Whatever you leave unspecified, the model supplies from its own defaults, which is why unguided output tends to come back sounding like the same producer every time.

So what this library hands over is the decisions themselves: which bar the guitar leaves, which section drops a layer before the lift, where the energy falls, what the vocal does differently on the third chorus. **The point is to direct a generation model with expert-level rules. There is no attempt here to rebuild one.**

What that buys you is the part prompt engineering cannot reach. A prompt is a sentence, and you cannot check a sentence field by field. A spec sheet can be compared against what came back, changed one field at a time, and re-run for a controlled comparison. Every decision in it carries a reason and a source, so six months later you can still say why the second chorus is arranged the way it is.

The model stays the performer and the studio. This is the arranger's chart handed to it.

## Install

### Claude Code

#### Plugin marketplace (recommended)

```
/plugin marketplace add jtydhr88/music-composition-skills
/plugin install music-composition@music-composition-skills
```

Skills are then invoked as `/music-composition:<skill>`, e.g. `/music-composition:mc-workflow`.

#### Personal (all projects)

```bash
git clone https://github.com/jtydhr88/music-composition-skills.git
cp -r music-composition-skills/plugins/music-composition/skills/* ~/.claude/skills/
```

#### Project-specific

```bash
mkdir -p .claude/skills
cp -r music-composition-skills/plugins/music-composition/skills/* .claude/skills/
```

### Codex (CLI / ChatGPT desktop app / IDE extension)

```bash
codex plugin marketplace add jtydhr88/music-composition-skills
# Then: Plugins → select "Music Composition Skills" → Install
```

Or copy the same files to `~/.agents/skills/` (personal) or `.agents/skills/` (project). The same 29 `SKILL.md` files are shipped through both plugin directories, with no duplication and no rewriting.

#### Verify

Skills load automatically when the agent detects relevant context. To list them: `/skills`.

## Usage examples

```
# "Write me a city pop track about the last train home, three and a half minutes,
#  and give me everything I need to paste into Suno"
# → mc-workflow → mc-style-citypop-rnb → mc-arrangement-arch → mc-render-compile

# "This chorus doesn't lift. Fix the arrangement, not the melody"
# → mc-arrangement-arch (energy curve, subtraction before the lift) + mc-texture-layering

# "Rap verses over a Chinese-style chorus, like a named song"
# → mc-workflow §2.4 fusion → mc-style-hiphop (base) + mc-style-chinese-pop (overlay)

# "The generated track sounds like AI but I can't say why"
# → mc-ai-tell-audit: fourteen enumerated tells, counted one by one

# "What drum pattern and tempo does a bossa nova need?"
# → mc-rhythm-section: eleven style tables, then the five-step pattern decision
```

## Common combinations

This is more involved than it looks. A one-sentence brief pulls in a dozen skills across composition, arrangement and lyrics, and what the agent reads and produces depends on whether that sentence names a few key things. Below are combinations that have actually been run end to end: how to say it, which skills it walks, what comes out. Anything with lyrics needs both the music library and the lyric library installed.

**1. A song with lyrics in the style of a named artist** (the most common case)

> Write a Mandarin pop song in the style of a named artist's song, female vocal, to be generated on Suno, with lyrics.

Walks: lw-workflow → lw-song-intent → lw-structure → lw-mandarin → lw-tone-check → lw-suno-interface; mc-workflow → mc-style-chinese-pop → mc-melody → mc-development → mc-arrangement-arch → mc-vocal-direction → mc-render-compile
Produces: LYR-SPEC.yaml, ARR-SPEC.yaml, suno-pack.md (title, style description, section-tagged lyrics, exclusions)
Note: the reference artist lends instrumentation, tempo, vocal persona and section layout. Never words or melody.

**2. A Douyin-style earworm, no storytelling**

> Write a Douyin earworm, female, sweet, no storytelling. Suno, with lyrics.

Walks: lw-song-intent (attitudinal) → lw-structure (repetition budget, hook placement) → lw-rhyme → lw-mandarin; mc-style-chinese-pop → mc-rhythm-groove → mc-arrangement-arch → mc-render-compile
Note: "no storytelling" is what sets the lyric type. Leave it out and you get a situational lyric by default.

**3. A sad love song that says it straight, no scenery**

> Write a sad love song like a named artist's ballad, male vocal, Suno, with lyrics. The kind that says it directly, no scene description.

Walks: as in 1, but lw-song-intent takes the attitudinal route and the anchor is a sentence (the line that opens every chorus) rather than an object; mc-vocal-direction handles the close-mic male voice and the bare final line
Note: in the corpus, four in ten attitudinal songs contain nothing you could photograph. The chorus is one sentence said to "you", the verses state how I am, the opener is a monologue. To get that, say "say it directly, no scenes, no objects" in the brief.

**4. A feeling, not a story**

> Write a Mandarin pop song, three and a half minutes, female vocal. The subject: on moving day I left the charger cable in the wall socket, walked two blocks before remembering, and did not go back. Not a story. I want the feeling of remembering and letting it go.

Walks: lw-song-intent (situational, anchor object = the cable) → lw-imagery → lw-structure → lw-mandarin → lw-tone-check; music side as in 1
Note: this is the line between situational and narrative. There is an object; there is no plot advancing.

**5. An instrumental, composed from nothing**

> Write a two-minute-forty instrumental as a short-video bed. Lead is a nylon-string guitar, a little percussion and bass, one keyboard allowed. The feel is "three in the afternoon, waiting for someone, no hurry". No vocals.

Walks: mc-workflow → mc-development (motif, character, development devices) → mc-melody → mc-form → mc-harmony → mc-rhythm-section → mc-orchestration → mc-render-compile
Produces: ARR-SPEC.yaml (material block and per-section development filled), suno-pack.md, a one-page note
Note: no lyrics, so the lyric library stays out. The criterion lives in mc-development: new material under half, and a return.

**6. Melody and words exist, arrangement only**

> Arrange a Mandarin love song whose melody and lyrics are already written. I want a salsa feel, with brass, medium-fast. Spec only, no Suno prompt.

Walks: mc-workflow (arrangement route) → mc-style-latin → mc-rhythm-section → mc-orchestration (brass) → mc-arrangement-arch → mc-texture-layering
Produces: ARR-SPEC.yaml and a one-page note, nothing else

**7. Fusion: rap verses, Chinese-style chorus**

> Write a song like a named song, rap plus Chinese style, full Chinese lyrics, for Suno.

Walks: mc-workflow §2.4 fusion → mc-style-hiphop (base) + mc-style-chinese-pop (overlay); lw-rap (verses) + lw-chinese-style (chorus) + lw-mandarin

**8. Cantonese lyric to a given melody**

> Fill this melody with Cantonese lyrics. Bars and syllables per line are attached.

Walks: lw-workflow (music-first) → lw-cantonese (compute by pitch class first, then choose characters) → lw-tone-check
Note: Cantonese writes in the reverse order, and the Mandarin layer cannot stand in for it.

**9. Not Suno: a local open-source model (YuE2)**

> Same song, not through Suno. Generate it with local YuE2 and compile the spec into an ABC score for me.

Walks: as in 1, plus mc-symbolic-score (compiles ARR-SPEC to ABC, vocal line in the lowercase note register, with w: lyric lines)
Note: experimental. The share of lyrics that come out sung is currently below the plain style-plus-lyrics path; details in mc-symbolic-score §6b.

The sentences in a brief that actually do something:

| What you say | What it decides |
|---|---|
| artist + song title | instrumentation, tempo, vocal persona, section layout are borrowed; words and melody never are |
| female / male, sweet / close-mic | persona and range in mc-vocal-direction |
| Suno / local YuE2 / spec only | the exit: suno-pack, ABC score, or ARR-SPEC alone |
| no storytelling / say it straight, no scenes / not a story | lyric type: attitudinal / attitudinal without objects / situational. Unsaid, situational is the default |
| duration | section count and bar count are derived from it |
| a list of deliverables | name the files you want and the agent produces exactly that list; one missing means not done |

Each of these takes twenty minutes or more to run in full, as described under "time for quality" above.

## No tooling required

The plugin is plain text. There is no script to run, no Python, no Node, no lint step. Every check in the library exists as a checklist you or the agent can walk through by eye — the twenty-point spec self-check in `mc-workflow` §3.0, the fourteen AI-tell flags in `mc-ai-tell-audit`, the per-skill "must pass before you finish" lists.

The rules are criteria, not gates. When a check does not pass, you either change the spec or write one line saying why this song should be that way. A deviation with a stated reason is a legitimate choice; nothing in the library blocks you from compiling.

## Multilingual support

**One source tree, every language at runtime.** The skills are written once, in Chinese, and the agent answers in whatever language you asked in. No flag, no separate install, no per-language plugin.

The skill bodies will not be forked into other languages, and that is a decision rather than an omission. The sister project [screenwriting-skills](https://github.com/jtydhr88/screenwriting-skills) built a full English edition, merged it, then removed it, and the argument that killed it applies here unchanged: if a reader who cannot read Chinese deserves a translated tree, so does a reader who cannot read English, and the next request is Japanese, then Korean, then French. This pack is 52 files. Four languages makes 208 of them, drifting apart independently, with nothing to tell you which one is stale. Translating is the cheap part. The real cost is that forking once removes any principled ground for refusing the second fork.

How the runtime covers it instead:

- **Output language follows your question.** Ask in Japanese, get Japanese. The chart, the section names and the reasoning all come back in that language.
- **Terminology is anchored to the original term.** This field's vocabulary is a mix of English, Italian, German and Japanese in the original: *tension*, *avoid note*, *comping*, *build*, *drop*, 落ちサビ, 打ち込み. The Chinese in the source books is itself a translation, and different translators chose differently, so the original term travels with the concept.
- **Terms with no equivalent keep their original form plus a gloss.** 落ちサビ, 符割り, 類音階 and 十三辙 come through as the original plus a short explanation.
- **Your score and your lyrics stay in their own language.** Discussing a Chinese song in English is normal. The conversation switches language; the work does not.

What this trades away is auditability: unless you read Chinese, you cannot read the instruction file itself, only the agent's account of it. That is a real cost, and it is the one thing a translated tree would genuinely buy. It is not worth a permanent four-way maintenance burden on a library whose value lies in every rule carrying a verifiable source and page.

**READMEs are a different matter** and are translated, because they are short, stable, and the first thing a newcomer meets.

## How the skills are organised

Six layers. A layer only knows about the layers below it, and every skill carries a boundary table saying what it does **not** own — that table is what keeps a style layer from quietly rewriting general harmony.

```
L0 workflow      router, the ARR-SPEC contract, fusion ownership, the stage map
L1 general       melody · harmony · progressions · modulation · rhythm · form · counterpoint
L2 arrangement   architecture · orchestration · texture · rhythm section · sound design · vocal · mix intent
L3 style         J-pop · city pop & R&B · jazz · Mandarin pop · EDM · rock band · cinematic · hip-hop
L4 execution     compile to backends · symbolic score · AI-tell audit
L5 corpus        measured recordings that testify for or against the textbook rules
```

### L0 — entry point

| Skill | What it covers |
|---|---|
| `mc-workflow` | Router and stage map (intent → skeleton → melody & harmony → roster → vocal & mix → compile → generate → accept). Owns the **ARR-SPEC** schema, the empty template, the backend capability file, three worked examples, the twenty-point self-check, and the **style fusion** rules: which skill signs for harmony, which for groove, which for low end |

### L1 — general craft (style-independent)

| Skill | What it covers | Main sources |
|---|---|---|
| `mc-melody` | What a line expresses and how to make it singable: range and tessitura, motif and development, peak placement, the reason a chorus sits higher | Schoenberg, Kachulis, 杨儒怀 |
| `mc-harmony` | Function and colour: the three functions, cadence types, reharmonisation without over-cooking, available tensions and avoid notes with Levine's chord-scale judgement kept beside the Japanese pop practice | 斯波索宾, Levine, Kachulis, 任达敏 |
| `mc-progressions` | 122 named progressions with letters and roman numerals, the eight famous ones, what each implies about form | Japanese progression corpus, 任达敏 |
| `mc-modulation` | Moving between keys so the seam sounds intended: pivot chords, the buffer-chord route, direct lifts and when they are cheap | 黄虎威, Japanese modulation practice |
| `mc-rhythm-groove` | Rhythm as material before anyone plays it: subdivision, syncopation, anticipation, push and pull | DeSantis, Japanese basics |
| `mc-form` | Section design and the standard templates, Japanese section naming, why every section being a multiple of eight is the most audible default | 吴祖强, 李吉提, 杨儒怀 |
| `mc-counterpoint` | A second line that is genuinely independent: four complementarity axes, the five species, voice-leading checks, invertible counterpoint for varying a repeat | 于苏贤, Schoenberg, 赵晓生 |
| `mc-development` | How one motif grows into a phrase, a section and a whole song: the ten development devices (repeat, sequence, extend, contract, fragment, vary, augment, counterpoint, new, recap), the line between developing and piling up, and the `material` / `form[].development` fields it owns | Yang Ruhuai, Schoenberg *Fundamentals*, Li Jiti |

### L2 — arrangement craft

| Skill | What it covers | Main sources |
|---|---|---|
| `mc-arrangement-arch` | The shape of a track over time: energy curve, subtraction events, entry and exit, the arrangement hook. Five rules verified against 62 measured recordings | 文海良, measured corpus |
| `mc-orchestration` | Which instrument carries which line, pure versus mixed timbre, balance by register and count — western orchestra **and** Chinese folk orchestra (four sections, group combination, score order, transposing instruments) | Adler, 杨立青, 杨春林, 朱晓谷, 李民雄 |
| `mc-texture-layering` | Arranging as a space budget: foreground/middle/background depth, the critical band as the real unit of masking, why two instruments a fifth apart in the low register fight | Izhaki, Howard & Angus |
| `mc-rhythm-section` | Drums, bass and harmony instruments as one unit: the five-step pattern decision (style & feel → tempo band → backbeat position → kick-bass relation → cymbal subdivision), eleven style tables with tempo ranges | Drummer's Bible, Groove Essentials, DeSantis |
| `mc-sound-design` | Deciding what a sound must be before touching a synth: 99 technique recipes, the brief-first discipline | 野崎貴朗 |
| `mc-vocal-direction` | How a vocal should be delivered and stacked: persona, per-section dynamics, pitch policy (where it is allowed to be imperfect), harmony types and panning | Japanese vocal production practice |
| `mc-mix-intent` | What a mix must achieve, stated so a human engineer, a DAW or a generative backend can all act on it — without prescribing EQ or compression | Izhaki, Howard & Angus |

### L3 — style layers

Each one carries a **transfer table**: what this style can lend to others, and what does not travel.

| Skill | What is specific to it | Main sources |
|---|---|---|
| `mc-style-jpop` | Chorus-centred design discipline, 落ちサビ, the section-contrast rules | Japanese arranging practice |
| `mc-style-citypop-rnb` | Borrowed jazz harmony over pop form plus a specific timbre set; the worked example spec is this style | measured corpus, L1 harmony |
| `mc-style-jazz` | What is jazz-specific rather than general theory: head form, standards, comping, modal thinking | Levine, Valerio, 爵士和声 |
| `mc-style-latin` | Latin as three families that must not be blended (Afro-Cuban, Brazilian, Caribbean): the clave grammar (son/rumba, 2-3/3-2), who holds the grid, where bossa's lightness comes from, the percussion register discipline, and a transfer table | The Drummer's Bible, Groove Essentials, Fujitani, Jazz Guitar Groove |
| `mc-style-chinese-pop` | Mandarin and Cantonese pop: what the tone system does to arrangement, the Cantonese layer, era differences | 同一首歌 corpus, 黄志华 |
| `mc-style-edm` | Form built on layer addition and subtraction: build, drop, tension devices | 竹內一弘, 野崎, measured corpus |
| `mc-style-rock-band` | What changes when five people in a room must play it: the roster as a hard constraint, riff as primary hook (Rooksby's 30 riff types, the 3+1 repetition pattern, riff versus vocal), twin guitars, drop D | Rooksby *Riffs*, measured corpus |
| `mc-style-cinematic` | Hybrid orchestral scoring at the arrangement level, depth and colour design | Adler, 杨立青 |
| `mc-style-hiphop` | Beat-first music: the third time-feel (Dilla), per-voice quantisation, 808 versus kick, sample-based texture and the producers' craft ethics, rap versus sung delivery | Charnas *Dilla Time*, Schloss *Making Beats*, *How to Rap* 1 & 2 |

### L4 — execution and verification

| Skill | What it covers |
|---|---|
| `mc-render-compile` | One compiler, many backends. What each backend can and cannot honour lives in `backends.yaml` as data, so a field the backend cannot act on is never held against it. Adding a model means adding a data block, not a skill |
| `mc-symbolic-score` | ARR-SPEC to ABC, MusicXML and MIDI from one emitter. This is the path where the spec is **executed** rather than hinted at |
| `mc-ai-tell-audit` | Fourteen enumerable tells of machine-made music, counted individually. Keeps two questions apart on purpose: *did the backend do what the spec said*, and *does the result carry machine fingerprints*. A track can pass the first and fail the second |

### L5 — corpus

| Skill | What it covers |
|---|---|
| `mc-case-studies` | How recordings are made to testify. 62 arrangements measured from multitrack sources and written back as ARR-SPECs, so a textbook claim can be checked against a hit rate instead of an opinion. Natural sampling, not curation — curating the corpus would bias it toward the rules you already believe |

## What ships in the package

Plain text only: 29 `SKILL.md` files, the `reference.md` files that hold the long tables, and six specification files inside `mc-workflow/`:

| File | What it is |
|---|---|
| `ARR-SPEC.schema.md` | The spec sheet definition — every field, what it is for, what it is fighting |
| `ARR-SPEC.template.yaml` | Empty template to copy |
| `backends.yaml` | Per-backend capability declarations |
| `example-01-citypop.yaml` | A complete worked spec, all twenty checks passing |
| `example-02-fusion.yaml` | Style fusion: rap over a Chinese-style base, with the ownership table |
| `example-00-ai-default.yaml` | The counter-example: what a model writes with no guidance, and which eight checks it fails |

## Source books

**Orchestration and instrumentation (10):** Samuel Adler *The Study of Orchestration* vols 1–2; 杨立青《管弦乐配器教程》上中下; Pejrolo & DeRosa *Acoustic and MIDI Orchestration for the Contemporary Composer*, English edition and the Chinese translation《现代音乐人编曲手册》; 杨春林《中国民族管弦乐配器法教程》; 朱晓谷《民族管弦乐队乐器法》; 李民雄《民族管弦乐总谱写法》

**Harmony, counterpoint and theory (11):** 斯波索宾《和声学教程》; Schoenberg *Theory of Harmony* and *Fundamentals of Musical Composition*; Mark Levine *The Jazz Theory Book*; 任达敏《流行音乐与爵士乐和声学》; 于苏贤《复调音乐教程》; 黄虎威《转调法》; Madeleine Richardson *Modes and their Harmony*; 赵晓生《巴赫平均律键盘曲集结构分析版》; 李重光《基本乐理》; 爵士和声（流行音乐学院系列）

**Form and songwriting (9):** 吴祖强《曲式与作品分析》; 李吉提《曲式与作品分析》; 杨儒怀《音乐的分析与创作》上下; 中央音乐学院《曲式与作品分析课程谱例集》; 《论曲式与音乐作品分析》; 朱敬修《歌曲写作基础》; Jimmy Kachulis *The Songwriter's Workshop: Melody* and *Harmony*

**Arranging, production and mixing (9):** 文海良《编曲》; 野崎貴朗《圖解合成器音樂創作法》; 竹內一弘《圖解電子音樂創作法》; 藤谷一郎《圖解樂風編曲入門》; 熊川浩孝等《图解编曲入门》; Roey Izhaki *Mixing Audio*; 黄承箱、刘希望《音乐制作人白金手册》; Dennis DeSantis *Making Music*; J. Anthony Allen *Music Theory for Electronic Music Producers*

**Rhythm, style and genre (9):** Berry & Gianni *The Drummer's Bible*; Tommy Igoe *Groove Essentials*; Rikky Rooksby *Riffs*; Dan Charnas *Dilla Time*; Joseph Schloss *Making Beats*; Paul Edwards *How to Rap* 1 & 2; John Valerio *Bebop Jazz Piano*; 爵士吉他即兴演奏·节奏律动

**Acoustics (1):** Howard & Angus *Acoustics and Psychoacoustics*

**Cantonese and Chinese pop history (1):** 黄志华《粤语流行曲四十年》

**Corpus:** Cambridge-MT multitrack library (62 arrangements measured and written back as ARR-SPECs), 《同一首歌》and Chinese guitar-chart collections for form and progression statistics

## Conventions

- `SKILL.md` frontmatter: `name` (kebab-case, matches the folder) and a long English `description` ending in "Use when …", with Chinese keywords appended so routing works in both languages.
- Skill bodies are Chinese; numbered principles, tables and checklists. Output language follows the user's question.
- **Every rule carries a failure condition or a self-check action.** A rule you cannot test is an opinion.
- Where the sources disagree, both positions are kept side by side with a note on when each applies — Levine's chord-scale judgement against the Japanese pop practice, the Drummer's Bible tempo bands against Groove Essentials' refusal to print numbers.
- Chords are always written twice: letter symbols **and** roman numerals, default key C.
- Citations carry the source and page. Claims that rest on a single source say so.
- `reference.md` holds long tables and worked examples so `SKILL.md` stays under 40 KB.
- Agent-neutral by design: the skill files name no agent and use no agent-specific syntax.

## License

MIT for the skills themselves, see [LICENSE](LICENSE). Quotations from the source books are not covered by it and remain the property of their authors and translators — see [NOTICE](NOTICE).

---

Sister project, same idea applied to screenwriting: [screenwriting-skills](https://github.com/jtydhr88/screenwriting-skills).

This library supersedes [japanese-composition-skills](https://github.com/jtydhr88/japanese-composition-skills); its eleven skills were absorbed into the general and style layers here.
