# AI-Native SDLC —— 一个 Codex & Claude Code Skill

一个 skill,用 **AI 原生** 的方式带一个功能/项目走完整个软件研发生命周期:agent 负责跑量
(意图、规格、测试、代码、评审),人只在**判断关口**把关,而 **git 就是审计链**。

提供 **两个版本** —— 一个给 [Codex](https://developers.openai.com/codex),一个给
[Claude Code](https://docs.claude.com/en/docs/claude-code) —— 阶段与规则完全一致,只是各自
适配了对应工具的原生机制。

> English version: [README.md](./README.md)

## 这个 skill 是怎么来的

它是把 Anthropic 的博客
[**The AI-Native SDLC Playbook**](https://claude.com/blog/the-ai-native-sdlc-playbook)
的理念改编而成。

博客的核心论点:**代码不再是瓶颈。** 传统 SDLC 是在"写代码占用项目大部分时间"的年代设计的;
如今 agent 能极快完成实现,真正的瓶颈转移到了**人类速度**的环节——评审、审批、部署。于是整个
流程围绕**版本化的 Markdown 交付物**重建:每个阶段 commit 一个文件,这个文件就是下一阶段的
gate,git 成为唯一的真相源(system of record)。

## 仓库结构

```
codex/                 # Codex 版本
  SKILL.md
  templates/{intent.md, spec.md, AGENTS.md}
claude/                # Claude Code 版本
  SKILL.md
  templates/{intent.md, spec.md, CLAUDE.md}
```

两个版本定义相同的六阶段和核心规则,只在工具原生细节上不同:

| 概念 | Codex | Claude Code |
|---|---|---|
| 自动加载的项目记忆 | `AGENTS.md` | `CLAUDE.md` |
| 无人值守动作的控制 | 沙箱模式(`read-only` / `workspace-write`) | 权限模式(`default` / `plan` / `acceptEdits`) |
| 无头 / 流水线执行 | `codex exec` | `claude -p` |
| skill 位置 | `~/.codex/skills/` | `~/.claude/skills/` |
| 政策即 skill | 是 | 是 |

## 六个阶段

| # | 阶段 | 交付物 | 人工关口 |
|---|------|--------|----------|
| 1 | Plan     | `intent.md` | Product owner 审批后进入 Design |
| 2 | Design   | `spec.md`   | Reviewer 审批后进入 Build |
| 3 | Build    | 代码+测试,约定写入 `AGENTS.md`/`CLAUDE.md` | — |
| 4 | Test     | 贯穿 Build,而非独立卡点 | — |
| 5 | Deploy   | 分层 agent 评审 | 受监管代码需人工签署 |
| 6 | Maintain | 控制项被突破 → 生成新的 `intent.md` 条目 | 闭环回到 Plan |

核心规则:交付物即真相源;人管 gate、agent 填量;政策以可复用 skill 形式存在;机构知识写进
自动加载的记忆文件(而不是一次性 prompt)。

## 安装

**Codex:**
```bash
git clone https://github.com/XiaoyingZuo/ai-native-sdlc-skill.git /tmp/ansdlc
cp -R /tmp/ansdlc/codex "${CODEX_HOME:-$HOME/.codex}/skills/ai-native-sdlc"
```

**Claude Code:**
```bash
git clone https://github.com/XiaoyingZuo/ai-native-sdlc-skill.git /tmp/ansdlc
cp -R /tmp/ansdlc/claude "$HOME/.claude/skills/ai-native-sdlc"
```

两个工具都通过 `SKILL.md` 的 frontmatter(`name` + `description`)加载 skill,并在请求匹配时
自动触发,无需手动注册。

## 使用教程

**1. 开始一件工作。** 直接告诉 agent 你想做什么,例如
*"用我们的 AI-native SDLC 把这个功能端到端做完"*。它加载 skill 并确认当前处于哪个阶段。

**2. Plan —— 产出 `intent.md`。** 复制模板,和 agent 一起填:

```bash
# Codex:  cp "${CODEX_HOME:-$HOME/.codex}/skills/ai-native-sdlc/templates/intent.md" ./intent.md
# Claude: cp "$HOME/.claude/skills/ai-native-sdlc/templates/intent.md" ./intent.md
```

只写清楚 *why/what*(Problem、Goals、Non-goals、Constraints、Success criteria)。在
Constraints 里指明要加载的 **policy skills** 和 **沙箱/权限模式**。Product owner 批准后
commit,这个 commit 就是 gate。

**3. Design —— 产出 `spec.md`。** 基于批准的 intent,agent 在一次会话里生成需求+设计。需求用
R1/R2… 编号并回溯到 intent 目标;Test strategy 写明确切命令(模型类工作还要写 eval);Deploy
段记录模式和无头执行路径(`codex exec` / `claude -p`)。批准后 commit。

**4. Build + Test。** agent 依据 spec 实现,边写代码边写测试,并把长期约定写进
`AGENTS.md`/`CLAUDE.md`(下次自动加载)。提交前跑真实的测试/构建命令。

**5. Deploy + Maintain。** 先走 agent 评审;仅受监管/高影响面的代码需人工签署。生产环境里,
一旦控制项被突破或出现新缺陷,就生成一条新的 `intent.md` —— 闭环。

## 使用案例:一个"提升 benchmark"的功能

目标:通过新增一种 prompting 策略,提升某表格 benchmark 的 pass@1。

1. **`intent.md`** —— Problem:当前 pass@1 停滞。Goal:在评测集上 +X 分。Non-goal:不改动
   harness。Constraints:`workspace-write`(Codex)/ `acceptEdits`(Claude),应用
   `eval-safety` policy skill。Success criteria:留出集上 pass@1 ≥ 目标值。
2. **`spec.md`** —— R1:新增策略模块;R2:用 flag 挂载。Test strategy:`pytest -q` + 跑
   benchmark eval,指标 = pass@1。Deploy:在评测 CI 里无头执行,仅 agent 评审(非监管)。
3. **Build/Test** —— agent 实现模块、补单测、跑 eval,把 flag 约定记进记忆文件。
4. **Deploy/Maintain** —— eval 达标后按 flag 合入;若后续某次跑分回退,该回退自动开一条新的
   `intent.md`。

收益:每一步都有一个已提交、可评审的交付物 —— 从 intent 到生产的完整审计链,人把注意力花在
审批上,而不是实现细节上。

## 许可

自由使用。理念改编自 Anthropic 的 *The AI-Native SDLC Playbook*,面向 Codex 与 Claude Code。
