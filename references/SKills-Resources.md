# 外部 AI Agent Skills 资源池索引（v1）

> **本文件是「深度分析四准则 · 准则 1 · 资料获取双轨制」的补充 SOP**。
> 当 pdc-team-workflow 团队在调研某个技术 / 实现某个 subagent / 编写新 SKILL.md / 找不到现成方法论时，按本索引快速定位「到哪儿找 → 该问题直接到哪个仓库抄」。
>
> **与现有 SOP 的关系**：
> - 与 `references/代码仓调研方法论.md` 互补：那份讲「找到目标仓后怎么精读」，本份讲「调研前怎么挑目标仓」。
> - 与 `references/技术原理分析SOP.md` 互补：那份讲「如何把原理拆成三级」，本份讲「哪些公开 skill 已经做过类似拆解，可以参考」。
>
> **最后更新**：2026-10-01

---

## 一、为什么需要这份索引

### 核心痛点

pdc-team-workflow 团队执行「深度分析 / 软件开发 / 技术调研」类任务时，常常陷入：

1. **闭门造车**：调研组 B 不知道已经有现成的 SKILL.md 仓库可以参考，要从零设计方法论
2. **重复造轮子**：撰写组 B 写出报告结构后才发现 vivy-yi/awesome-skills 里早就有 230+ 类似范例
3. **选错目标**：准备调研某个开源项目时，凭直觉挑仓库，没先扫一遍 awesome-* 列表看哪个最相关
4. **不知道有官方规范**：自己编 SKILL.md frontmatter 格式，结果不符合 Anthropic 官方规范，导致 plugin-entry 不识别

### 解决思路

**调研开始前，先花 5 分钟扫这份索引，按「问题类型 → 仓库 ID」快速定位候选。** 不要凭直觉选仓库；不要等写完报告才发现有现成模板。

类比法医取证（沿用 `代码仓调研方法论.md` 的比喻）：调研前先看「刑侦样本库」里有没有同类案件的破案报告，能直接复用作案手法分类、相似弹壳特征——而不是接到现场才发明勘查流程。

---

## 二、什么时候用（强制触发场景）

| 场景 | 谁用 | 用法 |
|------|------|------|
| **调研组 B** 准备 `git clone` 某个目标仓 | 调研组 B | 先查 §四「实战精选」找同类项目，看有没有现成的 SKILL.md / subagent 可参考 |
| **主控** 收到模糊任务，不知道该唤醒哪几个职能组 | 主控 | 先查 §三「官方规范」看 Anthropic 推荐的 subagent 分工 |
| **撰写组 B** 准备写一份 SKILL.md / 报告 / PRD | 撰写组 B | 先查 §三「官方规范」对齐 frontmatter 规范；查 §四 B1（alirezarezvani）抄目录结构 |
| **技术分析组 B** 要分析某个 agent 框架（如 LangGraph / LiteLLM / DSH）| 技术分析组 B | 先查 §四 B3-B9 看有没有现成的技术拆解 |
| **质检组 C** 收到一份 SKILL.md，要核对 frontmatter 规范 | 质检组 C | 用 §三「官方规范」作为 C 闸门 checklist |

---

## 四、资源分类清单

> **组织原则**：按「先看官方 → 再看汇总 → 最后动手」顺序。质检组 C 必须能跨组验证。

### 4.1 官方资源（最权威 · 必读 · 10 个）

| ID | 名称 | 链接 | 何时用 |
|---|---|---|---|
| O1 | Anthropic Engineering: Equipping agents with Agent Skills | [link](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills) | 任何 Skill 设计理念疑问 |
| O2 | Claude Skills 创建指南（含 frontmatter 规范）| [link](https://claude.com/blog/how-to-create-skills-key-steps-limitations-and-examples) | 写新 SKILL.md 时 |
| O3 | Claude Code Plugins Docs（市场机制）| [link](https://code.claude.com/docs/en/plugins/anthropic-marketplaces) | 设计 plugin 加载/发现机制 |
| O4 | Claude.ai 自定义 Skills 文档 | [link](https://claude.com/docs/skills/how-to) | 端用户视角 |
| O5 | Anthropic 完整 Skill 构建指南 PDF | [link](https://resources.anthropic.com/hubfs/The-Complete-Guide-to-Building-Skill-for-Claude.pdf) | 离线全量细节 |
| O6 | Claude Code Skills 文档（日文版）| [link](https://code.claude.com/docs/ja/skills) | 实现细节补充 |
| O7 | Anthropic Plugins 市场（中文版）| [link](https://code.claude.com/docs/zh-CN/plugins/anthropic-marketplaces) | 中文文档 |
| O8 | Skills 浏览/连接器/插件统一目录（支持中心）| [link](https://support.claude.com/en/articles/14328846-exploring-skills-in-one-directory) | 端用户使用指南 |
| O9 | Agent Skills Specification（arxiv 论文）| [link](https://arxiv.org/pdf/2606.03565v2) | 规范级论文，选读 |
| O10 | Skill 蓝皮书 v1.1 PDF（中文）| [link](https://github.com/zhuyansen/skill-blue-book/releases/download/v1.1/skill-blue-book-2026-v1.1.pdf) | 中文圈入门首选 |

### 4.2 Awesome 汇总类（资源入口 · 7 个）

> 用于「调研前不知道怎么挑起手式」——先扫这三份找候选。

| ID | 仓库 | 链接 | 规模 / 特点 |
|---|---|---|---|
| **A1** | **vivy-yi/awesome-skills** ⭐首推 | [link](https://github.com/vivy-yi/awesome-skills) | **230+ 仓库**，最大的精选列表 |
| **A2** | **itgoyo/awesome-claude-code-skills** | [link](https://github.com/itgoyo/awesome-claude-code-skills) | 高 star 的 Claude Code 工具与技能 |
| A3 | Chat2AnyLLM/awesome-claude-skills | [link](https://github.com/Chat2AnyLLM/awesome-claude-skills) | 另一份精选 |
| A4 | ranbot-ai/awesome-skills | [link](https://github.com/ranbot-ai/awesome-skills) | Claude AI workflow 工具集 |
| A5 | jiang-lin17/awesome-ai-skills | [link](https://github.com/jiang-lin17/awesome-ai-skills) | 综合列表 |
| A6 | luokai0/ai-agent-skills-by-luo-kai | [link](https://github.com/luokai0/ai-agent-skills-by-luo-kai) | 个人精选 |
| A7 | Angular-RU/awesome-skills | [link](https://github.com/Angular-RU/awesome-skills) | 俄语社区精选（英文内容）|

### 4.3 实战精选 / 工具类（直接借鉴 · 9 个）

> 用于「调研后想直接抄模板 / 抄实现」。

| ID | 仓库 | 链接 | 在本团队中的作用 |
|---|---|---|---|
| **B1** | **alirezarezvani/claude-skills** ⭐ | [link](https://github.com/alirezarezvani/claude-skills) | **完整企业级模板集**，抄 SKILL.md 目录结构 / subagent 分工 |
| **B2** | **pedronauck/skills** | [link](https://github.com/pedronauck/skills) | 个人精选，质量高，可借鉴风格 |
| B3 | ivklgn/bag-of-skills | [link](https://github.com/ivklgn/bag-of-skills) | Skills + Subagents 合集 |
| B4 | kasperjunge/agent-resources | [link](https://github.com/kasperjunge/agent-resources-legacy) | 可通过 `uvx add-skill` 本地安装 |
| B5 | thevoidsyntax/my-skills | [link](https://github.com/thevoidsyntax/my-skills) | 个人实战库 |
| B6 | okenwa/claude-skills | [link](https://github.com/okenwa/claude-skills) | 个人整理 |
| B7 | rooftop-Owl/skill-factory | [link](https://github.com/rooftop-Owl/skill-factory/blob/main/handbook/publishing-skills.md) | Skill 工厂化发布手册 |
| **B8** | **rtaparay/Open-Skills-Manager** | [link](https://github.com/rtaparay/Open-Skills-Manager) | **58,000+ skill** 云端管理器，借鉴 plugin 加载机制 |
| B9 | EGAdams/planner (meta-skill) | [link](https://github.com/EGAdams/planner/blob/main/.claude/skills/meta-skill/docs/blog_equipping_agents_with_skills.md) | 元 Skill 实现参考 |

---

## 五、可执行步骤（按问题 → 仓库的映射 playbook）

> 沿用 `代码仓调研方法论.md` 的「4 步法」风格——每个问题先问「我属于哪一类」，再回答「该看哪个 ID」。

### 5.1 「我要写一份新的 SKILL.md」

1. 打开 O2 / O5 → 复制 frontmatter 规范（必须含 `name` + `description`；建议含 `version` + `when_to_use`）
2. 打开 B1 → 复制 `.claude/skills/<skill-name>/` 三层目录结构
3. 打开 §七 检查清单 → 校验产物

### 5.2 「我要调研某个 agent 框架（LangGraph / LiteLLM / DSH / AutoGen ...）」

1. 打开 A1 / A2 → 在 230+ 仓库里搜同类框架分析报告
2. 如果有同类报告 → 直接 clone 该报告作者的源码仓，按 `代码仓调研方法论.md` 精读
3. 如果没有 → 退回 O1 / O9 看官方规范，再决定 clone 目标

### 5.3 「我要设计 plugin / marketplace 机制」

1. 打开 O3 / O7 → 学习 Anthropic Plugins 官方机制
2. 打开 B1 → 抄其插件目录结构
3. 打开 B8 → 借鉴其「plugin 加载失败不影响主进程」的 error boundary

### 5.4 「我要做技术原理 → 方案 → 落地的三级拆解」

1. 打开 `references/技术原理分析SOP.md`（已有 SOP）
2. 打开 B3 / B9 → 看现成的拆解范例
3. 质检组 C 用 B1 / B2 的目录结构作为 checklist

### 5.5 「调研组 B 不知道该 clone 哪个」

1. 打开 A1 → 搜关键词
2. 打开 §五.2 按框架分类选 ID
3. 列子智能，然后按 §五.3 走完整调研流程

---

## 六、推荐组合（不要全看，按场景挑）

| 任务类型 | 推荐组合 |
|---|---|
| **写新 SKILL.md** | O2 + B1 + A2 |
| **调研 agent 框架** | A1 + `代码仓调研方法论.md` + B3/B9 |
| **设计 plugin 机制** | O3 + B1 + B8 |
| **找现成报告模板** | A1 + A2 + `报告撰写SOP.md` |
| **质检 SKILL.md 合规性** | O2 + B1（作为 checklist）|

---

## 七、检查清单（C 闸门用）

质检组 C 在以下时机使用本清单：

- [ ] 调研组 B 提交 plan.md 时，确认 plan 中至少引用了 §四 中 1 个资源 ID（O/A/B 任意）
- [ ] 撰写组 B 提交新 SKILL.md 时，确认 frontmatter 含 `name` + `description`（对齐 O2）
- [ ] 技术分析组 B 提交三级拆解时，确认有可下钻的章节结构（参考 `技术原理分析SOP.md` + §四.3 模板）
- [ ] 主控收到模糊任务时，确认先查过 §六「推荐组合」再选组

---

## 八、已知坑 & 反例清单

| 反例 | 后果 | 正确做法 |
|------|------|---------|
| ❌ 凭直觉挑目标仓，不查 A1 | 选到非主流的废弃仓，浪费 clone + 精读时间 | 先扫 A1 / A2 至少 1 分钟再 clone |
| ❌ 自己编 frontmatter 格式 | 官方 plugin 不识别，团队成员无法引用 | 严格按 O2 / O5 规范 |
| ❌ 只查 Web 汇总不做代码考古 | 错过 awesome-* 没收录的优质仓 | A1 是起点不是终点，仍需按 `代码仓调研方法论.md` 精读 |
| ❌ 把 awesome-* 当成「唯一真源」 | awesome 收录≠质量保证 | 用 B 系列「实战精选」做二次过滤 |
| ❌ 不在产物里写 [src: 资源 ID] | C 闸门无法验证调研依据 | 调研产物每条断言带 `[src: O1/B3/...]` 标注 |

---

## 九、版本演进

- **v1（2026-10-01）**：首次创建。收录 10 个官方 + 7 个汇总 + 9 个实战精选，共 **26 个仓库**。
- 后续 v2+ 将持续：
  - 跟随 awesome-* 列表更新（建议每季一次）
  - 沉淀团队实战中「用过且有效」的仓库
  - 反例清单扩充

---

## 十、速查索引（贴书签用）

### 官方（10 个）
1. https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills
2. https://claude.com/blog/how-to-create-skills-key-steps-limitations-and-examples
3. https://code.claude.com/docs/en/plugins/anthropic-marketplaces
4. https://claude.com/docs/skills/how-to
5. https://resources.anthropic.com/hubfs/The-Complete-Guide-to-Building-Skill-for-Claude.pdf
6. https://code.claude.com/docs/ja/skills
7. https://code.claude.com/docs/zh-CN/plugins/anthropic-marketplaces
8. https://support.claude.com/en/articles/14328846-exploring-skills-in-one-directory
9. https://arxiv.org/pdf/2606.03565v2
10. https://github.com/zhuyansen/skill-blue-book/releases/download/v1.1/skill-blue-book-2026-v1.1.pdf

### Awesome（7 个）
- A1. https://github.com/vivy-yi/awesome-skills
- A2. https://github.com/itgoyo/awesome-claude-code-skills
- A3. https://github.com/Chat2AnyLLM/awesome-claude-skills
- A4. https://github.com/ranbot-ai/awesome-skills
- A5. https://github.com/jiang-lin17/awesome-ai-skills
- A6. https://github.com/luokai0/ai-agent-skills-by-luo-kai
- A7. https://github.com/Angular-RU/awesome-skills

### 实战精选（9 个）
- B1. https://github.com/alirezarezvani/claude-skills
- B2. https://github.com/pedronauck/skills
- B3. https://github.com/ivklgn/bag-of-skills
- B4. https://github.com/kasperjunge/agent-resources-legacy
- B5. https://github.com/thevoidsyntax/my-skills
- B6. https://github.com/okenwa/claude-skills
- B7. https://github.com/rooftop-Owl/skill-factory
- B8. https://github.com/rtaparay/Open-Skills-Manager
- B9. https://github.com/EGAdams/planner/tree/main/.claude/skills/meta-skill