# csitp｜产线视觉质检云边协同调研

**项目题目：面向产线视觉质检的云边协同 AI 服务编排系统设计与实现**

整理日期：2026-11-09。文献窗口：2023-11-09—2026-11-09。资料来自本次对话中的 DeepSeek、WorkBuddy 报告，以及 Codex（OpenAI）对出版社、官方项目文档和实际源码的交叉核验。

**综合整理与补充核验：Codex（OpenAI）。** 独立补充项目及各条目的来源归属见 [Codex 补充检索记录](sources/codex-supplement.md)；该署名表示调研整理者，不是论文或开源项目作者。

## 先看结论

**建议以“轻量边缘检测 + 云端训练与模型版本下发”为基础，叠加“疑难样本按需上云”的可切换策略。** 云端不必对每张图片复检；服务编排还包括训练任务、模型发布、设备状态、超时与断网处理。

本轮尚未核实到一篇同时满足“近三年 + 产线表面缺陷 + 受限边缘轻量模型 + 明确云边协同 + 可访问的作者对应完整源码”的论文。这里整理的是**有证据的论文参考、公开工程、可组合组件和本项目设计建议**，并明确各自缺项。不能把不同工作的优点合并后，宣称已有论文全部实现。

## 阅读导航

| 内容 | 入口 |
|---|---|
| 最初要求、后续放宽条件、5 条路线取舍 | [路线推荐](docs/01-requirements-and-routes.md) |
| 代表论文/项目、GitHub、方法、性能与局限 | [典型案例](docs/02-typical-cases.md) |
| 仓库外论文全文、源码、权重和数据集怎么找 | [访问指南](docs/03-access-guide.md) |
| 两份 AI 报告的纠错与其余候选处理结果 | [核验与候选清单](docs/04-audit-and-backlog.md) |
| 最小可行原型、对比实验、验收指标 | [实施与实验计划](docs/05-experiment-plan.md) |
| 本轮读取 GitHub 文件的路径与 blob SHA | [证据记录](references/github-evidence.json) |
| DeepSeek / WorkBuddy 原始报告及 Codex 补充记录 | [资料来源说明](sources/README.md) |

## 五条路线速览

| 路线 | 边缘端 | 云端 | 典型依据 | 建议 |
|---|---|---|---|---|
| R1 云端训练与模型发布 | 轻量模型实时检测 | 训练、验证、编译、版本下发 | 软包装 GA-YOLOv8 论文；AWS × Qualcomm；AWS DDA | **首选基础路线** |
| R2 疑难样本按需上云 | 初检、路由、超时处理 | 较大模型复检与结果回传 | Sensors 2024 PCB 论文；Sedna JointInference | **首选研究扩展** |
| R3 云模型库与现场少样本适配 | 下载权重、少量微调、推理 | 预训练和模型管理 | Tang 等，J. Supercomputing 2026 | 有换型需求且边缘能训练时采用 |
| R4 多产线联邦训练 | 各产线本地训练/推理 | 参数聚合、全局模型分发 | Sedna 磁瓦表面缺陷示例 | 进阶备选，示例早于三年窗口 |
| R5 分支/切分协同推理 | 浅层或早退分支 | 深层后续计算 | YOLOX BranchyNet / EAAI 2024 | 研究备选，代码和实现成本受限 |

断网队列、重连补传可作为 R1/R2 的公共能力；仅同步结果的工程不能当成“云端参与 AI 计算”的完整证据。

## 推荐首先阅读的例子

1. [Sensors 2024 PCB 云边论文](https://doi.org/10.3390/s24185921)：理解为什么只上传疑难样本；未找到作者代码。
2. [AWS × Qualcomm 开源样例](https://github.com/aws-samples/sm-qai-hub-examples)：理解云训练—模型编译—设备验证流程；是工程教程。
3. [Sedna 联合推理示例](https://github.com/kubeedge/sedna/tree/main/examples/joint_inference/helmet_detection_inference)：学习大小模型协同；原任务是安全帽，需迁移到缺陷检测。
4. [PCB YOLOv8 工程](https://github.com/Dingshichun/PCB-Defect-Detection-System-Based-on-YOLOv8)：训练与边缘部署脚手架；没有云端，板卡性能需实测。
5. [MicroDETR 论文](https://doi.org/10.1038/s41598-025-28617-y) / [作者代码](https://github.com/yixing166/MicroDETR)：有论文对应代码及 TX2 实测的轻量模型参考；没有云协同。

## 本仓库存了什么

已存入整理后的 Markdown 文档、原始 AI 报告及源码证据索引。第三方论文 PDF、第三方源码、数据集与权重均通过原站链接访问，**没有镜像进本仓库**。当前仓库是调研资料库，尚未实现或运行质检系统；论文数字和作者自述不等于本项目复现实测。
