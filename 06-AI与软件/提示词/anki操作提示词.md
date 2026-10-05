通过 Anki MCP 连接本地 Anki，从用户提供的文件或文本中提取英语单词、常用短语，制作完整卡片并批量添加到 Anki。

# 一、固定配置

牌组：

```text
常用词汇整理
```

笔记类型：

```text
雅思带例句图片
```

字段顺序：

```text
单词 | 音标 | 词性 | 中文 | 单词发音 | 图片 | 例句展示 | 例句发音 | 例句翻译 | 助记符
```

标签：

```text
常用词汇
AI生成
用户提供
```

音频：

```text
edge_tts
en-US-JennyNeural
```

Agnes AI：

```text
接口：https://apihub.agnes-ai.com/v1/images/generations
模型：agnes-image-2.0-flash
Key：cpk-HjPwZ0N7kfJjW24jlYhtFGULCcBt55jIg20qMNxjn34f2b5t
尺寸：1024x768
```

# 二、输入方式

用户可能通过以下方式提供词汇：

- 直接粘贴文本；
    
- TXT、Markdown、CSV等文本文件；
    
- Word、Excel、PowerPoint等Office文档；
    
- PDF文档；
    
- JPG、PNG、截图、扫描件等图片；
    
- 同时上传多个不同格式的文件。
    

必须先解析用户提供的全部输入，再继续制卡。

不自动生成用户材料中不存在的词汇，不自动补足数量。

# 三、文件解析与词汇提取

根据文件类型选择合适的解析方式：

- TXT、Markdown、CSV：直接读取文本；
    
- Word：读取正文和表格；
    
- Excel：读取包含词汇的工作表和单元格；
    
- PowerPoint：读取幻灯片文字和表格；
    
- PDF：提取正文和表格；扫描版PDF使用OCR或视觉识别；
    
- 图片或截图：使用视觉识别或OCR提取内容。
    

优先使用文件的原生文本结构。只有无法直接读取时才使用OCR。

从解析结果中提取：

1. 独立英语单词；
    
2. 常用英语短语；
    
3. 英文与中文对照词条；
    
4. 表格中的词汇和释义；
    
5. 用户明确标注需要学习的词汇。
    

提取规则：

- 只提取有实际学习价值的英语单词或短语；
    
- 短语一般不超过4个单词；
    
- 保留常见固定搭配和工程术语；
    
- 不把完整句子当作词汇；
    
- 不提取网址、邮箱、代码、编号、单位、文件名和乱码；
    
- 不提取纯数字、孤立字母、专有名词、品牌名和人名；
    
- 不把HTML标签、表格标题或页眉页脚误当词汇；
    
- 文件中有明确词汇表时，优先采用该词汇表；
    
- 英文旁边有中文释义时，同时提取中文释义；
    
- 用户直接提供的词汇优先级高于系统自动识别结果。
    

如果某个词存在明显识别错误，应结合上下文修正；无法确定时跳过并记录。

解析完成后，先输出或记录：

```text
文件数量：
成功解析文件数：
解析失败文件数：
提取词汇数：
无效内容数：
```

# 四、执行顺序

必须按照以下顺序执行：

1. 连接Anki MCP。
    
2. 检查牌组「常用词汇整理」是否存在。
    
3. 检查笔记类型「雅思带例句图片」是否存在。
    
4. 解析用户提供的文本、图片和文档。
    
5. 提取英语单词或短语及已有中文释义。
    
6. 清理无效内容。
    
7. 读取牌组中所有已有卡片的「单词」字段。
    
8. 对提取词汇进行内部查重和牌组查重。
    
9. 只保留需要新增的词汇。
    
10. 生成卡片文字字段。
    
11. 生成单词音频和例句音频。
    
12. 使用Agnes AI为每个新词生成一张图片。
    
13. 检查媒体文件并保存到Anki媒体库。
    
14. 保存JSON和Excel。
    
15. 完成以上工作后，批量写入Anki。
    
16. 输出完整统计。
    

如果牌组、笔记类型或已有单词字段无法读取，立即停止。

禁止修改或删除已有卡片，禁止扫描整个牌组补图或补音频。

# 五、查重规则

查重范围：

```text
deck:"常用词汇整理"
```

规范化处理：

- 全部转为小写；
    
- 删除首尾空格；
    
- 多个空格合并为一个；
    
- 连字符和下划线统一视为空格；
    
- 删除多余标点；
    
- 忽略简单单复数差异。
    

以下视为重复：

```text
Pump / pump / pumps
control panel / control-panel / control_panel
check valve / check-valve
```

简单复数至少识别：

```text
s
es
辅音字母+y → ies
```

疑似重复时宁可跳过，不要重复写入。

重复词不得生成音频和图片，也不得写入Anki。

# 六、卡片字段

每个新词生成：

```json
{
  "单词": "",
  "音标": "",
  "词性": "",
  "中文": "",
  "单词发音": "",
  "图片": "",
  "例句展示": "",
  "例句发音": "",
  "例句翻译": "",
  "助记符": ""
}
```

要求：

- 单词：保持提取到的正确英文形式；
    
- 音标：标准美式IPA，放在 `/ /` 中；
    
- 词性：使用 `n.`、`v.`、`adj.`、`adv.`、`phr.`、`n. phr.`、`v. phr.` 等；
    
- 中文：优先采用原文件中的释义，否则生成简洁准确的释义；
    
- 例句：自然完整，建议8至18个英文单词；
    
- 目标词用 `<b>` 和 `</b>` 加粗；
    
- 翻译必须准确对应英文例句；
    
- 助记符简短，不超过25个汉字，不编造词源。
    

示例：

```html
Please <b>inspect</b> the motor before starting the pump.
```

# 七、文件名规则

将词汇转换为安全文件名：

- 转为小写；
    
- 空格和连字符转为下划线；
    
- 删除撇号、括号、斜线和标点；
    
- 只保留英文字母、数字和下划线；
    
- 合并连续下划线；
    
- 删除首尾下划线。
    

示例：

```text
control panel → control_panel
check-valve → check_valve
operator's room → operators_room
```

# 八、音频生成

缺少依赖时执行：

```bash
python -m pip install edge-tts
```

语音：

```text
en-US-JennyNeural
```

单词音频：

```text
word_规范化单词.mp3
[sound:word_inspect.mp3]
```

例句音频：

```text
sentence_规范化单词.mp3
[sound:sentence_inspect.mp3]
```

例句音频朗读去除HTML标签后的英文例句。

要求：

- 只处理本次查重后的新词；
    
- 每个词生成一个单词音频和一个例句音频；
    
- 文件存在且大于1KB后才能填写字段；
    
- 单个音频失败时字段留空并继续；
    
- 不使用其他TTS服务。
    

# 九、Agnes AI图片

每个新词最多调用一次Agnes AI：

```python
AGNES_API_URL = "https://apihub.agnes-ai.com/v1/images/generations"
AGNES_API_KEY = "cpk-HjPwZ0N7kfJjW24jlYhtFGULCcBt55jIg20qMNxjn34f2b5t"

headers = {
    "Authorization": f"Bearer {AGNES_API_KEY}",
    "Content-Type": "application/json"
}

payload = {
    "model": "agnes-image-2.0-flash",
    "prompt": image_prompt,
    "size": "1024x768",
    "extra_body": {"response_format": "url"}
}
```

图片提示词必须根据单词、中文释义和例句场景，用英文具体描述，不得只提交单词。

通用模板：

```text
A clear realistic learning image representing “[word]” in its specific meaning. Show one clear object, action, daily-life, workplace, construction, maintenance or engineering scene. Simple composition, natural lighting, realistic details, visually easy to understand, suitable for English vocabulary learning. No text, words, letters, numbers, caption, label, watermark or logo.
```

文件名：

```text
image_规范化单词.png
```

根据真实格式也可保存为 `.jpg` 或 `.webp`。

Anki字段：

```html
<img src="image_inspect.png">
```

质量检查：

- 文件真实存在；
    
- 大于5KB；
    
- 是可正常读取的图片；
    
- 不是HTML或JSON错误页；
    
- 与词义基本对应；
    
- 没有明显文字、水印或Logo。
    

生成、URL获取、下载或检查失败时：

- 图片字段留空；
    
- 记录失败原因；
    
- 不重试；
    
- 不使用其他图片来源。
    

# 十、媒体、JSON和Excel

音频和图片先保存到临时目录，检查合格后写入Anki媒体库。

媒体文件成功进入Anki媒体库后，才能填写媒体字段。

批量写入Anki前必须保存：

```text
vocabulary_cards.json
vocabulary_cards.xlsx
```

Excel字段顺序必须与Anki字段一致。

可在字段后增加：

```text
来源文件
原始位置
处理状态
失败原因
```

JSON和Excel中不得保存Agnes API Key。

如果JSON或Excel保存失败，禁止写入Anki。

# 十一、批量写入

全部查重、字段生成、媒体处理及备份完成后，才可批量写入Anki。

要求：

- 只写入本次提取并查重合格的新词；
    
- 使用指定牌组和笔记类型；
    
- 字段名称和顺序完全一致；
    
- 添加标签：
    

```text
常用词汇
AI生成
用户提供
```

- 不修改、不删除已有卡片；
    
- 单张写入失败时记录原因并继续。
    

# 十二、输出统计

任务结束后输出：

```text
输入文件数：
成功解析文件数：
解析失败文件数：
原始提取词汇数：
无效内容数：
内部重复数：
与牌组已有词汇重复数：
最终新增词汇数：

单词音频成功数：
单词音频失败数：
例句音频成功数：
例句音频失败数：

Agnes AI调用次数：
图片成功数：
图片失败数：

JSON保存路径：
Excel保存路径：

Anki写入成功数：
Anki写入失败数：
```

同时列出：

```text
解析失败文件及原因
被过滤内容及原因
重复词汇及原因
音频失败词汇及原因
图片失败词汇及原因
Anki写入失败词汇及原因
```

核心原则：

```text
先解析用户文件，再提取词汇。
只处理用户材料中的单词和短语。
先查重，再生成卡片和媒体。
先保存媒体、JSON和Excel，再批量写入Anki。
不自动补词，不修改已有卡片。
```