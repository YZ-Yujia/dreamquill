# Dreamquill zcode 插件

在 zcode 中辅助小说创作的 AI 写作插件：五阶段创作流程（创意构思→全文大纲→分卷大纲→章节大纲→正文），全部产物（正文/三级大纲/世界观/角色/记忆/风格）落在书目录的 markdown 文件中（**正文域与大纲域独立目录树**）。**项目根目录即书目录**（一书一项目），章节序号全书连续（跨卷不重置）。

- **48 个 MCP 工具**：书初始化与阶段、卷/章目录管理（建删改名/卷内定位移动/**分侧建章与按章序补建**——`createChapter` 默认只落细纲骨架，正文原生 Write 规划路径即自动配对；卷序/章序定位）、**产物读合一 `readManuscript`**（章正文/章纲/分卷大纲/全文大纲四 scope＋`metaOnly` 定位档——只取路径状态不拖正文，未建文件带补建指引）、**分层上下文组装**（会话级静态层＋本章动态层＋全量恢复；触发记忆＋总预算报告）、记忆知识层检索、**部分字段 upsert**（更新只改传入字段；类型自动注册）、角色与关系对维护（增删改名改）、伏笔回收、**定稿收口 `draftFinalizePack`**（定稿＋一致性审稿＋记忆提炼＋状态盘点一次裁决；再调＝已定稿章增量收口）、**风格规范四级粒度编辑**（title 锚定小节内替换/整节替换/新增/删除）、书元数据与全书统计、**句式 lint**、**进度看板**（书根 `进度看板.md` 自动生成——全书统计/主体状态跨域聚合/未回收伏笔〔定稿章距基准〕/记忆概览＋三组装工具触发快照；作者视图，agent 勿读）。
- **1 个 skill**（`dreamquill-writing`）：五阶段流程引导＋两处硬确认纪律（切阶段、记忆入库）＋**写后三级审阅回路**＋**原生文件工具分工**（内容读写全部走 zcode 原生 Read/Edit/Write——五类内容区路径经清单与 metaOnly 透出；插件工具专司结构与语义：组装/检索/结构操作/条目格式写）＋项目 `AGENTS.md` 持久创作上下文维护。
- **1 个 agent**（`dreamquill-reviewer`）：三级审阅子代理，**检核表式审查**（判据逐条分解独立判定、命中逐处列出；文字模式判据经 lint＋Grep 机械枚举候选——模型只裁不找；语义判据按段遍历＋扫后自查；细纲符合性→逐句节奏→逐字用词含句式；只读工具）。
- **3 个 command**：`/阶段切换`、`/定稿收口`、`/审阅`。
- 误写恢复依赖 zcode checkpoint/rewind；插件不自建版本管理。

## 安装

本插件经 Dreamquill 自建 git 市场分发（`dist/` 构建产物已入库，安装即用，无需构建）：

```
/plugin marketplace add <你的GitHub用户名>/dreamquill-zcode
/plugin install dreamquill@dreamquill-local
```

完整说明见[仓库根 README](../../README.md)。

## 上下文预算（单层总预算）

组装注入的全部内容（记忆/设定/大纲/前文/摘要）受**单层总预算**约束：**容量 × 阈值**＝注入上限，超出按块优先级整块截断（前文→摘要→风格→世界→角色→记忆→细纲）。

- 插件配置（zcode 插件设置）：`context_capacity`（默认 1,000,000 token，可调）＋ `budget_percent`（默认 **60%**，可调）＋ `world_ref_depth`（世界条目 [[引用]] 递归展开层数上限，默认 **10**；成环自动跳过）。
- **预算管的是「组装注入的附加上下文」**——宿主会话自身的系统提示/工具定义/对话历史不在插件可见范围内（方案 B 既定边界）。因此把 `context_capacity` 设为宿主模型上下文窗口的保守值（留出对话余量），而非 1M 全额。
- 超限裁剪在组装报告（`getWritingContext` 尾注）中完整披露：被截断的块清单与记忆条数上限外的条目，agent 可向作者转述。

## 使用入门

直接对 agent 说「开始写小说」或运行 `/阶段切换`——skill 会引导五阶段流程。整章写入后自动进入三级审阅（违例一次改写后交付，可 `/审阅` 手动复查）；作者认可后 `/定稿收口`——`draftFinalizePack` 定稿（单向不回退）＋一次裁决一致性审稿＋记忆提炼＋状态盘点（伏笔埋设/推进/回收）；已定稿章修改完毕再调＝增量对账。

## English

AI-assisted novel writing in zcode: five-stage creation workflow (ideation → full outline → volume outline → chapter outline → drafting) with a three-round post-writing review loop (outline compliance → sentence rhythm → wording & sentence patterns, benchmarked against the book's style rules). Manuscript and outlines live in separate directory trees (正文/ and 大纲/). The project root opened in zcode IS the book directory (one book per project); chapter numbering is continuous across volumes. 48 MCP tools: unified manuscript reading (four scopes + meta-only locator), layered context assembly with memory triggering & budget, volume & chapter management incl. intra-volume repositioning, partial-field upserts with type auto-registration, character & relation maintenance, foreshadow resolution, consistency review, four-level granular style-spec editing, sentence-pattern lint. Content reads/writes go through native zcode file tools (paths disclosed via list/metaOnly); plugin tools own structure & semantics (index linkage, renumbering, pairing, reference rewrite, assembly engine). One workflow skill, one reviewer agent, three commands. Single-tier context budget: capacity (default 1M, configurable) × threshold (default 60%, configurable). Undo relies on zcode checkpoints; the plugin keeps no own versioning. Install via the Dreamquill git marketplace: `/plugin marketplace add <user>/dreamquill-zcode` then `/plugin install dreamquill@dreamquill-local` — built artifacts are committed, no local build needed.
