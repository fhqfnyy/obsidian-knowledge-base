# SCPW20 TTS 插件导致 WinCC 运行时挂起 —— 故障分析报告

- 报告日期：2026-10-08
- 涉及主机：SERVER5
- 涉及项目：`D:\PROJECT\OZ3WINCC20250918`（活动），另有副本 `E:\PROJECT\OZ3WINCC20250918`
- 运行时进程：`C:\Program Files (x86)\Siemens\WinCC\bin\PdlRt.exe`（版本 800.9.107.1）
- 结论：**根因已定位，已用占位程序规避，当前运行正常**

---

## 1. 问题现象

- WinCC 图形运行系统（`PdlRt.exe`）出现"未响应"（挂起），界面卡死。
- 事件查看器 Application 日志中出现 `Application Hang`（事件 1002）与 Windows Error Reporting（事件 1001）记录。
- 该挂起在**2026-10-08 当天集中出现**，此前无记录。

## 2. 触发逻辑（已确认）

运行时挂起由一个**画面打开事件**触发，而非定时/变量触发：

1. 运行时的**基本画面（BasePicture）**配置为 `ScreenModule\ScreenMain.Pdl`。
   配置文件（三处一致）：
   - `SERVER5\GraCS\GraCS.ini`
   - `OS05\GraCS\GraCS.ini`
   - `OS06\GraCS\GraCS.ini`
     ```
     BasePicture=ScreenModule\ScreenMain.Pdl
     ```
2. `ScreenMain.Pdl`（基本/框架画面）内嵌底部条模块 `ScreenButtom`。
3. `ScreenButtom.Pdl` 的画面事件 `OnOpenPicture` 中调用全局函数 `SCPW20_ExeTTS()`。
4. `SCPW20_ExeTTS()` 定义于 `Library\SCPW20_ExeTTS.fct`（源码标识 `uetzel.c`），在 `Library\AP_PBIB.H` 中声明，相关常量在 `Library\SCPW20.h`。
   从编译产物提取到的动作逻辑：
   ```
   printf("Project Path:%s", ...)
   printf("propath length:%d", ...)
   拼装路径: <ProPath>\TTS\SCPW20 TTS.exe
   printf("AAAATTS path:%s", ...)
   ProgramExecute(<path>)                     // 启动 TTS 程序
   FindWindowA(NULL, "SCPW2.0 TTS")           // 查找其窗口
   ShowWindow(...)                            // 显示窗口
   ```

**要点：每次 Runtime 启动 / 重启打开基本画面时，都会执行一次该调用。**

## 3. 根因

`SCPW20_ExeTTS()` 依赖的程序 `<项目>\TTS\SCPW20 TTS.exe` **已缺失**。

- 调用 `ProgramExecute` 启动该 exe 后，脚本又通过 `FindWindowA("SCPW2.0 TTS")` 查找并操作其窗口；
- 目标程序不存在，导致该跨进程交互无法正常完成，`PdlRt.exe` 阻塞。
- WER 报告证据：`ConsentKey=AppHangXProcB1`（**跨进程挂起**），UI 文本为"WinCC Graphics runtime 未响应"。

## 4. 时间线与证据

当日 `PdlRt.exe` 挂起事件（Application 日志，事件 1002）：

| 时间 | 事件 |
|------|------|
| 2026-10-08 17:17:51 | Application Hang 1002 |
| 2026-10-08 17:59:14 | Application Hang 1002 |
| 2026-10-08 18:03:45 | Application Hang 1002 |
| 2026-10-08 18:43:29 | Application Hang 1002 |
| 2026-10-08 20:18:10 | Application Hang 1002 |
| 2026-10-08 20:32:16 | Application Hang 1002 |

- 上述挂起**仅出现在 10-08 当天**，前 14 天内无任何 `PdlRt` 挂起记录；时间点与当日的 Runtime 启动/重启一致。
- 触发代码本身并非当天新增（`ScreenButtom.Pdl` 最后保存于 2025-11-27，`.fct`/`config.ini` 为 2020 年），说明是 **exe 在近期丢失/变更**后，原本无害的调用才变成了挂起。

## 5. 缺失程序的排查范围（均已排除）

- 全盘搜索 C: / D: / E: / F: / G:：无原始 `SCPW20 TTS.exe`（仅 TTS 目录内残留 `config.ini`、`TTSText*.txt`）。
- 回收站（`$Recycle.Bin`、360 回收站）：无。
- 项目备份压缩包（含 `W3_20260128.rar`、内蒙项目备份等）：无。
- 卷影副本（`vssadmin list shadows`）：无。
- Prefetch：无 `SCPW`/`TTS` 记录。
- Amcache：无命中。
- 互联网检索：该插件为**内部定制程序**（作者标识 `uetzel`），无任何公开分发来源。

## 6. 已采取的措施

在项目 TTS 目录部署**占位程序**（stub），使 `ProgramExecute` 有目标可启动、脚本不再阻塞：

| 文件 | 大小 | SHA256 |
|------|------|--------|
| `D:\PROJECT\OZ3WINCC20250918\TTS\SCPW20 TTS.exe` | 5632 B | `C37F6141995ADDBDF2D12EE68A9171EB3543D839CAF7FDEB0C6943966BFA8ABE` |
| `E:\PROJECT\OZ3WINCC20250918\TTS\SCPW20 TTS.exe` | 5632 B | `C37F6141995ADDBDF2D12EE68A9171EB3543D839CAF7FDEB0C6943966BFA8ABE` |

措施后状态（已核实）：

- `PdlRt`（PID 960，2026-10-08 21:27:08 启动）`Responding=True`；
- 20:32 之后**再无新的挂起事件**；
- 当前无 TTS 进程/窗口残留。

> 说明：占位程序仅用于消除挂起，**不提供语音播报功能**（该功能因原始程序丢失已无法恢复，可接受）。

## 7. 残留风险与建议

要确保"不再出现"，需满足以下前提：

1. **保持占位程序在位**：`TTS\SCPW20 TTS.exe` 不得被删除或覆盖。
   - 风险场景：工程源重新下发、或从备份/E: 覆盖还原整个项目时，可能冲掉该文件。
2. **（推荐，一劳永逸）从源头处理**：在上位机工程中，移除或加条件屏蔽 `ScreenButtom.Pdl` 的 `OnOpenPicture` 里对 `SCPW20_ExeTTS()` 的调用，使运行不再依赖该 exe。
   - 注意：这是**共享工程**，修改会影响其他使用方，需评估后再动。

## 8. 附：关键路径

- 触发画面/事件：`GraCS\ScreenModule\ScreenButtom.Pdl`（事件 `OnOpenPicture`）
- 基本画面：`GraCS\ScreenModule\ScreenMain.Pdl`（`BasePicture`，内嵌 `ScreenButtom`）
- 全局函数：`Library\SCPW20_ExeTTS.fct`；声明 `Library\AP_PBIB.H`；常量 `Library\SCPW20.h`
- 插件目录：`<项目>\TTS\`（`config.ini`、`TTSText*.txt`、`SCPW20 TTS.exe`）
- 运行时：`C:\Program Files (x86)\Siemens\WinCC\bin\PdlRt.exe`
- 日志：Windows 事件查看器 → Application → 源 `Application Hang` / `Windows Error Reporting`

---

*本报告基于 2026-10-08 现场排查记录整理。*
