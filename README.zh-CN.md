# AI-Native SDLC —— 一个 Codex Skill

一个 [Codex](https://developers.openai.com/codex) skill,用 **AI 原生** 的方式带一个功能/项目
走完整个软件研发生命周期:agent 负责跑量(意图、规格、测试、代码、评审),人只在**判断关口**
把关,而 **git 就是审计链**。

> English version: [README.md](./README.md)

## 这个 skill 是怎么来的

它是把 Anthropic 的博客
[**The AI-Native SDLC Playbook**](https://claude.com/blog/the-ai-native-sdlc-playbook)
的理念移植到 Codex 上的产物。

博客的核心论点:**代码不再是瓶颈。** 传统 SDLC 是在"写代码占用项目大部分时间"的年代设计的;
如今 agent 能极快完成实现,真正的瓶颈转移到了**人类速度**的环节——评审、审批、部署。于是整个
流程围绕**版本化的 Markdown 交付物**重建:每个阶段 commit 一个文件,这个文件就是下一阶段的
gate,git 成为唯一的真相源(system of record)。

本仓库把这些理念落到 Codex 的原生机制上:`AGENTS.md`(自动加载的项目上下文)、**policy skills**、
以及 Codex 的**沙箱/审批模式**和 `codex exec`。

## 目录结构

```
SKILL.md               # Codex 加载的 skill 本体 —— 规则 + 六个阶段
templates/
  intent.md            # 阶段1(Plan)   —— 机器可读的需求文档
  spec.md              # 阶段2(Design) —— 需求 + 技术设计
  AGENTS.md            # 阶段3+(Build)  —— 持久、自动加载的项目约定
```

## 六个阶段

| # | 阶段 | 交付物 | 人工关口 |
|---|------|--------|----------|
| 1 | Plan     | `intent.md` | Product owner 审批后进入 Design |
| 2 | Design   | `spec.md`   | Reviewer 审批后进入 Build |
| 3 | Build    | 代码+测试,约定写入 `AGENTS.md` | — |
| 4 | Test     | 贯穿 Build,而非独立卡点 | — |
| 5 | Deploy   | 分层 agent 评审 | 受监管代码需人工签署 |
| 6 | Maintain | 控制项被突破 → 生成新的 `intent.md` 条目 | 闭环回到 Plan |

核心规则:交付物即真相源;人管 gate、agent 填量;政策以可复用 skill 形式存在;机构知识写进
`AGENTS.md`(而不是一次性 prompt)。

## 安装

克隆到你的 Codex skills 目录,Codex 会自动发现:

```bash
git clone https://github.com/XiaoyingZuo/ai-native-sdlc-skill.git \
  "${CODEX_HOME:-$HOME/.codex}/skills/ai-native-sdlc"
```

Codex 通过 `SKILL.md` 的 frontmatter(`name` + `description`)加载 skill,并在你的请求匹配时
自动触发,无需手动注册。

## 使用教程

**1. 开始一件工作。** 直接告诉 Codex 你想做什么,例如
*"用我们的 AI-native SDLC 把这个功能端到端做完"*。Codex 加载 skill 并确认当前处于哪个阶段。

**2. Plan —— 产出 `intent.md`。** 复制模板,和 Codex 一起填:

```bash
cp "${CODEX_HOME:-$HOME/.codex}/skills/ai-native-sdlc/templates/intent.md" ./intent.md
```

只写清楚 *why/what*(Problem、Goals、Non-goals、Constraints、Success criteria)。在
Constraints 里指明 Codex 要加载的 **policy skills** 和 **沙箱模式**。Product owner 批准后
commit,这个 commit 就是 gate。

**3. Design —— 产出 `spec.md`。** 基于批准的 intent,Codex 在一次会话里生成需求+设计:

```bash
cp "${CODEX_HOME:-$HOME/.codex}/skills/ai-native-sdlc/templates/spec.md" ./spec.md
```

需求用 R1/R2… 编号并回溯到 intent 的目标;Test strategy 写明确切命令(模型类工作还要写
eval);Deploy 段记录审批模式和 `codex exec` 路径。批准后 commit。

**4. Build + Test。** Codex 依据 spec 实现,边写代码边写测试,并把任何长期约定写进 `AGENTS.md`
(下次 Codex 自动加载)。提交前跑真实的测试/构建命令。

**5. Deploy + Maintain。** 先走 agent 评审;仅受监管/高影响面的代码需人工签署。生产环境里,
一旦控制项被突破或出现新缺陷,就生成一条新的 `intent.md` —— 闭环。

## 使用案例:一个"提升 benchmark"的功能

目标:通过新增一种 prompting 策略,提升某表格 benchmark 的 pass@1。

1. **`intent.md`** —— Problem:当前 pass@1 停滞。Goal:在评测集上 +X 分。Non-goal:不改动
   harness。Constraints:沙箱 `workspace-write`,应用 `eval-safety` policy skill。Success
   criteria:留出集上 pass@1 ≥ 目标值。
2. **`spec.md`** —— R1:新增策略模块;R2:用 flag 挂载。Test strategy:`pytest -q` + 跑
   benchmark eval,指标 = pass@1。Deploy:在评测 CI 里用 `codex exec`,仅 agent 评审(非监管)。
3. **Build/Test** —— Codex 实现模块、补单测、跑 eval,把 flag 约定记进 `AGENTS.md`。
4. **Deploy/Maintain** —— eval 达标后按 flag 合入;若后续某次跑分回退,该回退自动开一条新的
   `intent.md`。

收益:每一步都有一个已提交、可评审的交付物 —— 从 intent 到生产的完整审计链,人把注意力花在
审批上,而不是实现细节上。

## 许可

自由使用。理念改编自 Anthropic 的 *The AI-Native SDLC Playbook*,面向 Codex 场景。
