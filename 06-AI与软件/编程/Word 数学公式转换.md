---
tags:
  - 编程
---
# ✅使用 Word VBA 宏自动转换（最稳定）

这是目前**最可靠**的方案，适合你这种工程文档大量公式转换场景。

---

## 🔧 功能说明

宏会自动：

1. 扫描全文
    
2. 查找：
    
    - `$$...$$`
        
    - `$...$`
        
3. 删除 `$` 或 `$$`
    
4. 转换为 Word 数学公式对象
    
5. 自动设置为 **Professional 格式**
    

---

# 📌 操作步骤

### 第一步：打开 VBA 编辑器

Word 中按：

```
Alt + F11
```

---

### 第二步：插入模块

菜单：

```
插入 → 模块
```

---

### 第三步：粘贴下面完整代码

```vba
Sub ConvertLatexToWordMath()

    Dim rng As Range
    Dim doc As Document
    Set doc = ActiveDocument

    ' ===== 处理 $$...$$ 块级公式 =====
    With doc.Content.Find
        .ClearFormatting
        .Text = "\$\$([!\$]@)\$\$"
        .MatchWildcards = True
        
        Do While .Execute
            Set rng = doc.Range(Start:=.Parent.Start, End:=.Parent.End)
            
            Dim formulaText As String
            formulaText = rng.Text
            
            ' 去掉 $$ 包围符
            formulaText = Mid(formulaText, 3, Len(formulaText) - 4)
            
            rng.Text = formulaText
            
            ' 转为公式
            doc.OMaths.Add rng
            rng.OMaths(1).BuildUp
            
        Loop
    End With

    ' ===== 处理 $...$ 行内公式 =====
    With doc.Content.Find
        .ClearFormatting
        .Text = "\$([!\$]@)\$"
        .MatchWildcards = True
        
        Do While .Execute
            Set rng = doc.Range(Start:=.Parent.Start, End:=.Parent.End)
            
            Dim formulaText2 As String
            formulaText2 = rng.Text
            
            ' 去掉 $ 包围符
            formulaText2 = Mid(formulaText2, 2, Len(formulaText2) - 2)
            
            rng.Text = formulaText2
            
            ' 转为公式
            doc.OMaths.Add rng
            rng.OMaths(1).BuildUp
            
        Loop
    End With

    MsgBox "全部公式转换完成！"

End Sub
```

---

# 🚀 使用方法

关闭 VBA 编辑器 → 回到 Word

按：

```
Alt + F8
```

运行：

```
ConvertLatexToWordMath
```

即可自动转换全文。

---

# 🎯 转换效果

|原始文本|转换后|
|---|---|
|`$a+b=c$`|行内数学公式|
|`$$E=mc^2$$`|独立居中数学公式|
|`$P_{min}=P_0-0.25$`|专业格式下标公式|

---

# 📌 优点

✔ 自动批量处理  
✔ 不需要手动一个个转换  
✔ 自动 Professional 格式  
✔ 支持复杂 LaTeX 语法  
✔ 适用于技术投标文件、水锤计算书、结构计算书等

---

