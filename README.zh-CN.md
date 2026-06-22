<p align="center">
  <img src="./media/photos/project/front_view.png" alt="Hsiwu - 仿生蝙蝠扑翼机器人前视图" width="800"/>
</p>

<h1 align="center">Hsiwu</h1>

<p align="center">
  <strong>无MCU纯模拟电路仿生蝙蝠扑翼机器人</strong>
  <br>
  三段独立驱动翅膀 · 纯模拟控制
</p>

<p align="center">
  <a href="./LICENSE.md"><img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="License: MIT"></a>
  <img src="https://img.shields.io/badge/SolidWorks-2025-e63946?logo=dassault-systemes&logoColor=white" alt="SolidWorks 2025">
  <img src="https://img.shields.io/badge/Altium_Designer-2025-00a0e3?logo=altium-designer&logoColor=white" alt="Altium Designer 2025">
  <img src="https://img.shields.io/badge/PCB-EasyEDA-5588ff" alt="EasyEDA">
  <img src="https://img.shields.io/badge/Make-FDM_3D_打印-f57c00" alt="FDM 3D打印">
  <img src="https://img.shields.io/badge/控制-纯模拟-f0c040" alt="纯模拟控制">
  <br>
  <a href="./README.md">English Version</a>
</p>

---

## 名称由来

<p align="center">
  <img src="./media/photos/hsiwu/hsiwu.png" alt="西木——成龙历险记中的天之恶魔" width="300"/>
  <br>
  <em><strong>Hsi Wu</strong>（西木），<strong>《成龙历险记》</strong>中的天之恶魔。</em>
</p>

本项目名称 **Hsiwu** 来源于《成龙历险记》中的蝙蝠形态**天之恶魔**——**Hsi Wu**（西木）。恰如其名：正如西木以分段变形之翼主宰天空，Hsiwu 机器人亦复现了真实蝙蝠的分段翼膜扑动。

---

## 项目简介

**Hsiwu**——以蝙蝠形态天之恶魔命名——是一款仿生蝙蝠扑翼机器人。与传统单段刚性机翼无人机不同，它复现了真实蝙蝠飞行中分段式翼膜变形的运动模式。每侧翅膀分为三个独立驱动的段落——**前缘段**、**中段**和**后缘段**——各自由独立的直流电机驱动。

仿生设计以*中华鼠耳蝠*（*Rhinolophus luctus*）为形态学参考。通过曲柄摇杆四连杆机构，将各直流电机的连续旋转运动转化为驱动翅膀段落的往复摆动，忠实再现了真实蝙蝠翼膜的耦合扑动-伸展运动学特性。

本项目的一个标志性特征是**纯模拟控制架构**。系统不使用任何微控制器（MCU），也不包含任何嵌入式软件。取而代之的是完全依赖分立模拟与数字逻辑电路——**NE555**定时器、**74HC04**反相器和**L293D** H桥驱动芯片——来产生驱动翅膀所需的同步周期性往复运动。

项目涵盖了完整的工程栈：模拟电子设计、数字逻辑、定制PCB布局、机械结构建模（SolidWorks）、FDM 3D打印以及硬件组装与调试。

> **参考文献**：气动与机械原理参考自《仿生蝙蝠飞行器的设计制造》（齐津浩，张卫平，2020），发表于《机械设计与研究》第36卷第5期。该参考样机翼展205 mm，整机质量17.81 g，扑动角度70°，扑动频率约10 Hz，在3.7 V供电下可产生约0.16 N的升力。

<p align="center">
  <img src="./media/photos/project/colored_version.png" alt="Hsiwu 完整装配体渲染图" width="700"/>
  <br>
  <em><strong>图0 — 项目概览。</strong>Hsiwu 完整装配体的全彩三维渲染图。可见双侧对称框架底盘（中央支撑、头部支撑、尾部支架）、六段独立驱动翅膀（每侧三段——前缘、中段、后缘）、连杆、丝杠驱动剪式伸缩机构以及定制异形PCB。此渲染图对应于 <code>final_assembly.SLDASM</code> 中定义的最终装配状态。</em>
</p>

---

## 核心特性

- **仿生翅膀运动学** — 每侧翅膀三段独立驱动（前缘、中段、后缘），各段配备独立直流电机，模拟真实蝙蝠翼膜的伸展与弯曲。曲柄摇杆四连杆机构将电机连续旋转转化为往复扑动。
- **零MCU / 零代码** — 纯模拟控制：NE555多谐振荡器产生时序，74HC04六反相器进行相位分离，L293D双H桥实现双向电机驱动。无微控制器、无固件、无需编程。
- **定制异形PCB** — 在EasyEDA（LCEDA）和Altium Designer中设计的专用异形PCB，轮廓贴合内部机械框架。将电源管理、信号发生和电机驱动集成于单块紧凑电路板上。
- **模块化机械设计** — SolidWorks全参数化三维模型。框架部件可FDM 3D打印，采用标准M2/M3螺丝和铰链连杆组装，便于调校和维修。
- **集成电源管理** — 单节3.7V锂电池输入，MT3608升压转换器（3.7V → 7.35V供电机），板载LDO稳压（5V供逻辑电路），防反接保护，带指示灯电源开关。

---

## 系统架构

### 1. 电子系统（`/electronics`）

核心逻辑分布在集成于单块定制PCB上的五个功能模块中：

<p align="center">
  <img src="./media/photos/project/PCB_Base_doc.png" alt="完整系统原理图" width="800"/>
  <br>
  <em><strong>图1 — 完整系统原理图。</strong>包含四个功能模块的完整电路图：（左）MT3608升压转换器，带肖特基二极管防反接保护和电源开关；（中上）三路NE555多谐振荡器级，各自产生独立的~4.8 Hz方波时序信号；（右）74HC04六反相器，用于产生互补相位信号；（下）三片L293D双H桥驱动芯片，每片使用一个半桥通道驱动一个电机，实现连续正反转往复运动。</em>
</p>

| 模块 | 核心器件 | 功能 |
|-------|-----------|----------|
| **电源供应** | MT3608、LDO、肖特基二极管 | 3.7V锂电 → 7.35V（升压，供电机）+ 5V（LDO，供逻辑）。防反接保护，带指示灯电源开关。 |
| **时序发生** | 3× NE555（多谐振荡模式） | 三路独立同步方波振荡器，频率约4.8 Hz。设定扑翼频率。每路NE555的RC网络可独立调校，实现分段时序微调。 |
| **相位反相** | 1× 74HC04 六反相器 | 将三路NE555输出反相，为H桥方向控制提供互补逻辑电平。每路NE555输出驱动一个反相器通道，产生对应半桥的反向信号。 |
| **电机驱动** | 3× L293D 双H桥 | 每片L293D使用一个半桥通道驱动一个电机。互补逻辑信号产生连续正反转往复运动。内置续流二极管防止感性反冲。 |
| **PCB布局** | 定制异形轮廓 | 垂直模块化布局；板外形贴合内部机械框架。异形板轮廓匹配中央支撑底盘的曲率。 |

<p align="center">
  <img src="./media/photos/project/PCB_3d_top.png" alt="3D PCB渲染" width="400"/>
  <img src="./media/photos/project/PCB_top.png" alt="2D顶层" width="400"/>
  <br>
  <em><strong>图2a–2b — PCB设计视图（一）。</strong>左：三维渲染图，展示定制异形PCB上的元件布局——三片NE555（SOP-20封装，U1–U3）、74HC04（SOIC-14，U5）、三片L293D（SOIC-8封装等效，U6–U8）、MT3608（SOT-23-6，U4）、SMC肖特基二极管（D1）、SMD功率电感（L1）、XH-2A连接器（U9）、0805无源元件（R1–R11、C1–C8）以及滑动开关（SW2）。右：顶层铜皮布线及丝印覆盖层。</em>
</p>

<p align="center">
  <img src="./media/photos/project/PCB_bottom.png" alt="2D底层" width="400"/>
  <img src="./media/photos/project/PCB_raw_top.png" alt="顶层裸铜走线" width="400"/>
  <img src="./media/photos/project/PCB_raw_bottom.png" alt="底层裸铜走线" width="400"/>
  <br>
  <em><strong>图2c–2e — PCB设计视图（二）。</strong>从左至右：底层铜皮及丝印层；顶层裸铜走线（去填充）；底层裸铜走线（去填充）。Gerber制板文件位于 <code>electronics/output/gerber/</code>。</em>
</p>

### 2. 机械结构（`/mechanical`）

机械装配体在SolidWorks中设计（`.SLDPRT` / `.SLDASM`源文件），通过FDM 3D打印制造。运动学原理参考自《仿生蝙蝠飞行器的设计制造》（齐津浩、张卫平，2020）。

#### 2.1 运动学机构（参考文献）

以下运动学示意图引自参考文献，展示了构成翅膀传动系统的四个核心机构：

<p align="center">
  <img src="./media/photos/reference/figure1.png" alt="曲柄摇杆机构" width="400"/>
  <img src="./media/photos/reference/figure2.png" alt="连杆尺寸" width="400"/>
  <br>
  <em><strong>图3a–3b — 曲柄摇杆四连杆机构。</strong>左：将电机轴连续旋转运动转化为摇杆上下往复摆动的曲柄摇杆机构运动学简图。曲柄（由减速空心杯直流电机驱动）连续旋转；连杆将运动传递给摇杆，摇杆输出角度决定了翅膀段落的扑动幅度（参考设计约70°）。右：带参数表的尺寸化连杆图纸——关键连杆长度a/b/c/d/e/f/g/h/i/j/k以及曲柄/摇杆枢轴角度α/β均按目标75°扑动行程标注。参考样机采用针对205 mm翼展优化的连杆比例。</em>
</p>

<p align="center">
  <img src="./media/photos/reference/figure3.png" alt="剪式机构" width="400"/>
  <img src="./media/photos/reference/figure4.png" alt="腿机构" width="400"/>
  <br>
  <em><strong>图3c–3d — 剪式伸缩与腿部传动机构。</strong>左：由丝杠（螺距P = 0.4 mm，有效直径d₂ = 1.74 mm）控制的剪式机构。电机驱动丝杠使移动螺母前进/后退，推动/拉动剪式连杆以控制翼膜伸缩。参考文献计算出丝杠驱动力矩M = 0.0064 N·mm。右：腿部/传动机构，展示电机到连杆的耦合几何关系，通过静力平衡分析求解枢轴点O–O′及反力F<sub>A</sub>、F<sub>B</sub>。整体机械优势设计为在3.7 V供电下产生约0.16 N升力。</em>
</p>

#### 2.2 本项目机械装配体

本项目的机械装配体由以下子装配体组成（全部SolidWorks源文件位于 `mechanical/source/`）：

| 子装配体 | 路径 | 关键零件 | 说明 |
|-------------|------|-----------|------|
| **结构框架** | `structural/` | `central_support`、`head_support`、`tail_bracket` | 左右对称底盘；中央支撑构成主体，头部/尾部支架锚定翅膀枢轴点 |
| **连杆** | `rods/` | `rod_a`、`rod_bc`、`rod_d`、`rod_h`、`rod_ij`、`rod_kf` | 六种连杆类型，构成各翅膀段落的曲柄摇杆四连杆运动链 |
| **电机与联轴器** | `motor/` | `four-stage_geared_hollow_cup_motor`、`2-to-3_coupling` | 空心杯直流电机，四级行星齿轮减速；2转3轴联轴器连接丝杠 |
| **丝杠总成** | `structural/` | `lead_screw`、`head_drive_shaft`、`bearing`、`center_bearing_cap`、`head_bearing_cover` | 丝杠（P = 0.4 mm）驱动剪式伸缩连杆；两端由微型轴承支撑 |
| **枢轴硬件** | `structural/` | `head_pin`、`tail_pin` | 前缘和后缘枢轴点处翅膀段落铰接的精密铰链销 |
| **紧固件** | `fasteners/` | `SCA_M2_L10/L12/L14/L18`、`SLMNA-M2`、`SLMNA-M3` | M2沉头螺丝（10–18 mm长度），M2/M3防松螺母用于框架装配 |
| **总装配体** | `assembly/` | `final_assembly.SLDASM` | 引用所有子零部件在其装配位置的主装配体文件 |

<p align="center">
  <img src="./media/photos/project/screw_movement.gif" alt="丝杠传动动画" width="600"/>
  <br>
  <em><strong>图4 — 丝杠传动动画。</strong>丝杠驱动剪式机构的动态演示。电机旋转丝杠时，移动螺母线性前进，推动剪式连杆张开以扩展翼膜面积——或收缩以减小面积。这种耦合的伸缩/扑动运动是复现蝙蝠翅膀变弯度空气动力学特性的关键仿生特征。</em>
</p>

<p align="center">
  <img src="./media/photos/project/top_view.png" alt="俯视图" width="400"/>
  <img src="./media/photos/project/line_drawing.png" alt="等轴测线框图" width="400"/>
  <br>
  <em><strong>图5a–5b — 机械图纸。</strong>左：俯视图——展示电机布局、连杆布线及整体翼展几何。右：等轴测线框图——线描渲染，展示完整的运动学骨架，所有连杆枢轴点、杆件连接和结构框架构件在同一透视图中可见。前视图见项目顶部横幅。</em>
</p>

---

## 仓库结构

```
HSIWU/
├── electronics/                 # PCB设计文件
│   ├── source/                  #   LCEDA工程（.eprj2）+ Altium Designer源文件（.schdoc, .pcbdoc）
│   └── output/                  #   Gerber文件、BOM、贴片坐标（.csv/.xlsx）、PDF原理图
├── mechanical/                  # 3D CAD模型（SolidWorks）
│   ├── source/                  #   原生SLDPRT/SLDASM文件
│   │   ├── structural/          #     框架、丝杠、轴承、销钉、支架
│   │   ├── rods/                #     六种连杆类型（a, bc, d, h, ij, kf）
│   │   ├── motor/               #     减速空心杯电机+轴联轴器
│   │   ├── fasteners/           #     M2/M3螺丝及防松螺母
│   │   ├── pcb_3d_models/       #     含全部元件3D模型的PCB装配体
│   │   └── assembly/            #     总装配体文件
│   └── output/                  #   导出文件 — STEP（3D打印）、STL（激光切割）
├── docs/                        # 技术文档
│   ├── en/                      #   英文文档 — 工作原理、机械设计、组装指南
│   └── zh-CN/                   #   中文文档 — 工作原理、机械设计、组装指南
├── BOM/                         # 物料清单电子表格（.xlsx）
├── media/                       # 项目媒体资源
│   ├── photos/
│   │   ├── project/             #   渲染图、PCB视图、机械图纸、测试照片
│   │   └── reference/           #   参考文献插图及原始论文（PDF）
│   └── videos/                  #   演示视频（.mp4）
├── .github/                     # GitHub社区文件
│   ├── CODEOWNERS               #   代码所有权定义
│   ├── PULL_REQUEST_TEMPLATE.md #   PR模板
│   ├── ISSUE_TEMPLATE/          #   缺陷报告与功能请求模板
│   └── FUNDING.yml              #   赞助配置
├── .gitignore                   # CAD/EDA/3D打印感知的忽略规则
├── .gitattributes               # 二进制/文本处理及diff配置
├── CHANGELOG.md                 # 版本历史（Keep a Changelog格式）
├── CONTRIBUTING.md              # 贡献指南
├── LICENSE.md                   # MIT许可证
├── README.md                    # 项目概览（英文）
└── README.zh-CN.md              # 项目概览（中文）
```

---

## 物料清单

完整元器件电子表格见 [`BOM/`](BOM/)。关键组件：

| 类别 | 项目 | 数量 | 备注 |
|----------|------|-----|-------|
| **框架** | 3D打印零件（ABS/PLA） | — | 中央支撑、头部支撑、尾部支架、轴承盖；推荐0.2 mm层高 |
| **紧固件** | M2沉头螺丝（10/12/14/18 mm） | — | SCA系列 |
| | M2/M3防松螺母 | — | SLMNA系列 |
| **轴承** | 微型滚珠轴承 | — | 用于丝杠和枢轴关节 |
| **电机** | 直流空心杯减速电机 | 3 | 四级行星齿轮减速；每个翅膀段落一个 |
| **电源** | 3.7V锂电池 | 1 | 单节 |
| **IC** | NE555定时器（SOIC-8等效，SOP-20载板） | 3 | 多谐振荡器配置 |
| | 74HC04六反相器（SOIC-14） | 1 | 六路非门；三路用于相位反相 |
| | L293D双H桥驱动（SOIC-8等效） | 3 | 每个电机使用一个半桥通道 |
| | MT3608升压转换器（SOT-23-6） | 1 | 3.7V → 7.35V升压 |
| **无源元件** | 电阻、电容（0805封装） | — | 定时RC网络、去耦、滤波 |
| | 肖特基二极管（SMC封装） | 1 | 防反接保护 |
| | 功率电感（SMD，7.3×6.8 mm） | 1 | MT3608升压电感 |
| | 可调电阻（SMD微调电位器） | 3 | 各段频率独立微调 |
| **连接器** | XH-2A 2针排母（P2.50间距） | — | 电机线连接 |
| | 滑动开关（SS12D07VG4） | 1 | 电源开关 |
| | 指示灯LED（0805封装） | 1 | 绿色电源指示 |

---

## 复现指南

### 1. 电子部分
参见 [`electronics/`](electronics/)。原理图文档见 `PCB_Base.pdf` 和 `PCB_Base_doc.png`（**图1**）。可直接使用 `electronics/output/gerber/` 中的Gerber文件制板，或从 `electronics/source/JLC/` 中的EasyEDA（LCEDA）工程重新导出。贴片坐标文件（`electronics/output/pick_and_place/PCB_BASE.csv`）和BOM（`electronics/output/bom/PCB_Base_BOM.xlsx`）可直接用于SMT组装服务。

### 2. 机械部分
三维模型位于 [`mechanical/`](mechanical/)。将零件导出为STL格式，在标准FDM 3D打印机上打印（推荐0.2 mm层高；结构耐久选ABS，易打印选PLA）。`mechanical/output/3dp/` 中的STEP文件可直接切片打印。激光切割零件（连杆）使用 `mechanical/output/laser/` 中的STL文件。如需修改设计，打开 `mechanical/source/` 中的原生SolidWorks源文件——所有零件均已全参数化。

### 3. 组装
1. 按原理图（**图1**）将全部元件焊接到定制PCB上。元件布局参考PCB设计视图（**图2a–2e**）。
2. 将三路电机线连接到PCB上的XH-2A排母。
3. 3D打印结构框架零件：`central_support`、`head_support`、`tail_bracket`、`head_bearing_cover`、`center_bearing_cap`、`head_drive_shaft`、`head_pin`、`tail_pin`。
4. 3D打印或激光切割连杆：`rod_a`、`rod_bc`、`rod_d`、`rod_h`、`rod_ij`、`rod_kf`。
5. 使用M2/M3螺丝、防松螺母和微型轴承按 `final_assembly.SLDASM` 组装框架。装配参考机械图纸（**图5a–5b**）及顶部前视图横幅。
6. 将PCB安装到中央支撑底盘上。

### 4. 上电
装入3.7V锂电池，拨动电源开关（SW2）。板载绿色LED（LED1）应点亮，翅膀应以设计的~4.8 Hz频率开始扑动。可通过调节三个微调电位器（R4、R10、R11）来微调扑动频率，它们分别设定各路NE555的RC时间常数。

---

## 演示与测试

上电测试期间，板载绿色指示灯点亮，3D打印的机器人执行完整扑翼动作。使用 Tektronix TDS1001B-SC 示波器监测 NE555 振荡器级的 ~4.8 Hz 方波输出，验证信号完整性。

<p align="center">
  <img src="./media/photos/project/test1.JPG" alt="组装场景" width="400"/>
  <img src="./media/photos/project/test3.JPG" alt="PCB焊接场景" width="400"/>
  <br>
  <em><strong>图6a–6b — 组装与制作。</strong>左：工作台组装场景——带放大镜的第三手工具夹持部分装配的机械框架，工作垫上可见PCB、电机和连杆。右：PCB焊接台——定制异形PCB正进行0805无源元件和IC的手工SMT焊接。</em>
</p>

<p align="center">
  <img src="./media/photos/project/test2.JPG" alt="示波器实时测试" width="600"/>
  <br>
  <em><strong>图7 — 示波器实时测试。</strong>Tektronix TDS1001B-SC 数字存储示波器在上电测试中探测 NE555 输出波形。示波器显示 ~4.8 Hz 方波（周期 ≈ 208 ms），电压摆幅从 0 V 到 V<sub>CC</sub>（5 V 逻辑轨），确认多谐振荡器工作正常。多通道显示可同时监测全部三路电机驱动信号的相位验证。</em>
</p>

---

## 设计规格

| 参数 | 数值 | 来源 |
|-----------|-------|--------|
| **仿生参考对象** | 中华鼠耳蝠（*Rhinolophus luctus*） | 参考文献 |
| **翼展** | 205 mm（参考文献） | 齐津浩、张卫平，2020 |
| **整机质量** | 17.81 g（参考文献） | 齐津浩、张卫平，2020 |
| **扑动频率** | ~4.8 Hz（本项目）/ ~10 Hz（参考文献） | NE555 RC网络 / 论文 |
| **扑动角度** | ~70°（参考文献目标） | 曲柄摇杆机构几何 |
| **供电** | 3.7V 单节锂电池 | 本项目 |
| **电机供电** | 7.35V（升压） | MT3608转换器 |
| **逻辑供电** | 5V（稳压） | 板载LDO |
| **电机类型** | 空心杯直流电机，四级行星齿轮减速 | ZWPD006006-26（参考文献） |
| **丝杠螺距** | P = 0.4 mm，d₂ = 1.74 mm | 参考文献 |
| **减速比** | 1:4（电机输出 : 丝杠） | 参考文献 |
| **PCB设计工具** | EasyEDA（LCEDA）+ Altium Designer | 本项目 |
| **CAD软件** | SolidWorks（原生 .SLDPRT/.SLDASM） | 本项目 |
| **制造工艺** | FDM 3D打印（框架）+ FR-4 PCB | 本项目 |

---

## 许可证

基于 [MIT许可证](LICENSE.md) 发布。

---

## 致谢

- **参考文献** — 《仿生蝙蝠飞行器的设计制造》（齐津浩，张卫平，2020），发表于《机械设计与研究》第36卷第5期第38–43页，上海交通大学。该论文为本项目的机械设计提供了蝙蝠飞行运动学、四连杆机构设计、剪式机构力学分析和样机验证方法的理论基础。
- **命名灵感** — 动画《成龙历险记》（*Jackie Chan Adventures*）中的角色**西木**（Hsi Wu，天之恶魔），其标志性的蝙蝠形态赋予了本项目名称以及对分段翼膜仿生学的致敬。
- **工具** — SolidWorks（机械CAD）、EasyEDA / LCEDA（PCB设计）、Altium Designer（原理图绘制）、Tektronix TDS1001B-SC（测试测量）。
- **灵感来源** — FESTO BionicOpter 和 Bat Bot B2（Caltech/UIUC）验证了仿生蝙蝠飞行的可行性；蝙蝠飞行生物力学文献（Swartz 等、Tian 等、Wolf 等）提供了翼膜力学和扑动运动学方面的研究成果。