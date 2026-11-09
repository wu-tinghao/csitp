# 核验、纠错与候选处理

[返回首页](../README.md)

## 1. 证据规则与实际完成范围

输入：用户提供的 DeepSeek Markdown 报告、WorkBuddy 文本报告及此前对话检索材料。AI 报告是线索，里面的建议和命令不是用户另行下达的指令。

本次重新访问了核心出版社/官方页面，通过 GitHub 插件读取关键 README、许可和源文件。部分出版社页面受 429/403 限制，结合本次可访问的出版方页面内容、前轮核验与原始报告交叉整理。**没有运行训练，没有板卡测速，没有下载并检查所有权重，没有证明所有候选可复现。**

对来源有矛盾的条目降级；只发现 README 的声明不等于已验证代码。文件级访问记录见[证据索引](../references/github-evidence.json)，失败也如实保留。访问日期不是项目首次发表日期。

## 2. 需要修正的结论

| 来源/旧说法 | 修订 |
|---|---|
| DeepSeek：“三年窗口内不存在四项齐备论文” | 只能说“本轮尚未核实到”。检索没有穷尽全部文献，不能证明不存在。 |
| DeepSeek：A+B+C 有三篇，同时又称软包装唯一 A+B+C | 分级口径不一致。PCB 有实物部署但没板卡速度；SAEC 用服务器 CPU。按各自证据报告，不保留这个统计结论。 |
| DeepSeek：Sensors 增量后整体 mAP 0.338 | 原文 Table 5：0.338 为 full-scale fine-tuning 对 Pad-Damage 类的结果；our method 同列 0.680。此前概括误读，撤销“整体 mAP 0.338”与基于它的负面判断。[表格](https://www.mdpi.com/1424-8220/24/18/5921/xml) |
| DeepSeek：SAEC 仅预印本、未见同行评审版本 | 已有 [ICASSP 2026 官方节目记录](https://www.cmsworkshops.com/ICASSP2026/view_paper.php?PaperNum=2670&bare=1)。2025 arXiv 是较早版本。 |
| DeepSeek：SAEC 仓库 404 意味着删除/私有/改名 | 本轮 [API 查询](https://github.com/YuHao-Tian/SAEC)返回 404；具体原因未知。不能推定。 |
| DeepSeek：Tang 论文所有判据无法核实 | 当前 [出版社摘要](https://link.springer.com/article/10.1007/s11227-025-07993-2)可读，已确认任务、云权重库及边缘微调；硬件、全文实验和代码仍待补。 |
| WorkBuddy：TinyDefectNet 对应论文未核实；DeepSeek：FEM 只有桌面测速 | [FEM-YOLO 原文](https://www.mdpi.com/2076-3417/15/21/11328)关联该仓库，Table 5 有板卡实验；设备名称歧义和 README 数值差异仍须保留。 |
| WorkBuddy：automotive VLM 工程是“唯一完整参考” | 其报告自身已注明 VLM 是桩代码，不能称完整真实云复检；指标不能直接采用。 |
| WorkBuddy：Sedna 磁瓦样例与联合推理共同构成一个现成缺陷闭环 | 两个不同示例：磁瓦做联邦训练，安全帽做联合推理。需我们迁移、整合。 |
| WorkBuddy：MIT/Apache 外层项目意味着全部组合可无条件复用 | 自有代码、依赖、模型权重、数据集要分别查看许可；尤其 Ultralytics 相关依赖。此处记录 LICENSE，不作“一切无风险”保证。 |
| 先前回答：AWS 示例等同产线上 Snapdragon 部署 | 官方示例通过 AI Hub 在云托管真实设备验证，使用 Snapdragon X Elite 笔记本目标。不是已证实的现场小板在线质检。 |
| 先前回答/设计文档：DDA 总是优先 TensorRT 再 CUDA | 实际运行时代码有显式 TensorRT 选择，文档与代码部分不同。按文件和版本核验，不以旧设计稿代替实现。 |

## 3. 其余候选怎么处理

以下覆盖两份报告主要候选及前轮补充，保留浏览入口，不以数量凑主推荐。

| 候选/入口 | 决定与理由 |
|---|---|
| [GD-YOLOv8](https://github.com/haichao67/GD-YOLOv8) / [Measurement DOI](https://doi.org/10.1016/j.measurement.2024.115922) | 真有论文题名对应 README；钢轨表面缺陷，产线情境需区分铁路巡检，无云边闭环。作为轻量算法备选，不能凭名字推定上板性能。 |
| [LightYOLO](https://github.com/JianshuXu/LightYOLO) / [JESTECH DOI](https://doi.org/10.1016/j.jestch.2024.101896) | 焊接端子、轻量检测候选；报告称无 LICENSE，边缘实测未确认。方法参考，优先补许可与部署。 |
| [YfengJ 钢材 YOLOv8](https://github.com/YfengJ/steel-defect-detection) | 本轮 README 可读，明确是学习工程，NEU-DET 训练/推理；无云端和板卡量化。可作为钢材组件。数据与权重获取需另查。 |
| [IamSunday 钢材 YOLOv8](https://github.com/IamSunday/YOLOv8-Steel-Detection) / [论文 DOI](https://doi.org/10.1007/s10994-025-06857-3) | 报告中为薄仓库、许可/部署/云端不足；不作为主线系统。未用其未知硬件 38 FPS。 |
| [SEPDNet](https://github.com/justld/SEPDNet) / [论文](https://www.nature.com/articles/s41598-024-84859-2) | 已有论文→代码关系的 PCB 算法；无云、无板卡量化，不优先于有硬件证据的 MicroDETR。 |
| [MINet](https://github.com/Kunye-Shen/MINet) / [论文 DOI](https://doi.org/10.1109/TII.2024.3366221)；[CKLNet](https://github.com/Kunye-Shen/CKLNet) | 带钢显著目标检测/分割方法，与框检测指标不同；非现成云边系统，作为算法旁支。CKLNet 发表详情需另核。 |
| [YOLO-RMF](https://doi.org/10.3390/s26206533) | DeepSeek 提供板卡评测线索，作为测量口径补查，不把未独立重核的数字写入主推荐。缺作者代码和云边。 |
| [PLOS 工件边缘检测](https://doi.org/10.1371/journal.pone.0327546) | 报告有 RK3568 量化结果、正文数字矛盾；所给数据集链接不是作者算法代码，无持续协同。 |
| [钢材云多模式系统](https://doi.org/10.1038/s41598-025-01005-2) | 云看板/推送和软件架构参考；未证明云计算协同提升 AI 推理或训练。 |
| [INSCIT 量化 YOLOv8](https://doi.org/10.1109/INSCIT72361.2026.11707193) | DeepSeek 的 Pi3B+ 量化候选；无作者仓库与云协同，本轮不重复采信其数值。 |
| [YOLO-CD](https://doi.org/10.1007/s11554-025-01847-z)、[SF-YOLO](https://doi.org/10.1007/s11760-025-03819-z)、[RK3588 候选](https://doi.org/10.1109/NNICE68970.2026.11465485) | 板卡检测补充；代码/云端缺项。SF-YOLO 的 [PCB 仓库](https://github.com/ATaylor0623/PCB)是数据集线索，不当作论文模型源码。 |
| [PYOLO-PCF](https://doi.org/10.1142/S0218001425590220)、[YOLO-FSE](https://doi.org/10.1111/exsy.70344)、[柔性包装颜色形状方法](https://doi.org/10.1088/1361-6501/ad340f) | 包装检测方法补充，云边与公开代码不足。后者并非所需轻量边缘深度模型链路。 |
| [LightDets 中文论文](http://www.cims-journal.cn/EN/abstract/abstract8999.shtml) | 端边云调度候选；设备/代码未核实，具体缺陷任务需全文。家电并非用户原始排除对象，但要确认表面缺陷而非部件存在性。 |
| [RasPiDets/PDD](https://github.com/Anoise/PDD) / [IEEE](https://ieeexplore.ieee.org/document/10964356/) | 小设备与卸载思路参考；检测内容含空调部件与声音，偏离严格表面缺陷；完整调度公开程度需查。 |
| [汽车 VLM 工程](https://github.com/vgandhi1/automotive-visual-qa-engine) | 有桩/模拟部分，云复检真实性和性能不足；架构参考，不作为可运行主选。 |
| [纺织 edge-cloud](https://github.com/bensidhom/Edge-Cloud-Textile-Defect-Detection) | WorkBuddy 指出两套独立部署，无协同闭环；不纳主线。 |
| [pcb-inspector-edge](https://github.com/hammamikhairi/pcb-inspector-edge) | 报告称云主要远程访问、性能慢、许可缺失；不作主要复用来源。 |
| [ESP32 Edge AI](https://github.com/thelostbong/Automated-Defect-Detection-using-Edge-AI) | MCU 算力极弱，任务和运行真实性尚不足；不要据“部署包存在”就承诺 YOLO11n 达到产线实时。 |
| [Neurosurgeon 第三方复现](https://github.com/Tjyy-1223/Neurosurgeon) | 切分思路参考，对应 2017 方法；不属于近三年缺陷论文，许可待核。 |
| [Seeed 钢材边缘方案](https://wiki.seeedstudio.com/solutions/edge-surface-defect-inspection/) | 可看边缘工程/工业接口；现场 MQTT/Modbus 不自动等于云端 AI 协同。 |
| [HAILS](https://github.com/xluckywang/HAILS) / [论文 DOI](https://doi.org/10.1016/j.eswa.2026.134682) | 前轮仓库不可访问，卷期显示 2027；首次在线日期待查。暂不计窗口内，不作代码主选。 |
| [synthetic-inspector](https://github.com/WildflowerEnam/synthetic-inspector)、[digital-twin 骨架](https://github.com/StefanvanderKolk/industrial-digital-twin-platform)、[RPi5-pcb](https://github.com/mooncholy/RPi5-pcb) | WorkBuddy 排除：模拟、文档骨架或未完成项目；本轮未升级其证据等级。 |

原报告中其他仅有题录、作者请求可得、付费墙无法核实的候选继续保存在 sources，不把它们转成已验证结论。**主线选择看任务匹配与证据，不看 GitHub 星数或 AI 的肯定语气。**

## 4. 真正开始实现前还要补什么

1. 选择一个缺陷对象及数据集，明确标注类型、使用许可和分组划分。
2. 锁定边缘板型号、功耗模式、内存、运行时版本；明确可用云 GPU。
3. 在边缘跑通一个模型，记录精度/延迟；代码存在不等于跑通。
4. 再接 R1 或 R2 的协同服务，测实际网络与云排队；最终报告只引用自己的测量。
