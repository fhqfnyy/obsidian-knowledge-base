---
tags:
  - AI
---

# 一、系统升级版总提示词（增加AI模型选择🔥）

```id="a9x2kf"
在现有英语学习Web应用基础上，增加“AI模型选择与多供应商支持功能”。

【目标】
实现一个统一AI调用层，支持多个模型供应商动态切换：

支持供应商：
- Ollama（本地模型）
- NVIDIA AI（如NIM / NeMo）
- MiniMax
- 火山引擎（Volcengine）
- OpenAI（兼容接口）

【核心要求】

1. 模型选择功能
- 用户可在设置页面选择：
  - 当前AI供应商
  - 模型名称（如 llama3 / gpt-4 / abab6 等）
- 支持默认模型配置
- 支持不同功能选择不同模型（如：
  - OCR解析 → 本地模型
  - 造句批改 → 云模型）

2. 统一AI调用接口（关键）
设计统一调用格式：

输入：
{
  provider: "ollama | openai | minimax | nvidia | volcengine",
  model: "xxx",
  prompt: "xxx",
  temperature: 0.7
}

输出：
{
  text: "",
  usage: {},
  latency: 0
}

3. Provider适配层（核心设计）
为每个供应商实现适配器：

- OllamaAdapter（本地HTTP API）
- OpenAIAdapter
- MinimaxAdapter
- NvidiaAdapter
- VolcengineAdapter

要求：
- 统一接口：generateText()
- 支持流式输出（stream）
- 自动错误处理和重试

4. 模型路由机制（高级）
支持：
- 按功能路由模型：
  - 单词解析 → 快速模型
  - AI批改 → 高精度模型
- fallback机制：
  - 主模型失败 → 自动切换备用模型

5. 成本与性能控制
- 记录token使用量
- 记录每次调用耗时
- 支持限流（Rate Limit）
- 支持缓存（相同单词不重复请求）

6. UI功能
新增“AI设置页面”：
- 选择供应商（下拉框）
- 输入API Key
- 选择模型
- 测试连接按钮
- 显示响应时间

【输出内容】
请生成：
1. AI统一调用架构设计图
2. Adapter代码实现（至少2个示例）
3. 模型路由逻辑代码
4. 前端模型选择UI
5. 数据库存储设计（模型配置）
```

---

# 二、AI统一调用层（核心提示词🔥）

👉 这个是你整个系统最重要的一层

```id="u3m8zp"
实现一个AI统一调用服务（AI Gateway）：

要求：
- 使用Node.js
- 提供统一函数：

async function generateText({
  provider,
  model,
  prompt,
  temperature
})

功能：
- 根据provider自动选择适配器
- 支持以下provider：
  - ollama
  - openai
  - minimax
  - nvidia
  - volcengine

返回：
{
  text: "",
  latency: number,
  provider: "",
  model: ""
}

附加要求：
- 支持超时控制
- 支持fallback机制
- 支持日志记录
```

---

# 三、Ollama本地模型提示词（适合你🔥）

👉 强烈建议你用（本地 + 低成本）

```id="8d2kmp"
实现Ollama适配器：

接口：
POST http://localhost:11434/api/generate

请求：
{
  model: "llama3",
  prompt: "xxx",
  stream: false
}

要求：
- 使用fetch或axios
- 支持stream模式
- 返回统一格式

适用场景：
- 单词解释
- 简单翻译
- 离线学习
```

---

# 四、Minimax / 火山引擎提示词

```id="p7v4yt"
实现Minimax和火山引擎适配器：

要求：
- 封装API调用
- 支持鉴权（API Key）
- 处理响应格式差异
- 转换为统一输出格式

注意：
- Minimax偏中文能力强
- 火山引擎适合国内访问
```

---

# 五、模型路由策略（非常关键🔥）

```id="r9c2lh"
实现AI模型路由策略：

规则：
- 单词解析 → 使用低成本模型（Ollama / Minimax）
- AI造句批改 → 使用高精度模型（OpenAI / NVIDIA）
- OCR纠错 → 使用快速模型

实现：
function selectModel(taskType) {
  switch(taskType) {
    case "word_explain":
      return { provider: "ollama", model: "llama3" }
    case "sentence_check":
      return { provider: "openai", model: "gpt-4" }
  }
}
```

---

# 六、数据库设计（新增AI配置表）

```id="d4l9as"
新增表：ai_model_config

字段：
- id
- user_id
- provider
- model
- api_key
- base_url
- is_default
- created_at

新增表：ai_usage_log
- id
- user_id
- provider
- model
- tokens
- latency
- created_at
```

---

# 七、前端AI设置页面提示词

```id="k8v1qe"
实现AI设置页面：

功能：
- 选择AI供应商（下拉框）
- 输入API Key
- 输入Base URL（可选）
- 选择模型
- 测试连接按钮
- 显示响应时间

技术：
React + Tailwind

要求：
- 简洁UI（类似Notion设置页）
- 状态管理使用Zustand
```

---

# 八、升级建议（结合你工程师背景🔥）

你这个系统可以做成：

👉 **企业级英语学习 + AI平台**

扩展方向：

### 1️⃣ 本地+云混合架构

- Ollama（本地） → 日常学习
    
- 云模型 → 高级分析
    

### 2️⃣ 专业英语模型

- 电气工程词汇优化
    
- PLC/自动化术语解释
    

### 3️⃣ 成本控制系统

- 每天token限额
    
- 自动降级模型
    

---

# 总结（关键价值🔥）

加上这套后，你的系统会变成：

👉 **不仅是学习软件，而是一个AI调度平台**

具备能力：

- 多模型切换
    
- 成本控制
    
- 离线运行（Ollama）
    
- 高精度AI（云模型）
    

---

如果你下一步想继续做，我可以帮你👇

✅ 直接生成 **完整AI网关代码（可运行）**  
✅ 或帮你做 **Ollama + 前端联调 demo**  
✅ 或做一版 **系统架构图（工程级）**