# Channel Layout Assembly Tracker
> 单个 channel (QC_rectifier) 的 layout 拼装记录
> 基于 schematic (重整版)，逐步填入各 block 的尺寸、anchor、摆放关系

---

## 1. Block 清单（按 schematic 位置）

| # | Cell Name | Instance | 类别 | 状态 |
|---|-----------|----------|------|------|
| 1 | `comparator_newV4_for_use_nb` | I123 | Analog – Control | 🔲 尺寸待填 |
| 2 | `VCDL_tryc_v4` | I133 | Analog – Control | 🔲 |
| 3 | `#ND2D1BWP7T` | I132 | Digital STD Cell | 🔲 |
| 4 | `Idle_controller_nb` | I108 | Analog – Control | 🔲 |
| 5 | `PI_controller_EN_mid_pos_nb_V3` | — | Analog – Control | 🔲 |
| 6 | `bias_local` | I128 | Analog – Bias | 🔲 |
| 7 | `cap` (Co, MOS+MIM) | I134 | Passive | 🔲 |
| 8 | `switchV8` | I98 | Analog – Power | 🔲 |
| 9 | `H_bridge_LVT_use` | — | Analog – Power | 🔲 |
| 10 | `IDAC_new_V5_for_sys_nb` | I165 | Analog – IDAC | 🔲 |
| 11 | `IDAC_assigner_V3` | I118 | Digital – Config | 🔲 |
| 12 | `Switch_assigner_V3` | I119 | Digital – Config | 🔲 |

---

## 2. 信号连接关系（Net List）

### 2.1 相位控制环路

```
V_ein ──────────────────────────────────────────────────→ switchV8 (输入 AC)
                                                          ↓
V_ein ─→ comparator_newV4 (I123)                    switchV8 输出 → Mp_out
Mp_out → comparator_newV4 (I123)                          ↓
         ↓ Vin                                           cap (Co) 并联至 VSS
         ↓
VCDL_tryc_v4 (I133)
  IN:  Vin  (来自 comparator 输出)
  IN:  Vb   (来自 PI controller 输出 Vc)
  OUT: VOUT
         ↓
#ND2D1BWP7T (I132)  ← NAND
  IN1: Vin   (直连 comparator 输出)
  IN2: VOUT  (来自 VCDL)
  OUT: Vand_out ──→ switchV8 (控制整流开关 DSL 端)
```

### 2.2 反馈控制环路

```
V_headroom (I_STIM 节点)
  ├──→ Idle_controller_nb (I108):  V_HEADROOM 输入
  │         IN:  V_EN_REF (bias), EN/nEN (来自 comparator)
  │         OUT: EN, nEN ──→ comparator & PI controller (开/关控制)
  │
  └──→ PI_controller_EN_mid_pos_nb_V3:  负输入端
            IN+: V_REF_OUT (来自 bias_local, 75 mV)
            IN:  EN, nEN   (来自 Idle controller)
            OUT: Vc (= PL_OTA_CLAMP) ──→ VCDL Vb
```

### 2.3 刺激路径

```
Mp_out (= V_SUPPLY)
  └──→ H_bridge_LVT_use
          IN:  V_SUPPLY
          IN:  EN_SW_PRE<7:0>  (来自 Switch_assigner I119)
          IN:  STIM_MODE       (来自 Switch_assigner I119)
          OUT: V_cout_p<7:0>   → 电极 P 端
          OUT: V_cout_n<7:0>   → 电极 N 端
          OUT: TO_CM           → V_headroom (通过 I83<7:0> bus)
                                       ↓
                               IDAC_new_V5_for_sys_nb (I165)
                                 IN:  D<4:0>    (= IDAC_ch1<4:0>, 来自 IDAC_assigner I118)
                                 IN:  V_REF     (来自 bias_local, 45 mV)
                                 IN:  IDAC bias (IDAC_VB1/2, IDAC_OTA_VB1/2, GOMP_VGM)
                                 OUT: I_STIM    (= V_headroom 节点)
                                 → VSS
```

### 2.4 数字配置路径

```
全局总线（来自 top-level data_inputV2）:
  IDAC<4:0>, CH_number, d_EN
  └──→ IDAC_assigner_V3 (I118)
          OUT: IDAC_PULSE<4:0> = IDAC_ch1<4:0> ──→ IDAC_new_V5 D<4:0>

  SW_number<7:0>, Stim_mode, CH_NUMBER, d_EN
  └──→ Switch_assigner_V3 (I119)
          OUT: EN_SW_PRE<7:0>   ──→ H_bridge EN_SW
          OUT: STIM_MODE_PRE    ──→ H_bridge STIM_MODE
```

### 2.5 Bias 分配

```
bias_local (I128)
  IN:  IDAC_VB2, IDAC_VB1  (全局 bias，来自 top-level)
  OUT: V_REF_OUT (75 mV)   ──→ PI controller IN+
  OUT: V_REF     (45 mV)   ──→ IDAC_new_V5 V_REF
  OUT: IDAC_OTA_VB1/VB2    ──→ IDAC_new_V5 OTA bias
  OUT: GOMP_VGM             ──→ IDAC_new_V5 cascode bias
  OUT: V_EN_REF (200 mV)   ──→ Idle_controller V_EN_REF
```

---

## 3. Block 尺寸记录（待填）

> 格式：Width × Height (µm)，以各 block 的 PR boundary 为准

| # | Cell Name | Width (µm) | Height (µm) | 备注 |
|---|-----------|-----------|------------|------|
| 1 | `comparator_newV4_for_use_nb` | 17.00 | 16.64 | 顶部差分对(INP/INN CC) + 左下 tail + 中 cascode 级 + 右下 MID_INV/output buffer；近似正方形 |
| 2 | `VCDL_tryc_v4` | 7.66 | 7.91 | 左上 pmos2v_n + 右上 pmos2v_c + 底部 nmos5v_mc；近似正方形 |
| 3 | `#ND2D1BWP7T` | 4.74 | 4.44 | BWP7T 标准单元，近似正方形 |
| 4 | `Idle_controller_nb` | 33.50 | 30.70 | 主体 ota_paperV4_nb_V2；顶部 IDAC_OTA_VB1/2 bias；底部 V_HEADROOM/V_EN_REF 长 bus；右侧 3×INVD0BWP7T (EN/nEN 输出)；近似正方形 |
| 5 | `PI_controller_EN_mid_pos_nb_V3` | 121.00 | 60.00 | 宽扁矩形(2:1)；左侧 OTA 误差放大器(V_FB/PL_OTA_CLAMP)；中央 rppolyhri3k 电阻(Rz=100kΩ)；底排 6×mimcap_2p0_sin(C1=3.2pF+C2=160fF 补偿电容) |
| 6 | `bias_local` | 51.00 | 50.00 | 近似正方形；左上 2组 pmos2v 阵列(电流镜)；右侧 unit_cap(VSS 去耦)；左下 2×rphpoly(分压电阻)；右下 V_REF_OUT bus |
| 7 | `cap` (Co, 80 pF, MOS+MIM) | 250.00 | 135.00 | ⚠️ 最大 block，面积 ~33750 µm²，主导 channel 整体版图；MOS+MIM 叠加 |
| 8 | `switchV8` (`LS_new_stacking`) | 19.30 | 19.00 | 左半：大面积整流开关管；右上：p/c PMOS；右中：DB level shifter driver；右下：NVDO BWP7T；最大 block |
| 9 | `H_bridge_LVT_use` × **8** | 91.00 | 27.00 | ⚠️ 1:8 TDM → **8个实例**；单个面积 2457 µm²，总面积 ~19656 µm²（第二大）；宽扁条形(宽:高≈3.4:1) |
| 10 | `IDAC_new_V5_for_sys_nb` | 170.00 | 58.50 | 宽扁矩形(2.9:1)；顶行多个 ota_paperV4_nb_V2(per-bit OTA)；左侧 pmos2v_mac 参考支路；中央 32-tile CC NMOS 阵列；第二大 block |
| 11 | `IDAC_assigner_V3` | 28.00 | 16.50 | 宽扁3行标准单元；顶行 DEL4+XOR2+AN2 脉冲发生器+CHANNEL_NUMBER，下两行 5×DFBD0BWP7T (IDAC_PULSE<4:0>) + TAPCELL |
| 12 | `Switch_assigner_V3` | 40.00 | 17.50 | 宽扁3行标准单元；顶行 DEL4+控制逻辑，下两行 8×DFBBBWP7T (EN_SWP<7:0>) + TAPCELL |

---

## 4. Layout 摆放策略（待规划）

> 原则：信号路径最短、关键 net 屏蔽、电源/信号走线分层

### 参考分区（根据 schematic 拓扑）

```
┌─────────────────────────────────────────────────┐
│  [bias_local]   [comparator]  [VCDL+NAND]  [switchV8]  │  ← 顶部：模拟控制 + 功率
│  [PI_ctrl]      [Idle_ctrl]                [cap(Co)]    │  ← 中部：反馈控制
│  [H_bridge]     [IDAC_new_V5]                           │  ← 中部：刺激路径
│  [IDAC_assigner]              [Switch_assigner]         │  ← 底部：数字配置
└─────────────────────────────────────────────────┘
```

> 注：具体坐标在各 block 尺寸确认后填入

---

## 5. 关键 Net 走线要求

| Net | 类型 | 走线层 | 备注 |
|-----|------|--------|------|
| V_headroom | 模拟敏感 | M3/M4 | 远离数字噪声 |
| Vc (PI output) | 模拟控制 | M3 | 屏蔽处理 |
| V_SUPPLY (Mp_out) | 高压大电流 | M4/M5 | EM check 必须 |
| IDAC_ch1<4:0> | 数字 | M2/M3 | 匹配长度 |
| SIN_p (V_ein) | 高频 RF | M5/M6 | 最短路径，远离模拟 |
| VSS/VDDL | 电源 | M1+ring | 全局连接，guard ring |

---

*最后更新：组件清单 + 连接关系完成；尺寸待逐个确认*
