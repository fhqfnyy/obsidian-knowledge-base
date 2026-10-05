安全PLC（Safety PLC）和普通PLC（Standard PLC）最大的区别是：安全PLC不仅控制设备运行，还专门负责“防止人员伤害和重大设备事故”，其设计必须满足严格的安全标准。

一句话理解：普通PLC负责“让设备工作”，安全PLC负责“让设备在危险时可靠停下来”。

一、核心区别（最重要）

|对比项|普通PLC|安全PLC|
|---|---|---|
|主要任务|逻辑控制、自动化运行|人员与设备安全保护|
|设计目标|稳定运行|故障时仍能保证安全|
|故障后果|设备停机、生产中断|必须避免人员伤亡和重大事故|
|硬件结构|单CPU为主|双CPU冗余、自诊断|
|输入输出|普通DI/DO|安全DI/DO，双通道检测|
|软件功能|普通逻辑运算|安全认证功能块|
|国际标准|一般工业标准|IEC 61508、ISO 13849等安全标准|
|典型应用|水厂、泵站、输送线|急停、安全门、机器人、化工联锁|

二、安全PLC为什么更安全？

1. 冗余结构（最关键）

![与 S7-1500R/H 冗余系统进行S7通信](https://images.openai.com/static-rsc-4/xI4xoOKxJbFrQWEfXut9GJl33cDR7josUVMayXw_qn7LAuCN1_vL5KtyK_WjtLbcUaJe7xLMAqtJtT1JvG8PV3NymwVby9_onEaGAPp4rggJGHpgV918IB0PnrHKum73gvxk0TFnrQmu6WTEDUYRKqwucu3_EWnwf8cb8CaFWZ74Zc3eg8LhSFy3NgvAcpi1?purpose=fullsize)

普通PLC：

- 通常只有1个CPU。
    
- 如果CPU死机、程序跑飞、硬件损坏，控制可能失效。
    
- 系统不一定知道自己已经故障。
    

安全PLC：

- 通常采用双CPU（甚至三CPU）冗余。
    
- 两个CPU同时运行同样的程序。
    
- 每个扫描周期互相校验结果。
    
- 只要结果不一致，立即进入安全状态（如停机）。
    

关键差异

安全PLC不是“不会坏”，而是“坏了也能安全停机”。

例如：

✔ CPU1计算：电机应该停止

✔ CPU2计算：电机应该停止

→ 输出停机

如果：

✔ CPU1计算：停止

✘ CPU2计算：运行

→ 系统判定故障 → 强制停机

2. 双通道安全输入

![使用安全继电器的急停电路设计-CSDN博客](https://images.openai.com/static-rsc-4/IG5nvdFQrXQ38jy2jPKb4zmAES_IZxvxv--xBl6kSfNf5KEbbq60TUm19LIaACh9BSUOrOm-HRIEDVlKZy5_mXKuYqTHT_-xKtvOiJTl1hq2IExi28OcHA9kE5GlXViFKOg1l6POwCAWnmVyLXTTGUdA380Edy5WaoHQhTYhuv3oYhjqnJwYwaIx1DetOme7?purpose=fullsize)

以急停按钮为例：

普通PLC接法：

急停按钮 → 一个输入点DI0

问题：

- 如果线路断了，PLC可能误认为按钮未按下。
    
- 存在危险。
    

安全PLC接法：

急停按钮 → 两个独立输入通道

通道A → SDI0

通道B → SDI1

PLC持续检测：

- 两路是否同时动作？
    
- 两路是否同时断开？
    
- 是否有短路？
    
- 是否有断线？
    

只要发现异常：

→ 判定故障 → 安全停机

3. 安全输出不会“粘住”

![安全回路如何设计？（机电产品）](https://images.openai.com/static-rsc-4/5wOReL__jGIbi1K8WNGHyIFpKTaLAMCrX54BHkvn761w61fu-vF4VaXkhoq4DoImys0hZFxR_QPrkH8wqLUgYDHgCL1cjf3dk6wg_rQ3floPtlSgusBYkSRQ5F4OzmiwpA2q476PbzvfLz_WafPIW2hL35oQyqB195BZb7U4d2hBxGKlm2wftgOGAhabZ4ht?purpose=fullsize)

普通PLC输出：

DO0 → 接触器线圈

如果输出继电器触点焊死：

PLC即使发出停止命令，设备仍可能运行。

安全PLC输出：

通常采用：

- 双继电器串联
    
- 强制导向触点
    
- 反馈检测回路
    

PLC会检查：

“我让它断开了，它是否真的断开了？”

如果没有断开：

→ 报安全故障 → 禁止重新启动

三、软件上的区别

![SIOS](https://images.openai.com/static-rsc-4/gsa9Bxn9fWMrTe6ja9cjNP3Y6oBeybeG8GKiwdHYoGPVygKGqO8dDL_cVYeSDwQE2dAsRTu0_jTZb8sEwdYNOPOfSXJ7WaqgK7ABtJbz7CUgykh2qGpDNOUflZnBKe4UgoNyLhpFuhGUQQAQ_I6EEf012H7auyCD9wHWv0E00F8KxfKVXMQvEdfDqMCWOv52?purpose=fullsize)

普通PLC程序：

你可以随便写：

IF 急停=1 THEN 电机停止

没有人保证这段程序一定安全。

安全PLC程序：

必须使用经过认证的安全功能块，例如：

- ESTOP（急停）
    
- Safety Gate（安全门）
    
- Two-Hand Control（双手按钮）
    
- Muting（屏蔽）
    
- Speed Monitoring（安全速度监控）
    

这些功能块已经通过国际安全认证。

你不能随便修改其核心逻辑。

四、认证等级区别

![Guide ISO 13849](https://images.openai.com/static-rsc-4/i23L6A7eEdbse-GvZhai-4LXxx7qq2HHwyhi87dvoqerweVaeWI0RCjOLGfrauMnrxdS51_pvfhmyp7zals1jUMeC6UKsM_FS9WnNgVr1sIBgFEC-2biu0YqlsU1DMIaj_xdLIZT9sPVT7RRmAmLmTwYCNSfe6L7veOrzlPpyY4Os5wDi7WiIOezhfr7Tqp9?purpose=fullsize)

安全PLC需要满足：

1. IEC 61508 → SIL等级

|等级|危险失效概率|
|---|---|
|SIL1|较低|
|SIL2|中等|
|SIL3|很高（工业常用）|
|SIL4|极高（核电、航空等）|

2. ISO 13849 → PL等级

|等级|安全能力|
|---|---|
|PL a|最低|
|PL b|较低|
|PL c|中等|
|PL d|高|
|PL e|最高（机械安全常用）|

普通PLC通常没有SIL/PL认证。

安全PLC通常可达到：

- SIL3
    
- PL e

## 整理关联

- [[02-工程技术/自动化/相关资料/可编程逻辑控制器（PLC）.md|可编程逻辑控制器（PLC）]]
