# wincc常用VB脚本
## 1.给变量赋值
```
HMIRuntime.Tags("C_fill").Write 6
-----------------------------------------------
Dim objTag
Set objTag = HMIRuntime.Tags("Tag")
objTag.Read   //读取变量
objTag.Value = objTag.Value + 1
objTag.Write
 
 
 
```
## 2.改变对象的属性
```
Dim objRectangle
Set objRectangle = ScreenItems("Rectangle1")//获取屏幕对象
objRectangle.BackColor = RGB(155,0,0)
----------------------------------------------------------------
Dim objTag,objTag1,objTag3
Dim objRectangle
Set objTag = HMIRuntime.Tags("ab")
Set objTag1 = HMIRuntime.Tags("ac")
Set objRectangle = ScreenItems("圆3")
objTag.Write 1
objTag1.Write 1
objTag3 = objTag.Value * objTag1.Value
If objTag3 = 1 Then objRectangle.BackColor = RGB(155,0,0)
```
## 3.通过二进制变量做逻辑运算改变圆的背景颜色
```
Function BackColor_Trigger(ByVal Item)
Dim a,b,c
Dim objRectangle
a = HMIRuntime.Tags("ac").Read
b = HMIRuntime.Tags("ad").Read
Set objRectangle = ScreenItems("圆3")
c = a And b
If c = 1 Then 
objRectangle.BackColor = RGB(155,0,0)
Elseif c = 0 Then
objRectangle.BackColor = RGB(255,255,0)
End If
End Function
--------------------------------------------------------------------------------
Sub OnLButtonUp(Byval Item, Byval Flags, Byval x, Byval y)     
Dim objTag,objTag1,obj
Set objTag = HMIRuntime.Tags("QL")
Set objTag1 = HMIRuntime.Tags("wjx")
objTag.Read
objTag1.Read
objTag.Value = objTag.Value + 1
objTag1.Value = 1
objTag.Write
objTag1.Write
Set obj = HMIRuntime.Screens("一期主洗.画面窗口1:变频器控制").ScreenItems("Test3")
obj.Text = "204"
End Sub
```
## 4.获取文本控件并赋值
```Sub OnClick(ByVal Item)          
Dim obj
Set obj= ScreenItems("test5")
obj.Text = "ww"
End Sub

```
## 5.动态传参数
```
Sub OnClick(ByVal Item)              
Dim obj,m1,m2
 
m2 = "med"
'获取当前屏幕传过来的参数，动态拼接参数，读取变量做逻辑运算
m1 = HMIRuntime.Tags(m2).Read
Set obj= ScreenItems("test5")
obj.Text = m1
End Sub
```
## 6.显示层按钮

```
  Sub X6309X94AE2X0000X0000_X6309X94AE2X0000X60C5_X6309X94AE2X0000X653E_OnLButtonDown(ByVal Item, ByVal Flags, ByVal x, ByVal y)        

  

Dim objScreen

  

Dim CountLayer

  

Set objScreen = HMIRuntime.Screens("layer")

  

For CountLayer=1 To 32 Step 1

  

objScreen.Layers(CountLayer).Visible = vbFalse

  

Next

  

objScreen.Layers(n).Visible = vbTrue

  

End Sub
 

n=层数+1

初始画面程序（画面空地--属性--事件--其它--打开画面--动作）
```

