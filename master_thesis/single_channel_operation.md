# Single Channel Operation — QC_rectifier

> 本文档描述单个刺激通道（QC_rectifier）的完整工作原理。
> 该通道基于Varkevisser et al. (TCAS-I 2025) 架构，进行了多项关键改进。

---

## 1. 概述

每个通道是一个**自适应调压整流器 + 精密电流刺激器**，核心思想是：

- 从无线接收的正半波AC信号（SIN_p, 13.56MHz）中取能
- 通过相位控制整流开关，自动调节输出电容上的电压至"刚好够用"
- 将精确电流通过H-bridge传递给选定电极
- 整个过程无需外部合规监测（compliance monitor）

单个通道支持：
- 电流范围：0–75µA（5µA步进，5-bit DAC）
- 负载阻抗：20–70kΩ
- 电容负载：1–20nF
- 8个电极复用（通过H-bridge + SW选择）
- 双相刺激（STIM_MODE控制H-bridge极性）

---

## 2. 主信号路径

### 2.1 能量传递路径（整流充电）

```
SIN_p（正半波AC）
    ↓ [switchV8导通时]
Mp_out节点
    ├── Co（输出电容，MOS+MIM叠加，同面积容值×2）← 储能
    └── V_SUPPLY → H_bridge_LVT_use
                        ↓ EN_SW选择电极，STIM_MODE决定极性
                   V_out_p → 片外电极（负载Ztissue）→ V_out_n
                        ↓
                   I_STIM节点（= V_headroom = Vfb）
                        ↓
                   IDAC_new_V5（current sink）→ VSS
```

**关键节点：**
- **Mp_out**：整流开关输出，Co稳定此节点电压
- **I_STIM = V_headroom**：IDAC的drain端电压，是整个反馈环路的核心反馈信号
- **V_headroom目标值：75mV**（由PI控制器维持）

### 2.2 控制信号路径（相位控制）

```
SIN_p ──→ comparator（比较SIN_p vs Mp_out）
                ↓ Vin（0/1）
    ┌───────────┴────────────┐
    │                        ↓
    │               VCDL（电流饥饿型INV，延迟td）
    │                        ↓ Vin_delayed（反相）
    └──→ NAND ←─────────────┘
              ↓
         低有效pulse（width = td）
              ↓
         switchV8（控制整流开关导通/截止）
```

### 2.3 反馈控制路径

```
V_headroom（I_STIM节点）
    ├──→ PI控制器（比较V_headroom vs V_REF_OUT=75mV）
    │         ↓ Vc
    │    VCDL（调节延迟td）
    │         ↓
    │    pulse width调节 → Co充电量调节 → V_headroom调节
    │
    └──→ Idle Controller（比较V_headroom vs V_EN_REF=200mV）
              ↓ EN/nEN
         控制comparator + PI控制器的开启/关闭
```

---

## 3. 逐模块工作说明

### Step 1：整流触发（comparator）

每个SIN_p正半波周期（73.8ns），comparator检测SIN_p与Mp_out的关系：

- **SIN_p > Mp_out**：输出Vin=1 → 上升沿触发VCDL+NAND产生低pulse
- **SIN_p < Mp_out**：输出Vin=0 → 强制关断整流开关，防止Co放电

这确保了整流开关只在能量可以从SIN_p流向Co时才导通。

### Step 2：脉冲生成（VCDL + NAND）

Vin上升沿到来时，VCDL（电流饥饿型反相器）产生延迟反相信号：

```
Vin=1到达后td时间内：NAND(1,1)=0  ← 低pulse，整流开关导通
td后VCDL输出翻转：  NAND(1,0)=1  ← pulse结束，开关关断
```

pulse宽度 = td，由PI控制器输出的Vc（偏置电压）控制：
- Vc↑ → 电流↑ → 延迟td↓ → pulse变窄 → 充电量↓
- Vc↓ → 电流↓ → 延迟td↑ → pulse变宽 → 充电量↑

### Step 3：整流充电（switchV8）

低pulse期间整流开关导通，SIN_p对Co充电：
- 高压工况（SIN_p高）：PMOS开关导通
- 低压工况（SIN_p低）：自动切换互补NMOS导通，低压效率提升~150%

### Step 4：电流刺激（H_bridge + IDAC）

Co上的电压（Mp_out）经H_bridge驱动选定电极：
- **EN_SW<7:0>**（one-hot）：选择8个电极之一
- **STIM_MODE**：
  - =1（Forward）：电流 V_P → 电极 → V_N → I_STIM
  - =0（Reverse）：电流 V_N → 电极 → V_P → I_STIM
- 电流经负载后流入I_STIM节点 → IDAC（current sink）→ VSS

IDAC精度由内部OTA主动反馈保证：
- 运放钳位电流镜管drain端 = V_REF（45mV）
- headroom从Cesc设计的250mV降至45mV（↓82%）

### Step 5：PI控制（PI controller）

PI控制器以13.56MHz采样频率（每周期一次）调节：

```
误差 = V_headroom - V_REF_OUT(75mV)
  > 0：Vc↑ → pulse缩短 → 充电减少 → V_headroom下降
  < 0：Vc↓ → pulse延长 → 充电增加 → V_headroom上升
  = 0：稳态，V_headroom精确保持在75mV
```

**vs Cesc开环OTA**：积分项消除稳态误差，稳态下V_headroom = 75mV（而非近似值）

带宽约束：控制环路带宽 < 13.56MHz / 2 = 6.78MHz（奈奎斯特限制）

### Step 6：轻载节能（Idle Controller）

当负载切换导致V_headroom > 200mV（V_EN_REF）时：

```
EN=0 → comparator关闭 → VCDL无触发 → switchV8停止充电
       PI控制器关闭
       仅IDAC维持工作（保持电流输出）
```

当V_headroom回落 ≤ 200mV后系统自动恢复。

---

## 4. 数字配置接口

每个channel通过SPI-like接口配置，每帧10µs：

```
外部SPI（CLK=10MHz，DATA，d_EN）
    ↓ data_inputV2（12bit移位寄存器）
    ↓ [CH_addr<2:0>][SW_addr<2:0>][Stim_mode][IDAC<4:0>]
    ↓ 3-bit decoder → CH<7:0> one-hot
    ↓
  channel N（CH<N>=1时响应）
    ├── IDAC_assigner_V3：存储IDAC<4:0> → 控制电流幅值
    └── Switch_assigner_V3：存储SW_number<7:0> + Stim_mode → 控制电极选择和极性
```

---

## 5. 关键设计改进（vs Cesc TCAS-I 2025）

| 改进点 | Cesc | 本设计 | 效果 |
|--------|------|--------|------|
| 误差放大器 | 开环OTA | PI控制器 | 消除稳态误差 |
| IDAC headroom | 250mV | 45mV（V_REF钳位） | ↓82%，更高效率 |
| 轻载处理 | 无 | Idle Controller | 高→低负载切换效率提升 |
| Level Shifter | Cross-coupled | CMLS架构 | 毛刺→标准方波 |
| 整流开关 | PMOS only | 互补NMOS | 低压效率↑150% |
| VCDL延迟范围 | 33ns–800ps | 10ns–170ps | 最小延迟↓70%，支持快速TDM |
| 输出电容Co | MOS或MIM | MOS+MIM叠加 | 同面积容值×2 |
| 通道数 | 2 | 8（+1:8 TDM→64电极） | 规模扩展 |

---

## 6. 本地Bias生成（bias_local）

每个channel内置bias_local模块，本地生成两个关键基准：

| 信号 | 电压 | 用途 | 本地生成原因 |
|------|------|------|------------|
| V_REF_OUT | 75mV | PI控制器参考 | 防止全局基准波动影响各channel |
| V_REF | 45mV | IDAC drain钳位 | 同上，channel间隔离 |

---

## 7. 稳态工作点总结

| 节点 | 电压 | 说明 |
|------|------|------|
| Mp_out | V_electrode + V_headroom | 随负载自动调节 |
| V_headroom（I_STIM） | 75mV（稳态） | PI控制器维持 |
| IDAC drain（V_REF） | 45mV | 运放钳位 |
| V_EN_REF | 200mV | Idle Controller阈值 |
| SIN_p频率 | 13.56MHz | 采样频率 = 控制环路更新率 |
