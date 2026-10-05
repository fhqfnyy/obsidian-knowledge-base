# Wincc常用C脚本

## 1 set/get相关；子窗口
```
//多设备调用
SetTagPrefix(lpszPictureName, "huamian1","电机1.");//设置变量前缀
SetPictureName(lpszPictureName,"huamian1","BENG");//给画面窗口绑定画面名称
SetVisible(lpszPictureName,"huamian1",1);   //将画面显示出来
SetTagChar("电机1.名称","电机1控制窗口"); //给变量赋值
///////////////////////////////////////////////////////////////

SetPropChar(lpszPictureName,"画面窗口名字","CaptionText","显示的标题名字");

SetPropChar(lpszPictureName,"画面窗口名字","PictureName","要弹出画面的名字");

SetPropBOOL(lpszPictureName,"画面窗口名字","Visible",1); //显示为1

```
## 2 用户登录/注册
```
// 用户注销：
#pragma code ("useadmin.dll")
#include "PWRT_api.h"
#pragma code()
PWRTLogout();

// 用户登录：
#pragma code ("useadmin.dll")
#include "PWRT_api.h"
#pragma code()
PWRTLogin('c');

```
## 3 操作项目
```
//关闭项目并退出wincc：
ExitWinCC ();

//退出运行到wincc资源管理器
DeactivateRTProject ();

//结束监控并关闭计算机（会弹出确认对话框）
HWND hWnd = NULL;//Author:Smartsys
int iRet;
hWnd = FindWindow(NULL,"WinCC-运行系统 - ");
iRet=MessageBox(hWnd,"结束监控并关闭计算机",
"关闭操作站",MB_YESNO|MB_ICONWARNING|MB_APPLMODAL);
if(iRet==IDYES)
   DMExitWinCCEx (DM_SDMODE_POWEROFF);

// 将WinCC运行画面最小化
HWND WinCCHwnd;
WinCCHwnd=FindWindow(NULL,"WinCC-Runtime - "); //如果运行语言为英文
if (WinCCHwnd==0) WinCCHwnd=FindWindow(NULL,"WinCC-运行系统 - "); //如果运行语言为中文
CloseWindow(WinCCHwnd);

```
## 4 弹窗
```

// 点击事件弹出对话框，通过点击弹出对话框的确定和取消按钮实现对变量的置位或取消:
int r;
r=MessageBox(NULL, "YES为开，NO为关", "确定开命令：", MB_YESNO |MB_SYSTEMMODAL);
if (r==IDYES)
SetTagBit("TAGNAME",TRUE);
else
SetTagBit("TAGNAME",FALSE);

// 开显示关显示:（要在内部变量中建立三个二进制变量COLSE_DISPLAY、DISPLAY_NO和DISPLAY_COMMENT）
// Funktion: Bit in Doppelwort setzen;

if (GetTagBitWait("COLSE_DISPLAY")==1 )
{SetTagBit("COLSE_DISPLAY",0);
SetTagBit("DISPLAY_NO",0); //Return-Type: BOOL
SetTagBit("DISPLAY_COMMENT",0);
}
else   
{SetTagBit("COLSE_DISPLAY",1);
SetTagBit("DISPLAY_NO",1); //Return-Type: BOOL
SetTagBit("DISPLAY_COMMENT",0);
}

// 开阀确认:
BOOL a;
a=GetTagBit("30T制水启动"); //Return-Type :short int
if (a==0)
{
if(MessageBox(NULL,"真的要启动制水吗？","操作提示",MB_YESNO|MB_ICONQUESTION|MB_SETFOREGROUND|MB_SYSTEMMODAL) == 6)
{ SetTagBit("30T制水启动",1);

//Return-Type :BOOL

}
}
else

{if(MessageBox(NULL,"真的要停止制水吗？","操作提示",MB_YESNO|MB_ICONQUESTION|MB_SETFOREGROUND|MB_SYSTEMMODAL) == 6)
{ SetTagBit("30T制水停止",1);

}}

```
## 5 操作元素
```
// 显示或不显示（其中hiddVtext为内部的二进制变量）
if(GetTagBit("hiddVtext")==0) //Return-Type: BOOL
{
SetTagBit("hiddVtext",TRUE); //Return-Type: BOOL
//Return-Type: BOOL
}
else
{SetTagBit("hiddVtext",FALSE);
}


// 打开编辑画面的脚本
#pragma option(mbcs)
#ifdef RUN_ON_WEBNAVIGATOR

#else
   HWND hExplorerWindow= NULL;
   HWND hRuntimeWindow= NULL;

   hExplorerWindow= FindWindow("WinCCExplorerFrameWndClass",NULL);
   hRuntimeWindow= FindWindow("PDLRTisAliveAndWaitsForYou",NULL);

   if(hExplorerWindow)
      {
      ShowWindow(hExplorerWindow, SW_MINIMIZE); // This entry is only necessary for Windows 2000
      ShowWindow(hExplorerWindow, SW_SHOWNORMAL);
      SetForegroundWindow(hExplorerWindow);
   }
   else
      printf("\r\n SetForeground failed");
#endif


```
## 6 WINCC中制作一个保持型按钮
```
方法1：
if( GetTagBit("M10") )
   SetTagBit("M10",0);
else
   SetTagBit("M10",1);
方法2：
SetTagBit("M1.0",(SHORT)!GetTagBit("M1.0"));
求反指令，如果为1点击变为0，
如果为0点击变为1
方法3：
或者做两个按钮，
按钮一：显示，直接连接m1.0，是--不显示，否--显示 。事件，直接连接,源，常数1，变量M1.0;
按钮二：事件，直接连接，源，常数0，变量M1.0；
用按钮一覆盖按钮二

```
## 7 声光报警
```
WINCC 中声音报警C脚本全局变量

#include "apdefap.h"

int gscAction( void )
{
     #pragma  code ("winmm.dll")
      BOOL PlaySound(LPCSTR pszSound, HMODULE hmod,DWORD fdwSound); 
     #pragma code()
 if (GetTagBit("test")==1)
  {
        PlaySound("D:\Program Files\AliWangWang\sound",NULL,8);
                                  }
     return 0;
  }

//注意：C 是大小写敏感的

//如何让WINCC发出报警声呢？
/*
1.WinCC V6.0可以用Horn组态实现声音报警，不用编程。
 打开Horn，在Message assignment选项页选定Message Class为Error，定义一个Bool型的Tag；在Signal assingment选项页选定Tag为刚才定义的Bool型的Tag，在Sound中选择要播放的.wav声音文件。
运行时，只要Error类别的报警触发，则Bool型的Tag置位，.wav声音循环播放；在报警控件中确认报警，.wav声音中止，也可以将Bool型的Tag复位，.wav声音中止。
Horn是基本过程控制(BPC)的组件，标准安装是不会安装的。
安装WinCC时选择“自定义安装”，已经安装了就选择“安装卸载组件”，组件栏中选择“选项”，右边栏子选项选择“Basic Process Control”安装即可。 
*/
/*
2.自定义两个内部32位变量stat1和stat1_save，然后在报警记录里面消息位使用外部变量，状态位为stat1中的位。然后在脚本中：
*/
定义两个DWord变量A1、B1连接stat1和stat1_save。
#pragma code("Winmm.dll")
void WINAPI PlaySoundA(char* pszSound ,char* hmode,DWORD dwFlag);
#pragma code()

A1=GetTagDWord("stat1");
B1=GetTagDWord("stat1_save");
if((A1^B1)&A1)

SetTagBit("ok_1",1);
if(GetTagBit("ok_1"))
PlaySoundA("C:\\winnt\\music\\06.wav",NULL,1);
SetTagDWord("stat1_save",A1

```
## 8 动画脚本电机风扇旋转（几何—起始角度）：
```
#include "apdefap.h"

 long _main(char* lpszPictureName, char* lpszObjectName, char* lpszPropertyName)

{

static  DWORD  f=20;

if  (GetTagBit("DI电机运行信号"))  {      

f= f+30;

if (f==360) (f=0);

}

return f;

}

电机风扇旋转（几何---结束角度）：

#include "apdefap.h"

 long _main(char* lpszPictureName, char* lpszObjectName, char* lpszPropertyName)

{

static  DWORD  i=80;

if  (GetTagBit("DI电机运行信号")) {        

i = i+30;

if (i==360) (i=0);

}

return i; 

}

```
## 9 物料水平右移动（几何—位置X）（停车后，物料回到原点）
```

#include "apdefap.h"

 long _main(char* lpszPictureName, char* lpszObjectName, char* lpszPropertyName)

{

static  int  a=350;

if  (GetTagBit("DI电机运行信号")  && (a<=730))  {  

   a+=20;

if (a>=720)  (a=350);

} 

if (!GetTagBit("DI电机运行信号"))  (a=350);   

return a;

}

// 物料水平右移动（几何---位置X）（停车后，物料停在该点）

#include "apdefap.h"

 long _main(char* lpszPictureName, char* lpszObjectName, char* lpszPropertyName)

{

static  int  a=350;

if  (GetTagBit("DI电机运行信号")  && (a<=730))  {  

   a+=20;

if  (a>=720)  (a=350);

}     

return a;

}

 

// 物料向上移动（几何---位置Y）（停车后，物料停在该点）

#include "apdefap.h"

 long _main(char* lpszPictureName, char* lpszObjectName, char* lpszPropertyName)

{

static  int  b=450;

if  (GetTagBit("DI电机运行信号")  && (b>=290))  {  

   b-=10;

if  (b<=280)  (b=450);

}     

return b;

}

```
## 10 时间静态文本
```

// 年月日（静态文本）

#include "apdefap.h"

 char* _main(char* lpszPictureName, char* lpszObjectName, char* lpszPropertyName)

{

time_t  timer ;

struct tm *ptm;

char *p;

time(&timer);

ptm =localtime(&timer);

p =SysMalloc(9);

sprintf(p,"%04d/%02d/%02d",ptm->tm_year-100+2000,ptm->tm_mon+1,ptm->tm_mday);

return(p);

}

 

// 时分秒（静态文本）

#include "apdefap.h"

 char* _main(char* lpszPictureName, char* lpszObjectName, char* lpszPropertyName)

{

time_t  timer ;

struct tm *ptm;

char *p;

time(&timer);

ptm =localtime(&timer);

p =SysMalloc(9);

sprintf(p,"%02d:%02d:%02d",ptm->tm_hour,ptm->tm_min,ptm->tm_sec);      

return(p);

}

```
## 11 登陆到指定的用户名无须手动输入（按钮操作）
```
#include "apdefap.h"

void OnLButtonDown(char* lpszPictureName, char* lpszObjectName, char* lpszPropertyName, UINT nFlags, int x, int y)

{

#pragma code("useadmin.dll")

#include "PWRT_API.H"

#pragma code()

PWRTSilentLogin("用户名", "口令");

}

```
## 12 显示登陆对话框无须热键（按钮操作）
```
#include "apdefap.h"

void OnLButtonDown(char* lpszPictureName, char* lpszObjectName, char* lpszPropertyName, UINT nFlags, int x, int y)

{

#pragma code ("useadmin.dll")

#include "PWRT_api.h"

#pragma code( )

  PWRTLogin('c');

}

```
## 13 退出WINCC运行（按钮操作）

```
#include "apdefap.h"

void OnLButtonDown(char* lpszPictureName, char* lpszObjectName, char* lpszPropertyName, UINT nFlags, int x, int y)

{

 { 

  DeactivateRTProject ();

}

}

```
## 14 单位递增/减按钮（到上限不提示出错）
```
// 单位递增按钮（到下限不提示出错）
#include "apdefap.h"

void OnClick(char* lpszPictureName, char* lpszObjectName, char* lpszPropertyName)

{

DWORD value;

value=GetTagDWord("AI标签");  

if (value>90) (value=上限);

else value=value+10;

SetTagDWord("AI标签",value);    

}

// 单位递减按钮（到下限不提示出错）

#include "apdefap.h"

void OnClick(char* lpszPictureName, char* lpszObjectName, char* lpszPropertyName)

{

DWORD value;

value=GetTagDWord("AI标签");  

if (value<10) (value=下限);

else value=value-10;

SetTagDWord("AI标签",value);    

}

```
## 15 单位递增/减按钮（到上下限提示出错）
```
// 单位递增按钮（到上限提示出错）**

#include "apdefap.h"

void OnClick(char* lpszPictureName, char* lpszObjectName, char* lpszPropertyName)

{

DWORD value;

HWND hWnd=NULL ;  

hWnd=FindWindow(NULL,"Wincc-运行系统- "); 

value=GetTagDWord("AI标签");  

if (value<=90) (value=value+10);

else MessageBox(hWnd,"已到达上限值！","错误",MB_OK|MB_ICONWARNING|MB_APPLMODAL);

SetTagDWord("AI标签",value);    

}

// 单位递减按钮（到下限提示出错）**

#include "apdefap.h"

void OnClick(char* lpszPictureName, char* lpszObjectName, char* lpszPropertyName)

{

DWORD value;

HWND hWnd=NULL ;  

hWnd=FindWindow(NULL,"Wincc-运行系统- "); 

value=GetTagDWord("AI标签");  

if (value>0) (value=value-10);

else MessageBox(hWnd," 已到达下限值！"," 错误",MB_OK|MB_ICONWARNING|MB_APPLMODAL);

SetTagDWord("AI标签",value);    

}

```
## 16 自动登陆到指定的用户（画面属性–其它—授权）
```
#include "apdefap.h"

 long _main(char* lpszPictureName, char* lpszObjectName, char* lpszPropertyName)

{

#pragma code("useadmin.dll")

#include "PWRT_API.H"

#pragma code()

PWRTSilentLogin("用户名", "口令");

return(0X0);

}

```
## 17 询问框
```
// （最好用于单机启动，组启也可，2个按钮，NO不操作）
#include "apdefap.h"

void OnLButtonDown(char* lpszPictureName, char* lpszObjectName, char* lpszPropertyName, UINT nFlags, int x, int y)

{

HWND hWnd=NULL;

int 上位启动信号;

hWnd=FindWindow(NULL,"WinCC C-Course");

if (GetTagBit("DI备妥"))

{

上位启动信号=MessageBox(hWnd,"电源已备妥，您现在确定要启动吗？","启动！",

                MB_YESNO|MB_ICONQUESTION|MB_APPLMODAL);

printf("\r\nExample 3\r\n");

    if (上位启动信号==IDNO)   (printf("User selected NO button \r\n")); 

    else (SetTagByte("上位启动信号",1));

}

else

MessageBox(hWnd,"电源未备妥，请查证后重试！","启动！",

           MB_OK|MB_ICONHAND|MB_APPLMODAL);

}

// 询问框（最好用于单机启动，组启也可，3个按钮，NO复位）**

#include "apdefap.h"

void OnLButtonDown(char* lpszPictureName, char* lpszObjectName, char* lpszPropertyName, UINT nFlags, int x, int y)

{

HWND hWnd=NULL;

int上位启动信号;

hWnd=FindWindow(NULL,"WinCC C-Course");

if (GetTagBit("DI备妥"))

{

上位启动信号=MessageBox(hWnd," 电源已备妥，您现在确定要启动吗？","启动！",

                        MB_YESNOCANCEL|MB_ICONQUESTION|MB_APPLMODAL);

    if (上位启动信号==IDCANCEL)   (printf("User selected NO button \r\n"));  

    else

         {

          if  (上位启动信号==IDYES)  (SetTagByte("上位启动信号",1));

          else  (SetTagByte("上位启动信号",0));

          }

}

else

MessageBox(hWnd," 电源未备妥，请查证后重试！","启动！",

           MB_OK|MB_ICONHAND|MB_APPLMODAL);

}

```
## 18 搅拌器动画
```
**搅拌器左侧扇页（几何—宽度）**

#include "apdefap.h"

 long _main(char* lpszPictureName, char* lpszObjectName, char* lpszPropertyName)

{

static  int  h=50;

static  int  u=0;

if (GetTagBit("DI电机运行信号"))

{

if ((h>0)&&(h<=50))

{

(h-=2);

return h;

}

if ((u>=0)&&(u<50))

{

(u+=2);

return u;

}

u=0;

h=50;

return h;

return u;

}

if (!GetTagBit("DI电机运行信号")){

h=50;

u=0;

return h;

return u;

}

}

**搅拌器左侧扇页（几何—位置X，右侧扇页没有位置变化）**

#include "apdefap.h"

 long _main(char* lpszPictureName, char* lpszObjectName, char* lpszPropertyName)

{

static  int  h=75;

static  int  u=125;

if (GetTagBit("DI电机运行信号"))

{

if ((h>=75)&&(h<125))

{

(h+=2);

return h;

}

 
if ((u<=125)&&(u>75))

{

(u-=2);

return u;

}

u=125;

h=75;

return h;

return u;

}

if (!GetTagBit("DI电机运行信号")){

h=75;

u=125;

return h;

return u;

}

}

```


## 20 打印按钮
```

#include "apdefap.h"

void OnLButtonDown(char* lpszPictureName, char* lpszObjectName, char* lpszPropertyName, UINT nFlags, int x, int y)

{

WinExec("PRTSCR.EXE -hardcopy",SW_SHOWNORMAL);

}

```
## 21 一个圈代表电机两种状态
```
#include "apdefap.h"

void OnClick(char* lpszPictureName, char* lpszObjectName, char* lpszPropertyName)

{

char* opentag="DI1电机运行信号";

       char* closetag="DI2电机运行信号";

       BOOL open,close;

       open = GetTagBit(opentag);

       close = GetTagBit(closetag);

       if (open)

       return CO_GREEN;

       if (close)

       return CO_BLUE;

}

```
