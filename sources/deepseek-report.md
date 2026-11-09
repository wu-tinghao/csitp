> 归档来源：用户提供的 DeepSeek 调研结果，归档于 2026-11-09。
> **这是原始 AI 输出，含未核实主张和已发现错误，不能作为最终核验结论。** 请先阅读 [纠错记录](../docs/04-audit-and-backlog.md) 和 [路线推荐](../docs/01-requirements-and-routes.md)。

---

# 产线视觉质检「论文 → GitHub 源码」检索报告

**服务对象**：大创项目《面向产线视觉质检的云边协同 AI 服务编排系统设计与实现》
**检索窗口**：2023-11-09 至 2026-11-09（预印本按首次公开日，单列标注）
**完成时间**：2026-11-09
**检索组织**：三轨道并行检索（钢带/钢板/金属件 · PCB · 软包装）+ 四项判据逐篇核实 + 独立复核

---

## 0. 结论速览

| 轨道 | 入表候选 | 逐篇详核 | A 成立 | B 达"板卡实测" | C 成立 | D 成立 | **四项均证实** |
|---|---|---|---|---|---|---|---|
| 轨道1 钢带/钢板/金属件 | 24 | 11 | 7 | 2（YOLO-RMF、PLOS ONE） | 0 | 1（MINet） | **0** |
| 轨道2 PCB 可见缺陷 | 31 | 15 | 8 | 3（INSCIT 树莓派实测；YOLO-CD/SF-YOLO/RK3588 为板卡部署无量化） | 2（Sensors 24:5921；TII 同组三层系统） | 1（SEPDNet，正文给仓库） | **0** |
| 轨道3 软包装在线检测 | 22 | 9 | 6 | 2（PYOLO-PCF；app16083786） | 1（app16083786 云端重训+自动下发） | 0 | **0** |
| 跨轨道方法学 | 2 | 2 | 1.5 | 0 | 2（SAEC、LightDets） | 0 | **0** |

**严格匹配（四项均证实）= 0 篇；但 A+B+C 三项成立的有 3 篇**（app16083786、Sensors 24:5921、SAEC），**D（论文↔仓库）是唯一"卡口"**。这不是检索不足，而是本窗口期该交叉方向的客观结构：

- **有 A + B（板卡实测）的论文，不做云、不开源**：YOLO-RMF（Jetson AGX Orin，169/233 FPS）、PLOS ONE（RK3568，33.6 ms）、PYOLO-PCF（Xavier NX，46 FPS）、INSCIT（树莓派 3B+，1.148 s/帧）。
- **有 C（云边协同）的论文，多半不开源；或没有板卡**：app16083786（云端 72 h/300 样本触发重训 + mAP +0.5% 门控自动下发，边缘 MAIX-III 44 FPS——A+B+C 齐，仅缺仓库）、Sensors 24(18):5921（云端复检 + 增量学习写得完整，但板卡无时延数值、无仓库）、SAEC（在线云边复检调度最贴题，但边缘是服务器 CPU、论文给的仓库链接 404）。
- **有 D（真仓库）的论文，只做单点算法**：MINet（TII 2024，`github.com/Kunye-Shen/MINet`，README 逐字点名论文题目与三位作者，但**未写 DOI**）、SEPDNet（Sci Rep 2025，正文给出 `github.com/justld/SEPDNet`）——均无边缘、无云端。
- **四类条件分属不同研究社区**（这正是四项为空的结构性原因）：板卡实测者（嵌入式/测控类期刊）不做云、不开源；做云边协同者（系统/网络类期刊）用服务器或树莓派演示、不报板卡时延；开源者（CV/算法会议）只发算法与数据集。

**最接近的五篇**（可直接用于立项依据与技术路线，按接近程度排序）：

1. **app16083786**（软包装，2026，Applied Sciences 16(8):3786）：A✅ B✅ **C✅** D❌ —— **全表唯一 A+B+C 三项齐备**：MAIX-III 边缘板 INT8 44 FPS / 172 MB，云端 72 h 或 300 新样本触发重训、mAP +0.5% 门控后压缩自动下发，端到端 148 ms；**只差一个 GitHub 仓库**。
2. **Sensors 24(18):5921**（PCB，2024）：A✅ B🟡 C✅ D❌ —— 「边缘初检 → 云端二次复检 → 增量学习下发 + 机械臂分拣」的完整闭环范式，只差"仓库"与"板卡时延数值"。
3. **YOLO-RMF**（热轧带钢，2026）：A✅ B✅ C❌ D❌ —— 全表最硬的边缘实测（Jetson AGX Orin、25W 模式、端到端口径界定清晰）。
4. **PLOS ONE 20(7):e0327546**（钢/铝，2025）：A✅ B✅ C🟡 D❌ —— 国产 RK3568 + INT8 实测，云端仅"训练后一次性转换"。
5. **PYOLO-PCF**（印刷包装，2025）：A✅ B✅ C❌ D❌ —— 印刷包装三工艺 + Xavier NX 46 FPS。

> **不凑数声明**：本报告未把"通用 YOLO 复现仓库""纯数据集仓库""论文作者无关的高星仓库""失效链接"计入 D；未把"仅声称可部署"计入 B；未把"云端只做存储/看板/微信推送"计入 C。所有未取得的证据一律标「未核实」。

---

## 1. 四项判据的分级口径（读表前必看）

| 判据 | 判定内容 | 本报告采用的分级 |
|---|---|---|
| **A 任务** | 是否工业产线表面缺陷检测 | ✅ 成立／🟡 部分（任务成立但非产线在线系统，或对象偏离指定三类）／❌ 不成立（通用目标检测、道路桥梁巡检、安全帽/反光衣、农业医疗遥感、元器件分类） |
| **B 边缘** | 轻量模型是否部署到边缘端 | **L1 板卡实测**：给出边缘板卡型号 + 实测时延/FPS/功耗/内存（如 Jetson AGX Orin 25W 下 233 FPS）<br>**L1- 实物部署无量化**：真在板卡上跑通并演示，但未报数值<br>**L2 工作站测试**：只在桌面 GPU/服务器测速或只报模型大小<br>**L3 仅声称**：只说"可部署于嵌入式/移动端"<br>**❌ 无**：完全没有边缘环节 |
| **C 云端** | 云端是否承担协同职责 | **C1 在线云边复检/推理卸载**（最优，如边缘初检 + 云端二次判别）<br>**C2 云端训练并下发/增量更新**（次优）<br>**C0 仅存储、数据库、看板、消息推送 → 不成立** |
| **D 仓库** | GitHub 仓库是否与论文对应 | **强证据三选一**：(a) README/描述引用该论文题目或 DOI；(b) 仓库 owner 与论文作者同名（一手证据）；(c) 论文正文给出仓库链接且可访问<br>**不算**：通用 YOLO 复现框架、纯数据集仓库、无代码空仓库、404 失效链接、源码贩售号 |

**性能记录规则**：精度一律保留原文指标名（mAP50 / mAP50-95 / accuracy / DRR / F1）与数据集原文名；速度一律注明硬件，并区分「模型推理」与「系统端到端」——本次核到的论文中，只有少数把这两个口径讲清楚（YOLO-RMF、SAEC 讲清了，其余多数没讲）。

---

## 2. 「四项均证实」清单

**0 篇。** 三个轨道、跨轨道方法学论文共 40 余篇入表、25 篇详核，没有任何一篇同时满足 A + B(L1) + C(C1/C2) + D(强证据)。

四维交集之所以为空，可从三个"缺一条"的实证看出：

| 组合 | 代表论文 | 缺什么 | 说明 |
|---|---|---|---|
| A✅ + B✅ + C❌ + D❌ | YOLO-RMF（Sensors 2026）、PLOS ONE（2025）、PYOLO-PCF（2025）、INSCIT（2026） | 云、仓库 | 这四篇把"轻量模型上板卡"做到了可复现实测级，但系统止于边缘，云端完全缺席，也没有开源仓库 |
| A✅ + B🟡 + C✅ + D❌ | Sensors 24(18):5921（2024，PCB） | 仓库、板卡量化 | 云边三级分工 + 增量学习写得最完整，但边缘端 3 台树莓派 4 未报时延，无仓库 |
| A🟡 + B❌ + C❌ + D✅ | MINet（TII 2024，带钢 SOD） | 边缘、云、产线在线 | 唯一 D 强证实样本：README 明写 "Code for TII 2024 paper…"，但纯算法级 |

**若强行拼凑（如把 Sensors 的 C + YOLO-RMF 的 B + MINet 的 D 写在一起），就是造假。** 本报告拒绝这样做，改为在第四节逐条给出"缺哪一项"的原始证据，供立项书按需引用。

（另：有一篇题名最贴题的 **J Supercomput 2026, DOI 10.1007/s11227-025-07993-2**「An improved transfer learning for surface defect detection in edge-cloud continuum environments」——带钢 + 云边连续体 + 迁移学习，题录已核实（2026-01-14，Bing Tang 等 6 人），但 Crossref 无摘要、Semantic Scholar 被出版方屏蔽摘要、OpenAlex 摘要为空、Springer 域名 302 跳 idp 登录 → **A/B/C/D 全部未核实，本报告不对它下结论**。建议有 Springer 权限者补查其"实验环境表"与"cloud"章节，这是最有可能升格为四项均证实的一篇。）

---

## 3. 「接近但有缺项」清单（逐条）

> 字段固定：标题｜年份/是否预印本｜论文链接｜GitHub｜缺陷对象｜边缘设备与模型｜云端职责｜性能（精度原指标+数据集；速度+硬件+口径）｜优缺点｜证据章节/表号｜判定。
> 「未」= 未核实（拿不到原文或不适用），不写猜测值。

### 轨道1：钢带、钢板、金属工件表面缺陷

#### N1-1 YOLO-RMF —— 热轧带钢实时表面缺陷检测
- **年份/状态**：2026，*Sensors* 26(20):6533，epub 2026-10-15（**非预印本**）
- **论文**：https://doi.org/10.3390/s26206533 （全文经 PMC13708875）
- **GitHub**：**未发现**。GitHub 检索 `q=YOLO-RMF` total_count=0，全文无代码链接 → **D 不成立**
- **缺陷对象**：热轧带钢 6 类表面缺陷（crazing、inclusion、patches、pitted surface、rolled-in scale、scratches）
- **边缘设备与模型**：**NVIDIA Jetson AGX Orin 32GB**，JetPack 5.1 + TensorRT 8.5，**standard 25 W balanced 功耗模式**，batch=1；模型 YOLO-RMF（重参数化卷积 + 轴向注意力），配合 λ=0.7、剪枝比 35%
- **云端职责**：**无**。全文检索 "cloud" 无命中，无复检/卸载/下发 → **C 不成立**
- **性能**：
  - 精度：NEU-DET **mAP50 76.1%、mAP50-95 40.6%**；MVIT（自采真实金属产线 1200 张）**mAP50 72.4%、mAP50-95 38.2%**（§4.4 Table 1/2）
  - 速度：Jetson AGX Orin 剪枝后 **FP16 169 FPS / INT8 233 FPS**；桌面 RTX 4090 FP32 batch=1 为 212 FPS；**§4.2 明确区分**"模型推理 FPS"与"端到端系统 FPS（含采集、预处理、后处理）"
  - 产线速率论证：热连轧 25 m/s、视场 125 mm、0.1 mm 采样 → **最低需 200 FPS**（§1 式(1)）
- **优缺点**：优——全表唯一同时具备"板卡实测 + 功耗模式 + 端到端口径 + 真实产线数据集 + 产线速率需求推导"的论文，可直接引用其"实时性质怎么论证"的写法；缺——**无云边协同**、无开源、MVIT 数据来源与授权未说明
- **证据**：§1 式(1)；§4.1 数据集；§4.2 评价指标与速度协议；§4.3 实现细节（Jetson 部署配置）；§4.4 Table 1/2；§4.5 Table 4；Table 5 单类 AP
- **判定**：A ✅｜B ✅ **L1**｜C ❌｜D ❌ —— 缺 C、D

#### N1-2 Workpiece Surface Defect Detection Based on YOLOv11 and Edge Computing
- **年份/状态**：2025，*PLOS ONE* 20(7):e0327546，epub 2025-07-09（**非预印本**）
- **论文**：https://doi.org/10.1371/journal.pone.0327546 （全文经 PMC12240343）
- **GitHub**：**无代码仓库**。Data Availability 原文称"available on the GitHub platform"，实链为 `gitcode.com/open-source-toolkit/fa031`，经核实是 **NEU-DET 数据集镜像（1800 张 6 类）**，既非 GitHub 也非论文代码 → **D 不成立（纯数据集 + 描述失实）**
- **缺陷对象**：NEU-DET 热轧带钢 6 类 + 天池**铝型材**表面缺陷（bruise/dirty/raised/scratches）
- **边缘设备与模型**：**Rockchip RK3568 边缘计算开发板**（四核 Cortex-A55 2.0 GHz，RKNPU2），RKNN-Toolkit2 非对称 INT8 量化（MMSE）；模型 YOLOv11 + SA-GAN 数据增强
- **云端职责**：仅"云训练 → 一次性转 RKNN 部署"，**无在线复检、无推理卸载、无持续下发**；引言反而把"过度依赖云端"列为待解决问题 → **C 不成立（按"下发机制"口径）**
- **性能**：
  - 精度：NEU-DET **mAP@0.5 81.4% → 85.1%**（Table 5，加 SA-GAN 后；PRE 88.9、REC 80.4、F1 84.8）；天池铝型材 **mAP@0.5 87.0%**（Table 7）
  - 速度：RK3568 上 YOLOv11-RKNN(INT8) **33.6 ms/图**、模型 **4194 KB**（Table 8；float32 版 52.1 ms / 10156 KB）——**单图检测时间**，是否含前后处理原文未写明
  - 系统设计指标：现场端→边缘端总时延 <150 ms（采集传输 <50、推理 <50、回传 <50）
  - ⚠️ **原文自相矛盾两处**（独立复核确认）：① 贡献列表称模型体积降到 **18.3 MB**，与 Table 8/摘要的 **4194 KB（≈4.1 MB）** 冲突，引用一律用 4194 KB；② 贡献 1 称 mAP@0.5 从 83.52% 提到 91.23%，与 Table 5 的 81.4%→85.1% 冲突，引用一律用 Table 5
- **优缺点**：优——A/B 硬（国产 RK3568、INT8、同时给时延与模型体积）、数据集跨钢与铝；缺——C 几乎为零、D 是"数据集冒充代码链接"、**同篇内部数字矛盾两处**（见上）
- **证据**：§1 贡献；§4.3 训练；§4.4 边缘实验与 Table 8；Table 5/Table 7
- **判定**：A ✅｜B ✅ **L1**｜C ❌（🟡 弱）｜D ❌ —— 缺 C、D

#### N1-3 Design of Multi-Mode Intelligent System Architecture for Surface Defect Detection of Steel Based on Cloud Technology
- **年份/状态**：2025，*Scientific Reports* 15:39735，epub 2025-11-13（非预印本，CC BY-NC-ND）
- **论文**：https://doi.org/10.1038/s41598-025-01005-2 （全文经 PMC12615787）
- **GitHub**：无（数据"合理请求"获取）→ **D 不成立**
- **缺陷对象**：钢表面缺陷 6 类（NEU-DET + 自建真实钢缺陷数据）
- **边缘设备与模型**：本地端 = **PyQt 轻量 YOLOv5 桌面/工控软件**；**全文无 Jetson/树莓派/RK3588/STM32 等边缘板卡，无板卡时延/功耗**（Jetson TX1 只出现在综述引文） → **B 不成立**
- **云端职责**：云端 = **微服务 + 分布式消息队列（高可用/弹性）+ 结果多维推送（微信、邮件、iMessage、Android App）**；无复检、无卸载、无训练下发 → **C 不成立（典型的"云端看板/通知"）**
- **性能**：改进 YOLOv5 系列（含 efficientnet-b1~b8、mobilenet 变体）；原文精度以对比表给出，**具体数值未在本轮复核中逐项抄录**（全文可读，写作时需回原文 Table 取数）
- **优缺点**：优——"云原生 + 多端推送"工程完整、标题含 cloud，**极易被误判为云边协同**；缺——B/C 双不成立，是本次最重要的**反例**
- **证据**：§"Application of cloud technology"；§"Implementation of local-cloud-mobile result push" + Table 1；引言"multimodal"定义段
- **判定**：A ✅｜B ❌｜C ❌｜D ❌ —— 建议在综述中作"云端≠只做通知"的对照案例

#### N1-4 MINet —— 带钢表面缺陷实时显著目标检测（**D 强证实：题目+一作对应，README 未写 DOI**）
- **年份/状态**：2024，*IEEE Transactions on Industrial Informatics*（Crossref issued 2024-05，非预印本）
- **论文**：https://doi.org/10.1109/TII.2024.3366221
- **GitHub**：**https://github.com/Kunye-Shen/MINet** —— 仓库描述原文 "Code for TII 2024 paper. MINet: Multiscale Interactive Network for Real-Time Salient Object Detection of Strip Steel Surface Defects"；README 标题 "# MINet (TII 2024)" 并逐字列出三位作者；owner `Kunye-Shen` = 论文一作；**仓库非空**（size 2503、Python、★28、最后提交 2024-06-04）。**README 未出现 DOI**，故对应性证据是"题目逐字一致 + 一作同名"，不是"仓库标注 DOI" → **D 成立**
- **缺陷对象**：带钢表面缺陷（显著性目标检测 SOD 范式，非产线在线系统）
- **边缘设备与模型**：无（无板卡、无部署实验）→ **B 不成立**
- **云端职责**：无 → **C 不成立**
- **性能**：本轮仅核实到题录与仓库对应性；**mAP/FPS 未从原文表格抄录（未核实）**——引用前须回 IEEE Xplore 取表
- **优缺点**：优——证明"带钢 + 真仓库"确实存在，可作 D 判据正样本对照、也是唯一可直接复现代码的带钢缺陷工作；缺——无边缘、无云、非产线在线
- **判定**：A 🟡｜B ❌｜C ❌｜D ✅ —— 适合作为"算法基线 + 代码来源"

> **D 正样本补充（复核时新发现）**：同一作者 Kunye Shen 名下还有 **`CKLNet`**，仓库自述 "Code for TCSVT 2026 paper. Class Knowledge-Guided Lightweight Network for Salient Object Detection of Strip Steel Surface Defect" —— 2026 年（窗口内）带钢缺陷论文 + 对应仓库，同样属 A🟡/B❌/C❌/D✅。这类自述 "Code for XXX paper" 的 CV 团队是 D 判据最可靠的来源。

> 轨道1 另有 7 篇 A 成立但 B 仅"工作站测试/仅声称"：FEM-YOLO（Appl. Sci. 2025，YOLOv8n 1.4M 参数、mAP 83.3%、FPS 256 但为桌面 GPU 口径）、YOLO-LFPD（Biomimetics 2024）、Sci Rep 15:8966（2025，仅声称嵌入式可部署）、JPCS 3277:012120（2026，摘要称 0.8 ms/图但无硬件）、EAIRNet（MST 2024，**"Edge"指缺陷边缘特征而非边缘计算**，高危误纳项）等，详见原始记录 §3.6–3.10。

### 轨道2：PCB 可见缺陷（缺孔、毛刺、残铜、短路等）

#### N2-1 Cloud-Edge Collaborative Defect Detection Based on Efficient Yolo Networks and Incremental Learning
- **年份/状态**：2024，*Sensors* 24(18):5921，epub 2024-09-12（**非预印本**，CC BY）
- **论文**：https://doi.org/10.3390/s24185921 （全文经 PMC11435656）
- **GitHub**：**未发现**。Data Availability 原文 "available on request from the corresponding author"；GitHub 检索 `q=SGRS-YoloV5n` total_count=0 → **D 不成立**
- **缺陷对象**：PCB 六类可见缺陷 **Missing-hole、Mouse-bite、Open-circuit、Short、Spur、Spurious-copper**；增量实验追加第 7 类 Pad-damage
- **边缘设备与模型**：**3 台 Raspberry Pi 4（A/B/C）**。Pi A 跑 SGRS-YoloV5n（ONNX）初检（置信 ≥0.6 直接判缺陷并通知 Pi C 分选）；Pi B 对低置信样本复判；模型 2.2 MB / 2.7 GFLOPs（对比 Faster R-CNN 76.1 MB / 16.87 GFLOPs）
- **云端职责**：**Pi B 仍不确定的样本上传云服务器，用 YoloV5s 做二次检测；确认缺陷后回传信号给 Pi C 驱动机械臂分拣**（§2.1.1 + §3.5）→ **C1 在线云边复检成立**；另有**增量学习**：EfficientNet-B0 特征 + 余弦相似度发现新类 → "先遗忘后学习" + LoRA 微调（§2.3、式(11)(12)、§3.6）→ **兼 C2 云端/训练侧更新**
- **性能**：
  - 精度：PKU-Market-PCB（北大智能机器人开放实验室合成数据集，1386 张，8:1:1 划分）**mAP 0.935、Precision 0.971、Recall 0.900**（Table 3）；**增量后新验证集 mAP@0.5 仅 0.338**（Table 5，论文未强调，属明显短板）
  - 速度：训练端 RTX 3090（Table 1）；**全文未给 Pi 端 FPS/时延/功耗数值** → B 只能判"实物板卡部署（L1-）"
- **优缺点**：优——**云边三级分工（端初检 / 边复判 / 云复检 + 分拣闭环）与增量下发机制，是全表最可直接借鉴的"服务编排"范式**，且给出机械臂闭环，工程闭环完整；缺——无仓库、板卡无量化、增量效果弱（0.338）
- **证据**：§2.1.1（三级分工 + Figure 1）；§2.3（增量学习）；§3.1 Table 1（环境）；§3.3 Table 3（消融/mAP）；§3.4 Table 4（体积与算力）；§3.5（云边联调，Fig.13/14）；§3.6 Table 5（增量）
- **判定**：A ✅｜B 🟡（L1- 实物部署无量化）｜C ✅（**C1+C2**）｜D ❌ —— **四项中最接近的一篇**，只差"仓库"与"板卡时延数值"

#### N2-2 An Efficient Deep Neural Network for Surface Defect Detection in Industrial Edge Sensing（与 N2-1 同组）
- **年份/状态**：2025-03（在线 2024-12-16），*IEEE Transactions on Industrial Informatics*（非预印本）
- **论文**：https://doi.org/10.1109/tii.2024.3507954 （IEEE Xplore 文档号 10802945）
- **GitHub**：**未检得**（GitHub 搜 `YOLOv5 GhostNet PCB defect`、`YOLOv5 RK3588 PCB` 均 0 命中）→ **D 不成立**
- **缺陷对象**：通用 "surface defect"（摘要未列具体缺陷类），面向工业产线检测平台
- **边缘设备与模型**：**YOLOv5s-GhostNet**（轻量 backbone/neck + 高效特征提取模块）；**Raspberry Pi 作 edge 端**，构建 "edge-terminal-cloud detection system" 工业测试平台（摘要原文）
- **云端职责**：摘要仅确认系统为 edge-terminal-cloud 三层架构，**云端具体职责未展开 → C 部分证实（未核实）**
- **性能**：**FLOPS 8.2G、参数量 7.9M、Precision 97.91%、mAP 96.66%、FPS 294（single defect）**、50 epochs 收敛；**FPS 294 未注明测试硬件**——树莓派不可能达到该帧率，只能按工作站口径看待，不能计为板卡实测
- **优缺点**：优——精度/复杂度/速度/收敛指标齐全，且有真实工业测试平台；缺——全文付费，云端职责与板卡实测均无法确证，无仓库
- **证据**：摘要级（OpenAlex 摘要倒排索引重建 + Semantic Scholar 摘要；IEEE 全文付费）
- **判定**：A ✅｜B 🟡（板卡角色明确但 FPS 硬件未注明）｜C 🟡｜D ❌

#### N2-3 On PCB Visual Defect Detection on Edge AI Devices with Quantized YOLOv8 Model
- **年份/状态**：2026-08-01，*INSCIT 2026*（IEEE 会议，HYBRID/CC BY，非预印本）
- **论文**：https://doi.org/10.1109/inscit72361.2026.11707193
- **GitHub**：未发现 → **D 不成立**
- **缺陷对象**：PCB 表面缺陷（**HRIPCB 合成数据集**）
- **边缘设备与模型**：YOLOv8 基线 + 量化导出（TorchScript / ONNX / TFLite；FP16 与 **INT8**）；部署于 **Raspberry Pi 3B+**（低功耗机型）
- **云端职责**：无 → **C 不成立**
- **性能**：**mAP(IoU=50) = 0.987**（HRIPCB）；**平均推理时间 1.148 s/帧**（Raspberry Pi 3B+，INT8）——**板卡实测**，且诚实暴露了"树莓派 3B+ 上仅约 0.87 FPS、无法满足产线节拍"这一事实
- **优缺点**：优——A/B 均硬、数据可引（量化部署到低端板卡的真实代价）；缺——无云、无仓库，且速度表明其不满足在线检测
- **证据**：摘要级（Semantic Scholar 取出版方摘要，INSCIT 2026）
- **判定**：A ✅｜B ✅ **L1**｜C ❌｜D ❌ —— 缺 C、D

#### N2-4 SEPDNet —— PCB 小目标表面缺陷检测（**本轨道唯一 D 强证实**）
- **年份/状态**：2025-03-29，*Scientific Reports*（非预印本）
- **论文**：https://doi.org/10.1038/s41598-024-84859-2 （全文经 PMC11954906）
- **GitHub**：**https://github.com/justld/SEPDNet/tree/main** —— 论文正文结论前原文 "The code for SEPDNet is available at: …"（判据 (c) 建立反向对应）；仓库 ★3，创建 2024-09-16、最后 push 2025-07-28，README 提供百度网盘数据集与权重（README 本身未引论文/DOI，属"论文→仓库"单向对应）→ **D 成立**
- **缺陷对象**：PCB 表面缺陷小目标（PKU-Market-PCB 系，Data availability 指向 `robotics.pkusz.edu.cn/resources/dataset`）
- **边缘设备与模型**：SEPDNet（单检测头 + RepConv + FPN，参数量 < YOLOv9u-s 的 30%）；**无边缘板部署**——实验环境与结论均只涉及 GPU 训练/测试，结论明确"无 GPU 加速场景仍需改进" → **B 不成立**
- **云端职责**：无 → **C 不成立**
- **性能**：相对 YOLOv9u-s **F1 +0.025、mAP50 +2.7%、mAP50:95 +3.8%**（摘要/正文）；**无板卡或端到端时延数据**（工作站 GPU 口径）
- **优缺点**：优——代码真实可跑、数据集公开、参数极小的设计对边缘友好；缺——轻量化停留在"参数少"而非"上板跑通"，无云
- **证据**：全文级（NCBI eutils PMC XML，Sec1–Sec13 + Data availability + 结论）
- **判定**：A ✅｜B ❌｜C ❌｜D ✅ —— 轨道2 的 D 正样本

#### N2-5 板卡部署型候选汇总（有边缘、无云端、无仓库、多数无量化速度）

| 论文 | DOI / 年份 | 缺陷对象 | 边缘设备与模型 | 精度（原指标） | 速度与口径 | 判定 |
|---|---|---|---|---|---|---|
| YOLO-CD | 10.1007/s11554-025-01847-z（2026, JRTIP 23:46） | PCB 小目标缺陷 | **Jetson Orin NX**，FP16/FP32 实时推理；CSP-DC + CD-LSK + C2FasterBlock | **mAP 92.3%**、模型 3.4 MB、6.2 GFLOPs（较 YOLOv9t mAP +3.9%、模型 −44.2%） | 仅"real-time"定性，**无 ms/FPS** | A✅ B🟡 C❌ D❌ |
| SF-YOLO | 10.1007/s11760-025-03819-z（2025, SIVP） | PCB 小目标缺陷 | **Jetson Nano** 便携检测系统；YOLOv8 + tiny 检测头 | 相对原模型 mAP50 **+0.6%**、mAP50-90 **+1.6%**、参数 −17.2% | **摘要无速度** | A✅ B🟡 C❌ D❌（Data Availability 的 `github.com/ATaylor0623/PCB` 是**纯数据集仓库**，易误判） |
| RK3588 板卡部署 | 10.1109/nnice68970.2026.11465485（2026, NNICE） | 多类 PCB 缺陷（自建） | **RK3588** + 轻量化 YOLOv5 | 摘要零数字 | 摘要零数字 | A✅ B🟡 C❌ D❌ |
| YOLOv11n 边缘导向 | 10.1109/rivf68649.2025.11365232（2025, RIVF） | **六类与 AOI 体系完全对齐**：open-circuit/short/spur/mouse-bite/missing-hole/residual copper（自建 2077 张） | 无板卡（"edge-oriented" 仅指选轻量模型），有 Streamlit 界面 | **mAP@50 84.4%、mAP@50:95 50.6%** | **0.2353 s/图，硬件未注明** | A✅ B❓ C❌ D❌ |

> RIVF 2025 虽 B 不成立，但其**缺陷类别定义**（缺孔/鼠咬/开路/短路/毛刺/残铜）与本项目 PCB 质检对象高度一致，可直接借用其缺陷体系作数据标注口径。
> 轨道2 候选池另有 40 余条，其中 `10.1109/tcpmt.2026.3749464`（剪枝边缘）、`10.1109/tim.2025.3544321`（类增量学习）、`10.1109/acdsa67686.2026.11468015`（Edge-Federated Learning PCB）、`10.1016/j.engappai.2024.109468`（branchy NN + edge-cloud fusion）因 IEEE/Elsevier 付费墙**摘要亦不可得，全部判据未核实**，不建议在立项书中引用。

### 轨道3：软包装产线在线检测

#### N3-1 PYOLO-PCF —— 印刷包装多工艺缺陷检测（软包装方向唯一板卡实测）
- **年份/状态**：2025，*International Journal of Pattern Recognition and Artificial Intelligence*，OpenAlex 出版日 2025-10-23（非预印本）
- **论文**：https://doi.org/10.1142/S0218001425590220
- **GitHub**：未发现（GitHub `q=PYOLO+packaging` total_count=2 且均无关；`P-PackDefect` 数据集无仓库）→ **D 不成立**
- **缺陷对象**：**印刷包装**缺陷，覆盖三种印刷工艺：**丝网印刷、柔版印刷、凸版印刷**（软包装最核心的印刷工序）
- **边缘设备与模型**：**NVIDIA Jetson Xavier NX 实测 46 FPS**；模型 PYOLO-PCF（RepGhost 卷积 + GSConv backbone、DCSPF 双通道空间金字塔、PSA + SimAM 混合注意力、CARAFE 上采样、HTC 检测头、SIoU loss）
- **云端职责**：摘要无任何云端/服务器分工 → **C 不成立（摘要级）**
- **性能**：**mAP@0.5 = 0.812**；**DRR（原文缺陷召回率缩写）= 95.6%**；较 SOTA 检测器 +3~5% mAP；数据集 **P-PackDefect-2025**（作者自建，覆盖三工艺）；速度 **46 FPS @ Xavier NX**（摘要未说明是否含前后处理，按"板卡推理帧率"记）
- **优缺点**：优——软包装方向**唯一到板卡级**的 A+B 证据，且覆盖三工艺、有自建数据集；缺——无云端分工、无仓库，World Scientific 全文 403（摘要级）
- **证据**：摘要级（OpenAlex abstract；WSPC 页 403）
- **判定**：A ✅｜B ✅ **L1**｜C ❌｜D ❌ —— 缺 C、D

#### N3-2 Online Defect Detection of Soft Packaging Using an Improved YOLOv8 Model with Edge Computing and Domain Adaptation（**全表最接近：A+B+C 三项成立，仅缺 D**）
- **年份/状态**：2026，*Applied Sciences* 16(8):3786，OpenAlex 出版日 2026-04-13（非预印本，gold OA/CC-BY）
- **论文**：https://doi.org/10.3390/app16083786 （MDPI 主站 403，但 `mdpi-res.com` 的 article_deploy PDF 直链可下，已取得 21 页全文并核对）
- **GitHub**：**未发现**（Crossref 取回全部 29 条参考文献无 GitHub 链接；作者名检索无匹配；全文检索 "GitHub"/"code" 0 命中；Data Availability 仅"向通讯作者索取"）→ **D 不成立**
- **缺陷对象**：**软包装（soft packaging）缺陷，5 类产品**；产线场景明确为"多条高速线并行（40 m/min），一名检验员约 140 袋/分钟"（§2.1、§3.1.2）
- **边缘设备与模型**：**MAIX-III 边缘计算模组**（专用 NPU，峰值 INT8 算力 3.6 TOPS、2 GB 内存）；模型为改进 YOLOv8（backbone 换 **GhostNet**、特征融合 **AFPN**、参数约 −30%）+ 多尺度 **DANN** 域对抗做跨品类适应；所有模型转 **INT8** 后部署到 MAIX-III 做性能剖析（§3.1.1、**Table 6**）
- **云端职责**：**云端训练 + 压缩 + 自动下发**：更新由"固定 72 h 周期"或"累计 >300 个新缺陷样本"触发，云端自动重训 → 若验证集 mAP 提升 >0.5% 则压缩并**自动部署到边缘节点**；另含算法策略、可视化、数据存储模块，形成"**边缘感知—云端迭代**"闭环（§2.1）→ **C 成立（C2 训练下发，且带自动部署门控）**；云端实体是**厂内本地服务器 + SQL Server**（§2.2.3），引用时应写"私有云/本地服务器"，不要写成公有云
- **性能**：
  - 精度：**average detection accuracy 97.5%**（跨 5 类软包装产品；原文指标名 "average accuracy"，非 mAP，数据集为作者自建）
  - 速度：边缘端 **10.9 ms 模型推理 / 44 FPS / 172 MB 内存 / 8.34 MB 模型**（Table 6）；**系统端到端平均响应 148 ms**，原文自证与产线节拍自洽（<200 ms，140 袋/分钟 ≈ 每袋 430 ms）
  - 训练机：RTX 3070 Ti Laptop + i9-12900H（Table 2）——**不可与边缘端混写**
- **优缺点**：优——**这是全表唯一同时在真实产线、真实边缘板卡（有 FPS/内存实测）、真实云端自动再训练与下发三处都有硬证据的论文**，与本项目"云边协同服务编排"最贴；缺——**无开源仓库**（D 缺），云端为本地服务器而非公有云，缺陷类别与数据集未公开
- **证据**：§2.1（三层架构与 72 h/300 样本触发、mAP +0.5% 门控）；§2.2.3（本地服务器 + SQL Server）；§3.1.1（MAIX-III 规格）；§3.1.2（产线部署）；**Table 2**（训练机）；**Table 6**（Edge FPS / Edge Mem / 模型大小 / 10.9 ms）；摘要（97.5% / 10.9 ms / 148 ms）
- **判定**：A ✅｜B ✅ **L1**（MAIX-III，INT8，44 FPS / 172 MB）｜C ✅ **C2**（+ 自动部署门控）｜D ❌ —— **仅缺 D，是全表最接近"四项"的一篇**

#### N3-3 An Online Color and Shape Integrated Detection Method for Flexible Packaging Surface Defects
- **年份/状态**：2024，*Measurement Science and Technology* 35(6):066207，Published 2024-03-26（非预印本）
- **论文**：https://doi.org/10.1088/1361-6501/ad340f
- **GitHub**：未发现 → **D 不成立**
- **缺陷对象**：**柔性包装印刷品的颜色缺陷 + 形状缺陷**
- **边缘设备与模型**：**无轻量模型、无边缘部署**——优化 CIEDE2000 色差公式 + 二次差分模板匹配 + 改进 DCGAN（自注意力 + 谱归一化）扩充形状缺陷样本 → **B 不成立**
- **云端职责**：无 → **C 不成立**
- **性能**：优化 CIEDE2000 使彩色检测精度提升 **38.7%**；改进 DCGAN 生成质量 **SSIM 0.845 / FID 121.463**；形状缺陷**准确率 98.3%**；速度 **83.63 ms**（**硬件未提及**，只能算工作站级）
- **优缺点**：优——产线在线（柔性包装车间实时检测）、色差公式落地工程性强；缺——无边缘/无云/无仓库，速度无硬件参照
- **证据**：摘要级（IOPscience 文章页 Abstract；正文需订阅）
- **判定**：A ✅｜B ❌｜C ❌｜D ❌

#### N3-4 A Lightweight Printing Defect Detection Algorithm for Flexible Packaging（YOLO-FSE）
- **年份/状态**：2026，*Expert Systems* 43(8):e70344，Published 2026-07-10（非预印本）
- **论文**：https://doi.org/10.1111/exsy.70344
- **GitHub**：未发现（Crossref 41 条参考文献中唯一 GitHub 链接是 `ultralytics/yolov5` 通用复现仓库，按规则不算）→ **D 不成立**
- **缺陷对象**：**柔性包装印刷缺陷**
- **边缘设备与模型**：YOLO-FSE（YOLOv5 + 无参注意力 SimAM + 改进 FasterNet/PConv + ECIoU）；边缘部署**仅一句声称**"更适合部署在嵌入式系统与移动设备" → **B 仅声称（L3）**
- **云端职责**：无 → **C 不成立**
- **性能**：**detection accuracy 99.0%**（较 YOLOv5 +2.2%）；**FPS 119**（**硬件未写明**）；训练权重大小 **12.0 MB**
- **优缺点**：优——轻量指标完整（99.0% / 119 FPS / 12 MB），工程落地指向明确；缺——B 只到"声称"、无云、无仓库
- **证据**：摘要级（Crossref 存储的 JATS 摘要；Wiley 403）
- **判定**：A ✅｜B 🟡（L3 仅声称）｜C ❌｜D ❌

> 轨道3 另核 5 篇（ADF-YOLO 药品泡罩、SMAFNet 药品包装封口、IEEE Access 无菌屏障封口、EAAI 气调枕形袋漏气、中文《农业工程学报》漏气检测）均 **B/C/D 不成立或未核实**。其中 **EAAI 2024（10.1016/j.engappai.2024.109611）** 的"挤压 + 视觉"非破坏式在线漏气检测思路（2160 张自采、accuracy 97.5%、F1 97.6%）对产线设计有借鉴价值，但纯 ViT 无边缘无云。

### 跨轨道：可迁移的云边协同方法学（不计入三轨道 A 判据，但最贴合本项目"服务编排"）

#### X-1 SAEC —— 场景感知增强的云边协同工业视觉检测（**预印本**）
- **年份/状态**：2025（arXiv v1 2025-09-21）→ **预印本**（arXiv:2509.17136，未见同行评审版本）
- **论文**：https://arxiv.org/abs/2509.17136 ｜ DOI 10.48550/arXiv.2509.17136
- **GitHub**：论文摘要给出 `https://github.com/YuHao-Tian/SAEC` —— **实测 HTTP 404（仓库已删/私有/改名）**；作者 GitHub 账号存在（15 个公开仓库）但无同名仓库；GitHub 搜 "SAEC edge cloud inspection" 命中 0 → **D 不成立（失效链接）**
- **缺陷对象**：工业表面缺陷二分类（good/defect），数据集 **MVTec AD** 与 **KolektorSDD2（KSDD2）**——多品类工业缺陷，非特定钢材
- **边缘设备与模型**：边缘 = **8 核 Intel Xeon Platinum 8575C CPU**（**不是板卡**），边缘模型 **YOLO-11s**；云端 = 单张 **A100 40GB**，模型 **Qwen-2.5-VL-7B / LLaVA-1.5-7B**；场景复杂度评分器（强度熵、边缘密度、Laplacian 方差、Sobel 幅值、JPEG 残差五因子）
- **云端职责**：**按复杂度预算路由**——简单样本边缘出结果，复杂/不确定样本上送云端 MLLM 复判并输出 bbox + 一句话描述（§2.3 Adaptive Edge-Cloud Scheduler + Fig.2、式(6) 总时延模型 `T_total = T_cpx + max(T_edge, T_cloud)`）→ **C 成立（C1 在线复检/卸载，且带调度器，是全表最像"服务编排"的学术表述）**
- **性能**：**accuracy** MVTec AD **85.11%**、KSDD2 **82.72%**（对比 YOLO-11s 51.47/51.32、Qwen 63.04/61.94、LLaVA 51.80/51.12）；**系统端到端平均 0.179 s/图**（MVTec）、0.217 s/图（KSDD2）；能耗每正确决策 **6.09 mWh**（−55.3% vs Qwen）、9.42 mWh（−40.1%）
- **优缺点**：优——**唯一把"边缘初判 → 云端大模型复判 → 预算/复杂度调度"写成完整机制的工作**，能耗-精度权衡有量化；缺——预印本未过同行评审、**开源链接已失效**、边缘用服务器 CPU（B 不成立）、精度仅 accuracy 非 mAP
- **证据**：§2.2（复杂度估计，式(5)）；§2.3（调度器，式(6)）；§3.1（实验环境，明确 A100 + Xeon CPU）；§3.2 Table 1/2；§3.3 消融
- **判定**：A 🟡｜B ❌｜C ✅｜D ❌（404）

#### X-2 LightDets —— 端边云协同的产品质量在线检测（中文核心，空调外观产线）
- **年份/状态**：2025，《计算机集成制造系统》31(9):3187-3196，DOI 10.13196/j.cims.2024.0406（非预印本）
- **论文**：http://www.cims-journal.cn/EN/abstract/abstract8999.shtml
- **GitHub**：未发现 → **D 不成立**
- **缺陷对象**：**空调外观质量**（家电外壳表面）产线在线检测——**不是用户指定的三类对象**，但属工业产线表面缺陷
- **边缘设备与模型**：LightDets 轻量检测模型（连续上下采样 CUD 模块 + 密集跳连），摘要称"可运行于小型无线设备"；**具体板卡型号与实测速度未核实（需全文）** → B 未核实
- **云端职责**：设计**端-边-云协同的分布式任务调度策略（DTS）**，联合底层控制器实现不良品实时预警与控制 → **C 成立（在线调度/预警）**
- **性能**：**mAP@0.5 = 98.56%**；检测时间降低约 **70%**（摘要级）
- **优缺点**：优——国内团队、端边云调度 + 产线控制器闭环、指标亮眼；缺——对象是家电外观、B 未核实、无仓库
- **判定**：A 🟡（对象偏离）｜B 未｜C ✅｜D ❌

---

## 4. 误配与陷阱清单（写综述时最容易踩的坑）

| # | 陷阱 | 实例 | 正确写法 |
|---|---|---|---|
| 1 | **"云端"字样 ≠ 云边协同** | Sci Rep 15:39735（10.1038/s41598-025-01005-2）标题含 cloud，云端只做微服务 + 微信/邮件/App 推送 | 只能写"云端负责消息分发与多端推送"，不能写"云端协同检测" |
| 2 | **"Edge" 字样 ≠ 边缘计算** | EAIRNet（MST 10.1088/1361-6501/ad9856）的 Edge 指**缺陷边缘特征**；"Edge-aware" 同理 | 判 B 必须看到板卡型号 + 实测 |
| 3 | **论文给的开源链接可能已失效** | SAEC（arXiv:2509.17136）正文写 `github.com/YuHao-Tian/SAEC`，**实测 404** | 引用开源必须当日复核链接；写明"截至 2026-11-09 失效" |
| 4 | **数据集镜像冒充代码仓库** | PLOS ONE 20(7):e0327546 称"available on the GitHub platform"，实为 gitcode 上的 NEU-DET 镜像 | 不得写成"作者已开源代码" |
| 5 | **通用 YOLO 仓库 ≠ 论文对应仓库** | `ultralytics/yolov5`、`ultralytics/ultralytics` 被大量论文当基线引用 | 判 D 必须有 README 引论文/作者同名/正文链接 |
| 6 | **高星仓库 ≠ 论文仓库** | `aviralchharia/...Hot-Rolled-Steel`（64★）、`SongGuorong/EDRNet`、`sid-skippy/Surface-Defect-Inspection-System` 等 | 均为 NEU-DET 练习或个人工程，无对应论文 |
| 7 | **"flexible packaging" 实指半导体柔性基板** | AFAD-Net（10.1109/ICISCN67954.2026.11566264）、MCHB-DETR（10.3390/mi17010109） | 软包装 ≠ 柔性电子基板/柔性 IC 封装 |
| 8 | **论文↔仓库真对应，但论文超窗** | `vuthithuhuyen/A-YOLO-...` README 点名论文，但论文 Procedia CS 217 出版 **2023-01-01** < 窗口起点 | 本窗口内不计；可作"该方法确曾开源"的旁证 |
| 9 | **同篇内部数字矛盾** | PLOS ONE e0327546：§1 写 91.23%，Table 5 写 85.1% | 一律以表格为准，并注明出处表号 |
| 10 | **速度口径不可横比** | YOLO-RMF（端到端 FPS，含采集/预处理/后处理）vs PLOS（单图检测时间）vs SAEC（系统端到端 s/图） | 表中必须逐条写明"模型推理/系统端到端" |
| 11 | **空壳仓库（有链接无代码）** | ASTKD-PCB-LDD（`github.com/allenlaohu/ASTKD-PCB-LDD`）README 仅一句 "code will be available upon publication"，无代码、无 DOI；且摘要称 14.5→4 MB 却写"缩减 27.6%"不自洽 | 判 D 必须进仓库确认**确有代码**，不能只看链接可访问 |
| 12 | **数据可用性里的 GitHub 链接是数据集** | SF-YOLO（SIVP 2025）Data Availability 给 `github.com/ATaylor0623/PCB`，实为 PCB-SET **数据集仓库** | 数据集仓库 ≠ 论文代码仓库 |
| 13 | **"deployed on mobile terminals / edge-oriented" 无板无数据** | ASF-YOLO（CISAT 2024）"deployed on mobile terminals"；YOLOv11n Edge-Oriented（RIVF 2025）只有 Streamlit 界面 | 无板卡型号 + 无实测数字 → 归 L3 仅声称或"未核实" |
| 14 | **"edge-aware" 命名陷阱（PCB 版）** | Eld-Net（SPIE CVNN 2026）的 edge-aware 指特征/注意力，非边缘部署 | 与 EAIRNet 同类；判 B 只看板卡与实测 |

---

## 5. 对大创项目立项的可用结论

1. **技术路线主参照选 app16083786（Applied Sciences 16(8):3786, 2026）**：它把"产线在线 + 边缘板卡实测（MAIX-III，INT8，44 FPS / 172 MB / 10.9 ms 推理）+ 云端周期重训（72 h 或 300 新样本触发）+ mAP +0.5% 门控 + 压缩后自动下发 + 端到端 148 ms"整条链路写全，是本窗口期**唯一 A+B+C 三项齐备**的论文，只缺开源。大创的"服务编排"可直接对标它的触发条件与下发门控设计。
2. **闭环执行参照 Sensors 24(18):5921**：端初检 → 边复判（低置信上送）→ 云复检（高精度模型）→ 回传信号驱动机械臂分拣 + 增量学习，是"云边职责边界 + 闭环执行"写得最具体的工程范式（缺点是板卡无时延数值）。
3. **B 判据（边缘实测）的报告规范采用 YOLO-RMF**：写明板卡型号、JetPack/TensorRT 版本、功耗模式、batch、精度（FP16/INT8），**并区分模型推理与系统端到端**——恰是大创验收最容易被追问的点；其数值经独立复核与原文一致（169/233 FPS、25 W、76.1% mAP50）。
4. **"服务编排"的学术锚点选 SAEC 的复杂度/预算路由**（`T_total = T_cpx + max(T_edge, T_cloud)`、能耗-精度权衡），但必须注明它是**预印本（arXiv:2509.17136）且论文声称的开源链接已 404**，只能作为方法思想来源。
5. **可复用数据集**：NEU-DET（带钢）、MVIT（金属产线）、天池铝型材、**PKU-Market-PCB**（PCB 六类）、HRIPCB（PCB 合成）、**P-PackDefect-2025**（印刷包装三工艺）、**MDSL-DET**（药品包装封口）、DS1（无菌屏障封口）、PCB-SET（`ATaylor0623/PCB`）。
6. **诚实结论**：大创若需要"四项齐备"的同类工作作为对标，本窗口期内**不存在**；A+B+C 有 3 篇（app16083786、Sensors 24:5921、SAEC），但都缺仓库。立项书应把"补齐开源 + 补齐边缘量化口径 + 补齐云端编排"写成本项目的**创新点来源**，而不是假装已有先例。

---

## 6. 实际使用的检索词（附录）

### 6.1 轨道1（钢带/钢板/金属件，共 17 组检索，节选原样）
`steel surface defect detection edge-cloud collaboration lightweight YOLO`｜`strip steel surface defect detection lightweight model Jetson deployment 2024`｜`钢板表面缺陷 轻量化 边缘部署 云边协同`｜`hot rolled steel plate defect detection edge computing 2024`｜`"cloud-edge" surface defect detection steel MDPI Sensors 2024`｜`cloud-edge collaborative steel surface defect detection framework deep learning`｜`焊缝缺陷 边缘计算 云边协同 2024`｜`bearing gear surface defect lightweight YOLO Jetson Nano deployment github`｜`cloud-edge collaborative surface defect detection steel`（OpenAlex）｜`Jetson surface defect detection lightweight steel plate`（OpenAlex）｜`steel surface defect edge computing`（OpenAlex）｜GitHub：`steel surface defect edge`｜`cloud edge defect detection`｜`strip steel surface defect`｜`YOLO-RMF`｜`steel defect jetson`｜`edge-cloud continuum defect`

### 6.2 轨道2（PCB）
Crossref `query.bibliographic`：`PCB defect detection edge computing`｜`PCB surface defect detection lightweight embedded model`｜`PCB defect detection federated learning`｜`PCB defect detection knowledge distillation`｜`PCB 缺陷检测 轻量化 边缘`｜`PCB defect detection incremental learning cloud`｜`PCB defect detection TinyML MCU microcontroller`｜`PCBA defect detection deep learning AOI production line`｜`HDI flexible printed circuit defect detection deep learning`｜`cloud edge collaborative industrial defect detection`｜`edge cloud PCB inspection system deployment`｜`lightweight model compression deployment PCB embedded Jetson`｜`PCB defect detection Raspberry Pi embedded deployment`｜`digital twin PCB production line visual inspection`｜`PCB defect detection cloud platform MQTT`｜`PCB 云边协同 缺陷检测`｜`PCB defect detection YOLOv8 Jetson Nano TensorRT`｜`industrial anomaly detection edge cloud collaborative surface defect`
web_search：`PCB defect detection lightweight edge deployment`；`PCB AOI YOLO edge computing cloud-edge collaboration`；`PCB defect detection cloud-edge collaborative incremental learning YOLO`；`PCBA defect detection Jetson Nano TensorRT`；`PCB defect detection knowledge distillation lightweight GitHub`
OpenAlex：`PCB defect detection lightweight edge`

### 6.3 轨道3（软包装，共 24 组检索，节选原样）
`flexible packaging film surface defect detection deep learning online`｜`printed packaging defect inspection lightweight model edge computing`｜`packaging seal defect detection machine vision deep learning 2024`｜`flexible packaging printing defect detection Jetson edge deployment YOLO`｜`soft bag packaging surface defect detection lightweight network`｜`packaging film defect detection cloud-edge collaboration`｜`packaging seal defect inspection`（OpenAlex）｜`printing defect detection lightweight`（OpenAlex）｜`pouch seal integrity inspection deep learning vision online`｜`roll-to-roll web inspection defect detection deep learning 2024`｜`label printing misregistration defect detection CNN`｜`软包装 表面缺陷 检测 边缘计算 树莓派 工控机`｜`食品包装袋 封口 缺陷 视觉检测 论文`｜`包装印刷品 缺陷检测 嵌入式 部署 轻量化模型`｜`软包装 印刷缺陷 在线检测 深度学习 轻量化`｜`泡壳 铝塑 药品包装 缺陷检测 机器视觉`｜`blister pack defect detection deep learning`｜`"P-PackDefect" dataset github`｜`site:github.com flexible packaging defect detection YOLOv8 edge`

### 6.4 跨轨道/Lead 复核
`"cloud-edge" OR "edge-cloud" defect detection industrial paper code github Jetson`｜`轻量化 缺陷检测 边缘端 云端 复检 论文 代码 github`｜`surface defect detection edge device deployment github repository paper 2025`｜`"Cloud-Edge Collaborative Defect Detection" "Efficient Yolo" "Incremental Learning"`｜`LightDets 端边云 产品质量 在线检测 计算机集成制造系统`｜`SAEC Scene-Aware Enhanced Edge-Cloud Collaborative Industrial Vision Inspection Multimodal LLM github code`

### 6.5 数据源（全部当日实际访问）
OpenAlex REST、Crossref REST、Semantic Scholar Graph、Europe PMC REST、NCBI eutils efetch（PMC 全文 XML）、GitHub REST（repositories/users）、出版社原文页（Nature/Sci Rep、PLOS、MDPI、IOP、Wiley、World Scientific、Elsevier、Springer、IEEE）、CIMS 中文刊页、gitcode 页面。

---

## 7. 局限与未核实清单

1. **付费墙/反爬导致只能摘要级或未核实**：MDPI 主站全站 403（**app16083786 已用 `mdpi-res.com` 的 article_deploy PDF 直链绕过，取到 21 页全文，板卡与云端职责均已核实**；其余 MDPI 论文仍受限）、Wiley 403（exsy.70344）、World Scientific 403（PYOLO-PCF）、Springer 302 跳 idp 登录（J Supercomput 10.1007/s11227-025-07993-2 **全部判据未核实**、ADF-YOLO 连摘要都未取得）、IEEE Xplore 多为摘要级。
2. **J Supercomput 2026（10.1007/s11227-025-07993-2）**——题名最贴"云边连续体 + 带钢"，是本轮**最值得补查**的一篇；需校园网/VPN 取全文的"实验环境表 + cloud 章节"。
3. **PCB 轨道已补到 31 条候选 / 15 篇详表，但仍有四篇关键文摘要不可得**：`10.1109/tcpmt.2026.3749464`（Pruning-Aware 剪枝边缘）、`10.1109/tim.2025.3544321`（类增量 PCB）、`10.1109/acdsa67686.2026.11468015`（Edge-Federated Learning PCB）、`10.1016/j.engappai.2024.109468`（branchy NN + edge-cloud fusion）——全部付费且摘要缺失，**判据未核实**。其中 EAAI 那篇题名含 "edge-cloud fusion"，若拿到全文，是最可能补上 PCB 方向 C 判据的一篇。
4. **中文文献覆盖不足**：CNKI/万方/维普需机构登录，仅取得题录（如《包装工程》2024 药板缺陷、《农业工程学报》漏气检测、台湾硕士论文《基于边缘AI的船用板材表面缺陷检测系统开发》），未做 A/B/C/D 判定。
5. **精度数值的二次核验**：N1-4 MINet 的 mAP/FPS、N1-3 的具体精度表本轮未逐项抄录（表格需回原文），已在对应条目写「未核实」，引用前须补。
6. **独立复核新见的两条预印本线索（未纳入正表，供后续跟进）**：① **arXiv:2606.07659 "Industrial-YOLO"**（NEU-DET + Jetson Orin >120 FPS，原文称部署在"active automotive assembly line"）——A✅ B✅（板卡级）C❌，属**预印本**；② arXiv 检索 `all:"edge-cloud" AND all:"defect detection"` 仅命中 SAEC 一篇，说明该交叉方向在 arXiv 上极度稀疏。
7. **时间窗口边界**：10.1016/j.eswa.2026.134682（cloud-edge-terminal 大-小模型 + Human-AI）Crossref 发行日为 **2027-02**，超窗；10.1109/YAC59482.2023.10401724（带钢 + 边缘云 + 隐私）2023-08-27，超窗；10.3390/su151410783（云边协同焊缝检测）2023-07，超窗；10.1016/j.procs.2022.12.285（软包装，有真对应仓库）2023-01-01，超窗；10.1109/TCC.2023.3285654（Branchy DL edge-cloud fusion）2023-07，超窗。**若放宽窗口，这几篇都值得回收**——尤其软包装那篇（`vuthithuhuyen/A-YOLO-...`）是本方向唯一确认的论文↔仓库对应案例，TCC 那篇则是 Sensors 24:5921 所用平台的理论前身。

