# colleague-skill 项目分析与讨论记录

> 日期：2026-04-11
> 分析者：Peter + Claude Code

## 项目概述

原始仓库：[titanwings/colleague-skill](https://github.com/titanwings/colleague-skill)（6.6k+ stars）

"把同事蒸馏成 AI Skill" — 通过采集飞书、钉钉、Slack、邮件等数据，提取一个人的工作能力和性格特征，生成可独立运行的 Claude Code Skill。

## 技术分析

### 核心架构

- **Work Skill**（能做事）— 提取技术能力、工作流程、代码规范
- **Persona**（像那个人）— 5 层结构的人格模型（L0 硬规则 > L1 身份 > L2 表达 > L3 决策 > L4 人际）
- **进化机制** — 追加文件 + 对话纠正，持续迭代

### 技术本质

**没有自定义模型、没有 fine-tuning、没有算法。** 整个蒸馏过程是纯 prompt 驱动的模式提取：

```
原材料（聊天记录、文档、邮件）
  → Claude 读入上下文
  → 按 prompt 模板提取模式
  → 输出结构化 markdown（work.md + persona.md）
  → 合并成可独立运行的 Claude Code Skill
```

Python 工具只负责数据采集和文件管理，不做任何分析。核心资产是 `prompts/` 目录下的模板。

### 关键文件

| 文件 | 作用 |
|------|------|
| `SKILL.md` | 主入口，1008 行的完整流程编排（中英双语） |
| `prompts/intake.md` | 基础信息录入，含职级对照表和个性标签库 |
| `prompts/work_analyzer.md` | 按职位类型提取工作能力的维度 |
| `prompts/persona_builder.md` | 5 层人格结构的生成模板 |
| `tools/feishu_auto_collector.py` | 飞书数据自动采集 |
| `tools/skill_writer.py` | Skill 文件写入和版本管理 |

## Fork 差异

本 fork（paipeter0801/colleague-skill）相比上游的主要变更：

1. **Discord 社群推广** — 所有语言 README 加了 Discord badge 和链接
2. **社区画廊公告** — 加了 community gallery announcement
3. **多语言 README 扩展** — 拆分中英文，新增 ES/DE/JA/RU/PT 版本
4. **Slack 自动收集器** — 新增 `tools/slack_auto_collector.py`
5. **P2P 私聊收集** — 飞书私聊消息采集功能
6. **技术论文 PDF** — 添加 `colleague_skill.pdf`
7. **Star History 图表** — 英文 README 加入 Star History

## 讨论：优势与局限

### 为什么能火（6.6k stars）

- **概念包装极强** — "蒸馏同事""赛博永生"，一听就想转发
- **场景精准** — 字节范、阿里味、甩锅高手、PUA 大师，戳中中国互联网人痛点
- **衍生生态** — 前任.skill、yourself.skill，证明范式有延展性
- **门槛低** — 纯 prompt，谁都能 fork 来玩
- **技术不难，难的是想到该做什么、怎么让人一听就懂**

### 核心局限：蒸馏是有损压缩

几千条聊天记录压缩成几段 prompt，信息丢失严重：
- 具体怎么回的某个问题 → 丢了
- 某次 Code Review 的完整评论 → 丢了
- 语境和上下文 → 丢了

最终生成的 Skill 只是一个模糊的影子。

### 改进方向：向量化 + 蒸馏混合

```
Layer 1：向量化原始数据（RAG）— 提供记忆和具体例子，零信息丢失
Layer 2：轻量蒸馏（只提核心规则）— 提供人格框架
```

向量存储做记忆，蒸馏做性格，两层叠起来才是更强的方案。

### 真正的实用价值：去 AI 味的 prompt 框架

抛开营销噱头，这个项目最有价值的是一套**让 AI 输出不像 AI 的结构化方法**：

- Layer 0 强制规则（"绝不主动认错""先问 impact"）
- Layer 2 口头禅和高频词
- Layer 3 决策偏好（什么推、什么拖）
- Layer 4 对上对下不同态度

每一条都在对抗 AI 的默认行为 — 礼貌、中立、全面、没有立场。最终效果是让输出有偏见、有习惯、有脾气，像个真人。

## 商业化思考

SaaS 化方向：用户上传数据 → 向量化 + 人格蒸馏 → 界面内对话。

潜在场景：
- 亲人/长辈的知识传承
- 离职同事的经验保留
- 个人数字分身

主要挑战不在技术，在伦理：
- 同意权（逝者无法同意）
- 心理影响（是疗愈还是让人走不出来）
- 滥用风险
- 文化禁忌

## 总结

**技术上不厉害，但产品设计和传播能力很强。** 核心价值不在代码，在于：
1. 一套去 AI 味的 prompt 框架（实用）
2. "蒸馏同事"这个概念包装（传播）
3. 中国职场文化的精准洞察（共鸣）

作为一个 Claude Code skill 来说，是完成度很高的参考案例。
