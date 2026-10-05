# Obsidian CLI 详细教程笔记

> 📺 视频来源：[YouTube](https://www.youtube.com/watch?v=WfH8l_b4JiU)
> 📅 整理日期：2026-04-03
> 🏷️ 标签：#Obsidian #CLI #AI智能体 #自动化

---

## 概述

Obsidian 官方推出了 **Obsidian CLI** 命令行工具，这是一个激进的更新，目的是让 Obsidian 全面拥抱 AI 智能体时代。

### 核心价值

- **节省 Token**：AI 不需要扫描整个笔记库，通过 CLI 直接查询内部结构和索引
- **实时同步**：通过 IPC 进程间通信，修改立即在 UI 生效
- **保持一致性**：自动维护内链、标签、元数据的一致性

---

## 开启 Obsidian CLI

### 步骤

1. 更新 Obsidian 到 **1.12 以上**版本
2. 设置 → 关于 → 检查更新
3. 滚动到最下方，打开「命令行界面」开关
4. 确认注册 Obsidian 到 PATH

### 注意事项

⚠️ **使用 CLI 时，Obsidian 程序必须处于运行状态**

---

## 核心原理

### 传统方式 vs CLI 方式

| 传统方式 | CLI 方式 |
|---------|---------|
| AI 直接修改文件系统 | AI → CLI → Obsidian 进程 (IPC) |
| 单纯新建 Markdown 文件 | 触发 Obsidian 内部机制（模板、日记等） |
| 需要扫描整个笔记库 | 直接查询索引，一条命令即可 |
| 消耗几百万 Token | 只需约 100 Token |

### 架构图

\\\
AI 智能体 → Obsidian CLI → Obsidian 程序进程 → 笔记库
\\\

---

## 常用命令示例

### 创建日记

\\\ash
obsidian daily
\\\

触发日记插件，使用模板创建当日日记。

### 查询标签

\\\ash
obsidian tags --sort=count --format=json
\\\

列出所有标签，按数量排序，JSON 格式输出。

---

## 让智能体使用 Obsidian CLI

### 安装 Skill

1. 访问 GitHub 搜索 **Kepano**（Obsidian CEO Steph 的账号）
2. 找到 **obsidian-skill** 仓库
3. 下载 skills 文件夹中的所有 skill
4. 安装到你的智能体工具（Claude Code、OpenClaw、Gemini CLI 等）

### 实际案例：获取 AI 论文并创建日记

**提示词示例：**

> 获取 arXiv 上 AI 目录下最新 10 篇论文，读取信息，翻译并整理成表格，然后在我的 Obsidian 中创建一篇日记，把表格添加进去，标题为「今日 AI 论文」。

智能体会自动调用 Obsidian CLI skill 完成任务，并触发日记模板机制。

---

## 在代码/工作流中使用

### Python 示例

\\\python
import subprocess

# 创建笔记并指定模板
subprocess.run([
    'obsidian', 'create',
    '--template', '周计划与复盘',
    '--title', '2026三月第二周AI学习资料'
])
\\\

### N8N 工作流

1. 添加 **Execute Command** 节点
2. 执行 Python 脚本或直接运行 CLI 命令

---

## Obsidian CLI 的核心优势

1. **降低 Token 消耗** - 从几百万降到几百
2. **感知笔记库结构** - 双链、标签、插件状态
3. **保持数据一致性** - 移动文件自动更新内部链接
4. **触发 Obsidian 机制** - 模板、日记、插件联动

---

## Steph 发布的 Skills

Obsidian CEO (Kepano) 已发布 5 个 skill：

| Skill | 功能 |
|-------|------|
| obsidian-cli | 命令行操作 Obsidian |
| 网页内容提取 | 提取网页内容转 Markdown |
| （其他 3 个） | 详见 GitHub |

---

## 建议

- **确定性任务** → 用工作流/代码，而非智能体
- **Token 敏感** → 尝试 Gemini CLI、OpenCode（免费额度多）
- **主要场景是 Obsidian** → 把 CLI skill 装进智能体

---

## 相关链接

- 🎬 [视频原址](https://www.youtube.com/watch?v=WfH8l_b4JiU)
- 🐙 [Steph GitHub (Kepano)](https://github.com/kepano)
- 📚 [Obsidian CLI 官方文档](https://help.obsidian.md)

---

*笔记由 QClaw 自动整理生成*