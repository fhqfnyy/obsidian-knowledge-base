---
title: "标签与Frontmatter规范"
aliases: []
tags: [学习]
created: 2026-10-05
updated: 2026-10-05
status: active
type: reference
---

# 标签与Frontmatter规范

## 使用规则

- 文件夹管理归属，链接表达概念，标签管理横向状态与领域。
- 主标签建议：工作、项目、工程、电气、自动化、英语、法语、AI、学习、资料、待整理。
- 保留 Omnivore、excalidraw、flashcard、copilot-conversation 等插件标签及其字段，不批量清理。
- 不改代码里的 `#变量`、CSS 色值、Markdown 标题和链接锚点。
- 英语学习/english 可规范为英语；工业自动化可规范为自动化，但需要先核查各笔记语义。
- 不把“西门子PLC”和“PLC”无条件当成同义词；品牌范围可以用专题链接保留。
- `create`、`date` 可能是日记或词卡字段，不直接替换为 `created`。
- `tag` 与 `tags` 必须检查插件依赖后再处理。

## 新笔记的最小字段

```yaml
---
title: 主题标题
aliases: []
tags: [工程]
status: draft
type: concept
---
```

created、updated 只记录真实时间，不从整理日期伪造历史创建日期。可选类型 concept、project、reference、language、meeting、troubleshooting、moc。旧笔记不强行补齐，新建导航已使用统一字段。

## 当前 Frontmatter 事实

- 真正以换行 YAML 头开始的文件：185 篇。
- 保守标签扫描识别 45 种字符串（包括插件标签与异常逗号列表；不是实时索引总数）。
- 以下 5 篇使用字面 `\n`，Obsidian 不会把它当成正常 Frontmatter。暂不修改正文，以免影响引用。

- [[人生智慧九大原则|人生智慧九大原则]]
- [[原技术文档-变频器制动电压升高问题详解|原技术文档-变频器制动电压升高问题详解]]
- [[变频器制动电压升高问题|变频器制动电压升高问题]]
- [[变频器制动电压升高问题详解|变频器制动电压升高问题详解]]
- [[施工常用词汇完善日记|施工常用词汇完善日记]]

## 扫描识别的标签字符串

- `AI`：14
- `Omnivore`：35
- `VPN`：1
- `brain-computer-interface`：1
- `consciousness-uploading`：1
- `copilot-conversation`：5
- `cybernetics`：1
- `english`：1
- `essay`：1
- `excalidraw`：14
- `family`：1
- `fiction`：2
- `identity-crisis`：1
- `language`：1
- `learning`：1
- `lighthouse`：1
- `literature`：1
- `nature`：1
- `personal-growth`：1
- `reading`：1
- `scifi`：1
- `scifi, short-story, brain-computer-interface, consciousness-uploading, transhumanism, identity-crisis, cybernetics`：1
- `short-story`：2
- `tradition`：1
- `transhumanism`：1
- `writing`：2
- `三维制图`：2
- `个人心得`：3
- `双语例句`：1
- `商务英语`：1
- `工业自动化`：1
- `工作`：58
- `工程`：1
- `欧佐内水厂`：1
- `水工艺`：1
- `法语`：2
- `电气制图`：1
- `编程`：4
- `英语`：6
- `英语学习`：1
- `视频笔记`：1
- `解说词`：1
- `计算机`：1
- `论文`：6
- `金沙萨`：1

## 本轮已执行

- 修复5篇字面反斜杠n的伪 Frontmatter，仅转换元数据头，正文不做全局替换。
- 在已确认语义处规范：英语学习/english → 英语；工业自动化 → 自动化；电气工程 → 电气。
- 保留旧标题别名、插件字段和专业细分标签；不更改日记 create、词卡 date 和 todo 标签。
- 模板、闪卡、剪藏和Excalidraw字段未强制统一。
