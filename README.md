# Research Workbench

> **EN**: An AI research advisor toolkit — from topic intake to peer review. Framework-agnostic prompts for any LLM agent. Never ghostwrites, never predicts acceptance odds.
> **中**: AI 学术研究导师工具包——从写作方向确认到模拟审稿。框架无关的 Prompt 集合，适用于任何 LLM Agent。绝不代写，也不预测录用概率。

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

---

## What is this?

A collection of **structured AI prompts** (skills) that turn any LLM into an academic research advisor:
direction intake → deep research (with a capability probe) → gated experiment design → claim-forward outline
→ citation audit → bidirectional claim calibration → blinded multi-reviewer simulation.

一组**结构化 AI 提示词**，把任何 LLM 变成学术导师：
启动问询 → 深度研究（先探能力）→ 门禁后实验设计 → 主张先行骨架 → 引用审查 → 双向声明校准 → 脱盲多视角审稿。

### Core Philosophy

**Research advisor, not ghostwriter.** It will never produce paragraphs you can paste into a paper. Every output is a plan, a diagnostic, a revision point. You remain the author.

**研究导师，不是代笔人。** 永远不产出可以粘贴进论文的段落。所有输出是规划、诊断、修改点。你始终是作者。

**It stops instead of faking.** No deep-research tool → it halts and hands you a copy-paste research prompt, rather than passing model memory off as a literature survey.

**能力不足时会停下来。** 本机/本 Agent 没有深度研究能力就直接中断并给出交接 prompt，不用记忆冒充检索。

---

## What's new in v2.1.0

| Change | Why |
|--------|-----|
| Stage 0 mandatory intake (writing direction + existing research line + materials + target journal) | Without the direction confirmed, every downstream stage guesses |
| Deep-research capability probe with **hard stop** |选题判断需要多轮检索+交叉验证；单次或多轮普通网页搜索都不够，假装做过更糟 |
| Stage 1→2 gate `G1/G2/G3` (confirmed gap · concrete next step · direction compared) | Experiment plans built on an unconfirmed gap look complete but are misdirected |
| New skill `反防御写作` (claim-forward) applied to outline + polish | Over-defensive writing turns papers into self-audit reports; claim strength is still capped by evidence |
| Manuscript intake asks for target journal first; if none, web-search a shortlist of verifiable fields | Reviewer calibration and format checks are meaningless without a venue |
| Reviewers run as **isolated subagents** (git worktree preferred) | The main chat has already seen author intent and revision history — it cannot be truly blind |
| Review output = `P0-P3` blockers + counts | Actionable triage instead of vibes |
| **No acceptance probabilities, no decision tiers** (no ACCEPT/MAJOR REVISE, no percentages) | A simulation cannot predict an editorial decision |

---

## Quick Start

### 1. Clone

```bash
git clone https://github.com/xuzeTop1/research-workbench.git
cd research-workbench
```

### 2. Use with your AI agent

Pick your platform below. All methods use the same prompt files.

---

## Platform Guide

### Cursor / Windsurf / any IDE with rules file

```bash
cp BOOTSTRAP.md .cursorrules
# or
cp BOOTSTRAP.md .windsurfrules
```

Then open the `research-workbench/` folder as your workspace. The AI reads `skills/*/SKILL.md` on demand when you invoke stages.

### Cline (VS Code extension)

Paste `BOOTSTRAP.md` content into Cline's **Custom Instructions** (Settings → Prompts → Custom Context). Add the `skills/` folder to your project context.

### Claude (Web / Desktop / API)

- **Claude Projects**: Upload `BOOTSTRAP.md` as project knowledge + attach relevant `SKILL.md` files per conversation.
- **API**: Use `BOOTSTRAP.md` as system prompt, load skill files into `user` messages as needed.

```python
# Example: Claude API
system = open("BOOTSTRAP.md").read()
user_turn_1 = open("skills/论文骨架/SKILL.md").read()
# → Stage 0 intake, then capability probe, then Stage 1
```

### ChatGPT / any chat LLM

1. Paste `BOOTSTRAP.md` as your **first message** (or into Custom Instructions)
2. When entering a new stage, paste the corresponding `SKILL.md` file content
3. Answer the four intake questions (direction / existing line / materials / journal)
4. If the agent reports `deep_research_mode: absent`, run the handoff prompt in a
   deep-research-capable product and paste the result back as `deep-research-handoff.md`

### Qoder (skills) / Claude Code / Codex skills

Copy each skill folder into your agent's skills directory (e.g. `%USERPROFILE%\.qoder-cn\skills\`),
or the whole plugin into `~/.qoderworkcn/plugins-custom/`. Stage 6 review uses subagents when the
host provides them, with an isolated git worktree per reviewer; on hosts without subagents it falls
back to three separate fresh sessions. Invoke via `/写作工坊`.

### Aider / Open Interpreter / custom agent frameworks

Load `BOOTSTRAP.md` as system instructions. Configure file read access to the `skills/` directory so the agent can progressively load stage-specific rules.

---

## Usage Example

```
You: [paste BOOTSTRAP.md content as first message]

AI:  [Adopts Research Advisor role] 先确认四件事：①写作方向 ②你/课题组现有研究方向
     ③手头材料 ④目标期刊（没有就说"未定"）。

You: 方向是"面向端侧资源的个性化混合检索"；是现有方向的延续；有 2 个数据集和部分结果；刊未定。

AI:  [Capability probe] 本 Agent 未检测到深度研究工具（只有单次网页搜索）。
     ⛔ Stage 1 中断。请用下面这段交接 prompt 在带 deep research 的平台跑一次，
     把结果存为 deep-research-handoff.md 后说"继续"：
     「研究主题…请回答四件事，每条结论附来源…」

You: [runs it elsewhere, pastes the file] 继续

AI:  [Builds venue profile + gap list + competitor table + comparison with your
      existing line] Gate: G1 ✅ G2 ✅ G3 ✅ → 下一步动作：在 [数据集B] 上补 [方法Y] 对照。
     → Stage 2: literature (every entry `[需用户核验]`), datasets/baselines/metrics/ablations,
      each experiment carrying one argument duty.
     → Stage 3: outline.md, section entries as claim → evidence → scope; flags both
      `[⚠️过度声称]` and `[⚠️过度防御]`.

You: [writes the paper offline for 2 weeks] 我写完了，帮我查引用。

AI:  先确认目标期刊——未定则我用联网搜索给 3-5 个候选（只填可核查字段，不编 IF/分区）。
     [After you pick] 6-dimension citation audit → problem list with `[需用户决策]` tags.

You: 改好了，审稿一轮。

AI:  [Web-search calibration: similar papers in that venue + published reviewer criteria]
     [3 isolated blinded reviewer subagents, each sees only the manuscript snapshot]
     → Round 1 blockers: P0 3 · P1 5 · P2 4 · P3 2.
     P0-1 §5.3 表4：正文称跨数据集一致提升，表中数据集C的 NDCG 下降 → 缩小主张或补机制分析。
     （不预测录用结果，不给档位判定。修改后说"再审"。）
```

---

## Architecture

```
BOOTSTRAP.md          ← Single entry: role + rules + gated workflow
skills/
├── 写作工坊/          ← Orchestrator: intake, probe, gates, journal confirmation, review loop
├── 论文骨架/          ← Stage 1 + 3: capability probe & handoff prompt, venue profile, gate, outline
├── 科研写作/          ← Writing doctrine: editing funnel, given→new, section recipes, venue picking
├── 反防御写作/        ← Claim-forward narrative rules with an evidence-strength ceiling
├── 实验设计/          ← Stage 2: entry gate, literature pools, per-experiment argument duty
├── 论文润色/          ← Stage 5: bidirectional claim calibration (diff suggestions)
├── 引用审查/          ← Stage 4: citation audit (6 dimensions, venue-dependent format check)
└── 模拟审稿/          ← Stage 6: blinded subagent reviewers, web-search calibration, P0-P3 blockers
```

Each `SKILL.md` is a self-contained prompt — readable by any LLM, no code dependencies.
The system works through conversation + file read/write; subagent-based review is used when the
host provides it and degrades to explicit, labelled fallbacks when it does not.

---

## Guardrails

| Rule | Enforcement |
|------|-------------|
| No ghostwriting | Refuses "write this paragraph for me"; returns argument structure instead |
| Citations need verification | Every recommendation tagged `[needs user verification]` with source + date |
| Venue facts need verification | Impact factor / quartile / limits / turnaround only as retrieved, else "not found — verify yourself" |
| No fabricated data | Never outputs numbers that did not come from your actual experiments |
| Capability honesty | Hard stop when deep research is unavailable; never auto-degrades without your instruction |
| Gates over momentum | No experiment plan before a sourced gap and a concrete next step |
| Two-way wording calibration | Flags over-claiming **and** over-defensive / self-weakening phrasing |
| Review = blockers only | Location + problem type + missing evidence + direction; never replacement text |
| Review = blinded | Independent subagents, isolated snapshots (worktree first), no cross-reviewer or cross-round leakage |
| No outcome prediction | No percentages, no ACCEPT / MINOR / MAJOR / REJECT verdicts |

---

## Built-in References

- `skills/论文润色/references/claim-calibration-guide.md` — two-way wording calibration table (over-claiming → scoped; over-defensive → claim-first), scope-limiting patterns, section conventions
- `skills/引用审查/references/citation-formats.md` — GB/T 7714 / IEEE / ACM / APA quick reference
- `skills/论文骨架/references/paper-structure-templates.md` — Chinese journal & English conference structure templates
- `dist/research-workbench-full-bundle.md` — single-file bundle (BOOTSTRAP + all skills + references) for paste-only chat LLMs

## Third-party credits

`skills/科研写作` and `skills/反防御写作` restate principles from two MIT-licensed community skills,
rewritten to fit this repo's no-ghostwriting and evidence-ceiling rules:

- [YuanZHAO321/Scientific-Writing-zh](https://github.com/YuanZHAO321/Scientific-Writing-zh) — scientific writing doctrine
- [Adkid-Zephyr/anti-defensive-writing-Skill](https://github.com/Adkid-Zephyr/anti-defensive-writing-Skill) — anti-defensive writing principles

The upstream "show the rewrite instead of describing it" habit is deliberately **not** adopted
(see Core Philosophy); the "never report a losing result" reading of anti-defensive writing is also
excluded — phrasing may be reframed, reported data may not be hidden.

---

## License

MIT — free for academic and personal use. Attribution appreciated.
