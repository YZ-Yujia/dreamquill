# Dreamquill for zcode

在 zcode 中辅助长篇小说创作的 AI 写作插件：五阶段创作流程（创意构思 → 全文大纲 → 分卷大纲 → 章节大纲 → 正文），全部产物（正文 / 三级大纲 / 世界观 / 角色 / 记忆 / 风格）落在书目录的 markdown 文件中。**项目根目录即书目录**（一书一项目），章节序号全书连续（跨卷不重置）。

- **48 个 MCP 工具**：书初始化与阶段、卷/章目录管理（建删改名 / 卷内定位移动 / 分侧建章与按章序补建）、产物读合一 `readManuscript`（四 scope ＋ `metaOnly` 定位档）、分层上下文组装（会话级静态层＋本章动态层＋全量恢复；触发记忆＋总预算报告）、记忆知识层检索、部分字段 upsert、角色与关系对维护、伏笔回收、定稿收口 `draftFinalizePack`（定稿＋一致性审稿＋记忆提炼＋状态盘点一次裁决）、风格规范四级粒度编辑、书元数据与全书统计、句式 lint、进度看板。
- **1 个 skill**（`dreamquill-writing`）：五阶段流程引导＋两处硬确认纪律＋写后三级审阅回路＋原生文件工具分工＋项目 `AGENTS.md` 持久创作上下文维护。
- **1 个 agent**（`dreamquill-reviewer`）：三级审阅子代理——细纲符合性 → 逐句节奏 → 逐字用词（含句式 lint 机械枚举候选，模型只裁不找；只读工具）。
- **3 个 command**：`/阶段切换`、`/定稿收口`、`/审阅`。

内容读写走 zcode 原生 Read/Edit/Write（产物就是普通 markdown，作者保有完全掌控）；插件工具专司结构与语义（组装 / 检索 / 结构操作 / 条目格式写）。

## 安装

本仓库即 zcode 插件市场（marketplace），构建产物 `plugin/dist/mcp/server.js` 已入库，克隆即可安装，无需本地构建。

仓库地址：`https://github.com/YZ-Yujia/dreamquill.git`（市场名 `dreamquill-local`，插件名 `dreamquill`）

在 zcode 中添加该 git 仓库作为插件市场，安装 `dreamquill@dreamquill-local`，然后重启会话即可使用。未初始化时插件引导 `initBook` 在项目根上初始化（项目根即书目录）。可用 `getCurrentBook` 验证连通——server 启动自动打开已初始化的书。

前置：zcode 宿主自带 Node 运行时（插件 MCP server 以 `node` 启动，Node ≥ 22）。

## 使用入门

直接对 agent 说「开始写小说」或运行 `/阶段切换`——skill 会引导五阶段流程。整章写入后自动进入三级审阅（违例一次改写后交付，可 `/审阅` 手动复查）；作者认可后 `/定稿收口`——`draftFinalizePack` 定稿（单向不回退）＋一次裁决一致性审稿＋记忆提炼＋状态盘点（伏笔埋设/推进/回收）；已定稿章修改完毕再调＝增量对账。

## 上下文预算（单层总预算）

组装注入的全部内容（记忆/设定/大纲/前文/摘要）受**单层总预算**约束：**容量 × 阈值** ＝ 注入上限，超出按块优先级整块截断（前文→摘要→风格→世界→角色→记忆→细纲）。

- 插件配置：`context_capacity`（默认 1,000,000 token）＋ `budget_percent`（默认 60%）＋ `world_ref_depth`（世界条目 [[引用]] 递归展开层数上限，默认 10；成环自动跳过）。
- 预算管的是「组装注入的附加上下文」；宿主会话自身的系统提示/工具定义/对话历史不在插件可见范围。请把 `context_capacity` 设为宿主模型上下文窗口的保守值（留出对话余量）。
- 超限裁剪在组装报告（`getWritingContext` 尾注）中完整披露，agent 可向作者转述。

## 赞赏

如果 Dreamquill 帮你写出了满意的章节，欢迎请作者喝杯咖啡：

<p align="center">
  <img src=".github/assets/wechat-qr.png" alt="微信赞赏码" width="220">
</p>

## English

AI-assisted novel writing in zcode: a five-stage creation workflow (ideation → full outline → volume outline → chapter outline → drafting) with a three-round post-writing review loop (outline compliance → sentence rhythm → wording & sentence patterns, benchmarked against the book's style rules). Manuscript and outlines live in separate directory trees; the project root opened in zcode IS the book directory (one book per project); chapter numbering is continuous across volumes. 48 MCP tools: unified manuscript reading (four scopes + meta-only locator), layered context assembly with memory triggering & budget, volume & chapter management, partial-field upserts, character & relation maintenance, foreshadow resolution, finalize pack (finalize + consistency review + memory distillation + status audit), granular style-spec editing, sentence-pattern lint, progress board. Content reads/writes go through native zcode file tools (plain markdown — the author stays in control); plugin tools own structure & semantics. One workflow skill, one reviewer agent, three commands. This repository is also a zcode plugin marketplace. Licensed under Apache-2.0.

## 协议

[Apache-2.0](LICENSE)。"Dreamquill" 名称与相关标识不属于该协议授权范围（见 [NOTICE.txt](NOTICE.txt)）。
