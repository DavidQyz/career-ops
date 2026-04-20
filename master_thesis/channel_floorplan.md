# Channel Floorplan — QC_rectifier (单个 channel)

> 记录单个 channel 的 layout floorplan 规划，包括 block 尺寸、相对位置、走线策略。
> 基于 8-channel 芯片全局架构决策。

---

## 1. 全局芯片架构背景

```
           pad ring (top)
     ┌──────┬──────────────────┬──────┐
     │  CH0 │                  │  CH1 │  H_bridge 朝上
     │  CH7 │                  │  CH2 │
     │      │   data_inputV2   │      │
     │  CH6 │   (chip center)  │  CH3 │
     │  CH5 │                  │  CH4 │  H_bridge 朝下
     └──────┴──────────────────┴──────┘
           pad ring (bottom)
```

- **8个 channel** 以 data_inputV2 为中心，中心对称分布
- 每两个 channel 面向芯片一侧的 pad ring
- H_bridge 输出端统一朝向最近的 chip 外侧边缘 → bond wire 最短
- 数字输入（SPI 信号）从中心等长分发到每个 channel

---

## 2. Block 尺寸汇总

| # | Cell Name | W (µm) | H (µm) | 面积 (µm²) | 备注 |
|---|-----------|--------|--------|-----------|------|
| 1 | `comparator_newV4_for_use_nb` | 17.00 | 16.64 | 283 | 近似正方形 |
| 2 | `VCDL_tryc_v4` | 7.66 | 7.91 | 61 | 近似正方形 |
| 3 | `#ND2D1BWP7T` (NAND) | 4.74 | 4.44 | 21 | BWP7T 标准单元 |
| 4 | `Idle_controller_nb` | 33.50 | 30.70 | 1,028 | 近似正方形 |
| 5 | `PI_controller_EN_mid_pos_nb_V3` | 121.00 | 60.00 | 7,260 | 宽扁 2:1 |
| 6 | `bias_local` | 51.00 | 50.00 | 2,550 | 近似正方形 |
| 7 | `cap` (Co, 80 pF) | 250.00 | 135.00 | 33,750 | ⚠️ 最大；MOS+MIM 叠加；硬约束 |
| 8 | `switchV8` (`LS_new_stacking`) | 19.30 | 19.00 | 367 | 近似正方形 |
| 9 | `H_bridge_LVT_use` × **8** | 91.00 | 27.00 | 19,656 | 8个实例；需朝向 pad ring |
| 10 | `IDAC_new_V5_for_sys_nb` | 170.00 | 58.50 | 9,945 | 宽扁 2.9:1 |
| 11 | `IDAC_assigner_V3` | 28.00 | 16.50 | 462 | 3行标准单元 |
| 12 | `Switch_assigner_V3` | 40.00 | 17.50 | 700 | 3行标准单元 |
| **合计（裸面积）** | | | | **76,083** | |
| **估算含走线×1.3** | | | | **~99,000** | ≈ 250 × 400 µm² |

**面积备注：**
- Co 占裸面积 **44%**，是绝对主导，且为硬约束（MOS+MIM 已达面积极限）
- 8× H_bridge 占裸面积 **26%**，是第二主导
- Co + 8×H_bridge 合计占 **70%**，决定 channel 整体形状

---

## 3. Channel 内部 Floorplan

### 3.1 核心原则

1. **H_bridge 朝外** — 8个 H_bridge 靠近 pad ring 侧，bond wire 最短
2. **Co 靠内** — Co 紧邻 H_bridge，V_SUPPLY (Mp_out) 走线最短
3. **模拟控制区居中** — comparator/VCDL/NAND/switchV8/PI/Idle 聚集，互联距离短
4. **数字配置靠内侧** — IDAC_assigner/Switch_assigner 靠近 chip 中心，接收 data_inputV2 信号
5. **V_headroom 走线远离数字噪声** — 模拟敏感节点屏蔽处理

### 3.2 Floorplan 示意图

```
  ← 内侧（chip center / data_inputV2）    外侧（pad ring）→

  ┌──────────┬──────────────────────────────┬──────────────┐  ↑
  │          │                              │  H_bridge×8  │  │
  │ IDAC_    │         Co (250×135)         │  (竖向叠排)  │  │
  │ assigner │                              │  91×27 each  │  ~216µm
  │          │                              │  (8×27=216)  │  │
  ├──────────┴──────────┬───────────────────┴──────────────┤  ↓
  │ Switch_assigner     │  IDAC (170×58.5)                 │  ↑
  ├─────────────────────┴──────────────────────────────────┤  │
  │ PI_controller (121×60)  │ bias │ Idle │ comp│VCDL│NAND │ ~60-80µm
  │                         │_local│_ctrl │     │    │ sw8 │  │
  └─────────────────────────┴──────┴──────┴─────┴────┴─────┘  ↓

  ←──────────────── ~250µm ─────────────────→←── 91µm ──→
```

### 3.3 各区域说明

**外侧列（pad ring 方向）：H_bridge × 8**
- 8个 H_bridge (91×27µm) 竖向叠排，总高度 = 8×27 = 216 µm
- 每个 H_bridge 的 V_cout_p/V_cout_n 输出端朝外，直连 bond pad
- 16个电极输出 pad 排成一列，pitch 管理简单

**中部主体：Co**
- Co (250×135µm) 占据中部最大区域
- 右侧紧邻 H_bridge，V_SUPPLY (Mp_out) 走线极短
- switchV8 输出直接连接 Co 顶部

**中部下方：IDAC**
- IDAC (170×58.5µm) 宽度与 Co 部分对齐
- I_STIM (V_headroom) 节点向左连接 PI controller

**底部：模拟控制带**
- PI_controller (121×60µm) 最宽，居左
- bias_local (51×50µm) 紧邻 PI controller，V_REF/V_REF_OUT 就近连接
- Idle_controller (33.5×30.7µm) 紧邻 PI controller
- comparator (17×16.64µm) + VCDL (7.66×7.91µm) + NAND (4.74×4.44µm) 串联排列，信号路径顺序
- switchV8 (19.3×19µm) 靠近 Co 顶部，SIN_p 输入从外侧引入

**内侧列（chip center 方向）：数字配置**
- IDAC_assigner (28×16.5µm) + Switch_assigner (40×17.5µm) 靠内侧
- 接收来自 data_inputV2 的 SPI 信号，走线短

---

## 4. 关键 Net 走线规划

| Net | 起点 → 终点 | 走线层 | 要求 |
|-----|-----------|--------|------|
| `V_SUPPLY` (Mp_out) | switchV8 → Co → H_bridge | M5/M6 | 最短路径，EM check |
| `V_headroom` (I_STIM) | H_bridge → IDAC → PI_ctrl | M4 | 模拟敏感，屏蔽 |
| `Vc` (PI output) | PI_ctrl → VCDL | M3 | 屏蔽，远离数字 |
| `SIN_p` (V_ein) | pad → switchV8 → comparator | M6 | 最短，13.56 MHz RF |
| `IDAC_ch1<4:0>` | IDAC_assigner → IDAC | M2/M3 | 匹配长度 |
| `EN_SW<7:0>` | Switch_assigner → H_bridge | M3 | 8-bit bus |
| `V_REF_OUT` (75mV) | bias_local → PI_ctrl | M3 | 模拟敏感 |
| `V_REF` (45mV) | bias_local → IDAC | M3 | 模拟敏感 |
| `VSS / VDDL` | ring → 各 block | M1+M2 ring | guard ring 全包 |

---

## 5. 面积估算

| 项目 | 面积 |
|------|------|
| 裸 block 面积合计 | 76,083 µm² |
| ×1.3 冗余系数（走线+间距+guard ring） | ~99,000 µm² |
| 等效尺寸（以250µm宽为基准） | 250 × ~400 µm² |
| 8个 channel 总面积 | ~0.79 mm² |
| 占 2×2.5 mm² chip 面积比 | ~16% |

**Co 是硬约束**：250µm 宽决定了 channel 宽度下限；
H_bridge 朝外后总高度约 216µm，Co 高度 135µm < 216µm，
channel 高度由 H_bridge 叠排高度（216µm）+ 下方控制区（~80µm）主导，
估算 channel 总高度约 **300~400 µm**。

---

## 6. 待确认事项

- [ ] H_bridge 8个实例：竖向单列（91×216µm）还是 4×2 双列（182×108µm）？
- [ ] Co 与 H_bridge 的精确对齐方式（顶部对齐 / 中部对齐）
- [ ] switchV8 位置：Co 顶部 vs 侧面
- [ ] 模拟控制带（PI/bias/comparator/VCDL）的精确排列顺序
- [ ] IDAC 与 Co 的上下关系（IDAC 在 Co 下方 vs 左方）
- [ ] 各 block 的 pin 位置确认（影响最终摆放方向）

---

*最后更新：全局架构 + block 尺寸完整；floorplan 草图完成；待细化各 block 相对坐标*
