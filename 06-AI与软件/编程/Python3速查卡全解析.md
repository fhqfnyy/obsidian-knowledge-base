---
tags:
  - 工作
---

---
## 🤖 Assistant

这张图片是一张 **Python 3 速查卡（Cheat Sheet）**，内容全面、结构清晰，适合初学者快速查阅和复习 Python 基础语法和常用操作。以下是这张速查卡的主要内容结构和关键知识点总结：

---

## 🧩 一、基础数据类型（Basic Data Types）

- **数值类型**：
  - `int`：整数，支持二进制（`0b1010`）、八进制（`0o642`）、十六进制（`0xF3`）
  - `float`：浮点数，如 `9.23`, `-1.7e-6`
  - `bool`：布尔值 `True`, `False`
  - `str`：字符串，支持单引号、双引号、三引号（多行）、转义字符（`\n`, `\t`, `\\`）
  - `bytes`：字节串，如 `b"toto\xfe\775"`

> ⚠️ 注意：`str` 和 `bytes` 不可互转，需显式编码/解码。

---

## 📦 二、容器类型（Container Types）

### 1. 有序序列（可重复、可索引）
- `list`：可变，如 `[1,5,9]`
- `tuple`：不可变，如 `(1,5,9)`
- `str`, `bytes`：也是有序序列

### 2. 关键字容器（键唯一、快速访问）
- `dict`：字典，如 `{'key': 'value'}` 或 `dict(a=3, b=4)`
- `set`：集合，无序不重复，如 `{1,9,3,0}`
- `frozenset`：不可变集合

> 💡 `dict` 的键必须是 **可哈希（hashable）** 类型（如数字、字符串、元组），不可是列表或字典。

---

## 🔤 三、标识符（Identifiers）

- 命名规则：`a-zA-Z_` 开头，后接 `a-zA-Z0-9_`
- 避免使用拼音、汉字、关键字（如 `if`, `for`）
- 大小写敏感：`a` 和 `A` 是不同变量
- 推荐命名：`a_toto`, `x7`, `y_max`, `BigOne`, `by_and_for`

---

## 🔄 四、变量赋值（Assignment）

- `=`：绑定值到名字
  - `x = 1.2 + 8 + sin(y)`
  - `a = b = c = 0` → 同时赋值
  - `y, z, r = 9.2, -7.6, 0` → 多变量赋值
  - `a, b = b, a` → 交换值
  - `a, *b = seq` → 拆包（`b` 为剩余部分列表）
  - `x += 3` → 自增，等价于 `x = x + 3`
  - `x = None` → 未定义常量
  - `del x` → 删除变量

---

## 🔄 五、类型转换（Type Conversion）

| 函数 | 说明 |
|------|------|
| `int("15")` → `15` | 字符串转整数 |
| `int("3f", 16)` → `63` | 指定进制转换 |
| `float("-11.24e8")` → `-1124000000.0` | 字符串转浮点 |
| `round(15.56, 1)` → `15.6` | 四舍五入到小数点后1位 |
| `bool(x)` → `False` | 空值、0、空序列 → False |
| `str(x)` → `"..."` | 转字符串（非格式化） |
| `chr(64)` → `'@'` | ASCII 码转字符 |
| `ord('@')` → `64` | 字符转 ASCII 码 |
| `repr(x)` → `"..."` | 获取对象的“直接表达式”（如 `repr([1,2])` → `'[1, 2]'`） |
| `list("abc")` → `['a','b','c']` | 转列表 |
| `dict([(3,"three"), (1,"one")])` → `{1:'one', 3:'three'}` | 转字典 |
| `set(["one","two"])` → `{'one','two'}` | 转集合 |

> ✅ 字符串分割：
> - `"words with spaces".split()` → `['words','with','spaces']`
> - `"1,4,8,2".split(",")` → `['1','4','8','2']`

> ✅ 列表推导式转换：
> - `[int(x) for x in ('1','29','-3')]` → `[1,29,-3]`

---

## 📏 六、序列容器的索引（Indexing & Slicing）

- **索引方式**：
  - 正索引：`0,1,2,3...`
  - 负索引：`-1,-2,-3...`（从末尾开始）
  - 切片：`lst[start:stop:step]`

- **示例**：
  ```python
  lst = [10,20,30,40,50]
  lst[0] → 10
  lst[-1] → 50
  lst[1:3] → [20,30]
  lst[::-1] → [50,40,30,20,10]  # 反转
  lst[::2] → [10,30,50]         # 步长2
  ```

- **操作**：
  - `len(lst)` → 5
  - `del lst[3]` → 删除第4个元素
  - `lst[4] = 25` → 修改元素

> ⚠️ 缺失切片索引默认从开始到结束（如 `lst[:3]` 等价于 `lst[0:3]`）

---

## 🧮 七、布尔逻辑（Boolean Logic）

- **比较运算符**：`<, >, <=, >=, ==, !=`
- **逻辑运算符**：
  - `a and b`：全真才真
  - `a or b`：一真即真
  - `not a`：取反
- **陷阱**：
  - `and/or` 返回的是操作数本身，不一定是 `True/False`
  - 确保 `a` 和 `b` 是布尔类型再做逻辑运算

> ✅ `True` 和 `False` 是布尔常量

---

## 🧮 八、数学运算（Math）

- **运算符优先级**：
  ```
  ** > * / // % > + - > = += -= ...
  ```
- **常用函数**（需导入 `math`）：
  ```python
  from math import sin, pi, cos, sqrt, log, ceil, floor
  sin(pi/4) → 0.707...
  cos(2*pi/3) → -0.4999...
  sqrt(81) → 9.0
  log(e**2) → 2.0
  ceil(12.5) → 13
  floor(12.5) → 12
  ```

> ✅ 其他模块：`math`, `statistics`, `random`, `decimal`, `fractions`, `numpy`

---

## 📜 九、程序块与缩进（Blocks & Indentation）

- Python 用缩进表示代码块（推荐4空格）
- 示例结构：
  ```
  程序块1:
      语句1
      语句2
  程序块2:
      语句3
  ```

---

## 📥 十、导入模块（Import）

- `from module import name1, name2 as alias`
- `import module as alias`
- `import module` → 使用 `module.name` 访问
- 模块搜索路径：`sys.path`

> ✅ 示例：
> ```python
> from math import sin, pi
> import numpy as np
> ```

---

## 🧭 十一、条件判断语句（Conditional Statements）

```python
if logical_condition:
    statements_block
elif ...:
    ...
else:
    ...
```

- 可跟多个 `elif`
- 只有第一个为真的块会执行
- `if bool(x) == True:` 等价于 `if x:`
- `if bool(x) == False:` 等价于 `if not x:`

> ✅ 示例：
> ```python
> if age <= 18:
>     state = "Kid"
> elif age > 65:
>     state = "Retired"
> else:
>     state = "Active"
> ```

---

## ⚠️ 十二、错误与异常（Exceptions）

- **触发错误**：`raise Exception(...)`
- **处理错误**：
  ```python
  try:
      # 正常处理的程序块
  except Exception as e:
      # 处理错误的程序块
  finally:
      # 无论是否出错都会执行
  ```

> ✅ 流程图示意：
> ```
> 正常 → raise → 处理 → finally
>        ↘ 错误 → 处理 → finally
> ```

---

## 📌 总结

这张速查卡覆盖了 Python 3 的核心语法和常用操作，包括：

✅ 数据类型  
✅ 容器操作  
✅ 类型转换  
✅ 索引切片  
✅ 逻辑运算  
✅ 数学函数  
✅ 模块导入  
✅ 条件语句  
✅ 异常处理  

非常适合：

- 初学者快速查阅
- 面试前复习
- 编程时随手参考

---

📌 **建议**：打印出来贴在桌边，或保存为 PDF 随时查阅！

---

✅ **备注**：图片右下角有“公众号：思察讲攻防”字样，说明该速查卡可能来自某个技术公众号或教学资源。

---

如需我为你生成 **可打印的 PDF 版本** 或 **Markdown 格式整理版**，也可以告诉我！


---
## 🤖 Assistant

这张图片是 **Python 3 速查卡的第二页**，承接第一页的基础语法，重点讲解了 **循环结构、函数定义、容器操作、文件读写、字符串处理、格式化输出** 等进阶内容，结构清晰、图文并茂，非常适合作为开发时的快速参考手册。

---

## 🔄 一、循环语句（Loop Statements）

### 1. `while` 循环（条件为真时执行）

```python
s = 0
i = 1
while i <= 100:
    s = s + i**2
    i = i + 1
print("sum:", s)
```

> ✅ 注意：
> - 循环体前需初始化变量
> - 循环条件至少有一个为真（如 `i` 从1开始）
> - 必须在循环体内改变条件变量，否则死循环！

### 2. `for` 循环（枚举序列中的每个条目）

```python
s = "Some text"
cnt = 0
for c in s:
    if c == "e":
        cnt = cnt + 1
print("found", cnt, "e's")
```

> ✅ 用途：
> - 遍历字符串、列表、元组、字典键、集合等
> - 可配合 `enumerate()` 同时获取索引和值：
>   ```python
>   for idx, val in enumerate(lst):
>       print(idx, val)
>   ```

### 3. 循环控制语句

| 关键字 | 作用 |
|--------|------|
| `break` | 立即退出当前循环 |
| `continue` | 跳过本次迭代，进入下一次 |
| `else` | 循环正常结束（未被 `break` 中断）时执行 |

> 💡 示例：
> ```python
> for i in range(5):
>     if i == 3:
>         break
> else:
>     print("循环正常结束")  # 不会执行
> ```

---

## 🖨️ 二、输入与输出（Input & Output）

### 1. `print()` 输出

```python
print("v=", 3, "cm :", x, ",", y+4)
```

> ✅ 可选参数：
> - `sep=" "`：项目分隔符，默认空格
> - `end="\n"`：打印结束符，默认换行
> - `file=sys.stdout`：输出到标准输出（可重定向到文件）

### 2. `input()` 输入

```python
s = input("Instructions: ")
```

> ⚠️ 注意：
> - `input()` 返回**字符串**，需手动转换类型（如 `int(s)`, `float(s)`）
> - 参见“类型转换”部分

---

## 📦 三、容器的常见操作（Container Operations）

### 通用函数：

| 函数 | 说明 |
|------|------|
| `len(c)` | 项目计数 |
| `min(c)`, `max(c)` | 最小/最大值 |
| `sum(c)` | 求和 |
| `sorted(c)` → `list` | 返回排序后的列表（不修改原容器） |
| `enumerate(c)` → 迭代器 | 返回 `(索引, 值)` 元组 |
| `zip(c1, c2, ...)` | 返回对应位置元素组成的元组 |
| `any(c)` | 任一元素为真 → `True` |
| `all(c)` | 所有元素为真 → `True` |
| `reversed(c)` | 反向迭代器 |
| `c.index(val)` | 返回值的位置 |
| `c.count(val)` | 统计出现次数 |

> ✅ 注意：
> - `in` / `not in`：判断元素是否存在（对字典是判断键）
> - `copy.copy(c)`：浅拷贝
> - `copy.deepcopy(c)`：深拷贝

---

## 📋 四、列表操作（List Operations）

| 方法 | 说明 |
|------|------|
| `lst.append(val)` | 末尾添加 |
| `lst.extend(seq)` | 末尾添加序列 |
| `lst.insert(idx, val)` | 在索引处插入 |
| `lst.remove(val)` | 删除第一个匹配项 |
| `lst.pop([idx])` → `value` | 删除并返回指定位置元素（默认末尾） |
| `lst.sort()` | 原地排序 |
| `lst.reverse()` | 原地反转 |

> ✅ 示例：
> ```python
> lst = [3, 1, 4, 2]
> lst.sort()      # → [1, 2, 3, 4]
> lst.reverse()   # → [4, 3, 2, 1]
> ```

---

## 📂 五、字典操作（Dict Operations）

| 操作 | 说明 |
|------|------|
| `d[key] = val` | 赋值 |
| `d[key]` → `val` | 获取值（不存在报错） |
| `d.get(key, default)` | 安全获取，不存在返回默认值 |
| `d.setdefault(key, default)` | 不存在则设默认值并返回 |
| `d.update(d2)` | 合并字典 |
| `d.keys()` → `dict_keys` | 获取键 |
| `d.values()` → `dict_values` | 获取值 |
| `d.items()` → `dict_items` | 获取键值对 |
| `d.pop(key, default)` | 删除并返回值 |
| `d.clear()` | 清空字典 |

> ✅ 示例：
> ```python
> d = {'a': 1, 'b': 2}
> d.get('c', 0)  # → 0
> d.setdefault('c', 3)  # → 3，d 变为 {'a':1, 'b':2, 'c':3}
> ```

---

## 🧩 六、集合操作（Set Operations）

| 方法 | 说明 |
|------|------|
| `s.add(key)` | 添加元素 |
| `s.remove(key)` | 删除元素（不存在报错） |
| `s.discard(key)` | 删除元素（不存在不报错） |
| `s.pop()` | 随机删除一个元素 |
| `s.clear()` | 清空集合 |
| `s.update(s2)` | 合并集合 |
| `s.copy()` | 浅拷贝 |

> ✅ 运算符：
> - `|` → 并集
> - `&` → 交集
> - `-` → 差集
> - `^` → 对称差集

---

## 📄 七、文件操作（File I/O）

### 1. 打开文件

```python
f = open("file.txt", "w", encoding="utf8")
```

> ✅ 模式：
> - `'r'`：读（默认）
> - `'w'`：写（覆盖）
> - `'a'`：追加
> - `'b'`：二进制模式（如 `'rb'`, `'wb'`）
> - `'t'`：文本模式（默认）
> - `'x'`：独占创建（文件存在则报错）

> ✅ 编码：
> - `utf8`, `ascii`, `latin1` 等

### 2. 写入文件

```python
f.write("coucou")
f.writelines(list_of_lines)
```

> ⚠️ 注意：
> - 写入内容必须是字符串或字节（`bytes`）
> - 二进制模式需用 `bytes` 类型

### 3. 读取文件

```python
f.read([n])        # 读取 n 个字符（默认全部）
f.readline()       # 读取一行
f.readlines()      # 读取所有行，返回列表
```

### 4. 文件指针控制

```python
f.tell()           # 当前位置
f.seek(position[, origin])  # 移动指针
f.flush()          # 刷新缓冲区
f.truncate([size]) # 截断文件
f.close()          # 关闭文件（务必调用！）
```

> ✅ 推荐使用 `with` 语句自动管理文件：

```python
with open("file.txt") as f:
    for line in f:
        print(line.strip())
```

---

## 📐 八、整数序列（`range`）

```python
range(start, end[, step])
```

> ✅ 示例：
> - `range(5)` → `0,1,2,3,4`
> - `range(2,12,3)` → `2,5,8,11`
> - `range(3,8)` → `3,4,5,6,7`
> - `range(20,5,-5)` → `20,15,10`
> - `range(len(seq))` → 遍历索引

> ⚠️ `range` 返回的是不可变序列对象，不是列表。

---

## 🧑‍💻 九、函数定义与调用（Function）

### 1. 定义函数

```python
def fct(x, y, z):
    """函数文档"""
    # 计算逻辑
    return res  # 若无 return，返回 None
```

> ✅ 参数说明：
> - `x, y, z`：位置参数
> - `a=3, b=5`：默认参数
> - `*args`：可变位置参数 → 元组
> - `**kwargs`：可变关键字参数 → 字典

> ✅ 示例：
> ```python
> def fct(x, y, z, *args, a=3, b=5, **kwargs):
>     print(args)   # → tuple
>     print(kwargs) # → dict
> ```

### 2. 调用函数

```python
r = fct(3, i+2, 2*i)
```

> ✅ 传参方式：
> - 位置参数：按顺序
> - 关键字参数：`fct(x=1, y=2)`
> - 拆包：`fct(*seq)`, `fct(**dict)`

---

## 🔤 十、字符串操作（String Methods）

| 方法 | 说明 |
|------|------|
| `s.startswith(prefix)` | 是否以指定前缀开头 |
| `s.endswith(suffix)` | 是否以指定后缀结尾 |
| `s.count(sub)` | 子串出现次数 |
| `s.find(sub)` / `s.index(sub)` | 查找位置（`index` 不存在报错） |
| `s.upper()` / `s.lower()` | 大小写转换 |
| `s.strip([chars])` | 去除首尾空白或指定字符 |
| `s.replace(old, new)` | 替换子串 |
| `s.split(sep)` | 按分隔符拆分 → 列表 |
| `s.join(seq)` | 用字符串连接序列 |
| `s.ljust(width[, fill])` / `s.rjust()` / `s.center()` | 左/右/居中对齐 |
| `s.encode(encoding)` | 编码为字节 |
| `s.isdigit()`, `s.isalpha()`, `s.isspace()` | 类型判断 |

> ✅ 示例：
> ```python
> s = "  Hello World  "
> s.strip() → "Hello World"
> s.split() → ['Hello', 'World']
> "-".join(['a','b','c']) → "a-b-c"
> ```

---

## 🎯 十一、格式化输出（String Formatting）

### 1. `.format()` 方法

```python
"modele{} {} {}".format(x, y, z)
```

> ✅ 位置占位符：
> - `{}`：按顺序填充
> - `{0}`, `{1}`：指定位置
> - `{key}`：关键字参数

> ✅ 格式化控制：
> - `"{:.2f}".format(45.72793)` → `'45.73'`
> - `"{:10s}".format("toto")` → `'      toto'`
> - `"{:x}".format(255)` → `'ff'`（十六进制）
> - `"{:.2%}".format(0.123)` → `'12.30%'`

### 2. 格式化语法（`{fill}{align}{width}{.precision}{type}`）

| 部分 | 说明 |
|------|------|
| `fill` | 填充字符（默认空格） |
| `align` | `<`左对齐, `>`右对齐, `^`居中 |
| `width` | 最小宽度 |
| `.precision` | 小数位数（浮点）或最大字符数（字符串） |
| `type` | `d`整数, `f`浮点, `s`字符串, `x`十六进制, `%`百分比 |

> ✅ 示例：
> ```python
> "{:>8.2f}".format(3.1415) → '    3.14'
> "{:0>5d}".format(42) → '00042'
> ```

---

## 🧭 总结

这张速查卡第二页内容丰富，覆盖了 Python 编程中**最常用、最核心的进阶操作**，包括：

✅ 循环控制（`while`, `for`, `break`, `continue`）  
✅ 输入输出（`input`, `print`）  
✅ 容器操作（列表、字典、集合）  
✅ 文件读写（`open`, `read`, `write`, `with`）  
✅ 函数定义与调用（参数、返回值、`*args`, `**kwargs`）  
✅ 字符串处理（常用方法、格式化）  
✅ `range` 序列生成  
✅ 格式化输出（`.format()`）

---

📌 **建议**：
- 打印出来贴在工位，或保存为 PDF 随时查阅
- 配合第一页使用，可覆盖 90% 的日常 Python 编程需求

---

✅ **备注**：图片右下角标注“公众号：恩奈讲攻防”，说明该速查卡可能来自安全/攻防方向的技术公众号，内容偏向实战和快速查阅。

---

如需我为你生成 **可打印 PDF 版本**、**Markdown 整理版** 或 **Anki 卡片格式**，欢迎随时告诉我！
