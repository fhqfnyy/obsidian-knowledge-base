---
title: Wiki 创建笔记
type: welcome
created: 2026-07-15
llm_config_status: ok
llm_config_provider: custom
llm_config_model: MiniMax-M3
---

# 欢迎使用你的 LLM-Wiki

此笔记由 Karpathy LLM Wiki 插件在首次运行时自动生成。你无需编辑它 — 阅读一次,然后开始摄取即可。

## 如何验证安装

如果你能以你的 wiki 语言阅读本文,说明安装正常工作。

LLM 配置本身可通过 **设置 → Karpathy LLM Wiki → LLM Provider → Test Connection** 进行验证(寻找 ✅ 标记)。

## 如何使用此插件

使用 `Ctrl/Cmd + P` 打开命令面板,然后搜索 "Karpathy LLM Wiki"。下面的第一条命令是你第一天唯一需要的命令。

| 命令 | 功能 |
| --- | --- |
| `Karpathy LLM Wiki: Ingest multiple files` | 选择 N 个源笔记;插件从每个笔记中提取实体/概念/来源,并写入 wiki 页面。**第一天 — 从这里开始。** |
| `Karpathy LLM Wiki: Ingest single source` | 同上,但仅针对单个文件。 |
| `Karpathy LLM Wiki: Ingest from folder` | 摄取所选文件夹中的每个文件(例如 `inbox/2024/`)。 |
| `Karpathy LLM Wiki: Query Wiki` | 打开右侧聊天面板,以针对已摄取内容进行提问。 |
| `Karpathy LLM Wiki: Lint wiki` | 运行 Lint 流水线(死链、孤立页、重复项)。当 wiki 拥有约 30+ 个页面时使用。 |
| `Karpathy LLM Wiki: View Ingestion History` | 打开一个面板,列出每次 Ingest 调用创建/更新的内容。 |
| `Karpathy LLM Wiki: Recreate Wiki Welcome Note` | 以当前 wiki 语言重新创建此笔记。 |

右侧的 Query 面板也可以通过聊天气泡功能区图标打开。

## Wiki 结构的含义

摄取源笔记后,插件会在你的 `wiki/` 文件夹中写入一小批页面。了解三种核心页面类型 — 以及顶部的可选 Schema 层 — 是你开始手动整理 wiki 时最有用的背景知识。

### 三种核心页面类型

- **`entities/`** — 有名称的事物:人物、组织、项目、产品、事件、地点。单个源笔记通常会生成多个实体页面。每个实体页面包含别名、摘要、出现的源笔记(`mentions_in_source`),以及指向相关实体和概念的链接。
- **`concepts/`** — 主题、方法、定义、研究领域、反复出现的主题。"PPR"、"心脏病学"、"模式驱动设计" 都是概念。概念页面链接到其他概念页面;实体页面链接到概念页面。
- **`sources/`** — 每个摄取的源笔记对应一个页面,包含原始内容以及 `source_file` frontmatter 字段。源页面是来源锚点 — 每个实体/概念页面都列出提及它的源页面,因此读者可以从某个主题追溯回原始笔记。

### Schema 层(可选)

你可以通过 **设置 → Karpathy LLM Wiki → Schema** 启用 Schema。启用后,插件会维护一个 `wiki/schema/` 文件夹,用于编码你的 wiki 词汇表 — 受控的标签类别、章节模板以及实体/概念类型列表。

Schema 存在于一个 Obsidian 格式的页面中:[[wiki/schema/config]]。打开它以查看当前生效的词汇表;每当词汇表发生变化时,插件都会重写此页面。

- Ingest 提示与该词汇表绑定,因此 LLM 从固定列表中选择标签和章节标题,而不是发明自由格式的文本。
- 当 LLM 注意到漂移时(例如出现词汇表中没有的新概念),建议会出现在 Lint 报告模态框中 — 在其生效前你可以接受/拒绝。
- 当词汇表发生变化时,每个现有页面都会被重写以匹配新词汇表(自动备份至 `.llm-wiki-backups/schema/`,轮换 MAX_BACKUPS=3)。

即使没有 Schema,插件仍可工作 — 三种核心类型始终会被创建。Schema 增加了结构,使查询在成熟的 wiki 上更加可靠。

### Wikilink 图

两个页面之间的每个 `[[wiki-link]]` 都是 LLM 在摄取时建立的关系。插件使用此图(而非嵌入)进行查询检索 — 有关图引擎架构,请参阅 v1.23.0 发布说明。实用要点:精心策划的 wikilink 图就是 wiki 的"搜索索引"。你可以手动在任何页面中添加或编辑 `[[X]]` 链接,下一次查询将会识别它们。

### 文件夹布局

所有 wiki 文件都位于 `wiki/` 之下(可在 设置 → Wiki folder 中配置):

```
wiki/
├── entities/    # 有名称的事物(人物、组织、项目)
├── concepts/    # 主题、方法、定义
├── sources/     # 每个摄取笔记对应一个页面(来源)
├── schema/      # 可选词汇表 + 章节模板
├── index.md     # 自动生成的图索引
└── log.md       # 自动生成的活动日志
```

## 快速开始

1. **选择一些要摄取的源笔记。** 从命令面板运行 `Karpathy LLM Wiki: Ingest multiple files`。勾选你想要的笔记,然后点击 **Add to queue**。模态框保持打开状态,你可以查看进度。
2. **等待摄取完成。** 每个笔记需要 10–60 秒(LLM 提取)。你可以继续工作 — 完成后会以通知形式出现。`View Ingestion History` 列出每个批次的结果。
3. **尝试查询。** 打开右侧的 Query Wiki 面板(聊天气泡功能区图标),就你的内容提出问题。
4. **根据需要调整设置。** 设置 → Karpathy LLM Wiki:语言、wiki 文件夹、schema、标签词汇表、自动监听。默认值对新库来说是合理的。

> 完整指南见 README:github.com/green-dalii/obsidian-llm-wiki