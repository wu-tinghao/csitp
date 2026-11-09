# 典型论文与开源项目

[返回首页](../README.md) · 核验日期：2026-11-09

**综合整理与补充核验：Codex（OpenAI）。** 下列“调研来源”记录本次资料的线索来源与核验分工，不代表论文或源码作者。详见 [Codex 补充记录](../sources/codex-supplement.md)。

数字保留原始指标和硬件条件，均不是本项目复现实测。以下没有任何一项被认定为“论文、云边、轻量上板、完整配套代码全部满足”。“无代码”表示本轮未核实到，不代表永远不存在。

## A. 直接解释云边质检的论文

### P1｜Online Defect Detection of Soft Packaging Using an Improved YOLOv8 Model with Edge Computing and Domain Adaptation

- **调研来源：** DeepSeek 报告已有线索；Codex（OpenAI）交叉核验与整理。
- **出处：** Applied Sciences，2026，16(8):3786。[论文 / DOI](https://doi.org/10.3390/app16083786)。
- **GitHub：** 未找到作者对应仓库。可参考下文 E1/E2 实现训练发布，但不是该论文代码。
- **方法与任务：** GhostNet、AFPN 和域适应改进 YOLOv8，面向多品类软包装检测；边缘推理，厂内服务器训练和更新模型。
- **云边证据：** §2.1 描述按 72 小时或超过 300 个新缺陷样本触发重训练；并描述验证改善后压缩下发。阈值是该文设定，不能当成本项目最优值。
- **性能：** Table 6 的 MAIX-III INT8 为 44 FPS、172 MB；摘要另报 10.9 ms 推理及 148 ms 平均系统响应。10.9 ms 与 44 FPS 不能直接互取倒数。摘要 97.5% 使用 average detection accuracy 表述，正文另出现 mAP 表述，引用应保留所在表/章节，不能混用。
- **优点：** 贴近“低延迟边缘检测 + 持续云端更新”的系统题目。
- **缺点：** 源码及公开数据不足；包含空袋、连袋等非纯表面任务；不同性能口径需谨慎核对。
- **定位：** R1 的论文依据；不能列为满足 GitHub 硬要求的主结果。

### P2｜Cloud-Edge Collaborative Defect Detection Based on Efficient Yolo Networks and Incremental Learning

- **调研来源：** DeepSeek 报告已有线索；Codex（OpenAI）交叉核验并纠正 Table 5 指标误读。
- **出处：** Sensors，2024，24(18):5921。[论文](https://doi.org/10.3390/s24185921) / [PMC 全文](https://pmc.ncbi.nlm.nih.gov/articles/PMC11435656/)。
- **GitHub：** 未找到作者配套仓库；Sedna 是独立框架参考。
- **方法与任务：** PCB 缺孔、开路、短路、毛刺等；轻量 SGRS-YoloV5n 初检，难判样本继续上云，云端 YOLOv5s 复检，分拣端执行结果；另研究增量学习。
- **证据：** §2.1、§3.5 的三级协作及三台 Raspberry Pi 4 分工；Table 3 报 mAP 0.935，Table 4 报模型约 2.2 MB、2.7 GFLOPs。
- **速度与边缘：** 真实小板部署，但本轮未核实到 Pi 端 FPS/时延量化。不能把训练 GPU 的速度写成 Pi 速度。
- **优点：** 最贴近“不确定再上报”的 PCB 质检范式。
- **缺点：** 缺源码与板卡性能量化；增量学习实验不能等同已验证的自动训练、灰度发布和回滚平台。
- **纠错：** Table 5 的 0.338 是 full-scale fine-tuning 行的 Pad-Damage 类结果，不是该文增量方案整体 mAP；our method 同列为 0.680。
- **定位：** R2 的任务级论文依据。

### P3｜An improved transfer learning for surface defect detection in edge-cloud continuum environments

- **调研来源：** DeepSeek 报告已有线索；Codex（OpenAI）补查出版社摘要与云边职责。
- **出处：** The Journal of Supercomputing，2026，82:63，2026-01-14。[出版社](https://link.springer.com/article/10.1007/s11227-025-07993-2)。
- **GitHub：** 未核实到作者代码；完整实验环境待全文。
- **方法：** 云模型库提供权重，边缘小数据微调 YOLOv10-DSC，在带钢、钢轨、铝板数据上评估迁移。
- **性能：** 摘要称相对直接训练 YOLOv10，mAP@50 改善 2.8%；本轮没有板卡速度和边缘训练资源证据。
- **优点：** 为跨产线/产品换型提供路线；不依赖逐帧云复检。
- **缺点：** 边缘训练可能超出弱设备能力；有公开摘要不等于已读完整实验。
- **定位：** R3 方法依据，不是可直接运行的开源系统。

## B. 首选工程参考——有明确 GitHub 入口

### E1｜Amazon SageMaker × Qualcomm AI Hub：YOLOv8 PCB 示例

- **调研来源：** Codex（OpenAI）补充检索与核验。
- **出处：** 2024 官方工程教程。[原文](https://aws.amazon.com/blogs/machine-learning/train-optimize-and-deploy-models-on-edge-devices-using-amazon-sagemaker-and-qualcomm-ai-hub/) / [GitHub](https://github.com/aws-samples/sm-qai-hub-examples)。
- **核心方法：** SageMaker A10G 训练自定义 YOLOv8，模型入 S3，AI Hub 编译并在设备上验证；示例目标为 Snapdragon X Elite Windows 设备。
- **代码入口：** [yolo 目录](https://github.com/aws-samples/sm-qai-hub-examples/tree/main/yolo)，依次看 notebook 和训练脚本。仓库 LICENSE 为 MIT-0。
- **优点：** 云训练→编译→设备验证链路清楚，最适合学习 R1 的可运行步骤。
- **缺点：** 原文是示例场景，并非已验收的真实工厂部署；使用 189 张 PCB 图像，任务还含装配/焊接异常；目标是设备农场中的真实硬件，不是已证实安装于产线的小板。
- **速度/精度：** 未提供足以支持本项目板卡 FPS、端到端时延或跨数据集精度结论的可比结果。
- **迁移工作：** 换成严格表面缺陷数据和小模型，补上真实边缘板测量及自动版本发布。不能把本例写成现成闭环重训练系统。

### E2｜Defect Detection Application（AWS DDA）

- **调研来源：** Codex（OpenAI）补充检索与源码核验。
- **类型：** AWS 开源工业质检平台，非对应学术论文。[GitHub](https://github.com/awslabs/DefectDetectionApplication)。
- **方法：** 云端管理训练/模型/设备，通过 Greengrass 发布到边缘推理。轻量程度取决于导入模型，平台不自动保证模型小。
- **源码证据：** [模型发布函数](https://github.com/awslabs/DefectDetectionApplication/blob/main/edge-cv-portal/backend/functions/greengrass_publish.py)；[实际推理运行时代码](https://github.com/awslabs/DefectDetectionApplication/blob/main/src/backend/dda_triton/resources_for_copy/inference_runtimes.py)。已看到 OnnxRunner 和 TorchRunner，而非仅架构图。
- **优点：** 模型与设备管理完整，适合研究编排接口、版本与状态；Apache-2.0。
- **缺点：** AWS/CDK/Greengrass 部署较重；自备数据与缺陷权重；未核实统一精度/FPS。不建议为了首个演示照搬整个平台。
- **复现细节：** [multi-runtime 文档](https://github.com/awslabs/DefectDetectionApplication/blob/main/docs/multi-runtime-inference.md)仍保留旧 proposal 状态和旧 provider 顺序；当前代码对 TensorRT 使用显式选择。应以锁定版本的代码和实际测试为准。
- **定位：** R1 编排参考，需接入自己的轻量表面缺陷模型。

### E3｜KubeEdge Sedna JointInference

- **调研来源：** WorkBuddy 报告已有线索；Codex（OpenAI）核对示例、源码与许可。
- **类型：** 通用云边 AI 框架。[GitHub](https://github.com/kubeedge/sedna) / [联合推理示例](https://github.com/kubeedge/sedna/tree/main/examples/joint_inference/helmet_detection_inference)。
- **方法：** 边缘小模型和 hard example mining 识别困难样本，云端大模型复检；有 JointInferenceService 配置与 worker 源码。
- **优点：** 可直接学习任务与模型资源的编排表达，Apache-2.0。
- **缺点：** 示例是安全帽检测和旧 YOLOv3 模型，非表面缺陷、非 YOLOv8；Kubernetes/KubeEdge 对入门团队有运维成本。
- **速度/精度：** 没有可移用为 PCB/钢材结果的板卡性能。迁移后需重新训练、适配输出和测量。
- **定位：** R2 的机制组件，不属于近三年的缺陷论文。磁瓦联邦示例是另一个任务，不可混为同一案例。

### E4｜基于 YOLOv8 的 PCB 缺陷检测系统（Dingshichun）

- **调研来源：** WorkBuddy 报告已有线索；Codex（OpenAI）核对部署源码、硬件测速口径与许可。
- **类型：** 独立工程，非论文官方实现。[GitHub](https://github.com/Dingshichun/PCB-Defect-Detection-System-Based-on-YOLOv8)。
- **方法：** YOLOv8n/s 训练评估、ONNX 导出、TensorRT 边缘推理。明确覆盖六类 PCB 表面缺陷；无云协同。
- **证据入口：** [src/deploy](https://github.com/Dingshichun/PCB-Defect-Detection-System-Based-on-YOLOv8/tree/main/src/deploy)，已核对实际推理文件及 MIT LICENSE。
- **性能：** README 自述 YOLOv8n 640 输入 mAP50=0.9286；1.3 ms/图（640）、4.9 ms/图（1280）均为 RTX 4070 SUPER 条件，**不是 Jetson 实测**。
- **优点：** 与用户希望的 YOLOv8 路线吻合，脚手架便于接云端 API。
- **缺点：** 缺板卡量化验证；预训练 YOLO 权重不等于已训练 PCB 权重。推理脚本有 get_binding_shape、max_batch_size、execute_async_v2 等旧 TensorRT API，新版本需适配。
- **定位：** R1/R2 的边缘模型组件；许可证还应分别核对依赖、数据和权重，不因外层 MIT 推定全部资产同许可。

## C. 有论文对应代码的轻量模型——补充算法基线

### M1｜MicroDETR for efficient and lightweight PCB defect detection

- **调研来源：** Codex（OpenAI）补充检索、论文—代码对应核验。
- **出处：** Scientific Reports，2026-04-29。[论文](https://www.nature.com/articles/s41598-025-28617-y) / [作者 GitHub](https://github.com/yixing166/MicroDETR)。
- **方法：** 改造 RT-DETR 的轻量骨干与多尺度特征聚合，解决 PCB 微小缺陷。
- **性能：** 论文报告 Jetson TX2、TensorRT FP16 下 **18.9 FPS、mAP@0.5=79.8%**。
- **优点：** 论文直接给仓库，有具体边缘实测；提供非 YOLO 模型对比路线。
- **缺点：** **没有云端协同**；当前 README 仅一行 train.py，虽然已确认训练文件存在，但安装说明、训练权重完整性和可运行性仍需审查。论文声称公开权重不等于本轮下载验证过权重。
- **定位：** 可接到 R1/R2 中做模型对照；不能宣称系统已经开源复现。

### M2｜Finite Element Method-YOLO: Lightweight Steel Strip Surface Defect Detection Algorithm（FEM-YOLO）

- **调研来源：** DeepSeek 提供论文线索、WorkBuddy 提供仓库线索；Codex（OpenAI）核对对应关系和板卡表格。
- **出处：** Applied Sciences，2025，15(21):11328。[论文](https://www.mdpi.com/2076-3417/15/21/11328) / [论文关联仓库 TinyDefectNet](https://github.com/uglly/TinyDefectNet)。
- **方法：** 轻量 YOLOv8n 改进用于带钢表面缺陷。
- **性能：** 原文 Table 5 报 Jetson Nano 15 FPS、另一列原文称“Jetson Nano NX”30 FPS；后者设备命名有歧义。仓库所述 Orin NX 58 FPS 属另一来源，不能互相替代。
- **优点：** 可用于带钢轻量化与上板评测的参考。
- **缺点：** 无云端；源码以 RAR 包提供，未解包运行；许可证和权重完整性未核实。
- **定位：** 次选算法依据；优先级低于脚本可直接审阅且部署文档清晰的组件。

## D. 进阶机制与通信参考

**调研来源：** Sedna 磁瓦示例来自 WorkBuddy 报告；YOLOX BranchyNet、SAEC 已见于 DeepSeek 报告；Jetson edge-defect-detector 由 **Codex（OpenAI）补充检索**。本节由 Codex（OpenAI）交叉核验与整理，具体核验边界见表。

| 名称与入口 | 核心思路 | 优点 | 缺项/限制 |
|---|---|---|---|
| [Sedna 磁瓦联邦学习](https://github.com/kubeedge/sedna/tree/main/examples/federated_learning/surface_defect_detection) | 边缘训练，云端聚合，生成更新模型 | 缺陷任务与云边训练职责清楚 | 较早示例；不是 YOLOv8 联合推理；弱板训练负担大 |
| [YOLOX BranchyNet / EAAI 2024](https://doi.org/10.1016/j.engappai.2024.109468) | 分支网络与云边融合用于 PCB/太赫兹检测 | 提供分支计算研究方向 | 作者 GitHub 未核实；成像任务差异大；精确切分细节待全文 |
| [SAEC](https://arxiv.org/abs/2509.17136) / [ICASSP 2026 节目](https://www.cmsworkshops.com/ICASSP2026/view_paper.php?PaperNum=2670&bare=1) | 场景复杂度/预算驱动的小模型与云端多模态大模型协同 | 适合讨论 R2 路由依据 | [声明的代码地址](https://github.com/YuHao-Tian/SAEC)本轮返回 404；实验边缘为服务器 CPU，不能当小板实测；分类 accuracy 不等于检测 mAP |
| [Jetson edge-defect-detector](https://github.com/lucamaf/edge-defect-detector) | 边缘 YOLO 检测，Artemis 队列向 OpenShift 同步 | 有[断网积压和重连补传文档](https://github.com/lucamaf/edge-defect-detector/blob/main/amq-artemis-federated/README.md) | 未核实统一板卡指标；LICENSE 路径未找到，许可未确认；云端 AI 计算未证明 |

完整的剩余候选及不优先理由见[核验清单](04-audit-and-backlog.md)。
