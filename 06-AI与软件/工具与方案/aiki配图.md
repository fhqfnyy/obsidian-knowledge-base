通过 Anki MCP 连接本地 Anki，为「每日英语词汇」牌组新增 30 个英语词汇卡片。

任务只做 5 件事：

1. 生成 30 个不重复的新词汇
    
2. 生成单词音频
    
3. 生成例句音频
    
4. 为这 30 个词配图
    
5. 保存 JSON、Excel，并批量写入 Anki
    

注意：

本任务只处理本次新生成的 30 个词。

不要扫描整个牌组补图。

不要修改已有卡片。

不要删除已有卡片。

---

# 一、Anki 信息

牌组名称：

每日英语词汇

笔记类型：

雅思带例句图片

字段必须为：

单词、音标、词性、中文、单词发音、图片、例句展示、例句发音、例句翻译、助记符

字段顺序必须为：

单词 | 音标 | 词性 | 中文 | 单词发音 | 图片 | 例句展示 | 例句发音 | 例句翻译 | 助记符

标签：

每日词汇、AI生成

---

# 二、执行顺序

必须严格按照下面顺序执行：

1. 连接 Anki MCP。
    
2. 检查牌组「每日英语词汇」是否存在。
    
3. 检查笔记类型「雅思带例句图片」是否存在。
    
4. 读取「每日英语词汇」牌组中所有已有卡片的「单词」字段。
    
5. 根据已有词汇建立查重集合。
    
6. 生成候选词汇。
    
7. 与已有词汇查重。
    
8. 与本次候选词内部查重。
    
9. 得到最终 30 个不重复的新词汇。
    
10. 为这 30 个词生成单词音频。
    
11. 为这 30 个词生成例句音频。
    
12. 为这 30 个词下载或生成图片。
    
13. 组装完整 Anki 字段数据。
    
14. 保存 JSON 和 Excel 到本地。
    
15. 批量写入 Anki。
    
16. 输出执行统计结果。
    

禁止先写入 Anki 再补图。

禁止未查重就生成音频、图片或写入 Anki。

---

# 三、查重规则

生成新词汇前，必须先通过 Anki MCP 读取「每日英语词汇」牌组中所有已有卡片的「单词」字段。

搜索范围：

deck:"每日英语词汇"

读取字段：

单词

生成的 30 个新词汇必须同时满足：

1. 不得与「每日英语词汇」牌组已有词汇重复。
    
2. 本次生成的 30 个词之间不得重复。
    
3. 如果候选词与已有词汇重复，必须丢弃。
    
4. 如果本次候选词内部重复，必须丢弃。
    
5. 最终必须得到 30 个不重复的新词汇后，才能继续生成音频和图片。
    

重复判断时忽略：

1. 大小写差异
    
2. 前后空格
    
3. 连字符和下划线差异
    
4. 简单复数差异
    
5. 多余空格差异
    

例如以下情况都视为重复：

pump / Pump  
pump / pumps  
control panel / control-panel  
control panel / control_panel  
check valve / check valve  
check valve / check-valve

推荐执行方式：

1. 先生成 40 个候选词。
    
2. 与已有词汇查重。
    
3. 与本次候选词内部查重。
    
4. 保留前 30 个不重复词汇。
    
5. 如果不足 30 个，继续生成候选词并查重，直到补足 30 个。
    

禁止行为：

1. 禁止不读取已有牌组词汇就直接生成。
    
2. 禁止生成后不查重直接写入 Anki。
    
3. 禁止发现重复后仍然写入。
    
4. 禁止用 Anki 添加失败来代替提前查重。
    
5. 禁止只检查本次 30 个词内部重复，而不检查牌组已有词汇。
    

---

# 四、词汇生成要求

一次性生成候选词汇，不要一个词一个词生成。

词汇范围优先包含：

1. 日常生活
    
2. 日常工作
    
3. 电气工程
    
4. 自动化
    
5. 计算机
    
6. 建筑施工
    
7. 工业维护
    
8. 项目管理
    
9. 设备维修
    
10. 海外工程沟通
    

词汇要求：

1. 实用、常见，不要冷僻词。
    
2. 可以是单词，也可以是常用短语。
    
3. 适合英语学习和工程工作场景。
    
4. 最终必须保留 30 个不重复词汇。
    

每个词必须包含：

1. 单词
    
2. 音标
    
3. 词性
    
4. 中文释义
    
5. 英文例句
    
6. 例句中文翻译
    
7. 简短助记符
    

字段格式：

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

---

# 五、音频生成规则

使用 Python edge_tts 生成音频。

语音：

en-US-JennyNeural

如果没有安装 edge_tts，提示安装：

pip install edge-tts

单词音频文件名格式：

word_单词.mp3

例句音频文件名格式：

sentence_单词.mp3

字段格式：

[sound:word_单词.mp3]

[sound:sentence_单词.mp3]

文件名规则：

1. 全部小写
    
2. 空格改为下划线
    
3. 只保留英文字母、数字和下划线
    
4. 不使用中文
    
5. 不使用特殊符号
    

示例：

word_voltage.mp3  
sentence_voltage.mp3  
word_control_panel.mp3  
sentence_control_panel.mp3

如果某个音频生成失败，该音频字段留空，但不要中断整个任务。

---

# 六、图片配图规则

每个新词都要尝试配图。

本任务只给本次新生成的 30 个词配图。

不要扫描整个牌组补图。

图片获取只允许两种方式：

1. firecrawl-toolkit 的 fc.py images 搜图
    
2. fc.py 搜图失败后，使用 Agnes AI 生图兜底
    
图片流程必须是：

fc.py 搜图  
→ 图片质量检查  
→ 合格则保存到 Anki 媒体库  
→ 不合格则 Agnes AI 生图  
→ 图片质量检查  
→ 合格则保存到 Anki 媒体库  
→ 全部失败则图片字段留空

---

# 七、fc.py 图片下载方式

fc.py 所在目录固定为：

C:\Users\Administrator\CodeBuddy\20260706172534\firecrawl-toolkit

执行图片搜索前，必须先进入该目录：

cd C:\Users\Administrator\CodeBuddy\20260706172534\firecrawl-toolkit

首次使用或环境异常时，先执行：

python fc.py setup

本任务统一使用：

python fc.py images "搜索内容" -n 1 -d results\anki_images

要求：

1. 搜图命令必须在 firecrawl-toolkit 目录下执行。
    
2. 不要在其他目录直接执行 fc.py。
    
3. 图片输出目录统一使用：
    

results\anki_images

4. 每个词只搜索 1 张图片。
    
5. 每个词只执行一次 fc.py 搜图。
    
6. 如果这 1 张图片不可用，再使用 Agnes AI 生图。
    
7. 不要反复更换搜索内容。
    
8. 不要无限重试。
    
9. 不要使用旧的图片下载方式。
    
10. 不要使用：
    

python fc.py images "关键词" -n 5 -d pics

必须使用：

cd C:\Users\Administrator\CodeBuddy\20260706172534\firecrawl-toolkit  
python fc.py images "搜索内容" -n 1 -d results\anki_images

---

# 八、图片搜索内容规则

不要转换关键词。

不要把单词改写成其他工程关键词。

不要使用固定映射表。

不要生成复杂搜索词。

每个词只允许使用一个搜索内容。

搜索内容按以下顺序确定：

1. 优先直接使用「单词」字段搜索图片。
    
2. 如果单词是短语，也直接使用该短语搜索图片。
    
3. 如果单词非常抽象、直接搜索明显不适合，则使用「中文释义」搜索图片。
    
4. 如果中文释义也不适合，则使用「单词 + image」搜索图片。
    
5. 不要使用完整例句作为搜索内容。
    

示例：

voltage → voltage  
control panel → control panel  
valve actuator → valve actuator  
maintenance → maintenance  
deadline → deadline  
schedule → schedule  
confirm → confirm

如果直接搜索失败，再由 Agnes AI 根据单词或中文含义生成图片。

---

# 九、图片质量检查

写入 Anki 前必须检查图片：

1. 图片文件真实存在。
    
2. 文件大小大于 5KB。
    
3. 图片可以正常打开。
    
4. 图片宽度大于 200。
    
5. 图片高度大于 150。
    
6. 图片不是空白图。
    
7. 图片不是纯文字图。
    
8. 图片不是单词卡片图。
    
9. 图片不是明显无关图片。
    
10. 图片整体清晰，适合辅助记忆单词。
    

如果 fc.py 下载的 1 张图片不合格，则调用 Agnes AI。

---

# 十、图片保存到 Anki

fc.py 下载的图片目录为：

C:\Users\Administrator\CodeBuddy\20260706172534\firecrawl-toolkit\results\anki_images

选择合格图片后，复制或重新保存到 Anki 媒体库。

图片文件名必须使用英文单词命名，不能出现中文。

fc.py 图片文件名：

image_单词.jpg

Agnes AI 图片文件名：

image_单词_ai.jpg

文件名规则：

1. 全部小写
    
2. 空格改为下划线
    
3. 只保留英文字母、数字和下划线
    
4. 不使用中文文件名
    
5. 不使用特殊符号
    

示例：

voltage → image_voltage.jpg  
control panel → image_control_panel.jpg  
valve actuator → image_valve_actuator.jpg  
check valve → image_check_valve.jpg

禁止出现：

image_电压表.jpg  
image_工业阀门.jpg  
image_西门子阀门执行器.jpg

图片字段格式：

fc.py 图片：

Agnes AI 图片：

---

# 十一、Agnes AI 生图兜底

只有当 fc.py 没有下载到合格图片时，才使用 Agnes AI。

Agnes AI Key：

cpk-HjPwZ0N7kfJjW24jlYhtFGULCcBt55jIg20qMNxjn34f2b5t

接口：

[https://apihub.agnes-ai.com/v1/images/generations](https://apihub.agnes-ai.com/v1/images/generations)

模型：

agnes-image-2.0-flash

请求方式：

POST

请求头：

Authorization: Bearer cpk-HjPwZ0N7kfJjW24jlYhtFGULCcBt55jIg20qMNxjn34f2b5t  
Content-Type: application/json

请求体：

{  
"model": "agnes-image-2.0-flash",  
"prompt": "A clear realistic learning image representing [word or meaning], simple background, no text, no watermark, high quality.",  
"size": "1024x768",  
"extra_body": {  
"response_format": "url"  
}  
}

要求：

1. 每个词最多调用 1 次 Agnes AI。
    
2. 图片中不要有文字。
    
3. 不要水印。
    
4. 不要 Logo。
    
5. 图片要清晰、简洁，有助于记忆单词含义。
    
6. Agnes AI 也失败时，图片字段留空。
    
7. Agnes AI 图片文件名仍然必须使用英文单词命名，不能出现中文。
    

---

# 十二、保存文件

写入 Anki 前保存两个备份文件。

保存目录：

E:\英语学习\每日词汇

文件名：

daily_vocabulary_YYYYMMDD_HHMMSS.json  
daily_vocabulary_YYYYMMDD_HHMMSS.xlsx

Excel 表头：

单词、音标、词性、中文、单词发音、图片、例句展示、例句发音、例句翻译、助记符

JSON 格式：

[  
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
]

---

# 十三、写入 Anki

批量写入 Anki。

要求：

1. 不删除已有卡片。
    
2. 不修改已有卡片。
    
3. 不重复添加已有词汇。
    
4. 一次性批量添加 30 张新卡片。
    
5. 所有字段必须对应正确。
    
6. 音频字段使用 Anki 标准格式：
    

[sound:文件名.mp3]

7. 图片字段使用 HTML 图片格式：
    

---

# 十四、最终输出

任务完成后输出：

1. Anki MCP 连接状态
    
2. 牌组是否存在
    
3. 笔记类型是否存在
    
4. 已有词汇数量
    
5. 本次最终生成词汇数量
    
6. 成功添加数量
    
7. 失败数量
    
8. 失败词汇及原因
    
9. 单词音频成功数量
    
10. 例句音频成功数量
    
11. fc.py 配图成功数量
    
12. Agnes AI 配图成功数量
    
13. 图片失败数量
    
14. JSON 文件路径
    
15. Excel 文件路径
    
16. 新增词汇清单
    

---

# 十五、核心要求

严格保持简单流程：

读取 Anki 已有词汇  
→ 生成候选词  
→ 查重得到 30 个新词  
→ 生成单词音频  
→ 生成例句音频  
→ 下载或生成图片  
→ 保存 JSON 和 Excel  
→ 批量写入 Anki

不要先写入 Anki 再补图。

不要扫描整个牌组补图。

不要修改已有卡片。

本任务只处理本次新生成的 30 个词。

图片只使用：

1. fc.py 搜图
    
2. Agnes AI 生图兜底
    

不要使用其他图片来源。

图片搜索不要转换关键词，优先直接用单词或短语本身搜索。

每个词只找 1 张图片。

图片文件名必须用英文单词命名，不能出现中文。

最终图片下载方式必须使用：

cd C:\Users\Administrator\CodeBuddy\20260706172534\firecrawl-toolkit  
python fc.py images "搜索内容" -n 1 -d results\anki_images