# 每日情报：RL × Agent Harness / 自进化 Harness

- 日期：2026-04-19
- 关注方向：
  1. 如何用 RL / RL 启发的思路简化 Harness Engineering
  2. 做出能"自进化"的 Agent Harness
- 渠道覆盖：arXiv、X（Twitter）、Hacker News、Google、各厂博客
- 评估维度：(a) 文档质量 (b) 作者/机构权威 (c) 可验证性 / 复现信号

历史库（repo 中已有，今天查重/关联基准）：
- `2026-03-18 · AgentFactory: A Self-Evolving Framework Through Executable Subagent Accumulation and Reuse` —— 已收录，本报告凡与 AgentFactory 强相关的新工作会标注「关联 AgentFactory」。
- `2026-03-17 Chronos (long-term memory)`、`2026-03-17 Efficient Reasoning on the Edge`、`2026-03-19 OS-Themis`、`2026-03-20 thinkingmachines.ai` —— 与今天主题无直接重叠。

---

## 今日精选（13 条）

### 1. Meta-Harness：把 harness 设计本身变成 RL-style 外环搜索
- arXiv: https://arxiv.org/abs/2603.28052
- 作者/机构：Yoonho Lee, Rafael Nair, Q. Zhang, K. Lee, Omar Khattab, Chelsea Finn（Stanford IRIS Lab）
- 代码：https://github.com/stanford-iris-lab/meta-harness
- 评分：质量★★★★★，权威★★★★★，可靠性★★★★★（Stanford + 公开代码 + 多基准）
- 关键数据：同一模型换 harness 可产生 **6×** 的性能差；IMO-level 数学检索 +4.7 点；TerminalBench-2 超过人工 baseline；上下文管理任务 **-4× token** 下 +7.7 点。
- 核心机制：外环是一个"coding agent 形的 proposer"（用 Claude Code / Opus 4.6），拥有对历史候选 harness 的源代码、分数、执行 trace 的**完整文件系统访问**，每次迭代中位数读 82 个文件——本质上是"把 DSPy/GEPA 式离散优化升级成 RL 风格的搜索+长记忆"。
- 与我们主题的关系：**最直接回答了"RL 风格能否自动化 harness 工程"**。把 harness 视作可学习的 policy，用 trace+score 做回报信号，outer-loop 即 RL。
- 关联：AgentFactory（把子 agent 能力沉淀成可复用资产）→ Meta-Harness 把沉淀对象升级到 harness 本身。

### 2. HyperAgents（Meta + UBC）：自指型 agent 自行"长出" harness
- arXiv: https://arxiv.org/abs/2603.19461
- 项目页：https://ai.meta.com/research/publications/hyperagents/ ；代码 https://github.com/facebookresearch/Hyperagents
- 评分：质量★★★★★，权威★★★★★（Meta FAIR），可靠性★★★★☆（发布时间新，需观察复现）
- 一句话：一个 task-agent + 一个 meta-agent 合成的**可自编辑程序**；meta 既能改 task，也能改自己——即"改进的改进机制"也可演化。
- 惊人结论：在 coding、paper review、机器人奖励设计、奥数批改 4 个完全不同领域里，hyperagents 都**独立"长出"**了持久化记忆、性能追踪、多级验证流水线、决策协议、重试逻辑——**这些正是工程师现在手写的 harness 组件**。说明 harness 是 agentic 系统的**收敛性架构**。
- 与我们主题的关系：**最直接回答了"自进化 Harness 是否可行"**。不是"换个 prompt"那种微调，而是 harness 结构本身从零演化。
- 关联：AgentFactory 的"可执行子 agent 累积"是 hyperagents 在某个方向的特例；Meta-Harness 是外环设计者写 harness，HyperAgents 是 agent 自己改自己。

### 3. AutoAgent（thirdlayer.inc / Kevin Gu）：让 AI 通宵优化自己的 harness
- MarkTechPost: https://www.marktechpost.com/2026/04/05/meet-autoagent-the-open-source-library-that-lets-an-ai-engineer-and-optimize-its-own-agent-harness-overnight/
- 代码：https://github.com/kevinrgu/autoagent
- 评分：质量★★★★☆，权威★★★☆☆（个人项目但公开、可复现），可靠性★★★★☆（有真实基准排名背书）
- 成绩：24h 自动优化跑到 SpreadsheetBench **#1 (96.5%)**、TerminalBench **top GPT-5 分 55.1%**，全部击败人写 harness。
- 机制：人写 `program.md`（自然语言指令），AutoAgent 自动改 system prompt、工具定义、routing、编排；跑 benchmark，接受/回滚。任务装在 Docker + Harbor 开格式里——**domain-agnostic**，只要有分数。
- 与主题关系：把"harness 设计"变成"meta-agent 的 RL 任务"，reward = benchmark score。对应 Meta-Harness 的工业化/实用化开源版。
- 关联：可以理解为 AgentFactory 思路 + Meta-Harness 的 outer-loop 思路的工程化合体。

### 4. Autogenesis Protocol (AGP) —— 给"自进化"定一个协议层
- arXiv: https://arxiv.org/abs/2604.15034（2026-04-16，极新）
- 评分：质量★★★★☆，权威★★★☆☆（独立作者 Wentao Zhang），可靠性★★★☆☆（实验规模待扩）
- 两层设计：
  - **RSPL（Resource Substrate）**：把 prompts、agents、tools、envs、memory 全部作为带生命周期与版本的协议化资源。
  - **SEPL（Self-Evolution Protocol）**：提出/评估/提交改动的闭环算子接口，带可审计 lineage 与回滚。
- 与主题关系：**补上了"自进化 Harness 的工程接口层"**。MCP 解决"工具"，AGP 想解决"可进化资源"。如果你要做一个能自改的 harness，这是个天然抽象层。
- 关联：与 AgentFactory 的"子 agent 累积"互补——AGP 提供底层 CRUD+版本，AgentFactory 提供 what to accumulate。

### 5. EvoSkills：co-evolutionary verifier 让 agent 自己写/测/长技能
- arXiv: https://arxiv.org/abs/2604.01687
- 代码：https://github.com/sentient-agi/EvoSkill
- 评分：质量★★★★☆，权威★★★★☆（UIC + Columbia 联合），可靠性★★★★☆（有 SkillsBench 可比较）
- 一句话：Skill Generator 和 Surrogate Verifier **互相对弈**——Verifier 在看不到 ground truth 的情况下写断言，给 Generator 结构化失败诊断。
- 数据：从 baseline 32% → 第 5 轮 **75%** 通过率，超过 human-curated skills；在 Claude Code/Codex 上都是五 baseline 最高。
- 与主题关系：这是"**harness 中的一个子模块（skill 生产线）自进化**"的标准做法，和 Hermes、AgentFactory 的 skill 沉淀思路是一条线，但把 verifier 协同进化这一点做成了显式设计。
- 关联 AgentFactory：AgentFactory 累积"可执行子 agent"，EvoSkills 累积"可验证 skill"——一个是黑盒可调用单元，一个是白盒文件包。

### 6. ProRL Agent（NVIDIA）：Rollout-as-a-Service，把 RL 训练和 harness 解耦
- arXiv: https://arxiv.org/abs/2603.18815 ；MarkTechPost 报道 2026-03-27
- 代码/集成：NVIDIA NeMo Gym
- 评分：质量★★★★★，权威★★★★★（NVIDIA），可靠性★★★★★（4B/8B/14B SWE-Bench Verified 全有增益）
- 架构：HTTP 服务化的 rollout，三段装配线 **INIT → RUN → EVAL**，每段独立 worker pool 异步重叠；trainer 只发请求收 trajectory + reward。
- 关键工程细节：token-in/token-out 轨迹通信避免 re-tokenization drift；rootless sandbox 适配 HPC。
- 与主题关系：**这是把 harness（rollout 侧）和 RL 训练（policy 侧）解耦的工业级范式**。对应 Lee Hanchung 文章里讲的"RL env = harness + dataset + reward"，ProRL 把 env 做成服务。
- 关联：为 Meta-Harness / HyperAgents 这类"harness 自进化"提供了底层训练基础设施。

### 7. Lee Hanchung：A Taxonomy of RL Environments for LLM Agents（博客）
- URL: https://leehanchung.github.io/blogs/2026/03/21/rl-environments-for-llm-agents/
- 评分：质量★★★★☆，权威★★★★☆（作者活跃于 RL-LLM 圈），可靠性★★★★☆
- 精华：
  - **State management**：stateless（LeetCode 式）vs stateful（DB agent 式）。
  - **自动环境生成**：LLM coding agent 直接写新 env 代码，而非人工穷举。
  - **Verifiable scoring**：能 scale 的基准 = 不需要人评判。
  - **Open Reward Standard**：把 MCP 扩展到 RL primitives，实现 env 与 trainer 解耦。
- 与主题关系：给"要自动化/RL 化 Harness"提供**标准语汇与设计原则**。
- 关联：和 ProRL 的 rollout-service 路线互补；Hugo Boedeker / Martin Fowler 那套语汇偏用户侧，这篇偏训练侧。

### 8. Martin Fowler × Birgitta Böckeler：Harness Engineering for Coding Agent Users
- URL: https://martinfowler.com/articles/exploring-gen-ai/harness-engineering.html （2026-04-02）
- 评分：质量★★★★★，权威★★★★★（Thoughtworks 首席），可靠性★★★★★
- 贡献：**Guides-and-Sensors 分类法**——guides（system prompt、AGENTS.md、约束文档）+ sensors（eval、validation loop、output parser、drift detector）。这套词汇现已成为实践者讨论 harness 组件的事实标准。
- 与主题关系：想用 RL 优化 harness，**先要能把 harness 拆出可独立搜索的组件**。Guides/Sensors 正好是搜索空间的自然切分。

### 9. Can Bölük：只改 harness 就让 15 个 LLM 在编程上都涨点
- 博客：https://blog.can.ac/2026/02/12/the-harness-problem/
- X：https://x.com/_can1357/status/2021828033640911196
- HN：https://news.ycombinator.com/item?id=46988596（热度极高）
- 评分：质量★★★★☆，权威★★★☆☆（社区作者，经过 HN 广泛验证），可靠性★★★★☆（可复现实验）
- 关键数据：仅换 edit format，15 个模型编程分数 +5~14 点、输出 token 降约 20%；极端个例 **6.7% → 68.3%（10×）**。
- 与主题关系：最通俗但最有力的"harness ≥ model"证据，是说服团队投 harness engineering 的"一页纸论据"。

### 10. Philipp Schmid（HuggingFace）：The Importance of Agent Harness in 2026
- 博客：https://www.philschmid.de/agent-harness-2026 ；X：https://x.com/_philschmid/status/2008175408923959574
- 评分：质量★★★★☆，权威★★★★★（Philipp Schmid，HF/Google DevRel），可靠性★★★★☆
- 核心观点：
  - "Context durability is the new bottleneck"；harness 是对抗 model drift 的主工具。
  - 要生存 Bitter Lesson，**harness 必须轻量**——模型一换，最优 harness 形态就变，重 harness 会被时代碾过。
- 与主题关系：**给"自进化 harness"提供了商业/工程上的 why**——正因为模型迭代快，harness 不能由人反复手写，自动化是唯一解。

### 11. Karpathy Autoresearch：最小化自进化 agent 循环（单 GPU nanochat）
- GitHub：https://github.com/karpathy/autoresearch
- X（原贴）：https://x.com/karpathy/status/2030371219518931079
- 评分：质量★★★★★，权威★★★★★（Karpathy），可靠性★★★★★（完全开源 630 行）
- 机制：agent 改训练脚本 → 跑 5 分钟 budget → 看 val_bpb → 保留/回滚 → 循环。
- 实测：H100 上多次自动 session，val_bpb 从 0.9979 → 0.9773（89 次）和 → **0.9697（126 次）**，全自动通宵。
- 后续推文提示下一步是 **SETI@home 式异步大协作**——从"一个 PhD"到"一群 PhD"。
- 与主题关系：把"harness 自进化"最小化为 ~630 行；是所有人可以上手的最小 RL 启发 outer-loop baseline。

### 12. Hermes Agent（Nous Research）：生产化的自改进 agent
- 代码：https://github.com/nousresearch/hermes-agent ；主页 https://hermes-agent.nousresearch.com/
- HN：https://news.ycombinator.com/item?id=47644400（#1，1064 分，811 评）
- 评分：质量★★★★☆，权威★★★★☆（Nous Research），可靠性★★★★☆（社区大量使用）
- 特征：47 内置工具 + 跨 session 持久化记忆 + 插件式 memory backend + MCP + 内置 cron。**每次运行写/更新 skill 文档**——这是 EvoSkills 思路的"非学术版产品"。
- 与主题关系：**自进化 agent 的"工程版"**，提供了今天就能跑起来的 baseline。
- 关联：与 AgentFactory（累积可执行子 agent）对照——Hermes 偏向"累积 skill 文档 + 持久化记忆"。

### 13. Agent-R1：端到端多轮 RL 训练 LLM Agent 的框架
- arXiv: https://arxiv.org/abs/2511.14460 （较早但最近活跃）
- 代码：https://github.com/0russwest0/Agent-R1
- 评分：质量★★★★☆，权威★★★★☆（USTC 团队），可靠性★★★★☆
- 贡献：把 LLM agent 的交互形式化到 **多轮 MDP**（思考步、动作、环境反馈、奖励），把传统单轮 RL 框架扩到 multi-turn；在 Multihop QA 上验证。
- 与主题关系：给"把 harness 当作可训练的东西"提供正统的 RL 形式化工具；配合 ProRL Agent 的 rollout-as-a-service 是一套完整栈。

---

## 关联图谱（一页纸）

```
        ┌── Why ──┐
Schmid / Fowler / Bölük（为什么 harness 重要 & 自动化不可避免）
        │
        ▼
   ┌── What 要进化 ──┐
AgentFactory（子 agent 资产）│ EvoSkills（skill 文件包）│ Hermes（skill+memory）
        │
        ▼
   ┌── How 进化 ──┐
Meta-Harness（外环 coding-agent 改 harness 源码）
HyperAgents（self-referential，meta 也可改自己）
AutoAgent（面向 benchmark 的 overnight 优化）
Autogenesis AGP（给自进化定协议层/版本/回滚）
Karpathy Autoresearch（最小 630 行 baseline）
        │
        ▼
   ┌── Infra ──┐
ProRL Agent（rollout-as-a-service）│ Agent-R1（多轮 MDP 训练框架）│ Lee Hanchung（RL env 分类学 & Open Reward Standard）
```

## 今日一句话结论

**"Harness 不再由人手写，而是由带记忆的 coding agent 在 RL 风格的 outer loop 里搜索 + 版本化沉淀"——这个范式本周从 Stanford (Meta-Harness) → Meta (HyperAgents) → 独立开发者 (AutoAgent) → 协议层 (Autogenesis) 形成了闭环。下一步值得盯紧：(a) Open Reward Standard 能否成事实标准；(b) HyperAgents 的 self-referential 能否在 harness 自进化跑出稳定收敛；(c) ProRL 的 rollout-as-a-service 会不会被接入 Meta-Harness 这类 outer-loop 搜索里。**

---

## 未入选 / 待跟踪

- MemRL（arXiv 2601.03192，episodic memory 运行时 RL） —— 与 AgentFactory 概念重叠较多，非本期新贡献。
- Experiential Reflective Learning / ERL（arXiv 2603.24639） —— 技术相近，Gaia2 +7.8%；已被 EvoSkills/Hermes 的思路覆盖。
- SAGE / Skill-Augmented GRPO（arXiv 2512.17102） —— 训练策略细节层面，等更多复现。
- Adaptive Memory Crystallization（arXiv 2604.13085）、MiniMax M2.7 自训报道 —— 需要更硬的一手证据。

## 评估与流程小结

- 查重：与 repo 中 `AgentFactory` 有概念重叠的 EvoSkills / Hermes / AgentEvolver 等，本期只列入能带来**新机制/新数据**的一条（EvoSkills：co-evolutionary verifier 是 AgentFactory 未覆盖的），其余作为"未入选"。
- 质量分级：arXiv 单作者 / 新发论文（如 Autogenesis）在"可靠性"分上谨慎给 3 星，等社区复现。
- 权威度：Stanford、Meta FAIR、NVIDIA、Martin Fowler / Thoughtworks、HuggingFace（Schmid）、Nous Research、Karpathy 均给 ★★★★★/★★★★☆。
